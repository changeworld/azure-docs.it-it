---
title: Spostare un'istanza in un'area di Azure diversa
titleSuffix: Azure Digital Twins
description: Informazioni su come spostare un'istanza Gemelli digitali di Azure da un'area di Azure a un'altra.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 62db56ac9791cea7d6f1a40f794241ed68fa90fa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483577"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Spostare un'Gemelli digitali di Azure in un'area di Azure diversa

Se è necessario spostare l'istanza di Gemelli digitali di Azure da un'area a un'altra, il processo corrente è quello di creare nuovamente le risorse nella nuova area e quindi eliminare le risorse originali. Al termine di questo processo, si lavora con una nuova istanza di Gemelli digitali di Azure identica alla prima, ad eccezione del percorso aggiornato.

Questo articolo fornisce indicazioni su come eseguire uno spostamento e una copia completi di tutti gli elementi necessari per fare in modo che la nuova istanza corrisponda all'originale.

Questo processo include i due passaggi seguenti:

1. Preparazione: scaricare i modelli, i gemelli e il grafo originali.
1. Spostamento: creare una nuova istanza Gemelli digitali di Azure in una nuova area.
1. Sposta: ripopolare la nuova Gemelli digitali di Azure istanza.
    - Caricare i modelli, i gemelli e il grafo originali.
    - Creare nuovamente endpoint e route.
    - Ricollegare le risorse connesse.
1. Pulire le risorse di origine: eliminare l'istanza originale.

## <a name="prerequisites"></a>Prerequisiti

Prima di provare a creare di nuovo l'istanza di Gemelli digitali di Azure, passare ai componenti dell'istanza originale per avere un'idea chiara di tutti i componenti che dovranno essere ri-creati.

Ecco alcune domande da considerare:

* Quali sono i *modelli* caricati nell'istanza? Quanti sono?
* Quali sono i *gemelli* nell'istanza? Quanti sono?
* Qual è la forma generale del *grafo* nell'istanza? Quante relazioni sono presenti?
* Quali *endpoint sono* presenti nell'istanza?
* Quali *route* sono presenti nell'istanza? Sono disponibili filtri?
* Dove si connette *l'istanza ad altri servizi di Azure?* Alcuni punti di integrazione comuni includono:

    - Griglia di eventi di Azure, Hub eventi di Azure o bus di servizio di Azure
    - Funzioni di Azure
    - App per la logica di Azure
    - Azure Time Series Insights
    - Mappe di Azure
    - Servizio Device Provisioning in hub IoT di Azure
* Quali altre *app personali o aziendali* si connettono all'istanza?

