---
title: Usare Azure Service Bus Explorer per eseguire operazioni sui dati nel bus di servizio (anteprima)
description: Questo articolo fornisce informazioni su come usare Azure Service Bus Explorer basato sul portale per accedere ai dati del bus di servizio di Azure.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 06bc2ffffc6b467aa730e16599099bc95117dac9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092218"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Usare Service Bus Explorer per eseguire operazioni sui dati nel bus di servizio (anteprima)

## <a name="overview"></a>Panoramica

Il bus di servizio di Azure consente alle applicazioni client mittente e ricevente di separare la logica di business tramite la familiare semantica point-to-point (coda) e di pubblicazione-sottoscrizione (argomento-sottoscrizione).

Le operazioni eseguite su uno spazio dei nomi del bus di servizio di Azure sono di due tipi 
   * Operazioni di gestione: creazione, aggiornamento ed eliminazione di spazio dei nomi, code, argomenti e sottoscrizioni del bus di servizio.
   * Operazioni sui dati: invio e ricezione di messaggi da code, argomenti e sottoscrizioni.

Azure Service Bus Explorer espande le funzionalità del portale oltre le operazioni di gestione per supportare le operazioni sui dati (invio, ricezione, anteprima) su code, argomenti e sottoscrizioni (e le relative sottoentità per i messaggi non recapitabili), direttamente dal portale di Azure.

> [!NOTE]
> Questo articolo evidenzia le funzionalità di Azure Service Bus Explorer disponibili nel portale di Azure.
>
> Lo strumento di esplorazione del bus di servizio di Azure è ***non** _ lo strumento OSS di proprietà della community di [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).
>

## <a name="prerequisites"></a>Prerequisiti

Per usare lo strumento Service Bus Explorer, è necessario eseguire le attività seguenti: 

- Fornire uno spazio dei nomi del bus di servizio di Azure.
- Creare una coda per inviare e ricevere un messaggio da o un argomento con una sottoscrizione per testare la funzionalità. Per informazioni su come creare code, argomenti e sottoscrizioni, vedere gli articoli seguenti: 
    - [Avvio rapido - Creare code](service-bus-quickstart-portal.md)
    - [Avvio rapido - Creare argomenti](service-bus-quickstart-topics-subscriptions-portal.md)
