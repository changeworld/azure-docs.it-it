---
title: Azure Active Directory domande frequenti sui certificati di gestione delle applicazioni
description: Risposte alle domande frequenti sulla gestione dei certificati per le app che usano Azure Active Directory come provider di identità(IdP).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: iangithinji
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 0868c942a023662a1a6d3053477d85b0245fef4b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376240"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory domande frequenti sui certificati di gestione delle applicazioni (Azure AD)

Questa pagina risponde alle domande frequenti sulla gestione dei certificati per le app che usano Azure Active Directory (Azure AD) come provider di identità(IdP).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Esiste un modo per generare un elenco di certificati di firma SAML in scadenza?

È possibile esportare tutte le registrazioni di app con segreti in scadenza, certificati e i relativi proprietari per le app specificate dalla directory in un file CSV tramite script [di PowerShell.](app-management-powershell-samples.md) 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Dove è possibile trovare le informazioni sulla scadenza dei passaggi di rinnovo dei certificati?

La procedura è disponibile [qui](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Come è possibile personalizzare la data di scadenza per i certificati rilasciati da Azure AD?

Per impostazione predefinita, Azure AD un certificato in modo che scada dopo tre anni quando viene creato automaticamente durante la configurazione dell'accesso Single Sign-On SAML. Poiché non è possibile modificare la data di un certificato dopo il salvataggio, è necessario creare un nuovo certificato. Per informazioni su come eseguire questa operazione, vedere Personalizzare la data di scadenza del certificato federativo ed eseguire il [roll over in un nuovo certificato.](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Come è possibile automatizzare le notifiche di scadenza dei certificati?

Azure AD invierà una notifica tramite posta elettronica 60, 30 e 7 giorni prima della scadenza del certificato SAML. È possibile aggiungere più di un indirizzo di posta elettronica per ricevere le notifiche. 

> [!NOTE]
> È possibile aggiungere fino a 5 indirizzi di posta elettronica all'elenco Delle notifiche ( incluso l'indirizzo di posta elettronica dell'amministratore che ha aggiunto l'applicazione). Se sono necessarie più persone per ricevere una notifica, usare i messaggi di posta elettronica della lista di distribuzione. 

Per specificare i messaggi di posta elettronica a cui si vogliono inviare le notifiche, vedere Aggiungere indirizzi di notifica tramite posta [elettronica per la scadenza del certificato.](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)

Non è possibile modificare o personalizzare queste notifiche di posta elettronica ricevute da `aadnotification@microsoft.com` . Tuttavia, è possibile esportare le registrazioni delle app con certificati e segreti in scadenza tramite [script di PowerShell.](app-management-powershell-samples.md)

## <a name="who-can-update-the-certificates"></a>Chi può aggiornare i certificati?

Il proprietario dell'applicazione, l'amministratore globale o l'amministratore dell'applicazione può aggiornare i certificati portale di Azure'interfaccia utente, PowerShell o Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Sono necessari altri dettagli sulle opzioni di firma del certificato.

In Azure AD, è possibile configurare le opzioni di firma del certificato e l'algoritmo di firma del certificato. Per altre informazioni, vedere [Advanced SAML token certificate signing options for Azure AD apps (Opzioni avanzate](certificate-signing-options.md)di firma del certificato del token SAML per Azure AD app).

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>È necessario sostituire il certificato per le applicazioni Azure AD Application Proxy e sono necessarie altre istruzioni.

Per sostituire i certificati per Azure AD Application Proxy applicazioni, vedere Esempio di [PowerShell - Sostituire il](scripts/powershell-get-custom-domain-replace-cert.md)certificato in Application Proxy applicazioni .

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Ricerca per categorie i certificati per i domini personalizzati in Azure AD Application Proxy?

Per configurare un'app locale per l'uso di un dominio personalizzato, sono necessari un dominio personalizzato Azure Active Directory verificato, un certificato PFX per il dominio personalizzato e un'app locale da configurare. Per altre informazioni, vedere [Domini personalizzati in Azure AD Application Proxy](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>È necessario aggiornare il certificato di firma del token sul lato applicazione. Dove è possibile ottenerlo Azure AD lato sinistro?

È possibile rinnovare un certificato SAML X.509. Vedere [Certificato di firma SAML](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Che cos'Azure AD rollover della chiave di firma?

Per informazioni più dettagliate, vedere [qui](../develop/active-directory-signing-key-rollover.md). 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Ricerca per categorie il certificato di crittografia del token dell'applicazione?

Per rinnovare un certificato di crittografia del token dell'applicazione, vedere [Come rinnovare un certificato di crittografia dei token per un'applicazione aziendale.](howto-saml-token-encryption.md) 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Ricerca per categorie rinnovare il certificato di firma del token dell'applicazione?

Per rinnovare un certificato di firma del token dell'applicazione, vedere [Come rinnovare un certificato di firma di token per un'applicazione aziendale.](manage-certificates-for-federated-single-sign-on.md)

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Ricerca per categorie aggiornare Azure AD dopo la modifica dei certificati di federazione?

Per aggiornare Azure AD dopo aver modificato i certificati federativi, vedere [Rinnovare](../hybrid/how-to-connect-fed-o365-certs.md)i certificati di federazione per Microsoft 365 e Azure Active Directory .
