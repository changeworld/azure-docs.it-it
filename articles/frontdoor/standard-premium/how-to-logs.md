---
title: Registrazione standard/Premium (anteprima) di Azure front door
description: Questo articolo illustra il funzionamento della registrazione in Azure front door standard/Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 531f4a9c9f535779e451ca316a8a5867f6cdaba5
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573898"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Registrazione standard/Premium (anteprima) di Azure front door

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Il front-end di Azure fornisce una registrazione diversa che consente di tenere traccia, monitorare ed eseguire il debug della porta anteriore. 

* I log di accesso contengono informazioni dettagliate su ogni richiesta ricevuta da AFD e consentono di analizzare e monitorare i modelli di accesso e di eseguire il debug dei problemi. 
* I log attività offrono visibilità sulle operazioni eseguite sulle risorse di Azure.  
* Log del probe di integrità fornisce i log per ogni Probe non riuscito per l'origine. 
* I log di Web Application Firewall (WAF) forniscono informazioni dettagliate sulle richieste che vengono registrate tramite la modalità di rilevamento o prevenzione di un endpoint di Azure front door. Un dominio personalizzato che viene configurato con WAF può essere visualizzato anche attraverso questi log.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Log di accesso, log del probe di integrità e log WAF non sono abilitati per impostazione predefinita. Attenersi alla procedura seguente per abilitare la registrazione. Le voci dei log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure. I log possono avere ritardi fino a pochi minuti. 

Sono disponibili tre opzioni di archiviazione dei log: 

* **Account di archiviazione:** Gli account di archiviazione sono ideali per gli scenari in cui i log vengono archiviati per un periodo di tempo più lungo ed esaminati quando necessario. 
* **Hub eventi:** Hub eventi è un'ottima opzione per l'integrazione con altri strumenti di gestione di informazioni ed eventi di sicurezza (SIEM) o archivi dati esterni. Ad esempio: Splunk/DataDog/Sumo. 
* **Log Analytics di Azure:** Azure Log Analytics in monitoraggio di Azure è ideale per il monitoraggio e l'analisi in tempo reale delle prestazioni di Azure front door.

## <a name="configure-logs"></a>Configurare i log

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Cercare Azure front door standard/Premium e selezionare il profilo di sportello anteriore di Azure.

1. Nel profilo passare a **monitoraggio** e selezionare impostazioni di **diagnostica**. Selezionare **Aggiungi impostazione di diagnostica**.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Screenshot della pagina di destinazione delle impostazioni di diagnostica.":::

1. In **impostazioni di diagnostica** immettere un nome per il  **nome delle impostazioni di diagnostica**.

1. Selezionare il  **log** da **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** e **FrontDoorWebApplicationFirewallLog**.

1. Selezionare i **Dettagli di destinazione**. Le opzioni di destinazione sono: 

    * **Invia a Log Analytics**
        * Selezionare la *sottoscrizione* e l'*area di lavoro Log Analytics*.
    * **Archivia in un account di archiviazione**
        * Selezionare la *sottoscrizione* e l'*account di archiviazione*. e imposta la **conservazione (giorni)**.
    * **Streaming in un hub eventi**
        * Selezionare la *sottoscrizione, lo spazio dei nomi dell'hub eventi, il nome dell'hub eventi (facoltativo)* e il *nome del criterio dell'hub eventi*. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Screenshot della pagina delle impostazioni di diagnostica.":::

1. Fare clic su **Save**.

## <a name="access-log"></a>Log di accesso

Il front-end di Azure fornisce attualmente le singole richieste API con ogni voce con lo schema seguente e registrato in formato JSON, come illustrato di seguito. 

