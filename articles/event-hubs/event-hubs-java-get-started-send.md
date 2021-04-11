---
title: Inviare o ricevere eventi da Hub eventi di Azure tramite Java (ultima versione)
description: Questo articolo illustra come creare un'applicazione Java che invia/riceve eventi a/da Hub eventi di Azure usando il pacchetto azure-messaging-eventhubs più recente.
ms.topic: quickstart
ms.date: 04/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: bae0d4147fddf57398d494ca7f13c347f892e45e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448441"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Usare Java per inviare o ricevere eventi da Hub eventi di Azure (azure-messaging-eventhubs)
Questa guida di avvio rapido illustra come inviare e ricevere eventi da un hub eventi con il pacchetto Java **azure-messaging-eventhubs**.

> [!IMPORTANT]
> In questo argomento di avvio rapido viene usato il nuovo pacchetto **azure-messaging-eventhubs**. Per una guida di avvio rapido sui pacchetti **azure-eventhubs** e **azure-eventhubs-eph** precedenti, vedere [Inviare e ricevere eventi con azure-eventhubs e azure-eventhubs-eph](event-hubs-java-get-started-send-legacy.md). 


## <a name="prerequisites"></a>Prerequisiti
Se non si ha familiarità con Hub eventi di Azure, vedere [Panoramica di Hub eventi](event-hubs-about.md) prima di procedere con questa guida di avvio rapido. 

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- **Sottoscrizione di Microsoft Azure**. Per usare i servizi di Azure, tra cui Hub eventi di Azure, è necessaria una sottoscrizione.  Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/free/) oppure usare i vantaggi della sottoscrizione di MSDN per la [creazione di un account](https://azure.microsoft.com).
- Ambiente di sviluppo in Java. In questa guida di avvio rapido si usa [Eclipse](https://www.eclipse.org/). È necessario Java Development Kit (JDK) versione 8 o successiva. 
- **Creare uno spazio dei nomi di Hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md). Ottenere quindi la **stringa di connessione allo spazio dei nomi di Hub eventi** seguendo le istruzioni disponibili nell'articolo [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti in questa guida di avvio rapido.

## <a name="send-events"></a>Inviare eventi 
Questa sezione illustra come creare un'applicazione Java per inviare eventi a un hub eventi. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Aggiungere il riferimento alla libreria di Hub eventi di Azure

La libreria client Java per Hub eventi è disponibile nel [repository centrale di Maven](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Per fare riferimento a questa libreria è possibile usare questa dichiarazione di dipendenza all'interno del file di progetto di Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.6.0</version>
</dependency>
```

> [!NOTE]
> Aggiornare la versione alla versione più recente pubblicata nel repository maven. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Scrivere codice per inviare messaggi all'hub eventi

Per l'esempio seguente, creare prima un nuovo progetto Maven per un'applicazione console/shell nell'ambiente di sviluppo Java preferito. Aggiungere una classe denominata `Sender` e includere il codice seguente nella classe:

> [!IMPORTANT]
> Aggiornare `<Event Hubs namespace connection string>` con la stringa di connessione allo spazio dei nomi di hub eventi. Aggiornare `<Event hub name>` con il nome dell'hub eventi nello spazio dei nomi. 

```java
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```
### <a name="add-code-to-publish-events-to-the-event-hub"></a>Aggiungere il codice per pubblicare gli eventi nell'hub eventi
Aggiungere un metodo denominato `publishEvents` alla `Sender` classe, come illustrato di seguito. 

```java
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the event data is bigger than max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));
        
        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
