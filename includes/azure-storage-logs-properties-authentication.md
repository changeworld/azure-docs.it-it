---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ca8963ed8928745a6d5918c86021199432339c83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104611862"
---
| Proprietà | Descrizione |
|:--- |:---|
|**identity / type** | Tipo di autenticazione usato per effettuare la richiesta. Ad esempio: `OAuth` , `Kerberos` , `SAS Key` , `Account Key` o `Anonymous` |
|**identity / tokenHash**|Hash SHA-256 del token di autenticazione utilizzato nella richiesta. <br>Quando il tipo di autenticazione è `Account Key` , il formato è "Key1 \| Key2 (SHA256 hash of the Key)". Ad esempio: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>Quando il tipo di autenticazione è `SAS Key` , il formato è "Key1 \| Key2 (hash SHA 256 della chiave), SasSignature (hash SHA 256 del token SAS)". Ad esempio: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. Quando il tipo di autenticazione è `OAuth` , il formato è "hash SHA 256 del token OAuth". ad esempio `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DW25C`<br> Per altri tipi di autenticazione non esiste alcun campo tokenHash. |
|**authorization / action** | Azione assegnata alla richiesta. |
|**authorization / roleAssignmentId** | ID assegnazione di ruolo. Ad esempio: `4e2521b7-13be-4363-aeda-111111111111`.|
|**authorization / roleDefinitionId** | ID definizione del ruolo. Ad esempio: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**principals / id** | ID dell'entità di sicurezza. Ad esempio: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**principals / type** | Tipo dell'entità di sicurezza. Ad esempio: `ServicePrincipal`. |
|**requester / appID** | ID applicazione OAuth (Open Authorization) usato come richiedente. <br> Ad esempio: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**requester / audience** | Destinatari OAuth della richiesta. Ad esempio: `https://storage.azure.com`. |
|**requester / objectId** | ID oggetto OAuth del richiedente. In caso di autenticazione Kerberos, rappresenta l'identificatore di oggetto dell'utente autenticato Kerberos. Ad esempio: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**requester / tenantId** | ID tenant OAuth dell'identità. Ad esempio: `72f988bf-86f1-41af-91ab-222222222222`.|
|**requester / tokenIssuer** | Autorità emittente di token OAuth. Ad esempio: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**requester / upn** | Nome dell'entità utente (UPN) del richiedente. Ad esempio: `someone@contoso.com`. |
|**requester / userName** | Questo campo è solo per uso interno.|
