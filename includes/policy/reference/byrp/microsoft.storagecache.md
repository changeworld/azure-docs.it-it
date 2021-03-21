---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2c1e93c97092d850615bf9fe9811cac841f0f676
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588306"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli account della cache HPC devono usare la chiave gestita dal cliente per la crittografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Gestire la crittografia inattiva della cache HPC di Azure con chiavi gestite dal cliente. Per impostazione predefinita, i dati dei clienti vengono crittografati con chiavi gestite dal servizio, ma le chiavi gestite dal cliente sono in genere necessarie per soddisfare gli standard di conformità normativi. Le chiavi gestite dal cliente consentono la crittografia dei dati con una chiave di Azure Key Vault creata e di proprietà dell'utente. L'utente dispone del controllo completo e della piena responsabilità in merito al ciclo di vita della chiave, incluse le operazioni di rotazione e gestione. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
