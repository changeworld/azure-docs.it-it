---
title: Pubblicare ed elaborare messaggi dagli argomenti Atlas Kafka di Azure Purview tramite Hub eventi usando .NET
description: Questo articolo fornisce una procedura dettagliata per creare un'applicazione .NET Core che invia/riceve eventi da e verso gli argomenti Apache Atlas Kafka di Purview usando il pacchetto Azure.Messaging.EventHubs più recente.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 04/15/2021
ms.openlocfilehash: 60c177c913c78dbcfcbfe1d465044b69b0e6dd2e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590422"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>Pubblicare ed elaborare messaggi dagli argomenti Atlas Kafka di Azure Purview tramite Hub eventi usando .NET 
Questa guida introduttiva illustra come inviare e ricevere eventi dagli argomenti Atlas Kafka di Azure Purview tramite l'hub eventi usando la libreria .NET **Azure.Messaging.EventHubs.** 

> [!IMPORTANT]
> Un hub eventi gestito viene creato come parte della creazione dell'account Purview. Vedere [Purview account creation](create-catalog-portal.md). È possibile pubblicare messaggi nell'argomento kafka dell'hub eventi ATLAS_HOOK e Purview lo consumerà ed elagherà. Purview invierà una notifica alle modifiche dell'entità all'argomento kafka dell'hub eventi ATLAS_ENTITIES e l'utente può utilizzare ed elaborarlo. Questa guida introduttiva usa la nuova **libreria Azure.Messaging.EventHubs.** 


