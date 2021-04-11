---
title: Spiegazione della metrica di metriche di monitoraggio di Azure per l'aggregazione e la visualizzazione
description: Informazioni dettagliate sul modo in cui vengono aggregate le metriche in monitoraggio di Azure
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: c89b352954f114ec9da22cad6751bb57ef59899b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106381798"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Spiegazione della metrica di metriche di monitoraggio di Azure per l'aggregazione e la visualizzazione

In questo articolo viene illustrata l'aggregazione delle metriche nel database di monitoraggio di Azure di serie temporali che riporta le [metriche della piattaforma](../data-platform.md) di monitoraggio di Azure e le [metriche personalizzate](../essentials/metrics-custom-overview.md). Questo articolo si applica anche alle [metriche di Application Insights](../app/app-insights-overview.md)standard. 

Questo è un argomento complesso e non è necessario comprendere tutte le informazioni contenute in questo articolo per usare in modo efficace le metriche di monitoraggio di Azure.

## <a name="overview-and-terms"></a>Panoramica e termini

Quando si aggiunge una metrica a un grafico, Esplora metriche ne seleziona automaticamente l'aggregazione predefinita. Il valore predefinito è opportuno negli scenari di base, ma è possibile usare aggregazioni diverse per ottenere informazioni più dettagliate sulla metrica. Per visualizzare aggregazioni diverse in un grafico è necessario comprendere il modo in cui vengono gestite da Esplora metriche.

Per prima cosa è necessario definire alcuni termini:

- **Valore della metrica** : un singolo valore di misurazione raccolto per una risorsa specifica.
- **Database di serie temporali** : database ottimizzato per l'archiviazione e il recupero di punti dati che contengono tutti un valore e un timestamp corrispondente. 
- **Periodo di tempo** : periodo di tempo generico.
- **Intervallo di tempo** : periodo di tempo tra la raccolta di due valori di metrica. 
- **Intervallo di tempo** : periodo di tempo visualizzato in un grafico. Il valore predefinito tipico è 24 ore. Sono disponibili solo intervalli specifici. 
- **Granularità temporale** o **intervallo di tempo** : periodo di tempo utilizzato per aggregare i valori in modo da consentire la visualizzazione in un grafico. Sono disponibili solo intervalli specifici. Il valore minimo corrente è 1 minuto. Il valore di granularità dell'ora deve essere inferiore all'intervallo di tempo selezionato per essere utile; in caso contrario, viene visualizzato un solo valore per l'intero grafico. 
- **Tipo di aggregazione** : tipo di statistica calcolato da più valori di metrica.  
- **Aggregate** : processo di assunzione di più valori di input e quindi di utilizzo per produrre un singolo valore di output tramite le regole definite dal tipo di aggregazione. Ad esempio, prendendo una media di più valori.  

## <a name="summary-of-process"></a>Riepilogo del processo

