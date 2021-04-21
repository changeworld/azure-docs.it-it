---
title: Abilitare l'inoltro automatico per bus di servizio di Azure code e sottoscrizioni
description: Questo articolo illustra come abilitare l'inoltro automatico per code e sottoscrizioni usando portale di Azure, PowerShell, l'interfaccia della riga di comando e i linguaggi di programmazione (C#, Java, Python e JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: e5f69a82aac96deaf96f0ae6aaa1a26d0ee3aaf3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814677"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Abilitare l'inoltro automatico per bus di servizio di Azure code e sottoscrizioni
La funzionalità di inoltro automatico del bus di servizio consente di concatenare una coda o una sottoscrizione a un'altra coda o argomento che fa parte dello stesso spazio dei nomi. Quando l'inoltro automatico è abilitato, il bus di servizio rimuove automaticamente i messaggi inseriti nella prima coda o sottoscrizione (origine) e li inserisce nella seconda coda o argomento (destinazione). È comunque possibile inviare un messaggio direttamente all'entità di destinazione. Per altre informazioni, vedere [Concatenamento di entità del bus di servizio con inoltro automatico.](service-bus-auto-forwarding.md) Questo articolo illustra diversi modi per abilitare l'inoltro automatico per le code e le sottoscrizioni del bus di servizio. 

> [!IMPORTANT]
> Il livello Basic del bus di servizio non supporta la funzionalità di inoltro automatico. I livelli Standard e Premium supportano questa funzionalità. Per le differenze tra questi livelli, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-azure-portal"></a>Uso del portale di Azure
Quando si crea  **una coda** o una sottoscrizione per un argomento nel portale di Azure, selezionare Inoltra messaggi alla coda **o all'argomento,** come illustrato negli esempi seguenti. Specificare quindi se si vuole che i messaggi siano inoltrati a una coda o a un argomento. In questo esempio è selezionata **l'opzione Coda** e viene selezionata una coda dallo stesso spazio dei nomi.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Creare una coda con l'inoltro automatico abilitato
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Abilitare l'inoltro automatico al momento della creazione della coda":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Creare una sottoscrizione per un argomento con l'inoltro automatico abilitato
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Abilitare l'inoltro automatico al momento della creazione della sottoscrizione":::

### <a name="update-the-auto-forward-setting-for-an-existing-queue"></a>Aggiornare l'impostazione di inoltro automatico per una coda esistente
Nella pagina **Panoramica** della coda del bus di servizio selezionare il valore corrente per l'impostazione **Inoltra messaggi a.** Nell'esempio seguente il valore corrente è **Disabled.** Nella finestra **Inoltra messaggi a coda/argomento** è possibile selezionare la coda o l'argomento in cui si vuole inoltrare i messaggi. 

:::image type="content" source="./media/enable-auto-forward/queue-auto-forward.png" alt-text="Abilitare l'inoltro automatico per una coda esistente":::

### <a name="update-the-auto-forward-setting-for-an-existing-subscription"></a>Aggiornare l'impostazione di inoltro automatico per una sottoscrizione esistente
Nella pagina **Panoramica** per la sottoscrizione del bus di servizio selezionare il valore corrente per l'impostazione **Inoltra messaggi a.** Nell'esempio seguente il valore corrente è **Disabled**. Nella finestra **Inoltra messaggi a coda/argomento** è possibile selezionare la coda o l'argomento in cui si vuole inoltrare i messaggi. 

:::image type="content" source="./media/enable-auto-forward/subscription-auto-forward.png" alt-text="Abilitare l'inoltro automatico per una sottoscrizione esistente":::

## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Per **creare una coda con** l'inoltro automatico abilitato, usare il comando con impostato sul nome della coda o dell'argomento a cui si vuole inoltrare i [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--forward-to` messaggi. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

Per **aggiornare l'impostazione** di inoltro automatico per una coda esistente, usare il comando con impostato sul nome della coda o dell'argomento a cui si vuole inoltrare [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) i `--forward-to` messaggi. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```


Per **creare una sottoscrizione a un** argomento con l'inoltro automatico abilitato, usare il comando con impostato sul nome della coda o dell'argomento a cui si vuole inoltrare i [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) `--forward-to` messaggi.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

Per **aggiornare l'impostazione** di inoltro automatico per una sottoscrizione a un argomento , usare il comando con impostato sul nome della coda o dell'argomento a cui si vuole inoltrare [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) i `--forward-to` messaggi.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Per **creare una coda con** l'inoltro automatico abilitato, usare il comando con impostato sul nome della coda o dell'argomento a cui si vuole inoltrare i [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-ForwardTo` messaggi. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

Per **aggiornare l'impostazione di inoltro automatico per una coda esistente,** usare il [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) comando come illustrato nell'esempio seguente.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.ForwardTo='myqueue2'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Per **creare una sottoscrizione per un** argomento con l'inoltro automatico abilitato, usare il comando con impostato sul nome della coda o dell'argomento a cui si vuole inoltrare i [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-ForwardTo` messaggi.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

Per **aggiornare l'impostazione di inoltro automatico per una sottoscrizione esistente,** vedere l'esempio seguente.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.ForwardTo='mytopic2'

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
Per **creare una coda con l'inoltro** automatico abilitato, impostare nella sezione delle proprietà della coda sul nome della coda o dell'argomento a cui si desidera inoltrare i `forwardTo` messaggi. Per altre informazioni, vedere [Microsoft.ServiceBus namespaces/queues template reference](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)(Informazioni di riferimento sui modelli di code/spazi dei nomi di Microsoft.ServiceBus). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

Per **creare una sottoscrizione** per un argomento con l'inoltro automatico abilitato, impostare nella sezione delle proprietà della coda sul nome della coda o dell'argomento a cui si desidera inoltrare i `forwardTo` messaggi. Per altre informazioni, vedere [Microsoft.ServiceBus namespaces/topics/subscriptions template reference](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)(Informazioni di riferimento sui modelli di Microsoft.ServiceBus). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "forwardTo": "myqueue2"
              }
            }
          ]
        }
      ]
    }
  ]
}
```


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