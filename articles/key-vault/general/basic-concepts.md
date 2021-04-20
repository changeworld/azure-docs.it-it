---
title: Che cos'è Azure Key Vault? | Microsoft Docs
description: Informazioni su come Azure Key Vault le chiavi crittografiche e i segreti utilizzati da servizi e applicazioni cloud.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 46c8845b4f01db09bf5f96eb1e67078b4e361f9f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728321"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault concetti di base

Azure Key Vault è un servizio cloud per l'archiviazione sicura e l'accesso ai segreti. Un segreto è qualsiasi elemento a cui si vuole controllare strettamente l'accesso, ad esempio chiavi API, password, certificati o chiavi crittografiche. Key Vault servizio supporta due tipi di contenitori: insiemi di credenziali e pool di moduli di protezione hardware gestiti. Gli insiemi di credenziali supportano l'archiviazione di chiavi, segreti e certificati supportati da software e HSM. I pool di HSM gestiti supportano solo chiavi supportate da HSM. Per [informazioni dettagliate, Azure Key Vault panoramica dell'API REST.](about-keys-secrets-certificates.md)

Ecco altri termini importanti:

- **Tenant**: un tenant è l'organizzazione che possiede e gestisce una specifica istanza dei servizi cloud Microsoft. Viene spesso usato per fare riferimento al set di servizi di azure e Microsoft 365 per un'organizzazione.

- **Proprietario dell'insieme di credenziali**: il proprietario può creare un insieme di credenziali delle chiavi e ottenerne un accesso e controllo completi. Il proprietario dell'insieme di credenziali può anche configurare il controllo per registrare chi accede a segreti e chiavi. Gli amministratori possono controllare il ciclo di vita delle chiavi. Possono passare a una nuova versione della chiave, eseguirne il backup e svolgere attività correlate.

- **Consumer dell'insieme di credenziali**: quando il proprietario dell'insieme di credenziali gli concede l'accesso, il consumer può eseguire azioni sulle risorse nell'insieme di credenziali delle chiavi. Le azioni disponibili dipendono dalle autorizzazioni concesse.

- **Amministratori HSM gestiti:** gli utenti a cui è assegnato il ruolo Di amministratore hanno il controllo completo su un pool di HSM gestiti. Possono creare più assegnazioni di ruolo per delegare l'accesso controllato ad altri utenti.

- **Managed HSM Crypto Officer/User**: ruoli predefiniti assegnati in genere a utenti o entità servizio che eseguono operazioni di crittografia usando le chiavi nel servizio HSM gestito. Crypto User può creare nuove chiavi, ma non eliminarle.

- Crittografia del servizio di crittografia del modulo di protezione **HSM** gestito: ruolo predefinito che viene in genere assegnato a un'identità del servizio gestita degli account del servizio (ad esempio, un account di archiviazione) per la crittografia dei dati in pausa con la chiave gestita dal cliente.

- **Risorsa**: una risorsa è un elemento gestibile disponibile tramite Azure. Esempi comuni sono la macchina virtuale, l'account di archiviazione, l'app Web, il database e la rete virtuale. Ce ne sono molti altri.

- **Gruppo di risorse**: un gruppo di risorse è un contenitore con risorse correlate per una soluzione di Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione.

- **Entità di sicurezza:** un'entità di sicurezza di Azure è un'identità di sicurezza che viene utilizzata da app, servizi e strumenti di automazione creati dall'utente per accedere a risorse di Azure specifiche. Può essere una "identità utente" (nome utente, password o certificato) con un ruolo specifico e autorizzazioni strettamente controllate. Un'entità di sicurezza deve eseguire solo operazioni specifiche, a differenza di un'identità utente generale. Migliora la sicurezza se si concede solo il livello di autorizzazione minimo necessario per eseguire le attività di gestione. Un'entità di sicurezza usata con un'applicazione o un servizio è definita in modo specifico **entità servizio**.

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Azure AD è il servizio Active Directory per un tenant. Ogni directory dispone di uno o più domini. A una directory possono essere associate molte sottoscrizioni, ma un solo tenant.

- **ID tenant di Azure**: un ID tenant è un modo univoco per identificare un'istanza di Azure AD all'interno di una sottoscrizione di Azure.

- **Identità gestite:** Azure Key Vault un modo per archiviare in modo sicuro credenziali e altre chiavi e segreti, ma il codice deve eseguire l'autenticazione per Key Vault recuperarle. L'uso di un'identità gestita consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare questa identità per l'autenticazione in Key Vault o in qualsiasi servizio che supporti l'autenticazione di Azure AD, senza dover inserire le credenziali nel codice. Per altre informazioni, vedere l'immagine seguente e la [panoramica delle identità gestite per le risorse di Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authentication"></a>Authentication
Per eseguire qualsiasi operazione con Key Vault, è prima di tutto necessario eseguire l'autenticazione. Esistono tre modi per eseguire l'autenticazione Key Vault:

- [Identità gestite per](../../active-directory/managed-identities-azure-resources/overview.md)le risorse di Azure: quando si distribuisce un'app in una macchina virtuale in Azure, è possibile assegnare un'identità alla macchina virtuale che ha accesso a Key Vault. È anche possibile assegnare identità ad [altre risorse di Azure.](../../active-directory/managed-identities-azure-resources/overview.md) Il vantaggio di questo approccio è che l'app o il servizio non gestisce la rotazione del primo segreto. Azure ruota automaticamente l'identità. È consigliabile usare questo approccio come procedura consigliata. 
- **Entità servizio e certificato:** è possibile usare un'entità servizio e un certificato associato che ha accesso a Key Vault. Questo approccio non è consigliabile perché il proprietario o lo sviluppatore dell'applicazione deve ruotare il certificato.
- **Entità servizio e** segreto: anche se è possibile usare un'entità servizio e un segreto per l'autenticazione Key Vault, non è consigliabile. È difficile ruotare automaticamente il segreto bootstrap usato per l'autenticazione Key Vault.


## <a name="key-vault-roles"></a>Ruoli dell'insieme di credenziali delle chiavi

La seguente tabella permette di capire meglio come l'insieme di credenziali chiave aiuti a soddisfare le esigenze degli sviluppatori e degli amministratori della sicurezza.

| Ruolo | Presentazione del problema | Soluzione offerta dall'insieme di credenziali chiave di Azure |
| --- | --- | --- |
| Sviluppatore di un'applicazione Azure |"Voglio scrivere un'applicazione per Azure che usa le chiavi per la firma e la crittografia. È tuttavia necessario che queste chiavi siano esterne all'applicazione in modo che la soluzione sia adatta a un'applicazione distribuita geograficamente. <br/><br/>Voglio che queste chiavi e questi segreti siano protetti, senza dover scrivere manualmente il codice, Voglio anche che queste chiavi e segreti siano facili da usare dalle applicazioni, con prestazioni ottimali." |√ Le chiavi vengono archiviate in un insieme di credenziali e richiamate dall'URI quando è necessario.<br/><br/> √ Le chiavi vengono protette da Azure con algoritmi standard del settore, lunghezze delle chiavi e moduli di protezione hardware.<br/><br/>  √ Le chiavi vengono elaborate in moduli di protezione hardware che risiedono negli stessi data center di Azure in cui si trovano le applicazioni. In questo modo si ottiene una migliore affidabilità e una latenza ridotta rispetto a chiavi che si trovano in una posizione diversa, ad esempio in locale. |
| Sviluppatore di software come un servizio (SaaS) |"Non voglio la responsabilità o la responsabilità potenziale per i segreti e le chiavi del tenant dei clienti. <br/><br/>Voglio che i clienti disercitino e gestino le chiavi in modo da potersi concentrare sulle prestazioni migliori, fornendo le principali funzionalità software". |√ I clienti possono importare le loro chiavi in Azure e gestirle. Quando un'applicazione SaaS deve eseguire operazioni di crittografia usando le chiavi dei clienti, Key Vault esegue queste operazioni per conto dell'applicazione. L'applicazione non visualizza le chiavi dei clienti. |
| Responsabile della sicurezza |"Si vuole sapere che le applicazioni sono conformi ai sistemi HMS FIPS 140-2 livello 2 o FIPS 140-2 livello 3 per la gestione sicura delle chiavi. <br/><br/>Voglio assicurarmi che la mia organizzazione abbia il controllo del ciclo di vita delle chiavi e possa monitorare l'utilizzo delle chiavi. <br/><br/>E anche se si usano più servizi e risorse di Azure, si vogliono gestire le chiavi da un'unica posizione in Azure." |√ scegliere **gli insiemi di** credenziali per i modulo di protezione utente convalidati FIPS 140-2 livello 2.<br/>√ scegliere **i pool di HSM** gestiti per i HSM convalidati FIPS 140-2 livello 3.<br/><br/>√ L'insieme di credenziali delle chiavi è progettato in modo che Microsoft non possa vedere o estrarre le chiavi.<br/>√ L'utilizzo delle chiavi viene registrato quasi in tempo reale.<br/><br/>√ L'insieme di credenziali offre un'unica interfaccia, indipendentemente dal numero di insiemi di credenziali disponibili in Azure, dalle aree supportate e dalle applicazioni che li usano. |

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

- Informazioni su come proteggere [l'insieme di credenziali.](secure-your-key-vault.md)
- Informazioni su come [proteggere i pool di HSM gestiti](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).
