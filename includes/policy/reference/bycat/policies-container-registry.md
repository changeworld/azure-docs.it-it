---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 91c03d457479145a915d6bb8843b53090caf0956
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98738736"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I registri contenitori devono essere crittografati con una chiave gestita dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Usare le chiavi gestite dal cliente per gestire la crittografia dei dati inattivi del contenuto dei registri. Per impostazione predefinita, i dati inattivi sono crittografati tramite chiavi gestite dal servizio, ma in genere sono richieste chiavi gestite dal cliente per soddisfare gli standard di conformità alle normative. Le chiavi gestite dal cliente consentono di crittografare i dati con una chiave di Azure Key Vault creata dall'utente e di sua proprietà. L'utente dispone del controllo completo e della piena responsabilità in merito al ciclo di vita della chiave, incluse le operazioni di rotazione e gestione. Per altre informazioni sulla crittografia con chiavi gestite dal cliente, vedere [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, Deny, Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[I registri contenitori non devono consentire l'accesso alla rete senza restrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Per impostazione predefinita, i Registri Azure Container accettano le connessioni tramite Internet dagli host in qualsiasi rete. Per proteggere i registri da potenziali minacce, consentire l'accesso solo da indirizzi o intervalli di indirizzi IP pubblici specifici. Se nel registro non sono configurate regole del firewall o IP o una rete virtuale, il registro verrà visualizzato nelle risorse non integre. Per altre informazioni sulle regole di rete per il Registro Azure Container, vedere [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) e [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[I registri contenitori devono usare collegamenti privati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Collegamento privato di Azure consente di connettere la rete virtuale ai servizi di Azure senza un indirizzo IP pubblico nell'origine o nella destinazione. La piattaforma del collegamento privato gestisce la connettività tra il consumer e i servizi tramite la rete backbone di Azure. Il mapping degli endpoint privati ai soli registri contenitori anziché all'intero servizio, garantisce la protezione dai rischi di perdita dei dati. Per altre informazioni, vedere [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
