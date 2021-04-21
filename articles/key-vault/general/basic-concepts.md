---
title: Che cos'è Azure Key Vault? | Microsoft Docs
description: Informazioni su Azure Key Vault le chiavi crittografiche e i segreti utilizzati da applicazioni e servizi cloud.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 863f98e643a7978856c03f5efe95736e6787f977
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814407"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault concetti di base

Azure Key Vault è un servizio cloud per l'archiviazione sicura e l'accesso ai segreti. Un segreto è qualsiasi elemento a cui si vuole controllare strettamente l'accesso, ad esempio chiavi API, password, certificati o chiavi crittografiche. Key Vault servizio supporta due tipi di contenitori: insiemi di credenziali e pool HSM (Hardware Security Module) gestiti. Gli insiemi di credenziali supportano l'archiviazione di chiavi, segreti e certificati supportati da software e HSM. I pool di HSM gestiti supportano solo chiavi supportate da HSM. Vedere [Azure Key Vault panoramica dell'API REST](about-keys-secrets-certificates.md) per informazioni dettagliate complete.

Ecco altri termini importanti:

- **Tenant**: un tenant è l'organizzazione che possiede e gestisce una specifica istanza dei servizi cloud Microsoft. Viene usato più spesso per fare riferimento al set di servizi azure e Microsoft 365 per un'organizzazione.

- **Proprietario dell'insieme di credenziali**: il proprietario può creare un insieme di credenziali delle chiavi e ottenerne un accesso e controllo completi. Il proprietario dell'insieme di credenziali può anche configurare il controllo per registrare chi accede a segreti e chiavi. Gli amministratori possono controllare il ciclo di vita delle chiavi. Possono passare a una nuova versione della chiave, eseguirne il backup e svolgere attività correlate.

- **Consumer dell'insieme di credenziali**: quando il proprietario dell'insieme di credenziali gli concede l'accesso, il consumer può eseguire azioni sulle risorse nell'insieme di credenziali delle chiavi. Le azioni disponibili dipendono dalle autorizzazioni concesse.

- **Amministratori HSM gestiti:** gli utenti a cui è assegnato il ruolo Amministratore hanno il controllo completo su un pool di HSM gestiti. Possono creare più assegnazioni di ruolo per delegare l'accesso controllato ad altri utenti.

- **Managed HSM Crypto Officer/User**: ruoli predefiniti che vengono in genere assegnati a utenti o entità servizio che eseguono operazioni di crittografia usando le chiavi in Managed HSM. L'utente crypto può creare nuove chiavi, ma non può eliminare le chiavi.

- Crittografia del servizio crittografico **HSM** gestito: ruolo predefinito in genere assegnato a un'identità del servizio gestita degli account del servizio (ad esempio account di archiviazione) per la crittografia dei dati in stato di incoerzione con la chiave gestita dal cliente.

- **Risorsa**: una risorsa è un elemento gestibile disponibile tramite Azure. Esempi comuni sono macchina virtuale, account di archiviazione, app Web, database e rete virtuale. Ce ne sono molti altri.

- **Gruppo di risorse**: un gruppo di risorse è un contenitore con risorse correlate per una soluzione di Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione.

- **Entità di sicurezza:** un'entità di sicurezza di Azure è un'identità di sicurezza che le app, i servizi e gli strumenti di automazione creati dall'utente usano per accedere a risorse di Azure specifiche. Si pensi a una "identità utente" (nome utente, password o certificato) con un ruolo specifico e autorizzazioni strettamente controllate. Un'entità di sicurezza deve solo eseguire operazioni specifiche, a differenza di un'identità utente generale. Migliora la sicurezza se gli si concede solo il livello di autorizzazione minimo necessario per eseguire le attività di gestione. Un'entità di sicurezza usata con un'applicazione o un servizio viene definita in modo specifico entità **servizio.**

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Azure AD è il servizio Active Directory per un tenant. Ogni directory dispone di uno o più domini. A una directory possono essere associate molte sottoscrizioni, ma un solo tenant.

- **ID tenant di Azure**: un ID tenant è un modo univoco per identificare un'istanza di Azure AD all'interno di una sottoscrizione di Azure.

- **Identità gestite:** Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma il codice deve eseguire l'autenticazione Key Vault per recuperarle. L'uso di un'identità gestita consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare questa identità per l'autenticazione in Key Vault o in qualsiasi servizio che supporti l'autenticazione di Azure AD, senza dover inserire le credenziali nel codice. Per altre informazioni, vedere l'immagine seguente e la [panoramica delle identità gestite per le risorse di Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authentication"></a>Authentication
Per eseguire qualsiasi operazione con Key Vault, è prima necessario eseguire l'autenticazione. Esistono tre modi per eseguire l'autenticazione Key Vault:

- [Identità gestite per le](../../active-directory/managed-identities-azure-resources/overview.md)risorse di Azure: quando si distribuisce un'app in una macchina virtuale in Azure, è possibile assegnare un'identità alla macchina virtuale che ha accesso a Key Vault. È anche possibile assegnare identità ad [altre risorse di Azure.](../../active-directory/managed-identities-azure-resources/overview.md) Il vantaggio di questo approccio è che l'app o il servizio non gestisce la rotazione del primo segreto. Azure ruota automaticamente l'identità. È consigliabile usare questo approccio come procedura consigliata. 
- **Entità servizio e certificato:** è possibile usare un'entità servizio e un certificato associato che ha accesso a Key Vault. Questo approccio non è consigliato perché il proprietario o lo sviluppatore dell'applicazione deve ruotare il certificato.
- **Entità servizio e segreto:** anche se è possibile usare un'entità servizio e un segreto per l'autenticazione Key Vault, non è consigliabile. È difficile ruotare automaticamente il segreto bootstrap usato per eseguire l'autenticazione Key Vault.


## <a name="key-vault-roles"></a>Ruoli dell'insieme di credenziali delle chiavi

La seguente tabella permette di capire meglio come l'insieme di credenziali chiave aiuti a soddisfare le esigenze degli sviluppatori e degli amministratori della sicurezza.

| Ruolo | Presentazione del problema | Soluzione offerta dall'insieme di credenziali chiave di Azure |
| --- | --- | --- |
| Sviluppatore di un'applicazione Azure |"Si vuole scrivere un'applicazione per Azure che usa chiavi per la firma e la crittografia. Ma si vuole che queste chiavi siano esterne all'applicazione in modo che la soluzione sia adatta per un'applicazione distribuita geograficamente. <br/><br/>Voglio che queste chiavi e questi segreti siano protetti, senza dover scrivere manualmente il codice, Voglio anche che queste chiavi e questi segreti siano facili da usare dalle applicazioni, con prestazioni ottimali." |√ Le chiavi vengono archiviate in un insieme di credenziali e richiamate dall'URI quando è necessario.<br/><br/> √ Le chiavi vengono protette da Azure con algoritmi standard del settore, lunghezze delle chiavi e moduli di protezione hardware.<br/><br/>  √ Le chiavi vengono elaborate in moduli di protezione hardware che risiedono negli stessi data center di Azure in cui si trovano le applicazioni. In questo modo si ottiene una migliore affidabilità e una latenza ridotta rispetto a chiavi che si trovano in una posizione diversa, ad esempio in locale. |
| Sviluppatore di software come un servizio (SaaS) |"I don't want the responsibility or potentialresponsibility for my customers' tenant keys and secrets. <br/><br/>Voglio che i clienti disercino e gestino le chiavi in modo da potersi concentrare sulle mie prestazioni migliori, fornendo le funzionalità software di base." |√ I clienti possono importare le loro chiavi in Azure e gestirle. Quando un'applicazione SaaS deve eseguire operazioni di crittografia usando le chiavi dei clienti, Key Vault queste operazioni per conto dell'applicazione. L'applicazione non visualizza le chiavi dei clienti. |
| Responsabile della sicurezza |"Voglio sapere che le applicazioni sono conformi agli HMS FIPS 140-2 livello 2 o FIPS 140-2 livello 3 per la gestione sicura delle chiavi. <br/><br/>Voglio assicurarmi che la mia organizzazione abbia il controllo del ciclo di vita delle chiavi e possa monitorare l'utilizzo delle chiavi. <br/><br/>Anche se si usano più servizi e risorse di Azure, si vogliono gestire le chiavi da un'unica posizione in Azure." |√ scegliere **gli insiemi di credenziali** per gli HMS convalidati FIPS 140-2 livello 2.<br/>√ scegliere **i pool di HSM** gestiti per gli HSM convalidati FIPS 140-2 livello 3.<br/><br/>√ L'insieme di credenziali delle chiavi è progettato in modo che Microsoft non possa vedere o estrarre le chiavi.<br/>√ L'utilizzo delle chiavi viene registrato quasi in tempo reale.<br/><br/>√ L'insieme di credenziali offre un'unica interfaccia, indipendentemente dal numero di insiemi di credenziali disponibili in Azure, dalle aree supportate e dalle applicazioni che li usano. |

Chiunque abbia una sottoscrizione di Azure può creare e usare insiemi di credenziali delle chiavi. Sebbene Key Vault vantaggi per sviluppatori e amministratori della sicurezza, può essere implementata e gestita dall'amministratore di un'organizzazione che gestisce altri servizi di Azure. Ad esempio, questo amministratore può accedere con una sottoscrizione di Azure, creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e quindi essere responsabile di attività operative come le seguenti:

- Creare o importare una chiave o un segreto
- Revocare o eliminare una chiave o un segreto
- Autorizzare gli utenti o le applicazioni per l'accesso all'insieme di credenziali delle chiavi, per consentire la gestione o l'uso delle chiavi e dei segreti corrispondenti
- Configurare l'utilizzo delle chiavi (ad esempio, la firma o la crittografia)
- Monitorare l'utilizzo delle chiavi

Questo amministratore fornisce quindi agli sviluppatori gli URI da chiamare dalle applicazioni. Questo amministratore fornisce anche le informazioni di registrazione dell'utilizzo delle chiavi all'amministratore della sicurezza. 

![Panoramica del funzionamento di Azure Key Vault][1]

Gli sviluppatori possono gestire le chiavi anche direttamente, usando le API. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure](developers-guide.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [Azure Key Vault di sicurezza.](security-features.md)
- Informazioni su come [proteggere i pool di HSM gestiti](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).
