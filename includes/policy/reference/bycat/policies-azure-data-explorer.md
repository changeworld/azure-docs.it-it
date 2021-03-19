---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8257b52e394d2381fae5f4a56d221151d657bb8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605386"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La crittografia dei dati inattivi di Esplora dati di Azure deve usare una chiave gestita dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |L'abilitazione della crittografia dei dati inattivi mediante una chiave gestita dal cliente nel cluster di Esplora dati di Azure fornisce un controllo aggiuntivo sulla chiave usata dalla crittografia dei dati inattivi. Questa funzionalità è spesso applicabile ai clienti con requisiti di conformità speciali e richiede un'istanza di Key Vault per la gestione delle chiavi. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[La crittografia dei dischi deve essere abilitata in Esplora dati di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |L'abilitazione della crittografia dei dischi consente di proteggere e tutelare i dati per soddisfare gli impegni dell'organizzazione in termini di sicurezza e conformità. |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[La crittografia doppia deve essere abilitata in Esplora dati di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |L'abilitazione della doppia crittografia consente di proteggere e tutelare i dati per soddisfare gli impegni dell'organizzazione in termini di sicurezza e conformità. Una volta abilitata la doppia crittografia, i dati nell'account di archiviazione vengono crittografati due volte, una volta a livello del servizio e una volta a livello dell'infrastruttura, usando due algoritmi di crittografia diversi e due chiavi diverse. |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[La distribuzione all'interno della rete virtuale deve essere abilitata per Esplora dati di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Protegge il perimetro della rete con la distribuzione all'interno di una rete virtuale che consente di imporre regole dei gruppi di sicurezza di rete, connettersi all'ambiente locale e proteggere le origini della connessione dati con endpoint servizio. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
