---
title: Stimare la capacità e i costi
titleSuffix: Azure Cognitive Search
description: Esaminare le linee guida per la stima della capacità e la gestione dei costi del servizio di ricerca, tra cui l'infrastruttura e gli strumenti in Azure, nonché le procedure consigliate per l'uso più efficiente delle risorse
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.openlocfilehash: d48ae71a979a2d0f1457b0cefa8a98a02710dd96
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577688"
---
# <a name="how-to-estimate-capacity-and-costs-of-an-azure-cognitive-search-service"></a>Come stimare la capacità e i costi di un servizio ricerca cognitiva di Azure

Come parte della pianificazione della capacità per Azure ricerca cognitiva, le linee guida seguenti consentono di ridurre i costi o gestire i costi in modo più efficace:

+ Creare tutte le risorse nella stessa area o in un minor numero di aree possibile per ridurre al minimo o eliminare gli addebiti per la larghezza di banda.

+ Consolidare tutti i servizi in un gruppo di risorse, ad esempio ricerca cognitiva di Azure, servizi cognitivi e qualsiasi altro servizio di Azure usato nella soluzione. Nella portale di Azure trovare il gruppo di risorse e usare i comandi di **Gestione costi** per informazioni dettagliate sulla spesa effettiva e proiettata.

+ Prendere in considerazione l'app Web di Azure per l'applicazione front-end in modo che le richieste e le risposte rientrino entro il limite di data center.

+ Scalabilità verticale per operazioni con utilizzo intensivo di risorse, ad esempio l'indicizzazione, quindi riadatta verso il basso per carichi di lavoro di query normali. Iniziare con la configurazione minima per ricerca cognitiva di Azure (uno SU composto da una partizione e una replica), quindi monitorare le attività degli utenti per identificare i modelli di utilizzo che indicano la necessità di una maggiore capacità. Se è disponibile un modello stimabile, è possibile sincronizzare la scala con l'attività (per automatizzare questa operazione è necessario scrivere il codice).

Gli eventi fatturabili, la formula di fatturazione e la Tariffa fatturabile sono descritti in [scegliere un piano tariffario](search-sku-tier.md). Inoltre, è possibile visitare [Gestione costi e fatturazione](../cost-management-billing/cost-management-billing-overview.md) per gli strumenti e le funzionalità predefinite correlati alla spesa.

L'arresto di un servizio di ricerca su base temporanea non è possibile. Le risorse dedicate sono sempre operative, allocate per l'uso esclusivo per la durata del servizio. L'eliminazione di un servizio è permanente ed elimina anche i dati associati.

Per quanto riguarda il servizio stesso, l'unico modo per abbassare la fattura consiste nel ridurre le repliche e le partizioni a un livello che fornisce comunque una [conformità](https://azure.microsoft.com/support/legal/sla/search/v1_0/)accettabile per le prestazioni e il contratto di servizio oppure creare un servizio a un livello inferiore (le tariffe orarie S1 sono inferiori alle tariffe S2 o S3). Supponendo di effettuare il provisioning del servizio nella parte inferiore delle proiezioni di carico, se si aumenta il servizio, è possibile creare un secondo servizio a più livelli, ricompilare gli indici nel secondo servizio e quindi eliminare il primo.

## <a name="how-to-evaluate-capacity-requirements"></a>Come valutare i requisiti di capacità

In Azure ricerca cognitiva la capacità è strutturata come *repliche* e *partizioni*.

+ Le repliche sono istanze del servizio di ricerca. Ogni replica ospita una copia con carico bilanciato di un indice. Ad esempio, un servizio con sei repliche ha sei copie di ogni indice caricato nel servizio.

+ Le partizioni archiviano gli indici e suddividono automaticamente i dati ricercabili. Due partizioni suddividono l'indice a metà, tre partizioni la suddividono in terze e così via. In termini di capacità, le *dimensioni della partizione* sono la funzionalità di differenziazione principale tra i livelli.

> [!NOTE]
> Tutti i livelli standard e ottimizzati per l'archiviazione supportano [combinazioni flessibili di repliche e partizioni,](search-capacity-planning.md#chart) in modo da poter [ottimizzare il sistema per la velocità o l'archiviazione](search-performance-optimization.md) modificando il saldo. Il livello Basic offre fino a tre repliche per la disponibilità elevata, ma dispone di una sola partizione. I livelli gratuiti non forniscono risorse dedicate: le risorse di calcolo sono condivise da più Sottoscrittori.

### <a name="evaluating-capacity"></a>Valutazione della capacità

La capacità e i costi per l'esecuzione del servizio sono disponibili. I livelli impongono limiti su due livelli: archiviazione e contenuto (ad esempio, numero di indici). È necessario considerare entrambi i motivi perché il limite raggiunto per primo è il limite effettivo.

I requisiti aziendali in genere impongono il numero di indici necessari. Potrebbe essere necessario, ad esempio, un indice globale per un repository di documenti di grandi dimensioni. In alternativa, potrebbero essere necessari più indici basati su area, applicazione o nicchia aziendale.

