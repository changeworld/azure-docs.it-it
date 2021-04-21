---
title: Abilitare l'impostazione dei messaggi non recapit bus di servizio di Azure per le code e le sottoscrizioni
description: Questo articolo illustra come abilitare i messaggi non recapitbili per code e sottoscrizioni usando portale di Azure, PowerShell, l'interfaccia della riga di comando e i linguaggi di programmazione (C#, Java, Python e JavaScript)
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 4d4a232d9129cf110a33ece56330ee708f9341b6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819403"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Abilitare i messaggi non recapitbili alla scadenza dei messaggi bus di servizio di Azure code e sottoscrizioni
bus di servizio di Azure code e sottoscrizioni per gli argomenti forniscono una coda secondaria, denominata coda di messaggi non recapitati (DLQ). Non è necessario creare in modo esplicito la coda dei messaggi non recapitare un messaggio non recapitato e non può essere eliminata o gestita indipendentemente dall'entità principale. Lo scopo della coda dei messaggi non recapitabili è conservare i messaggi che non possono essere recapitati ai ricevitori o che non possono essere elaborati. Per altre informazioni, vedere Panoramica delle code dei messaggi [non recapiti](service-bus-dead-letter-queues.md)del bus di servizio. Questo articolo illustra diversi modi per abilitare i messaggi non recapitbili per le code e le sottoscrizioni del bus di servizio. 

## <a name="using-azure-portal"></a>Uso del portale di Azure
Quando si crea  **una coda** o una sottoscrizione per  un argomento nel portale di Azure, selezionare Abilita messaggi non recapitbili alla scadenza del messaggio, come illustrato negli esempi seguenti. 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>Creare una coda con i messaggi non recapitare abilitati
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="Abilitare i messaggi non recapitbili al momento della creazione della coda":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>Creare una sottoscrizione con l'opzione Per i messaggi non recapitare abilitata
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="Abilitare i messaggi non recapitbili al momento della creazione della sottoscrizione":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>Aggiornare l'impostazione di messaggi non recapitare alla scadenza del messaggio per una coda esistente
Nella pagina **Panoramica** per la coda del bus di servizio selezionare il valore corrente per l'impostazione Messaggi non recapitare **alla** scadenza del messaggio. Nell'esempio seguente il valore corrente è **Disabled.** È possibile abilitare o disabilitare i messaggi non recapitbili alla scadenza del messaggio nella finestra popup. 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="Abilitare i messaggi non recapitbili alla scadenza del messaggio per una coda esistente":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>Aggiornare l'impostazione di messaggi non recapitare alla scadenza del messaggio per una sottoscrizione esistente
Nella pagina **Panoramica** della sottoscrizione del bus di servizio selezionare il valore corrente per l'impostazione Messaggi non recapitare **alla** scadenza del messaggio. Nell'esempio seguente il valore corrente è **Disabled.** È possibile abilitare o disabilitare i messaggi non recapitbili alla scadenza del messaggio nella finestra popup. 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="Abilitare i messaggi non recapitbili alla scadenza del messaggio per una sottoscrizione esistente":::

## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Per **creare una coda con messaggi non recapitare** alla scadenza del messaggio abilitata, usare il comando con impostato su [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-dead-lettering-on-message-expiration` `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

Per **abilitare l'impostazione di messaggi non** recapitbili alla scadenza del messaggio per una coda esistente, usare il comando con impostato su [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) `--enable-dead-lettering-on-message-expiration` `true` . 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


Per **creare una sottoscrizione a un argomento con** i messaggi non recapitare alla scadenza del messaggio abilitati, usare il comando con impostato su [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) `--enable-dead-lettering-on-message-expiration` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

Per **abilitare l'impostazione di messaggi non** recapitbili alla scadenza del messaggio per una sottoscrizione a un argomento , usare il comando con impostato su [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) `--enable-dead-lettering-on-message-expiration` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Per **creare una coda con messaggi non recapitare** alla scadenza del messaggio abilitata, usare il comando con impostato su [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-DeadLetteringOnMessageExpiration` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

Per **abilitare l'impostazione di messaggi non** recapitbili alla scadenza del messaggio per una coda esistente, usare il comando come illustrato [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) nell'esempio seguente.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Per **creare una sottoscrizione per un argomento con** i messaggi non recapitare alla scadenza del messaggio abilitati, usare il comando con impostato su [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) `-DeadLetteringOnMessageExpiration` `$True` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

Per **abilitare l'impostazione di messaggi non** recapitbili alla scadenza del messaggio per una sottoscrizione esistente, vedere l'esempio seguente.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
Per **creare una coda con messaggi non recapitare** alla scadenza del messaggio abilitata, impostare nella sezione delle proprietà della coda su `deadLetteringOnMessageExpiration` `true` . Per altre informazioni, vedere Informazioni di riferimento sul modello [microsoft.ServiceBus namespaces/queues.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

Per **creare una sottoscrizione per un argomento con** i messaggi non recapitare alla scadenza del messaggio abilitati, impostare nella sezione delle proprietà della coda su `deadLetteringOnMessageExpiration` `true` . Per altre informazioni, vedere [Microsoft.ServiceBus namespaces/topics/subscriptions template reference](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json). 

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
                "deadLetteringOnMessageExpiration": true
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
Provare gli esempi nel linguaggio scelto per esplorare bus di servizio di Azure funzionalità. 

- [bus di servizio di Azure esempi di librerie client per Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [bus di servizio di Azure di libreria client per Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [bus di servizio di Azure di libreria client per JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [bus di servizio di Azure di libreria client per TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Esempi di Azure.Messaging.ServiceBus per .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Di seguito sono riportati esempi per le librerie client .NET e Java precedenti:
- [Esempi di Microsoft.Azure.ServiceBus per .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Esempi di azure-servicebus per Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)