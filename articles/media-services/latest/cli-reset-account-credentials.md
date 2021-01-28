---
title: Reimpostare le credenziali dell'account-interfaccia della riga di comando
description: Usare lo script dell'interfaccia della riga di comando di Azure per reimpostare le credenziali dell'account e ottenere le impostazioni di app.config.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6fe958c9300d6a6feeb6926f84dbecf565dfafc1
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954345"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Esempio di interfaccia della riga di comando di Azure: Reimpostare le credenziali dell'account

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come reimpostare le credenziali dell'account e ottenere le impostazioni di app.config.

## <a name="prerequisites"></a>Prerequisiti

[Creare un account di Servizi multimediali di Azure](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script di esempio

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Passaggi successivi

* [az ams](/cli/azure/ams)
* [Reimpostare le credenziali](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