È possibile raccogliere queste informazioni usando le API portale di Azure [,](https://portal.azure.com) [Gemelli digitali di Azure](how-to-use-apis-sdks.md)e SDK , i comandi dell'interfaccia della riga di comando di [Gemelli digitali di Azure](how-to-use-cli.md)o [l'Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) seguente.

## <a name="prepare"></a>Preparazione

In questa sezione si preparerà a creare nuovamente l'istanza scaricando i modelli, i gemelli e il grafo originali dall'istanza originale. Questo articolo [](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) usa l Azure Digital Twins Explorer di esempio per questa attività.

>[!NOTE]
>Potrebbero essere già presenti file che contengono i modelli o il grafo nell'istanza. In questo caso, non è necessario scaricare di nuovo tutto, ma solo i componenti mancanti o gli elementi che potrebbero essere cambiati dopo il caricamento originale di questi file. Ad esempio, si potrebbero avere dispositivi gemelli che sono stati aggiornati con nuovi dati.

### <a name="limitations-of-azure-digital-twins-explorer"></a>Limitazioni dei Azure Digital Twins Explorer

[L Azure Digital Twins Explorer di esempio](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) è un esempio di app client che supporta una rappresentazione visiva del grafo e fornisce l'interazione visiva con l'istanza. Questo articolo illustra come usarlo per scaricare e ricaricare in un secondo momento modelli, gemelli e grafi.

Questo esempio non è uno strumento completo. Non è stata testata da stress e non è stata creata per gestire grafici di grandi dimensioni. Di conseguenza, tenere presenti le limitazioni di esempio seguenti:

* L'esempio è stato attualmente testato solo su dimensioni del grafo fino a 1.000 nodi e 2.000 relazioni.
* L'esempio non supporta i tentativi in caso di errori intermittenti.
* L'esempio non notificherà necessariamente all'utente se i dati caricati sono incompleti.
* L'esempio non gestisce gli errori risultanti da grafici molto grandi che superano le risorse disponibili, ad esempio la memoria.

Se l'esempio non è in grado di gestire le dimensioni del grafo, è possibile esportare e importare il grafo usando altri strumenti Gemelli digitali di Azure per sviluppatori:

* [Gemelli digitali di Azure comandi dell'interfaccia della riga di comando](how-to-use-cli.md)
* [Gemelli digitali di Azure API e SDK](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>Configurare l'Azure Digital Twins Explorer di distribuzione

Per procedere con Azure Digital Twins Explorer, scaricare prima il codice dell'applicazione di esempio e configurarlo per l'esecuzione nel computer.

Per ottenere l'esempio, passare [a Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Selezionare il **pulsante Sfoglia** codice sotto il titolo, che consente di accedere al repository GitHub per l'esempio. Selezionare il **pulsante Codice** e **scaricare ZIP** per scaricare l'esempio come *. FILE ZIP* nel computer.

:::image type="content" source="media/how-to-move-regions/download-repo-zip.png" alt-text="Screenshot del repository digital-twins-explorer in GitHub. Il pulsante Codice è selezionato, producendo una piccola finestra di dialogo in cui è evidenziato il pulsante Scarica ZIP." lightbox="media/how-to-move-regions/download-repo-zip.png":::

Decomprimere i file.

Configurare quindi le autorizzazioni per Azure Digital Twins Explorer. Seguire le istruzioni nella [sezione Configurare Gemelli digitali di Azure e Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) della guida Gemelli digitali di Azure guida introduttiva. Questa sezione illustra i passaggi seguenti:

1. Configurare un'Gemelli digitali di Azure predefinita. È possibile ignorare questa parte perché è già presente un'istanza di .
1. Configurare le credenziali di Azure locali per fornire l'accesso all'istanza.
1. Eseguire Azure Digital Twins Explorer e configurarlo per connettersi all'istanza. Si userà il *nome host dell'istanza* Gemelli digitali di Azure originale che si sta spostando.

A questo punto dovrebbe essere disponibile Azure Digital Twins Explorer app di esempio in esecuzione in un browser nel computer. L'esempio deve essere connesso all'istanza Gemelli digitali di Azure originale.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Finestra del browser che mostra un'app in esecuzione in localhost:3000. L'app è denominata Azure Digital Twins Explorer e contiene caselle per Esplora query, Visualizzazione modello, Visualizzazione grafico ed Esplora proprietà. Non sono ancora presenti dati sullo schermo." lightbox="media/how-to-move-regions/explorer-blank.png":::

Per verificare la connessione, selezionare il pulsante Esegui query per eseguire la query predefinita che visualizza tutti i gemelli e le relazioni nel grafico nella casella **GRAPH EXPLORER** . 

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Un pulsante che legge Esegui query nell'angolo superiore destro della finestra è evidenziato." lightbox="media/how-to-move-regions/run-query.png":::

È possibile lasciare Azure Digital Twins Explorer in esecuzione perché verrà nuovamente utilizzato più avanti in questo articolo per ricaricare questi elementi nella nuova istanza nell'area di destinazione.

### <a name="download-models-twins-and-graph"></a>Scaricare modelli, gemelli e grafo

Scaricare quindi i modelli, i gemelli e il grafo nella soluzione nel computer.

Per scaricare tutti questi elementi contemporaneamente, assicurarsi prima di tutto che nella casella **GRAPH VIEW (VISUALIZZAZIONE GRAFICO)** sia visualizzato il grafico completo. Se il grafico completo non è già visualizzato, eseguire di nuovo la query predefinita di `SELECT * FROM digitaltwins` nella **casella ESPLORA** QUERY.
 
Selezionare quindi **l'icona Esporta grafico** nella **casella GRAPH VIEW (VISUALIZZAZIONE** GRAFICO).

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Nella casella Visualizzazione grafico è evidenziata un'icona. Mostra una freccia che punta verso il basso da una nuvola." lightbox="media/how-to-move-regions/export-graph.png":::

Questa azione abilita un **collegamento Scarica** nella casella GRAPH **VIEW (VISUALIZZAZIONE** GRAFICO). Selezionarlo per scaricare una rappresentazione basata su JSON del risultato della query, che include modelli, gemelli e relazioni. Questa azione deve scaricare un file con estensione json nel computer.

>[!NOTE]
>Se il file scaricato sembra avere un'estensione diversa, provare a modificare direttamente l'estensione e a modificarla in .json.

## <a name="move"></a>Sposta

Successivamente, si completerà lo "spostamento" dell'istanza creando una nuova istanza nell'area di destinazione. Sarà quindi necessario popolarla con i dati e i componenti dell'istanza originale.

### <a name="create-a-new-instance"></a>Creare una nuova istanza

Creare prima di tutto una nuova istanza di Gemelli digitali di Azure nell'area di destinazione. Seguire la procedura descritta in [Procedura: Configurare un'istanza e l'autenticazione](how-to-set-up-instance-portal.md). Tenere presenti i puntatori seguenti:

* È possibile mantenere lo stesso nome per la nuova *istanza se* si tratta di un gruppo di risorse diverso. Se è necessario usare lo stesso gruppo di risorse che contiene l'istanza originale, la nuova istanza dovrà avere un nome distinto.
* Immettere la nuova area di destinazione quando viene richiesta una località.

Al termine di questo passaggio, sarà necessario il nome host della nuova istanza per continuare a configurarla con i dati. Se non è stato prendere nota del nome [](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) host durante l'installazione, seguire queste istruzioni per ottenerlo dal portale di Azure.

### <a name="repopulate-the-old-instance"></a>Ripopolare l'istanza precedente

Successivamente, si configura la nuova istanza in modo che sia una copia dell'originale.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Caricare i modelli, i gemelli e il grafo originali usando Azure Digital Twins Explorer

In questa sezione è possibile ricaricare i modelli, i gemelli e il grafo nella nuova istanza. Se nell'istanza originale non sono presenti modelli, gemelli o grafi o non si vuole spostarli nella nuova istanza, è possibile passare alla [sezione successiva.](#re-create-endpoints-and-routes)

In caso contrario, tornare alla finestra del browser che Azure Digital Twins Explorer e seguire questa procedura.

##### <a name="connect-to-the-new-instance"></a>Connettersi alla nuova istanza

Attualmente, Azure Digital Twins Explorer è connessa all'istanza Gemelli digitali di Azure originale. Impostare la connessione in modo che punti alla nuova istanza selezionando il **pulsante Accedi** nell'angolo superiore destro della finestra.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Azure Digital Twins Explorer l'icona Accedi nell'angolo superiore destro della finestra. L'icona mostra un semplice ingranaggio di una persona sovrapposto con una chiave." lightbox="media/how-to-move-regions/sign-in.png":::

Sostituire **l'URL ADT** in modo che rifletta la nuova istanza. Modificare questo valore in modo che letta *https://{nome host nuova istanza}*.

Selezionare **Connetti**. Potrebbe essere richiesto di accedere di nuovo con le credenziali di Azure o concedere il consenso dell'applicazione per l'istanza.

##### <a name="upload-models-twins-and-graph"></a>Caricare modelli, gemelli e grafo

Caricare quindi i componenti della soluzione scaricati in precedenza nella nuova istanza.

Per caricare i modelli, i gemelli e il grafo, selezionare **l'icona Importa** grafico nella casella **VISUALIZZAZIONE** GRAFO. Questa opzione carica tutti e tre questi componenti contemporaneamente. Carica anche i modelli che non sono attualmente in uso nel grafico.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Nel riquadro Graph View è evidenziata un'icona. Mostra una freccia che punta verso l'alto in una nuvola." lightbox="media/how-to-move-regions/import-graph.png":::

Nella casella del selettore di file passare al grafico scaricato. Selezionare il file **JSON del grafo** e selezionare **Apri**.

Dopo alcuni secondi, Azure Digital Twins Explorer apre una **visualizzazione Importa** che mostra un'anteprima del grafo da caricare.

Per confermare il caricamento del grafico, selezionare l'icona **Save** (Salva) nell'angolo in alto a destra del riquadro **GRAPH VIEW** (VISUALIZZAZIONE GRAFO).

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Icona di salvataggio evidenziata nel riquadro di anteprima del grafo" lightbox="media/how-to-move-regions/graph-preview-save.png":::.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Azure Digital Twins Explorer ora carica i modelli e il grafo (inclusi i gemelli e le relazioni) nella nuova Gemelli digitali di Azure istanza. Verrà visualizzato un messaggio di esito positivo che indica il numero di modelli, gemelli e relazioni caricati.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Finestra di dialogo che indica l'esito positivo dell'importazione del grafo. Il testo indica che l'importazione è riuscita. 2 modelli importati. 4 gemelli importati. 2 relazioni importate.&quot;" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Per verificare che tutto sia stato caricato correttamente, selezionare il pulsante Esegui **query** nella casella **GRAPH EXPLORER** per eseguire la query predefinita che visualizza tutti i gemelli e le relazioni nel grafico. Questa azione aggiorna anche l'elenco di modelli nella **casella VISTA** MODELLO.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Evidenziare intorno al pulsante Esegui query nell'angolo superiore destro della finestra." lightbox="media/how-to-move-regions/run-query.png":::

Verrà visualizzato il grafico con tutti i relativi gemelli e relazioni visualizzati nella casella **GRAPH EXPLORER** . I modelli dovrebbero essere elencati anche nella **casella VISTA** MODELLO.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Visualizzazione di un Azure Digital Twins Explorer che mostra due modelli evidenziati nella casella Visualizzazione modello e un grafo evidenziato nella casella Esplora grafi." lightbox="media/how-to-move-regions/post-upload.png":::

Queste visualizzazioni confermano che i modelli, i gemelli e il grafo sono stati ricaricati nella nuova istanza nell'area di destinazione.

#### <a name="re-create-endpoints-and-routes"></a>Ri-creare endpoint e route

Se nell'istanza originale sono presenti endpoint o route, è necessario crearli nuovamente nella nuova istanza. Se nell'istanza originale non sono presenti endpoint o route o non si vuole spostarli nella nuova istanza, è possibile passare alla [sezione successiva.](#relink-connected-resources)

In caso contrario, seguire la procedura descritta in [Procedura: Gestire endpoint e route](how-to-manage-routes-portal.md) usando la nuova istanza. Tenere presenti questi puntatori:

* Non è *necessario* creare nuovamente la risorsa Griglia di eventi, Hub eventi o bus di servizio in uso per l'endpoint. Per altre informazioni, vedere la sezione "Prerequisiti" nelle istruzioni per l'endpoint. È sufficiente creare nuovamente l'endpoint nell'istanza Gemelli digitali di Azure locale.
* È possibile riutilizzare i nomi di endpoint e route perché hanno come ambito un'istanza diversa.
* Ricordarsi di aggiungere eventuali filtri necessari alle route create.

#### <a name="relink-connected-resources"></a>Ricollegare le risorse connesse

Se sono presenti altre app o risorse di Azure connesse all'istanza di Gemelli digitali di Azure originale, sarà necessario modificare la connessione in modo che raggiungano la nuova istanza. Queste risorse possono includere altri servizi di Azure o app personali o aziendali che sono stati impostati per l'uso con Gemelli digitali di Azure.

Se all'istanza originale non sono connesse altre risorse o non si vuole spostarle nella nuova istanza, è possibile passare alla [sezione successiva.](#verify)

In caso contrario, prendere in considerazione le risorse connesse nello scenario. Non è necessario eliminare e creare di nuovo le risorse connesse. È invece sufficiente modificare i punti in cui si connettono a un'Gemelli digitali di Azure tramite il nome host. Questi punti vengono quindi aggiornati in modo da usare il nome host della nuova istanza anziché l'originale.

Le risorse esatte da modificare dipendono dallo scenario, ma di seguito sono riportati alcuni punti di integrazione comuni:

* Funzioni di Azure. Se si dispone di una funzione di Azure il cui codice include il nome host dell'istanza originale, è necessario aggiornare questo valore al nome host della nuova istanza e ripubblicare la funzione.
* Griglia di eventi, Hub eventi o bus di servizio.
* App per la logica.
* Time Series Insights.
* Mappe di Azure.
* Servizio Device Provisioning dell'hub IoT.
* App personali o aziendali all'esterno di Azure, ad esempio l'app client creata in [Esercitazione:](tutorial-code.md)Codificare un'app client , che si connette all'istanza e chiama Gemelli digitali di Azure API.
* Azure AD le registrazioni delle *app* non devono essere ri create. Se si usa una registrazione [dell'app](how-to-create-app-registration.md) per connettersi alle API Gemelli digitali di Azure, è possibile riusare la stessa registrazione dell'app con la nuova istanza.

Al termine di questo passaggio, la nuova istanza nell'area di destinazione deve essere una copia dell'istanza originale.

## <a name="verify"></a>Verifica

Per verificare che la nuova istanza sia stata configurata correttamente, usare gli strumenti seguenti:

* [Portale di Azure](https://portal.azure.com). Il portale consente di verificare che la nuova istanza esista e che si trova nell'area di destinazione corretta. È anche buona per verificare endpoint, route e connessioni ad altri servizi di Azure.
* [Gemelli digitali di Azure comandi dell'interfaccia della riga di comando](how-to-use-cli.md). Questi comandi consentono di verificare che la nuova istanza esista e si trova nell'area di destinazione corretta. Possono anche essere usati per verificare i dati dell'istanza.
* [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure Digital Twins Explorer è buona per verificare i dati dell'istanza, ad esempio modelli, gemelli e grafi.
* [Gemelli digitali di Azure API e SDK](how-to-use-apis-sdks.md). Queste risorse sono utili per verificare i dati dell'istanza, ad esempio modelli, gemelli e grafi. Sono inoltre buoni per la verifica di endpoint e route.

È anche possibile provare a eseguire qualsiasi app personalizzata o flusso end-to-end eseguito con l'istanza originale per verificare che funzionino correttamente con la nuova istanza.

## <a name="clean-up-source-resources"></a>Pulire le risorse di origine

Ora che la nuova istanza è impostata nell'area di destinazione con una copia dei dati e delle connessioni dell'istanza originale, è possibile eliminare l'istanza originale.

È possibile usare il [portale di Azure](https://portal.azure.com), l'interfaccia della riga di comando [di Azure](how-to-use-cli.md)o le API del piano [di controllo](how-to-use-apis-sdks.md#overview-control-plane-apis).

Per eliminare l'istanza usando il [portale di Azure,](https://portal.azure.com) aprire il portale in una finestra del browser e passare all'istanza di Gemelli digitali di Azure originale cercando il nome nella barra di ricerca del portale.

Selezionare il **pulsante** Elimina e seguire le istruzioni per completare l'eliminazione.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Visualizzazione dei dettagli Gemelli digitali di Azure'istanza nella portale di Azure, nella scheda Panoramica. Il pulsante Elimina è evidenziato.":::