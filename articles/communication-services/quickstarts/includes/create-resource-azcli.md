---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: 32d869e6285e76b57a7b8e462e8110a43de82dd9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656577"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Creare una risorsa di Servizi di comunicazione di Azure

Per creare una risorsa di servizi di comunicazione di Azure, [accedere all'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli), quindi eseguire il comando seguente:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

È possibile configurare la risorsa di servizi di comunicazione con le opzioni seguenti:

* Gruppo di risorse
* Il nome della risorsa di Servizi di comunicazione
* La geografia a cui verrà associata la risorsa

Nel passaggio successivo è possibile assegnare tag alla risorsa. È possibile usare i tag per organizzare le risorse di Azure. Per altre informazioni sui tag, vedere la [documentazione relativa all'assegnazione di tag a una risorsa](../../../azure-resource-manager/management/tag-resources.md).

## <a name="manage-your-communication-services-resource"></a>Gestire la risorsa di Servizi di comunicazione

Per aggiungere tag alla risorsa di servizi di comunicazione, eseguire i comandi seguenti:

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

Per informazioni sui comandi aggiuntivi, vedere [AZ Communication](/cli/azure/ext/communication/communication).