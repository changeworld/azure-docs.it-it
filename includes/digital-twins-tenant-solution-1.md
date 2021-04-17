---
author: baanders
description: file di inclusione che descrive una soluzione token per la limitazione tra tenant con Gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 5bc7e8af356ebe5968fe8a74783edc215788e5b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589355"
---
Un modo per eseguire questa operazione è con il comando dell'interfaccia della riga di comando seguente, dove è l'ID del tenant Azure AD `<home-tenant-ID>` che contiene l'istanza Gemelli digitali di Azure seguente:

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

Dopo la richiesta, l'identità riceverà un token emesso per la risorsa Azure AD, che ha un'attestazione id tenant corrispondente *https://digitaltwins.azure.net* all'istanza Gemelli digitali di Azure locale. L'uso di questo token nelle richieste API o con il codice deve consentire all'identità federata di accedere Gemelli digitali di Azure `Azure.Identity` risorsa.