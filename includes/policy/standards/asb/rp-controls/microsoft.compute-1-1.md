---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 067b099bee447c559d7196e0f7e72bcaa1d00bf5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867763"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le raccomandazioni di Protezione avanzata adattiva per la rete devono essere applicate alle macchine virtuali con connessione Internet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Centro sicurezza di Azure analizza i modelli di traffico delle macchine virtuali con connessione Internet e fornisce raccomandazioni sulle regole del gruppo di sicurezza di rete al fine di ridurre la superficie di attacco potenziale |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Protegge le macchine virtuali da potenziali minacce limitandone l'accesso con i gruppi di sicurezza di rete. Per altre informazioni sul controllo del traffico con i gruppi di sicurezza di rete, vedere [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[L'inoltro IP nella macchina virtuale deve essere disabilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |L'abilitazione dell'inoltro IP sulla scheda di rete di una macchina virtuale consente alla macchina virtuale di ricevere traffico indirizzato ad altre destinazioni. L'inoltro IP è richiesto raramente, ad esempio, quando si usa la macchina virtuale come appliance virtuale di rete, di conseguenza l'abilitazione di questa impostazione deve essere verificata dal team di sicurezza della rete. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Le porte di gestione delle macchine virtuali devono essere protette tramite un controllo di accesso alla rete JIT](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |I possibili accessi JIT alla rete verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[È consigliabile chiudere le porte di gestione nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Le porte di gestione remota aperte espongono la macchina virtuale a un rischio elevato derivante da attacchi di forza bruta basati su Internet per sottrarre le credenziali e ottenere l'accesso di amministratore alla macchina virtuale. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
