---
title: Stimare la capacità per i carichi di lavoro di query e indici
titleSuffix: Azure Cognitive Search
description: Modificare le risorse del computer di replica e di partizione in ricerca cognitiva di Azure, in cui ogni risorsa viene addebitata in unità di ricerca fatturabili.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: d848c1ed1ab9d4cb24dec9423d93ec62ab45633b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537222"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Stimare e gestire la capacità di un servizio ricerca cognitiva di Azure

Prima di eseguire il [provisioning di un servizio di ricerca](search-create-service-portal.md) e il blocco in un piano tariffario specifico, è necessario attendere alcuni minuti per comprendere il funzionamento della capacità e come modificare le repliche e le partizioni per gestire la fluttuazione del carico di lavoro.

La capacità è una funzione del [livello di servizio](search-sku-tier.md), che stabilisce la quantità massima di spazio di archiviazione per ogni servizio, per partizione e i limiti massimi per il numero di oggetti che è possibile creare. Il livello Basic è progettato per le app con requisiti di archiviazione modesto (solo una partizione), ma con la possibilità di eseguire in una configurazione a disponibilità elevata (3 repliche). Altri livelli sono progettati per carichi di lavoro o modelli specifici, ad esempio la multitenant. Internamente, i servizi creati su tali livelli traggono vantaggio dall'hardware che consente tali scenari.

L'architettura di scalabilità in Azure ricerca cognitiva si basa su combinazioni flessibili di repliche e partizioni, in modo da poter variare la capacità a seconda che sia necessaria una maggiore potenza di query o di indicizzazione. Una volta creato un servizio, è possibile aumentare o ridurre il numero di repliche o partizioni in modo indipendente. I costi verranno applicati a ogni risorsa fisica aggiuntiva, ma una volta completati i carichi di lavoro di grandi dimensioni, è possibile ridurre la scalabilità per abbassare la fattura. A seconda del livello e della dimensione della regolazione, l'aggiunta o la riduzione della capacità può richiedere da 15 minuti a diverse ore.

Quando si modifica l'allocazione delle repliche e delle partizioni, è consigliabile usare la portale di Azure. Il portale impone limiti per le combinazioni consentite che sono inferiori ai limiti massimi di un livello. Tuttavia, se è necessario un approccio di provisioning basato su script o basato su codice, l' [Azure PowerShell](search-manage-powershell.md) o l' [API REST di gestione](/rest/api/searchmanagement/services) sono soluzioni alternative.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Concetti: unità di ricerca, repliche, partizioni, partizioni

La capacità è espressa *in unità di ricerca* che possono essere allocate in combinazioni di *partizioni* e *repliche*, usando un meccanismo di *partizionamento orizzontale* sottostante per supportare configurazioni flessibili:

| Concetto  | Definizione|
|----------|-----------|
|*Unità di ricerca* | Un singolo incremento della capacità totale disponibile (36 unità). È anche l'unità di fatturazione per un servizio ricerca cognitiva di Azure. Per eseguire il servizio è necessario almeno un'unità.|
|*Replica* | Istanze del servizio di ricerca, utilizzate principalmente per il bilanciamento di carico delle operazioni di query. Ogni replica ospita una copia di un indice. Se si allocano tre repliche, saranno disponibili tre copie di un indice per la manutenzione delle richieste di query.|
|*Partizione* | Archiviazione fisica e I/O per le operazioni di lettura/scrittura, ad esempio per la ricompilazione o l'aggiornamento di un indice. Ogni partizione dispone di una sezione dell'indice totale. Se si allocano tre partizioni, l'indice è suddiviso in terze parti. |
|*Partizione* | Un blocco di un indice. Azure ricerca cognitiva divide ogni indice in partizioni per rendere più veloce il processo di aggiunta delle partizioni (spostando le partizioni in nuove unità di ricerca).|

