---
title: Diagnostica con metriche, avvisi e integrità delle risorse-Azure Load Balancer Standard
description: Usare le metriche disponibili, gli avvisi e le informazioni di integrità delle risorse per diagnosticare la Load Balancer Standard di Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2021
ms.author: allensu
ms.openlocfilehash: 29584a9453fa052745f417cba0bbe940766c30e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699080"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnostica di Load Balancer Standard con metriche, avvisi e integrità delle risorse

Azure Load Balancer Standard espone le funzionalità di diagnostica seguenti:

* **Metriche e avvisi multidimensionali**: fornisce funzionalità di diagnostica multidimensionali tramite [monitoraggio di Azure](../azure-monitor/overview.md) per le configurazioni di Load Balancer standard. È possibile monitorare, gestire e risolvere i problemi relativi alle risorse di Load Balancer standard.

* **Integrità risorse**: lo stato Integrità risorse della Load Balancer è disponibile nella pagina integrità risorse in monitoraggio. Questo controllo automatico informa l'utente della disponibilità corrente della risorsa Load Balancer.
Questo articolo offre una breve panoramica di queste funzionalità e illustra come usarle per Load Balancer Standard. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Metriche multidimensionali

Azure Load Balancer fornisce metriche multidimensionali tramite le metriche di Azure nella portale di Azure e consente di ottenere informazioni di diagnostica in tempo reale sulle risorse di bilanciamento del carico. 

Le varie configurazioni Load Balancer Standard forniscono le seguenti metriche:

| Metrica | Tipo di risorsa | Descrizione | Aggregazione consigliata |
| --- | --- | --- | --- |
| Disponibilità del percorso dati | Servizio di bilanciamento del carico interno e pubblico | Load Balancer Standard esercita continuamente il percorso dati dall'interno di un'area al front-end di Load Balancer e infine allo stack SDN che supporta la macchina virtuale. Finché sono presenti istanze integre, la misurazione segue lo stesso percorso del traffico con bilanciamento del carico dell'applicazione. Viene anche convalidato il percorso dati usato dai clienti. La misurazione è invisibile all'applicazione e non interferisce con altre operazioni.| Media |
| Stato del probe di integrità | Servizio di bilanciamento del carico interno e pubblico | Load Balancer Standard usa un servizio di probe dell'integrità distribuito che monitora l'integrità dell'endpoint dell'applicazione in base alle impostazioni di configurazione. Questa metrica offre una visualizzazione filtrata, aggregata o per endpoint di ogni endpoint dell'istanza nel pool di Load Balancer. In questo modo è possibile visualizzare l'integrità dell'applicazione rilevata da Load Balancer, in base alla configurazione del probe di integrità. |  Media |
| Conteggio SYN (Synchronize) | Servizio di bilanciamento del carico interno e pubblico | Load Balancer Standard non termina le connessioni TCP (Transmission Control Protocol), né interagisce con i flussi di pacchetti TCP o UDP. I flussi e i relativi handshake sono sempre tra l'origine e l'istanza VM. Per risolvere meglio i problemi degli scenari del protocollo TCP, è possibile usare contatori di pacchetti SYN per determinare quanti tentativi di connessione TCP vengono eseguiti. La metrica indica il numero di pacchetti SYN TCP ricevuti.| Sum |
| Conteggio connessioni SNAT | Bilanciamento del carico pubblico |Load Balancer Standard segnala il numero di flussi in uscita mascherati per il front-end dell'indirizzo IP pubblico. Le porte Source Network Address Translation (SNAT) sono una risorsa esauribile. Questa metrica può indicare l'uso che l'applicazione fa di SNAT per i flussi originati in uscita. Vengono segnalati i contatori per i flussi SNAT con esito positivo e negativo, che è possibile usare per risolvere i problemi e comprendere l'integrità dei flussi in uscita.| Sum |
| Porte SNAT allocate | Bilanciamento del carico pubblico | Load Balancer Standard segnala il numero di porte SNAT allocate per ogni istanza di back-end | Media. |
| Porte SNAT utilizzate | Bilanciamento del carico pubblico | Load Balancer Standard segnala il numero di porte SNAT utilizzate per ogni istanza di back-end. | Media | 
| Conteggio byte |  Servizio di bilanciamento del carico interno e pubblico | Load Balancer Standard restituisce i dati elaborati per ogni front-end. È possibile notare che i byte non sono distribuiti equamente tra le istanze back-end. Questa operazione è prevista perché l'algoritmo Load Balancer di Azure è basato sui flussi | Sum |
| Numero di pacchetti |  Servizio di bilanciamento del carico interno e pubblico | Load Balancer Standard restituisce i pacchetti elaborati per ogni front-end.| Sum |

  >[!NOTE]
  >Quando si usa la distribuzione del traffico da un servizio di bilanciamento del carico interno tramite un pacchetto di appliance virtuale di virtualizzazione o un pacchetto SYN firewall, il numero di byte e le metriche del conteggio dei pacchetti non sono disponibili e verranno visualizzati come zero. 
  
  >[!NOTE]
  >Le aggregazioni Max e min non sono disponibili per il numero SYN, il numero di pacchetti, il conteggio delle connessioni SNAT e la metrica per il conteggio dei byte 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Consente di visualizzare le metriche di Load Balancer nel portale di Azure

