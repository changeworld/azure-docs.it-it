---
title: Domande frequenti-credenziali verificabili di Azure (anteprima)
description: Trovare le risposte alle domande comuni sulle credenziali verificabili
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 3c43cfb537c84fb25a6bf879d32a8034342ff605
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280718"
---
# <a name="frequently-asked-questions-faq"></a>Domande frequenti

Questa pagina contiene le domande frequenti sulle credenziali verificabili e sull'identità decentralizzata. Le domande sono organizzate nelle sezioni seguenti.

- [Vocabolario e nozioni di base](#the-basics)
- [Domande concettuali sull'identità decentralizzata](#conceptual-questions)
- [Domande sull'uso dell'anteprima delle credenziali verificabili](#using-the-preview)

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="the-basics"></a>Nozioni di base

### <a name="what-is-a-did"></a>Che cos'è un? 

Identificatori decentralizzati (DIDs) sono identificatori che possono essere usati per proteggere l'accesso alle risorse, firmare e verificare le credenziali e semplificare lo scambio di dati dell'applicazione. A differenza dei nomi utente e degli indirizzi di posta elettronica tradizionali, DIDs sono di proprietà e controllati dall'entità stessa, ovvero una persona, un dispositivo o una società. DIDs esiste indipendentemente da qualsiasi organizzazione esterna o intermediario attendibile. [La specifica W3C decentralizzated Identifier](https://www.w3.org/TR/did-core/) spiega questo aspetto in modo più dettagliato.

### <a name="why-do-we-need-a-did"></a>Perché è necessaria una?

La relazione di trust digitale richiede fondamentalmente ai partecipanti la proprietà e il controllo delle identità e l'identità inizia in corrispondenza dell'identificatore.
In un'epoca di violazioni di sistema su larga scala e di attacchi all'identificatore centralizzato honeypot, la decentralizzazione dell'identità sta diventando una necessità fondamentale di sicurezza per gli utenti e le aziende.
I singoli utenti che possiedono e controllano le proprie identità sono in grado di scambiare dati e prove verificabili. Un ambiente di credenziali distribuite consente l'automazione di molti processi aziendali che sono attualmente manuali e laboriosi.

### <a name="what-is-a-verifiable-credential"></a>Informazioni sulle credenziali verificabili 

Le credenziali fanno parte della nostra vita quotidiana. le licenze del driver vengono usate per dichiarare che Microsoft è in grado di gestire un veicolo a motore, i gradi dell'università possono essere usati per asserire il livello di formazione e i passaporti emessi dal governo ci permettono di spostarsi tra i paesi. Credenziali verificabili fornisce un meccanismo per esprimere questi tipi di credenziali sul Web in modo da garantire la sicurezza crittografica, la privacy e la verifica del computer. [La specifica delle credenziali verificabili W3C](https://www.w3.org/TR/vc-data-model//) spiega questo aspetto in modo più dettagliato.


## <a name="conceptual-questions"></a>Domande concettuali

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>Cosa accade quando un utente perde il telefono? È possibile ripristinare la propria identità?

Esistono diversi modi per offrire un meccanismo di ripristino agli utenti, ognuno con compromessi propri. Stiamo attualmente valutando opzioni e progettando approcci al ripristino che offrono praticità e sicurezza rispettando la privacy e la sovranità dell'utente.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>In che modo un utente può considerare attendibile una richiesta da un'autorità emittente o da un verificatore? Come si fa a capire che è vero per un'organizzazione?

Sono state implementate [le specifiche di configurazione note per la configurazione di Identity Foundation decentralizzata](https://identity.foundation/.well-known/resources/did-configuration/) per connettere un oggetto a un sistema esistente noto, ovvero nomi di dominio. Ogni creato con le credenziali Azure Active Directory verificabili può includere un nome di dominio radice che verrà codificato nel documento DID. Per altre informazioni, vedere l'articolo [collegare il dominio all'identificatore distribuito](how-to-dnsbind.md) .  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Perché l'anteprima delle credenziali verificabile USA ION come metodo DID e pertanto Bitcoin per fornire un'infrastruttura a chiave pubblica decentralizzata?

ION è una rete di livello 2 decentralizzata, non autorizzata e scalabile, che viene eseguita in cima a Bitcoin. Raggiunge la scalabilità senza includere un token cryptoasset speciale, validator attendibili o meccanismi di consenso centralizzati. Si usa Bitcoin per il supporto di base di livello 1 a causa dell'attendibilità della rete decentralizzata per fornire un elevato grado di immutabilità per un sistema di record di eventi cronologici.

## <a name="using-the-preview"></a>Uso dell'anteprima

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Perché è necessario usare NodeJS per l'anteprima delle credenziali verificabili? Eventuali piani per altri linguaggi di programmazione? 

Abbiamo scelto NodeJS perché si tratta di una piattaforma molto diffusa per gli sviluppatori di applicazioni. Verrà rilasciata un'API REST che consentirà agli sviluppatori di emettere e verificare le credenziali. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>Il codice usato nell'anteprima Open Source?

Sì. I repository seguenti sono i componenti open source dei servizi.

1. [SideTree su GitHub](https://github.com/decentralized-identity/sidetree)
2. [VC SDK per node su GitHub](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. [Android SDK per la creazione di portafogli di identità decentralizzati, su GitHub](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. Un [SDK iOS per la creazione di portafogli di identità decentralizzati, su GitHub](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>Quali sono i requisiti di licenza?

Per usare l'anteprima delle credenziali verificabili, è necessaria una licenza di Azure AD P2. Si tratta di un requisito temporaneo, perché si prevede che i prezzi per questo servizio vengano fatturati in base all'utilizzo. 


## <a name="next-steps"></a>Passaggi successivi

- [Come personalizzare le credenziali Azure Active Directory verificabili](credential-design.md)
