---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 22a9cf3338f422341928a77f2bf14c497aa2ba31
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563778"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet/).
- Installa [interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) della riga di comando di Azure 

## <a name="create-azure-communication-resource"></a>Creare una risorsa di Servizi di comunicazione di Azure

Per creare una risorsa di servizi di comunicazione di Azure, [accedere all'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli). È possibile eseguire questa operazione tramite il terminale usando il ```az login``` comando e fornendo le proprie credenziali. Eseguire il comando seguente per creare la risorsa:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Se si vuole selezionare una sottoscrizione specifica, è anche possibile specificare il ```--subscription``` flag e fornire l'ID sottoscrizione.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

È possibile configurare la risorsa di servizi di comunicazione con le opzioni seguenti:

* Gruppo di risorse
* Il nome della risorsa di Servizi di comunicazione
* La geografia a cui verrà associata la risorsa

Nel passaggio successivo è possibile assegnare tag alla risorsa. È possibile usare i tag per organizzare le risorse di Azure. Per altre informazioni sui tag, vedere la [documentazione relativa all'assegnazione di tag a una risorsa](../../../azure-resource-manager/management/tag-resources.md).

## <a name="manage-your-communication-services-resource"></a>Gestire la risorsa di Servizi di comunicazione

Per aggiungere tag alla risorsa di servizi di comunicazione, eseguire i comandi seguenti. È anche possibile fare riferimento a una sottoscrizione specifica.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

Per informazioni sui comandi aggiuntivi, vedere [AZ Communication](/cli/azure/ext/communication/communication).