| Proprietà | Descrizione |
|----------|-------------| 
| TrackingReference | Stringa di riferimento univoca che identifica una richiesta servita da AFD, inviata anche come intestazione X-Azure-Ref al client. Obbligatoria per la ricerca di dettagli nei log di accesso per una richiesta specifica. |
| Tempo | Data e ora in cui il Edge AFD ha recapitato il contenuto richiesto al client (in UTC). |
| HttpMethod | Metodo HTTP usato dalla richiesta: DELETE, GET, HEAD, OPTIONS, PATCH, POST o PUT. |
| HttpVersion | Versione HTTP specificata dal visualizzatore nella richiesta. |
| RequestUri | URI della richiesta ricevuta. Questo campo è uno schema completo, una porta, un dominio, un percorso e una stringa di query |
| HostName | Nome host nella richiesta dal client. Se si abilitano i domini personalizzati e si dispone del dominio con caratteri jolly (*. contoso.com), il nome host è a.contoso.com. Se si usa il dominio front-end di Azure (contoso.azurefd.net), il nome host è contoso.azurefd.net. |
| RequestBytes | Dimensioni del messaggio di richiesta HTTP in byte, inclusi intestazioni e corpo della richiesta. Numero di byte di dati inclusi nel Visualizzatore nella richiesta, incluse le intestazioni. |
| ResponseBytes | Byte inviati dal server back-end come risposta. |
| UserAgent | Tipo di browser usato dal client. |
| ClientIp | Indirizzo IP del client che ha effettuato la richiesta originale. Se nella richiesta è presente un'intestazione X-Inoltred-for, l'indirizzo IP del client viene selezionato dallo stesso. |
| SocketIp | Indirizzo IP della connessione diretta a AFD Edge. Se il client usa un proxy HTTP o un servizio di bilanciamento del carico per inviare la richiesta, il valore di SocketIp è l'indirizzo IP del proxy o del servizio di bilanciamento del carico. |
| Latenza | L'intervallo di tempo dall'ora del server perimetrale AFD riceve una richiesta del client al momento in cui AFD invia l'ultimo byte di risposta al client, in millisecondi. Questo campo non prende in considerazione la latenza di rete e il buffer TCP. |
| RequestProtocol | Protocollo specificato dal client nella richiesta: HTTP, HTTPS. |
| SecurityProtocol | Versione del protocollo TLS/SSL usata dalla richiesta o Null se non è stato usato alcun tipo di crittografia. I valori possibili includono: SSLv3, TLSv1, TLSv 1.1, TLSv 1.2 |
| SecurityCipher | Quando il valore per il protocollo di richiesta è HTTPS, questo campo indica la crittografia TLS/SSL negoziata dal client e da AFD per la crittografia. |
| Endpoint | Nome di dominio dell'endpoint AFD, ad esempio contoso.z01.azurefd.net |
| HttpStatusCode | Codice di stato HTTP restituito da AFD. |
| POP | POP perimetrale che ha risposto alla richiesta utente.  |
| Stato della cache | Fornisce il codice di stato del modo in cui la richiesta viene gestita dal servizio rete CDN per la memorizzazione nella cache. I valori possibili sono HIT: la richiesta HTTP è stata servita dalla cache POP AFD Edge. <br> **Miss**: la richiesta HTTP è stata servita dall'origine. <br/> **PARTIAL_HIT**: alcuni byte di una richiesta sono serviti dalla cache pop di AFD Edge, mentre alcuni dei byte sono serviti dall'origine per gli scenari di suddivisione in blocchi degli oggetti. <br> **CACHE_NOCONFIG**: inoltrare le richieste senza impostazioni di Caching, incluso lo scenario di bypass. <br/> **PRIVATE_NOSTORE**: nessuna cache configurata nelle impostazioni di Caching da clienti. <br> **REMOTE_HIT**: la richiesta è stata servita dalla cache del nodo padre. <br/> **N/A**:* * richiesta negata dall'URL firmato e dalle regole impostate. |
| MatchedRulesSetName | Nomi delle regole elaborate. |
| RouteName | Nome della route a cui corrisponde la richiesta. |
| ClientPort | Porta IP del client che ha effettuato la richiesta. |
| Referrer | URL del sito che ha originato la richiesta. |
| Tempi | La quantità di tempo, espressa in millisecondi, da AFD riceve la richiesta fino al momento in cui il primo byte viene inviato al client, come misurato in un front door di Azure. Questa proprietà non misura i dati client. |
| ErrorInfo | Questo campo fornisce informazioni dettagliate sul token di errore per ogni risposta. <br> **NOERROR**: indica che non è stato trovato alcun errore. <br> **CertificateError**: errore di certificato SSL generico. <br> **CertificateNameCheckFailed**: il nome host nel certificato SSL non è valido o non corrisponde. <br> **ClientDisconnected**: errore della richiesta a causa della connessione di rete del client. <br> **ClientGeoBlocked**: il client è stato bloccato a causa di una posizione geografica dell'IP. <br> **UnspecifiedClientError**: errore del client generico. <br> **InvalidRequest**: richiesta non valida. Potrebbe verificarsi a causa di un'intestazione, un corpo e un URL non validi. <br> **DNSFailure**: errore DNS. <br> **DNSTimeout**: si è verificato il timeout della query DNS per risolvere il back-end. <br> **DNSNameNotResolved**: non è stato possibile risolvere il nome o l'indirizzo del server. <br> **OriginConnectionAborted**: la connessione con l'origine è stata disconnessa in modo anomalo. <br> **OriginConnectionError**: errore di connessione all'origine generica. <br> **OriginConnectionRefused**: la connessione con l'origine non è stata stabilita. <br> **OriginError**: errore di origine generico. <br> **OriginInvalidRequest**: è stata inviata una richiesta non valida all'origine. <br> **ResponseHeaderTooBig**: l'origine ha restituito un valore troppo grande di un'intestazione di risposta. <br> **OriginInvalidResponse**:* * Origin ha restituito una risposta non valida o non riconosciuta. <br> **OriginTimeout**: il periodo di timeout per la richiesta di origine è scaduto. <br> **ResponseHeaderTooBig**: l'origine ha restituito un valore troppo grande di un'intestazione di risposta. <br> **RestrictedIP**: la richiesta è stata bloccata a causa dell'IP limitato. <br> **SSLHandshakeError**: Impossibile stabilire una connessione con l'origine a causa di un errore di scuotimento della mano SSL. <br> **SSLInvalidRootCA**: rootca non valido. <br> **SSLInvalidCipher**: la crittografia non è valida per la quale è stata stabilita la connessione HTTPS. <br> **OriginConnectionAborted**: la connessione con l'origine è stata disconnessa in modo anomalo. <br> **OriginConnectionRefused**: la connessione con l'origine non è stata stabilita. <br> **UnspecifiedError**: si è verificato un errore che non rientrava in nessuno degli errori della tabella. |
| OriginURL | URL completo dell'origine in cui vengono inviate le richieste. Composto dallo schema, dall'intestazione host, dalla porta, dal percorso e dalla stringa di query. <br> **URL Rewrite**: se è presente una regola di riscrittura URL nel set di regole, il percorso si riferisce al percorso riscritto. <br> **Cache nel pop Edge** Se si tratta di un riscontro nella cache al POP Edge, l'origine è N/A. <br> **Richiesta di grandi dimensioni** Se il contenuto richiesto è di grandi dimensioni con più richieste suddivise in blocchi tornando all'origine, questo campo corrisponderà alla prima richiesta all'origine. Per ulteriori informazioni, vedere la suddivisione in blocchi degli oggetti. |
| OriginIP | Indirizzo IP di origine che ha servito la richiesta. <br> **Riscontri nella cache sul pop Edge** Se si tratta di un riscontro nella cache al POP Edge, l'origine è N/A. <br> **Richiesta di grandi dimensioni** Se il contenuto richiesto è di grandi dimensioni con più richieste suddivise in blocchi tornando all'origine, questo campo corrisponderà alla prima richiesta all'origine. Per ulteriori informazioni, vedere la suddivisione in blocchi degli oggetti. |
| Originname| Nome DNS completo (nome host nell'URL di origine) all'origine. <br> **Riscontri nella cache sul pop Edge** Se si tratta di un riscontro nella cache al POP Edge, l'origine è N/A. <br> **Richiesta di grandi dimensioni** Se il contenuto richiesto è di grandi dimensioni con più richieste suddivise in blocchi tornando all'origine, questo campo corrisponderà alla prima richiesta all'origine. Per ulteriori informazioni, vedere la suddivisione in blocchi degli oggetti. |

## <a name="health-probe-log"></a>Log del probe di integrità

I log del probe di integrità forniscono la registrazione per ogni Probe non riuscito per facilitare la diagnosi dell'origine.I log forniranno informazioni che è possibile usare per riportare l'origine al servizio. Di seguito sono riportati alcuni scenari per i quali questo log può essere utile:

* Si è notato che il traffico della porta anteriore di Azure è stato inviato ad alcune delle origini. Ad esempio, solo tre di quattro origini ricevono traffico. Si desidera verificare se le origini ricevono Probe e, in caso contrario, il motivo dell'errore.  

* Si è notato che la percentuale di integrità dell'origine è inferiore al previsto e si vuole stabilire quale origine ha avuto esito negativo e il motivo dell'errore.

### <a name="health-probe-log-properties"></a>Proprietà del log del probe di integrità

Ogni log del probe di integrità presenta lo schema seguente.

| Proprietà | Descrizione |
| --- | --- |
| HealthProbeId  | ID univoco per identificare la richiesta. |
| Tempo | Ora di completamento del probe |
| HttpMethod | Metodo HTTP usato dalla richiesta del probe di integrità. I valori includono GET e HEAD, in base alle configurazioni del probe di integrità. |
| Risultato | Stato del probe di integrità a Origin, il valore include Success e altro testo di errore. |
| HttpStatusCode  | Codice di stato HTTP restituito dall'origine. |
| ProbeURL (destinazione) | URL completo dell'origine in cui vengono inviate le richieste. Composto dallo schema, dall'intestazione host, dal percorso e dalla stringa di query. |
| Originname  | Origine in cui vengono inviate le richieste. Questo campo consente di individuare le origini di interesse se l'origine è configurata su FDQN. |
| POP | Pop Edge, che ha inviato la richiesta di probe. |
| ID di origine | IP origine di destinazione. Questo campo è utile per individuare le origini di interesse se si configura l'origine usando FDQN. |
| TotolaLatency | Il tempo da AFDX Edge invia la richiesta all'origine all'ora di origine invia l'ultima risposta a AFDX Edge. |
| ConnectionLatency| Tempo trascorso per la configurazione della connessione TCP per inviare la richiesta di probe HTTP all'origine. | 
| Latenza DNSResolution | Durata del tempo impiegato per la risoluzione DNS se l'origine è configurata come FDQN anziché come IP. N/A se l'origine è configurata su IP. |

### <a name="health-probe-log-sample-in-json"></a>Esempio di log del probe di integrità in JSON

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Log attività

I log attività forniscono informazioni sulle operazioni eseguite in Azure front door standard/Premium. I log includono informazioni dettagliate su cosa, chi e quando un'operazione di scrittura è stata eseguita in uno sportello anteriore di Azure. 

> [!NOTE]
> I log attività non includono le operazioni GET. Non includono inoltre le operazioni eseguite tramite l'portale di Azure o l'API di gestione originale. 

Accedere ai log attività nel front-end o a tutti i log delle risorse di Azure in monitoraggio di Azure.

Per visualizzare i log di attività:

1. Selezionare il profilo della porta anteriore.

1. Selezionare **log attività.**

1. Scegliere un ambito di filtro e quindi fare clic su **applica**.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [report standard e Premium (anteprima) di Azure front door](how-to-reports.md).
- Informazioni sulle [metriche di monitoraggio in tempo reale di Azure front door standard e Premium (anteprima)](how-to-monitor-metrics.md).
