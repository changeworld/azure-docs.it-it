---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f3d63180219caa99fed22a5f8249de7e8f1a3eea
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097839"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Negli insiemi di credenziali delle chiavi deve essere abilitata la protezione dalla rimozione definitiva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |L'eliminazione dolosa di un insieme di credenziali delle chiavi può causare la perdita permanente di dati. Un utente malintenzionato interno all'organizzazione può potenzialmente eliminare e rimuovere definitivamente gli insiemi di credenziali delle chiavi. La protezione dalla rimozione definitiva consente di rispondere a questi tipi di attacco imponendo un periodo di conservazione obbligatorio per gli insiemi di credenziali delle chiavi eliminati temporaneamente. Nessuno all'interno dell'organizzazione né Microsoft sarà in grado di rimuovere definitivamente gli insiemi di credenziali delle chiavi durante il periodo di conservazione associato all'eliminazione temporanea. |Audit, Deny, Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
