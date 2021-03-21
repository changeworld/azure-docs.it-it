---
title: Usare le code del bus di servizio di Azure con Java (azure-messaging-servicebus)
description: Questa esercitazione illustra come usare Java per inviare e ricevere messaggi dalla coda del bus di servizio di Azure. Si userà il nuovo pacchetto azure-messaging-servicebus.
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 1fab8d3f603b3c2c7e3c307d3d2ce53590d7511c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739764"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Inviare e ricevere messaggi dalle code del bus di servizio di Azure (Java)
In questa guida di avvio rapido si creerà un'app Java per inviare e ricevere messaggi da una coda del bus di servizio di Azure. 

> [!IMPORTANT]
> Questa guida di avvio rapido usa il nuovo pacchetto azure-messaging-servicebus. Per una guida di avvio rapido che usa il pacchetto azure-servicebus precedente, vedere [Inviare e ricevere messaggi con azure-servicebus](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se non si ha una coda da usare, seguire la procedura descritta nell'articolo [Usare il portale di Azure per creare una coda del bus di servizio](service-bus-quickstart-portal.md) per crearne una. Prendere nota della **stringa di connessione** per lo spazio dei nomi del bus di servizio e del nome della **coda** creata.
- Installare [Azure SDK per Java][Azure SDK for Java]. Se si usa Eclipse, è possibile installare [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] che include Azure SDK per Java. È quindi possibile aggiungere le **librerie di Microsoft Azure per Java** al progetto. Se si usa IntelliJ, vedere [Installare Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda
In questa sezione verrà creato un progetto di console Java, quindi verrà aggiunto codice per inviare messaggi alla coda creata in precedenza. 

### <a name="create-a-java-console-project"></a>Creare un progetto di console Java
Creare un progetto Java usando Eclipse o un altro strumento a scelta. 

### <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione per l'uso del bus di servizio
Aggiungere i riferimenti alle librerie di Azure core e del bus di servizio di Azure. 

Se si usa Eclipse e si crea un'applicazione console Java, convertire il progetto Java in Maven: fare clic con il pulsante destro del mouse sul progetto nella finestra **Esplora pacchetti** e selezionare **Configura**  ->  **Converti in progetto Maven**. Aggiungere quindi le dipendenze a queste due librerie, come illustrato nell'esempio seguente.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Aggiungere il codice per inviare messaggi alla coda
1. Aggiungere le istruzioni `import` seguenti nell'argomento del file Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Nella classe definire le variabili in cui contenere la stringa di connessione e il nome della coda, come illustrato di seguito: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Sostituire `<NAMESPACE CONNECTION STRING>` con la stringa di connessione allo spazio dei nomi del bus di servizio. Sostituire `<QUEUE NAME>` con il nome della coda.
3. Aggiungere un metodo denominato `sendMessage` nella classe per inviare un messaggio alla coda. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. Aggiungere un metodo denominato `createMessages` nella classe per creare un elenco di messaggi. In genere, questi messaggi provengono da parti diverse dell'applicazione. In questo caso verrà creato un elenco di messaggi di esempio.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Aggiungere un metodo denominato `sendMessageBatch` per inviare messaggi alla coda creata. Questo metodo crea un oggetto `ServiceBusSenderClient` per la coda, richiama il metodo `createMessages` per ottenere l'elenco di messaggi, prepara uno o più batch e li invia alla coda. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda
In questa sezione si aggiunge il codice per recuperare i messaggi dalla coda. 

1. Aggiungere un metodo denominato `receiveMessages` per ricevere messaggi dalla coda. Questo metodo crea un oggetto `ServiceBusProcessorClient` per la coda specificando un gestore per l'elaborazione di messaggi e un altro per la gestione degli errori. Quindi avvia il processore, aspetta alcuni secondi, stampa i messaggi ricevuti e infine arresta e chiude il processore.

    > [!IMPORTANT]
    > Sostituire `QueueTest` in `QueueTest::processMessage` nel codice con il nome della classe. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(QueueTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }   
    ```
2. Aggiungere il `processMessage` metodo per elaborare un messaggio ricevuto dalla sottoscrizione del bus di servizio. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Aggiungere il `processError` metodo per gestire i messaggi di errore.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
2. Aggiornare il metodo `main` per richiamare i metodi `sendMessage`, `sendMessageBatch` e `receiveMessages` e per generare `InterruptedException`.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Eseguire l'app
Quando si esegue l'applicazione, nella finestra della console vengono visualizzati i messaggi seguenti. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Processing message. Session: 88d961dd801f449e9c3e0f8a5393a527, Sequence #: 1. Contents: Hello, World!
Processing message. Session: e90c8d9039ce403bbe1d0ec7038033a0, Sequence #: 2. Contents: First message
Processing message. Session: 311a216a560c47d184f9831984e6ac1d, Sequence #: 3. Contents: Second message
Processing message. Session: f9a871be07414baf9505f2c3d466c4ab, Sequence #: 4. Contents: Third message
Stopping and closing the processor
```

Nella pagina **Panoramica** dello spazio dei nomi del bus di servizio nel portale di Azure è possibile visualizzare il numero di messaggi **in ingresso** e **in uscita**. Può essere necessario aspettare circa un minuto e quindi aggiornare la pagina per vedere i valori più recenti. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Numero di messaggi in ingresso e in uscita" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Selezionare la coda in questa pagina **Panoramica** per passare alla pagina **Coda del bus di servizio**. Anche in questa pagina viene visualizzato il numero di messaggi **in ingresso** e **in uscita**. Vengono visualizzate anche altre informazioni, come le **dimensioni correnti** della coda, le **dimensioni massime**, il **numero di messaggi attivi** e così via. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Dettagli della coda" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione e gli esempi seguenti:

- [Libreria client del bus di servizio di Azure per Java - Leggimi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Esempi in GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Informazioni di riferimento sulle API Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
