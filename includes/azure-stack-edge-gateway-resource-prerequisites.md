---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: c29c7abf0964edb7a5427d8c2cfe62e066d13bfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98900999"
---
Prima di iniziare, verificare che:

* La sottoscrizione di Microsoft Azure sia abilitata per una risorsa Azure Stack Edge. Assicurarsi di aver usato una sottoscrizione supportata, ad esempio [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) o [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Si abbia accesso di tipo Proprietario o Collaboratore a livello di gruppo di risorse per le risorse Azure Stack Edge/gateway di archiviazione di Azure, hub IoT e Archiviazione di Azure.

  * Per creare una risorsa Azure Stack Edge, è necessario avere autorizzazioni di collaboratore (o superiore) con ambito a livello di gruppo di risorse. È anche necessario assicurarsi che il provider `Microsoft.DataBoxEdge` sia registrato. Per informazioni sulla registrazione, vedere [Registrare i provider di risorse](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Per creare qualsiasi risorsa hub IoT, assicurarsi che il provider Microsoft.Devices sia registrato. Per informazioni sulla registrazione, vedere [Registrare i provider di risorse](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Per creare una risorsa account di archiviazione, è ugualmente necessario l'accesso di tipo Collaboratore o superiore con ambito a livello di gruppo di risorse. Archiviazione di Azure è un provider di risorse registrato per impostazione predefinita.
* Si abbia accesso all'API Graph di Azure Active Directory Graph come utente o amministratore. Per altre informazioni, vedere [API Graph di Azure Active Directory](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.