---
title: Azure Active Directory Domande frequenti sui certificati di gestione delle applicazioni
description: Informazioni sulle risposte alle domande frequenti sulla gestione dei certificati per le app che usano Azure Active Directory come provider di identità (IdP).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 928bf02e2d628379738483b40631e36f0f929176
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803716"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Domande frequenti sui certificati di gestione delle applicazioni Azure Active Directory (Azure AD)

Questa pagina risponde alle domande frequenti sulla gestione dei certificati per le app che usano Azure Active Directory (Azure AD) come provider di identità (IdP).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Esiste un modo per generare un elenco di certificati di firma SAML in scadenza?

È possibile esportare tutte le registrazioni di app con segreti scaduti, certificati e proprietari per le app specificate dalla directory in un file CSV tramite gli [script di PowerShell](app-management-powershell-samples.md). 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Dove è possibile trovare le informazioni sui passaggi per il rinnovo dei certificati imminenti?

La procedura è disponibile [qui](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Come è possibile personalizzare la data di scadenza per i certificati emessi da Azure AD?

Per impostazione predefinita, Azure AD configura un certificato in modo che scada dopo tre anni quando viene creato automaticamente durante la configurazione di SAML Single Sign-On. Poiché non è possibile modificare la data di un certificato dopo averlo salvato, è necessario creare un nuovo certificato. Per istruzioni su come eseguire questa operazione, fare riferimento [a personalizzare la data di scadenza per il certificato federativo ed eseguire il rollback a un nuovo certificato](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate).

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Come è possibile automatizzare le notifiche di scadenza dei certificati?

Azure AD invierà una notifica di posta elettronica 60, 30 e 7 giorni prima della scadenza del certificato SAML. È possibile aggiungere più di un indirizzo di posta elettronica per ricevere le notifiche. 

> [!NOTE]
> È possibile aggiungere fino a cinque indirizzi di posta elettronica all'elenco di notifiche (incluso l'indirizzo di posta elettronica dell'amministratore che ha aggiunto l'applicazione). Se è necessario ricevere una notifica per più persone, usare i messaggi di posta elettronica della lista di distribuzione. 

Per specificare i messaggi di posta elettronica a cui si desidera inviare le notifiche, vedere [aggiungere indirizzi di notifica tramite posta elettronica per la scadenza del certificato](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration).

Non è possibile modificare o personalizzare le notifiche di posta elettronica ricevute da `aadnotification@microsoft.com` . Tuttavia, è possibile esportare le registrazioni dell'app con i segreti e i certificati in scadenza tramite gli [script di PowerShell](app-management-powershell-samples.md).

## <a name="who-can-update-the-certificates"></a>Chi può aggiornare i certificati?

Il proprietario dell'applicazione o dell'amministratore globale o dell'amministratore dell'applicazione può aggiornare i certificati tramite portale di Azure interfaccia utente, PowerShell o Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Sono necessari altri dettagli sulle opzioni di firma del certificato.

In Azure AD, è possibile configurare le opzioni di firma del certificato e l'algoritmo di firma del certificato. Per altre informazioni, vedere [Opzioni avanzate di firma del certificato SAML per le app Azure ad](certificate-signing-options.md).

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>È necessario sostituire il certificato per Azure AD applicazioni proxy di applicazione e sono necessarie altre istruzioni.

Per sostituire i certificati per Azure AD applicazioni proxy di applicazione, vedere [esempio di PowerShell-sostituire il certificato nelle app del proxy di applicazione](scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Ricerca per categorie gestire i certificati per i domini personalizzati nel proxy di applicazione Azure AD?

Per configurare un'app locale per l'uso di un dominio personalizzato, sono necessari un dominio personalizzato Azure Active Directory verificato, un certificato PFX per il dominio personalizzato e un'app locale da configurare. Per altre informazioni, vedere [domini personalizzati nel proxy di applicazione Azure ad](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>È necessario aggiornare il certificato per la firma di token sul lato applicazione. Dove è possibile reperirlo Azure AD lato?

È possibile rinnovare un certificato X. 509 SAML, vedere [certificato di firma SAML](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Che cos'è Azure AD rollover della chiave di firma?

Per informazioni più dettagliate, vedere [qui](../develop/active-directory-signing-key-rollover.md). 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Ricerca per categorie rinnovare il certificato di crittografia del token dell'applicazione?

Per rinnovare un certificato di crittografia del token dell'applicazione, vedere [come rinnovare un certificato di crittografia di token per un'applicazione aziendale](howto-saml-token-encryption.md). 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Ricerca per categorie rinnovare il certificato di firma del token dell'applicazione?

Per rinnovare un certificato di firma del token dell'applicazione, vedere [come rinnovare un certificato per la firma di token per un'applicazione aziendale](manage-certificates-for-federated-single-sign-on.md).

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Ricerca per categorie aggiornare Azure AD dopo aver modificato i certificati di Federazione?

Per aggiornare Azure AD dopo aver modificato i certificati di federazione, vedere [rinnovo dei certificati federativi per Microsoft 365 e Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md).
