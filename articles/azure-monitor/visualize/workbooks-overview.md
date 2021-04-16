---
title: Panoramica di Cartelle di lavoro di Monitoraggio di Azure
description: Informazioni su come le cartelle di lavoro offrono un'area di disegno flessibile per l'analisi dei dati e la creazione di report visivi all'interno del portale di Azure.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3d75d7605ba082aac84973aef247de79d55b4c9c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482773"
---
# <a name="azure-monitor-workbooks"></a>Monitoraggio di Azure cartelle di lavoro

Le cartelle di lavoro offrono un canvas flessibile per l'analisi dei dati e la creazione di report visivi avanzati nel portale di Azure. Consentono di sfruttare più origini dati in tutto Azure e di combinarle in esperienze interattive unificate.

Ecco una procedura dettagliata video sulla creazione di cartelle di lavoro.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Origini dati

Le cartelle di lavoro possono eseguire query su dati di origini diverse in Azure. Gli autori delle cartelle di lavoro possono trasformare questi dati per offrire informazioni dettagliate sulla disponibilità, le prestazioni, l'uso e l'integrità complessiva dei componenti sottostanti. Ad esempio, l'analisi dei log delle prestazioni dalle macchine virtuali per identificare istanze di CPU o memoria insufficiente e la visualizzazione dei risultati come griglia in un report interattivo.
  
Ma il vero valore delle cartelle di lavoro è la capacità di combinare dati provenienti da origini diverse in un unico report. Ciò consente la creazione di viste o join di risorse composite tra le risorse, consentendo dati e informazioni dettagliate più dettagliate che altrimenti sarebbe impossibile.

Le cartelle di lavoro sono attualmente compatibili con le origini dati seguenti:

* [Log](../visualize/workbooks-data-sources.md#logs)
* [Metriche](../visualize/workbooks-data-sources.md#metrics)
* [Diagramma delle risorse di Azure](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Avvisi (anteprima)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Integrità del carico di lavoro](../visualize/workbooks-data-sources.md#workload-health)
* [Integrità risorse di Azure](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Esplora dati di Azure](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualizzazioni

Le cartelle di lavoro offrono un set di funzionalità avanzate per la visualizzazione dei dati. Per esempi dettagliati di ogni tipo di visualizzazione, è possibile consultare i collegamenti seguenti:

* [Text](../visualize/workbooks-text-visualizations.md)
* [Grafici](../visualize/workbooks-chart-visualizations.md)
* [Griglie](../visualize/workbooks-grid-visualizations.md)
* [Riquadri](../visualize/workbooks-tile-visualizations.md)
* [Trees](../visualize/workbooks-tree-visualizations.md)
* [Grafici](../visualize/workbooks-graph-visualizations.md)
* [Barra composita](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Esempio di visualizzazioni della cartella di lavoro." border="false" lightbox="./media/workbooks-overview/visualizations.png":::

### <a name="pinning-visualizations"></a>Aggiunta di visualizzazioni

I passaggi di testo, query e metriche in una cartella di lavoro possono essere aggiunti usando il pulsante Aggiungi su tali elementi mentre la cartella di lavoro è in modalità di aggiunta oppure se l'autore della cartella di lavoro ha abilitato le impostazioni per tale elemento per rendere visibile l'icona aggiungi.

Per accedere alla modalità di aggiunta, fare **clic su** Modifica per accedere alla modalità di modifica e selezionare l'icona a forma di puntina blu nella barra superiore. Verrà quindi visualizzata una singola icona a  forma di puntina sopra la casella Edit (Modifica) di ogni parte della cartella di lavoro corrispondente sul lato destro dello schermo.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Screenshot dell'esperienza di aggiunta." border="false":::

> [!NOTE]
> Lo stato della cartella di lavoro viene salvato al momento dell'aggiunta e le cartelle di lavoro aggiunte in un dashboard non verranno aggiornate se la cartella di lavoro sottostante viene modificata. Per aggiornare una parte della cartella di lavoro bloccata, è necessario eliminare e aggiungere nuovamente tale parte.

## <a name="getting-started"></a>Introduzione

Per esplorare l'esperienza delle cartelle di lavoro, passare prima al Monitoraggio di Azure esistente. Questa operazione può essere eseguita **digitando Monitoraggio** nella casella di ricerca nella portale di Azure.

Selezionare quindi **Cartelle di lavoro**.

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Screenshot del pulsante Cartelle di lavoro evidenziato in una casella rossa." border="false":::

### <a name="gallery"></a>Raccolta

La raccolta semplifica l'organizzazione, l'ordinamento e la gestione delle cartelle di lavoro di tutti i tipi.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Screenshot della raccolta nella scheda Tutti." lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Schede della raccolta

La raccolta contiene quattro schede che consentono di organizzare i tipi di cartella di lavoro.

| Scheda              | Descrizione                                       |
|------------------|---------------------------------------------------|
| Tutti | Mostra i primi quattro elementi per ogni tipo: cartelle di lavoro, modelli pubblici e modelli personali. Le cartelle di lavoro vengono ordinate in base alla data di modifica, quindi verranno visualizzate le otto cartelle di lavoro modificate più recenti.|
| Workbooks | Visualizza l'elenco di tutte le cartelle di lavoro disponibili create o condivise con l'utente. |
| Modelli pubblici | Mostra l'elenco di tutti i modelli di cartella di lavoro funzionali disponibili pronti per l'uso pubblicati da Microsoft. Raggruppati per categoria. |
| Modelli personali | Visualizza l'elenco di tutti i modelli di cartella di lavoro distribuiti disponibili creati o condivisi con l'utente. Raggruppati per categoria. |

#### <a name="features"></a>Funzionalità

* In ogni scheda è presente una griglia con informazioni sulle cartelle di lavoro. Include descrizione, data dell'ultima modifica, tag, sottoscrizione, gruppo di risorse, area e stato condiviso. È anche possibile ordinare le cartelle di lavoro in base a queste informazioni.
* Filtrare per gruppo di risorse, sottoscrizioni, nome cartella di lavoro/modello o categoria di modelli.
* Selezionare più cartelle di lavoro da eliminare o eliminare in blocco.
* Ogni cartella di lavoro ha un menu di scelta rapida (puntini di sospensione/tre punti alla fine), selezionandolo verrà aperto un elenco di azioni rapide.
    * Visualizzare la risorsa: accedere alla scheda della risorsa della cartella di lavoro per visualizzare l'ID risorsa della cartella di lavoro, aggiungere tag, gestire i blocchi e così via.
    * Eliminare o rinominare una cartella di lavoro.
    * Aggiungere la cartella di lavoro al dashboard.

### <a name="workbooks-versus-workbook-templates"></a>Cartelle di lavoro e modelli di cartella di lavoro

È possibile visualizzare una cartella _di lavoro_ in verde e alcuni modelli di cartella _di lavoro_ in viola. I modelli fungono da report curati progettati per un riutilizzo flessibile da parte di più utenti e team. L'apertura di un modello crea una cartella di lavoro temporanea popolata con il contenuto del modello.

È possibile modificare i parametri della cartella di lavoro basata su modello ed eseguire l'analisi senza temere di interrompere l'esperienza di creazione di report futura per i colleghi. Se si apre un modello, apportare alcune modifiche e quindi selezionare l'icona di salvataggio in cui si salverà il modello come cartella di lavoro, che verrà visualizzata in verde lasciando invariato il modello originale.

Anche i modelli differiscono dalle cartelle di lavoro salvate. Il salvataggio di una cartella di lavoro crea una risorsa Azure Resource Manager, mentre la cartella di lavoro temporanea creata quando si apre un modello non ha una risorsa univoca associata. Per altre informazioni su come viene gestito il controllo di accesso nelle cartelle di lavoro, vedere l'articolo controllo di accesso delle cartelle [di lavoro](../visualize/workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Esplorazione di un modello di cartella di lavoro

Selezionare **Analisi degli errori dell'applicazione** per visualizzare uno dei modelli di cartella di lavoro dell'applicazione predefiniti.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Screenshot del modello di analisi degli errori dell'applicazione." border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Come indicato in precedenza, l'apertura del modello crea una cartella di lavoro temporanea con cui poter interagire. Per impostazione predefinita, la cartella di lavoro viene aperta in modalità di lettura che visualizza solo le informazioni per l'esperienza di analisi prevista creata dall'autore del modello originale.

Nel caso di questa particolare cartella di lavoro, l'esperienza è interattiva. È possibile modificare la sottoscrizione, le app di destinazione e l'intervallo di tempo dei dati da visualizzare. Dopo aver effettuato queste selezioni, anche la griglia delle richieste HTTP è interattiva, in base alla quale la selezione di una singola riga modificherà i dati di cui viene eseguito il rendering nei due grafici nella parte inferiore del report.

### <a name="editing-mode"></a>Modalità di modifica

Per comprendere come viene riunito questo modello di cartella di lavoro, è necessario passare alla modalità di modifica selezionando **Modifica**.

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Screenshot del pulsante Modifica nelle cartelle di lavoro." border="false" :::

Una volta passata alla modalità di modifica, si noterà che a destra vengono visualizzate una serie di caselle di modifica corrispondenti a ogni singolo aspetto della cartella di lavoro. 

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Screenshot del pulsante Modifica." border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Se si seleziona il pulsante Modifica immediatamente sotto la griglia dei dati della richiesta, è possibile vedere che questa parte della cartella di lavoro è costituita da una query Kusto sui dati di una risorsa Application Insights dati.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Screenshot della query Kusto sottostante." border="false" lightbox="./media/workbooks-overview/kusto.png":::

Se si  selezionano gli altri pulsanti Modifica a destra, verranno visualizzati alcuni componenti principali [](../visualize/workbooks-parameters.md) che costituiscono cartelle di lavoro, ad esempio caselle di testo basate su markdown, [](../visualize/workbooks-text-visualizations.md)elementi dell'interfaccia utente di selezione dei parametri e altri tipi di [grafico/visualizzazione.](#visualizations)

Esplorare i modelli predefiniti in modalità di modifica e quindi modificarli in base alle proprie esigenze e salvare la propria cartella di lavoro personalizzata è un ottimo modo per iniziare a scoprire cosa è possibile con le cartelle di lavoro Monitoraggio di Azure personalizzate.

## <a name="dashboard-time-ranges"></a>Intervalli di tempo del dashboard

Le parti di query della cartella di lavoro aggiunte rispetteranno l'intervallo di tempo del dashboard se l'elemento aggiunto è configurato per l'uso di un *parametro Intervallo di* tempo. Il valore dell'intervallo di tempo del dashboard verrà usato come valore del parametro dell'intervallo di tempo e qualsiasi modifica dell'intervallo di tempo del dashboard causerà l'aggiornamento dell'elemento aggiunto. Se una parte aggiunta usa l'intervallo di tempo del dashboard, verrà visualizzato il sottotitolo dell'aggiornamento della parte aggiunta per visualizzare l'intervallo di tempo del dashboard ogni volta che cambia l'intervallo di tempo.

Inoltre, le parti della cartella di lavoro aggiunte usando un parametro dell'intervallo di tempo verranno aggiornate automaticamente a una velocità determinata dall'intervallo di tempo del dashboard. L'ultima volta che la query è stata eseguita verrà visualizzata nel sottotitolo della parte bloccata.

Se un passaggio aggiunto ha un intervallo di tempo impostato in modo esplicito (non usa un parametro di intervallo di tempo), tale intervallo di tempo verrà sempre usato per il dashboard, indipendentemente dalle impostazioni del dashboard. Il sottotitolo della parte aggiunta non mostrerà l'intervallo di tempo del dashboard e la query non verrà aggiornata automaticamente nel dashboard. Il sottotitolo mostrerà l'ultima esecuzione della query.

> [!NOTE]
> Le query che *usano l'origine* dati merge non sono attualmente supportate quando si bloccano i dashboard.

## <a name="sharing-workbook-templates"></a>Condivisione di modelli di cartella di lavoro

Dopo aver avviato la creazione di modelli di cartella di lavoro personalizzati, è possibile condividerli con la community più ampia. Per altre informazioni ed esplorare altri modelli che non fanno parte della visualizzazione Monitoraggio di Azure predefinita della raccolta, visitare il [repository GitHub.](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md) Per esplorare le cartelle di lavoro esistenti, visitare la [libreria Workbook](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) su GitHub.

## <a name="next-step"></a>Passaggio successivo

* [Per altre informazioni](#visualizations) sulle cartelle di lavoro, vedere Molte opzioni di visualizzazione avanzate.
* [Controllare e](../visualize/workbooks-access-control.md) condividere l'accesso alle risorse della cartella di lavoro.