Il diagramma seguente illustra la relazione tra le repliche, le partizioni, le partizioni e le unità di ricerca. Viene illustrato un esempio di come un singolo indice viene esteso in quattro unità di ricerca in un servizio con due repliche e due partizioni. Ognuna delle quattro unità di ricerca archivia solo la metà delle partizioni dell'indice. Le unità di ricerca nella colonna a sinistra archiviano la prima metà delle partizioni, che comprendono la prima partizione, mentre quelle nella colonna destra archiviano la seconda metà delle partizioni, che comprende la seconda partizione. Poiché sono presenti due repliche, sono presenti due copie di ogni partizione dell'indice. Le unità di ricerca nella parte superiore archiviano una copia, che comprende la prima replica, mentre quelle nella riga inferiore archiviano un'altra copia, che comprende la seconda replica.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Gli indici di ricerca sono partizionati tra le partizioni.":::

Il diagramma precedente è solo un esempio. Sono possibili molte combinazioni di partizioni e repliche, fino a un massimo di 36 unità di ricerca totali.

In ricerca cognitiva, la gestione delle partizioni è un dettaglio di implementazione e non configurabile, ma la conoscenza del partizionamento di un indice consente di comprendere le anomalie occasionali nei comportamenti di classificazione e completamento automatico:

