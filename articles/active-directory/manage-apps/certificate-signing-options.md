---
title: Opzioni avanzate di firma del certificato token SAML per Azure AD app
description: Informazioni su come usare le opzioni avanzate di firma del certificato nel token SAML per le app preintegrate in Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb58f59b3796311f52d78ef87c8918f9888c152
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377871"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opzioni avanzate di firma del certificato nel token SAML per le app della raccolta in Azure Active Directory

Azure Active Directory (Azure AD) supporta attualmente migliaia di applicazioni preintegrate nella raccolta di app di Azure Active Directory. Oltre 500 applicazioni supportano l'accesso Single Sign-On usando il [protocollo Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0, ad esempio l'applicazione [NetSuite.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) Quando un cliente esegue l'autenticazione a un'applicazione Azure AD tramite SAML, Azure AD invia un token all'applicazione (tramite HTTP POST). L'applicazione convalida e usa quindi il token per accedere al cliente anziché richiedere un nome utente e una password. I token SAML vengono firmati con il certificato univoco generato in Azure AD e algoritmi standard specifici.

Azure AD usa alcune delle impostazioni predefinite per le applicazioni della raccolta. I valori predefiniti vengono configurati in base ai requisiti dell'applicazione.

In Azure AD, è possibile configurare le opzioni di firma del certificato e l'algoritmo di firma del certificato.

## <a name="certificate-signing-options"></a>Opzioni di firma del certificato

Azure AD supporta tre opzioni per la firma di certificati:

* **Firma asserzione SAML**. Per la maggior parte delle applicazioni della raccolta viene impostata questa opzione predefinita. Se si seleziona questa opzione, Azure AD come provider di identità (IdP) firma l'asserzione SAML e il certificato con il certificato [X.509](https://wikipedia.org/wiki/X.509) dell'applicazione.

* **Firma risposta SAML**. Se si seleziona questa opzione, Azure AD idp firma la risposta SAML con il certificato X.509 dell'applicazione.

* **Firma asserzione e risposta SAML**. Se si seleziona questa opzione, Azure AD idp firma l'intero token SAML con il certificato X.509 dell'applicazione.

## <a name="certificate-signing-algorithms"></a>Algoritmi per la firma di certificati

Azure AD supporta due algoritmi di firma o algoritmi hash sicuri per firmare la risposta SAML:

* **SHA-256**. Per firmare la risposta SAML, Azure AD usa questo algoritmo predefinito, È l'algoritmo più recente ed è più sicuro di SHA-1. La maggior parte delle applicazioni supporta l'algoritmo SHA-256. Se un'applicazione supporta solo SHA-1 come algoritmo di firma, è possibile modificare questa impostazione. In caso contrario è consigliabile usare l'algoritmo SHA-256 per firmare la risposta SAML.

* **SHA-1**. Questo algoritmo è meno recente e viene considerato meno sicuro rispetto a SHA-256. Se un'applicazione supporta solo questo algoritmo di firma, è possibile selezionare questa opzione nell'elenco a discesa **Algoritmo di firma**. Azure AD firmerà quindi la risposta SAML con l'algoritmo SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Modificare le opzioni di firma del certificato e l'algoritmo di firma

Per modificare le opzioni di firma del certificato SAML di un'applicazione e l'algoritmo di firma del certificato, selezionare l'applicazione in questione:

1. Nel portale [Azure Active Directory accedere](https://aad.portal.azure.com/)al proprio account. Viene **visualizzata Azure Active Directory'interfaccia di amministrazione.**
1. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Viene visualizzato un elenco delle applicazioni aziendali nell'account.
1. Selezionare un'applicazione. Viene visualizzata una pagina di panoramica per l'applicazione.

   ![Esempio: pagina di panoramica dell'applicazione](./media/certificate-signing-options/application-overview-page.png)

Modificare quindi le opzioni di firma del certificato nel token SAML per l'applicazione:

1. Nel riquadro sinistro della pagina di panoramica dell'applicazione selezionare **Single Sign-On.**
1. Se viene visualizzata la pagina Set up Single Sign-On with SAML - Preview (Configura Single **Sign-On con SAML - Anteprima),** andare al passaggio 5.
1. Se la pagina Selezionare un metodo di accesso Single **Sign-On** non viene visualizzata, selezionare Modifica modalità **Single Sign-On** per visualizzare la pagina.
1. Nella pagina Selezionare un metodo di accesso Single **Sign-On** selezionare **SAML,** se disponibile. Se **SAML** non è disponibile, l'applicazione non supporta SAML ed è possibile ignorare il resto della procedura e dell'articolo.
1. Nella pagina **Configura Single Sign-On con SAML - Anteprima** individuare l'intestazione Certificato di firma **SAML** e selezionare l'icona **Modifica** (a forma di matita). Viene visualizzata la pagina Certificato di firma **SAML.**

   ![Esempio: pagina certificato di firma SAML](./media/certificate-signing-options/saml-signing-page.png)

1. Nell'elenco a discesa **Opzioni di firma** scegliere **Firma risposta SAML**, **Firma asserzione SAML** o **Firma risposta e asserzione SAML**. Le descrizioni di queste opzioni sono disponibili in precedenza in questo articolo in [Opzioni di firma del certificato](#certificate-signing-options).
1. Nell'elenco a discesa **Algoritmo di firma** scegliere **SHA-1** o **SHA-256**. Le descrizioni di queste opzioni sono disponibili in precedenza in questo articolo nella [sezione Algoritmi di firma del](#certificate-signing-algorithms) certificato.
1. Se si è soddisfatti delle scelte effettuate, selezionare **Salva per** applicare le nuove impostazioni del certificato di firma SAML. In caso contrario, selezionare **la X** per annullare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare l'accesso Single Sign-On per applicazioni non incluse nella raccolta di app di Azure Active Directory](./configure-saml-single-sign-on.md)
* [Risolvere i problemi relativi all'accesso Single Sign-On basato su SAML](./debug-saml-sso-issues.md)