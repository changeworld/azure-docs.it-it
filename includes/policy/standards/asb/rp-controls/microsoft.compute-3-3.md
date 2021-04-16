---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1b7e8fab6aafb599ed49939333ac998564cfa27f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511249"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controlla i computer Windows in cui manca uno dei membri specificati nel gruppo Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Richiede che i prerequisiti vengano distribuiti nell'ambito dell'assegnazione dei criteri. Per informazioni dettagliate, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol). I computer saranno non conformi se il gruppo Administrators locale non contiene uno o più membri elencati nel parametro dei criteri. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Controlla i computer Windows in cui sono presenti account in eccesso nel gruppo Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Richiede che i prerequisiti vengano distribuiti nell'ambito dell'assegnazione dei criteri. Per informazioni dettagliate, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol). I computer saranno non conformi se il gruppo Administrators locale contiene membri non elencati nel parametro dei criteri. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[Controlla i computer Windows in cui sono presenti i membri specificati nel gruppo Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Richiede che i prerequisiti vengano distribuiti nell'ambito dell'assegnazione dei criteri. Per informazioni dettagliate, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol). I computer saranno non conformi se il gruppo Administrators locale contiene membri non elencati nel parametro dei criteri. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
