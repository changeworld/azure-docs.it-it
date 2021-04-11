---
title: Creare trigger basati su eventi in Azure Data Factory
description: Informazioni su come creare un trigger in Azure Data Factory per l'esecuzione di una pipeline in risposta a un evento.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 3021d049a38f1d883518fc7c45aa8ca0a906c2f7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221586"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>Creare un trigger per l'esecuzione di una pipeline in risposta a un evento di archiviazione

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive i trigger di eventi di archiviazione che è possibile creare nelle pipeline Data Factory.

Un'architettura guidata dagli eventi è un comune modello di integrazione dei dati che implica produzione, rilevamento, utilizzo e risposta agli eventi. Gli scenari di integrazione dei dati richiedono spesso Data Factory ai clienti di attivare pipeline in base agli eventi che si verificano nell'account di archiviazione, ad esempio l'arrivo o l'eliminazione di un file nell'account di archiviazione BLOB di Azure. Data Factory si integra in modo nativo con [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/), che consente di attivare pipeline per tali eventi.

Per un'introduzione di dieci minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> L'integrazione descritta in questo articolo dipende dalla [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/). Verificare che la sottoscrizione sia registrata con il provider di risorse di Griglia di eventi. Per altre informazioni, vedere [Provider e tipi di risorse](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). È necessario essere in grado di eseguire l'azione *Microsoft. EventGrid/eventSubscriptions/**. Questa azione fa parte del ruolo predefinito collaboratore sottoscrizione evento di EventGrid.

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory

Questa sezione illustra come creare un trigger di evento di archiviazione all'interno dell'interfaccia utente di Azure Data Factory.

1. Passare alla scheda **modifica** , mostrata con un simbolo a matita.

1. Selezionare **trigger** dal menu, quindi selezionare **nuovo/modifica**.

1. Nella pagina **Aggiungi trigger** selezionare **Scegli trigger**, quindi selezionare **+ nuovo**.

1. Selezionare l' **evento di archiviazione** del tipo di trigger

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image1.png" alt-text="Screenshot della pagina Author (autore) per creare un nuovo trigger di evento di archiviazione nell'interfaccia utente di Data Factory.":::

