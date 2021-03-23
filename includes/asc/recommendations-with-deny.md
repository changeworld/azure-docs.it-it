---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/21/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 5fc36e327a9530105182f0a23b3ef22ab324e01c
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104803456"
---
- L'accesso agli account di archiviazione con configurazioni del firewall e di rete virtuale deve essere limitato
- Le variabili dell'account di automazione devono essere crittografate
- La cache di Azure per Redis deve risiedere all'interno di una rete virtuale
- Gli account Azure Cosmos DB devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi
- Le aree di lavoro di Azure Machine Learning devono essere crittografate con una chiave gestita dal cliente
- Azure Spring Cloud deve usare l'aggiunta alla rete
- Gli account di servizi cognitivi devono abilitare la crittografia dei dati con una chiave gestita dal cliente (CMK)
- È consigliabile applicare limiti per la CPU e la memoria dei contenitori
- Le immagini del contenitore devono essere distribuite solo da registri attendibili
- I registri contenitori devono essere crittografati con una chiave gestita dal cliente
- È consigliabile evitare i contenitori con escalation dei privilegi
- I contenitori che condividono spazi dei nomi host sensibili devono essere evitati
- I contenitori devono essere in ascolto solo sulle porte consentite
- Per i contenitori deve essere imposto il file system radice non modificabile (di sola lettura)
- Le chiavi degli insiemi di credenziali delle chiavi devono avere una data di scadenza
- I segreti degli insiemi di credenziali delle chiavi devono avere una data di scadenza
- Negli insiemi di credenziali delle chiavi deve essere abilitata la protezione dalla rimozione definitiva
- Negli insiemi di credenziali delle chiavi deve essere abilitata la funzionalità di eliminazione temporanea
- Per i contenitori devono essere imposte le funzionalità Linux con privilegi minimi
- Devono essere abilitate solo connessioni sicure alla Cache Redis
- La sovrascrittura o la disabilitazione del profilo AppArmor dei contenitori deve essere limitata
- I contenitori con privilegi devono essere evitati
- È consigliabile evitare l'esecuzione di contenitori come utente radice
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign
- I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client
- I servizi devono essere in ascolto solo sulle porte consentite
- L'accesso pubblico agli account di archiviazione non deve essere consentito
- È consigliabile eseguire la migrazione degli account di archiviazione alle nuove risorse di Azure Resource Manager
- Gli account di archiviazione devono limitare l'accesso alla rete usando regole di rete virtuale
- L'utilizzo della rete host e delle porte deve essere limitato
- L'utilizzo dei montaggi dei volumi HostPath dei pod deve essere limitato a un elenco noto per limitare l'accesso ai nodi da contenitori compromessi
- È consigliabile che il periodo di validità dei certificati archiviati in Azure Key Vault non sia superiore a 12 mesi
- È consigliabile eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager
- Web Application Firewall (WAF) deve essere abilitato per il gateway applicazione
- Il Web Application Firewall (WAF) deve essere abilitato per il servizio Azure front door Service

