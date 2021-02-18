---
title: Azioni di collegamento delle cartelle di lavoro di monitoraggio di Azure
description: Come usare le azioni di collegamento nelle cartelle di lavoro di monitoraggio di Azure
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616037"
---
# <a name="link-actions"></a>Azioni di collegamento

È possibile accedere alle azioni di collegamento tramite i passaggi dei collegamenti alla cartella di lavoro o le impostazioni delle colonne di [griglie](../visualize/workbooks-grid-visualizations.md), [titoli](../visualize/workbooks-tile-visualizations.md)o [grafici](../visualize/workbooks-graph-visualizations.md).

## <a name="general-link-actions"></a>Azioni di collegamento generali

| Azione di collegamento | Azione su clic |
|:------------- |:-------------|
| `Generic Details` | Mostra i valori di riga in una visualizzazione del contesto della griglia delle proprietà. |
| `Cell Details` | Mostra il valore della cella in una visualizzazione del contesto della griglia delle proprietà. Utile quando la cella contiene un tipo dinamico con informazioni, ad esempio JSON con proprietà della richiesta come la posizione, l'istanza del ruolo e così via. |
| `Url` | Il valore della cella dovrebbe essere un URL http valido e la cella sarà un collegamento che apre tale URL in una nuova scheda.|

## <a name="application-insights"></a>Application Insights

| Azione di collegamento | Azione su clic |
|:------------- |:-------------|
| `Custom Event Details` | Apre il Application Insights dettagli della ricerca con l'ID evento personalizzato ( `itemId` ) nella cella. |
| `* Details` | Simile ai dettagli dell'evento personalizzato, ad eccezione delle dipendenze, delle eccezioni, delle visualizzazioni di pagina, delle richieste e delle tracce. |
| `Custom Event User Flows` | Apre il Application Insights Flussi utente esperienza trasformata tramite pivot per il nome dell'evento personalizzato nella cella. |
| `* User Flows` | Simile a Flussi utente di eventi personalizzati ad eccezione di eccezioni, visualizzazioni di pagina e richieste. |
| `User Timeline` | Apre la sequenza temporale dell'utente con l'ID utente ( `user_Id` ) nella cella. |
| `Session Timeline` | Apre l'esperienza di ricerca Application Insights per il valore nella cella, ad esempio ricercare il testo "ABC", dove ABC è il valore nella cella. |

`*` indica un carattere jolly per la tabella precedente

## <a name="azure-resource"></a>Risorsa di Azure

