---
title: Funzionalità avanzate di Esplora metriche di Azure
description: Informazioni sugli usi avanzati di Esplora metriche di Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: b042049c803ad04b54bb6c2a242ca1008bc17dd3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734520"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Funzionalità avanzate di Esplora metriche di Azure

> [!NOTE]
> Questo articolo presuppone che l'utente abbia familiarità con le funzionalità di base della funzionalità Esplora metriche di Azure di monitoraggio di Azure. Se si è un nuovo utente e si vuole imparare a creare il primo grafico delle metriche, vedere [Introduzione a Esplora metriche](./metrics-getting-started.md).

In monitoraggio di Azure le [metriche](data-platform-metrics.md) sono una serie di valori misurati e conteggi raccolti e archiviati nel corso del tempo. Le metriche possono essere standard (detta anche "piattaforma") o personalizzate. 

Le metriche standard sono fornite dalla piattaforma Azure. Riflettono le statistiche sull'integrità e sull'utilizzo delle risorse di Azure. 

## <a name="resource-scope-picker"></a>Selezione ambito risorsa
La selezione dell'ambito delle risorse consente di visualizzare le metriche tra le singole risorse e più risorse. Le sezioni seguenti illustrano come usare la selezione dell'ambito delle risorse. 

### <a name="select-a-single-resource"></a>Selezionare una singola risorsa
Selezionare **Metriche** dal menu **Monitoraggio di Azure** o dalla sezione **Monitoraggio** del menu di una risorsa. Scegliere quindi **selezionare un ambito** per aprire la selezione ambito. 

Usare selezione ambito per selezionare le risorse di cui si desidera visualizzare le metriche. L'ambito deve essere popolato se è stato aperto Esplora metriche di Azure dal menu di una risorsa. 