Il portale di Azure espone le metriche del servizio di bilanciamento del carico tramite la pagina metrica, disponibile sia nella pagina delle risorse del servizio di bilanciamento del carico per una determinata risorsa che nella pagina Monitoraggio di Azure. 

Per visualizzare le metriche delle risorse di Load Balancer Standard:
1. Passare alla pagina metrica ed eseguire una delle operazioni seguenti:
   * Nella pagina delle risorse di Load Balancer, selezionare il tipo di metrica nell'elenco a discesa.
   * Nella pagina Monitoraggio di Azure, selezionare la risorsa di Load Balancer.
2. Impostare il tipo di aggregazione delle metriche appropriato.
3. Facoltativamente, configurare il filtro e il raggruppamento necessari.
4. Facoltativamente, configurare l'intervallo di tempo e l'aggregazione. Per impostazione predefinita, l'ora viene visualizzata in formato UTC.

  >[!NOTE] 
  >L'aggregazione temporale è importante quando si interpretano determinate metriche quando si campionano i dati una volta al minuto. Se l'aggregazione temporale è impostata su cinque minuti e il tipo di aggregazione metrica Sum viene usato per le metriche, ad esempio l'allocazione SNAT, il grafico visualizzerà cinque volte le porte SNAT allocate totali. 