## <a name="prerequisites"></a>Prerequisiti
Se non si ha familiarità con Hub eventi di Azure, vedere [Panoramica di Hub eventi](../event-hubs/event-hubs-about.md) prima di procedere con questa guida di avvio rapido. 

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- **Sottoscrizione di Microsoft Azure**. Per usare i servizi di Azure, tra cui Hub eventi di Azure, è necessaria una sottoscrizione.  Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/free/) oppure usare i vantaggi della sottoscrizione di MSDN per la [creazione di un account](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. La libreria client di Hub eventi di Azure usa le nuove funzionalità introdotte in C# 8.0.  È comunque possibile usare la libreria con le  versioni precedenti del linguaggio C#, ma la nuova sintassi non sarà disponibile. Per utilizzare la sintassi completa, è consigliabile eseguire la compilazione con [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 o versione successiva e la [versione della lingua](/dotnet/csharp/language-reference/configure-language-version#override-a-default) impostata su `latest`. Se si usa Visual Studio, le versioni precedenti a Visual Studio 2019 non sono compatibili con gli strumenti necessari per la compilazione di progetti in C# 8.0. Visual Studio 2019, inclusa l'edizione Community gratuita, può essere scaricato [qui](https://visualstudio.microsoft.com/vs/).

## <a name="publish-messages-to-purview"></a>Pubblicare messaggi in Purview 
Questa sezione illustra come creare un'applicazione console .NET Core per inviare eventi a purview tramite l'argomento kafka dell'hub **eventi ATLAS_HOOK**. 

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

Creare quindi un'applicazione console C# .NET in Visual Studio:

1. Avviare **Visual Studio**.
2. Nella finestra di avvio selezionare **Crea un nuovo progetto** > **App console (.NET Framework)** . È necessario .NET versione 4.5.2 o successiva.
3. In **Nome progetto** immettere **PurviewKafkaProducer**.
4. Selezionare **Crea** per creare il progetto.

### <a name="create-a-console-application"></a>Creare un'applicazione console

1. Avviare Visual Studio 2019. 
1. Selezionare **Crea un nuovo progetto**. 
1. Nella finestra di dialogo **Crea un nuovo progetto** seguire questa procedura: Se questa finestra di dialogo non viene visualizzata, scegliere **File** dal menu, selezionare **Nuovo** e quindi **Progetto**. 
    1. Selezionare **C#** come linguaggio di programmazione.
    1. Selezionare **Console** come tipo di applicazione. 
    1. Selezionare **App console (.NET Core)** nell'elenco risultati. 
    1. Quindi selezionare **Avanti**. 


### <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

1. Scegliere **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti** dal menu. 
1. Eseguire il comando seguente per installare il **pacchetto NuGet Azure.Messaging.EventHubs** e il pacchetto **NuGet Azure.Messaging.EventHubs.Producer:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
    ```    


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Scrivere codice per inviare messaggi all'hub eventi

1. Aggiungere le istruzioni `using` seguenti all'inizio del file **Program.cs**:

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Aggiungere costanti alla classe per la stringa di connessione di Hub eventi e `Program` il nome dell'hub eventi. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    È possibile ottenere lo spazio dei nomi dell'hub eventi associato all'account purview esaminando le stringhe di connessione primaria/secondaria dell'endpoint Kafka Atlas nella scheda delle proprietà dell'account Purview.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Spazio dei nomi dell'hub eventi":::

    Il nome dell'hub eventi deve **ATLAS_HOOK** per l'invio di messaggi a Purview.

3. Sostituire il `Main` metodo con il metodo seguente e aggiungere un oggetto per eseguire il push dei messaggi in `async Main` `async ProduceMessage` Purview. Per i dettagli, vedere i commenti nel codice. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Compilare il progetto e assicurarsi che non siano presenti errori.
6. Eseguire il programma e attendere il messaggio di conferma. 

    > [!NOTE]
    > Per il codice sorgente completo con commenti più informativi, vedere [questo file in GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>Esempio di messaggio Create Entity JSON (Crea entità JSON) per creare una tabella sql con due colonne.

```json
    
    {
    "msgCreatedBy": "nayenama",
    "message": {
    "entities": {
    "referredEntities": {
        "-1102395743156037": {
            "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                "precision": 23,
                "length": 8,
                "description": "Sales Order ID",
                "scale": 3,
                "name": "OrderID",
                "data_type": "int",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156037",
            "version": 2
        },
        "-1102395743156038": {
         "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                "description": "Sales Order Date",
                "scale": 3,
                "name": "OrderDate",
                "data_type": "datetime",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156038",
            "status": "ACTIVE",
            "createdBy": "ServiceAdmin",
            "version": 0
        }
        },
        "entity": 
                {
                    "typeName": "azure_sql_table",
                    "attributes": {
                        "owner": "admin",
                        "temporary": false,
                        "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name" : "SalesOrderTable",
                        "description": "Sales Order Table added via Kafka",
                        "columns": [
                            {
                                "guid": "-1102395743156037",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid": "-1102395743156038",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                        },
                        "guid": "-1102395743156036",
                    "version": 0                
                    }
                },
        "type": "ENTITY_CREATE_V2",
        "user": "admin"
    },
    "version": {
        "version": "1.0.0"
    },
    "msgCompressionKind": "NONE",
    "msgSplitIdx": 1,
    "msgSplitCount": 1
}

``` 

## <a name="consume-messages-from-purview"></a>Utilizzare i messaggi da Purview
Questa sezione mostra come scrivere un'applicazione console .NET Core che riceve i messaggi da un hub eventi tramite un processore di eventi. È necessario usare ATLAS_ENTITIES hub eventi per ricevere messaggi da Purview. Il processore di eventi semplifica la ricezione di eventi dagli hub eventi gestendo checkpoint persistenti e ricezioni parallele da tali hub eventi. 

> [!WARNING]
> Se si esegue questo codice nell'hub di Azure Stack, si verificheranno errori di runtime a meno che non si scelga una versione specifica dell'API di archiviazione come destinazione. Il motivo è che Event Hubs SDK usa l'ultima versione disponibile in Azure dell'API di archiviazione di Azure, che potrebbe non essere presente nella piattaforma dell'hub di Azure Stack. L'hub di Azure Stack potrebbe supportare una versione di Storage Blob SDK diversa da quelle solitamente disponibili in Azure. Se si usa Archiviazione BLOB di Azure come archivio di checkpoint, verificare la [versione dell'API di archiviazione di Azure supportata per la build dell'hub di Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) e scegliere tale versione come destinazione nel codice. 
>
> Se ad esempio l'esecuzione avviene nell'hub di Azure Stack versione 2005, la versione più recente disponibile per il servizio di archiviazione è 2019-02-02. Per impostazione predefinita, la libreria client di Event Hubs SDK usa la versione più recente disponibile in Azure (2019-07-07 al momento del rilascio dell'SDK). In questo caso, oltre ai passaggi descritti in questa sezione, sarà anche necessario aggiungere codice destinato alla versione 2019-02-02 dell'API del servizio di archiviazione. Per informazioni su come definire come destinazione una versione specifica dell'API di archiviazione, vedere [questo esempio in GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Creare un account di archiviazione di Azure e un contenitore BLOB
In questo argomento di avvio rapido si usa una risorsa di archiviazione di Azure come archivio di checkpoint. Seguire questa procedura per creare un account di archiviazione di Azure. 

1. [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Creare un contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Ottenere la stringa di connessione all'account di archiviazione](../storage/common/storage-configure-connection-string.md)

    Prendere nota della stringa di connessione e del nome del contenitore. Questi valori verranno usati nel codice di ricezione. 


### <a name="create-a-project-for-the-receiver"></a>Creare un progetto per l'app ricevente

1. Nella finestra Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **EventHubQuickStart**, scegliere **Aggiunti** e selezionare **Nuovo progetto**. 
1. Selezionare **App console (.NET Core)**, quindi **Avanti**. 
1. Immettere **PurviewKafkaConsumer** per **Nome progetto** e selezionare **Crea.** 

### <a name="add-the-event-hubs-nuget-package"></a>Aggiungere il pacchetto NuGet di Hub eventi

1. Scegliere **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti** dal menu. 
1. Eseguire il comando seguente per installare il pacchetto NuGet **Azure.Messaging.EventHubs**:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Eseguire il comando seguente per installare il pacchetto NuGet **Azure.Messaging.EventHubs.Processor**:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Aggiornare il metodo Main 

1. Aggiungere le istruzioni `using` seguenti all'inizio del file **Program.cs**.

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Aggiungere le costanti alla classe `Program` per la stringa di connessione di Hub eventi e il nome dell'hub eventi. Sostituire i segnaposto tra parentesi con i valori appropriati ottenuti durante la creazione dell'hub eventi. Sostituire i segnaposto tra parentesi quadre con i valori appropriati ottenuti durante la creazione dell'hub eventi e dell'account di archiviazione (chiavi di accesso, stringa di connessione primaria). Assicurarsi che `{Event Hubs namespace connection string}` sia la stringa di connessione a livello di spazio dei nomi e non la stringa dell'hub eventi.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
    
    È possibile ottenere lo spazio dei nomi dell'hub eventi associato all'account purview esaminando le stringhe di connessione primaria/secondaria dell'endpoint Kafka Atlas nella scheda delle proprietà dell'account Purview.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Spazio dei nomi dell'hub eventi":::

    Il nome dell'hub eventi deve **essere ATLAS_ENTITIES** per l'invio di messaggi a Purview.

3. Sostituire il metodo `Main` con il metodo `async Main` seguente. Per i dettagli, vedere i commenti nel codice. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. A questo punto, aggiungere i seguenti metodi di gestori di eventi ed errori alla classe. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Compilare il progetto e assicurarsi che non siano presenti errori.

    > [!NOTE]
    > Per il codice sorgente completo con commenti più informativi, vedere [questo file in GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md).
6. Eseguire l'applicazione ricevente. 

### <a name="sample-message-received-from-purview"></a>Messaggio di esempio ricevuto da Purview

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas supporta attualmente i tipi di operazione seguenti: **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2**, **ENTITY_DELETE_V2**. Il push dei messaggi in Purview è attualmente abilitato per impostazione predefinita. Se lo scenario prevede la lettura da Purview, contattare Microsoft perché deve essere consentito nell'elenco. (specificare l'ID sottoscrizione e il nome dell'account Purview).


## <a name="next-steps"></a>Passaggi successivi
Controllare gli esempi in GitHub. 

- [Esempi di Hub eventi su GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Esempi di processore di eventi in GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Introduzione ad Atlas alle notifiche](https://atlas.apache.org/2.0.0/Notifications.html)