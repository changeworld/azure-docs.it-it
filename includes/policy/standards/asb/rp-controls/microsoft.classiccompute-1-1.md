---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4c54b7f2bcad81b8113e35b115dd66e395c333da
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284754"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Protegge le macchine virtuali da potenziali minacce limitandone l'accesso con i gruppi di sicurezza di rete. Per altre informazioni sul controllo del traffico con i gruppi di sicurezza di rete, vedere [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[L'inoltro IP nella macchina virtuale deve essere disabilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |L'abilitazione dell'inoltro IP sulla scheda di rete di una macchina virtuale consente alla macchina virtuale di ricevere traffico indirizzato ad altre destinazioni. L'inoltro IP è richiesto raramente, ad esempio, quando si usa la macchina virtuale come appliance virtuale di rete, di conseguenza l'abilitazione di questa impostazione deve essere verificata dal team di sicurezza della rete. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[È consigliabile chiudere le porte di gestione nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Le porte di gestione remota aperte espongono la macchina virtuale a un rischio elevato derivante da attacchi di forza bruta basati su Internet per sottrarre le credenziali e ottenere l'accesso di amministratore alla macchina virtuale. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
