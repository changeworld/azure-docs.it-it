---
title: Abilitare bus di servizio di Azure sessioni di messaggi | Microsoft Docs
description: Questo articolo illustra come abilitare sessioni di messaggi usando portale di Azure, PowerShell, interfaccia della riga di comando e linguaggi di programmazione (C#, Java, Python e JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: dc2667b746a57c7f2e4ac5faec88c4540bed1180
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755249"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>Abilitare le sessioni di messaggi per una bus di servizio di Azure o una sottoscrizione
bus di servizio di Azure sessioni consentono la gestione congiunta e ordinata di sequenze non associato di messaggi correlati. Le sessioni possono essere usate in criteri **First in, First out (FIFO)** e di **richiesta-risposta**. Per altre informazioni, vedere [Sessioni di messaggi](message-sessions.md). Questo articolo illustra diversi modi per abilitare le sessioni per una coda o una sottoscrizione del bus di servizio. 

> [!IMPORTANT]
> - Il livello Basic del bus di servizio non supporta le sessioni. I livelli Standard e Premium supportano le sessioni. Per le differenze tra questi livelli, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).
> - Non è possibile abilitare o disabilitare le sessioni di messaggi dopo la creazione della coda o della sottoscrizione. È possibile eseguire questa operazione solo al momento della creazione della coda o della sottoscrizione. 

## <a name="using-azure-portal"></a>Uso del portale di Azure
Quando si crea **una coda** nel portale di Azure, selezionare **Abilita sessioni** come illustrato nell'immagine seguente. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Abilitare la sessione al momento della creazione della coda":::

Quando si crea una sottoscrizione per un argomento nel portale di Azure, selezionare **Abilita sessioni** come illustrato nell'immagine seguente. 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Abilitare la sessione al momento della creazione della sottoscrizione":::

## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Per **creare una coda con le sessioni di messaggi abilitate,** usare il comando con [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) impostato su `--enable-session` `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

Per **creare una sottoscrizione per un argomento con sessioni di messaggi abilitate,** usare il [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) comando con impostato su `--enable-session` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Per **creare una coda con le sessioni di messaggi abilitate,** usare il comando con [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) impostato su `-RequiresSession` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

Per **creare una sottoscrizione per un argomento con sessioni di messaggi abilitate,** usare il [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) comando con impostato su `-RequiresSession` `true` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
Per **creare una coda con le sessioni di messaggi abilitate,** impostare su `requiresSession` nella sezione delle proprietà della `true` coda. Per altre informazioni, vedere Informazioni di riferimento sul modello [microsoft.ServiceBus namespaces/queues.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

Per **creare una sottoscrizione per un argomento con sessioni di messaggi abilitate,** impostare `requiresSession` su nella sezione delle proprietà della `true` sottoscrizione. Per altre informazioni, vedere [Microsoft.ServiceBus namespaces/topics/subscriptions template reference](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)(Informazioni di riferimento sui modelli di Microsoft.ServiceBus). 

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
                "requiresSession": true
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
Provare gli esempi nel linguaggio preferito per esplorare bus di servizio di Azure funzionalità. 

- [bus di servizio di Azure di librerie client per Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [bus di servizio di Azure di libreria client per Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [bus di servizio di Azure di libreria client per JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [bus di servizio di Azure di libreria client per TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Esempi di Azure.Messaging.ServiceBus per .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Di seguito sono riportati esempi per le librerie client .NET e Java precedenti:
- [Esempi di Microsoft.Azure.ServiceBus per .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Esempi di azure-servicebus per Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)