---
title: Abilitare il rilevamento dei messaggi duplicati - bus di servizio di Azure
description: Questo articolo illustra come abilitare il rilevamento dei messaggi duplicati usando portale di Azure, PowerShell, l'interfaccia della riga di comando e i linguaggi di programmazione (C#, Java, Python e JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755250"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Abilitare il rilevamento di messaggi duplicati per bus di servizio di Azure coda o un argomento
Quando si abilita il rilevamento dei duplicati per una coda o un argomento, bus di servizio di Azure mantiene una cronologia di tutti i messaggi inviati alla coda o all'argomento per un periodo di tempo configurato. Durante questo intervallo, la coda o l'argomento non archivia alcun messaggio duplicato. L'abilitazione di questa proprietà garantisce il recapito di una sola volta in un intervallo di tempo definito dall'utente. Per altre informazioni, vedere Rilevamento [duplicati.](duplicate-detection.md) Questo articolo illustra diversi modi per abilitare il rilevamento di messaggi duplicati per una coda o un argomento del bus di servizio. 


> [!NOTE]
> - Il livello Basic del bus di servizio non supporta il rilevamento dei duplicati. I livelli Standard e Premium supportano il rilevamento dei duplicati. Per le differenze tra questi livelli, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).
> - Non è possibile abilitare o disabilitare il rilevamento dei duplicati dopo la creazione della coda o dell'argomento. È possibile eseguire questa operazione solo al momento della creazione della coda o dell'argomento. 

## <a name="using-azure-portal"></a>Uso del portale di Azure
Quando si crea **una coda** nel portale di Azure, selezionare Abilita **rilevamento duplicati** come illustrato nell'immagine seguente. È possibile configurare le dimensioni della finestra di rilevamento duplicati durante la creazione di una coda o di un argomento. 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="Abilitare il rilevamento dei duplicati al momento della creazione della coda":::

Quando si crea un argomento nella portale di Azure, selezionare Abilita rilevamento **duplicati** come illustrato nell'immagine seguente. 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="Abilitare il rilevamento dei duplicati al momento della creazione dell'argomento":::

È anche possibile configurare questa impostazione per una coda o un argomento esistente, se al momento della creazione è stato abilitato il rilevamento dei duplicati. 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>Aggiornare le dimensioni della finestra di rilevamento duplicati per una coda o un argomento esistente
Per modificare le dimensioni della finestra di rilevamento duplicati  per una  coda o un argomento esistente, nella pagina Panoramica selezionare Modifica per **Finestra rilevamento duplicati**.  

#### <a name="queue"></a>Coda
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="Impostare le dimensioni della finestra di rilevamento duplicati per una coda":::

#### <a name="topic"></a>Argomento
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="Impostare le dimensioni della finestra di rilevamento duplicati per un argomento":::


## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Per **creare una coda con il rilevamento duplicati abilitato,** usare il comando con impostato su [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-duplicate-detection` `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Per **creare un argomento con il rilevamento duplicati abilitato,** usare il comando con impostato su [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-duplicate-detection` `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Gli esempi precedenti impostano anche le dimensioni della finestra di rilevamento duplicati usando il `--duplicate-detection-history-time-window` parametro . Le dimensioni della finestra sono impostate su un giorno. Il valore predefinito è 10 minuti e il valore massimo consentito è sette giorni.

Per **aggiornare una coda con una nuova dimensione della finestra di rilevamento,** usare [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) il comando con il parametro `--duplicate-detection-history-time-window` . In questo esempio le dimensioni della finestra vengono aggiornate a sette giorni. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

Analogamente, per aggiornare un argomento con una nuova dimensione della **finestra di rilevamento,** usare [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) il comando con il parametro `--duplicate-detection-history-time-window` . In questo esempio le dimensioni della finestra vengono aggiornate a sette giorni.

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Per **creare una coda con il rilevamento duplicati abilitato,** usare il comando con impostato su [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-RequiresDuplicateDetection` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

Per **creare un argomento con il rilevamento duplicati abilitato,** usare il comando con impostato su [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-RequiresDuplicateDetection` `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

Gli esempi precedenti impostano anche le dimensioni della finestra di rilevamento duplicati usando il `-DuplicateDetectionHistoryTimeWindow` parametro . Le dimensioni della finestra sono impostate su un giorno. Il valore predefinito è 10 minuti e il valore massimo consentito è sette giorni.

Per **aggiornare una coda con una nuova dimensione della finestra di rilevamento,** vedere l'esempio seguente.  In questo esempio le dimensioni della finestra vengono aggiornate a sette giorni.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

Per **aggiornare un argomento con una nuova dimensione della finestra di rilevamento,** vedere l'esempio seguente. In questo esempio le dimensioni della finestra vengono aggiornate a sette giorni.

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
Per **creare una coda con il rilevamento dei duplicati abilitato,** impostare su `requiresDuplicateDetection` nella sezione delle proprietà della `true` coda. Per altre informazioni, vedere [Microsoft.ServiceBus namespaces/queues template reference](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)(Informazioni di riferimento sui modelli di code/spazi dei nomi di Microsoft.ServiceBus). Specificare un valore per la `duplicateDetectionHistoryTimeWindow` proprietà per impostare le dimensioni della finestra di rilevamento duplicati. Nell'esempio seguente è impostato su un giorno.  

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

Per **creare un argomento con il rilevamento dei duplicati abilitato,** impostare su `requiresDuplicateDetection` nella sezione delle proprietà `true` dell'argomento. Per altre informazioni, vedere [Microsoft.ServiceBus namespaces/topics template reference (Informazioni di riferimento](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json)sui modelli di Microsoft.ServiceBus). 

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
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