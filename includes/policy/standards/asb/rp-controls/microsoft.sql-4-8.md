---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d2fb4d397d47c24c7bafc3b378943abed66624ee
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442658"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le istanze gestite di SQL devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |L'implementazione di Transparent Data Encryption con la chiave personale offre maggiore trasparenza e controllo su TDE Protector, sicurezza avanzata con un servizio esterno supportato dal modulo di protezione hardware e la promozione della separazione dei compiti. Questa raccomandazione si applica alle organizzazioni con un requisito di conformità correlato. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[I server SQL devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |L'implementazione di Transparent Data Encryption con la chiave personale offre maggiore trasparenza e controllo su TDE Protector, sicurezza avanzata con un servizio esterno supportato dal modulo di protezione hardware e la promozione della separazione dei compiti. Questa raccomandazione si applica alle organizzazioni con un requisito di conformità correlato. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[È consigliabile abilitare Transparent Data Encryption nei database SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Abilitare Transparent Data Encryption per proteggere i dati inattivi e rispettare i requisiti relativi alla conformità |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
