---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 191f6fe6dc8086dbf187851c542fc75b9d0e5643
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429139"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le variabili dell'account di automazione devono essere crittografate](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |È importante abilitare la crittografia degli asset della variabile dell'account di Automazione quando si archiviano dati sensibili |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Gli account di automazione di Azure devono usare chiavi gestite dal cliente per crittografare i dati inattivi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |Usare chiavi gestite dal cliente per gestire la crittografia degli account di automazione di Azure. Per impostazione predefinita, i dati dei clienti vengono crittografati con chiavi gestite dal servizio, ma le chiavi gestite dal cliente sono in genere necessarie per soddisfare gli standard di conformità normativi. Le chiavi gestite dal cliente consentono la crittografia dei dati con una chiave di Azure Key Vault creata e di proprietà dell'utente. L'utente dispone del controllo completo e della piena responsabilità in merito al ciclo di vita della chiave, incluse le operazioni di rotazione e gestione. Ulteriori informazioni sono disponibili all'indirizzo [https://aka.ms/automation-cmk](https://aka.ms/automation-cmk) . |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
