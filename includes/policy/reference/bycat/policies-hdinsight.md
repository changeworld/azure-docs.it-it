---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0789a0d7a13884f273bb6538a87c1b841782246d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807233"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I cluster HDInsight di Azure devono usare chiavi gestite dal cliente per crittografare i dati inattivi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Usare chiavi gestite dal cliente (CMK) per gestire la crittografia dei cluster HDInsight di Azure. Per impostazione predefinita, i dati dei clienti sono crittografati tramite chiavi gestite dal servizio, ma in genere sono richieste chiavi gestite dal cliente per soddisfare gli standard di conformità alle normative. Le chiavi gestite dal cliente consentono di crittografare i dati con una chiave di Azure Key Vault creata dall'utente e di sua proprietà. L'utente dispone del controllo completo e della piena responsabilità in merito al ciclo di vita della chiave, incluse le operazioni di rotazione e gestione. Per altre informazioni sulla crittografia con chiavi gestite dal cliente, vedere [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk).  |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[I cluster HDInsight di Azure devono usare la crittografia nell'host per crittografare i dati inattivi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |L'abilitazione della crittografia nell'host consente di proteggere e salvaguardare i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Quando si Abilita la crittografia in host, i dati archiviati nell'host della macchina virtuale vengono crittografati a riposo e i flussi vengono crittografati nel servizio di archiviazione. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[I cluster HDInsight di Azure devono usare la crittografia in transito per crittografare le comunicazioni tra i nodi del cluster di Azure HDInsight](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |I dati possono essere manomessi durante la trasmissione tra i nodi del cluster HDInsight di Azure. L'abilitazione della crittografia in transito risolve i problemi di utilizzo improprio e manomissioni durante la trasmissione. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
