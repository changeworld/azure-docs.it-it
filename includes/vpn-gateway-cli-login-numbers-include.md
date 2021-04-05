---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 52f828b9ba7bd286184b44a3d843c2cf8c75c592
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92755953"
---
1. Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/) e seguire le istruzioni visualizzate. Per altre informazioni sull'accesso, vedere [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Se si hanno più sottoscrizioni Azure, elencare le sottoscrizioni per l'account.

   ```azurecli
   az account list --all
   ```
3. Specificare la sottoscrizione da usare.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