1. Selezionare l'account di archiviazione nell'elenco a discesa della sottoscrizione di Azure o manualmente usando l'ID risorsa dell'account di archiviazione. Scegliere il contenitore in cui si vuole che si verifichino gli eventi. La selezione del contenitore è obbligatoria, ma tenere presente che la selezione di tutti i contenitori può causare un numero elevato di eventi.

   > [!NOTE]
   > Il trigger dell'evento di archiviazione supporta attualmente solo gli account di archiviazione Azure Data Lake Storage Gen2 e per utilizzo generico versione 2. A causa di una limitazione di griglia di eventi di Azure, Azure Data Factory supporta solo un massimo di 500 trigger di eventi di archiviazione per account di archiviazione. Se si raggiunge il limite, contattare il supporto tecnico per consigli e aumentare il limite al momento della valutazione da parte del team di griglia di eventi. 

   > [!NOTE]
   > Per creare un nuovo trigger di evento di archiviazione esistente o modificarlo, l'account Azure usato per accedere Data Factory e pubblicare il trigger dell'evento di archiviazione deve avere l'autorizzazione appropriata per il controllo degli accessi in base al ruolo (RBAC di Azure) per l'account di archiviazione. Non è necessaria alcuna autorizzazione aggiuntiva: l'entità servizio per la Azure Data Factory _non necessita di_ autorizzazioni speciali per l'account di archiviazione o griglia di eventi. Per altre informazioni sul controllo di accesso, vedere la sezione [controllo degli accessi in base al ruolo](#role-based-access-control) .

1. Le proprietà **Percorso BLOB inizia con** e **Percorso BLOB termina con** consentono di specificare i contenitori, le cartelle e i nomi di BLOB per cui si vogliono ricevere eventi. Per il trigger dell'evento di archiviazione è necessario definire almeno una di queste proprietà. È possibile usare svariati modelli per le due proprietà **Percorso BLOB inizia con** e **Percorso BLOB termina con**, come mostrato negli esempi più avanti in questo articolo.

    * **Il percorso del BLOB inizia con:** il percorso del BLOB deve iniziare con un percorso di cartella. I valori validi includono `2018/` e `2018/april/shoes.csv`. Se non è selezionato un contenitore, questo campo non è selezionabile.
    * **Il percorso del BLOB termina con:** il percorso del BLOB deve terminare con un nome file o un'estensione. I valori validi includono `shoes.csv` e `.csv`. I nomi di contenitori e cartelle, quando specificati, devono essere separati da un `/blobs/` segmento. Ad esempio, il valore di un contenitore denominato 'orders' è `/orders/blobs/2018/april/shoes.csv`. Per specificare una cartella in qualsiasi contenitore, omettere il carattere '/' iniziale. Ad esempio, con `april/shoes.csv` verrà attivato un evento in tutti i file denominati `shoes.csv` nella cartella denominata 'april' presente in qualsiasi contenitore.
    * Si noti che il percorso BLOB **inizia con** e **termina con** è l'unico criterio consentito nel trigger dell'evento di archiviazione. Altri tipi di corrispondenza con caratteri jolly non sono supportati per il tipo di trigger.

1. Scegliere se il trigger deve rispondere a un evento **Blob created** (BLOB creato), **Blob deleted** (BLOB eliminato) o entrambi. Nel percorso di archiviazione specificato ogni evento attiverà le pipeline di Data Factory associate al trigger.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image2.png" alt-text="Screenshot della pagina di creazione del trigger dell'evento di archiviazione.":::

1. Consente di indicare se il trigger ignora i BLOB con zero byte.

1. Dopo aver configurato il trigger, fare clic su Avanti **: Anteprima dati**. Questa schermata mostra i BLOB esistenti corrispondenti alla configurazione del trigger dell'evento di archiviazione. Assicurarsi che siano presenti filtri specifici. La configurazione di filtri troppo generici può causare la creazione o l'eliminazione di un numero elevato di file e influire significativamente sui costi. Dopo aver verificato le condizioni di filtro, fare clic su **Fine**.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image3.png" alt-text="Screenshot della pagina di anteprima del trigger dell'evento di archiviazione.":::

1. Per alleghi una pipeline a questo trigger, passare all'area di disegno della pipeline e fare clic su **trigger** e selezionare **New/Edit (nuovo/modifica**). Quando viene visualizzato il riquadro di spostamento laterale, fare clic sull'elenco a discesa **Choose trigger** (Scegli trigger) e selezionare il trigger creato. Fare clic su **Avanti: Anteprima dati** per verificare che la configurazione sia corretta e quindi su **Avanti** per verificare che l'anteprima dei dati sia corretta.

1. Se alla pipeline sono associati parametri, è possibile specificarli nel riquadro di spostamento laterale dei parametri di esecuzione del trigger. Il trigger dell'evento di archiviazione acquisisce il percorso e il nome file del BLOB nelle proprietà `@triggerBody().folderPath` e `@triggerBody().fileName` . Per usare i valori di queste proprietà in una pipeline, è necessario mappare le proprietà per i parametri della pipeline. Dopo il mapping delle proprietà per i parametri, è possibile accedere ai valori acquisiti dal trigger attraverso l'espressione `@pipeline().parameters.parameterName` attraverso la pipeline. Per una spiegazione dettagliata, vedere [metadati del trigger di riferimento nelle pipeline](how-to-use-trigger-parameterization.md)

   :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image4.png" alt-text="Screenshot delle proprietà di mapping del trigger dell'evento di archiviazione ai parametri della pipeline.":::

   Nell'esempio precedente, il trigger viene configurato in modo da essere attivato quando viene creato un percorso BLOB che termina con. csv nella cartella Sample _-testing_ nel contenitore _Sample-Data_. Le proprietà **folderPath** e **fileName** acquisiscono il percorso del nuovo BLOB. Ad esempio, quando si aggiunge MoviesDB.csv viene aggiunto al percorso sample-data/event-testing, il valore di `@triggerBody().folderPath` è `sample-data/event-testing` e il valore di `@triggerBody().fileName` è `moviesDB.csv`. Questi valori vengono mappati nell'esempio ai parametri della pipeline `sourceFolder` e `sourceFile` , che possono essere usati in tutta la pipeline come `@pipeline().parameters.sourceFolder` e `@pipeline().parameters.sourceFile` rispettivamente.

   > [!NOTE]
   > Se si sta creando la pipeline e il trigger in [Azure sinapsi Analytics](../synapse-analytics/overview-what-is.md), è necessario `@trigger().outputs.body.fileName` usare `@trigger().outputs.body.folderPath` e come parametri. Queste due proprietà acquisiscono informazioni sui BLOB. Utilizzare tali proprietà anziché utilizzare `@triggerBody().fileName` e `@triggerBody().folderPath` .

1. Al termine, fare clic su **Fine**.

## <a name="json-schema"></a>Schema JSON

La tabella seguente fornisce una panoramica degli elementi dello schema correlati ai trigger degli eventi di archiviazione:

| **Elemento JSON** | **Descrizione** | **Tipo** | **Valori consentiti** | **Obbligatorio** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **ambito** | ID risorsa di Azure Resource Manager dell'account di archiviazione. | string | ID Azure Resource Manager | Sì |
| **eventi** | Tipo di eventi che provocano l'attivazione del trigger. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sì, qualsiasi combinazione di questi valori. |
| **blobPathBeginsWith** | Il percorso del BLOB deve iniziare con il modello fornito per l'attivazione del trigger. Ad esempio, `/records/blobs/december/` attiva il trigger solo per i BLOB nella cartella `december` nel contenitore `records`. | string   | | Specificare un valore per almeno una di queste proprietà: `blobPathBeginsWith` o `blobPathEndsWith` . |
| **blobPathEndsWith** | Il percorso del BLOB deve terminare con il modello fornito per l'attivazione del trigger. Ad esempio, `december/boxes.csv` attiva il trigger solo per i BLOB denominati `boxes` in una cartella `december`. | string   | | È necessario specificare un valore per almeno una di queste proprietà: `blobPathBeginsWith` o `blobPathEndsWith`. |
| **ignoreEmptyBlobs** | Indica se con i BLOB a zero byte verrà attivata un'esecuzione della pipeline. Per impostazione predefinita, questa opzione è impostata su true. | Boolean | true o false | No |

## <a name="examples-of-storage-event-triggers"></a>Esempi di trigger di eventi di archiviazione

In questa sezione vengono forniti esempi di impostazioni del trigger dell'evento di archiviazione.

> [!IMPORTANT]
> È necessario includere il segmento `/blobs/` del percorso, come illustrato negli esempi seguenti, ogni volta che si specifica il contenitore e la cartella, il contenitore e il file o il contenitore, la cartella e il file. Per **blobPathBeginsWith** l'interfaccia utente di Data Factory aggiungerà automaticamente `/blobs/` tra il nome della cartella e quello nome del contenitore nel codice JSON del trigger.

| Proprietà | Esempio | Descrizione |
|---|---|---|
| **Il percorso del BLOB inizia con** | `/containername/` | Riceve gli eventi per qualsiasi BLOB nel contenitore. |
| **Il percorso del BLOB inizia con** | `/containername/blobs/foldername/` | Riceve gli eventi per qualsiasi BLOB nel contenitore `containername` e nella cartella `foldername`. |
| **Il percorso del BLOB inizia con** | `/containername/blobs/foldername/subfoldername/` | È anche possibile fare riferimento a una sottocartella. |
| **Il percorso del BLOB inizia con** | `/containername/blobs/foldername/file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` nella cartella `foldername` nel contenitore `containername`. |
| **Il percorso del BLOB termina con** | `file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` in qualsiasi percorso. |
| **Il percorso del BLOB termina con** | `/containername/blobs/file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` nel contenitore `containername`. |
| **Il percorso del BLOB termina con** | `foldername/file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` nella cartella `foldername` in qualsiasi contenitore. |

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Azure Data Factory usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per garantire che l'accesso non autorizzato ad ascolto, sottoscrizione degli aggiornamenti da e trigger di pipeline collegati a eventi BLOB sia strettamente vietato.

* Per creare un nuovo trigger di evento di archiviazione esistente o aggiornarlo, l'account Azure che ha eseguito l'accesso al Data Factory deve avere l'accesso appropriato all'account di archiviazione pertinente. In caso contrario, l'operazione con ha esito negativo con _accesso negato_.
* Data Factory non richiede alcuna autorizzazione speciale per la griglia di eventi e _non_ è necessario assegnare l'autorizzazione RBAC speciale per data factory entità servizio per l'operazione.

Le impostazioni RBAC seguenti funzionano per il trigger dell'evento di archiviazione:

* Ruolo di proprietario per l'account di archiviazione
* Ruolo Collaboratore per l'account di archiviazione
* _Microsoft. EventGrid/EventSubscriptions/_ autorizzazione di scrittura per l'account di archiviazione _/Subscriptions/# # # #/resourceGroups/# # # #/Providers/Microsoft.storage/storageAccounts/storageAccountName_

Per comprendere il modo in cui Azure Data Factory fornisce le due promesse, è opportuno eseguire un passaggio e ottenere una sbirciatina dietro la scena. Ecco i flussi di lavoro di alto livello per l'integrazione tra Data Factory, archiviazione e griglia di eventi.

### <a name="create-a-new-storage-event-trigger"></a>Creare un nuovo trigger di evento di archiviazione

Questo flusso di lavoro di alto livello descrive il modo in cui Azure Data Factory interagisce con griglia di eventi per creare un trigger di evento di archiviazione

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="Flusso di lavoro della creazione del trigger dell'evento di archiviazione.":::

Due chiamate evidenti dai flussi di lavoro:

* Azure Data Factory _non esegue alcun_ contatto diretto con l'account di archiviazione. La richiesta di creazione di una sottoscrizione viene invece inoltrata ed elaborata da griglia di eventi. Di conseguenza, Data Factory non richiede l'autorizzazione per l'account di archiviazione per questo passaggio.

* Il controllo di accesso e il controllo delle autorizzazioni avvengono sul lato Azure Data Factory. Prima che ADF invii una richiesta di sottoscrizione all'evento di archiviazione, verifica l'autorizzazione per l'utente. In particolare, controlla se l'account Azure che ha effettuato l'accesso e il tentativo di creare il trigger dell'evento di archiviazione ha accesso appropriato all'account di archiviazione pertinente. Se il controllo delle autorizzazioni ha esito negativo, anche la creazione del trigger non riesce.

### <a name="storage-event-trigger-data-factory-pipeline-run"></a>Trigger dell'evento di archiviazione Data Factory esecuzione della pipeline

Questi flussi di lavoro di alto livello descrivono la modalità di esecuzione della pipeline degli eventi di archiviazione tramite griglia di eventi

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="Flusso di lavoro delle esecuzioni della pipeline di attivazione degli eventi di archiviazione.":::

Quando si tratta di una pipeline di attivazione degli eventi in Data Factory, tre chiamate evidenti nel flusso di lavoro:

* Griglia di eventi usa un modello push che inoltra il messaggio il prima possibile quando l'archiviazione rilascia il messaggio nel sistema. Questa operazione è diversa dal sistema di messaggistica, ad esempio Kafka, in cui viene usato un sistema di pull.
* Il trigger di evento su Azure Data Factory funge da listener attivo per il messaggio in arrivo e attiva correttamente la pipeline associata.
* Il trigger dell'evento di archiviazione non esegue alcun contatto diretto con l'account di archiviazione

  * Ciò premesso, se si dispone di una copia o di un'altra attività all'interno della pipeline per elaborare i dati nell'account di archiviazione, Data Factory farà un contatto diretto con l'archiviazione usando le credenziali archiviate nel servizio collegato. Verificare che il servizio collegato sia configurato in modo appropriato
  * Tuttavia, se non si fa riferimento all'account di archiviazione nella pipeline, non è necessario concedere l'autorizzazione per Data Factory ad accedere all'account di archiviazione

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sui trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
* Informazioni su come fare riferimento ai metadati dei trigger nella pipeline, vedere [metadati del trigger di riferimento nelle esecuzioni di pipeline](how-to-use-trigger-parameterization.md)
