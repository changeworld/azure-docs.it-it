---
title: Supporto per hub Azure MQTT 5 (anteprima)
description: Informazioni sul supporto di MQTT 5 per l'hub.
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603558"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>Panoramica sul supporto dell'hub MQTT 5 (anteprima)

**Versione:** 2,0 **API-Version:** 2020-10-01-Preview

Questo documento definisce l'API del piano dati dell'hub Internet sul protocollo MQTT versione 5,0. Vedere le informazioni di [riferimento sulle API](iot-hub-mqtt-5-reference.md) per completare le definizioni in questa API.

## <a name="prerequisites"></a>Prerequisiti

- [Abilitare la modalità di anteprima](iot-hub-preview-mode.md) in un nuovo hub delle cose per provare MQTT 5.
- È necessaria la conoscenza precedente della [specifica MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) .

## <a name="level-of-support-and-limitations"></a>Livello di supporto e limitazioni

Il supporto dell'hub Internet per MQTT 5 è in versione di anteprima e limitato nei modi seguenti (comunicati al client tramite proprietà, a `CONNACK` meno che non sia esplicitamente specificato diversamente):

- Non è ancora disponibile alcun supporto ufficiale per l' [SDK per dispositivi dell'hub Azure](iot-hub-devguide-sdks.md) .
- Gli identificatori di sottoscrizione non sono supportati.
- Le sottoscrizioni condivise non sono supportate.
- `RETAIN` non è supportata.
- `Maximum QoS` è `1`.
- `Maximum Packet Size` è `256 KiB` (soggetto ad altre restrizioni per ogni operazione).
- Gli ID client assegnati non sono supportati.
- `Keep Alive` è limitato a `19 min` (ritardo massimo per il controllo della durata `28.5 min` ).
- `Topic Alias Maximum` è `10`.
- `Response Information` non è supportato. `CONNACK` non restituisce la `Response Information` proprietà anche se `CONNECT` contiene la `Request Response Information` Proprietà.
- `Receive Maximum` il numero massimo di pacchetti non riconosciuti `PUBLISH` in attesa (in direzione client-server) con `QoS: 1` ) è `16` .
- Il client singolo non può avere più di `50` sottoscrizioni.
  Quando viene raggiunto il limite, `SUBACK` restituirà il `0x97` codice motivo per le sottoscrizioni (superamento della quota).

## <a name="connection-lifecycle"></a>Ciclo di vita della connessione

### <a name="connection"></a>Connessioni

