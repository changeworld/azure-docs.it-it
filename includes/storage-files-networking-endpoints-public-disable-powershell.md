---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a0a9bc29c3e20a025fb2c46a71c2f134c37bee04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84465119"
---
Il comando di PowerShell seguente negherà tutto il traffico diretto all'endpoint pubblico dell'account di archiviazione. Si noti che il parametro `-Bypass` di questo comando è impostato su `AzureServices`. Questa impostazione consentirà ai servizi Microsoft attendibili, come Sincronizzazione file di Azure, di accedere all'account di archiviazione tramite l'endpoint pubblico.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```