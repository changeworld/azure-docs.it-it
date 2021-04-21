---
title: Riscrivere intestazioni HTTP e URL con gateway applicazione di Azure | Microsoft Docs
description: Questo articolo offre una panoramica della riscrittura di intestazioni e URL HTTP in gateway applicazione di Azure
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: b7cf7c98e71da215eb30dcab556a88d6d2701591
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789448"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Riscrivere intestazioni HTTP e URL con il gateway applicazione

Il gateway applicazione consente di riscrivere il contenuto selezionato di richieste e risposte. Con questa funzionalità è possibile tradurre URL, parametri di stringa di query, nonché modificare le intestazioni di richiesta e risposta. Consente inoltre di aggiungere condizioni per garantire che l'URL o le intestazioni specificate siano riscritte solo quando vengono soddisfatte determinate condizioni. Queste condizioni sono basate sulle informazioni della richiesta e della risposta.

>[!NOTE]
>Le funzionalità di riscrittura URL e intestazione HTTP sono disponibili solo per lo [SKU del gateway applicazione v2](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Tipi di riscrittura supportati

### <a name="request-and-response-headers"></a>Intestazioni di richiesta e risposta

Le intestazioni HTTP consentono a un client e a un server di passare informazioni aggiuntive con una richiesta o una risposta. Riscrivendo queste intestazioni, è possibile eseguire attività importanti, ad esempio l'aggiunta di campi di intestazione correlati alla sicurezza, ad esempio HSTS/X-XSS-Protection, la rimozione dei campi di intestazione della risposta che potrebbero rivelare informazioni riservate e la rimozione delle informazioni sulle porte dalle intestazioni X-Forwarded-For.

Il gateway applicazione permette di aggiungere, rimuovere o aggiornare le intestazioni di richieste e risposte HTTP durante lo spostamento dei pacchetti di richiesta e risposta tra il client e i pool back-end.

Per informazioni su come riscrivere le intestazioni di richiesta e risposta con il gateway applicazione portale di Azure, vedere [qui](rewrite-url-portal.md).

![immagine](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Intestazioni supportate**

È possibile riscrivere tutte le intestazioni nelle richieste e nelle risposte, ad eccezione delle intestazioni Connection e Upgrade. È anche possibile usare il gateway applicazione per creare intestazioni personalizzate e aggiungerle alle richieste e alle risposte instradati attraverso di esso.

### <a name="url-path-and-query-string"></a>Percorso URL e stringa di query

Con la funzionalità di riscrittura URL nel gateway applicazione, è possibile:

* Riscrivere il nome host, il percorso e la stringa di query dell'URL della richiesta 

* Scegliere di riscrivere l'URL di tutte le richieste in un listener o solo quelle che corrispondono a una o più condizioni impostate. Queste condizioni si basano sulle proprietà della richiesta e della risposta (richiesta, intestazione, intestazione della risposta e variabili del server).

* Scegliere di instradare la richiesta (selezionare il pool back-end) in base all'URL originale o all'URL riscritto

Per informazioni su come riscrivere l'URL con il gateway applicazione portale di Azure, vedere [qui](rewrite-url-portal.md).

![Diagramma che descrive il processo di riscrittura di un URL con il gateway applicazione.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Azioni di riscrittura

Le azioni di riscrittura vengono usate per specificare l'URL, le intestazioni della richiesta o le intestazioni di risposta da riscrivere e il nuovo valore in cui si intende riscriverle. Il valore di un URL o di un'intestazione nuova o esistente può essere impostato su questi tipi di valori:

* Testo
* Intestazione della richiesta. Per specificare un'intestazione della richiesta, è necessario usare la sintassi {http_req_ *headerName*}
* Intestazione della risposta. Per specificare un'intestazione di risposta, è necessario usare la sintassi {http_resp_ *headerName*}
* Variabile server. Per specificare una variabile del server, è necessario usare la sintassi {var_ *serverVariable*}. Vedere l'elenco delle variabili server supportate
* Combinazione di testo, intestazione di richiesta, intestazione di risposta e variabile del server. 

## <a name="rewrite-conditions"></a>Riscrittura delle condizioni

È possibile usare le condizioni di riscrittura, una configurazione facoltativa, per valutare il contenuto delle richieste e delle risposte HTTP(S) ed eseguire una riscrittura solo quando vengono soddisfatte una o più condizioni. Il gateway applicazione usa questi tipi di variabili per valutare il contenuto delle richieste e delle risposte:

* Intestazioni HTTP nella richiesta
* Intestazioni HTTP nella risposta
* Variabili del server del gateway applicazione

È possibile usare una condizione per valutare se una variabile specificata è presente, se una variabile specificata corrisponde a un valore specifico o se una variabile specificata corrisponde a un criterio specifico. 


### <a name="pattern-matching"></a>Criteri di ricerca 

Il gateway applicazione usa espressioni regolari per i criteri di ricerca nella condizione. È possibile usare la [libreria PCRE (Perl Compatible Regular Expressions)](https://www.pcre.org/) per configurare i criteri di ricerca di espressioni regolari nelle condizioni. Per informazioni sulla sintassi delle espressioni regolari, vedere la pagina [principale delle espressioni regolari Perl.](https://perldoc.perl.org/perlre.html)

### <a name="capturing"></a>Acquisizione

Per acquisire una sottostringa per un uso successivo, racchiudere tra parentesi il sottopattern corrispondente nella definizione dell'espressione regolare della condizione. La prima coppia di parentesi archivia la sottostringa in 1, la seconda coppia in 2 e così via. È possibile usare tutte le parentesi che si desidera; Perl continua a definire più variabili numerate per rappresentare queste stringhe acquisite. Alcuni esempi da [ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm): 

*  /(\d)(\d)/ # Corrisponde a due cifre, acquisendole nei gruppi 1 e 2 

* /(\d+)/ # Corrisponde a una o più cifre, acquisendole tutte nel gruppo 1 

* /(\d)+/ # Trova la corrispondenza con una cifra una o più volte, acquisendo l'ultima nel gruppo 1

Dopo l'acquisizione, è possibile fare riferimento a essi nel set di azioni usando il formato seguente:

* Per l'acquisizione di un'intestazione della richiesta, è necessario usare {http_req_headerName_groupNumber}. Ad esempio, {http_req_User-Agent_1} o {http_req_User-Agent_2}
* Per l'acquisizione di un'intestazione di risposta, è necessario usare {http_resp_headerName_groupNumber}. Ad esempio, {http_resp_Location_1} o {http_resp_Location_2}
* Per una variabile server, è necessario usare {var_serverVariableName_groupNumber}. Ad esempio, {var_uri_path_1} o {var_uri_path_2}

Se si vuole usare l'intero valore, è consigliabile non menzionare il numero. È sufficiente usare il formato {http_req_headerName} e così via senza groupNumber.

## <a name="server-variables"></a>Variabili del server

Il gateway applicazione usa le variabili del server per archiviare informazioni utili sul server, la connessione con il client e la richiesta corrente nella connessione. Esempi di informazioni archiviate includono l'indirizzo IP del client e il tipo di Web browser. Le variabili del server cambiano in modo dinamico, ad esempio quando viene caricata una nuova pagina o quando viene inviato un modulo. È possibile usare queste variabili per valutare le condizioni di riscrittura e riscrivere le intestazioni. Per usare il valore delle variabili server per riscrivere le intestazioni, è necessario specificare queste variabili nella sintassi {var_ *serverVariableName*}

Il gateway applicazione supporta le variabili server seguenti:

|   Nome variabile    |                   Descrizione                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Campo di intestazione della richiesta client X-Forwarded-For con la variabile (vedere la spiegazione più avanti in questa tabella) aggiunta nel formato `client_ip` IP1, IP2, IP3 e così via. Se il campo X-Forwarded-For non è presente nell'intestazione della richiesta client, la `add_x_forwarded_for_proxy` variabile è uguale alla variabile `$client_ip` .   Questa variabile è particolarmente utile quando si vuole riscrivere l'intestazione X-Forwarded-For impostata dal gateway applicazione in modo che l'intestazione contenga solo l'indirizzo IP senza le informazioni sulla porta. |
| ciphers_supported         | Elenco delle crittografia supportate dal client.               |
| ciphers_used              | Stringa di crittografia usata per una connessione TLS stabilita. |
| client_ip                 | Indirizzo IP del client da cui il gateway applicazione ha ricevuto la richiesta. Se è presente un proxy inverso prima del gateway applicazione e del client di origine, restituirà l'indirizzo `client_ip` IP del proxy inverso. |
| client_port               | Porta client.                                             |
| client_tcp_rtt            | Informazioni sulla connessione TCP del client. Disponibile nei sistemi che supportano l'opzione TCP_INFO socket. |
| client_user               | Quando si utilizza l'autenticazione HTTP, il nome utente fornito per l'autenticazione. |
| host                      | In questo ordine di precedenza: il nome host dalla riga della richiesta, il nome host dal campo Intestazione della richiesta host o il nome del server corrispondente a una richiesta. Esempio: nella richiesta `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` , il valore host sarà `contoso.com` |
| cookie_ *nome*             | Cookie *del* nome.                                           |
| http_method               | Metodo utilizzato per effettuare la richiesta dell'URL. Ad esempio, GET o POST. |
| http_status               | Stato della sessione. Ad esempio, 200, 400 o 403.           |
| http_version              | Protocollo di richiesta. In genere HTTP/1.0, HTTP/1.1 o HTTP/2.0. |
| query_string              | Elenco di coppie variabile/valore che segue "?" nell'URL richiesto. Esempio: nella `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` richiesta, query_string valore sarà `id=123&title=fabrikam` |
| received_bytes            | Lunghezza della richiesta( inclusa la riga della richiesta, l'intestazione e il corpo della richiesta). |
| request_query             | Argomenti nella riga della richiesta.                           |
| request_scheme            | Schema di richiesta: http o https.                           |
| request_uri               | URI completo della richiesta originale (con argomenti). Esempio: nella richiesta `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` il request_uri valore sarà `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Numero di byte inviati a un client.                        |
| server_port               | Porta del server che ha accettato una richiesta.              |
| ssl_connection_protocol   | Protocollo di una connessione TLS stabilita.               |
| ssl_enabled               | "On" se la connessione funziona in modalità TLS. In caso contrario, una stringa vuota. |
| uri_path                  | Identifica la risorsa specifica nell'host a cui il client Web vuole accedere. Questa è la parte dell'URI della richiesta senza gli argomenti. Esempio: nella `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` richiesta, uri_path valore sarà `/article.aspx` |

### <a name="mutual-authentication-server-variables-preview"></a>Variabili del server di autenticazione reciproca (anteprima)

Il gateway applicazione supporta le variabili server seguenti per scenari di autenticazione reciproca. Usare queste variabili server come sopra con le altre variabili del server. 

|   Nome variabile    |                   Descrizione                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | Certificato client in formato PEM per una connessione SSL stabilita. |
| client_certificate_end_date| Data di fine del certificato client. |
| client_certificate_fingerprint| Impronta digitale SHA1 del certificato client per una connessione SSL stabilita. |
| client_certificate_issuer | Stringa "Emittente DN" del certificato client per una connessione SSL stabilita. |
| client_certificate_serial | Numero di serie del certificato client per una connessione SSL stabilita.  |
| client_certificate_start_date| Data di inizio del certificato client. |
| client_certificate_subject| Stringa "DN soggetto" del certificato client per una connessione SSL stabilita. |
| client_certificate_verification| Risultato della verifica del certificato client: *SUCCESS,* *<reason> FAILED:* o *NONE* se non è presente un certificato. | 

## <a name="rewrite-configuration"></a>Riscrivere la configurazione

Per configurare una regola di riscrittura, è necessario creare un set di regole di riscrittura e aggiungervi la configurazione della regola di riscrittura.

Un set di regole di riscrittura contiene:

* **Richiedere l'associazione delle regole di routing:** La configurazione di riscrittura è associata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione di riscrittura è associata a un listener di origine ed è una riscrittura dell'intestazione globale. Quando si usa una regola di routing basata sul percorso, la configurazione di riscrittura viene definita nella mappa dei percorsi URL. In tal caso, si applica solo all'area del percorso specifico di un sito. È possibile creare più set di riscrittura e applicare ogni set di riscrittura a più listener. È tuttavia possibile applicare un solo set di riscrittura a un listener specifico.

* **Condizione di riscrittura:** configurazione facoltativa. Le condizioni di riscrittura valutano il contenuto delle richieste e delle risposte HTTP(S). L'azione di riscrittura si verificherà se la richiesta o la risposta HTTP(S) corrisponde alla condizione di riscrittura. Se si associano più condizioni a un'azione, l'azione si verifica solo quando vengono soddisfatte tutte le condizioni. In altre parole, l'operazione è un'operazione AND logica.

* **Tipo di** riscrittura: sono disponibili 3 tipi di riscrittura:
   * Riscrittura delle intestazioni delle richieste 
   * Riscrittura delle intestazioni di risposta
   * Riscrivere i componenti url
      * **Percorso URL:** valore in cui deve essere riscritto il percorso. 
      * **URL Query String (Stringa** di query URL): valore in cui deve essere riscritta la stringa di query. 
      * **Valuta di nuovo il mapping dei** percorsi: usato per determinare se la mappa dei percorsi URL deve essere valutata nuovamente o meno. Se l'opzione non è selezionata, il percorso URL originale verrà usato per trovare la corrispondenza con il modello di percorso nella mappa dei percorsi URL. Se impostato su true, la mappa dei percorsi URL verrà valutata nuovamente per verificare la corrispondenza con il percorso riscritto. L'abilitazione di questa opzione consente di instradare la richiesta a un pool back-end diverso dopo la riscrittura.

## <a name="rewrite-configuration-common-pitfalls"></a>Problemi comuni di riscrittura della configurazione

* L'abilitazione di 'Re-evaluate path map' non è consentita per le regole di routing delle richieste di base. Ciò consente di evitare il ciclo di valutazione infinito per una regola di routing di base.

* Deve essere presente almeno una regola di riscrittura condizionale o una regola di riscrittura che non dispone di "Re-evaluate path map" abilitata per le regole di routing basate sul percorso per impedire un ciclo di valutazione infinito per una regola di routing basata sul percorso.

* Le richieste in ingresso verrebbero terminate con un codice di errore 500 nel caso in cui un ciclo venga creato dinamicamente in base a input client. Il gateway applicazione continuerà a gestire altre richieste senza alcuna riduzione delle prestazioni in uno scenario di questo tipo.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>Uso della riscrittura URL o della riscrittura dell'intestazione host con Web Application Firewall (WAF_v2 SKU)

Quando si configura la riscrittura dell'URL o la riscrittura dell'intestazione host, la valutazione di WAF si verifica dopo la modifica dell'intestazione della richiesta o dei parametri URL (post-riscrittura). Quando si rimuove la configurazione di riscrittura url o riscrittura dell'intestazione host nel gateway applicazione, la valutazione waf verrà eseguita prima della riscrittura dell'intestazione (pre-riscrittura). Questo ordine garantisce che le regole waf siano applicate alla richiesta finale che verrebbe ricevuta dal pool back-end.

Si supponga, ad esempio, di avere la regola di riscrittura dell'intestazione seguente per l'intestazione . Se il valore dell'intestazione è uguale a , riscrivere `"Accept" : "text/html"` `"Accept"` il valore in `"text/html"` `"image/png"` .

In questo caso, con la sola riscrittura dell'intestazione configurata, la valutazione di WAF verrà eseguita in `"Accept" : "text/html"` . Tuttavia, quando si configura la riscrittura dell'URL o la riscrittura dell'intestazione host, la valutazione waf verrà eseguita in `"Accept" : "image/png"` .

>[!NOTE]
> Si prevede che le operazioni di riscrittura URL causeranno un lieve aumento dell'utilizzo della CPU del gateway applicazione WAF. È consigliabile monitorare la metrica di utilizzo della [CPU](high-traffic-support.md) per un breve periodo di tempo dopo aver abilitato le regole di riscrittura URL nel gateway applicazione WAF.

### <a name="common-scenarios-for-header-rewrite"></a>Scenari comuni per la riscrittura delle intestazioni

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Rimuovere le informazioni sulla porta dall'intestazione X-Forwarded-For

Il gateway applicazione inserisce un'intestazione X-Forwarded-For in tutte le richieste prima di inoltrare le richieste al back-end. Questa intestazione è un elenco delimitato da virgole di porte IP. In alcuni scenari i server back-end devono contenere solo le intestazioni per contenere gli indirizzi IP. È possibile usare la riscrittura dell'intestazione per rimuovere le informazioni sulla porta dall'intestazione X-Forwarded-For. Un modo per eseguire questa operazione è impostare l'intestazione sulla variabile add_x_forwarded_for_proxy server. In alternativa, è anche possibile usare la variabile client_ip:

![Rimuovere la porta](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Modificare un URL di reindirizzamento

Quando un'applicazione back-end invia una risposta di reindirizzamento, potrebbe essere necessario reindirizzare il client a un URL diverso da quello specificato dall'applicazione back-end. Ad esempio, è possibile eseguire questa operazione quando un servizio app è ospitato dietro un gateway applicazione e richiede al client di eseguire un reindirizzamento al percorso relativo. Ad esempio, un reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2.

Poiché il servizio app è un servizio multi-tenant, usa l'intestazione host nella richiesta per instradare la richiesta all'endpoint corretto. I servizi app hanno un nome di dominio predefinito azurewebsites.net (ad esempio contoso.azurewebsites.net) diverso dal nome di dominio del gateway applicazione (ad esempio \* contoso.com). Poiché la richiesta originale dal client ha il nome di dominio del gateway applicazione (contoso.com) come nome host, il gateway applicazione modifica il nome host in contoso.azurewebsites.net. Questa modifica viene apportata in modo che il servizio app possa indirizzare la richiesta all'endpoint corretto.

Quando il servizio app invia una risposta di reindirizzamento, usa lo stesso nome host nell'intestazione location della risposta come quello nella richiesta che riceve dal gateway applicazione. Il client effettua quindi la richiesta direttamente a `contoso.azurewebsites.net/path2` anziché passare attraverso il gateway applicazione ( `contoso.com/path2` ). Non è consigliabile ignorare il gateway applicazione.

È possibile risolvere questo problema impostando il nome host nell'intestazione del percorso sul nome di dominio del gateway applicazione.

Ecco i passaggi per sostituire il nome host:

1. Creare una regola di riscrittura con una condizione che valuta se l'intestazione location nella risposta contiene azurewebsites.net. Immettere il modello `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Eseguire un'azione per riscrivere l'intestazione location in modo che abbia il nome host del gateway applicazione. A tale scopo, immettere `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come valore dell'intestazione. In alternativa, è anche possibile usare la variabile server `host` per impostare il nome host in modo che corrisponda alla richiesta originale.

![Modificare l'intestazione della posizione](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementare intestazioni HTTP di sicurezza per evitare vulnerabilità

È possibile correggere diverse vulnerabilità della sicurezza implementando le intestazioni necessarie nella risposta dell'applicazione. Queste intestazioni di sicurezza includono X-XSS-Protection, Strict-Transport-Security e Content-Security-Policy. È possibile usare il gateway applicazione per impostare queste intestazioni per tutte le risposte.

![Intestazione di sicurezza](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Eliminare intestazioni indesiderate

È possibile rimuovere le intestazioni che rivelano informazioni riservate da una risposta HTTP. Ad esempio, è possibile rimuovere informazioni come il nome del server back-end, il sistema operativo o i dettagli della libreria. È possibile usare il gateway applicazione per rimuovere queste intestazioni:

![Eliminazione dell'intestazione](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Verificare la presenza di un'intestazione

È possibile valutare un'intestazione di richiesta o risposta HTTP per la presenza di un'intestazione o di una variabile server. Questa valutazione è utile quando si vuole eseguire una riscrittura dell'intestazione solo quando è presente una determinata intestazione.

![Controllo della presenza di un'intestazione](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Scenari comuni per la riscrittura url

#### <a name="parameter-based-path-selection"></a>Selezione del percorso basato su parametri

Per eseguire scenari in cui si vuole scegliere il pool back-end in base al valore di un'intestazione, parte dell'URL o della stringa di query nella richiesta, è possibile usare la combinazione di funzionalità di riscrittura URL e routing basato sul percorso. Ad esempio, se si dispone di un sito Web di acquisti e la categoria di prodotti viene passata come stringa di query nell'URL e si vuole instradare la richiesta al back-end in base alla stringa di query, quindi:

**Passaggio 1:**  Creare una mappa percorso come illustrato nell'immagine seguente

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="Scenario di riscrittura URL 1-1.":::

**Passaggio 2 (a):** Creare un set di riscrittura con 3 regole di riscrittura: 

* La prima regola ha una condizione che controlla la variabile *query_string* *per category=shoes* e dispone di un'azione che riscrive il percorso URL in */listing1* e ha **re-evaluate path map** abilitato

* La seconda regola ha una condizione che controlla la variabile *query_string* *per category=bags* e ha un'azione che riscrive il percorso URL a */listing2*  e ha **re-evaluate path map** abilitato

* La terza regola ha una condizione che controlla la variabile *query_string* *per category=accessories* e dispone di un'azione che riscrive il percorso URL a */listing3* e ha **re-evaluate path map** abilitato

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="Scenario di riscrittura URL 1-2.":::

 

**Passaggio 2 (b):** Associare questo set di riscrittura al percorso predefinito della regola basata sul percorso precedente

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="Scenario di riscrittura URL 1-3.":::

Ora, se l'utente richiede contoso.com/listing?category=any *,* verrà trovata una corrispondenza con il percorso predefinito perché nessuno dei modelli di percorso nella mappa dei percorsi (/listing1, /listing2, /listing3) corrisponderà. Poiché il set di riscrittura precedente è stato associato a questo percorso, questo set di riscrittura verrà valutato. Poiché la stringa di query non corrisponderà alla condizione in nessuna delle 3 regole di riscrittura in questo set di riscrittura, non verrà eseguita alcuna azione di riscrittura e pertanto la richiesta verrà indirizzata senza modifiche al back-end associato al percorso predefinito *(ovvero GenericList*).

Se l'utente *contoso.com/listing?category=shoes*, verrà trovata una corrispondenza per il percorso predefinito. Tuttavia, in questo caso la condizione nella prima regola corrisponderà e quindi verrà eseguita l'azione associata alla condizione che riscriverà il percorso URL in */listing1*  e valuterà nuovamente il mapping del percorso. Quando la mappa dei percorsi viene valutata nuovamente, la richiesta corrisponderà ora al percorso associato al modello */listing1* e la richiesta verrà instradata al back-end associato a questo modello, che è ShoesListBackendPool.

>[!NOTE]
>Questo scenario può essere esteso a qualsiasi valore di intestazione o cookie, percorso URL, stringa di query o variabili server in base alle condizioni definite e consente essenzialmente di instradare le richieste in base a tali condizioni.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Riscrivere i parametri della stringa di query in base all'URL

Si consideri uno scenario di un sito Web di acquisto in cui il collegamento visibile all'utente deve essere semplice e leggibile, ma il server back-end necessita dei parametri della stringa di query per visualizzare il contenuto giusto.

In tal caso, il gateway applicazione può acquisire i parametri dall'URL e aggiungere coppie chiave-valore della stringa di query da quelle dell'URL. Si supponga, ad esempio, che l'utente voglia riscrivere, in , che sia possibile ottenere tramite `https://www.contoso.com/fashion/shirts` la configurazione di riscrittura URL `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` seguente.

**Condizione:** se la variabile server `uri_path` è uguale al modello `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="Scenario di riscrittura URL 2-1.":::

**Azione** : impostare il percorso URL su `buy.aspx` e la stringa di query su `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="Scenario di riscrittura URL 2-2.":::

Per una guida dettagliata per ottenere lo scenario descritto in precedenza, vedere [Riscrivere l'URL](rewrite-url-portal.md) con il gateway applicazione usando portale di Azure

### <a name="url-rewrite-vs-url-redirect"></a>Riscrittura URL e reindirizzamento URL

Nel caso di una riscrittura URL, il gateway applicazione riscrive l'URL prima che la richiesta venga inviata al back-end. Ciò non modificherà ciò che gli utenti visualizzano nel browser perché le modifiche sono nascoste all'utente.

Nel caso di un reindirizzamento URL, il gateway applicazione invia una risposta di reindirizzamento al client con il nuovo URL. Ciò richiede a sua volta al client di inviare nuovamente la richiesta al nuovo URL specificato nel reindirizzamento. L'URL visualizzato dall'utente nel browser verrà aggiornato al nuovo URL.

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Riscrivere e reindirizzare.":::

## <a name="limitations"></a>Limitazioni

- Se una risposta ha più di un'intestazione con lo stesso nome, la riscrittura del valore di una di queste intestazioni comporta l'eliminazione delle altre intestazioni nella risposta. Questa operazione può in genere verificarsi Set-Cookie'intestazione poiché in una risposta possono essere presenti Set-Cookie'intestazione. Uno di questi scenari si verifica quando si usa un servizio app con un gateway applicazione e si è configurata l'affinità di sessione basata su cookie nel gateway applicazione. In questo caso la risposta conterrà due intestazioni Set-Cookie: una usata dal servizio app, ad esempio: e un'altra per l'affinità del `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` gateway applicazione, ad esempio `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . La riscrittura di una delle intestazioni Set-Cookie in questo scenario può comportare la rimozione dell'altra intestazione Set-Cookie dalla risposta.
- Le riscrittura non sono supportate quando il gateway applicazione è configurato per reindirizzare le richieste o per visualizzare una pagina di errore personalizzata.
- I nomi di intestazione possono contenere qualsiasi carattere alfanumerico e simboli specifici, come definito in [RFC 7230.](https://tools.ietf.org/html/rfc7230#page-27) Attualmente non è possibile supportare il carattere speciale di sottolineatura ( \_ ) nei nomi di intestazione.
- Le intestazioni di connessione e aggiornamento non possono essere riscritte

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come riscrivere le intestazioni HTTP con il gateway applicazione usando portale di Azure](rewrite-http-headers-portal.md)
- [Informazioni su come riscrivere l'URL con il gateway applicazione usando portale di Azure](rewrite-url-portal.md)
