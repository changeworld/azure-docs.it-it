---
title: Gestire i IoT Central dall'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo descrive come creare e gestire l'applicazione IoT Central tramite l'interfaccia della riga di comando. È possibile visualizzare, modificare e rimuovere l'applicazione tramite l'interfaccia della riga di comando.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: c30781cb83436e15a217a1d43c0e39facae9f52d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770418"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gestire i IoT Central dall'interfaccia della riga di comando di Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Anziché creare e gestire applicazioni IoT Central nel sito Web Azure IoT Central [application manager,](https://aka.ms/iotcentral) è possibile usare l'interfaccia della riga di comando di [Azure](/cli/azure/) per gestire le applicazioni.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Se è necessario eseguire i comandi dell'interfaccia della riga di comando in una sottoscrizione di Azure diversa, vedere [Modificare la sottoscrizione attiva.](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)

## <a name="create-an-application"></a>Creare un'applicazione

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Usare il [comando az iot central app create](/cli/azure/iot/central/app#az_iot_central_app_create) per creare un IoT Central app nella sottoscrizione di Azure. Ad esempio:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Questi comandi creano innanzitutto un gruppo di risorse nell'area Stati Uniti orientali per l'applicazione. La tabella seguente descrive i parametri usati con il **comando az iot central app create:**

| Parametro         | Descrizione |
| ----------------- | ----------- |
| resource-group    | Gruppo di risorse che contiene l'applicazione. Questo gruppo di risorse deve già esistere nella sottoscrizione. |
| posizione          | Per impostazione predefinita, questo comando usa la posizione del gruppo di risorse. Attualmente, è possibile creare un'applicazione IoT Central nelle aree geografiche **Australia**, **Asia Pacifico** **,** Europa , Stati Uniti **,** Regno **Unito** **e** Giappone. |
| name              | Nome dell'applicazione nel portale di Azure. |
| Sottodominio         | Sottodominio nell'URL dell'applicazione. In questo esempio l'URL dell'applicazione è `https://mysubdomain.azureiotcentral.com`. |
| sku               | Attualmente, è possibile usare **ST1** o **ST2.** Vedere [Prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Modello di applicazione da usare. Per altre informazioni, vedere la tabella seguente. |
| display-name      | Nome dell'applicazione visualizzato nell'interfaccia utente. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Visualizzare le proprie applicazioni

Usare il [comando az iot central app list](/cli/azure/iot/central/app#az_iot_central_app_list) per elencare le IoT Central e visualizzare i metadati.

## <a name="modify-an-application"></a>Modificare un'applicazione

Usare il [comando az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) per aggiornare i metadati di un IoT Central app. Ad esempio, per modificare il nome visualizzato dell'applicazione creata:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Rimuovere un'applicazione

Usare il [comando az iot central app delete](/cli/azure/iot/central/app#az_iot_central_app_delete) per eliminare un'IoT Central app. Ad esempio:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central dall'interfaccia della riga di comando di Azure, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)
