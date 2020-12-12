---
title: Raccolta di partner ISV per Azure AD B2C
titleSuffix: Azure AD B2C
description: Scopri come integrarti con i nostri partner ISV per personalizzare l'esperienza degli utenti finali in base alle tue esigenze. La nostra rete partner estende le funzionalità della soluzione; abilitare l'autenticazione a più fattori, autenticazione dei clienti sicura, controllo degli accessi in base al ruolo; combattere le frodi tramite la verifica dell'identità.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4592742b99d7c3e915c31ca5a202355d32e76d68
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356124"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Partner ISV Azure Active Directory B2C

Microsoft ISV partner Network estende le funzionalità della soluzione per facilitare la creazione di esperienze utente finali senza problemi. Con Azure AD B2C, è possibile eseguire l'integrazione con i partner ISV per abilitare i metodi di autenticazione a più fattori, eseguire il controllo degli accessi in base al ruolo, abilitare la verifica dell'identità e la correzione, migliorare la sicurezza grazie al rilevamento dei bot e alla protezione dalle frodi e soddisfare i requisiti di PSD2 (Secure Customer Authentication). Per informazioni su come integrare le app con i partner ISV, seguire le procedure dettagliate di esempio.

>[!NOTE]
>Il [sito della community di Azure Active Directory B2C su GitHub](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) fornisce anche criteri personalizzati di esempio dalla community.

## <a name="identity-verification-and-proofing"></a>Verifica dell'identità e correzione

Con Azure AD B2C partner, i clienti possono abilitare la verifica dell'identità e la correzione degli utenti finali prima di consentire l'accesso o la registrazione dell'account. La verifica e la verifica dell'identità consentono di controllare il documento, le informazioni basate sulle informazioni e la loro esperienza.

Un diagramma dell'architettura di alto livello illustra il flusso.

