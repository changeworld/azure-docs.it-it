---
title: Informazioni di riferimento sull'API MQTT 5 per hub Azure (anteprima)
description: Informazioni di riferimento sulle API MQTT 5 dell'hub Internet
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603559"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>Informazioni di riferimento sull'API del piano dati dell'hub MQTT 5

Questo documento definisce le operazioni disponibili nella versione 2,0 (API-Version: `2020-10-01-preview` ) dell'API del piano dati dell'hub Internet.

## <a name="operations"></a>Gestione operativa

### <a name="get-twin"></a>Ottenere un dispositivo gemello

Ottenere lo stato del dispositivo gemello

#### <a name="request"></a>Richiesta

**Nome argomento:**`$iothub/twin/get`

**Proprietà**: nessuna

**Payload**: vuoto

#### <a name="success-response"></a>Risposta riuscita

**Proprietà**: nessuna

**Payload**: dispositivo gemello

#### <a name="alternative-responses"></a>Risposte alternative

| Stato | Nome | Descrizione |
| :----- | :--- | :---------- |
| 0100 |  Bad Request | Il formato del messaggio dell'operazione non è corretto e non può essere elaborato. |
| 0101 |  Non autorizzato | Il client non è autorizzato a eseguire l'operazione. |
| 0102 |  Non consentito | Operazione non consentita. |
| 0501 |  Sospensione causata dal servizio Microsoft FullText | la frequenza delle richieste è troppo elevata per SKU |
| 0502 |  Quota superata | è stata superata la quota giornaliera per lo SKU corrente |
| 0601 |  Errore del server | errore interno del server |
| 0602 |  Timeout | timeout dell'operazione prima del completamento |
| 0603 |  Server occupato | Server occupato |

#### <a name="pseudo-code-sample"></a>Esempio di pseudo-codice

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>Patch gemelli segnalato

Stato segnalato della patch gemello

#### <a name="request"></a>Richiesta

**Nome argomento:**`$iothub/twin/patch/reported`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| versione if | u64 | no |  |

**Payload**: TwinState

#### <a name="success-response"></a>Risposta riuscita

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| version | u64 | sì | Versione dello stato segnalato dopo l'applicazione della patch |

**Payload**: vuoto

#### <a name="alternative-responses"></a>Risposte alternative

| Stato | Nome | Descrizione |
| :----- | :--- | :---------- |
| 0104 |  Precondizione non riuscita | precondizione non soddisfatta perché la richiesta è stata annullata |
| 0100 |  Bad Request | Il formato del messaggio dell'operazione non è corretto e non può essere elaborato. |
| 0101 |  Non autorizzato | Il client non è autorizzato a eseguire l'operazione. |
| 0102 |  Non consentito | Operazione non consentita. |
| 0501 |  Sospensione causata dal servizio Microsoft FullText | la frequenza delle richieste è troppo elevata per SKU |
| 0502 |  Quota superata | è stata superata la quota giornaliera per lo SKU corrente |
| 0601 |  Errore del server | errore interno del server |
| 0602 |  Timeout | timeout dell'operazione prima del completamento |
| 0603 |  Server occupato | Server occupato |

#### <a name="pseudo-code-sample"></a>Esempio di pseudo-codice

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>Comandi Receive

Comandi Receive e handle

#### <a name="message"></a>Message

**Nome argomento:**`$iothub/commands`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| sequenza-No | u64 | sì | Numero di sequenza del messaggio |
| tempo di Accodamento | time | sì | Timestamp del momento in cui il messaggio è stato inserito nel sistema |
| delivery-count | piantare u32 | sì | Numero di tentativi di recapito dei messaggi |
| creation-time | time | no | Timestamp di quando è stato creato il messaggio (fornito dal mittente) |
| message-id | string | no | Identità del messaggio (fornita dal mittente) |
| user-id | string | no | Identità utente (fornita dal mittente) |
| correlation-id | string | no | Identità di correlazione (fornita dal mittente) |
| Tipo di contenuto | string | no | determina il tipo di contenuto del payload |
| content-encoding | string | no | determina la codifica del contenuto del payload |

**Payload**: qualsiasi sequenza di byte

#### <a name="success-acknowledgment"></a>Riconoscimento riuscito