Per connettere un client all'hub Internet delle cose usando questa API, stabilire la connessione per ogni specifica MQTT 5.
Il client deve inviare `CONNECT` il pacchetto entro 30 secondi dopo il completamento dell'handshake TLS oppure il server chiude la connessione.
Ecco un esempio di `CONNECT` pacchetto:

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` la proprietà è obbligatoria e identifica il metodo di autenticazione utilizzato. Per ulteriori informazioni sul metodo di autenticazione, vedere [Authentication](#authentication).
- `Authentication Data` la gestione delle proprietà dipende da `Authentication Method` . Se `Authentication Method` è impostato su `SAS` , `Authentication Data` è obbligatorio e deve contenere una firma valida. Per ulteriori informazioni sui dati di autenticazione, vedere [Authentication](#authentication).
- `api-version` la proprietà è obbligatoria e deve essere impostata sul valore di versione API specificato nell'intestazione di questa specifica per applicare questa specifica.
- `host` Proprietà che definisce il nome host del tenant. È obbligatorio, a meno che l'estensione SNI non sia stata presentata nel record Hello del client durante l'handshake TLS
- `sas-at` definisce l'ora di connessione.
- `sas-expiry` definisce l'ora di scadenza per la firma di accesso condiviso fornita.
- `client-agent` comunica facoltativamente le informazioni sul client che crea la connessione.

> [!NOTE]
> `Authentication Method` e altre proprietà in tutte le specifiche con nomi con lettere maiuscole sono proprietà di prima classe in MQTT 5, descritte in dettaglio nella specifica MQTT 5. `api-version` e altre proprietà in caso di trattino sono proprietà utente specifiche dell'API dell'hub Internet.

L'hub Internet delle cose risponde con `CONNACK` il pacchetto una volta terminato con l'autenticazione e il recupero dei dati per supportare la connessione. Se la connessione viene stabilita correttamente, sarà `CONNACK` simile a quanto segue:

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

Queste `CONNACK` proprietà dei pacchetti seguono la [specifica MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080). Riflettono le funzionalità dell'hub delle cose.

### <a name="authentication"></a>Authentication

La `Authentication Method` proprietà nel `CONNECT` client definisce il tipo di autenticazione usato per la connessione:

- `SAS` -La firma di accesso condiviso viene fornita nella `CONNECT` `Authentication Data` proprietà di.
- `X509` -client si basa sull'autenticazione del certificato client.

 L'autenticazione ha esito negativo se il metodo di autenticazione non corrisponde al metodo configurato del client nell'hub.

> [!NOTE]
> Questa API richiede `Authentication Method` che la proprietà sia impostata nel `CONNECT` pacchetto. Se `Authentication Method` la proprietà non viene specificata, la connessione ha esito negativo con `Bad Request` risposta.

L'autenticazione con nome utente/password usata nelle versioni precedenti dell'API non è supportata.

#### <a name="sas"></a>SAS

Con l'autenticazione basata su SAS, il client deve fornire la firma del contesto di connessione. Ciò dimostra l'autenticità della connessione MQTT. La firma deve essere basata su una delle due chiavi di autenticazione nella configurazione del client nell'hub Internet o in una delle due chiavi di accesso condivise di un [criterio di accesso condiviso](iot-hub-devguide-security.md).

La stringa da firmare deve essere formata come indicato di seguito:

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` viene derivato dall'estensione SNI (presentato dal client nel record Hello del client durante l'handshake TLS) o dalla `host` proprietà utente nel `CONNECT` pacchetto.
- `Client Id` Identificatore client nel `CONNECT` pacchetto.
- `sas-policy` -Se presente, definisce i criteri di accesso dell'hub per l'utilizzo per l'autenticazione. Viene codificata come proprietà utente nel `CONNECT` pacchetto. Facoltativo: se si omette, significa che verranno usate le impostazioni di autenticazione nel registro del dispositivo.
- `sas-at` -Se presente, specifica l'ora corrente della connessione. Viene codificato come proprietà User di `time` tipo nel `CONNECT` pacchetto.
- `sas-expiry` definisce la data di scadenza dell'autenticazione. Si tratta di una `time` proprietà utente tipizzata nel `CONNECT` pacchetto. Questa proprietà è obbligatoria.

Per i parametri facoltativi, se omesso, è necessario utilizzare una stringa vuota in stringa per la firma.

HMAC-SHA256 viene usato per eseguire l'hashing della stringa in base a una delle chiavi simmetriche del dispositivo. Il valore hash viene quindi impostato come valore della `Authentication Data` Proprietà.

#### <a name="x509"></a>X509

Se `Authentication Method` la proprietà è impostata su `X509` , l'hub Internet esegue l'autenticazione della connessione in base al certificato client fornito.

#### <a name="reauthentication"></a>Riautenticazione

Se si usa l'autenticazione basata su SAS, si consiglia di usare i token di autenticazione di breve durata. Per consentire l'autenticazione della connessione e impedire la disconnessione a causa della scadenza, il client deve eseguire nuovamente l'autenticazione inviando il `AUTH` pacchetto con `Reason Code: 0x19` (riautenticazione):

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

Regole:

- `Authentication Method` deve corrispondere a quello usato per l'autenticazione iniziale
- Se la connessione è stata originariamente autenticata con la firma di accesso condiviso basata su criteri di accesso condiviso, la firma usata nella riautenticazione deve essere basata sugli stessi criteri.

Se la riautenticazione ha esito positivo, l'hub Internet invia un `AUTH` pacchetto con `Reason Code: 0x00` (esito positivo). In caso contrario, `DISCONNECT` l'hub Internet invia un pacchetto con `Reason Code: 0x87` (non autorizzato) e chiude la connessione.

### <a name="disconnection"></a>Disconnessione

Il server può disconnettere il client per alcuni motivi:

- il client si comporta in modo errato in modo che non sia possibile rispondere direttamente con un riconoscimento (o risposta) negativo,
- il server non è in grado di Mantenete aggiornato lo stato della connessione.
- il client con la stessa identità è connesso.

Il server può disconnettersi con qualsiasi codice motivo definito nella specifica MQTT 5,0. Menzioni rilevanti:

