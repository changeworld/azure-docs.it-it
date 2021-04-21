---
title: Raccolta di partner ISV per Azure AD B2C
titleSuffix: Azure AD B2C
description: Informazioni su come eseguire l'integrazione con i partner ISV per personalizzare l'esperienza dell'utente finale in base alle esigenze. La rete partner estende le funzionalità della soluzione. abilitare MFA, Autenticazione sicura dei clienti, controllo degli accessi in base al ruolo; contrasto di frodi tramite verifica dell'identità Proofing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b555c8651c6e1608193a6ae06c39b20f633e4ea9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813661"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C partner ISV

La rete di partner ISV estende le funzionalità della soluzione per facilitare la creazione di esperienze utente finali senza problemi. Con Azure AD B2C è possibile eseguire l'integrazione con i partner ISV per abilitare i metodi di Multi-Factor Authentication (MFA), eseguire il controllo degli accessi in base al ruolo, abilitare la verifica e la verifica dell'identità, migliorare la sicurezza con il rilevamento dei bot e la protezione delle frodi e soddisfare i requisiti di Autenticazione sicura dei clienti (SCA) di Payment Services Directive 2 (PSD2). Usare le procedure dettagliate di esempio per informazioni su come integrare le app con i partner ISV.

>[!NOTE]
>Il [Azure Active Directory B2C della community su GitHub](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) fornisce anche criteri personalizzati di esempio della community.

## <a name="identity-verification-and-proofing"></a>Verifica e verifica dell'identità

Microsoft collabora con i seguenti ISV per la verifica e la verifica dell'identità.

| Partner ISV | Descrizione e procedure dettagliate per l'integrazione |
|:-------------------------|:--------------|
|![Screenshot di un logo experian.](./media/partner-gallery/experian-logo.png) | [Experian è un](./partner-experian.md) provider di verifica e verifica dell'identità che esegue valutazioni dei rischi in base agli attributi utente per prevenire le frodi. |
|![Screenshot di un logo IDology.](./media/partner-gallery/idology-logo.png) | [IDology è un](./partner-idology.md) provider di verifica e verifica dell'identità con soluzioni di verifica dell'ID, soluzioni di prevenzione delle frodi, soluzioni di conformità e altro ancora.|
|![Screenshot di un logo jumio.](./media/partner-gallery/jumio-logo.png) | [Jumio è](./partner-jumio.md) un servizio di verifica ID che consente la verifica automatica degli ID in tempo reale, proteggendo i dati dei clienti. |
| ![Screenshot di un logo LexisNexis.](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) è un provider di profilatura e convalida dell'identità che verifica l'identificazione dell'utente e fornisce una valutazione completa dei rischi in base al dispositivo dell'utente. |
| ![Screenshot di un logo Onfido](./media/partner-gallery/onfido-logo.png) | [Onfido è](./partner-onfido.md) una soluzione di verifica biometrica facciale e ID documento che consente alle aziende di soddisfare i requisiti di know *your customer* e identity in tempo reale.  |

## <a name="mfa-and-passwordless-authentication"></a>Autenticazione mFA e senza password

Microsoft collabora con gli ISV seguenti per l'autenticazione mfa e senza password.