Indica che il comando è stato accettato per la gestione da parte del client

**Proprietà**: nessuna

**Payload**: vuoto

#### <a name="alternative-acknowledgments"></a>Riconoscimenti alternativi

| Codice motivo | Stato | Nome | Descrizione |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Abbandono | Indica che il comando non verrà elaborato in questo momento e deve essere recapitato in futuro. |
| 131 | 0100 | Rifiuto | Indica che il comando è stato rifiutato dal client e non deve essere ritentato. |

#### <a name="pseudo-code-sample"></a>Esempio di pseudo-codice

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>Ricezione di metodi diretti

Ricevere e gestire le chiamate ai metodi diretti

#### <a name="request"></a>Richiesta

**Nome argomento:**`$iothub/methods/{name}`

**Proprietà**: nessuna

**Payload**: qualsiasi sequenza di byte

#### <a name="success-response"></a>Risposta riuscita

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| codice di risposta | piantare u32 | sì |  |

**Payload**: qualsiasi sequenza di byte

#### <a name="alternative-responses"></a>Risposte alternative

| Stato | Nome | Descrizione |
| :----- | :--- | :---------- |
| 06A0 |  Non disponibile | Indica che il client non è raggiungibile tramite questa connessione. |

#### <a name="pseudo-code-sample"></a>Esempio di pseudo-codice

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>Ricevere le modifiche dello stato desiderato del dispositivo gemello

Ricevere gli aggiornamenti allo stato desiderato del dispositivo gemello

#### <a name="message"></a>Message

**Nome argomento:**`$iothub/twin/patch/desired`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| version | u64 | sì | Versione dello stato desiderato corrispondente a questo aggiornamento |

**Payload**: TwinState

#### <a name="pseudo-code-sample"></a>Esempio di pseudo-codice

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>Inviare dati di telemetria

Inviare un messaggio al canale di telemetria: EventHubs per impostazione predefinita o un altro endpoint tramite la configurazione di routing.

#### <a name="message"></a>Message

**Nome argomento:**`$iothub/telemetry`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| Tipo di contenuto | string | no | converte nella `content-type` proprietà di sistema del messaggio inviato |
| content-encoding | string | no | converte nella `content-encoding` proprietà di sistema del messaggio inviato |
| message-id | string | no | converte nella `message-id` proprietà di sistema del messaggio inviato |
| user-id | string | no | converte nella `user-id` proprietà di sistema del messaggio inviato |
| correlation-id | string | no | converte nella `correlation-id` proprietà di sistema del messaggio inviato |
| creation-time | time | no | converte nella `iothub-creation-time-utc` proprietà del messaggio inviato |

**Payload**: qualsiasi sequenza di byte

#### <a name="success-acknowledgment"></a>Riconoscimento riuscito

Il messaggio è stato inviato al canale di telemetria

**Proprietà**: nessuna

**Payload**: vuoto

#### <a name="alternative-acknowledgments"></a>Riconoscimenti alternativi

| Codice motivo | Stato | Nome | Descrizione |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Bad Request | Il formato del messaggio dell'operazione non è corretto e non può essere elaborato. |
| 135 | 0101 | Non autorizzato | Il client non è autorizzato a eseguire l'operazione. |
| 131 | 0102 | Non consentito | Operazione non consentita. |
| 131 | 0601 | Errore del server | errore interno del server |
| 151 | 0501 | Sospensione causata dal servizio Microsoft FullText | la frequenza delle richieste è troppo elevata per SKU |
| 151 | 0502 | Quota superata | è stata superata la quota giornaliera per lo SKU corrente |
| 131 | 0602 | Timeout | timeout dell'operazione prima del completamento |
| 131 | 0603 | Server occupato | Server occupato |

#### <a name="pseudo-code-sample"></a>Esempio di pseudo-codice

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Risposte

### <a name="bad-request"></a>Bad Request

Il formato del messaggio dell'operazione non è corretto e non può essere elaborato.

**Codice motivo:**`131`

**Stato:**`0100`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contiene informazioni sugli elementi non validi per il messaggio |

**Payload**: vuoto

### <a name="conflict"></a>Conflitto

L'operazione è in conflitto con un'altra operazione in corso.