![Screenshot che illustra come aprire la selezione dell'ambito delle risorse.](./media/metrics-charts/scope-picker.png)

Per alcune risorse, è possibile visualizzare solo le metriche di una risorsa alla volta. Nel menu **tipi di risorse** queste risorse si trovano nella sezione **tutti i tipi di risorse** .

![Screenshot che mostra una singola risorsa.](./media/metrics-charts/single-resource-scope.png)

Dopo aver selezionato una risorsa, vengono visualizzate tutte le sottoscrizioni e i gruppi di risorse che contengono tale risorsa.

![Screenshot che mostra le risorse disponibili.](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Se si vuole che la funzionalità di visualizzare le metriche per più risorse contemporaneamente o per visualizzare le metriche in una sottoscrizione o in un gruppo di risorse, selezionare **Voto**.

Quando si è soddisfatti della selezione, selezionare **applica**.

### <a name="view-metrics-across-multiple-resources"></a>Visualizzare le metriche tra più risorse
Alcuni tipi di risorse possono eseguire query per le metriche su più risorse. Le risorse devono trovarsi nella stessa sottoscrizione e nella stessa località. Trovare questi tipi di risorse nella parte superiore del menu **tipi di risorse** . 

Per altre informazioni, vedere [selezionare più risorse](./metrics-dynamic-scope.md#select-multiple-resources).

![Screenshot che mostra i tipi di risorse incrociate.](./media/metrics-charts/multi-resource-scope.png)

Per i tipi compatibili con più risorse, è possibile eseguire una query per le metriche in una sottoscrizione o in più gruppi di risorse. Per altre informazioni, vedere [selezionare un gruppo di risorse o una sottoscrizione](./metrics-dynamic-scope.md#select-a-resource-group-or-subscription).

## <a name="multiple-metric-lines-and-charts"></a>Più linee e grafici di metriche

In Esplora metriche di Azure è possibile creare grafici che tracciano più linee metriche o visualizzano contemporaneamente più grafici di metriche. Questa funzionalità consente di:

- Correlare le metriche correlate nello stesso grafico per vedere come un valore è correlato a un altro.
- Visualizzare le metriche che usano unità di misura diverse in prossimità.
- Aggregare visivamente e confrontare le metriche da più risorse.

Si supponga, ad esempio, di avere cinque account di archiviazione e di voler conoscere la quantità di spazio utilizzata insieme. È possibile creare un grafico ad area in pila che mostra i singoli valori e la somma di tutti i valori in momenti specifici.

### <a name="multiple-metrics-on-the-same-chart"></a>Più metriche nello stesso grafico

Per visualizzare più metriche nello stesso grafico, [creare innanzitutto un nuovo grafico](./metrics-getting-started.md#create-your-first-metric-chart). Quindi selezionare **Aggiungi metrica**. Ripetere questo passaggio per aggiungere un'altra metrica nello stesso grafico.

> [!NOTE]
> In genere, i grafici non devono combinare metriche che usano unità di misura diverse. Ad esempio, evitare di combinare una metrica che usa i millisecondi con un altro che usa kilobyte. Evitare anche di combinare le metriche le cui scale differiscono significativamente. 
>
> In questi casi, è consigliabile usare invece più grafici. In Esplora metriche selezionare **Aggiungi grafico** per creare un nuovo grafico.

### <a name="multiple-charts"></a>Più grafici

Per creare un altro grafico che utilizza una metrica diversa, selezionare **Aggiungi grafico**.

Per riordinare o eliminare più grafici, selezionare il pulsante con i puntini di sospensione (**..**.) per aprire il menu grafico. Quindi scegliere **Sposta su**, **Sposta giù** o **Elimina**.

## <a name="aggregation"></a>Aggregazione

Quando si aggiunge una metrica a un grafico, Esplora metriche applica automaticamente un'aggregazione predefinita. Il valore predefinito è opportuno negli scenari di base. È tuttavia possibile usare un'aggregazione diversa per ottenere informazioni più dettagliate sulla metrica. 

Prima di utilizzare aggregazioni diverse in un grafico, è necessario comprendere il modo in cui vengono gestite da Esplora metriche. Le metriche sono una serie di misurazioni (o "valori metrici") acquisite in un periodo di tempo. Quando si traccia un grafico, i valori della metrica selezionata vengono aggregati separatamente nell'intervallo di *tempo*. 

Per selezionare le dimensioni dell'intervallo di tempo, usare il pannello di selezione dell' [ora](./metrics-getting-started.md#select-a-time-range)di Esplora metriche. Se non si seleziona esplicitamente la granularità temporale, per impostazione predefinita viene usato l'intervallo di tempo attualmente selezionato. Dopo aver determinato l'intervallo di tempo, i valori delle metriche acquisiti durante ogni intervallo di tempo vengono aggregati nel grafico, un punto dati per ogni intervallo di tempo.

Si supponga, ad esempio, che un grafico mostri la metrica del *tempo di risposta del server* . Viene utilizzata l'aggregazione *media* nell'intervallo di tempo delle *ultime 24 ore*. In questo esempio:

- Se la granularità temporale è impostata su 30 minuti, il grafico viene disegnato da 48 punti dati aggregati. Ovvero, il grafico a linee connette 48 punti nell'area del tracciato del grafico (24 ore x 2 punti dati all'ora). Ogni punto dati rappresenta la *media* di tutti i tempi di risposta acquisiti per le richieste server che si sono verificati durante ogni periodo di 30 minuti pertinente.
- Se si passa la granularità dell'ora a 15 minuti, si ottengono 96 punti dati aggregati.  Ovvero si ottengono 24 ore x 4 punti dati all'ora.

Esplora metriche è costituito da cinque tipi di aggregazione statistici di base: Sum, Count, min, Max e Average. L'aggregazione *Sum* viene talvolta chiamata aggregazione *totale* . Per molte metriche, Esplora metriche nasconde le aggregazioni irrilevanti e non può essere usata.

* **Sum**: somma di tutti i valori acquisiti durante l'intervallo di aggregazione.

    ![Screenshot di una richiesta di somma.](./media/metrics-charts/request-sum.png)

* **Count**: numero di misurazioni acquisite durante l'intervallo di aggregazione. 
    
    Quando la metrica viene sempre acquisita con il valore 1, l'aggregazione Count è uguale all'aggregazione Sum. Questo scenario è comune quando la metrica tiene traccia del numero di eventi distinti e ogni misura rappresenta un evento. Il codice emette un record della metrica ogni volta che arriva una nuova richiesta.

    ![Screenshot di una richiesta Count.](./media/metrics-charts/request-count.png)

* **Media**: media dei valori della metrica acquisiti durante l'intervallo di aggregazione.

    ![Screenshot di una richiesta media.](./media/metrics-charts/request-avg.png)

* **Min**: il valore più piccolo acquisito durante l'intervallo di aggregazione.

    ![Screenshot di una richiesta minima.](./media/metrics-charts/request-min.png)

* **Max**: il valore più grande acquisito durante l'intervallo di aggregazione.

    ![Screenshot di una richiesta massima.](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Filtri

È possibile applicare filtri ai grafici le cui metriche hanno dimensioni. Si supponga, ad esempio, una metrica "conteggio transazioni" con una dimensione "tipo di risposta". Questa dimensione indica se la risposta da transazioni ha avuto esito positivo o negativo. Se si applica un filtro su questa dimensione, viene visualizzata una linea del grafico solo per le transazioni con esito positivo o negativo. 

### <a name="add-a-filter"></a>Aggiungere un filtro

1. Sopra il grafico selezionare **Aggiungi filtro**.

2. Selezionare una dimensione (proprietà) da filtrare.

   ![Screenshot che mostra le dimensioni (proprietà) che è possibile filtrare.](./media/metrics-charts/028.png)

3. Consente di selezionare l'operatore che si desidera applicare alla dimensione (proprietà). L'operatore predefinito è = (uguale a)

   ![Screenshot che mostra l'operatore che è possibile usare con il filtro.](./media/metrics-charts/filter-operator.png)

4. Consente di selezionare i valori delle dimensioni che si desidera applicare al filtro durante il tracciato del grafico. in questo esempio viene illustrato come filtrare le transazioni di archiviazione riuscite:

   ![Screenshot che mostra le transazioni di archiviazione filtrate riuscite.](./media/metrics-charts/029.png)

5. Dopo avere selezionato i valori di filtro, fare clic all'esterno del selettore di filtro per chiuderlo. Il grafico mostra a questo punto il numero di transazioni di archiviazione con esito negativo:

   ![Screenshot che mostra il numero di transazioni di archiviazione non riuscite.](./media/metrics-charts/030.png)

6. È possibile ripetere i passaggi 1-5 per applicare più filtri agli stessi grafici.


## <a name="metric-splitting"></a>Suddivisione delle metriche

È possibile suddividere una metrica per dimensione per visualizzare il confronto tra segmenti diversi della metrica. La suddivisione può inoltre consentire di identificare i segmenti di una dimensione.

### <a name="apply-splitting"></a>Applicare la suddivisione

1. Sopra il grafico selezionare **applica suddivisione**.
 
   > [!NOTE]
   > I grafici con più metriche non possono usare la funzionalità di suddivisione. Inoltre, sebbene un grafico possa avere più filtri, può disporre di una sola dimensione di suddivisione.

2. Scegliere una dimensione in cui segmentare il grafico:

   ![Screenshot che mostra la dimensione selezionata in cui segmentare il grafico.](./media/metrics-charts/031.png)

   Il grafico mostra ora più righe, una per ogni segmento della dimensione:

   ![Screenshot che Mostra più righe, una per ogni segmento della dimensione.](./media/metrics-charts/segment-dimension.png)
   
3. Scegliere un limite per il numero di valori da visualizzare dopo la suddivisione in base alla dimensione selezionata. Il limite predefinito è 10, come illustrato nel grafico precedente. L'intervallo di limite è 1-50.
   
   ![Screenshot che mostra il limite di divisione, che limita il numero di valori dopo la suddivisione.](./media/metrics-charts/segment-dimension-limit.png)
   
4. Scegliere il tipo di ordinamento nei segmenti: crescente o decrescente. La selezione predefinita è decrescente.
   
   ![Screenshot che mostra il tipo di ordinamento sui valori divisi.](./media/metrics-charts/segment-dimension-sort.png)

5. Fare clic al di fuori del **selettore di raggruppamento** per chiuderlo.
   

   > [!NOTE]
   > Per nascondere i segmenti irrilevanti per lo scenario e semplificare la lettura dei grafici, utilizzare sia il filtro che la suddivisione sulla stessa dimensione.

## <a name="locking-the-range-of-the-y-axis"></a>Blocco dell'intervallo dell'asse y

Il blocco dell'intervallo dell'asse Value (y) diventa importante nei grafici che mostrano piccole fluttuazioni di valori di grandi dimensioni. 

Ad esempio, un calo del volume di richieste riuscite dal 99,99% al 99,5% potrebbe rappresentare una riduzione significativa della qualità del servizio. Tuttavia, se si usano le impostazioni predefinite del grafico, si nota che la fluttuazione di un valore numerico ridotto sarebbe difficile o addirittura impossibile. In questo caso, è possibile bloccare il limite inferiore del grafico al 99% per rendere più evidente una piccola riduzione. 

Un altro esempio è la fluttuazione della memoria disponibile. In questo scenario, il valore tecnicamente non raggiungerà mai 0. La correzione dell'intervallo a un valore superiore potrebbe rendere più semplice l'individuazione delle gocce nella memoria disponibile. 

Per controllare l'intervallo dell'asse y, aprire il menu del grafico (**...**). Selezionare quindi **Impostazioni grafico** per accedere alle impostazioni avanzate del grafico.

![Screenshot che evidenzia la selezione delle impostazioni del grafico.](./media/metrics-charts/033.png)

Modificare i valori nella sezione **Intervallo asse Y** oppure selezionare **automatico** per ripristinare i valori predefiniti.
 
 ![Screenshot che evidenzia la sezione relativa all'intervallo dell'asse Y.](./media/metrics-charts/034.png)

> [!WARNING]
> Se è necessario bloccare i limiti dell'asse y per i grafici che tengono traccia dei conteggi o delle somme in un periodo di tempo (usando le aggregazioni Count, Sum, min o max), è in genere necessario specificare una granularità a tempo fisso. In questo caso, non è necessario basarsi sulle impostazioni predefinite automatiche. 
>
> Si sceglie una granularità di tempo fissa perché i valori del grafico cambiano quando la granularità temporale viene modificata automaticamente dopo che un utente ridimensiona una finestra del browser o modifica la risoluzione dello schermo. La variazione risultante nella granularità temporale influiscono sull'aspetto del grafico, invalidando la selezione corrente dell'intervallo dell'asse y.

## <a name="line-colors"></a>Colori linea

Una volta configurati i grafici, alle linee del grafico viene assegnato automaticamente un colore da una tavolozza predefinita. È possibile modificare questi colori.

Per modificare il colore di una linea del grafico, selezionare la barra colorata nella legenda che corrisponde al grafico. Verrà visualizzata la finestra di dialogo Selezione colori. Utilizzare la selezione colori per configurare il colore della linea.

![Screenshot che Mostra come modificare il colore.](./media/metrics-charts/035.png)

I colori personalizzati vengono conservati quando si aggiunge il grafico a un dashboard. Nella sezione seguente viene illustrato come aggiungere un grafico.

## <a name="pinning-to-dashboards"></a>Aggiunta ai dashboard 

Dopo aver configurato un grafico, potrebbe essere necessario aggiungerlo a un dashboard. Con l'aggiunta di un grafico a un dashboard, è possibile renderlo accessibile al team. È anche possibile ottenere informazioni dettagliate visualizzandolo nel contesto di altri dati di telemetria di monitoraggio.

Per aggiungere un grafico configurato a un dashboard, fare clic su **Aggiungi al dashboard** nell'angolo superiore destro del grafico.

![Screenshot che illustra come aggiungere un grafico a un dashboard.](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Regole di avviso

È possibile usare i criteri di visualizzazione per creare una regola di avviso basata sulla metrica. La nuova regola di avviso includerà la risorsa di destinazione del grafico, la metrica, la suddivisione e le dimensioni del filtro. È possibile modificare queste impostazioni usando il riquadro di creazione della regola di avviso.

Per iniziare, selezionare **nuova regola di avviso**.

![Screenshot che mostra il pulsante nuova regola di avviso evidenziato in rosso.](./media/metrics-charts/042.png)

Si apre il riquadro di creazione della regola di avviso. Nel riquadro vengono visualizzate le dimensioni della metrica del grafico. I campi nel riquadro vengono precompilati per semplificare la personalizzazione della regola.

![Screenshot che illustra il riquadro di creazione della regola.](./media/metrics-charts/041.png)

Per altre informazioni, vedere [creare, visualizzare e gestire gli avvisi delle metriche](../alerts/alerts-metric.md).

## <a name="correlate-metrics-to-logs"></a>Correlare le metriche ai log
Per consentire ai clienti di diagnosticare la causa radice delle anomalie nel grafico delle metriche, sono stati creati log di drill-through. Il drill-through dei log consente ai clienti di correlare i picchi nel grafico delle metriche ai log e alle query. 

Prima di approfondire l'esperienza, è opportuno introdurre prima i diversi tipi di log e query forniti. 

| Termine             | Definizione  | 
|------------------|-------------|
| Log attività    | Fornisce informazioni approfondite sulle operazioni su ogni risorsa di Azure nella sottoscrizione dall'esterno (il piano di gestione) oltre agli aggiornamenti sugli eventi di integrità del servizio. Usare il log attività per determinare l'elemento, chi e quando per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. È disponibile un singolo log attività per ogni sottoscrizione di Azure.  |   
| Log di diagnostica   | Fornire informazioni approfondite sulle operazioni eseguite all'interno di una risorsa di Azure (il piano dati), ad esempio il recupero di un segreto da un Key Vault o l'esecuzione di una richiesta a un database. Il contenuto dei log delle risorse varia in base al servizio di Azure e al tipo di risorsa. **Nota:** Deve essere fornito dal servizio e abilitato dal cliente  | 
| Log consigliato | Query basate su scenari che possono essere utilizzate dal cliente per analizzare le anomalie nella Esplora metriche.  |

Attualmente, i log di drill-through sono disponibili per i provider di risorse selezionati. I provider di risorse che hanno l'esperienza di drill-through completa nei log sono: 

* Application Insights 
* Autoscale 
* Servizi app  
* Archiviazione  

Di seguito sono riportate alcune esperienze di esempio per il provider di risorse Application Insights.

![Picco degli errori nel pannello delle metriche di App Insights](./media/metrics-charts/drill-into-log-ai.png)

Per diagnosticare il picco nelle richieste non riuscite, fare clic su "drill into log".

![Screenshot dell'elenco a discesa dei log drill-through](./media/metrics-charts/drill-into-logs-dropdown.png)

Facendo clic sull'opzione errore, si verrà portati a un pannello di errore personalizzato che fornisce le operazioni di operazione non riuscite, i tipi di eccezioni principali e le dipendenze. 

![Screenshot del pannello degli errori di Application Insights](./media/metrics-charts/ai-failure-blade.png)

### <a name="common-problems-with-drill-into-logs"></a>Problemi comuni relativi ai log di drill-through

* Log e query sono disabilitati. per visualizzare i log e le query consigliati, è necessario indirizzare i log di diagnostica a Log Analytics. Leggere [questo documento](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) per informazioni su come eseguire questa operazione. 
* Sono disponibili solo i log attività. la funzionalità drill-in log è disponibile solo per i provider di risorse selezionati. Per impostazione predefinita, vengono forniti i log attività. 

 
## <a name="troubleshooting"></a>Risoluzione dei problemi

Se non vengono visualizzati dati nel grafico, esaminare le seguenti informazioni per la risoluzione dei problemi:

* I filtri si applicano a tutti i grafici nel riquadro. Quando ci si concentra su un grafico, assicurarsi di non impostare un filtro che escluda tutti i dati in un altro grafico.

* Per impostare filtri diversi in grafici diversi, creare i grafici in pannelli diversi. Salvare quindi i grafici come Preferiti distinti. Se lo si desidera, è possibile aggiungere i grafici al dashboard in modo che possano essere visualizzati insieme.

* Se si Segmenta un grafico in base a una proprietà non definita dalla metrica, nel grafico non verrà visualizzato alcun contenuto. Provare a cancellare la segmentazione (suddivisione) oppure scegliere un'altra proprietà.

## <a name="next-steps"></a>Passaggi successivi

Per creare dashboard di utilità pratica usando le metriche, vedere [creazione di dashboard KPI personalizzati](../app/tutorial-app-dashboards.md).

