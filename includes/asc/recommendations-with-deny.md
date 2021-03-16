---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/14/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: c142f6a00275b4b21b6bbc64e51a5c4d7d957258
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467992"
---
- Per accedere agli account di archiviazione con configurazioni di rete virtuale e firewall, è necessario che gli account di archiviazione limitati impediscano l'accesso alla rete
- Le variabili dell'account di automazione devono essere crittografate con le variabili dell'account di automazione
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
- È necessario abilitare solo le connessioni sicure alla cache Redis solo per le connessioni sicure alla cache di Azure per Redis.
- La sovrascrittura o la disabilitazione del profilo AppArmor dei contenitori deve essere limitata
- I contenitori con privilegi devono essere evitati
- È consigliabile evitare l'esecuzione di contenitori come utente radice
- Il trasferimento sicuro agli account di archiviazione deve essere abilitato per il trasferimento sicuro agli account di archiviazione
- Per i cluster di Service Fabric la proprietà ClusterProtectionLevel deve essere impostata su EncryptAndSign Service Fabric i cluster devono avere la proprietà ClusterProtectionLevel impostata su EncryptAndSign
- I cluster Service Fabric devono usare solo Azure Active Directory per l'autenticazione client Service Fabric i cluster devono usare solo Azure Active Directory per l'autenticazione client
- I servizi devono essere in ascolto solo sulle porte consentite
- L'accesso pubblico agli account di archiviazione non deve essere consentito
- È necessario eseguire la migrazione degli account di archiviazione a nuove risorse Azure Resource Manager gli account di archiviazione devono essere migrati a nuove risorse di Azure Resource Manager
- Gli account di archiviazione devono limitare l'accesso alla rete usando regole di rete virtuale
- L'utilizzo della rete host e delle porte deve essere limitato
- L'utilizzo dei montaggi dei volumi HostPath dei pod deve essere limitato a un elenco noto per limitare l'accesso ai nodi da contenitori compromessi
- È consigliabile che il periodo di validità dei certificati archiviati in Azure Key Vault non sia superiore a 12 mesi
- È necessario eseguire la migrazione delle macchine virtuali a nuove risorse Azure Resource Manager le macchine virtuali devono essere migrate a nuove risorse Azure Resource Manager
- Web Application Firewall (WAF) deve essere abilitato per il gateway applicazione
- Il Web Application Firewall (WAF) deve essere abilitato per il servizio Azure front door Service

