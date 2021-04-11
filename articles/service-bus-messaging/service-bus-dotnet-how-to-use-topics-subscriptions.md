---
title: Inviare messaggi agli argomenti del bus di servizio con azure-messaging-servicebus
description: Questa guida di avvio rapido illustra come inviare messaggi agli argomenti del bus di servizio di Azure usando il pacchetto azure-messaging-servicebus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 79eb7783fd3daf546539dd5b9048f4e9f484374f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279800"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Inviare messaggi a un argomento del bus di servizio di Azure e ricevere messaggi dalle sottoscrizioni dell'argomento (.NET)
In questa esercitazione viene creata un'applicazione C# per eseguire le attività seguenti:

1. Inviare messaggi a un argomento del bus di servizio. 

    Un argomento del bus di servizio fornisce un endpoint per le applicazioni mittente per l'invio di messaggi. Un argomento può includere una o più sottoscrizioni. Ogni sottoscrizione di un argomento ottiene una copia del messaggio inviato all'argomento. Per altre informazioni sugli argomenti del bus di servizio, vedere informazioni sul [bus di servizio di Azure](service-bus-messaging-overview.md). 
1. Ricevere messaggi da una sottoscrizione dell'argomento. 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Argomenti e sottoscrizioni del bus di servizio":::

    > [!Important]
    > Questa guida di avvio rapido usa il nuovo pacchetto **Azure.Messaging.ServiceBus**. Se si usa il pacchetto Microsoft. Azure. ServiceBus precedente, vedere [inviare e ricevere messaggi usando il pacchetto Microsoft. Azure. ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione Visual Studio o MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oppure registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Seguire i passaggi in questa [Guida introduttiva](service-bus-quickstart-topics-subscriptions-portal.md) per creare un argomento del bus di servizio e le sottoscrizioni dell'argomento. 

    > [!NOTE]
    > Si utilizzerà la stringa di connessione per lo spazio dei nomi, il nome dell'argomento e il nome di una delle sottoscrizioni dell'argomento in questa esercitazione.  
- [Visual Studio 2019](https://www.visualstudio.com/vs). 
 
## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento
In questa sezione verrà creata un'applicazione console .NET Core in Visual Studio, verrà aggiunto il codice per inviare messaggi all'argomento del bus di servizio creato. 

### <a name="create-a-console-application"></a>Creare un'applicazione console
Creare un'applicazione console .NET Core usando Visual Studio. 

1. Avviare Visual Studio.  
1. Se viene visualizzata la pagina attività **iniziali** , selezionare **Crea un nuovo progetto**. 
1. Nella pagina **Crea un nuovo progetto** , attenersi alla seguente procedura: 
    1. Per il linguaggio di programmazione, selezionare **C#**. 
    1. Per il tipo di progetto, selezionare **console**. 
    1. Selezionare **app console (.NET Core)** dall'elenco di modelli. 
    1. Quindi selezionare **Avanti**. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="Creare un progetto di app console":::
1. Nella pagina **Configura nuovo progetto** , attenersi alla seguente procedura: 
    1. Per **nome progetto**, immettere un nome per il progetto. 
    1. Per **località** selezionare un percorso per i file del progetto e della soluzione. 
    1. Per **Nome soluzione** immettere un nome per la soluzione. Una soluzione Visual Studio può avere uno o più progetti. In questa Guida introduttiva, la soluzione avrà un solo progetto. 
    1. Selezionare **Crea** per creare il progetto. 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="Immettere il nome e il percorso della soluzione e del progetto":::    


### <a name="add-the-service-bus-nuget-package"></a>Aggiungere il pacchetto NuGet del bus di servizio
1. Fare clic con il pulsante destro del mouse sul progetto appena creato e scegliere **Gestisci pacchetti NuGet**.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="Menu Gestisci pacchetti NuGet":::        
1. Passare alla scheda **Sfoglia**.
1. Cercare e selezionare **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** .
1. Selezionare **Installa** per completare l'installazione.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="Selezionare il pacchetto NuGet del bus di servizio.":::
5. Se viene visualizzata la finestra di dialogo **Anteprima modifiche** , selezionare **OK** per continuare. 
1. Se viene visualizzata la pagina **accettazione della licenza** , **selezionare Accetto** per continuare. 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>Aggiungere il codice per inviare messaggi all'argomento 

1. Nella finestra **Esplora soluzioni** fare doppio clic su **Program. cs** per aprire il file nell'editor. 
1. Aggiungere le `using` istruzioni seguenti all'inizio della definizione dello spazio dei nomi, prima della dichiarazione di classe:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. Nella `Program` classe, sopra la `Main` funzione, dichiarare le variabili seguenti:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    Sostituire i valori seguenti:
    - `<NAMESPACE CONNECTION STRING>` con la stringa di connessione allo spazio dei nomi del bus di servizio
    - `<TOPIC NAME>` con il nome dell'argomento
    - `<SUBSCRIPTION NAME>` con il nome della sottoscrizione

### <a name="send-a-single-message-to-the-topic"></a>Inviare un singolo messaggio all'argomento
Aggiungere un metodo denominato `SendMessageToTopicAsync` alla `Program` classe sopra o sotto il `Main` metodo. Questo metodo invia un singolo messaggio all'argomento.

```csharp
    static async Task SendMessageToTopicAsync()
    {
        // create a Service Bus client 
        await using (ServiceBusClient client = new ServiceBusClient(connectionString))
        {
            // create a sender for the topic
            ServiceBusSender sender = client.CreateSender(topicName);
            await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
            Console.WriteLine($"Sent a single message to the topic: {topicName}");
        }
    }
```

Questo metodo esegue i passaggi seguenti: 
1. Crea un oggetto [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) usando la stringa di connessione allo spazio dei nomi. 
1. Usa l' `ServiceBusClient` oggetto per creare un oggetto [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) per l'argomento del bus di servizio specificato. Questo passaggio usa il metodo [ServiceBusClient. CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) .
1. Quindi, il metodo invia un singolo messaggio di test all'argomento del bus di servizio usando il metodo [ServiceBusSender. SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) . 

### <a name="send-a-batch-of-messages-to-the-topic"></a>Inviare un batch di messaggi all'argomento
1. Aggiungere un metodo denominato `CreateMessages` per creare una coda (coda .NET, non una coda del bus di servizio) per la `Program` classe. In genere, questi messaggi provengono da parti diverse dell'applicazione. In questo caso verrà creata una coda di messaggi di esempio. Se non si ha familiarità con le code .NET, vedere [queue. INQUEUE](/dotnet/api/system.collections.queue.enqueue).

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Aggiungere un metodo denominato `SendMessageBatchAsync` `Program` Class e aggiungere il codice seguente. Questo metodo prepara uno o più batch di una coda di messaggi da inviare all'argomento del bus di servizio. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```    

    Ecco i passaggi importanti dal codice:
    1. Crea un oggetto [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) usando la stringa di connessione allo spazio dei nomi. 
    1. Richiama il metodo [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) sull' `ServiceBusClient` oggetto per creare un oggetto [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) per l'argomento del bus di servizio specificato. 
    1. Richiama il metodo helper `GetMessages` per ottenere una coda di messaggi da inviare all'argomento del bus di servizio. 
    1. Crea un [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) usando [ServiceBusSender. CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync).
    1. Aggiungere messaggi al batch usando [ServiceBusMessageBatch. TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage). Man mano che i messaggi vengono aggiunti al batch, vengono rimossi dalla coda .NET. 
    1. Invia il batch di messaggi all'argomento del bus di servizio usando il metodo [ServiceBusSender. SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) .

### <a name="update-the-main-method"></a>Aggiornare il metodo Main
Sostituire il metodo `Main()` con il metodo `Main` **asincrono** seguente. Questo metodo chiama entrambi i metodi per inviare un singolo messaggio e un batch di messaggi all'argomento.  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>Testare l'app per inviare messaggi all'argomento
1. Eseguire l'applicazione. Dovrebbe venire visualizzato l'output seguente:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. Nel portale di Azure seguire questa procedura:
    1. Passare allo spazio dei nomi del bus di servizio. 
    1. Nella pagina **Panoramica** passare alla scheda **Argomenti** nel riquadro centrale inferiore e selezionare l'argomento del bus di servizio. Nell'esempio seguente è `mytopic`.
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Selezionare l'argomento":::
    1. Nella pagina **Argomento del bus di servizio**, nel grafico **Messaggi** della sezione **Metriche** in basso, sono presenti quattro messaggi in ingresso per l'argomento. Se il valore non viene visualizzato, attendere alcuni minuti e aggiornare la pagina per vedere il grafico aggiornato. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Messaggi inviati all'argomento" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Selezionare la sottoscrizione nel riquadro in basso. Nell'esempio seguente è **S1**. Nella pagina **Sottoscrizione del bus di servizio** il valore visualizzato per **Numero di messaggi attivi** è **4**. La sottoscrizione ha ricevuto i quattro messaggi inviati all'argomento, ma non sono stati ancora prelevati da nessun destinatario. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Messaggi ricevuti nella sottoscrizione" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione

1. Aggiungere i metodi seguenti alla classe `Program` per gestire i messaggi ed eventuali errori. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Aggiungere il metodo `ReceiveMessagesFromSubscriptionAsync` seguente alla classe `Program`.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```

    Ecco i passaggi importanti dal codice:
    1. Crea un oggetto [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) usando la stringa di connessione allo spazio dei nomi. 
    1. Richiama il metodo [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) sull' `ServiceBusClient` oggetto per creare un oggetto [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) per l'argomento del bus di servizio specificato e la combinazione di sottoscrizioni. 
    1. Specifica i gestori per gli eventi [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) e [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) dell' `ServiceBusProcessor` oggetto. 
    1. Avvia l'elaborazione dei messaggi richiamando [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) sull' `ServiceBusProcessor` oggetto. 
    1. Quando l'utente preme un tasto per terminare l'elaborazione, richiama [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) sull' `ServiceBusProcessor` oggetto. 
1. Aggiungere una chiamata al metodo `ReceiveMessagesFromSubscriptionAsync` nel metodo `Main`. Impostare come commento il metodo `SendMessagesToTopicAsync` se si vuole testare solo la ricezione di messaggi. In caso contrario, vengono visualizzati altri quattro messaggi inviati all'argomento. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Eseguire l'app
Eseguire l'applicazione. Attendere un minuto e quindi premere un tasto qualsiasi per interrompere la ricezione di messaggi. Verrà visualizzato l'output seguente (barra spaziatrice come tasto). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Controllare di nuovo il portale. 

- Nella pagina **Argomento del bus di servizio** il grafico **Messaggi** mostra otto messaggi in ingresso e otto in uscita. Se questi valori non vengono visualizzati, attendere alcuni minuti e aggiornare la pagina per vedere il grafico aggiornato. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Messaggi inviati e ricevuti" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- Nella pagina **Sottoscrizione del bus di servizio** il valore visualizzato per **Numero di messaggi attivi** è zero. Il motivo è che il destinatario ha ricevuto i messaggi inviati da questa sottoscrizione e li ha completati. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Numero di messaggi attivi nella sottoscrizione alla fine" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione e gli esempi seguenti:

- [Libreria client del bus di servizio di Azure per .NET - Leggimi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Esempi .NET per il bus di servizio di Azure su GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Riferimento alle API .NET](/dotnet/api/azure.messaging.servicebus?preserve-view=true)