![Diagramma che mostra il flusso di prova dell'identità](./media/partner-gallery/third-party-identity-proofing.png)

Partner Microsoft con i seguenti ISV per la verifica dell'identità e la correzione.

| Partner ISV | Procedure dettagliate per la descrizione e l'integrazione |
|:-------------------------|:--------------|
|![Screenshot di un logo Experian.](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) è un provider di verifica dell'identità e di correzione che esegue valutazioni del rischio in base agli attributi dell'utente per prevenire le frodi. |
|![Screenshot di un logo IDology.](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) è un provider di verifica dell'identità e di correzione con soluzioni di verifica ID, soluzioni per la prevenzione di frodi, soluzioni di conformità e altro ancora.|
|![Screenshot di un logo Jumio.](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) è un servizio di verifica ID, che consente la verifica automatica in tempo reale degli ID, salvaguardando i dati dei clienti. |
| ![Screenshot di un logo LexisNexis.](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) è un provider di convalida dell'identità e di profilatura che verifica l'identificazione dell'utente e fornisce una valutazione completa dei rischi in base al dispositivo dell'utente. |
| ![Screenshot di un logo onfido](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) è un ID documento e una soluzione di verifica biometria facciale che consente alle aziende di soddisfare i requisiti dei *clienti* e delle identità in tempo reale.  |

## <a name="mfa-and-passwordless-authentication"></a>Autenticazione a più fattori e autenticazione con password

Partner Microsoft con i seguenti ISV per l'autenticazione a più fattori e l'autenticazione senza password.

| Partner ISV | Procedure dettagliate per la descrizione e l'integrazione |
|:-------------------------|:--------------|
| ![Screenshot di un logo HYPR](./media/partner-gallery/hypr-logo.png) | [HYPR](./partner-hypr.md) è un provider di autenticazione senza password, che sostituisce le password con crittografia a chiave pubblica, eliminando la frode, il phishing e il riutilizzo delle credenziali. |
| ![Screenshot di un logo itsme](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) è una soluzione di identificazione digitale conforme ai servizi di identificazione, autenticazione e attendibilità (eiDAS) per consentire agli utenti di accedere in modo sicuro senza lettori di schede, password, autenticazione a due fattori e codici pin multipli. |
| ![Screenshot di un logo Nevis](./media/partner-gallery/nevis-logo.png) | [Nevis](./partner-nevis.md) consente l'autenticazione senza password e offre un'esperienza utente finale per dispositivi mobili e completamente personalizzata con l'app di accesso Nevis per l'autenticazione avanzata dei clienti e per conformarsi ai requisiti delle transazioni di PSD2. |
| ![Screenshot di un logo trusona](./media/partner-gallery/trusona-logo.png) | L'integrazione con [Trusona](./partner-trusona.md) consente di accedere in modo sicuro e consente l'autenticazione senza password, l'autenticazione a più fattori e l'analisi delle licenze digitali. |
| ![Screenshot di un logo Twilio.](./media/partner-gallery/twilio-logo.png) | L' [app Twilio Verify](./partner-twilio.md) offre più soluzioni per abilitare l'autenticazione a più fattori tramite SMS, una password monouso (OTP), una password monouso basata sul tempo (TOTP) e le notifiche push e per conformarsi ai requisiti SCA per PSD2. |
| ![Screenshot di un logo typingDNA](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-twilio.md) consente un'autenticazione avanzata dei clienti analizzando il modello di digitazione di un utente. Consente alle aziende di abilitare un'autenticazione a più fattori invisibile all'utente e di conformarsi ai requisiti SCA per PSD2. |
| ![Screenshot di un logo whoiam](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) è un'applicazione con un sistema di gestione delle identità personalizzato che consente alle organizzazioni di verificare la propria base di utenti tramite voce, SMS e posta elettronica. |

## <a name="role-based-access-control"></a>Controllo di accesso in base ai ruoli 
 
Partner Microsoft con i seguenti ISV per il controllo degli accessi in base al ruolo.

| Partner ISV | Procedure dettagliate per la descrizione e l'integrazione |
|:-------------------------|:--------------|
| ![Screenshot di un logo n8identity](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) è una piattaforma di governance come servizio che fornisce la soluzione per gestire la migrazione degli account cliente e l'amministrazione delle richieste di assistenza clienti (CSR) in esecuzione in Microsoft Azure. |
| ![Screenshot di un logo Saviynt](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-Saviynt.md) cloud-native Platform promuove una migliore sicurezza, conformità e governance tramite l'analisi intelligente e l'integrazione tra applicazioni per semplificare la modernizzazione it. |

## <a name="security"></a>Sicurezza

Partner Microsoft con i seguenti ISV per la sicurezza.

| Partner ISV | Procedure dettagliate per la descrizione e l'integrazione |
|:-------------------------|:--------------|
| ![Screenshot di un logo di arcose Lab](./media/partner-gallery/arkose-logo.png) | [Arcose Labs](./partner-arkose-labs.md) è un provider di soluzioni per la prevenzione delle frodi che consente alle organizzazioni di proteggersi da attacchi bot, attacchi di acquisizione di account e aperture fraudolente degli account. |
| ![Screenshot del logo di uno strato](./media/partner-gallery/strata-logo.png) | Gli [strati](./partner-strata.md) forniscono un accesso ibrido sicuro alle applicazioni locali applicando criteri di accesso coerenti, mantenendo le identità sincronizzate e semplificando la transizione di applicazioni da sistemi di identità legacy a controlli di accesso e autenticazione basati su standard forniti da Azure ad B2C. |
| ![Screenshot di un logo zScaler](./media/partner-gallery/zscaler-logo.png) | [ZScaler](./partner-zscaler.md) offre un accesso sicuro e basato su criteri a applicazioni e asset privati senza costi, problemi o rischi per la sicurezza di una VPN. |

## <a name="additional-information"></a>Informazioni aggiuntive

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)

## <a name="next-steps"></a>Passaggi successivi

Selezionare un partner nelle tabelle indicate per informazioni su come integrare la soluzione con Azure AD B2C.