**Codice motivo:**`131`

**Stato:**`0103`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| Trace-ID | string | no | ID di traccia per la correlazione con la diagnostica aggiuntiva per l'errore |
| reason | string | no | contiene informazioni sugli elementi non validi per il messaggio |

**Payload**: vuoto

### <a name="not-allowed"></a>Non consentito

Operazione non consentita.

**Codice motivo:**`131`

**Stato:**`0102`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contiene informazioni sugli elementi non validi per il messaggio |

**Payload**: vuoto

### <a name="not-authorized"></a>Non autorizzato

Il client non è autorizzato a eseguire l'operazione.

**Codice motivo:**`135`

**Stato:**`0101`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| Trace-ID | string | no | ID di traccia per la correlazione con la diagnostica aggiuntiva per l'errore |

**Payload**: vuoto

### <a name="not-found"></a>Non trovato

la risorsa richiesta non esiste

**Codice motivo:**`131`

**Stato:**`0504`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contiene informazioni sugli elementi non validi per il messaggio |

**Payload**: vuoto

### <a name="not-modified"></a>Non modificato

La risorsa non è stata modificata in base alla precondizione specificata.

**Codice motivo:**`0`

**Stato:**`0001`

**Proprietà**: nessuna

**Payload**: vuoto

### <a name="precondition-failed"></a>Precondizione non riuscita

Precondizione non soddisfatta perché la richiesta è stata annullata

**Codice motivo:**`131`

**Stato:**`0104`

**Proprietà**: nessuna

**Payload**: vuoto

### <a name="quota-exceeded"></a>Quota superata

è stata superata la quota giornaliera per lo SKU corrente

**Codice motivo:**`151`

**Stato:**`0502`

**Proprietà**: nessuna

**Payload**: vuoto

### <a name="resource-exhausted"></a>Risorsa esaurita

la risorsa non ha capacità per completare l'operazione

**Codice motivo:**`131`

**Stato:**`0503`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contiene informazioni sugli elementi non validi per il messaggio |

**Payload**: vuoto

### <a name="server-busy"></a>Server occupato

Server occupato

**Codice motivo:**`131`

**Stato:**`0603`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| Trace-ID | string | no | ID di traccia per la correlazione con la diagnostica aggiuntiva per l'errore |

**Payload**: vuoto

### <a name="server-error"></a>Errore del server

errore interno del server

**Codice motivo:**`131`

**Stato:**`0601`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| Trace-ID | string | no | ID di traccia per la correlazione con la diagnostica aggiuntiva per l'errore |

**Payload**: vuoto

### <a name="target-failed"></a>Destinazione non riuscita

La destinazione ha risposto ma la risposta non è valida o non è valida

**Codice motivo:**`131`

**Stato:**`06A2`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contiene informazioni sugli elementi non validi per il messaggio |

**Payload**: vuoto

### <a name="target-timeout"></a>Timeout destinazione

timeout durante l'attesa del completamento della richiesta da parte della destinazione

**Codice motivo:**`131`

**Stato:**`06A1`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| Trace-ID | string | no | ID di traccia per la correlazione con la diagnostica aggiuntiva per l'errore |
| reason | string | no | contiene informazioni sugli elementi non validi per il messaggio |

**Payload**: vuoto

### <a name="target-unavailable"></a>Destinazione non disponibile

Destinazione non raggiungibile per completare la richiesta

**Codice motivo:**`131`

**Stato:**`06A0`

**Proprietà**: nessuna

**Payload**: vuoto

### <a name="throttled"></a>Sospensione causata dal servizio Microsoft FullText

la frequenza delle richieste è troppo elevata per SKU

**Codice motivo:**`151`

**Stato:**`0501`

**Proprietà**: nessuna

**Payload**: vuoto

### <a name="timeout"></a>Timeout

timeout dell'operazione prima del completamento

**Codice motivo:**`131`

**Stato:**`0602`

**Proprietà**:

| Nome | Tipo | Obbligatoria | Descrizione |
| :--- | :--- | :------- | :---------- |
| Trace-ID | string | no | ID di traccia per la correlazione con la diagnostica aggiuntiva per l'errore |

**Payload**: vuoto

