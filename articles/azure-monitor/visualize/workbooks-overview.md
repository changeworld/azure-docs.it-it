---
title: Panoramica di Cartelle di lavoro di Monitoraggio di Azure
description: Informazioni su come le cartelle di lavoro forniscono un'area di disegno flessibile per l'analisi dei dati e la creazione di report visivi avanzati all'interno del portale di Azure.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: a02e5fced0a9e338a32d8d8beaa9e4b5fca994e8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309479"
---
# <a name="azure-monitor-workbooks"></a>Cartelle di lavoro di monitoraggio di Azure

Le cartelle di lavoro offrono un canvas flessibile per l'analisi dei dati e la creazione di report visivi avanzati nel portale di Azure. Consentono di sfruttare più origini dati in tutto Azure e di combinarle in esperienze interattive unificate.

Ecco una procedura dettagliata video sulla creazione di cartelle di lavoro.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Origini dati

Le cartelle di lavoro possono eseguire query su dati di origini diverse in Azure. Gli autori delle cartelle di lavoro possono trasformare questi dati per offrire informazioni dettagliate sulla disponibilità, le prestazioni, l'uso e l'integrità complessiva dei componenti sottostanti. Ad esempio, l'analisi dei log delle prestazioni dalle macchine virtuali per identificare le istanze di CPU elevata o di memoria insufficiente e visualizzare i risultati come griglia in un report interattivo.
  
Ma il vero valore delle cartelle di lavoro è la capacità di combinare dati provenienti da origini diverse in un unico report. Ciò consente la creazione di visualizzazioni di risorse Composite o join tra le risorse che consentono dati e informazioni dettagliate più completi che altrimenti sarebbero impossibili.

Le cartelle di lavoro sono attualmente compatibili con le origini dati seguenti:

* [Log](../visualize/workbooks-data-sources.md#logs)
* [Metriche](../visualize/workbooks-data-sources.md#metrics)
* [Diagramma delle risorse di Azure](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Avvisi (anteprima)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Stato del carico di lavoro](../visualize/workbooks-data-sources.md#workload-health)
* [Integrità risorse di Azure](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Esplora dati di Azure](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualizzazioni

Le cartelle di lavoro di offrono un set completo di funzionalità per la visualizzazione dei dati. Per esempi dettagliati di ogni tipo di visualizzazione, è possibile consultare i collegamenti seguenti:

* [Text](../visualize/workbooks-text-visualizations.md)
* [Grafici](../visualize/workbooks-chart-visualizations.md)
* [Griglie](../visualize/workbooks-grid-visualizations.md)
* [Riquadri](../visualize/workbooks-tile-visualizations.md)
* [Trees](../visualize/workbooks-tree-visualizations.md)
* [Grafici](../visualize/workbooks-graph-visualizations.md)
* [Barra composita](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Esempio di visualizzazioni di cartelle di lavoro" border="false" lightbox="./media/workbooks-overview/visualizations.png":::

## <a name="getting-started"></a>Introduzione

Per esplorare l'esperienza delle cartelle di lavoro, passare prima al servizio monitoraggio di Azure. Questa operazione può essere eseguita digitando **monitoraggio** nella casella cerca della portale di Azure.

Selezionare quindi **cartelle di lavoro**.

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Screenshot del pulsante cartelle di lavoro evidenziato in una casella rossa" border="false":::

### <a name="gallery"></a>Raccolta

La raccolta rende più semplice organizzare, ordinare e gestire cartelle di lavoro di tutti i tipi.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Screenshot della raccolta nella scheda tutti." lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Schede della raccolta

Nella raccolta sono disponibili quattro schede che consentono di organizzare i tipi di cartelle di lavoro.

| Scheda              | Descrizione                                       |
|------------------|---------------------------------------------------|
| Tutti | Mostra i primi quattro elementi per ogni tipo di cartella di lavoro, modelli pubblici e modelli personali. Le cartelle di lavoro vengono ordinate in base alla data modificata, quindi verranno visualizzate le otto cartelle di lavoro modificate più recenti.|
| Workbooks | Mostra l'elenco di tutte le cartelle di lavoro disponibili create o condivise con l'utente. |
| Modelli pubblici | Mostra l'elenco di tutti i modelli di cartella di lavoro pronti per l'uso e di avvio disponibili pubblicati da Microsoft. Raggruppate per categoria. |
| Modelli personali | Mostra l'elenco di tutti i modelli di cartella di lavoro distribuiti disponibili creati o condivisi con l'utente. Raggruppate per categoria. |

#### <a name="features"></a>Funzionalità

* In ogni scheda è presente una griglia con informazioni sulle cartelle di lavoro. Include la descrizione, la data dell'Ultima modifica, i tag, la sottoscrizione, il gruppo di risorse, l'area e lo stato condiviso. È anche possibile ordinare le cartelle di lavoro in base a queste informazioni.
* Filtrare in base a gruppo di risorse, sottoscrizioni, nome cartella di lavoro/nome modello o categoria modello.
* Selezionare più cartelle di lavoro da eliminare o eliminare in blocco.
* Ogni cartella di lavoro dispone di un menu di scelta rapida (puntini di sospensione/tre punti alla fine). verrà aperto un elenco di azioni rapide.
    * Visualizzare la scheda delle risorse della cartella di lavoro di accesso alle risorse per visualizzare l'ID risorsa della cartella di lavoro, aggiungere tag, gestire i blocchi e così via.
    * Eliminare o rinominare la cartella di lavoro.
    * Aggiungere la cartella di lavoro al dashboard.

### <a name="workbooks-versus-workbook-templates"></a>Cartelle di lavoro e modelli di cartella di lavoro

È possibile visualizzare una _cartella di lavoro_ in verde e un numero di _modelli di cartella di lavoro_ in viola. I modelli vengono usati come report curati progettati per un riutilizzo flessibile da parte di più utenti e team. L'apertura di un modello consente di creare una cartella di lavoro temporanea popolata con il contenuto del modello.

È possibile modificare i parametri della cartella di lavoro basata su modelli ed eseguire l'analisi senza temere che si verifichi un'esperienza di Reporting futura per i colleghi. Se si apre un modello, si apportano alcune modifiche e quindi si seleziona l'icona Salva, il modello verrà salvato come cartella di lavoro che verrebbe visualizzata in verde lasciando invariato il modello originale.

Dietro le quinte, i modelli differiscono anche dalle cartelle di lavoro salvate. Il salvataggio di una cartella di lavoro crea una risorsa Azure Resource Manager associata, mentre la cartella di lavoro temporanea creata quando si apre solo un modello non dispone di una risorsa univoca associata. Per altre informazioni sulla gestione del controllo di accesso nelle cartelle di lavoro, vedere l'articolo relativo al [controllo di accesso delle cartelle di lavoro](../visualize/workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Esplorazione di un modello di cartella di lavoro

Selezionare **analisi degli errori dell'applicazione** per visualizzare uno dei modelli di cartella di lavoro delle applicazioni predefinite.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Screenshot del modello di analisi degli errori dell'applicazione" border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Come indicato in precedenza, l'apertura del modello consente di creare una cartella di lavoro temporanea per consentire l'interazione con. Per impostazione predefinita, la cartella di lavoro viene aperta in modalità di lettura che visualizza solo le informazioni per l'esperienza di analisi desiderata creata dall'autore del modello originale.

Nel caso di questa particolare cartella di lavoro, l'esperienza è interattiva. È possibile modificare la sottoscrizione, le app di destinazione e l'intervallo di tempo dei dati che si desidera visualizzare. Una volta effettuate le selezioni, la griglia delle richieste HTTP è interattiva, in modo che la selezione di una singola riga modifichi i dati di cui viene eseguito il rendering nei due grafici nella parte inferiore del report.

### <a name="editing-mode"></a>Modalità di modifica

Per comprendere il modo in cui il modello di cartella di lavoro viene combinato, è necessario passare alla modalità di modifica selezionando **modifica**.

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Screenshot del pulsante modifica nelle cartelle di lavoro di." border="false" :::

Dopo aver impostato la modalità di modifica, si noterà che un numero di caselle di **modifica** viene visualizzato a destra corrispondente a ogni singolo aspetto della cartella di lavoro.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Screenshot del pulsante modifica" border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Se si seleziona il pulsante modifica immediatamente sotto la griglia dei dati della richiesta, è possibile osservare che questa parte della cartella di lavoro è costituita da una query kusto sui dati di una risorsa Application Insights.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Screenshot della query kusto sottostante" border="false" lightbox="./media/workbooks-overview/kusto.png":::


Facendo clic sugli altri pulsanti di **modifica** a destra si riveleranno alcuni componenti di base che compongono cartelle di lavoro quali [caselle di testo](../visualize/workbooks-text-visualizations.md)basate su Markdown, elementi dell'interfaccia utente per la [selezione dei parametri](../visualize/workbooks-parameters.md) e altri [tipi di grafico/visualizzazione](#visualizations).

Esplorare i modelli predefiniti in modalità di modifica e quindi modificarli in base alle proprie esigenze e salvare la cartella di lavoro personalizzata è un modo eccellente per iniziare a conoscere le funzionalità possibili con le cartelle di lavoro di monitoraggio di Azure.

## <a name="pinning-visualizations"></a>Aggiunta di visualizzazioni

I passaggi relativi a testo, query e metriche in una cartella di lavoro possono essere aggiunti usando il pulsante Aggiungi su tali elementi quando la cartella di lavoro è in modalità PIN oppure se l'autore della cartella di lavoro ha abilitato le impostazioni per tale elemento per rendere visibile l'icona del PIN.

Per accedere alla modalità PIN, fare clic su **modifica** per passare alla modalità di modifica e selezionare l'icona del pin blu nella barra superiore. Una singola icona PIN verrà visualizzata sopra ogni casella di *modifica* della parte della cartella di lavoro corrispondente sul lato destro dello schermo.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Screenshot dell'esperienza del PIN." border="false":::

> [!NOTE]
> Lo stato della cartella di lavoro viene salvato al momento del PIN e le cartelle di lavoro aggiunte in un dashboard non vengono aggiornate se la cartella di lavoro sottostante viene modificata. Per aggiornare una parte della cartella di lavoro bloccata, è necessario eliminare e aggiungere nuovamente tale parte.

## <a name="dashboard-time-ranges"></a>Intervalli di tempo del dashboard

Le parti della query della cartella di lavoro bloccata rispetteranno l'intervallo di tempo del dashboard se l'elemento bloccato è configurato per l'utilizzo di un parametro di *intervallo di tempo* . Il valore dell'intervallo di tempo del dashboard verrà usato come valore del parametro dell'intervallo di tempo e qualsiasi modifica dell'intervallo di tempo del dashboard provocherà l'aggiornamento dell'elemento bloccato. Se una parte bloccata usa l'intervallo di tempo del dashboard, verrà visualizzato il sottotitolo dell'aggiornamento della parte bloccata per visualizzare l'intervallo di tempo del dashboard ogni volta che viene modificato l'intervallo di tempo.

Inoltre, le parti della cartella di lavoro bloccate che usano un parametro di intervallo di tempo vengono aggiornate automaticamente a una velocità determinata dall'intervallo di tempo del dashboard. L'ultima esecuzione della query verrà visualizzata nel sottotitolo della parte bloccata.

Se un passaggio aggiunto ha un intervallo di tempo impostato in modo esplicito (non usa un parametro di intervallo di tempo), l'intervallo di tempo verrà sempre usato per il dashboard, indipendentemente dalle impostazioni del dashboard. Il sottotitolo della parte bloccata non visualizzerà l'intervallo di tempo del dashboard e la query non viene aggiornata automaticamente nel dashboard. Il sottotitolo mostrerà l'ultima esecuzione della query.

> [!NOTE]
> Le query che utilizzano l'origine dati di *merge* non sono attualmente supportate durante l'aggiunta ai dashboard.

## <a name="sharing-workbook-templates"></a>Condivisione di modelli di cartella di lavoro

Una volta avviata la creazione di modelli di cartella di lavoro personalizzati, può essere utile condividerla con la community più ampia. Per altre informazioni e per esplorare altri modelli che non fanno parte della visualizzazione raccolta predefinita di monitoraggio di Azure, visitare il [repository GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Per esplorare le cartelle di lavoro esistenti, visitare la [libreria delle cartelle di lavoro](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) in GitHub.

## <a name="next-step"></a>Passaggio successivo

* Per [iniziare ad](#visualizations) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](../visualize/workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.