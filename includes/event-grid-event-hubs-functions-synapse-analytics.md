---
title: includere file
description: includere file
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854271"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Panoramica dell'applicazione":::

Questo diagramma illustra il flusso di lavoro della soluzione compilata in questa esercitazione: 

1. I dati inviati a un hub eventi di Azure vengono acquisiti in una risorsa di archiviazione BLOB di Azure.
2. Una volta completata l'acquisizione dei dati, viene generato un evento e viene inviato a una griglia di eventi di Azure. 
3. La griglia di eventi inoltra i dati di tale evento a un'app per le funzioni di Azure.
4. L'app per le funzioni usa l'URL del BLOB nei dati dell'evento per recuperare il BLOB dalla risorsa di archiviazione. 
5. L'app per le funzioni esegue la migrazione dei dati del BLOB a un'istanza di Azure Synapse Analytics. 

In questo articolo, si eseguono i passaggi seguenti:

> [!div class="checklist"]
> - Distribuire l'infrastruttura necessaria per l'esercitazione
> - Pubblicare il codice in un'app per le funzioni
> - Creare una sottoscrizione di Griglia di eventi 
> - Trasmettere in streaming dati di esempio in Hub eventi
> - Verificare i dati acquisiti in Azure Synapse Analytics

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessari:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- [Visual Studio 2019](https://www.visualstudio.com/vs/) con carichi di lavoro per lo sviluppo di desktop .NET, di Azure, Web e ASP.NET, di Node.js e di Python.
- Scaricare il [progetto di esempio EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) sul computer.
    - WindTurbineDataGenerator: un server di pubblicazione semplice che invia dati di turbine eoliche di esempio a un hub eventi con Acquisizione abilitata
    - FunctionDWDumper: una funzione di Azure che riceve una notifica da Griglia di eventi quando un file Avro viene acquisito nel BLOB di Archiviazione di Azure. Riceve il percorso URI del BLOB, ne legge il contenuto ed esegue il push dei dati ad Azure Synapse Analytics (pool SQL dedicato).

## <a name="deploy-the-infrastructure"></a>Distribuire l'infrastruttura
In questo passaggio, implementare l'infrastruttura necessaria con un [modello di Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Quando si implementa il modello, vengono create le risorse seguenti:

* Hub eventi con la funzionalità Acquisizione abilitata.
* Account di archiviazione per i file acquisiti. 
* Piano di servizio app per l'hosting dell'app per le funzioni
* App per le funzioni per l'elaborazione dell'evento
* SQL Server per l'hosting del data warehouse
* Azure Synapse Analytics (pool SQL dedicato) per l'archiviazione dei dati trasferiti

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Usare l'interfaccia della riga di comando di Azure per distribuire l'infrastruttura

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Selezionare il pulsante **Cloud Shell** nella parte superiore.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure portal":::
3. Si noterà che Cloud Shell è aperto nella parte inferiore del browser.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. In Cloud Shell, se viene visualizzata un'opzione per scegliere tra **Bash** e **PowerShell**, selezionare **Bash**. 
5. Se si usa Cloud Shell per la prima volta, creare un account di archiviazione selezionando **Crea risorsa di archiviazione**. Azure Cloud Shell richiede un account di archiviazione di Azure per archiviare alcuni file. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Creare un account di archiviazione per Cloud Shell":::
6. Attendere finché non viene inizializzata Cloud Shell. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell inizializzata":::
1. Creare un gruppo di risorse di Azure eseguendo il comando CLI seguente: 
    1. Copiare e incollare il comando seguente nella finestra di Cloud Shell. Se si desidera, modificare il nome del gruppo di risorse e la località.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Premere **INVIO**. 

        Esempio:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Distribuire tutte le risorse menzionate nella sezione precedente (hub eventi, account di archiviazione, app per le funzioni, Azure Synapse Analytics) eseguendo il comando dell'interfaccia della riga di comando seguente: 
    1. Copiare e incollare il comando nella finestra di Cloud Shell. In alternativa, è possibile copiare/incollare in un editor di propria scelta, impostare i valori, quindi copiare il comando in Cloud Shell. 

        > [!IMPORTANT]
        > Specificare i valori per le entità seguenti prima di eseguire il comando: 
        > - Nome del gruppo di risorse creato in precedenza.
        > - Nome dello spazio dei nomi dell'hub eventi. 
        > - Nome dell'hub eventi. È possibile lasciare il valore così com'è (hubdatamigration).
        > - Nome del server SQL.
        > - Nome dell'utente e della password SQL. 
        > - Nome del database.
        > - Nome dell'account di archiviazione. 
        > - Nome dell’app per le funzioni. 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Premere **INVIO** nella finestra di Cloud Shell per eseguire il comando. Questo processo potrebbe richiedere qualche minuto, poiché si sta creando una serie di risorse. Nel risultato del comando, assicurarsi che non si siano verificati errori. 
1. Chiudere Cloud Shell selezionando il pulsante **Cloud Shell** nel portale oppure il pulsante **X** nell'angolo in alto a destra della finestra Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Verificare che siano state create le risorse

1. Nel portale di Azure, selezionare **Gruppi di risorse** nel menu a sinistra. 
2. Filtrare l'elenco dei gruppi di risorse immettendo il nome del gruppo di risorse nella casella di ricerca. 
3. Selezionare il gruppo di risorse nell'elenco.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Selezionare un gruppo di risorse":::
4. Confermare che le risorse seguenti sono visibili nel gruppo di risorse:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Risorse nel gruppo di risorse" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Creare una tabella in Azure Synapse Analytics
Creare una tabella nel data warehouse mediante l'esecuzione dello script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Per eseguire lo script, è possibile usare Visual Studio o l'Editor di query nel portale. I passaggi seguenti mostrano come usare l'Editor di query: 

1. Nell'elenco di risorse nel gruppo di risorse selezionare il **pool SQL dedicato**. 
2. Nel menu sinistro della sezione **Attività comuni** nella pagina **Pool SQL dedicato** selezionare **Editor di query (anteprima)** . 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Pagina Azure Synapse Analytics":::
2. Immettere il nome dell'**utente** e la **password** per il server SQL e selezionare **OK**. Se viene visualizzato un messaggio che chiede se consentire al client di accedere al server SQL, procedere come segue:
    1. Selezionare il collegamento **Imposta firewall server**. 
    2. Nella pagina **Impostazioni del firewall** selezionare **Aggiungi IP client** e quindi **Salva** sulla barra degli strumenti. 
    3. Selezionare **OK** nella finestra del messaggio di operazione riuscita.
    4. Tornare nella pagina **Pool SQL dedicato** e selezionare **Editor di query (anteprima)** nel menu sinistro. 
    5. Immettere **nome utente** e **password** e selezionare **OK**. 
1. Nella finestra di query, copiare ed eseguire lo script SQL seguente: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="Eseguire query SQL":::
5. Mantenere questa scheda o finestra aperta in modo che sia possibile verificare che i dati vengono creati al termine dell'esercitazione. 

### <a name="update-the-function-runtime-version"></a>Aggiornare la versione del runtime della funzione

1. Aprire un'altra scheda del Web browser e passare al [portale di Azure](https://portal.azure.com).
1. Nel portale di Azure, selezionare **Gruppi di risorse** nel menu a sinistra.
1. Selezionare il gruppo di risorse in cui è presente l'app per le funzioni. 
1. Selezionare l'**app per le funzioni** nell'elenco di risorse del gruppo.
1. Selezionare **Configurazione** in **Impostazioni** nel menu a sinistra. 
1. Passare alla scheda **Impostazioni di runtime della funzione** nel riquadro a destra. 
1. Aggiornare **Versione runtime** a **~3**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="Aggiornare la versione del runtime della funzione":::
6. Sulla barra degli strumenti selezionare **Salva**. 
1. Nel popup di conferma **Salva modifiche** selezionare **Continua**. 

## <a name="publish-the-azure-functions-app"></a>Pubblicare l'app Funzioni di Azure

1. Avviare Visual Studio.
2. Aprire la soluzione **EventHubsCaptureEventGridDemo.sln** scaricata dal [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) come parte dei prerequisiti. È possibile trovarla nella cartella `/samples/e2e/EventHubsCaptureEventGridDemo`. 
3. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **FunctionEGDWDumper** e scegliere **Pubblica**.
4. Se si visualizza la schermata seguente, selezionare **Avvia**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="Pulsante Avvia nella sezione Pubblica.":::
5. Nella finestra di dialogo **Pubblica** selezionare **Azure** per **Destinazione**, quindi fare clic su **Avanti**. 
6. Selezionare **App per le funzioni di Azure (Windows)** e quindi **Avanti**.
7. Nella scheda **Istanza di Funzioni** selezionare la sottoscrizione di Azure, espandere il gruppo di risorse, selezionare l'app per le funzioni e quindi selezionare **Fine**. È necessario accedere al proprio account Azure, se non si è già connessi. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="Selezionare l'app per le funzioni desiderata":::
8. Nella sezione **Dipendenze del servizio** della pagina **Pubblica** selezionare **Configura** per **Archiviazione**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="Selezionare il collegamento Configura per la dipendenza del servizio archiviazione":::
1. Nella pagina **Configura la dipendenza** seguire questa procedura: 
    1. Selezionare l'**account di archiviazione** creato in precedenza e quindi selezionare **Avanti**. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Selezionare l'account di archiviazione":::
    10. Specificare un **nome per la stringa di connessione** e selezionare **Nessuno** per l'opzione **Salva il valore della stringa di connessione in**, quindi selezionare **Avanti**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Specificare il nome della stringa di connessione":::      
    1. Deselezionare le opzioni **File di codice C#** e **Archivio segreti** e quindi selezionare **Fine**.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Esaminare il riepilogo delle modifiche":::
1. Quando il profilo è stato configurato automaticamente in Visual Studio, selezionare **Pubblica**.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Select publish":::
2. Nella scheda in cui è aperta la pagina **Funzione di Azure** selezionare **Funzioni** nel menu sinistro. Verificare che la funzione **EventGridTriggerMigrateData** sia visualizzata nell'elenco. Se non è visualizzata, provare a eseguirne di nuovo la pubblicazione in Visual Studio e quindi aggiornare la pagina nel portale. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="Confermare la creazione della funzione":::    

Dopo aver pubblicato la funzione, si è pronti per sottoscrivere l'evento.

## <a name="subscribe-to-the-event"></a>Sottoscrivere l'evento

1. In una nuova scheda o in una nuova finestra di un Web browser, passare al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure, selezionare **Gruppi di risorse** nel menu a sinistra. 
3. Filtrare l'elenco dei gruppi di risorse immettendo il nome del gruppo di risorse nella casella di ricerca. 
4. Selezionare il gruppo di risorse nell'elenco.
1. Selezionare **Spazio dei nomi di Hub eventi** nell'elenco di risorse.
1. Nella pagina **Spazio dei nomi di Hub eventi** selezionare **Eventi** nel menu sinistro e quindi **+ Sottoscrizione di eventi** sulla barra degli strumenti. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Collegamento per l'aggiunta di una sottoscrizione di eventi nella pagina Eventi di Spazio dei nomi di Hub eventi":::
1. Nella pagina **Crea sottoscrizione di eventi** procedere come segue:
    1. Immettere un nome per la **sottoscrizione di eventi**. 
    1. Immettere un nome per l'**argomento di sistema**. Un argomento di sistema fornisce al mittente un endpoint per l'invio di eventi. Per altre informazioni, vedere [Argomenti di sistema](../articles/event-grid/system-topics.md)
    1. Per **Tipo di endpoint**, selezionare **Funzione di Azure**.
    1. Per **Endpoint**, selezionare il collegamento.
    1. Nella pagina **Seleziona funzione di Azure** seguire questa procedura se i campi non vengono automaticamente compilati.
        1. Selezionare la sottoscrizione di Azure che include la funzione di Azure. 
        1. Selezionare il gruppo di risorse per la funzione. 
        1. Selezionare l'app per le funzioni.
        1. Selezionare lo slot di distribuzione. 
        1. Selezionare la funzione **EventGridTriggerMigrateData**. 
    1. Nella pagina **Seleziona funzione di Azure** selezionare **Conferma selezione**.
    1. Quindi di nuovo nella pagina **Crea sottoscrizione di eventi** selezionare **Crea**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="Creare una sottoscrizione di eventi usando la funzione" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Verificare che la sottoscrizione di eventi sia stata creata. Passare alla scheda **Sottoscrizione di eventi** della scheda **Eventi** per lo spazio dei nomi di Hub eventi. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Confermare la sottoscrizione di eventi" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. Selezionare il piano di servizio app (non il servizio app) nell'elenco delle risorse nel gruppo di risorse. 

## <a name="run-the-app-to-generate-data"></a>Eseguire l'app per generare i dati
È stata completata la configurazione dell'hub eventi, del pool SQL dedicato (in precedenza SQL Data Warehouse), dell'app per le funzioni di Azure e della sottoscrizione di eventi. Prima di eseguire un'applicazione che genera i dati per l'hub eventi, è necessario configurare alcuni valori.

1. Nel portale di Azure, passare al gruppo di risorse come in precedenza. 
2. Selezionare lo spazio dei nomi di Hub eventi.
3. Nella pagina **Spazio dei nomi di Hub eventi**, selezionare **Criteri di accesso condivisi** nel menu a sinistra.
4. Selezionare **RootManageSharedAccessKey** nell'elenco dei criteri. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Pagina Criteri di accesso condivisi per uno spazio dei nomi di Hub eventi":::    
1. Selezionare il pulsante di copia accanto alla casella di testo **Chiave primaria della stringa di connessione**. 
1. Tornare alla soluzione Visual Studio. 
1. Fare clic con il pulsante destro sul progetto **WindTurbineDataGenerator** e scegliere **Imposta come progetto di avvio**. 
1. Nel progetto WindTurbineDataGenerator aprire **program.cs**.
1. Sostituire `<EVENT HUBS NAMESPACE CONNECTION STRING>` con la stringa di connessione copiata dal portale. 
1. Sostituire `<EVENT HUB NAME>` con il nome dell'hub eventi. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Compilare la soluzione. Eseguire l'applicazione **WindTurbineGenerator.exe**. 
7. Dopo un paio di minuti, nell'altra scheda del browser in cui è aperta la finestra della query, eseguire una query sulla tabella del data warehouse per trovare i dati trasferiti.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Risultati query](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>Monitorare la soluzione
Questa sezione illustra come monitorare o risolvere i problemi della soluzione. 

### <a name="view-captured-data-in-the-storage-account"></a>Visualizzare i dati acquisiti nell'account di archiviazione
1. Passare al gruppo di risorse e selezionare l'account di archiviazione usato per l'acquisizione dei dati degli eventi. 
1. Nella pagina **Account di archiviazione** selezionare **Storage Explorer (anteprima)** nel menu sinistro.
1. Espandere **Contenitori BLOB** e selezionare **windturbinecapture**. 
1. Aprire la cartella con lo stesso nome dello **spazio dei nomi di Hub eventi** nel riquadro destro. 
1. Aprire la cartella con lo stesso nome dell'hub eventi (**hubdatamigration**). 
1. Scorrere le cartelle fino a visualizzare i file AVRO. Ecco un esempio:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="File acquisito nell'account di archiviazione" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Verificare che il trigger di Griglia di eventi abbia richiamato la funzione
1. Passare al gruppo di risorse e selezionare l'app per le funzioni. 
1. Selezionare **Funzioni** nel menu sinistro.
1. Selezionare la funzione **EventGridTriggerMigrateData** nell'elenco. 
1. Nella pagina **Funzione** selezionare **Monitoraggio** nel menu sinistro. 
1. Selezionare **Configura** per configurare Application Insights in modo da acquisire il log di chiamate. 
1. Creare una nuova risorsa di **Application Insights** o selezionarne una esistente. 
1. Tornare nella pagina **Monitoraggio** per la funzione. 
1. Verificare che l'applicazione client (**WindTurbineDataGenerator**) che invia gli eventi sia ancora in esecuzione. In caso contrario, eseguire l'app. 
1. Attendere almeno 5 minuti e quindi selezionare il pulsante **Aggiorna** per visualizzare le chiamate alla funzione.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="Chiamate alla funzione":::
1. Selezionare una chiamata per visualizzare i dettagli.

    Griglia di eventi distribuisce i dati dell'evento ai sottoscrittori. L'esempio seguente mostra i dati di evento generati quando i dati trasmessi attraverso un hub eventi vengono acquisiti in un BLOB. In particolare, si noti la `fileUrl` proprietà nei `data` punti oggetto nel BLOB nella risorsa di archiviazione. L'app per le funzioni usa questo URL per recuperare il file BLOB con i dati acquisiti.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Verificare che i dati siano archiviati nel pool SQL dedicato
Nella scheda del browser in cui è aperta la finestra della query, eseguire una query sulla tabella del pool SQL dedicato per trovare i dati trasferiti.

![Risultati query](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

