---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070215"
---
Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Usare Azure PowerShell il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) per creare un gruppo di risorse denominato *myResourceGroup* nella località *eastus* . 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