- `135` (Non autorizzato) quando la riautenticazione ha esito negativo, il token SAS corrente scade o la modifica delle credenziali del dispositivo
- `142` (Sessione acquisita) quando è stata aperta una nuova connessione con la stessa identità client.
- `159` (Velocità di connessione superata) quando la velocità di connessione per l'hub Internet è superiore a  
- `131` (Errore specifico dell'implementazione) viene usato per tutti gli errori personalizzati definiti in questa API. `status``reason`le proprietà e verranno usate per comunicare ulteriori informazioni sulla motivo della disconnessione (vedere la [risposta](#response) per i dettagli).

## <a name="operations"></a>Gestione operativa

Tutte le funzionalità di questa API sono espresse come operazioni. Di seguito è riportato un esempio di operazione di invio dei dati di telemetria:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

Per la specifica completa delle operazioni in questa API, vedere informazioni di [riferimento sulle API](iot-hub-mqtt-5-reference.md).

> [!NOTE]
> Tutti gli esempi in questa specifica vengono visualizzati dal punto di vista del client. Sign `->` significa che il client invia il pacchetto, ovvero la `<-` ricezione.

### <a name="message-topics-and-subscriptions"></a>Argomenti e sottoscrizioni del messaggio

Gli argomenti usati nei messaggi delle operazioni in questa API iniziano con `$iothub/` .
La semantica del broker MQTT non si applica a queste operazioni (vedere "[argomenti che iniziano con \$ ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)" per informazioni dettagliate).
Gli argomenti che iniziano con `$iothub/` che non sono definiti in questa API non sono supportati:

- L'invio di messaggi a un argomento non definito restituisce una `Not Found` risposta (vedere la [risposta](#response) per i dettagli più avanti).
- La sottoscrizione di un argomento non definito restituisce `SUBACK` con `Reason Code: 0x8F` (filtro dell'argomento non valido).

I nomi degli argomenti e delle proprietà fanno distinzione tra maiuscole e minuscole e devono corrispondere esattamente. Ad esempio, `$iothub/telemetry/` non è supportato mentre `$iothub/telemetry` è.

> [!NOTE]
> I caratteri jolly nelle sottoscrizioni in `$iothub/..` non sono supportati. Ovvero, un client non può sottoscrivere `$iothub/+` o `$iothub/#` . Il tentativo di eseguire questa operazione comporta l'esecuzione di `SUBACK` con `Reason Code: 0xA2` (le sottoscrizioni con caratteri jolly non sono supportate). Sono supportati solo i caratteri jolly a segmento singolo ( `+` ) anziché i parametri del percorso nel nome dell'argomento per le operazioni in cui sono presenti.

### <a name="interaction-types"></a>Tipi di interazione

Tutte le operazioni in questa API sono basate su uno dei due tipi di interazione:

- Messaggio con riconoscimento facoltativo (MessageAck)
- Request-Response (ReqRep)

Le operazioni variano anche in base alla direzione (determinata dalla direzione del messaggio iniziale in Exchange):

- Da client a server (C2S)
- Da server a client (S2c)

Ad esempio, l'invio di dati di telemetria è un'operazione da client a server del tipo "messaggio con riconoscimento", mentre il metodo diretto di gestione è un'operazione da server a client di tipo Request-Response.

#### <a name="message-acknowledgement-interactions"></a>Interazioni di riconoscimento del messaggio

Il messaggio con interazione facoltativa di riconoscimento (MessageAck) viene espresso come scambio `PUBLISH` di `PUBACK` pacchetti e in MQTT. Il riconoscimento è facoltativo e il mittente può scegliere di non richiederlo inviando un `PUBLISH` pacchetto con `QoS: 0` .

> [!NOTE]
> Se le proprietà nel `PUBACK` pacchetto devono essere troncate a causa del `Maximum Packet Size` dichiarate dal client, l'hub di Internet delle cose manterrà il numero di proprietà utente che può rientrare nel limite specificato. Le proprietà utente elencate per prime hanno una maggiore probabilità di essere inviate rispetto a quelle elencate in seguito; `Reason String` la proprietà ha la priorità minore.

##### <a name="example-of-simple-messageack-interaction"></a>Esempio di semplice interazione con MessageAck

Messaggio:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

Riconoscimento (esito positivo):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Interazioni tra Request-Response

Nelle interazioni Request-Response (ReqRep), sia la richiesta che la risposta vengono convertite in `PUBLISH` pacchetti con `QoS: 0` .

`Correlation Data` la proprietà deve essere impostata sia in che in viene usata per trovare la corrispondenza con il pacchetto di risposta al pacchetto di richiesta.

Questa API usa l'argomento di risposta singola `$iothub/responses` per tutte le operazioni ReqRep. La sottoscrizione o l'annullamento della sottoscrizione da questo argomento per le operazioni client-server non è obbligatorio. il server presuppone che tutti i client siano sottoscritti.

##### <a name="example-of-simple-reqrep-interaction"></a>Esempio di semplice interazione con ReqRep

Richiesta:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Risposta (esito positivo):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Le interazioni ReqRep non supportano `PUBLISH` i pacchetti con `QoS: 1` come messaggi di richiesta o di risposta. Invio `PUBLISH` dei risultati della richiesta in `Bad Request` risposta.

La lunghezza massima supportata nella `Correlation Data` proprietà è di 16 byte. Se `Correlation Data` la proprietà su `PUBLISH` pacchetto è impostata su un valore superiore a 16 byte, l'hub `DISCONNECT` Internet viene inviato con `Bad Request` risultato e chiude la connessione. Questo comportamento si applica solo ai pacchetti scambiati all'interno dell'API.

> [!NOTE]
> I dati di correlazione sono una sequenza di byte arbitraria, ad esempio non è garantita la stringa UTF-8.
>
> ReqRep usa argomenti predefiniti per la risposta; La proprietà dell'argomento della risposta nel pacchetto di richiesta `PUBLISH` , se impostata dal mittente, viene ignorata.

L'hub Internet degli utenti sottoscrive automaticamente gli argomenti di risposta da client a server per tutte le operazioni ReqRep da client a server. Anche se il client Annulla esplicitamente la sottoscrizione dall'argomento della risposta, l'hub Internet ripristina automaticamente la sottoscrizione. Per le interazioni ReqRep da server a client, è ancora necessario per il dispositivo sottoscrivere.

### <a name="message-properties"></a>Proprietà del messaggio

Le proprietà dell'operazione, definite dall'utente o dal sistema, sono espresse come proprietà del pacchetto in MQTT 5.

I nomi delle proprietà utente fanno distinzione tra maiuscole e minuscole e devono essere digitati esattamente come definito. Ad esempio, `Trace-ID` non è supportato mentre `trace-id` è.

Le richieste con proprietà utente esterne alla specifica e senza prefisso `@` generano un errore.

Le proprietà di sistema vengono codificate come proprietà di prima classe (ad esempio, `Content Type` ) o come proprietà utente. Specifica fornisce un elenco completo delle proprietà di sistema supportate.
Tutte le proprietà della prima classe vengono ignorate, a meno che il supporto per tali proprietà non venga dichiarato in modo esplicito nella specifica.

Se sono consentite le proprietà definite dall'utente, i rispettivi nomi devono seguire il formato `@{property name}` . Le proprietà definite dall'utente supportano solo valori stringa UTF-8 validi. la proprietà con valore, ad esempio, `MyProperty1` `15` deve essere codificata come proprietà utente con nome `@MyProperty` e valore `15` .

Se l'hub Internet non riconosce la proprietà dell'utente, viene considerato un errore e l'hub Internet risponde con `PUBACK` with `Reason Code: 0x83` (errore specifico dell'implementazione) e `status: 0100` (richiesta non valida). Se il riconoscimento non è stato richiesto (QoS: 0), `DISCONNECT` verrà restituito un pacchetto con lo stesso errore e la connessione verrà terminata.

Questa API definisce i tipi di dati seguenti oltre a `string` :

- `time`: numero di millisecondi a partire da `1970-01-01T00:00:00.000Z` . ad esempio, `1600987195320` per `2020-09-24T22:39:55.320Z` ,
- `u32`: numero intero senza segno a 32 bit,
- `u64`: numero intero senza segno a 64 bit,
- `i32`: numero intero con segno a 32 bit.

### <a name="response"></a>Risposta

Le interazioni possono produrre risultati diversi, ovvero, `Success` , `Bad Request` `Not Found` e altri.
I risultati sono distinti gli uni dagli altri in base alla `status` proprietà utente. `Reason Code` nei `PUBACK` pacchetti (per le interazioni MessageAck) corrisponde `status` al significato laddove possibile.

> [!NOTE]
> Se il client specifica `Request Problem Information: 0` il pacchetto Connect, non verrà inviata alcuna proprietà utente `PUBACK` per i pacchetti conformi alla specifica MQTT 5, inclusa la `status` Proprietà. In questo caso, il client può continuare `Reason Code` a utilizzare per determinare se il riconoscimento è positivo o negativo. 

Ogni interazione ha un valore predefinito (o esito positivo). Dispone `Reason Code` della `0` proprietà e `status` di "not set". In caso contrario:

- Per le interazioni MessageAck, `PUBACK` ottiene un `Reason Code` risultato diverso da 0x0 (Success). `status` per chiarire ulteriormente il risultato, è possibile che la proprietà sia presente.
- Per le interazioni ReqRep, Response `PUBLISH` ottiene il `status` set di proprietà.
- Poiché non esiste alcun modo per rispondere alle interazioni MessageAck con `QoS: 0` direttamente, il `DISCONNECT` pacchetto viene inviato invece con le informazioni di risposta, seguito da Disconnetti.

Esempi:

Richiesta non valida (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

Non autorizzato (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

Non autorizzato (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

Quando necessario, l'hub Internet imposta le proprietà utente seguenti:

- `status` -Codice esteso dell'hub Internet per lo stato dell'operazione. Questo codice può essere usato per distinguere i risultati.
- `trace-id` : ID traccia per l'operazione. L'hub Internet delle cose può tenere una diagnostica aggiuntiva sull'operazione che può essere usata per l'analisi interna.
- `reason` -messaggio leggibile che fornisce ulteriori informazioni sul motivo per cui l'operazione è terminata in uno stato indicato dalla `status` Proprietà.

> [!NOTE]
> Se il client imposta la `Maximum Packet Size` proprietà nel pacchetto di connessione su un valore molto piccolo, non tutte le proprietà utente potrebbero adattarsi e non verranno visualizzate nel pacchetto.
> 
> `reason` è destinato solo alle persone e non deve essere usato nella logica client. Questa API consente la modifica dei messaggi in qualsiasi momento senza preavviso o modifica della versione.
>
> Se il client invia il `RequestProblemInformation: 0` pacchetto Connect, le proprietà utente non verranno incluse nei riconoscimenti per ogni [specifica MQTT 5](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053).

#### <a name="status-code"></a>Codice di stato

`status` la proprietà contiene il codice di stato per l'operazione. È ottimizzato per l'efficienza di lettura del computer.
È costituito da un Unsigned Integer a due byte codificato come Hex in una stringa come `0501` .
Struttura del codice (mappa di bit):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

Il primo byte viene usato per i flag:

- BITS 0 e 1 indicano il tipo di risultati:
  - `00` -operazione riuscita
  - `01` -errore client
  - `10` -errore del server
- bit 2: `1` indica che l'errore è irreversibile
- i bit da 3 a 7 sono riservati e devono essere impostati su `0`

Il secondo byte contiene il codice di risposta Distinct effettivo. I codici di errore con flag diversi possono avere lo stesso valore di secondo byte. Possono ad esempio essere presenti `0001` codici di `0101` errore,,, con un `0201` `0301` significato distinto.

Ad esempio, `Too Many Requests` è un client, un errore irreversibile con codice personalizzato `1` . Il valore è `0000 0101 0000 0001` o `0x0501` .

I client possono usare bits di tipo per identificare se l'operazione è stata completata correttamente. I client possono anche usare un bit ripetibile per decidere se è ragionevole ritentare l'operazione.

## <a name="recommendations"></a>Consigli

### <a name="session-management"></a>Gestione delle sessioni

`CONNACK` il pacchetto contiene la `Session Present` proprietà per indicare se il server ha ripristinato la sessione precedentemente creata. Usare questa proprietà per stabilire se sottoscrivere gli argomenti o ignorare la sottoscrizione poiché la sottoscrizione è stata eseguita in precedenza.

Per fare affidamento su `Session Present` , il client deve tenere traccia delle sottoscrizioni effettuate (ovvero il `SUBSCRIBE` pacchetto inviato e ricevuto `SUBACK` con codice motivo corretto) o assicurarsi di sottoscrivere tutti gli argomenti in un unico `SUBSCRIBE` / `SUBACK` scambio. In caso contrario, se il client invia due `SUBSCRIBE` pacchetti e solo uno di essi viene elaborato correttamente dal server, il server comunicherà `Session Present: 1` in `CONNACK` mentre è accettata solo una parte delle sottoscrizioni del client.

Per evitare che una versione precedente del client non abbia sottoscritto tutti gli argomenti, è preferibile sottoscrivere in modo incondizionato quando il comportamento del client cambia (ad esempio, come parte dell'aggiornamento del firmware). Inoltre, per assicurarsi che non vengano lasciate sottoscrizioni obsolete (prendendo il numero massimo consentito di sottoscrizioni), annullare in modo esplicito le sottoscrizioni che non sono più in uso.

### <a name="batching"></a>Creazione di batch

Non esiste un formato speciale per l'invio di un batch di messaggi. Per ridurre il sovraccarico delle operazioni a elevato utilizzo di risorse in TLS e rete, `PUBLISH` combinare pacchetti (,, `PUBACK` `SUBSCRIBE` e così via) prima di passarli allo stack TLS/TCP sottostante. Inoltre, il client può semplificare l'alias degli argomenti all'interno del "batch":

- Inserire il nome completo dell'argomento nel primo `PUBLISH` pacchetto per la connessione e associarvi l'alias dell'argomento,
- Inserire i pacchetti seguenti per lo stesso argomento con nome argomento vuoto e proprietà alias argomento.

## <a name="migration"></a>Migrazione

Questa sezione elenca le modifiche nell'API rispetto alla [precedente API MQTT](iot-hub-mqtt-support.md).

- Il protocollo di trasporto è MQTT 5. In precedenza-MQTT 3.1.1.
- Le informazioni di contesto per l'autenticazione SAS sono contenute `CONNECT` direttamente nel pacchetto anziché essere codificate insieme alla firma.
- Il metodo di autenticazione viene usato per indicare il metodo di autenticazione usato.
- La firma di accesso condiviso viene inserita nella proprietà dati di autenticazione. In precedenza è stato usato il campo password.
- Gli argomenti per le operazioni sono diversi:
  - Telemetria: `$iothub/telemetry` anziché `devices/{Client Id}/messages/events` ,
  - Comandi: `$iothub/commands` anziché `devices/{Client Id}/messages/devicebound` ,
  - Patch gemello segnalato: `$iothub/twin/patch/reported` anziché `$iothub/twin/PATCH/properties/reported` ,
  - Notifica dello stato desiderato del dispositivo gemello modificato: `$iothub/twin/patch/desired` anziché `$iothub/twin/PATCH/properties/desired` .
- La sottoscrizione per l'argomento della risposta per le operazioni di richiesta-risposta del server client non è obbligatoria.
- Vengono usate le proprietà utente anziché le proprietà di codifica nel segmento nome argomento.
- i nomi delle proprietà sono digitati nella convenzione di denominazione "Dash-case" anziché nelle abbreviazioni con prefisso speciale. Le proprietà definite dall'utente richiedono invece il prefisso. Ad esempio, `$.mid` è ora `message-id` , mentre `myProperty1` diventa `@myProperty1` .
- La proprietà dati di correlazione viene utilizzata per correlare i messaggi di richiesta e risposta per le operazioni di richiesta-risposta anziché la `$rid` proprietà codificata nell'argomento.
- `iothub-connection-auth-method` la proprietà non è più contrassegnata per gli eventi di telemetria.
- I comandi C2D non verranno eliminati in assenza della sottoscrizione dal dispositivo. Rimarranno in coda fino a quando il dispositivo non viene sottoscritto o non scade.

## <a name="examples"></a>Esempio

### <a name="send-telemetry"></a>Inviare dati di telemetria

Messaggio:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

Riconoscimento:

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

Riconoscimento alternativo (limitato):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>Invia lo stato di Get gemello

Richiesta:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

Risposta (esito positivo):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

Risposta (non consentita):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>Gestire la chiamata al metodo diretto

Richiesta:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Risposta (esito positivo):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` non è impostato. si tratta di una risposta di esito positivo.

Risposta dispositivo non disponibile:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>Errore durante l'utilizzo di QoS 0, parte 1

Richiesta:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Risposta:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>Errore durante l'utilizzo di QoS 0, parte 2

Richiesta:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

Risposta:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>Passaggi successivi

- Per esaminare le informazioni di riferimento sull'API MQTT 5 Preview, vedere [informazioni di riferimento sull'API del piano dati dell'hub MQTT 5](iot-hub-mqtt-5-reference.md).
- Per seguire un esempio C#, vedere il [repository di esempio GitHub](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp).