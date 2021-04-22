---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7839d6be9eab20253c1518a550bcb2b6ebd3dcc4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866758"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I processi di Azure Data Box devono abilitare la crittografia doppia per i dati inattivi nel dispositivo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Abilita un secondo livello di crittografia basata sul software per i dati inattivi nel dispositivo. Il dispositivo è già protetto tramite la crittografia Advanced Encryption Standard a 256 bit per i dati inattivi. Questa opzione aggiunge un secondo livello di crittografia dei dati. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box processi deve usare una chiave gestita dal cliente per crittografare la password di sblocco del dispositivo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Usare una chiave gestita dal cliente per controllare la crittografia della password di sblocco del dispositivo per Azure Data Box. Le chiavi gestite dal cliente consentono inoltre di gestire l'accesso alla password di sblocco del dispositivo da parte del servizio Data Box per preparare il dispositivo e copiare i dati in modo automatico. I dati inattivi nel dispositivo stesso sono già crittografati tramite la crittografia Advanced Encryption Standard a 256 bit e la password di sblocco del dispositivo è crittografata per impostazione predefinita con una chiave gestita da Microsoft. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