Per determinare le dimensioni di un indice, è necessario [crearne uno](search-what-is-an-index.md). La relativa dimensione sarà basata sui dati importati e sulla configurazione degli indici, ad esempio se si abilitano i suggerimenti, i filtri e l'ordinamento.

Per la ricerca full-text, la struttura dei dati primaria è una struttura di [Indice invertita](https://en.wikipedia.org/wiki/Inverted_index) , che presenta caratteristiche diverse rispetto ai dati di origine. Per un indice invertito, le dimensioni e la complessità sono determinate dal contenuto, non necessariamente dalla quantità di dati da inserire. Un'origine dati di grandi dimensioni con ridondanza elevata può comportare un indice più piccolo rispetto a un set di dati più piccolo che contiene contenuto altamente variabile. Pertanto, è raramente possibile dedurre le dimensioni dell'indice in base alle dimensioni del set di dati originale.

> [!NOTE] 
> Anche se la stima delle esigenze future per gli indici e l'archiviazione può sembrare una supposizione, vale la pena. Se la capacità di un livello risulta troppo bassa, è necessario effettuare il provisioning di un nuovo servizio a un livello superiore e quindi [ricaricare gli indici](search-howto-reindex.md). Non è disponibile alcun aggiornamento sul posto di un servizio da un livello a un altro.
>

## <a name="estimate-with-the-free-tier"></a>Stima con il livello gratuito

Un approccio per la stima della capacità consiste nell'iniziare con il livello gratuito. Tenere presente che il servizio gratuito offre fino a tre indici, 50 MB di spazio di archiviazione e 2 minuti di tempo di indicizzazione. Può essere difficile stimare una dimensione di indice proiettata con questi vincoli, ma questi sono i passaggi seguenti:

+ [Creare un servizio gratuito](search-create-service-portal.md).
+ Preparare un set di dati di piccole dimensioni rappresentativo.
+ [Compilare un indice iniziale nel portale](search-get-started-portal.md) e annotarne le dimensioni. Le funzionalità e gli attributi hanno un effetto sull'archiviazione. Se, ad esempio, si aggiungono i suggerimenti (query di ricerca di tipo), i requisiti di archiviazione aumentano. Utilizzando lo stesso set di dati, è possibile provare a creare più versioni di un indice, con attributi diversi in ogni campo, per verificare la variazione dei requisiti di archiviazione. Per ulteriori informazioni, vedere ["implicazioni dell'archiviazione" in creare un indice di base](search-what-is-an-index.md#index-size).

Con una stima approssimativa a disposizione, è possibile raddoppiare la quantità di budget per due indici (sviluppo e produzione), quindi scegliere il livello di conseguenza.

## <a name="estimate-with-a-billable-tier"></a>Stima con un livello fatturabile

Le risorse dedicate possono adattarsi a tempi di elaborazione e campionamento maggiori per stime più realistiche di quantità di indice, dimensioni e volumi di query durante lo sviluppo. Alcuni clienti entrano subito con un livello fatturabile e quindi rivalutano il progetto di sviluppo.

1. [Esaminare i limiti del servizio a ogni livello](./search-limits-quotas-capacity.md#index-limits) per determinare se i livelli inferiori possono supportare il numero di indici necessari. Nei livelli Basic, S1 e S2, i limiti di indice sono rispettivamente 15, 50 e 200. Il livello ottimizzato per l'archiviazione è costituito da un limite di 10 indici perché è progettato per supportare un numero ridotto di indici di dimensioni molto grandi.

1. [Creare un servizio a un livello fatturabile](search-create-service-portal.md):

    + Avviare low, in Basic o S1, se non si è certi del carico previsto.
    + Partire da alto, a S2 o addirittura S3, se si è certi che si prevede di eseguire indicizzazione su larga scala e carichi di query.
    + Iniziare con l'archiviazione ottimizzata, in L1 o L2, se si esegue l'indicizzazione di una grande quantità di dati e il carico di query è relativamente basso, come per un'applicazione aziendale interna.

1. [Generare un indice iniziale](search-what-is-an-index.md) per determinare il modo in cui i dati di origine vengono convertiti in un indice. Questo è l'unico modo per stimare le dimensioni di un indice.

1. [Monitorare l'archiviazione, i limiti del servizio, il volume di query e la latenza](search-monitor-usage.md) nel portale. Il portale Mostra le query al secondo, le query limitate e la latenza di ricerca. Tutti questi valori possono essere utili per decidere se è stato selezionato il livello corretto. 

Il numero di indice e le dimensioni sono ugualmente importanti per l'analisi. Ciò è dovuto al fatto che i limiti massimi vengono raggiunti tramite l'utilizzo completo dell'archiviazione (partizioni) o dei limiti massimi per le risorse (indici, indicizzatori e così via), a seconda del valore che viene raggiunto per primo. Il portale consente di tenere traccia di entrambi gli aspetti visualizzando l'utilizzo corrente accanto ai limiti massimi nella pagina Panoramica.

> [!NOTE]
> I requisiti di archiviazione possono essere inflat se i documenti contengono dati estranei. Idealmente, i documenti contengono solo i dati necessari per l'esperienza di ricerca. I dati binari non sono ricercabili e devono essere archiviati separatamente (forse in una tabella o in un archivio BLOB di Azure). È quindi necessario aggiungere un campo nell'indice per contenere un riferimento URL ai dati esterni. Le dimensioni massime di un singolo documento sono pari a 16 MB (o inferiore se si esegue il caricamento bulk di più documenti in un'unica richiesta). Per altre informazioni, vedere [limiti dei servizi in Azure ricerca cognitiva](search-limits-quotas-capacity.md).
>

**Considerazioni sul volume delle query**

Il numero di query al secondo (query al secondo) è una metrica importante durante l'ottimizzazione delle prestazioni, ma in genere è solo un livello di considerazione se si prevede un volume di query elevato all'inizio.

I livelli standard possono fornire un equilibrio tra repliche e partizioni. È possibile aumentare il turnaround della query aggiungendo repliche per il bilanciamento del carico o aggiungere partizioni per l'elaborazione parallela. È quindi possibile ottimizzare le prestazioni dopo il provisioning del servizio.

Se si prevedono volumi di query sostenuti elevati dall'inizio, è consigliabile prendere in considerazione livelli standard più elevati, supportati da hardware più potente. È quindi possibile portare le partizioni e le repliche offline oppure passare a un servizio di livello inferiore, se non si verificano questi volumi di query. Per altre informazioni su come calcolare la velocità effettiva delle query, vedere [ottimizzazione delle prestazioni e delle ricerca cognitiva di Azure](search-performance-optimization.md).

I livelli ottimizzati per l'archiviazione sono utili per i carichi di lavoro di dati di grandi dimensioni, supportando una maggiore quantità di spazio di archiviazione per gli indici disponibili quando i requisiti di latenza È comunque necessario usare repliche aggiuntive per il bilanciamento del carico e partizioni aggiuntive per l'elaborazione parallela. È quindi possibile ottimizzare le prestazioni dopo il provisioning del servizio.

**Contratti di servizio**

Il livello gratuito e le funzionalità di anteprima non forniscono [contratti di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Per tutti i livelli fatturabili, i contratti di servizio diventano effettivi quando viene effettuato il provisioning di una ridondanza sufficiente per il servizio. È necessario disporre di due o più repliche per i contratti di esecuzione di query (lettura). È necessario avere tre o più repliche per i contratti di esecuzione di query e indicizzazione (lettura/scrittura). Il numero di partizioni non influisce sui contratti di contratto.

## <a name="tips-for-tier-evaluation"></a>Suggerimenti per la valutazione del livello

+ Consenti le metriche per la compilazione delle query e raccoglie i dati relativi ai modelli di utilizzo (query durante l'orario di ufficio, indicizzazione durante gli orari di minore attività). Usare questi dati per informare le decisioni relative al provisioning del servizio. Sebbene non sia pratico a cadenza oraria o giornaliera, è possibile modificare dinamicamente le partizioni e le risorse in modo da adattare le modifiche pianificate nei volumi di query. È anche possibile gestire le modifiche non pianificate ma prolungate se i livelli contengono abbastanza tempo per garantire l'esecuzione di un'azione.

+ Tenere presente che l'unico svantaggio del provisioning è che potrebbe essere necessario eliminare un servizio se i requisiti effettivi sono maggiori delle stime. Per evitare l'alterazione del servizio, è necessario creare un nuovo servizio a un livello superiore e eseguirlo affiancato fino a quando tutte le app e le richieste non sono destinate al nuovo endpoint.

## <a name="next-steps"></a>Passaggi successivi

Inizia con un livello gratuito e crea un indice iniziale usando un subset dei dati per comprenderne le caratteristiche. La struttura dei dati in Azure ricerca cognitiva è una struttura di indice invertita. Le dimensioni e la complessità di un indice invertito sono determinate dal contenuto. Tenere presente che un indice molto ridondante tende a generare un indice più piccolo rispetto a un contenuto molto irregolare. Pertanto, le caratteristiche del contenuto invece delle dimensioni del set di dati determinano i requisiti di archiviazione dell'indice.

Dopo aver eseguito una stima iniziale delle dimensioni dell'indice, effettuare il [provisioning di un servizio fatturabile](search-create-service-portal.md) su uno dei livelli descritti in questo articolo: Basic, standard o con ottimizzazione per l'archiviazione. Rilassare tutti i vincoli artificiali sul dimensionamento dei dati e [ricompilare l'indice](search-howto-reindex.md) per includere tutti i dati che si desidera siano disponibili per la ricerca.

[Allocare le partizioni e le repliche](search-capacity-planning.md) in base alle esigenze per ottenere le prestazioni e la scala desiderate.

Se le prestazioni e la capacità sono ottimali, l'operazione è terminata. In caso contrario, ricreare un servizio di ricerca in un altro livello più adatto alle proprie esigenze.

> [!NOTE]
> In caso di domande, pubblica un post su [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) o [Contatta il supporto tecnico di Azure](https://azure.microsoft.com/support/options/).