---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88602229"
---
<!-- Create a resource group -->

Usare il comando seguente per creare un gruppo di risorse. Selezionare l'area geografica che verrà usata per archiviare i contenuti multimediali e i record di metadati per l'account Servizi multimediali. Questa area verrà usata per elaborare e riprodurre in streaming il contenuto multimediale.

```azurecli
az group create --name amsResourceGroup --location westus2
```
