---
title: includere file
description: includere file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d12df7197945a514ed8d3d0dca77271fb4bd0903
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509430"
---
## <a name="prerequisites"></a>Prerequisiti
Se non si ha una [sottoscrizione di Azure](../articles/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio
Seguire le istruzioni disponibili nell'esercitazione [Avvio rapido: Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni all'argomento](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) per eseguire le attività seguenti:

- Creare uno spazio dei nomi del bus di servizio **Premium**. 
- Ottenere la stringa di connessione. 
- Creare un argomento del bus di servizio.
- Creare una sottoscrizione dell'argomento. Per questa esercitazione è richiesta una sola sottoscrizione, quindi non è necessario creare sottoscrizioni S2 e S3. 

## <a name="send-messages-to-the-service-bus-topic"></a>Inviare messaggi all'argomento del bus di servizio
In questo passaggio si usa un'applicazione di esempio per inviare messaggi all'argomento del bus di servizio creato nel passaggio precedente. 

1. Clonare [il repository azure-service-bus di GitHub](https://github.com/Azure/azure-service-bus/).
2. In Visual Studio passare alla cartella *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration* e aprire il file *SBEventGridIntegration.sln*.
3. Nella finestra Esplora soluzioni espandere il progetto **MessageSender** e selezionare **Program.cs**.
4. Sostituire `<SERVICE BUS NAMESPACE - CONNECTION STRING>` con la stringa di connessione allo spazio dei nomi del bus di servizio e `<TOPIC NAME>` con il nome dell'argomento. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Compilare ed eseguire il programma per inviare 5 messaggi di test (`const int numberOfMessages = 5;`) all'argomento del bus di servizio. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Output dell'app console":::
