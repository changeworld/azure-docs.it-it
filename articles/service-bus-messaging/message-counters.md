---
title: bus di servizio di Azure - Numero di messaggi
description: Recuperare il numero di messaggi mantenuti in code e sottoscrizioni usando Azure Resource Manager e le API NamespaceManager bus di servizio di Azure namespacemanager.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5fc7211673badfde664d77128f9d79523926ccc9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814605"
---
# <a name="get-message-counters"></a>Ottenere i contatori dei messaggi
Questo articolo illustra diversi modi per ottenere i conteggi dei messaggi seguenti per una coda o una sottoscrizione. Il conteggio dei messaggi attivi è utile per stabilire se una coda crea un backlog che richiede più risorse per essere elaborato rispetto a quelle attualmente distribuite. 

| Contatore | Descrizione |
| ----- | ---------- | 
| ActiveMessageCount | Numero di messaggi nella coda o nella sottoscrizione nello stato attivo e pronti per il recapito. |
| ScheduledMessageCount | Numero di messaggi nello stato pianificato. |
| DeadLetterMessageCount | Numero di messaggi nella coda dei messaggi non recapitato. |
| TransferMessageCount | Numero di messaggi in sospeso trasferiti in un'altra coda o argomento. |
| TransferDeadLetterMessageCount | Numero di messaggi che non sono stati trasferiti in un'altra coda o argomento e che sono stati spostati nella coda dei messaggi non recapitare. |

Se un'applicazione vuole ridimensionare le risorse in base alla lunghezza della coda, deve farlo in maniera limitata. L'acquisizione dei contatori di messaggi è un'operazione impegnativa all'interno del broker messaggi e, se viene eseguita frequentemente, influisce direttamente e negativamente sulle prestazioni dell'entità.

> [!NOTE]
> I messaggi inviati a un argomento del bus di servizio vengono inoltrati alle sottoscrizioni per tale argomento. Pertanto, il numero di messaggi attivi per l'argomento stesso è 0, poiché tali messaggi sono stati inoltrati correttamente alla sottoscrizione. Ottenere il numero di messaggi nella sottoscrizione e verificare che sia maggiore di 0. Anche se vengono visualizzati messaggi nella sottoscrizione, vengono effettivamente archiviati in un archivio di proprietà dell'argomento. Se si osservano le sottoscrizioni, il conteggio dei messaggi sarà diverso da zero (che somma fino a 323 MB di spazio per l'intera entità).


## <a name="using-azure-portal"></a>Uso del portale di Azure
Passare allo spazio dei nomi e selezionare la coda. I contatori dei messaggi vengono visualizzati **nella pagina Panoramica** per la coda.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="Contatori dei messaggi nella pagina di panoramica della coda":::

Passare allo spazio dei nomi, selezionare l'argomento e quindi selezionare la sottoscrizione per l'argomento. I contatori dei messaggi vengono visualizzati **nella pagina Panoramica** per la coda.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="Contatori dei messaggi nella pagina di panoramica della sottoscrizione":::

## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Usare il [`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) comando per ottenere i dettagli del numero di messaggi per una coda, come illustrato nell'esempio seguente. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

Di seguito è riportato un output di esempio:

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

Usare il [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) comando per ottenere i dettagli del numero di messaggi per una sottoscrizione, come illustrato nell'esempio seguente. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Con PowerShell è possibile ottenere i dettagli del numero di messaggi per una coda come indicato di seguito:

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

Ecco l'output di esempio:

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

È possibile ottenere i dettagli del numero di messaggi per una sottoscrizione come indicato di seguito:

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

`MessageCountDetails`L'oggetto restituito ha le proprietà `ActiveMessageCount` seguenti: `DeadLetterMessageCount` , , , `ScheduledMessageCount` , `TransferDeadLetterMessageCount` `TransferMessageCount` . 

## <a name="next-steps"></a>Passaggi successivi

Provare gli esempi nel linguaggio preferito per esplorare le bus di servizio di Azure funzionalità. 

- [bus di servizio di Azure di librerie client per Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [bus di servizio di Azure di libreria client per Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [bus di servizio di Azure di libreria client per JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [bus di servizio di Azure di libreria client per TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Esempi di Azure.Messaging.ServiceBus per .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Di seguito sono riportati esempi per le librerie client .NET e Java precedenti:
- [Esempi di Microsoft.Azure.ServiceBus per .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Esempi di azure-servicebus per Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
