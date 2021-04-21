---
title: Abilitare il partizionamento bus di servizio di Azure code e argomenti
description: Questo articolo illustra come abilitare il partizionamento in code e argomenti di bus di servizio di Azure usando portale di Azure, PowerShell, l'interfaccia della riga di comando e i linguaggi di programmazione (C#, Java, Python e JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: fb704f784d490cb73c14fc73b1a6c4368d16acbc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755241"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Abilitare il partizionamento per una bus di servizio di Azure o un argomento
Le partizioni del bus di servizio consentono il partizionamento di code e argomenti, o entità di messaggistica, tra più broker messaggi e archivi di messaggistica. Il partizionamento indica che la velocità effettiva complessiva di un'entità partizionata non è più limitata dalle prestazioni di un singolo broker messaggi o archivio di messaggistica. Inoltre, un'interruzione temporanea di un archivio di messaggistica non rende non disponibile una coda o un argomento partizionato. Le code e gli argomenti partizionati possono contenere tutte le funzionalità avanzate del bus di servizio, ad esempio il supporto delle transazioni e delle sessioni. Per altre informazioni, vedere [Code e argomenti partizionati.](service-bus-partitioning.md) Questo articolo illustra diversi modi per abilitare il rilevamento di messaggi duplicati per una coda o un argomento del bus di servizio. 

> [!IMPORTANT]
> - Il partizionamento è disponibile alla creazione dell'entità per tutte le code e gli argomenti in SKU di base o standard. Non è disponibile per lo SKU di messaggistica Premium, ma tutte le entità partizionate esistenti in precedenza negli spazi dei nomi Premium continuano a funzionare come previsto.
> - Non è possibile modificare l'opzione di partizionamento in alcuna coda o argomento esistente. È possibile impostare l'opzione solo quando si crea una coda o un argomento. 

## <a name="using-azure-portal"></a>Uso del portale di Azure
Quando si crea **una** coda nel portale di Azure, selezionare **Abilita partizionamento** come illustrato nell'immagine seguente. 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="Abilitare il partizionamento al momento della creazione della coda":::

Quando si crea un argomento nel portale di Azure, selezionare **Abilita partizionamento** come illustrato nell'immagine seguente. 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="Abilitare il partizionamento al momento della creazione dell'argomento":::

## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Per **creare una coda con il partizionamento abilitato,** usare il comando con impostato su [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-partitioning` `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

Per **creare un argomento con il partizionamento abilitato,** usare il comando con impostato su [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-partitioning` `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Per **creare una coda con il partizionamento abilitato,** usare il comando con impostato su [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-EnablePartitioning` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

Per **creare un argomento con il partizionamento abilitato,** usare il comando con impostato su [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-EnablePartitioning` `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
Per **creare una coda con il partizionamento abilitato,** impostare su nella sezione delle proprietà della `enablePartitioning` `true` coda. Per altre informazioni, vedere [Microsoft.ServiceBus namespaces/queues template reference](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)(Informazioni di riferimento sui modelli di code/spazi dei nomi di Microsoft.ServiceBus). 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

Per **creare un argomento con il rilevamento duplicati abilitato,** impostare su nella sezione delle proprietà `enablePartitioning` `true` dell'argomento. Per altre informazioni, vedere [Microsoft.ServiceBus namespaces/topics template reference](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json). 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>Passaggi successivi
Provare gli esempi nel linguaggio preferito per esplorare bus di servizio di Azure funzionalità. 

- [bus di servizio di Azure esempi di librerie client per Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [bus di servizio di Azure esempi di librerie client per Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [bus di servizio di Azure di libreria client per JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [bus di servizio di Azure esempi di librerie client per TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Esempi di Azure.Messaging.ServiceBus per .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Di seguito sono riportati esempi per le librerie client .NET e Java precedenti:
- [Esempi di Microsoft.Azure.ServiceBus per .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Esempi di azure-servicebus per Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)