| Partner ISV | Descrizione e procedure dettagliate sull'integrazione |
|:-------------------------|:--------------|
| ![Screenshot di un logo hypr](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) è un provider di autenticazione senza password, che sostituisce le password con crittografia a chiave pubblica eliminando frodi, phishing e riutilizzo delle credenziali. |
| ![Screenshot di un logo itsme](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) è una soluzione di ID digitale conforme ai servizi di identificazione elettronica, autenticazione e attendibilità (eiDAS) per consentire agli utenti di accedere in modo sicuro senza lettori di schede, password, autenticazione a due fattori e più codici PIN. |
|![Screenshot di un logo keyless.](./media/partner-gallery/keyless-logo.png) | [Keyless](./partner-keyless.md) è un provider di autenticazione senza password che fornisce l'autenticazione sotto forma di analisi biometrica facciale ed elimina frodi, phishing e riutilizzo delle credenziali.
| ![Screenshot di un logo nevis](./media/partner-gallery/nevis-logo.png) | [Nevis abilita](./partner-nevis.md) l'autenticazione senza password e offre un'esperienza utente finale completamente personalizzata per dispositivi mobili con l'app Nevis Access per l'autenticazione avanzata dei clienti e per la conformità ai requisiti delle transazioni PSD2. |
| ![Screenshot di un logo trusona](./media/partner-gallery/trusona-logo.png) | [L'integrazione di Trusona](./partner-trusona.md) consente di accedere in modo sicuro e consente l'autenticazione senza password, l'autenticazione a più fattori e l'analisi delle licenze digitali. |
| ![Screenshot di un logo twilio.](./media/partner-gallery/twilio-logo.png) | [L'app Twilio Verify](./partner-twilio.md) offre più soluzioni per abilitare L'autenticazione a più fattori tramite SMS una sola password (OTP), la password unica basata sul tempo (TOTP) e le notifiche push e per soddisfare i requisiti di SCA per PSD2. |
| ![Screenshot di un logo di digitazioneDNA](./media/partner-gallery/typingdna-logo.png) | [La digitazioneDNA](./partner-typingdna.md) consente l'autenticazione avanzata dei clienti analizzando il modello di digitazione di un utente. Consente alle aziende di abilitare un'autenticazione a più fattori invisibile all'utente e di rispettare i requisiti di SCA per PSD2. |
| ![Screenshot di un logo whoiam](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) è un'applicazione BRIMS (Branded Identity Management System) che consente alle organizzazioni di verificare la base utenti tramite voce, SMS e posta elettronica. |

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo 
 
Microsoft collabora con i seguenti ISV per il controllo degli accessi in base al ruolo.

| Partner ISV | Descrizione e procedure dettagliate per l'integrazione |
|:-------------------------|:--------------|
| ![Screenshot di un logo n8identity](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) è una piattaforma di governance identity-as-a-Service che offre una soluzione per risolvere la migrazione degli account dei clienti e l'amministrazione delle richieste del servizio clienti in esecuzione in Microsoft Azure. |
| ![Screenshot di un logo Saviynt](./media/partner-gallery/saviynt-logo.png) | La piattaforma nativa del cloud di [Saviynt](./partner-Saviynt.md) promuove una migliore sicurezza, conformità e governance grazie all'analisi intelligente e all'integrazione tra applicazioni per snellire la modernizzazione IT. |

## <a name="secure-hybrid-access-to-on-premises-application"></a>Proteggere l'accesso ibrido all'applicazione locale

Microsoft collabora con i seguenti ISV per fornire l'accesso ibrido sicuro alle applicazioni locali. 

| Partner ISV | Descrizione e procedure dettagliate per l'integrazione |
|:-------------------------|:--------------|
| ![Screenshot di un logo ping](./media/partner-gallery/ping-logo.png) | [Ping Identity](./partner-ping-identity.md) consente l'accesso ibrido sicuro alle applicazioni legacy locali in più cloud. |
| ![Screenshot del logo di uno strato](./media/partner-gallery/strata-logo.png) | [Strata](./partner-strata.md) offre l'accesso ibrido sicuro alle applicazioni locali tramite l'applicazione di criteri di accesso coerenti, la sincronizzazione delle identità e la semplice transizione delle applicazioni dai sistemi di gestione delle identità legacy all'autenticazione basata su standard e al controllo di accesso forniti da Azure AD B2C. |
| ![Screenshot di un logo zscaler](./media/partner-gallery/zscaler-logo.png) | [Zscaler offre](./partner-zscaler.md) l'accesso sicuro e basato su criteri alle applicazioni e agli asset privati senza i costi, i problemi o i rischi per la sicurezza di una VPN. |

## <a name="fraud-protection"></a>Protezione da frodi

Microsoft collabora con gli ISV seguenti per il rilevamento e la prevenzione delle frodi. 

| Partner ISV | Descrizione e procedure dettagliate sull'integrazione |
|:-------------------------|:--------------|
| ![Screenshot del logo di un lab di Arkose](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) è un provider di soluzioni di prevenzione delle frodi che consente alle organizzazioni di proteggere da attacchi bot, attacchi di acquisizione di account e aperture fraudolente degli account. |
| ![Screenshot di un logo BioCatch](./media/partner-gallery/biocatch-logo.png) | [BioCatch](./partner-biocatch.md) è un provider di soluzioni di prevenzione delle frodi che analizza i comportamenti digitali fisici e cognitivi di un utente per generare informazioni dettagliate che distinguono tra clienti legittimi e cyber-criminali. |
| ![Screenshot di un logo di Microsoft Dynamics 365](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Microsoft Dynamics 365 Fraud Protection](./partner-dynamics-365-fraud-protection.md) è una soluzione che consente alle organizzazioni di proteggersi dalle aperture fraudolente degli account tramite l'impronta digitale del dispositivo. |


## <a name="additional-information"></a>Informazioni aggiuntive

- [Criteri personalizzati in AAD B2C](./custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

## <a name="next-steps"></a>Passaggi successivi

Selezionare un partner nelle tabelle indicate per informazioni su come integrare la soluzione con Azure AD B2C.
