---
title: Report standard e Premium (anteprima) di Azure front door
description: Questo articolo illustra il funzionamento della creazione di report nel front-end di Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100270"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Report standard e Premium (anteprima) di Azure front door

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I report di analisi standard e Premium di Azure front door forniscono una panoramica incorporata e interamente approfondita del comportamento di Azure front door con le metriche associate al firewall dell'applicazione Web. È anche possibile sfruttare i registri di accesso per eseguire ulteriori operazioni di risoluzione dei problemi e debug. I report di analisi di Azure front door includono report sul traffico e report sulla sicurezza.

| Report | Dettagli |
|---------|---------|
| Panoramica delle metriche chiave | Mostra i dati complessivi che sono stati inviati dai bordi anteriore di Azure ai client<br/>-Larghezza di banda massima<br/>-Richieste <br/>-Percentuale riscontri cache<br/> -Latenza totale<br/>-frequenza degli errori di 5XX |
| Traffico per dominio | -Fornisce una panoramica di tutti i domini nel profilo<br/>-Suddivisione dei dati trasferiti da AFD Edge al client<br/>-Richieste totali<br/>-3XX/4XX/5XX-codice di risposta per dominio |
| Traffico per località | -Mostra una visualizzazione mappa della richiesta e dell'utilizzo da parte dei principali paesi<br/>-Visualizzazione tendenza dei paesi principali |
| Utilizzo | -Visualizza il trasferimento dei dati dal perimetro front-end di Azure ai client<br/>-Trasferimento dei dati dall'origine al perimetro AFD<br/>-Larghezza di banda da AFD Edge ai client<br/>-Larghezza di banda dall'origine al perimetro AFD<br/>-Richieste<br/>-Latenza totale<br/>-Numero di richieste tendenza per codice di stato HTTP |
| Memorizzazione nella cache | -Mostra la percentuale di riscontri nella cache per numero di richieste<br/>-Visualizzazione tendenza delle richieste hit e Miss |
| URL superiore | -Mostra il numero di richieste <br/>-Dati trasferiti <br/>-Percentuale riscontri cache <br/>-Distribuzione del codice di stato della risposta per le risorse 50 più richieste. |
| Referrer superiore | -Mostra il numero di richieste <br/>-Dati trasferiti <br/>-Percentuale riscontri cache <br/>-Distribuzione del codice di stato della risposta per i primi 50 referrer che generano traffico. |
| Agente utente principale | -Mostra il numero di richieste <br/>-Dati trasferiti <br/>-Percentuale riscontri cache <br/>-Distribuzione del codice di stato della risposta per i primi 50 agenti utente utilizzati per richiedere il contenuto. |

| Report sulla sicurezza | Dettagli |
|---------|---------|
| Panoramica delle metriche chiave | -Mostra le regole WAF corrispondenti<br/>-Regole OWASP corrispondenti<br/>-Regole BOT corrispondenti<br/>-Regole personalizzate corrispondenti |
| Metriche per dimensioni | -Ripartizione della tendenza delle regole WAF corrispondenti per azione<br/>-Grafico ad anello di eventi per tipo di set di regole ed evento per gruppo di regole<br/>-Elenco di suddivisione dei primi eventi in base all'ID regola, al paese, all'indirizzo IP, all'URL e all'agente utente  |

> [!NOTE]
> I report di sicurezza sono disponibili solo con lo SKU di Azure front door Premium.

La maggior parte dei report è basata sui log di accesso e viene offerta gratuitamente ai clienti sull'sportello anteriore di Azure. Il cliente non deve abilitare i log di accesso o eseguire alcuna configurazione per visualizzare questi report. I report sono accessibili tramite il portale e l'API. È supportato anche il download del file CSV. 

I report supportano qualsiasi intervallo di date selezionato tra i 90 giorni precedenti. Con punti dati di ogni 5 minuti, ogni ora o ogni giorno in base all'intervallo di date selezionato. In genere, è possibile visualizzare i dati con un ritardo di un'ora e talvolta con un ritardo di alcune ore. 