| Azione di collegamento | Azione su clic |
|:------------- |:-------------|
| `ARM Deployment` | Distribuire un modello di Azure Resource Manager.  Quando l'elemento è selezionato, vengono visualizzati campi aggiuntivi per consentire all'autore di configurare il modello di Azure Resource Manager da aprire, i parametri per il modello e così via. [vedere Azure Resource Manager impostazioni del collegamento di distribuzione](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Crea una regola di avviso per una risorsa.  |
| `Custom View` | Apre una visualizzazione personalizzata. Quando l'elemento è selezionato, vengono visualizzati campi aggiuntivi per consentire all'autore di configurare l'estensione della visualizzazione, il nome della visualizzazione e tutti i parametri utilizzati per aprire la visualizzazione. [Vedere visualizzazione personalizzata](#custom-view-link-settings). |
| `Metrics` | Apre una visualizzazione metrica.  |
| `Resource overview` | Aprire la visualizzazione della risorsa nel portale in base al valore di ID risorsa nella cella. L'autore può anche impostare facoltativamente un `submenu` valore che consente di aprire una voce di menu specifica nella visualizzazione risorse. |
| `Workbook (template)` | Aprire un modello di cartella di lavoro.  Quando l'elemento è selezionato, vengono visualizzati campi aggiuntivi per consentire all'autore di configurare il modello da aprire e così via.  |

## <a name="link-settings"></a>Impostazioni collegamento

Quando si usa il renderer di collegamento, sono disponibili le impostazioni seguenti:

![Screenshot delle impostazioni di collegamento](./media/workbooks-link-actions/link-settings.png)

| Impostazione | Spiegazione |
|:------------- |:-------------|
| `View to open` | Consente all'autore di selezionare una delle azioni enumerate in precedenza. |
| `Menu item` | Se è selezionata l'opzione "panoramica risorse", questa è la voce di menu nella panoramica della risorsa da aprire. Questa operazione può essere usata per aprire avvisi o log attività anziché "Overview" per la risorsa. I valori delle voci di menu sono diversi per ogni Azure `Resourcetype` .|
| `Link label` | Se specificato, questo valore verrà visualizzato nella colonna della griglia. Se questo valore non è specificato, verrà visualizzato il valore della cella. Se si vuole che venga visualizzato un altro valore, ad esempio mappa termica o Icon, non usare il `Link` renderer, invece usare il renderer appropriato e selezionare l' `Make this item a link` opzione. |
| `Open link in Context Blade` | Se specificato, il collegamento verrà aperto come visualizzazione "context" popup sul lato destro della finestra anziché aprirlo come visualizzazione completa. |

Quando si usa l' `Make this item a link` opzione, sono disponibili le impostazioni seguenti:

| Impostazione | Spiegazione |
|:------------- |:-------------|
| `Link value comes from` | Quando si visualizza una cella come renderer con un collegamento, questo campo specifica il punto in cui deriva il valore "link" da usare nel collegamento, consentendo all'autore di effettuare la selezione da un elenco a discesa delle altre colonne nella griglia. Ad esempio, la cella può essere un valore mappa termica, ma si vuole che il collegamento apra la panoramica delle risorse per l'ID risorsa nella riga. In tal caso, è necessario impostare il valore del collegamento in modo che provenga dal `Resource Id` campo.
| `View to open` | come sopra. |
| `Menu item` | come sopra. |
| `Open link in Context Blade` | come sopra. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Impostazioni del collegamento di distribuzione Azure Resource Manager

Se il tipo di collegamento selezionato è `ARM Deployment` l'autore deve specificare impostazioni aggiuntive per aprire una distribuzione di Azure Resource Manager. Sono disponibili due schede principali per le configurazioni.

### <a name="template-settings"></a>Impostazioni modello

Questa sezione definisce la provenienza del modello e i parametri usati per eseguire la distribuzione di Azure Resource Manager.

| Source (Sorgente) | Spiegazione |
|:------------- |:-------------|
|`Resource group id comes from` | L'ID risorsa viene usato per gestire le risorse distribuite. La sottoscrizione viene utilizzata per gestire le risorse distribuite e i costi. I gruppi di risorse vengono usati come cartelle per organizzare e gestire tutte le risorse. Se questo valore non è specificato, la distribuzione avrà esito negativo. Consente di selezionare le `Cell` `Column` origini dei collegamenti,, `Static Value` o `Parameter` . [](#link-sources)|
|`ARM template URI from` | URI del modello di Azure Resource Manager stesso. L'URI del modello deve essere accessibile agli utenti che distribuiscono il modello. Consente di selezionare le `Cell` `Column` origini dei collegamenti,, `Parameter` o `Static Value` . [](#link-sources) Per iniziare, vedere i modelli di avvio [rapido](https://azure.microsoft.com/resources/templates/).|
|`ARM Template Parameters` | Questa sezione definisce i parametri del modello usati per l'URI del modello definito in precedenza. Questi parametri verranno usati per distribuire il modello nella pagina di esecuzione. La griglia contiene un pulsante della barra degli strumenti Espandi per inserire i parametri usando i nomi definiti nell'URI del modello e impostarli su valori statici vuoti. Questa opzione può essere utilizzata solo quando non sono presenti parametri nella griglia e l'URI del modello è stato impostato. La sezione inferiore è un'anteprima dell'aspetto dell'output del parametro. Selezionare Aggiorna per aggiornare l'anteprima con le modifiche correnti. I parametri sono in genere valori, mentre i riferimenti sono elementi che potrebbero puntare a segreti dell'insieme di credenziali delle chiavi a cui l'utente ha accesso. <br/><br/> **Limitazione del pannello Visualizzatore modelli** : non esegue correttamente il rendering dei parametri di riferimento e viene visualizzato come valore null, quindi gli utenti non saranno in grado di distribuire correttamente i parametri di riferimento dalla scheda Visualizzatore modello.|

![Screenshot delle impostazioni del modello di Azure Resource Manager](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>Impostazioni UX

Questa sezione Configura gli elementi che verranno visualizzati dagli utenti prima di eseguire la distribuzione di Azure Resource Manager.

| Source (Sorgente) | Spiegazione |
|:------------- |:-------------|
|`Title from` | Titolo usato nella visualizzazione di esecuzione. Consente di selezionare le `Cell` `Column` origini dei collegamenti,, `Parameter` o `Static Value` . [](#link-sources)|
|`Description from` | Si tratta del testo Markdown usato per fornire una descrizione utile agli utenti quando vogliono distribuire il modello. Consente di selezionare le `Cell` `Column` origini dei collegamenti,, `Parameter` o `Static Value` . [](#link-sources) <br/><br/> **Nota:** Se `Static Value` è selezionata, verrà visualizzata una casella di testo a più righe. In questa casella di testo è possibile risolvere i parametri usando `{paramName}` . Inoltre, è possibile considerare le colonne come parametri accodando `_column` dopo il nome della colonna, ad esempio `{columnName_column}` . Nell'immagine di esempio riportata di seguito, è possibile fare riferimento alla colonna `VMName` scrivendo `{VMName_column}` . Il valore dopo i due punti è il [formattatore del parametro](../visualize/workbooks-parameters.md#parameter-options), in questo caso è `value` .|
|`Run button text from` | Etichetta utilizzata sul pulsante Esegui (Esegui) per distribuire il modello di Azure Resource Manager. Questo è il modo in cui gli utenti selezioneranno per iniziare a distribuire il modello di Azure Resource Manager.|

![Screenshot delle impostazioni di Azure Resource Manager UX](./media/workbooks-link-actions/ux-settings.png)

Una volta impostate queste configurazioni, quando gli utenti selezionano il collegamento, verrà aperta la visualizzazione con l'esperienza utente descritta nelle impostazioni di UX. Se l'utente seleziona `Run button text from` questa operazione, verrà distribuito un modello di Azure Resource Manager usando i valori delle [impostazioni del modello](#template-settings). Visualizza modello consente di aprire la scheda Visualizzatore modelli per consentire all'utente di esaminare il modello e i parametri prima della distribuzione.

![Screenshot della visualizzazione Azure Resource Manager esecuzione](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Impostazioni di collegamento visualizzazione personalizzata

Usare questa finestra per aprire visualizzazioni personalizzate nell'portale di Azure. Verificare tutte le impostazioni e la configurazione. I valori non corretti provocheranno errori nel portale o non riusciranno ad aprire correttamente le visualizzazioni. Esistono due modi per configurare le impostazioni tramite `Form` o `URL` .

> [!NOTE]
> Non è possibile aprire viste con un menu in una scheda di contesto. Se una vista con un menu è configurata per l'apertura in una scheda del contesto, non verrà visualizzata alcuna scheda di contesto quando si seleziona il collegamento.

### <a name="form"></a>Form

| Source (Sorgente) | Spiegazione |
|:------------- |:-------------|
|`Extension name` | Nome dell'estensione che ospita il nome della vista.|
|`View name` | Nome della visualizzazione da aprire.|

#### <a name="view-inputs"></a>Visualizza input

Sono disponibili due tipi di input, griglie e JSON. Usare `Grid` per gli input della scheda chiave e valore semplici oppure selezionare `JSON` per specificare un input JSON annidato.

- Pannello Grid
    - `Parameter Name`: Nome del parametro di input di visualizzazione.
    - `Parameter Comes From`: Dove deve provenire il valore del parametro di visualizzazione. Consente di selezionare le `Cell` `Column` origini dei collegamenti,, `Parameter` o `Static Value` . [](#link-sources)
    > [!NOTE]
    > Se `Static Value` è selezionata, i parametri possono essere risolti utilizzando il collegamento tra parentesi quadre `{paramName}` nella casella di testo. È possibile considerare le colonne come colonne di parametri aggiungendo `_column` dopo il nome della colonna `{columnName_column}` , ad esempio.

    - `Parameter Value`: a seconda `Parameter Comes From` di, si tratta di un elenco a discesa di parametri disponibili, colonne o un valore statico.

    ![Screenshot dell'impostazione modifica colonna Mostra impostazioni visualizzazione personalizzata dal modulo.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Specificare l'input della scheda in un formato JSON nell'editor. Analogamente alla `Grid` modalità, è possibile fare riferimento ai parametri e alle colonne utilizzando `{paramName}` per i parametri e per le `{columnName_column}` colonne. Selezionando `Show JSON Sample` , viene visualizzato l'output previsto di tutti i parametri risolti e l'utente delle colonne per l'input di visualizzazione.

    ![Screenshot che mostra le impostazioni di visualizzazione personalizzata aperte con l'input di visualizzazione in JSON.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

Incollare un URL del portale contenente l'estensione, il nome della visualizzazione e gli eventuali input necessari per aprire la visualizzazione. Dopo aver selezionato `Initialize Settings` , verrà popolato `Form` per l'autore per aggiungere, modificare o rimuovere gli input della vista.

![Screenshot che mostra l'impostazione della colonna Mostra impostazioni visualizzazione personalizzata dall'URL. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Impostazioni collegamento cartella di lavoro (modello)

Se il tipo di collegamento selezionato è `Workbook (Template)` , l'autore deve specificare impostazioni aggiuntive per aprire il modello di cartella di lavoro corretto. Nelle impostazioni seguenti sono disponibili le opzioni relative alla modalità di individuazione del valore appropriato per ciascuna impostazione nella griglia.

| Impostazione | Spiegazione |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Si tratta dell'ID risorsa della risorsa di Azure che possiede la cartella di lavoro. Si tratta in genere di una risorsa Application Insights o di un'area di lavoro Log Analytics. All'interno di monitoraggio di Azure, può essere anche la stringa letterale `"Azure Monitor"` . Quando la cartella di lavoro viene salvata, la cartella di lavoro sarà collegata. |
| `Workbook resources` | Matrice di ID risorsa di Azure che specifica la risorsa predefinita utilizzata nella cartella di lavoro. Ad esempio, se il modello da aprire Mostra le metriche della macchina virtuale, i valori sono gli ID delle risorse della macchina virtuale.  Molte volte, il proprietario e le risorse vengono impostati sulle stesse impostazioni. |
| `Template Id` | Consente di specificare l'ID del modello da aprire. Se si tratta di un modello della community della raccolta (il caso più comune), anteporre il percorso al modello `Community-` , ad esempio `Community-Workbooks/Performance/Apdex` per il `Workbooks/Performance/Apdex` modello. Se questo è un collegamento a una cartella di lavoro o a un modello salvato, è l'ID di risorsa di Azure completo di tale elemento. |
| `Workbook Type` | Consente di specificare il tipo di modello di cartella di lavoro da aprire. Nei casi più comuni si usa `Default` l' `Workbook` opzione o per usare il valore nella cartella di lavoro corrente. |
| `Gallery Type` | Consente di specificare il tipo di raccolta che verrà visualizzato nella visualizzazione "raccolta" del modello che si apre. Nei casi più comuni si usa `Default` l' `Workbook` opzione o per usare il valore nella cartella di lavoro corrente. |
|`Location comes from` | Il campo percorso deve essere specificato se si apre una risorsa cartella di lavoro specifica. Se il percorso non è specificato, la ricerca del contenuto della cartella di lavoro è molto più lenta. Se si conosce il percorso, specificarlo. Se non si conosce il percorso o si apre un modello senza percorso specifico, lasciare questo campo come "predefinito".|
|`Pass specific parameters to template` | Selezionare questa selezione per passare parametri specifici al modello. Se questa opzione è selezionata, al modello vengono passati solo i parametri specificati e tutti i parametri nella cartella di lavoro corrente vengono passati al modello e, in tal caso, i *nomi* dei parametri devono essere gli stessi in entrambe le cartelle di lavoro per il corretto funzionamento di questo valore di parametro.|
|`Workbook Template Parameters` | Questa sezione definisce i parametri passati al modello di destinazione. Il nome deve corrispondere al nome del parametro nel modello di destinazione. Selezionare valore da `Cell` , `Column` , `Parameter` e `Static Value` . Il nome e il valore non devono essere vuoti per passare il parametro al modello di destinazione.|

Per ognuna delle impostazioni precedenti, l'autore deve selezionare il punto da cui derivano i valori nella cartella di lavoro collegata. Vedere [origini collegamenti](#link-sources)

Quando si apre il collegamento alla cartella di lavoro, alla nuova visualizzazione della cartella di lavoro vengono passati tutti i valori configurati dalle impostazioni precedenti.

![Screenshot delle impostazioni del collegamento alla cartella di lavoro](./media/workbooks-link-actions/workbook-link-settings.png)

![Screenshot delle impostazioni dei parametri del modello di cartella di lavoro](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Origini collegamento

| Source (Sorgente) | Spiegazione |
|:------------- |:-------------|
| `Cell` | Verrà utilizzato il valore della cella nella griglia come valore del collegamento |
| `Column` | Quando questa opzione è selezionata, viene visualizzato un altro campo che consente all'autore di selezionare un'altra colonna nella griglia.  Il valore della colonna per la riga verrà utilizzato nel valore del collegamento. Questa operazione viene in genere usata per consentire a ogni riga di una griglia di aprire un modello diverso, impostando `Template Id` il campo su `column` o aprendo lo stesso modello di cartella di lavoro per le diverse risorse, se il `Workbook resources` campo è impostato su una colonna che contiene un ID di risorsa di Azure |
| `Parameter` | Quando questa opzione è selezionata, viene visualizzato un altro campo che consente all'autore di selezionare un parametro. Il valore di tale parametro verrà usato per il valore quando si fa clic sul collegamento |
| `Static value` | Quando questa opzione è selezionata, viene visualizzato un altro campo che consente all'autore di digitare un valore statico che verrà usato nella cartella di lavoro collegata. Questa operazione viene in genere utilizzata quando tutte le righe della griglia utilizzeranno lo stesso valore per un campo. |
| `Step` | Usare il valore impostato nel passaggio corrente della cartella di lavoro. Si tratta di una procedura comune nei passaggi della query e della metrica per impostare le risorse della cartella di lavoro nella cartella di lavoro collegata a quelle usate *nel passaggio query/metrica*, non nella cartella di lavoro corrente |
| `Workbook` | Usare il valore impostato nella cartella di lavoro corrente. |
| `Default` | Usare il valore predefinito che verrebbe usato se non è stato specificato alcun valore. Si tratta di una situazione comune per il tipo di raccolta, in cui la raccolta predefinita verrà impostata dal tipo della risorsa proprietario |

## <a name="next-steps"></a>Passaggi successivi

- [Controllare](../visualize/workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
- Informazioni su come usare i [gruppi nelle cartelle di lavoro di](../visualize/workbooks-groups.md).