```

Compilare il programma e assicurarsi che non siano presenti errori. Questo programma verrà eseguito dopo l'esecuzione del programma ricevente. 

## <a name="receive-events"></a>Ricevere eventi
Il codice di questa esercitazione si basa sull'[esempio di codice EventProcessorSample in GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java), che è possibile esaminare per vedere l'applicazione completa in funzione.

> [!WARNING]
> Se si esegue questo codice nell'hub di Azure Stack, si verificheranno errori di runtime a meno che non si scelga una versione specifica dell'API di archiviazione come destinazione. Il motivo è che Event Hubs SDK usa l'ultima versione disponibile in Azure dell'API di archiviazione di Azure, che potrebbe non essere presente nella piattaforma dell'hub di Azure Stack. L'hub di Azure Stack potrebbe supportare una versione di Storage Blob SDK diversa da quelle solitamente disponibili in Azure. Se si usa Archiviazione BLOB di Azure come archivio di checkpoint, verificare la [versione dell'API di archiviazione di Azure supportata per la build dell'hub di Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) e scegliere tale versione come destinazione nel codice. 
>
> Se ad esempio l'esecuzione avviene nell'hub di Azure Stack versione 2005, la versione più recente disponibile per il servizio di archiviazione è 2019-02-02. Per impostazione predefinita, la libreria client di Event Hubs SDK usa la versione più recente disponibile in Azure (2019-07-07 al momento del rilascio dell'SDK). In questo caso, oltre ai passaggi descritti in questa sezione, sarà anche necessario aggiungere codice destinato alla versione 2019-02-02 dell'API del servizio di archiviazione. Per informazioni su come definire come destinazione una versione specifica dell'API di archiviazione, vedere [questo esempio in GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Creare un account di archiviazione di Azure e un contenitore BLOB
In questa guida di avvio rapido verrà usata una risorsa di archiviazione di Azure (nello specifico, l'archiviazione BLOB) come archivio di checkpoint. Il checkpoint è un processo mediante il quale un processore di eventi contrassegna o conferma la posizione dell'ultimo evento elaborato correttamente all'interno di una partizione. Il contrassegno di un checkpoint viene in genere eseguito all'interno della funzione che elabora gli eventi. Per altre informazioni sul checkpoint, vedere [Processore di eventi](event-processor-balance-partition-load.md).

Seguire questa procedura per creare un account di archiviazione di Azure. 

1. [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Creare un contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Ottenere la stringa di connessione all'account di archiviazione](../storage/common/storage-configure-connection-string.md)

    Prendere nota della **stringa di connessione** e del **nome del contenitore**. Questi valori verranno usati nel codice di ricezione. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Aggiungere le librerie di Hub eventi al progetto Java
Aggiungere le dipendenze seguenti nel file pom.xml. 

- [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.1</version>
    </dependency>
</dependencies>
```

1. Aggiungere le istruzioni **import** seguenti all'inizio del file Java. 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    ```
2. Creare una classe denominata `Receiver` e aggiungere le variabili di stringa seguenti alla classe. Sostituire i segnaposto con i valori corretti. 

    > [!IMPORTANT]
    > Sostituire i segnaposto con i valori corretti.
    > - `<Event Hubs namespace connection string>` con la stringa di connessione allo spazio dei nomi di hub eventi. Aggiornamento 
    > - `<Event hub name>` con il nome dell'hub eventi nello spazio dei nomi. 
    > - `<Storage connection string>` con la stringa di connessione all'account di archiviazione di Azure. 
    > - `<Storage container name>` con il nome del contenitore nell'archivio BLOB di Azure. 

    ```java
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";
    private static final String storageConnectionString = "<Storage connection string>";
    private static final String storageContainerName = "<Storage container name>";
    ```
1. Aggiungere il metodo `main` seguente alla classe. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(storageConnectionString) 
            .containerName(storageContainerName) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(connectionString, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. Aggiungere i due metodi helper (`PARTITION_PROCESSOR` e `ERROR_HANDLER`) che elaborano gli eventi e gli errori alla classe `Receiver`. 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. Compilare il programma e assicurarsi che non siano presenti errori. 

## <a name="run-the-applications"></a>Eseguire le applicazioni
1. Eseguire prima l'applicazione **ricevente**.
1. Quindi, eseguire l'applicazione **mittente**. 
1. Nella finestra dell'applicazione **ricevente** verificare che siano visualizzati gli eventi pubblicati dall'applicazione mittente.

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    ```
1. Premere **INVIO** nella finestra dell'applicazione ricevente per arrestare l'applicazione. 

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    
    Stopping event processor
    Event processor stopped.
    Exiting process
    ```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli esempi seguenti in GitHub:

- [Esempi per azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Esempi per azure-messaging-eventhubs-checkpointstore-blob](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  