## <a name="access-reports-using-the-azure-portal"></a>Accedere ai report tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare il profilo standard/Premium di Azure front door.

1. Nel riquadro di spostamento selezionare **report o sicurezza** in *Analytics*.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Screenshot della pagina di destinazione dei report":::

1. Sono disponibili sette schede per dimensioni diverse, selezionare la dimensione di interesse.

   * Traffico per dominio
   * Utilizzo 
   * Traffico per località
   * Cache
   * URL superiore
   * Referrer superiore
   * Agente utente principale

1. Dopo aver scelto la dimensione, è possibile selezionare filtri diversi.
  
    1. **Mostra dati per** : selezionare l'intervallo di date per il quale si desidera visualizzare il traffico per dominio. Gli intervalli disponibili sono:
        
        * Ultime 24 ore
        * Ultimi 7 giorni
        * Ultimi 30 giorni
        * Ultimi 90 giorni
        * Mese corrente
        * Ultimo mese
        * Data personalizzata

         Per impostazione predefinita, i dati vengono visualizzati per gli ultimi sette giorni. Per le schede con grafici a linee, la granularità dei dati passa con gli intervalli di date selezionati come comportamento predefinito. 
    
        * 5 minuti: un punto dati ogni 5 minuti per gli intervalli di date inferiori o uguali a 24 ore.
        * Per ora: un dato ogni ora per gli intervalli di date tra 24 ore e 30 giorni
        * Per giorno: un dato al giorno per gli intervalli di date superiori a 30 giorni.

        È sempre possibile usare l'aggregazione per modificare la granularità di aggregazione predefinita. Nota: 5 minuti non funzionano per un intervallo di dati superiore a 14 giorni. 

    1. **Località** : selezionare una o più posizioni client in base al paese. I paesi sono raggruppati in sei aree: America del Nord, Asia, Europa, Africa, Oceania e Sud America. Vedere [mapping di aree/paesi](https://en.wikipedia.org/wiki/Subregion). Per impostazione predefinita, vengono selezionati tutti i paesi.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Screenshot dei report per la dimensione location.":::
   
    1. **Protocollo** : selezionare http o HTTPS per visualizzare i dati del traffico.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Screenshot dei report per la dimensione del protocollo.":::
    
    1. **Domini** : selezionare un singolo o più endpoint o domini personalizzati. Per impostazione predefinita, vengono selezionati tutti gli endpoint e i domini personalizzati. 
    
        * Se si elimina un endpoint o un dominio personalizzato in un profilo e quindi si ricrea lo stesso endpoint o dominio in un altro profilo. L'endpoint verrà considerato un secondo endpoint.  
        * Se i report vengono visualizzati in base al dominio personalizzato, quando si elimina un dominio personalizzato e lo si associa a un endpoint diverso. Verranno considerati come un dominio personalizzato. Se Visualizza per endpoint, verranno trattati come elementi distinti. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Screenshot dei report per la dimensione di dominio.":::

1. Se si desidera esportare i dati in un file CSV, selezionare il collegamento *Scarica CSV* nella scheda selezionata.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Screenshot del download del file CSV per i report.":::

### <a name="key-metrics-for-all-reports"></a>Metriche chiave per tutti i report

| Metrica | Descrizione |
|---------|---------|
| Dati trasferiti | Mostra i dati trasferiti dai pop perimetrali AFD al client per l'intervallo di tempo, i percorsi client, i domini e i protocolli selezionati. |
| Larghezza di banda massima | Picco di utilizzo della larghezza di banda in bit al secondo dal perimetro front-end di Azure viene visualizzato sul client per l'intervallo di tempo, le posizioni client, i domini e i protocolli selezionati. | 
| Totale richieste | Il numero di richieste che il dispositivo AFD Edge pop ha risposto al client per l'intervallo di tempo, i percorsi client, i domini e i protocolli selezionati. |
| Percentuale riscontri cache | Percentuale di tutte le richieste memorizzabili nella cache per le quali AFD ha servito il contenuto delle relative cache perimetrali per l'intervallo di tempo, le posizioni client, i domini e i protocolli selezionati. |
| Frequenza degli errori 5XX | Percentuale di richieste per cui il codice di stato HTTP al client era un 5XX per l'intervallo di tempo, i percorsi client, i domini e i protocolli selezionati. |
| Latenza totale | Latenza media di tutte le richieste per l'intervallo di tempo, i percorsi client, i domini e i protocolli selezionati. La latenza per ogni richiesta viene misurata come tempo totale di ricezione della richiesta client da parte di Azure prima dell'ultimo byte di risposta inviato da Azure front door al client. |

## <a name="traffic-by-domain"></a>Traffico per dominio

Il traffico per dominio fornisce una visualizzazione griglia di tutti i domini in questo profilo di porta anteriore di Azure. In questo report è possibile visualizzare: 
* Requests
* Dati trasferiti dalla porta di ingresso di Azure al client
* Richieste con codice di stato (3XX, 4Xx e 5XX) di ogni dominio

I domini includono endpoint e domini personalizzati, come spiegato nella sessione di accesso al report.  

È possibile passare ad altre schede per esaminare ulteriormente o visualizzare il log di accesso per ulteriori informazioni se si trovano le metriche sotto la previsione. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Screenshot della pagina di destinazione per i report":::


## <a name="usage"></a>Utilizzo

Questo report Mostra le tendenze del codice di stato del traffico e della risposta in base a dimensioni diverse, tra cui:

* Dati trasferiti da Edge a client e da Origin a Edge nel grafico a linee. 

* Dati trasferiti da Edge a client per protocollo nel grafico a linee. 

* Numero di richieste da Edge a client nel grafico a linee.  

* Numero di richieste da Edge a client per protocollo, HTTP e HTTPS, nel grafico a linee. 

* Larghezza di banda da Edge a client nel grafico a linee. 

* Latenza totale, che misura il tempo totale dalla richiesta client ricevuta dalla porta anteriore finché l'ultimo byte di risposta inviato dalla porta anteriore al client.

* Numero di richieste da Edge a client per codice di stato HTTP, in grafico a linee. Ogni richiesta genera un codice di stato HTTP. Il codice di stato HTTP viene visualizzato in HTTPStatusCode nel log non elaborato. Il codice di stato descrive il modo in cui Edge della rete CDN ha gestito la richiesta. Ad esempio, un codice di stato 2xx indica che la richiesta è stata servita correttamente a un client. Mentre un codice di stato 4xx indica che si è verificato un errore. Per altre informazioni sui codici di stato HTTP, vedere Codici di stato HTTP. 

* Numero di richieste dal perimetro ai client tramite codice di stato HTTP. Percentuale di richieste per codice di stato HTTP tra tutte le richieste nella griglia. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Screenshot dei report per utilizzo" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Traffico per località

Questo report Visualizza le prime 50 località in base al paese dei visitatori che accedono al proprio asset. Il report fornisce anche un riepilogo delle metriche in base al paese e offre una visualizzazione complessiva dei paesi in cui viene generato il maggior numero di traffico. Infine, è possibile vedere quale paese presenta una percentuale di riscontri nella cache maggiore o codici di errore 4XX/5XX.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Screenshot dei report per località" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

Nei report sono inclusi i seguenti elementi:

* Una vista mappa mondiale dei principali 50 paesi in base ai dati trasferiti o alle richieste di tua scelta.
* Due grafici a linee-visualizzazione delle tendenze dei primi cinque paesi per dati trasferiti e richieste di propria scelta. 
* Una griglia dei paesi principali con i dati corrispondenti trasferiti da AFD ai client, i dati trasferiti fuori% di tutti i paesi, le richieste, il% di richiesta tra tutti i paesi, la percentuale di riscontri nella cache, il codice di risposta 4XX e il codice di risposta 5XX.

## <a name="caching"></a>Memorizzazione nella cache

La memorizzazione nella cache dei report fornisce una visualizzazione del grafico di riscontri/mancati riscontri nella cache e percentuale di riscontri nella cache in base Queste metriche chiave spiegano in che modo la rete CDN memorizza nella cache il contenuto, in quanto i risultati più rapidi delle prestazioni dei riscontri nella cache È possibile ottimizzare le velocità di recapito dei dati riducendo al minimo i riscontri nella cache Il report include:

* Tendenza numero di riscontri e mancati riscontri nella cache, in grafico a linee.

* Percentuale riscontri cache nel grafico a linee.

Riscontri/mancati riscontri nella cache descrivono i riscontri nella cache dei numeri di richiesta e i mancati riscontri

* Riscontri: le richieste del client che vengono gestite direttamente dai server perimetrali della rete CDN di Azure. Si riferisce a tali richieste i cui valori per CacheStatus nei log non elaborati sono HIT, PARTIAL_HIT o REMOTE HIT. 

* Miss: le richieste del client che vengono gestite dai server perimetrali della rete CDN di Azure recuperano il contenuto dall'origine. Si riferisce a tali richieste i cui valori per il campo CacheStatus nei log non elaborati sono MISS. 

Percentuale **riscontri cache** descrive la percentuale di richieste memorizzate nella cache che vengono gestite direttamente da Edge. La formula della percentuale di riscontri nella cache è: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` . 

Questo report prende in considerazione gli scenari di memorizzazione nella cache e le richieste che soddisfano i requisiti seguenti vengono eseguite nel calcolo. 

* Il contenuto richiesto è stato memorizzato nella cache nel POP più vicino al richiedente o allo scudo di origine. 

* Contenuto parzialmente memorizzato nella cache per la suddivisione in blocchi degli oggetti.

Esclude tutti i casi seguenti: 

* Richieste negate a causa del set di regole.

* Richieste che contengono regole di corrispondenza impostate impostate su Disabled cache. 

* Richieste bloccate da WAF. 

* Le intestazioni della risposta Origin indicano che non devono essere memorizzate nella cache. Ad esempio, le intestazioni Cache-Control: private, Cache-Control: no-cache o Pragma: no-cache impediscono la memorizzazione nella cache di un asset. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Screenshot dei report per la memorizzazione nella cache.":::

## <a name="top-urls"></a>URL principali

Gli URL principali consentono di visualizzare la quantità di traffico che si verifica su un particolare endpoint o dominio personalizzato. Verranno visualizzati i dati per le risorse 50 più richieste durante qualsiasi periodo negli ultimi 90 giorni. Gli URL più diffusi verranno visualizzati con i valori seguenti. L'utente può ordinare gli URL in base al numero di richieste, alla richiesta, al trasferimento dei dati e al trasferimento dei dati. Tutte le metriche vengono aggregate in base all'ora e possono variare in base all'intervallo di tempo selezionato. L'URL fa riferimento al valore di RequestUri nel log di accesso. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Screenshot dei report per l'URL superiore.":::

* URL, si riferisce al percorso completo dell'asset richiesto nel formato `http(s)://contoso.com/index.html/images/example.jpg` . 
* Conteggi della richiesta. 
* Richiedi% delle richieste totali gestite dal front-end di Azure. 
* Dati trasferiti. 
* % Dati trasferiti. 
* Percentuale riscontri cache
* Richieste con codice di risposta come 4XX
* Richieste con codice di risposta come 5XX

> [!NOTE]
> Gli URL più frequenti possono cambiare nel tempo e ottenere un elenco accurato dei primi 50 URL, ovvero tutte le richieste URL per ora e mantengono il totale parziale nel corso di un giorno. Gli URL nella parte inferiore degli URL 500 possono aumentare o diminuire l'elenco nel corso della giornata, quindi il numero totale di questi URL è approssimativo.  
>
> I primi 50 URL potrebbero rientrare nell'elenco, ma raramente scompaiono dall'elenco, quindi i numeri degli URL più frequenti sono in genere affidabili. Quando un URL scende dall'elenco e aumenta di nuovo in un giorno, il numero di richieste durante il periodo in cui mancano nell'elenco viene stimato in base al numero di richiesta dell'URL visualizzato in tale periodo.  
>
> La stessa logica si applica a Top User Agent. 

## <a name="top-referrers"></a>Referrer principali

I referrer principali consentono ai clienti di visualizzare i primi 50 referrer che hanno originato il maggior numero di richieste al contenuto in un particolare endpoint o dominio personalizzato. È possibile visualizzare i dati per qualsiasi periodo negli ultimi 90 giorni. Un referrer indica l'URL da cui è stata generata una richiesta. Il referrer può provenire da un motore di ricerca o da altri siti Web. Se un utente digita un URL, ad esempio http (s)://contoso.com/index.html) direttamente nella riga dell'indirizzo di un browser, il referrer per l'oggetto richiesto è "Empty". Il report dei referrer principali include i valori seguenti. È possibile ordinare per numero di richieste,% di richiesta, dati trasferiti e% di dati trasferiti. Tutte le metriche vengono aggregate in base all'ora e possono variare in base all'intervallo di tempo selezionato. 

* Referrer, il valore del referrer nei log non elaborati 
* Numero di richieste 
* Richiedi% delle richieste totali gestite dalla rete CDN di Azure nel periodo di tempo selezionato. 
* Dati trasferiti 
* % Dati trasferiti 
* Percentuale riscontri cache
* Richieste con codice di risposta come 4XX
* Richieste con codice di risposta come 5XX

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Screenshot dei report per il referrer superiore.":::

## <a name="top-user-agent"></a>Agente utente principale

Questo report consente di visualizzare grafica e statistiche dei primi 50 agenti utente utilizzati per richiedere il contenuto. Ad esempio,
* Mozilla/5.0 (Windows NT 10,0; WOW64 
* AppleWebKit/537.36 (KHTML, ad esempio Gecko) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

Una griglia consente di visualizzare i conteggi delle richieste, la percentuale di richieste, i dati trasferiti e i dati trasferiti, la percentuale di riscontri nella cache, le richieste con codice di risposta come 4XX e le richieste con codice di risposta 5XX. L'agente utente si riferisce al valore di UserAgent nei log di accesso.

## <a name="security-report"></a>Report sulla sicurezza

Questo report consente di ottenere una visualizzazione grafica e statistiche dei modelli WAF in base a dimensioni diverse.

| Dimensioni | Descrizione |
|---------|---------|
| Cenni preliminari sulle metriche-regole WAF corrispondenti | Richieste corrispondenti a regole WAF personalizzate, regole WAF gestite e gestione bot. |
| Metriche di panoramica-richieste bloccate | Percentuale di richieste bloccate dalle regole WAF tra tutte le richieste corrispondenti alle regole WAF. |
| Cenni preliminari sulle metriche-regole gestite corrispondenti | Quattro tendenze dei grafici a linee per le richieste bloccate, log, Allow e redirect. |
| Metrica Panoramica-regola personalizzata corrispondente | Richieste che corrispondono alle regole WAF personalizzate. |
| Metrica Panoramica-regola bot corrispondente | Richieste corrispondenti a bot Manager. |
| Tendenza richiesta WAF per azione | Quattro tendenze dei grafici a linee per le richieste bloccate, log, Allow e redirect. |
| Eventi per tipo di regola | Grafico ad anello di WAF richiede la distribuzione in base al tipo di regola, ad esempio bot, regole personalizzate e regole gestite. |
| Eventi per gruppo di regole | Grafico ad anello della distribuzione delle richieste WAF per gruppo di regole. |
| Richieste per azioni | Tabella di richieste per azioni, in ordine decrescente. |
| Richieste per ID regola superiore | Tabella di richieste in base ai primi 50 ID regola, in ordine decrescente. |
| Richieste per i paesi principali |  Tabella di richieste per i primi 50 paesi, in ordine decrescente. |
| Richieste per indirizzi IP client principali |  Tabella di richieste da parte dei principali indirizzi IP 50, in ordine decrescente. |
| Richieste per URL di richiesta superiore |  Tabella di richieste per i primi 50 URL, in ordine decrescente. |
| Richieste per nome host più frequenti | Tabella di richieste per primo nome host 50, in ordine decrescente. |
| Richieste per agenti utente principali | Tabella di richieste da parte dei primi 50 agenti utente, in ordine decrescente. |

## <a name="cvs-format"></a>Formato CVS

È possibile scaricare i file CSV per schede diverse nei report. In questa sezione vengono descritti i valori in ogni file CSV.

### <a name="general-information-about-the-cvs-report"></a>Informazioni generali sul report CVS

Ogni report CSV include alcune informazioni generali e le informazioni sono disponibili in tutti i file CSV. con le variabili basate sul report da scaricare. 


| Valore | Descrizione |
|---------|---------|
| Report | Nome del report. | 
| Domains | Elenco di endpoint o domini personalizzati per il report. |
| StartDateUTC | Inizio dell'intervallo di date per il quale è stato generato il report, in formato UTC (Coordinated Universal Time) |
| EndDateUTC | Fine dell'intervallo di date per il quale è stato generato il report, in formato UTC (Coordinated Universal Time) |
| GeneratedTimeUTC | Data e ora in cui è stato generato il report in formato UTC (Coordinated Universal Time) |
| Location | Elenco dei paesi in cui sono state originate le richieste del client. Per impostazione predefinita, il valore è tutto. Non applicabile al report di sicurezza. |
| Protocollo | Protocollo della richiesta, HTTP o HTTPs. Non applicabile all'URL e al traffico principali per agente utente nei report e nel rapporto di sicurezza. |
| Aggregazione | Granularità dell'aggregazione dei dati in ogni riga, ogni 5 minuti, ogni ora e ogni giorno. Non applicabile al traffico per dominio, URL principale e traffico per agente utente nei report e nel rapporto di sicurezza. |

### <a name="data-in-traffic-by-domain"></a>Dati nel traffico per dominio

* Dominio 
* Totale richiesta 
* Percentuale riscontri cache 
* Richieste 3XX 
* Richieste 4XX 
* Richieste 5XX 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Dati nel traffico per località

* Location
* TotalRequests
* Richiesta
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Dati in uso

Il file CSV contiene tre report. Uno per il protocollo HTTP, uno per il protocollo HTTPS e uno per il codice di stato HTTP. 

I report per HTTP e HTTPs condividono lo stesso set di dati. 

* Tempo 
* Protocollo 
* DataTransfer (byte) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

Report per il codice di stato HTTP. 

* Tempo 
* DataTransfer (byte) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Dati nella cache 

* Tempo
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>Dati nell'URL superiore 

* URL 
* TotalRequests 
* Richiesta 
* DataTransfer (byte) 
* Metadatitrasferiti 

### <a name="data-in-user-agent"></a>Dati nell'agente utente 

* UserAgent 
* TotalRequests 
* Richiesta 
* DataTransfer (byte) 
* Metadatitrasferiti 

### <a name="security-report"></a>Report sulla sicurezza 

Sono presenti sette tabelle con gli stessi campi indicati di seguito.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

Le sette tabelle sono per ora, ID regola, paese, indirizzo IP, URL, nome host, agente utente. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [metriche di monitoraggio di Azure front door standard/Premium in tempo reale](how-to-monitor-metrics.md).
