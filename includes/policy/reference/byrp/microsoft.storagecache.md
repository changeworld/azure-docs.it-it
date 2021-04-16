---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a9295290e8b0a26c407295c3b8f1ccb025159eac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504290"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli account cache HPC devono usare la chiave gestita dal cliente per la crittografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Gestire la crittografia dei dati in Cache HPC di Azure con chiavi gestite dal cliente. Per impostazione predefinita, i dati dei clienti vengono crittografati con chiavi gestite dal servizio, ma le chiavi gestite dal cliente sono in genere necessarie per soddisfare gli standard di conformità alle normative. Le chiavi gestite dal cliente consentono di crittografare i dati con Azure Key Vault chiave creata e di proprietà dell'utente. L'utente dispone del controllo completo e della piena responsabilità in merito al ciclo di vita della chiave, incluse le operazioni di rotazione e gestione. |Audit, Disabled, Deny |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
