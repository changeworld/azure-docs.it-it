---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831205"
---
Se si prevede di continuare con altri articoli su Plug and Play IoT, è possibile conservare e riutilizzare le risorse usate in questo articolo. In caso contrario, è possibile eliminare le risorse create in questo articolo per evitare addebiti aggiuntivi.

È possibile eliminare l'hub e il dispositivo registrato contemporaneamente eliminando l'intero gruppo di risorse con il comando dell'interfaccia della riga di comando di Azure seguente. Non usare questo comando se queste risorse condividono un gruppo di risorse con altre risorse che si vogliono conservare.

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

Per eliminare solo l'hub IoT, eseguire questo comando usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Per eliminare solo l'identità del dispositivo registrato con l'hub IoT, eseguire questo comando usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

È anche possibile rimuovere dal computer di sviluppo i file di esempio clonati.