Le metriche sono una serie di valori archiviati con un timestamp. In Azure la maggior parte delle metriche viene archiviata nel database di serie temporali di metriche di Azure. Quando si esegue il tracciato di un grafico, i valori delle metriche selezionate vengono recuperati dal database e quindi aggregati separatamente in base alla granularità temporale scelta (nota anche come granularità temporale). Per selezionare le dimensioni della granularità temporale, è possibile usare il [Pannello di selezione Esplora metriche tempo](../essentials/metrics-getting-started.md#select-a-time-range). Se non si esegue una selezione esplicita, la granularità temporale viene selezionata automaticamente in base all'intervallo di tempo attualmente selezionato. Una volta selezionati, i valori delle metriche acquisiti durante ogni intervallo di granularità dell'ora vengono aggregati e inseriti nel grafico, uno per ogni intervallo.

## <a name="aggregation-types"></a>Tipi di aggregazione 

In Esplora metriche sono disponibili cinque tipi di aggregazione di base. Esplora metriche nasconde le aggregazioni irrilevanti e non possono essere usate per una determinata metrica. 

- **Sum** : somma di tutti i valori acquisiti nell'intervallo di aggregazione. Talvolta definito aggregazione totale.
- **Count** : numero di misurazioni acquisite sull'intervallo di aggregazione. Il conteggio non esamina il valore della misura, ma solo il numero di record. 
- **Media** : media dei valori delle metriche acquisiti nell'intervallo di aggregazione. Per la maggior parte delle metriche, questo valore è Sum/Count. 
- **Min** : il valore più piccolo acquisito nell'intervallo di aggregazione.
- **Max** : il valore più grande acquisito nell'intervallo di aggregazione.

Si supponga, ad esempio, che in un grafico venga mostrata la metrica **totale di rete in uscita** per una macchina virtuale utilizzando l'aggregazione **Sum** nelle ultime 24 ore. È possibile modificare l'intervallo di tempo e la granularità dalla parte superiore destra del grafico, come illustrato nello screenshot seguente.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="Screenshot che illustra l'intervallo di tempo e la selezione granularità temporale" border="true":::

Per la granularità temporale = 30 minuti e l'intervallo di tempo = 24 ore:

- Il grafico viene disegnato da 48 punti di riferimento. Ovvero 24 ore x 2 punti di datapoints all'ora (60min/30min) aggregati di 1 minuto DataPoints. 
- Il grafico a linee connette 48 punti nell'area del tracciato del grafico. 
- Ogni punto dati rappresenta la somma di tutti i byte di rete inviati durante ogni periodo di 30 minuti pertinente. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="Screenshot che mostra i dati in un grafico a linee impostato sull'intervallo di tempo di 24 ore e la granularità di 30 minuti" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*Fare clic sulle immagini in questa sezione per visualizzare le versioni più grandi.*

Se si passa la granularità dell'ora a 15 minuti, il grafico viene disegnato da 96 punti dati aggregati. Ovvero 60min/15 minuti = 4 punti di DataPoint all'ora x 24 ore.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="Screenshot che mostra i dati in un grafico a linee impostato sull'intervallo di tempo di 24 ore e la granularità di 15 minuti" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Per la granularità temporale di 5 minuti, si ottengono 24 x (60/5) = 288 punti. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="Screenshot che mostra i dati in un grafico a linee impostato sull'intervallo di tempo di 24 ore e la granularità di 5 minuti" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Per la granularità temporale di 1 minuto (il più piccolo possibile nel grafico), si ottengono 24 x 60/1 = 1440 punti. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="Screenshot che mostra i dati in un grafico a linee impostato sull'intervallo di tempo di 24 ore e la granularità di 1 minuto" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

I grafici hanno un aspetto diverso per queste sommatoria, come illustrato nelle schermate precedenti.  Si noti che questa macchina virtuale ha un numero elevato di output in un periodo di tempo ridotto rispetto al resto dell'intervallo di tempo.  

Con la granularità temporale è possibile regolare il rapporto "segnale a rumore" di un grafico. Le aggregazioni più elevate eliminano il rumore e i picchi smussati.  Si notino le variazioni nel grafico di 1 minuto inferiore e il modo in cui vengono smussate Man mano che si passa a valori di granularità più elevati. 

Questo comportamento di smussamento è importante quando si inviano questi dati ad altri sistemi, ad esempio gli avvisi. In genere, in genere non si vuole ricevere avvisi con picchi molto brevi nel tempo della CPU superiore al 90%. Tuttavia, se la CPU rimane al 90% per 5 minuti, probabilmente è importante. Se si configura una regola di avviso sulla CPU (o su qualsiasi metrica), la granularità temporale maggiore può ridurre il numero di falsi avvisi ricevuti. 

È importante stabilire cosa "normale" per il carico di lavoro per conoscere l'intervallo di tempo migliore. Questo è uno dei vantaggi degli [avvisi dinamici](../alerts/alerts-dynamic-thresholds.md), che è un argomento diverso non trattato qui.  

## <a name="how-the-system-collects-metrics"></a>Come il sistema raccoglie le metriche

La raccolta dati varia in base alla metrica. 

### <a name="measurement-collection-frequency"></a>Frequenza raccolta misure 

Esistono due tipi di periodi di raccolta.

- **Regolare** : la metrica viene raccolta in un intervallo di tempo coerente che non varia.

- **Basata sull'attività** : la metrica viene raccolta in base al momento in cui si verifica una transazione di un determinato tipo. Ogni transazione include una voce della metrica e un timestamp. Non vengono raccolti a intervalli regolari, pertanto è presente un numero variabile di record in un determinato periodo di tempo. 

### <a name="granularity"></a>Granularità

L'intervallo di tempo minimo è di 1 minuto, ma il sistema sottostante può acquisire i dati più velocemente a seconda della metrica. Ad esempio, la percentuale di CPU viene rilevata ogni 15 secondi a intervalli regolari. Poiché gli errori HTTP vengono rilevati come transazioni, possono superare facilmente molti più di un minuto. Altre metriche, ad esempio l'archiviazione SQL, vengono acquisite ogni 20 minuti. Questa scelta riguarda il singolo provider di risorse e il tipo. La maggior parte del tentativo di fornire l'intervallo più piccolo possibile.

### <a name="dimensions-splitting-and-filtering"></a>Dimensioni, suddivisione e filtro

Le metriche vengono acquisite per ogni singola risorsa. Tuttavia, il livello in cui la metrica viene raccolta, archiviata e in grado di essere tracciato può variare. Questo livello è rappresentato da metriche aggiuntive disponibili nelle **dimensioni della metrica**. Ogni singolo provider di risorse consente di definire la modalità di analisi dettagliata dei dati raccolti. Monitoraggio di Azure definisce solo il modo in cui devono essere presentati e archiviati i dettagli. 

Quando si crea un grafico di una metrica in Esplora metriche, è possibile scegliere di suddividere il grafico in base a una dimensione.  Suddividendo un grafico si intende esaminare i dati sottostanti per ottenere informazioni più dettagliate e visualizzare i dati grafici o filtrati in Esplora metriche.

Ad esempio, [Microsoft. ApiManagement/Service](./metrics-supported.md#microsoftapimanagementservice) ha la *posizione* come dimensione per molte metriche. 

- La **capacità** è una metrica di questo tipo. Se la dimensione del *percorso* implica che il sistema sottostante archivia un record di metrica per la capacità di ogni posizione, anziché una sola per l'importo aggregato. È quindi possibile recuperare o suddividere tali informazioni in un grafico delle metriche.  

- Esaminando la **durata complessiva delle richieste del gateway**, sono disponibili 2 dimensioni per la *posizione* e il *nome host*, che consentono di sapere la posizione di una durata e il nome host da cui provengono.  

- Una delle metriche più flessibili, **richieste**, ha 7 dimensioni diverse. 
 
Per informazioni dettagliate su ogni metrica e sulle dimensioni disponibili, vedere l'articolo [supportato sulle metriche](./metrics-supported.md) di monitoraggio di Azure. Inoltre, la documentazione per ogni provider di risorse e tipo può fornire informazioni aggiuntive sulle dimensioni e sulle misure che misurano.

È possibile utilizzare la suddivisione e il filtraggio insieme per analizzare un problema. Di seguito è riportato un esempio di un grafico che mostra i *byte di scrittura del disco media* per un gruppo di macchine virtuali in un gruppo di risorse. Si dispone di un rollup di tutte le macchine virtuali con questa metrica, ma è possibile approfondire il problema per vedere quali sono effettivamente responsabili dei picchi circa le 6.00. Si tratta dello stesso computer? Quanti computer sono interessati?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="Screenshot che mostra i byte totali di scrittura su disco per tutte le macchine virtuali nel gruppo di risorse di Contoso Hotels" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*Fare clic sulle immagini in questa sezione per visualizzare le versioni più grandi.*

Quando si applica la suddivisione, è possibile visualizzare i dati sottostanti, ma è un po' di confusione. Sono presenti 20 VM aggregate nel grafico precedente. In questo caso, abbiamo usato il mouse per passare il puntatore del mouse sul picco alle 6, che indica che CH-DCVM11 è la causa. ma è difficile vedere il resto dei dati associati a tale macchina virtuale a causa di altre macchine virtuali che ingombrano il grafico. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="Screenshot che mostra i byte scritti su disco per tutte le macchine virtuali nel gruppo di risorse di Contoso Hotels diviso per nome macchina virtuale" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

L'uso del filtro consente di eseguire la pulizia del grafico per vedere cosa accade realmente. È possibile selezionare o deselezionare le macchine virtuali che si vuole visualizzare. Si notino le linee tratteggiate. Questi sono indicati in una sezione successiva.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="Screenshot che mostra i byte scritti su disco per tutte le macchine virtuali nel gruppo di risorse di Contoso Hotels Split e filtrate in base al nome della macchina virtuale" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

Per altre informazioni su come visualizzare i dati delle dimensioni divise in un grafico di Esplora metriche, vedere [funzionalità avanzate di Esplora metriche-filtri e suddivisione](../essentials/metrics-charts.md#filters).

### <a name="null-and-zero-values"></a>Valori NULL e zero

Quando il sistema prevede i dati delle metriche da una risorsa ma non li riceve, registra un valore NULL.  NULL è diverso da un valore zero, che diventa importante nel calcolo delle aggregazioni e della creazione di grafici. I valori NULL non vengono conteggiati come misurazioni valide. 

I valori NULL vengono visualizzati in modo diverso in grafici diversi. I grafici a dispersione ignorano la visualizzazione di un punto sul grafico. I grafici a barre non visualizzano la barra. Nei grafici a linee, NULL può essere visualizzato come [linee tratteggiate o tratteggiate](../essentials/metrics-troubleshoot.md#chart-shows-dashed-line) come quelle mostrate nello screenshot della sezione precedente. Quando si calcolano le medie che includono valori NULL, il numero di punti dati da cui calcolare la media è inferiore.  Questo comportamento può a volte causare un calo imprevisto dei valori in un grafico, anche se in genere è inferiore a se il valore è stato convertito in uno zero e utilizzato come punto di riferimento valido.  

Quando non viene ricevuto alcun dato, le [metriche personalizzate](../essentials/metrics-custom-overview.md) utilizzano sempre valori null. Con le [metriche della piattaforma](../data-platform.md), ogni provider di risorse decide se usare zeri o valori null in base a quanto è più sensato per una determinata metrica.

Gli avvisi di monitoraggio di Azure usano i valori scritti dal provider di risorse nel database delle metriche, quindi è importante capire in che modo il provider di risorse gestisce i valori NULL visualizzando prima i dati.

## <a name="how-aggregation-works"></a>Funzionamento dell'aggregazione

I grafici delle metriche nel sistema precedente mostrano tipi diversi di dati aggregati. Il sistema pre-aggrega i dati in modo che i grafici richiesti possano essere visualizzati più rapidamente senza una grande quantità di calcoli ripetuti.  

In questo esempio:

- Si sta raccogliendo una metrica transazionale **fittizia** denominata **errori http** 
- Il *Server* è una dimensione per la metrica degli **errori http** .
- Sono disponibili 3 server: Server A, B e C.

Per semplificare la spiegazione, si inizia solo con il tipo di aggregazione SUM. 

### <a name="sub-minute-to-1-minute-aggregation"></a>Aggregazione da minuto a 1 minuto

Prima i dati delle metriche non elaborati vengono raccolti e archiviati nel database di metriche di monitoraggio di Azure. In questo caso, ogni server dispone di record di transazioni archiviati con un timestamp perché il *Server* è una dimensione. Dato che il periodo di tempo più piccolo che è possibile visualizzare come cliente è 1 minuto, i timestamp vengono prima aggregati in valori di metrica di 1 minuto per ogni singolo server.  Il processo di aggregazione per il server B è illustrato nell'immagine seguente. I server A e C vengono eseguiti nello stesso modo e hanno dati diversi.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="Screenshot che mostra le voci transazionali del sottominuto in aggregazioni di 1 minuto. " border="false":::

I valori aggregati di 1 minuto risultanti vengono archiviati come nuove voci nel database di metriche, in modo che possano essere raccolti per calcoli successivi. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="Screenshot che Mostra più voci aggregate di 1 minuto tra le dimensioni del server. Server A, B e C visualizzati singolarmente" border="false":::

### <a name="dimension-aggregation"></a>Aggregazione di dimensioni

I calcoli di 1 minuto vengono quindi compressi dalla dimensione e archiviati di nuovo come singoli record.   In questo caso, tutti i dati di tutti i singoli server vengono aggregati in una metrica di intervallo di 1 minuto e archiviati nel database di metriche per l'utilizzo in aggregazioni successive.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="Screenshot che Mostra più voci aggregate di 1 minuto del server A, B e C aggregate in un minuto di tutti i server interi" border="false":::

Per maggiore chiarezza, nella tabella seguente viene illustrato il metodo di aggregazione.

| Periodo   | Server A | Server B | Server C | Sum (A + B + C)|
| -------- | -------- | -------- | -------- | --------   |  
| Minuto 1 | 1        | 1        | 1        | 3          |
| Minuto 2 | 0        | 5        | 1        | 6          |
| Minuto 3 | 0        | 5        | 1        | 6          |
| Minuto 4 | 2        | 3        | 4        | 9          |
| Minuto 5 | 1        | 0        | 3        | 4          |
| Minuto 6 | 1        | 0        | 4        | 5          |
| Minuto 7 | 1        | 2        | 4        | 7          |
| Minuto 8 | 0        | 1        | 0        | 1          |
| Minuto 9 | 1        | 1        | 4        | 6          |
| Minuto 10| 2        | 1        | 0        | 3          |

Viene visualizzata una sola dimensione, ma questo stesso processo di aggregazione e archiviazione si verifica per **tutte le dimensioni** supportate da una metrica.

- Raccogliere i valori in un set aggregato di 1 minuto in base a tale dimensione. Archiviare tali valori. 
- Comprime la dimensione in una somma aggregata di 1 minuto. Archiviare tali valori. 

Viene ora introdotta un'altra dimensione degli errori HTTP denominata NetworkAdapter. Supponiamo di avere un numero variabile di schede per server.

- Il server A ha 1 Adapter
- Il server B dispone di 2 schede
- Il server C dispone di 3 Adapter

Si raccoglieranno i dati per le transazioni seguenti separatamente. Verranno contrassegnati con:

- Ora
- Valore
- Server da cui proviene la transazione
- Adapter da cui proviene la transazione

Ognuno di questi flussi di sottominuti verrebbe quindi aggregato in valori di una serie temporale di 1 minuto e archiviato nel database delle metriche di monitoraggio di Azure:

- Server A, Adapter 1
- Server B, Adapter 1
- Server B, Adapter 2
- Server C, Adapter 1
- Server C, Adapter 2
- Server C, Adapter 3

Verranno inoltre archiviate le aggregazioni compresse seguenti:

- Server A, Adapter 1 (poiché non c'è nulla da comprimere, verrà archiviato nuovamente)
- Server B, Adapter 1 + 2
- Server C, Adapter 1 + 2 + 3
- TUTTI i server, tutti gli adapter

Ciò indica che le metriche con un numero elevato di dimensioni hanno un numero maggiore di aggregazioni. Non è importante conoscere tutte le permutazioni, ma è sufficiente comprendere la motivazione. Il sistema vuole avere sia i dati singoli che i dati aggregati archiviati per un recupero rapido per l'accesso in qualsiasi grafico. Il sistema seleziona l'aggregazione archiviata più pertinente o i dati non elaborati sottostanti a seconda di ciò che si sceglie di visualizzare. 

### <a name="aggregation-with-no-dimensions"></a>Aggregazione senza dimensioni

Poiché questa metrica dispone di un *Server* delle dimensioni, è possibile ottenere i dati sottostanti per server a, B e C sopra tramite la suddivisione e il filtraggio, come illustrato in precedenza in questo articolo. Se la metrica non ha un *Server* come dimensione, è possibile che un cliente possa accedere solo alle somme aggregate di 1 minuto visualizzate in nero nel diagramma. Ovvero i valori 3, 6, 6, 9 e così via. Il sistema non eseguirebbe il lavoro sottostante per aggregare valori divisi che non li avrebbe mai usati in Esplora metriche o li invierà tramite l'API REST delle metriche. 

## <a name="viewing-time-granularities-above-1-minute"></a>Visualizzazione di granularità temporali superiore a 1 minuto

Se si richiedono metriche con una granularità maggiore, il sistema usa le somme aggregate di 1 minuto per calcolare le somme per le granularità temporali più grandi.  Di seguito, le linee punteggiate mostrano il metodo di sommatoria per le granularità di 2 minuti e 5 minuti. Anche in questo caso, viene visualizzato solo il tipo di aggregazione SUM per semplicità.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="Screenshot che Mostra più voci aggregate di 1 minuto tra le dimensioni del server aggregate in periodi di tempo di 2 minuti e 5 minuti." border="false":::

Per la granularità del tempo di 2 minuti.

| Periodo       | Somme       
| -------------|-------------|  
| Minuto 1 & 2 | (3 + 6) = 9 |
| Minuto 3 & 4 | (6 + 9) = 15|
| Minuto 4 & 5 | (4 + 5) = 9 |
| Minuto 6 & 7 | (7 + 1) = 8 |
| Minuto 8 & 9 | (6 + 3) = 9 |

Per la granularità di 5 minuti.

| Periodo              |            Somme        |
|---------------------|------------------------|  
| Minuti da 1 a 5  | 3 + 6 + 6 + 9 + 4 = 28 |
| Minuti da 6 a 10 | 5 + 7 + 1 + 6 + 3 = 22 |

Il sistema usa i dati aggregati archiviati che offrono le migliori prestazioni. 

Di seguito è riportato il diagramma più grande per il processo di aggregazione di 1 minuto precedente, con alcune delle frecce escluse per migliorare la leggibilità.

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="Screenshot che mostra il consolidamento delle 3 schermate precedenti. Più voci aggregate di 1 minuto tra le dimensioni del server aggregate in intervalli di 1 minuto, 2 minuti e 5 minuti. Server A, B e C visualizzati singolarmente" border="false":::

## <a name="more-complex-example"></a>Esempio più complesso

Di seguito è riportato un esempio più ampio che usa i valori per una metrica fittizia denominata tempo di risposta HTTP in millisecondi.  In questa sezione vengono introdotti ulteriori livelli di complessità.

1. Viene mostrata l'aggregazione per Sum, Count, min e Max e per il calcolo della media.
2. Vengono mostrati i valori NULL e il modo in cui influiscono sui calcoli. 

Si consideri l'esempio seguente. Le caselle e le frecce mostrano esempi di come i valori vengono aggregati e calcolati. 

Lo stesso processo di pre-aggregazione di 1 minuto, come descritto nella sezione precedente, si verifica per somme, conteggi, valori minimi e massimi.  La media non è tuttavia pre-aggregata. Viene ricalcolato utilizzando dati aggregati per evitare errori di calcolo.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="Screenshot che mostra un esempio complesso di aggregazione e calcolo di Sum, Count, min, Max e Average da 1 minuto a 10 minuti." border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

Prendere in considerazione il minuto 6 per l'aggregazione di 1 minuto, come evidenziato sopra. Questo minuto è il punto in cui server B è andato offline e ha interrotto i dati di report, probabilmente a causa di un riavvio. 

Dal minuto 6 precedente, i tipi di aggregazione calcolati di 1 minuto sono: 

| Tipo di aggregazione | Valore        | Note |
|------------------|--------------|-------|
| Sum              | 53 + 20 = 73 | |
| Conteggio            | 2            | Mostra l'effetto dei valori NULL.  Il valore sarebbe stato 3 Se il server fosse stato online.  |
| Minima          | 20           | |
| Massimo          | 53           | |
| Media          | 73/2       | Sempre la somma divisa per il conteggio. Non viene mai archiviato e ricalcolato sempre per ogni granularità temporale usando i numeri aggregati per tale granularità. Si noti il ricalcolo per le granularità di 5 minuti e 10 minuti evidenziate in precedenza. |

Il colore rosso del testo indica i valori che potrebbero essere considerati fuori dall'intervallo normale e Mostra come si propagano (o non riescono a) come la granularità del tempo aumenta. Si noti che i valori *min* e *Max* indicano che sono presenti anomalie sottostanti mentre la *media* e le *somme* perdono tali informazioni quando aumenta la granularità del tempo.  

È inoltre possibile notare che i valori NULL forniscono un calcolo migliore della media rispetto al caso in cui fossero stati utilizzati zeri.  

> [!NOTE] 
> Sebbene non sia il caso in questo esempio, *count* è uguale a *Sum* nei casi in cui una metrica viene sempre acquisita con il valore 1. Si tratta di una situazione comune quando una metrica tiene traccia dell'occorrenza di un evento transazionale, ad esempio il numero di errori HTTP indicati in un esempio precedente di questo articolo.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Esplora metriche](../essentials/metrics-getting-started.md)
- [Esplora metriche avanzate](../essentials/metrics-charts.md)