![Metriche per Load Balancer Standard](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Figura: metrica di disponibilità del percorso dati per Load Balancer Standard*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperare le metriche multidimensionali a livello di codice tramite le API

Per informazioni sull'API per il recupero dei valori e delle definizioni delle metriche multidimensionali, vedere la [Procedura dettagliata di API REST di Azure Monitoring](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-definitions-multi-dimensional-api). Queste metriche possono essere scritte in un account di archiviazione aggiungendo un' [impostazione di diagnostica](../azure-monitor/essentials/diagnostic-settings.md) per la categoria ' tutte le metriche '. 

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Scenari di diagnostica comuni e visualizzazioni consigliate

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>Il percorso dati è attivo e disponibile per il Front-End Load Balancer?
<details><summary>Espandere</summary>

La metrica di disponibilità del percorso dati descrive l'integrità del percorso dati all'interno dell'area per l'host di calcolo in cui si trovano le macchine virtuali. La metrica riflette lo stato di integrità delle infrastrutture di Azure. La metrica può essere utilizzata per:
- Monitorare la disponibilità esterna del servizio.
- Eseguire un approfondimento e comprendere se la piattaforma in cui viene distribuito il servizio è integra o se il sistema operativo guest o l'istanza dell'applicazione è integra.
- Isolare se un evento è correlato al servizio o al piano dati sottostante. Non confondere questa metrica con lo stato del probe di integrità ("disponibilità dell'istanza back-end").

Per ottenere la disponibilità del percorso dati per le risorse di Load Balancer Standard:
1. Assicurarsi che sia selezionata la risorsa di Load Balancer corretta. 
2. Nell'elenco a discesa **metrica** selezionare **disponibilità percorso dati**. 
3. Nell’elenco a discesa **Aggregazione**, selezionare **Media**. 
4. Aggiungere inoltre un filtro per l'indirizzo IP front-end o la porta front-end come dimensione con l'indirizzo IP front-end o la porta front-end necessaria, quindi raggrupparli in base alla dimensione selezionata.

![Esecuzione del probe VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura: dettagli del sondaggio del Front-End Load Balancer*

La metrica viene generata da una misura attiva in banda. Un servizio di esecuzione del probe all'interno della regione genera traffico per la misura. Il servizio viene attivato non appena si crea una distribuzione con un front end pubblico e continua fino a quando non viene rimosso. 

Viene generato periodicamente un pacchetto corrispondente alla regola e al front-end di distribuzione che attraversa l'area dall'origine all'host in cui si trova una macchina virtuale nel pool back-end. L'infrastruttura di Load Balancer esegue le stesse operazioni di traslazione e bilanciamento del carico che vengono effettuate per tutto il resto del traffico. Questo probe è in banda nell'endpoint con carico bilanciato. Dopo che il probe arriva nell'host di calcolo in cui si trova una macchina virtuale integra nel pool back-end, l'host di calcolo genera una risposta al servizio di esecuzione del probe. La macchina virtuale non vede questo traffico.

La disponibilità dei percorsi di DataPath non riesce per i motivi seguenti:
- La distribuzione non ha macchine virtuali integre rimanenti nel pool back-end. 
- Si è verificata un'interruzione dell'infrastruttura.

Ai fini della diagnostica, è possibile usare la [metrica di disponibilità del percorso dati insieme allo stato del probe di integrità](#vipavailabilityandhealthprobes).

Usare **Media** come aggregazione per la maggior parte degli scenari.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>Le istanze back-end per My Load Balancer rispondono ai Probe?
<details>
  <summary>Espandere</summary>
La metrica relativa allo stato del probe di integrità descrive l'integrità della distribuzione dell'applicazione come configurato dall'utente quando si configura il probe di integrità di Load Balancer. Load Balancer usa lo stato del probe di integrità per determinare dove inviare nuovi flussi. I probe di integrità hanno origine da un indirizzo dell'infrastruttura di Azure e sono visibili all'interno del sistema operativo guest della macchina virtuale.

Per ottenere lo stato del probe di integrità per le risorse di Load Balancer Standard:
1. Selezionare la metrica **stato del probe di integrità** con tipo di aggregazione **AVG** . 
2. Applicare un filtro alla porta o all'indirizzo IP front-end richiesto (o entrambi).

I probe di integrità hanno esito negativo per le ragioni seguenti:
- Se si configura un probe di integrità per una porta che non è in ascolto, che non risponde o che non utilizza il protocollo corretto. Se il servizio usa regole Direct Server Return (DSR o IP mobile), assicurarsi che il servizio sia in ascolto sull'indirizzo IP della configurazione IP della scheda di rete e non soltanto sul loopback configurato con l'indirizzo IP front-end.
- Se il probe non è consentito dal gruppo di sicurezza di rete, dal firewall del sistema operativo guest della macchina virtuale o dai filtri del livello dell'applicazione.

Usare **Media** come aggregazione per la maggior parte degli scenari.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Come è possibile controllare le statistiche di connessione in uscita? 
<details>
  <summary>Espandere</summary>
La metrica relativa alle connessioni SNAT descrive il volume di quelle con esito positivo e con esito negativo (connessioni per i [flussi in uscita](./load-balancer-outbound-connections.md)).

Un volume di connessioni non riuscite maggiore di zero indica l'esaurimento delle porte SNAT. È necessario effettuare un'analisi approfondita per determinare che cosa potrebbe causare questi errori. L'esaurimento delle porte SNAT si manifesta sotto forma di errore durante la definizione di un [flusso in uscita](./load-balancer-outbound-connections.md). Fare riferimento all'articolo sulle connessioni in uscita per comprendere gli scenari e i meccanismi coinvolti e scoprire come ridurre ed evitare l'esaurimento delle porte SNAT in fase di progettazione. 

Per ottenere statistiche sulle connessioni SNAT:
1. Selezionare il tipo di metrica **connessioni SNAT** e **Sum** come aggregazione. 
2. Raggruppa per **stato di connessione** per i conteggi delle connessioni SNAT riuscite e non riuscite che devono essere rappresentati da righe diverse. 

![Connessione SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura: Load Balancer numero di connessioni SNAT*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Ricerca per categorie controllare l'utilizzo e l'allocazione della porta SNAT?
<details>
  <summary>Espandere</summary>
La metrica delle porte SNAT utilizzate rileva il numero di porte SNAT utilizzate per gestire i flussi in uscita. Ciò indica quanti flussi univoci vengono stabiliti tra un'origine Internet e una VM back-end o un set di scalabilità di macchine virtuali che si trova dietro un servizio di bilanciamento del carico e non dispone di un indirizzo IP pubblico. Confrontando il numero di porte SNAT che si sta usando con la metrica delle porte SNAT allocate, è possibile determinare se il servizio sta riscontrando o a rischio di esaurimento del SNAT e risultante errore del flusso in uscita. 

Se le metriche indicano il rischio di errore del [flusso in uscita](./load-balancer-outbound-connections.md) , fare riferimento all'articolo e adottare le misure per attenuare questo problema per garantire l'integrità del servizio.

Per visualizzare l'utilizzo e l'allocazione della porta SNAT:
1. Impostare l'aggregazione temporale del grafico su 1 minuto per garantire la visualizzazione dei dati desiderati.
1. Selezionare le **porte SNAT utilizzate** e/o le **porte SNAT allocate** come tipo di metrica e **media** come aggregazione
    * Per impostazione predefinita, queste metriche rappresentano il numero medio di porte SNAT allocate o usate da ogni VM back-end o VMSS, corrispondenti a tutti gli indirizzi IP pubblici front-end mappati alla Load Balancer, aggregati su TCP e UDP.
    * Per visualizzare le porte SNAT totali utilizzate da o allocate per il servizio di bilanciamento del carico utilizzare la **somma** di aggregazione metrica
1. Filtrare per un **tipo di protocollo** specifico, un set di **indirizzi IP back-end** e/o **indirizzi IP** front-end.
1. Per monitorare l'integrità per ogni istanza back-end o front-end, applicare la suddivisione. 
    * Nota la suddivisione consente solo la visualizzazione di una singola metrica alla volta. 
1. Ad esempio, per monitorare l'utilizzo di SNAT per i flussi TCP per computer, aggregati in base alla **media**, divisi per **indirizzi IP back-end** e filtra per **tipo di protocollo**. 

![Allocazione e utilizzo di SNAT](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Figura: l'allocazione e l'utilizzo medio di porte SNAT TCP per un set di macchine virtuali back-end*

![Utilizzo di SNAT per istanza back-end](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Figura: utilizzo della porta TCP SNAT per ogni istanza back-end*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Come è possibile verificare i tentativi di connessione in ingresso/uscita per il servizio?
<details>
  <summary>Espandere</summary>
La metrica relativa a un pacchetto SYN descrive il volume di pacchetti SYN TCP arrivati o inviati (per i [flussi in uscita](./load-balancer-outbound-connections.md)) che sono associati a uno specifico front-end. Questa metrica può essere usata per comprendere i tentativi di connessione TCP al servizio.

Utilizzare **Sum** come aggregazione per la maggior parte degli scenari.

![Connessione SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura: conteggio Load Balancer SYN*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Come è possibile verificare il consumo di larghezza di banda di rete? 
<details>
  <summary>Espandere</summary>
La metrica relativa ai contatori di byte e pacchetti descrive il volume di byte e pacchetti che vengono inviati o ricevuti dal servizio per ogni front-end.

Utilizzare **Sum** come aggregazione per la maggior parte degli scenari.

Per ottenere statistiche relative al conteggio di byte o pacchetti:
1. Selezionare il tipo di metrica **conteggio byte** e/o **conteggio pacchetti** , con **Sum** come aggregazione. 
2. Eseguire una delle operazioni seguenti:
   * Applicare un filtro su una specifica porta IP front-end, porta front-end, porta IP back-end o porta back-end.
   * È possibile ottenere statistiche generali per la risorsa di Load Balancer senza applicare filtri.

![Conteggio byte](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura: conteggio di byte Load Balancer*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Come è possibile diagnosticare la distribuzione di Load Balancer?
<details>
  <summary>Espandere</summary>
Utilizzando una combinazione delle metriche di disponibilità del percorso dei dati e dello stato del probe di integrità in un singolo grafico è possibile identificare la posizione in cui cercare il problema e risolvere il problema. È possibile assicurarsi che Azure funzioni correttamente e usare questa conoscenza per determinare definitivamente che la configurazione o l'applicazione costituisca la causa radice.

È possibile usare le metriche relative ai probe di integrità per comprendere in che modo Azure visualizza lo stato della distribuzione in base alla configurazione specificata. L'analisi dei probe di integrità costituisce sempre una valida opzione per iniziare a monitorare o determinare una causa.

È possibile eseguire altre operazioni e usare la metrica di disponibilità del percorso dati per ottenere informazioni sul modo in cui Azure Visualizza l'integrità del piano dati sottostante responsabile della distribuzione specifica. Quando si combinano entrambe le metriche, è possibile isolare il punto in cui potrebbe verificarsi l'errore come illustrato in questo esempio:

![Combinazione della metrica di disponibilità del percorso dati e dello stato del probe di integrità](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Figura: combinare le metriche di disponibilità del percorso dei dati e dello stato del probe di integrità*

In questo grafico vengono visualizzate le informazioni seguenti:
- L'infrastruttura che ospita le macchine virtuali non è disponibile e allo 0% all'inizio del grafico. In seguito, l'infrastruttura era integra e le macchine virtuali erano raggiungibili e più di una VM veniva posizionata nel back-end. Queste informazioni sono indicate dalla traccia blu per la disponibilità del percorso dati, che in seguito si trovava al 100%. 
- Lo stato del probe di integrità, indicato dalla traccia viola, è pari allo 0% all'inizio del grafico. L'area rotonda in verde evidenzia la posizione in cui lo stato del probe di integrità diventa integro e a questo punto la distribuzione del cliente è stata in grado di accettare nuovi flussi.

Il grafico consente al cliente di risolvere i problemi della distribuzione in modo autonomo senza dover fare supposizioni o contattare il supporto per sapere se si stanno verificando altri problemi. Il servizio non era disponibile perché i probe di integrità hanno avuto esito negativo a causa di una configurazione errata o di un'applicazione non riuscita.
</details>

## <a name="configure-alerts-for-multi-dimensional-metrics"></a>Configurare gli avvisi per le metriche multidimensionali ###

Azure Load Balancer Standard supporta gli avvisi facilmente configurabili per le metriche multidimensionali. Configurare soglie personalizzate per metriche specifiche per attivare avvisi con livelli di gravità diversi per offrire un'esperienza di monitoraggio delle risorse senza problemi.

Per configurare gli avvisi:
1. Passare al Sottopannello avviso per il servizio di bilanciamento del carico
1. Creare una nuova regola di avviso
    1.  Configurare la condizione di avviso
    1.  Opzionale Aggiungi gruppo di azione per il ripristino automatico
    1.  Assegnare la gravità, il nome e la descrizione dell'avviso che consente la reazione intuitiva

### <a name="inbound-availability-alerting"></a>Avvisi di disponibilità in ingresso
Per generare un avviso per la disponibilità in ingresso, è possibile creare due avvisi distinti usando le metriche di disponibilità del percorso dati e dello stato del probe di integrità. I clienti possono avere scenari diversi che richiedono una logica di avviso specifica, ma gli esempi seguenti saranno utili per la maggior parte delle configurazioni.

Con la disponibilità del percorso dati è possibile generare avvisi ogni volta che una specifica regola di bilanciamento del carico diventa non disponibile. È possibile configurare questo avviso impostando una condizione di avviso per la disponibilità del percorso dati e la suddivisione in base a tutti i valori correnti e ai valori futuri sia per la porta front-end che per l'indirizzo IP front-end. Se si imposta la logica di avviso su un valore minore o uguale a 0, l'avviso verrà generato ogni volta che una regola di bilanciamento del carico smette di rispondere. Consente di impostare la granularità e la frequenza di valutazione dell'aggregazione in base alla valutazione desiderata. 

Con lo stato del probe di integrità è possibile inviare un avviso quando una determinata istanza di back-end non riesce a rispondere al Probe di integrità per un periodo di tempo significativo. Configurare la condizione di avviso per usare la metrica dello stato del probe di integrità e dividere per indirizzo IP back-end e porta back-end. In questo modo è possibile inviare un avviso separatamente per la capacità di ogni singola istanza di back-end di gestire il traffico su una porta specifica. Usare il tipo di aggregazione **media** e impostare il valore di soglia in base alla frequenza con cui viene eseguita l'analisi dell'istanza di back-end e a quanto si considera la soglia di integrità. 

È anche possibile inviare un avviso a livello di pool back-end non suddividendo le dimensioni in base alle dimensioni e usando il tipo di aggregazione **medio** . Questo consentirà di configurare regole di avviso, ad esempio un avviso quando il 50% dei membri del pool back-end non è integro.

### <a name="outbound-availability-alerting"></a>Avvisi sulla disponibilità in uscita
Per configurare la disponibilità in uscita, è possibile configurare due avvisi distinti usando il numero di connessioni SNAT e le metriche delle porte SNAT usate.

Per rilevare gli errori di connessione in uscita, configurare un avviso utilizzando il numero di connessioni SNAT e filtrando lo stato di connessione = operazione non riuscita. Utilizzare l'aggregazione **totale** . È quindi possibile suddividere questo valore in base all'indirizzo IP del back-end impostato su tutti i valori correnti e futuri per l'avviso separatamente per ogni istanza di back-end che si verificano connessioni Impostare la soglia su un valore maggiore di zero o un numero maggiore se si prevede di visualizzare alcuni errori di connessione in uscita.

Tramite le porte SNAT utilizzate è possibile segnalare un rischio maggiore di esaurimento di SNAT e di errori di connessione in uscita. Assicurarsi di eseguire la suddivisione in base all'indirizzo IP e al protocollo back-end quando si usa questo avviso e usare l'aggregazione **media** . Impostare la soglia su un valore maggiore di una o più percentuali del numero di porte allocate per ogni istanza ritenute non sicure. Ad esempio, è possibile configurare un avviso con livello di gravità basso quando un'istanza back-end usa il 75% delle porte allocate e un livello di gravità elevato quando USA 90% o 100% delle porte allocate.  
## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Stato di integrità delle risorse

Lo stato di integrità per le risorse di Load Balancer Standard viene esposto tramite **Integrità risorse** in **Monitoraggio > Integrità dei servizi**. Viene valutato ogni **due minuti** misurando la disponibilità del percorso dati che determina se gli endpoint di bilanciamento del carico front-end sono disponibili.

| Stato di integrità delle risorse | Descrizione |
| --- | --- |
| Disponibile | Il servizio di bilanciamento del carico standard è integro e disponibile. |
| Degraded | Il servizio di bilanciamento del carico standard dispone di eventi avviati dalla piattaforma o dagli utenti che influiscano sulle prestazioni. La metrica di disponibilità del percorso dati ha restituito un valore di integrità inferiore al 90% ma maggiore del 25% per almeno due minuti. Si verificheranno un notevole effetto sulle prestazioni. [Seguire la guida alla risoluzione dei problemi di RHC](./troubleshoot-rhc.md) per determinare se sono presenti eventi avviati dall'utente che potrebbero compromettere la disponibilità.
| Non disponibile | La risorsa di Load Balancer standard non è integra. La metrica di disponibilità del percorso di DataPath ha segnalato meno il 25% di integrità per almeno due minuti. Si verificherà un impatto significativo sulle prestazioni o la mancanza di disponibilità per la connettività in ingresso. Potrebbero verificarsi eventi di utenti o piattaforme che provocano l'indisponibilità. [Seguire la guida alla risoluzione dei problemi di RHC](./troubleshoot-rhc.md) per determinare se sono presenti eventi avviati dall'utente che incidono sulla disponibilità. |
| Sconosciuto | Lo stato di integrità delle risorse per la risorsa Load Balancer standard non è stato ancora aggiornato o non ha ricevuto informazioni sulla disponibilità del percorso dati per gli ultimi 10 minuti. Questo stato deve essere temporaneo e rifletterà lo stato corretto non appena vengono ricevuti i dati. |

Per visualizzare l'integrità delle risorse della configurazione pubblica di Load Balancer Standard:
1. Selezionare **monitoraggio**  >  **integrità del servizio**.

   ![Pagina Monitoraggio](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura - Collegamento a Integrità dei servizi in Monitoraggio di Azure*

2. Selezionare **Integrità risorse** e assicurarsi che **ID di sottoscrizione** e **Tipo di risorsa = Load Balancer** siano selezionati.

   ![Stato di integrità delle risorse](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura: Selezionare la risorsa per la visualizzazione dell'integrità*

3. Selezionare la risorsa di Load Balancer dall'elenco per visualizzare il relativo stato di integrità cronologico.

    ![Stato di integrità di Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura: Visualizzazione dell'integrità delle risorse di Load Balancer*
 
La descrizione dello stato di integrità delle risorse generiche è disponibile nella [documentazione di RHC](../service-health/resource-health-overview.md). Per gli stati specifici per il Azure Load Balancer sono elencati nella tabella seguente: 


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'uso di [Insights](./load-balancer-insights.md) per visualizzare le metriche preconfigurate per la Load Balancer
- Altre informazioni su [Load Balancer Standard](./load-balancer-overview.md).
- Altre informazioni sulla [connettività in uscita di Load Balancer](./load-balancer-outbound-connections.md).
- Informazioni su [Monitoraggio di Azure](../azure-monitor/overview.md).
- Informazioni sull'[API REST di Monitoraggio di Azure](/rest/api/monitor/) e su [come recuperare le metriche tramite l'API REST](/rest/api/monitor/metrics/list).