+ Anomalie di classificazione: i punteggi di ricerca vengono calcolati a livello di partizione prima e quindi aggregati in un unico set di risultati. A seconda delle caratteristiche del contenuto della partizione, le corrispondenze di una partizione possono essere classificate in un livello superiore rispetto alle corrispondenze in un altro. Se si notano livelli di classificazione intuitivi nei risultati della ricerca, è molto probabile che si verifichino gli effetti del partizionamento orizzontale, soprattutto se gli indici sono di dimensioni ridotte. È possibile evitare queste anomalie di rango scegliendo di [calcolare i punteggi a livello globale nell'intero indice](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), ma in questo modo si ridurrà una riduzione delle prestazioni.

+ Anomalie di completamento automatico: le query con completamento automatico, in cui vengono eseguite corrispondenze sui primi caratteri di un termine parzialmente immesso, accettano un parametro fuzzy che perdona le piccole deviazioni nell'ortografia. Per il completamento automatico, la corrispondenza fuzzy è vincolata a termini all'interno della partizione corrente. Se, ad esempio, una partizione contiene "Microsoft" e viene immesso un termine parziale di "micor", il motore di ricerca corrisponderà a "Microsoft" nella partizione, ma non in altre partizioni che contengono le parti rimanenti dell'indice.

## <a name="how-to-evaluate-capacity-requirements"></a>Come valutare i requisiti di capacità

La capacità e i costi per l'esecuzione del servizio sono disponibili. I livelli impongono limiti su due livelli: archiviazione e contenuto (un numero di indici in un servizio, ad esempio). È importante prendere in considerazione entrambi perché il limite raggiunto per primo è il limite effettivo.

Le quantità di indici e altri oggetti sono in genere determinate dai requisiti aziendali e di progettazione. È ad esempio possibile disporre di più versioni dello stesso indice per lo sviluppo, il test e la produzione attivi.

Le esigenze di archiviazione sono determinate dalla dimensione degli indici che si prevede di compilare. Non sono disponibili regole euristiche solide o generali utili per le stime. L'unico modo per determinare le dimensioni di un indice è [crearne uno](search-what-is-an-index.md). La relativa dimensione sarà basata sui dati importati, sull'analisi del testo e sulla configurazione degli indici, ad esempio se si abilitano i suggerimenti, i filtri e l'ordinamento.

Per la ricerca full-text, la struttura dei dati primaria è una struttura di [Indice invertita](https://en.wikipedia.org/wiki/Inverted_index) , che presenta caratteristiche diverse rispetto ai dati di origine. Per un indice invertito, le dimensioni e la complessità sono determinate dal contenuto, non necessariamente dalla quantità di dati da inserire. Un'origine dati di grandi dimensioni con ridondanza elevata può comportare un indice più piccolo rispetto a un set di dati più piccolo che contiene contenuto altamente variabile. Pertanto, è raramente possibile dedurre le dimensioni dell'indice in base alle dimensioni del set di dati originale.

> [!NOTE] 
> Anche se la stima delle esigenze future per gli indici e l'archiviazione può sembrare una supposizione, vale la pena. Se la capacità di un livello risulta troppo bassa, è necessario effettuare il provisioning di un nuovo servizio a un livello superiore e quindi [ricaricare gli indici](search-howto-reindex.md). Non è disponibile alcun aggiornamento sul posto di un servizio da un livello a un altro.
>

### <a name="estimate-with-the-free-tier"></a>Stima con il livello gratuito

Un approccio per la stima della capacità consiste nell'iniziare con il livello gratuito. Tenere presente che il servizio gratuito offre fino a tre indici, 50 MB di spazio di archiviazione e 2 minuti di tempo di indicizzazione. Può essere difficile stimare una dimensione di indice proiettata con questi vincoli, ma questi sono i passaggi seguenti:

+ [Creare un servizio gratuito](search-create-service-portal.md).
+ Preparare un set di dati di piccole dimensioni rappresentativo.
+ Creare un indice e caricare i dati. Se il set di dati può essere ospitato in un'origine dati di Azure supportata dagli indicizzatori, è possibile usare la [procedura guidata Importa dati nel portale](search-get-started-portal.md) per creare e caricare l'indice. In caso contrario, è consigliabile usare REST e [postazione](search-get-started-rest.md) o [Visual Studio Code](search-get-started-vs-code.md) per creare l'indice ed effettuare il push dei dati. Il modello push richiede che i dati siano sotto forma di documenti JSON, dove i campi del documento corrispondono ai campi dell'indice.
+ Raccogliere informazioni sull'indice, ad esempio le dimensioni. Le funzionalità e gli attributi hanno un effetto sull'archiviazione. Se, ad esempio, si aggiungono i suggerimenti (query di ricerca di tipo), i requisiti di archiviazione aumentano. 

  Utilizzando lo stesso set di dati, è possibile provare a creare più versioni di un indice, con attributi diversi in ogni campo, per verificare la variazione dei requisiti di archiviazione. Per ulteriori informazioni, vedere ["implicazioni dell'archiviazione" in creare un indice di base](search-what-is-an-index.md#index-size).

Con una stima approssimativa a disposizione, è possibile raddoppiare la quantità di budget per due indici (sviluppo e produzione), quindi scegliere il livello di conseguenza.

### <a name="estimate-with-a-billable-tier"></a>Stima con un livello fatturabile

Le risorse dedicate possono adattarsi a tempi di elaborazione e campionamento maggiori per stime più realistiche di quantità di indice, dimensioni e volumi di query durante lo sviluppo. Alcuni clienti entrano subito con un livello fatturabile e quindi rivalutano il progetto di sviluppo.

1. [Esaminare i limiti del servizio a ogni livello](./search-limits-quotas-capacity.md#index-limits) per determinare se i livelli inferiori possono supportare il numero di indici necessari. Nei livelli Basic, S1 e S2, i limiti di indice sono rispettivamente 15, 50 e 200. Il livello ottimizzato per l'archiviazione è costituito da un limite di 10 indici perché è progettato per supportare un numero ridotto di indici di dimensioni molto grandi.

1. [Creare un servizio a un livello fatturabile](search-create-service-portal.md):

    + Avviare low, in Basic o S1, se non si è certi del carico previsto.
    + Avviare High, a S2 o persino S3, se il test include l'indicizzazione su larga scala e i carichi di query.
    + Iniziare con l'archiviazione ottimizzata, in L1 o L2, se si esegue l'indicizzazione di una grande quantità di dati e il carico di query è relativamente basso, come per un'applicazione aziendale interna.

1. [Generare un indice iniziale](search-what-is-an-index.md) per determinare il modo in cui i dati di origine vengono convertiti in un indice. Questo è l'unico modo per stimare le dimensioni di un indice.

1. [Monitorare l'archiviazione, i limiti del servizio, il volume di query e la latenza](search-monitor-usage.md) nel portale. Il portale Mostra le query al secondo, le query limitate e la latenza di ricerca. Tutti questi valori possono essere utili per decidere se è stato selezionato il livello corretto.

1. Aggiungere repliche se è necessaria la disponibilità elevata o se si verifica un rallentamento delle prestazioni delle query.

   Non sono disponibili linee guida sul numero di repliche necessarie per gestire i carichi di query. Le prestazioni delle query variano a seconda della complessità della query e dei carichi di lavoro in competizione. Sebbene l'aggiunta di repliche consenta certamente di migliorare le prestazioni, il risultato non è strettamente lineare. L'aggiunta di tre repliche, infatti, non garantisce una velocità effettiva triplicata. Per informazioni aggiuntive sulla stima di query al secondo per la soluzione, vedere [scalabilità per le query di prestazioni](search-performance-optimization.md)e [monitoraggio](search-monitor-queries.md).

> [!NOTE]
> I requisiti di archiviazione possono essere inflat se si includono dati che non verranno mai cercati. Idealmente, i documenti contengono solo i dati necessari per l'esperienza di ricerca. I dati binari non sono ricercabili e devono essere archiviati separatamente (forse in una tabella o in un archivio BLOB di Azure). È quindi necessario aggiungere un campo nell'indice per contenere un riferimento URL ai dati esterni. Le dimensioni massime di un singolo documento di ricerca sono pari a 16 MB (o meno se si esegue il caricamento bulk di più documenti in un'unica richiesta). Per altre informazioni, vedere [limiti dei servizi in Azure ricerca cognitiva](search-limits-quotas-capacity.md).
>

**Considerazioni sul volume delle query**

Il numero di query al secondo (query al secondo) è una metrica importante durante l'ottimizzazione delle prestazioni, ma in genere è solo un livello di considerazione se si prevede un volume di query elevato all'inizio.

I livelli standard possono fornire un equilibrio tra repliche e partizioni. È possibile aumentare il turnaround della query aggiungendo repliche per il bilanciamento del carico o aggiungere partizioni per l'elaborazione parallela. È quindi possibile ottimizzare le prestazioni dopo il provisioning del servizio.

Se si prevedono volumi di query sostenuti elevati dall'inizio, è consigliabile prendere in considerazione livelli standard più elevati, supportati da hardware più potente. È quindi possibile portare le partizioni e le repliche offline oppure passare a un servizio di livello inferiore, se non si verificano questi volumi di query. Per altre informazioni su come calcolare la velocità effettiva delle query, vedere [ottimizzazione delle prestazioni e delle ricerca cognitiva di Azure](search-performance-optimization.md).

I livelli ottimizzati per l'archiviazione sono utili per i carichi di lavoro di dati di grandi dimensioni, supportando una maggiore quantità di spazio di archiviazione per gli indici disponibili quando i requisiti di latenza È comunque necessario usare repliche aggiuntive per il bilanciamento del carico e partizioni aggiuntive per l'elaborazione parallela. È quindi possibile ottimizzare le prestazioni dopo il provisioning del servizio.

**Contratti di servizio**

Il livello gratuito e le funzionalità di anteprima non forniscono [contratti di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Per tutti i livelli fatturabili, i contratti di servizio diventano effettivi quando viene effettuato il provisioning di una ridondanza sufficiente per il servizio. È necessario disporre di due o più repliche per i contratti di esecuzione di query (lettura). È necessario avere tre o più repliche per i contratti di esecuzione di query e indicizzazione (lettura/scrittura). Il numero di partizioni non influisce sui contratti di contratto.

## <a name="tips-for-capacity-planning"></a>Suggerimenti per la pianificazione della capacità

+ Consenti le metriche per la compilazione delle query e raccoglie i dati relativi ai modelli di utilizzo (query durante l'orario di ufficio, indicizzazione durante gli orari di minore attività). Usare questi dati per informare le decisioni relative al provisioning del servizio. Sebbene non sia pratico a cadenza oraria o giornaliera, è possibile modificare dinamicamente le partizioni e le risorse in modo da adattare le modifiche pianificate nei volumi di query. È anche possibile gestire le modifiche non pianificate ma prolungate se i livelli contengono abbastanza tempo per garantire l'esecuzione di un'azione.

+ Tenere presente che l'unico svantaggio del provisioning è che potrebbe essere necessario eliminare un servizio se i requisiti effettivi sono maggiori delle stime. Per evitare l'alterazione del servizio, è necessario creare un nuovo servizio a un livello superiore e eseguirlo affiancato fino a quando tutte le app e le richieste non sono destinate al nuovo endpoint.

## <a name="when-to-add-partitions-and-replicas"></a>Quando aggiungere partizioni e repliche

Inizialmente, a un servizio viene allocato un livello minimo di risorse costituito da una partizione e una replica.

Un singolo servizio deve disporre di risorse sufficienti per gestire tutti i carichi di lavoro (indicizzazione e query). Nessuno dei due carichi di lavoro viene eseguito in background. È possibile pianificare l'indicizzazione per i periodi in cui le richieste di query sono naturalmente meno frequenti, ma il servizio non assegna priorità a un'attività rispetto a un'altra. Inoltre, una certa quantità di ridondanza smussa le prestazioni delle query quando i servizi o i nodi vengono aggiornati internamente.

Come regola generale, le applicazioni di ricerca tendono a avere più repliche rispetto alle partizioni, in particolare quando le operazioni del servizio sono distorte per i carichi di lavoro di query. La sezione relativa alla [disponibilità elevata](#HA) spiega perché.

Il [livello scelto](search-sku-tier.md) determina la velocità e le dimensioni della partizione e ogni livello viene ottimizzato in base a un set di caratteristiche che si adattano a diversi scenari. Se si sceglie un livello superiore, potrebbe essere necessario un minor numero di partizioni rispetto a S1. Una delle domande a cui è necessario rispondere attraverso test autoindirizzati consiste nel fatto che una partizione più grande e costosa produce prestazioni migliori rispetto a due partizioni meno economiche in un servizio di cui è stato effettuato il provisioning a un livello inferiore.

Le applicazioni di ricerca che richiedono l'aggiornamento dei dati quasi in tempo reale avranno bisogno in proporzione di più partizioni che repliche. L'aggiunta di partizioni distribuisce le operazioni di lettura/scrittura su un numero più ampio di risorse di calcolo. Offre inoltre più spazio su disco per l'archiviazione di documenti e indici aggiuntivi.

L'esecuzione di query su indici di dimensioni maggiori può richiedere tempi più lunghi. Di conseguenza, si noterà che ogni aumento incrementale delle partizioni richiede un aumento delle repliche proporzionale ma più limitato. La complessità e il volume delle query influiscono sulla rapidità di esecuzione delle stesse.

> [!NOTE]
> L'aggiunta di più repliche o partizioni aumenta il costo di esecuzione del servizio e può introdurre variazioni minime nel modo in cui vengono ordinati i risultati. Assicurarsi di controllare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per comprendere le implicazioni di fatturazione dell'aggiunta di altri nodi. Il [grafico seguente](#chart) può essere utile per fare riferimento incrociato al numero di unità di ricerca necessarie per una configurazione specifica. Per ulteriori informazioni sul modo in cui le repliche aggiuntive influiscano sull'elaborazione delle query, vedere [ordinamento dei risultati](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Come allocare repliche e partizioni

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il servizio di ricerca.

1. In **Impostazioni** aprire la pagina **scala** per modificare le repliche e le partizioni. 

   Lo screenshot seguente mostra uno standard di base con provisioning con una replica e una partizione. La formula nella parte inferiore indica il numero di unità di ricerca utilizzate (1). Se il prezzo unitario è $100 (non un prezzo reale), il costo mensile per l'esecuzione di questo servizio sarà in media pari a $100.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Pagina scala che mostra i valori correnti" border="true":::

1. Utilizzare il dispositivo di scorrimento per aumentare o diminuire il numero di partizioni. La formula nella parte inferiore indica il numero di unità di ricerca utilizzate. Selezionare **Salva**.

   Questo esempio aggiunge una seconda replica e una partizione. Si noti il numero di unità di ricerca; Ora è quattro perché la formula di fatturazione è rappresentata dalle repliche moltiplicate per le partizioni (2 x 2). Raddoppiare la capacità più di raddoppiare il costo di esecuzione del servizio. Se il costo unitario di ricerca è $100, la nuova fattura mensile sarà $400.

   Per i costi correnti per unità di ogni livello, visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Aggiungere repliche e partizioni" border="true":::

1. Dopo il salvataggio, è possibile controllare le notifiche per confermare che l'azione è riuscita.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Salva modifiche" border="true":::

   Il completamento delle modifiche alla capacità può richiedere fino a diverse ore. Non è possibile annullare una volta che il processo è stato avviato e non è disponibile alcun monitoraggio in tempo reale per le modifiche della replica e della partizione. Tuttavia, il messaggio seguente rimane visibile mentre sono in corso le modifiche.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Messaggio di stato nel portale" border="true":::

> [!NOTE]
> Una volta eseguito il provisioning di un servizio, non è possibile aggiornarlo a un livello superiore. È necessario creare un servizio di ricerca a un nuovo livello e ricaricare gli indici. Per informazioni sul provisioning del servizio, vedere [creare un servizio ricerca cognitiva di Azure nel portale](search-create-service-portal.md) .
>
> Inoltre, le partizioni e le repliche vengono gestite esclusivamente e internamente dal servizio. Non esiste un concetto di affinità processori o l'assegnazione di un carico di lavoro a un nodo specifico.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>combinazioni di partizioni e repliche

Il servizio Basic prevede esattamente una partizione e fino a tre repliche, per un massimo di tre unità di ricerca. Le repliche sono l'unica risorsa regolabile. Per la disponibilità elevata relativa alle query è necessario un minimo di due repliche.

Tutti i servizi di ricerca standard e ottimizzati per l'archiviazione possono presupporre le seguenti combinazioni di repliche e partizioni, in base al limite di 36 SU consentito per questi livelli. 

|   | **1 partizione** | **2 partizioni** | **3 partizioni** | **4 partizioni** | **6 partizioni** | **12 partizioni** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replica.** |1 unità di ricerca |2 unità di ricerca |3 unità di ricerca |4 unità di ricerca |6 unità di ricerca |12 unità di ricerca |
| **2 repliche** |2 unità di ricerca |4 unità di ricerca |6 unità di ricerca |8 unità di ricerca |12 unità di ricerca |24 unità di ricerca |
| **3 repliche** |3 unità di ricerca |6 unità di ricerca |9 unità di ricerca |12 unità di ricerca |18 unità di ricerca |36 unità di ricerca |
| **4 repliche** |4 unità di ricerca |8 unità di ricerca |12 unità di ricerca |16 unità di ricerca |24 unità di ricerca |N/D |
| **5 repliche** |5 unità di ricerca |10 unità di ricerca |15 unità di ricerca |20 unità di ricerca |30 unità di ricerca |N/D |
| **6 repliche** |6 unità di ricerca |12 unità di ricerca |18 unità di ricerca |24 unità di ricerca |36 unità di ricerca |N/D |
| **12 repliche** |12 unità di ricerca |24 unità di ricerca |36 unità di ricerca |N/D |N/D |N/D |

Le unità di ricerca, i prezzi e le capacità sono illustrati in dettaglio nel sito web di Azure. Per ulteriori informazioni, vedere la pagina relativa ai [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Il numero di repliche e partizioni divide equamente per 12 (in particolare 1, 2, 3, 4, 6, 12). Questo perché Azure ricerca cognitiva suddivide ogni indice in 12 partizioni in modo che sia possibile distribuirlo in parti uguali tra tutte le partizioni. Ad esempio, se il servizio ha tre partizioni e si crea un indice, ogni partizione conterrà quattro partizioni dell'indice. La modalità di partizionamento di un indice in Azure ricerca cognitiva è un dettaglio di implementazione, soggetta a modifiche nelle versioni future. Sebbene il numero attualmente sia 12, tale numero potrebbe non essere 12 in futuro.
>

<a id="HA"></a>

## <a name="high-availability"></a>Disponibilità elevata

Poiché è facile e relativamente veloce eseguire la scalabilità verticale, è consigliabile in genere iniziare con una partizione e una o due repliche e quindi eseguire la scalabilità verticale come vengono compilati i volumi di query. I carichi di lavoro di query vengono eseguiti principalmente nelle repliche. Se è necessaria maggiore velocità effettiva o una disponibilità elevata, probabilmente saranno necessarie repliche aggiuntive.

Le indicazioni generali per la disponibilità elevata sono:

+ Due repliche per la disponibilità elevata di carichi di lavoro di sola lettura, vale a dire query.

+ Tre o più repliche per la disponibilità elevata dei carichi di lavoro di lettura e scrittura, vale a dire query e indicizzazione man mano che singoli documenti vengono aggiunti, aggiornati o eliminati.

I contratti di servizio per Azure ricerca cognitiva sono destinati a operazioni di query e a aggiornamenti degli indici che includono l'aggiunta, l'aggiornamento o l'eliminazione di documenti.

Il livello Basic prevede una partizione e fino a tre repliche. Se si vuole avere la flessibilità necessaria per rispondere immediatamente alle fluttuazioni della richiesta di indicizzazione e velocità effettiva di query, prendere in considerazione uno dei piani Standard.  Se i requisiti di archiviazione aumentano molto più rapidamente rispetto alla velocità effettiva delle query, prendere in considerazione uno dei livelli ottimizzati per l'archiviazione.

## <a name="about-queries-per-second-qps"></a>Informazioni sulle query al secondo (query al secondo)

A causa dell'elevato numero di fattori che influiscono sulle prestazioni delle query, Microsoft non pubblica i numeri query al secondo previsti. Le stime query al secondo devono essere sviluppate in modo indipendente da ogni cliente usando i costrutti del livello di servizio, della configurazione, dell'indice e della query validi per l'applicazione. Le dimensioni e la complessità dell'indice, le dimensioni e la complessità della query e la quantità di traffico sono i fattori principali in base ai quali è possibile determinare il numero di query al secondo. Non è possibile fornire stime significative se questi fattori sono sconosciuti.

Le stime sono più prevedibili se vengono calcolate su servizi in esecuzione su risorse dedicate (livelli Basic e Standard). In questo caso, infatti, è possibile stimare in modo più preciso il numero di query al secondo, poiché si ha il controllo di un numero maggiore di parametri. Per informazioni su come eseguire la stima, vedere [Considerazioni sulle prestazioni e sull'ottimizzazione di Ricerca cognitiva di Azure](search-performance-optimization.md).

Per i livelli Ottimizzato per l'archiviazione (L1 e L2), sono previste una velocità effettiva delle query inferiore e una latenza superiore rispetto ai livelli Standard.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come stimare e gestire i costi](search-sku-manage-costs.md)