- Assicurarsi di essere un membro di uno di questi ruoli nello spazio dei nomi: 
    - [Proprietario dati del bus di servizio](/azure/role-based-access-control/built-in-roles#azure-service-bus-data-owner) 
    - [Collaboratore](/azure/role-based-access-control/built-in-roles#contributor) 
    - [Proprietario](/azure/role-based-access-control/built-in-roles#owner)


## <a name="using-the-service-bus-explorer"></a>Uso di Service Bus Explorer

Per usare Azure Service Bus Explorer, è necessario passare allo spazio dei nomi del bus di servizio in cui eseguire le operazioni di invio, anteprima e ricezione.

Se si sta cercando di eseguire operazioni su una coda, scegliere _ *"code"** dal menu di navigazione. Per eseguire operazioni su un argomento (e le relative sottoscrizioni), scegliere **Argomenti**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="Selezione dell'entità":::

Dopo aver selezionato **Code** o **Argomenti**, selezionare la coda o l'argomento specifico.

Selezionare **Service Bus Explorer (anteprima)** dal menu di spostamento a sinistra

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Menu di spostamento a sinistra di Service Bus Explorer":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Invio di un messaggio a una coda o a un argomento

Per inviare un messaggio a una **coda** o a un **argomento**, fare clic sulla scheda **_Invia_* in Service Bus Explorer.

Per comporre un messaggio: 

1. Selezionare il *tipo di contenuto* _ * in modo che sia "text/plain", "application/xml" o "application/json".
2. Aggiungere il **contenuto** del messaggio. Assicurarsi che corrisponda al **tipo di contenuto** impostato in precedenza.
3. Impostare le **proprietà avanzate** (facoltativo), tra cui ID correlazione, ID messaggio, etichetta, ReplyTo, durata (TTL) e ora di accodamento pianificata (per i messaggi pianificati).
4. Impostare le **proprietà personalizzate**: può trattarsi di qualsiasi proprietà utente impostata su una chiave del dizionario.

Dopo avere composto il messaggio, fare clic su Invia.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Composizione del messaggio":::

Quando l'operazione di invio viene completata correttamente: 

* Se è stato eseguito l'invio alla coda, verrà incrementato il contatore della metrica **Messaggi attivi**.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* Se è stato eseguito l'invio all'argomento, verrà incrementato il contatore della metrica **Messaggi attivi** per la sottoscrizione a cui è stato indirizzato il messaggio.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>Ricezione di un messaggio da una coda

La funzione di ricezione in Service Bus Explorer consente di ricevere un singolo messaggio alla volta. L'operazione di ricezione viene eseguita usando la modalità **ReceiveAndDelete**.

> [!IMPORTANT]
> Si noti che l'operazione di ricezione eseguita da Service Bus Explorer è un **_Receive distruttivo_* _, ovvero il messaggio viene rimosso dalla coda quando viene visualizzato nello strumento Service Bus Explorer.
>
> Per esplorare i messaggi senza rimuoverli dalla coda, provare a usare la funzionalità di _*_visualizzazione_*_ .
>

Per ricevere un messaggio da una coda (o dalla relativa coda secondaria dei messaggi non recapitabili) 

1. Fare clic sulla scheda _*_Receive_*_ in Service Bus Explorer.
2. Controllare le metriche per verificare se sono presenti _ *messaggi attivi** o **messaggi non recapitabili** da ricevere.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Scegliere tra **_queue_* _ o la coda secondaria _*_DeadLetter_*_ .

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Fare clic sul pulsante _*_Receive_*_ , seguito da _*_Yes_*_ per confermare l'operazione di ricezione ed eliminazione.


Quando l'operazione di ricezione ha esito positivo, i dettagli del messaggio vengono visualizzati nella griglia come illustrato di seguito. È possibile selezionare il messaggio dalla griglia per visualizzarne i dettagli.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Screenshot della finestra code in Esplora bus di servizio di Azure con i dettagli del messaggio visualizzati per un messaggio attivo selezionato nella coda.":::


### <a name="peeking-a-message-from-a-queue"></a>Anteprima di un messaggio da una coda

Con la funzionalità Anteprima, è possibile usare Service Bus Explorer per visualizzare i primi 32 messaggi in una coda o nella coda dei messaggi non recapitabili.

1. Per visualizzare il messaggio in una coda, fare clic sulla scheda _*_Visualizza_*_ in Service Bus Explorer.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Controllare le metriche per verificare se sono presenti _ *messaggi attivi** o **messaggi non recapitabili** da leggere.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Quindi scegliere tra **_queue_* _ o la coda secondaria _*_DeadLetter_*_ .

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Fare clic sul pulsante _*_Visualizza_*_ . 

Al termine dell'operazione di anteprima, nella griglia vengono visualizzati fino a 32 messaggi, come illustrato di seguito. Per visualizzare i dettagli di un determinato messaggio, selezionarlo nella griglia. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> Poiché Peek non è un'operazione distruttiva, il messaggio _ *non verrà* rimosso dalla coda.
>

### <a name="receiving-a-message-from-a-subscription"></a>Ricezione di un messaggio da una sottoscrizione

Analogamente a una coda, l'operazione **_Receive_* _ può essere eseguita su una sottoscrizione o sull'entità DeadLetter. Tuttavia, poiché una sottoscrizione è intrinseca al contesto dell'argomento, l'operazione di ricezione viene eseguita passando a Service Bus Explorer per un argomento specifico.

> [!IMPORTANT]
> Si noti che l'operazione di ricezione eseguita da Service Bus Explorer è una _*_ricezione distruttiva_*_, ovvero il messaggio viene rimosso dalla coda quando viene visualizzato nello strumento Service Bus Explorer.
>
> Per esplorare i messaggi senza rimuoverli dalla coda, provare a usare la funzionalità di _*_visualizzazione_*_ .
>

1. Fare clic sulla scheda _*_ricezione_*_ e selezionare la _*_sottoscrizione_*_ specifica dal selettore a discesa.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. Scegliere tra la _*_sottoscrizione_*_ o la sottoentità _*_DeadLetter_*_ .

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Fare clic sul pulsante _*_Receive_*_ , seguito da _*_Yes_*_ per confermare l'operazione di ricezione ed eliminazione.

Quando l'operazione di ricezione ha esito positivo, il messaggio ricevuto verrà visualizzato nella griglia come illustrato di seguito. Per visualizzare i dettagli del messaggio, fare clic sul messaggio.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Screenshot della scheda ricezione in Esplora bus di servizio di Azure con i dettagli del messaggio visualizzati per un messaggio attivo che è stato ricevuto.":::

### <a name="peeking-a-message-from-a-subscription"></a>Anteprima di un messaggio da una sottoscrizione

Per esplorare semplicemente i messaggi in una sottoscrizione o nella relativa sottoentità DeadLetter, è possibile usare anche la funzionalità _*_Visualizza_*_ nella sottoscrizione.

1. Fare clic sulla scheda _*_Visualizza_*_ e selezionare la _*_sottoscrizione_*_ specifica dal selettore a discesa.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. Scegliere tra la _*_sottoscrizione_*_ o la sottoentità _*_DeadLetter_*_ .

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Fare clic sul pulsante _*_Visualizza_*_ .

Al termine dell'operazione di anteprima, nella griglia vengono visualizzati fino a 32 messaggi, come illustrato di seguito. Per visualizzare i dettagli di un determinato messaggio, selezionarlo nella griglia. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> Poiché Peek non è un'operazione distruttiva, il messaggio _ *non verrà* rimosso dalla coda.
>

## <a name="next-steps"></a>Passaggi successivi

   * Altre informazioni sulle [code](service-bus-queues-topics-subscriptions.md#queues) e gli [argomenti](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) del bus di servizio
   * Altre informazioni sulla creazione di [code del bus di servizio tramite il portale di Azure](service-bus-quickstart-portal.md)
   * Altre informazioni sulla creazione di [argomenti e sottoscrizioni del bus di servizio tramite il portale di Azure](service-bus-quickstart-topics-subscriptions-portal.md)
