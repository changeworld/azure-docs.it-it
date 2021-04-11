---
title: Pubblicare in = SharePoint locale con il proxy di applicazione-Azure AD
description: Vengono illustrate le nozioni di base su come integrare un server SharePoint locale con Azure AD proxy di applicazione per SAML.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34aaafcd03e737b1e59529f8001e0c008bd39b70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104888873"
---
# <a name="integrate-with-sharepoint-saml"></a>Integrazione con SharePoint (SAML)

Questa guida dettagliata illustra come proteggere l'accesso al [Azure Active Directory SharePoint locale integrato (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) con Azure ad proxy di applicazione, in cui gli utenti dell'organizzazione (Azure ad, B2B) si connettono a SharePoint tramite Internet.

> [!NOTE] 
> Se non si ha familiarità con Azure AD proxy di applicazione e si desidera ottenere altre informazioni, vedere [accesso remoto alle applicazioni locali tramite Azure ad proxy di applicazione](./application-proxy.md).

Questa configurazione presenta tre vantaggi principali:

- Azure AD proxy di applicazione garantisce che il traffico autenticato possa raggiungere la rete interna e il server SharePoint.
- Gli utenti possono accedere ai siti di SharePoint come di consueto senza usare la VPN.
- È possibile controllare l'accesso in base all'assegnazione dell'utente in Azure AD livello del proxy dell'applicazione ed è possibile aumentare la sicurezza con Azure AD funzionalità come l'accesso condizionale e la Multi-Factor Authentication (multi-factor authentication).

Questo processo richiede due applicazioni aziendali. Una è un'istanza locale di SharePoint pubblicata dalla raccolta al proprio elenco di app SaaS gestite. Il secondo è un'applicazione locale (applicazione non della raccolta) che verrà usata per pubblicare la prima applicazione Enterprise Gallery.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa configurazione, sono necessarie le risorse seguenti:
 - Farm di SharePoint 2013 o versioni successive. La farm di SharePoint deve essere [integrata con Azure ad](../saas-apps/sharepoint-on-premises-tutorial.md).
 - Un tenant di Azure AD con un piano che include il proxy di applicazione. Scopri di più sui [piani di Azure ad e sui prezzi](https://azure.microsoft.com/pricing/details/active-directory/).
 - Un [dominio personalizzato e verificato](../fundamentals/add-custom-domain.md) nel tenant del Azure ad. Il dominio verificato deve corrispondere al suffisso dell'URL di SharePoint.
 - È necessario un certificato SSL. Vedere i dettagli nella [pubblicazione di domini personalizzati](./application-proxy-configure-custom-domain.md).
 - Gli utenti Active Directory locali devono essere sincronizzati con Azure AD Connect e devono essere configurati per [accedere ad Azure](../hybrid/plan-connect-user-signin.md). 
 - Per gli utenti guest solo cloud e B2B, è necessario [concedere l'accesso a un account Guest a SharePoint locale nella portale di Azure](../saas-apps/sharepoint-on-premises-tutorial.md#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal).
 - Un connettore del proxy di applicazione installato e in esecuzione in un computer all'interno del dominio aziendale.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>Passaggio 1: integrare SharePoint locale con Azure AD 

1. Configurare l'app locale di SharePoint. Per ulteriori informazioni, vedere [esercitazione: Azure Active Directory Single Sign-on l'integrazione con SharePoint locale](../saas-apps/sharepoint-on-premises-tutorial.md).
2. Convalidare la configurazione prima di passare al passaggio successivo. Per convalidare, provare ad accedere a SharePoint locale dalla rete interna e verificare che sia accessibile internamente. 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>Passaggio 2: pubblicare l'applicazione SharePoint locale con il proxy di applicazione

In questo passaggio viene creata un'applicazione nel tenant di Azure AD che usa il proxy di applicazione. Si imposta l'URL esterno e si specifica l'URL interno, entrambi usati in un secondo momento in SharePoint.

> [!NOTE] 
> Gli URL interni ed esterni devono corrispondere all' **URL di accesso** nella configurazione dell'applicazione basata su SAML nel passaggio 1.

   ![Screenshot che mostra il valore dell'URL di accesso.](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. Creare una nuova applicazione proxy di applicazione Azure AD con dominio personalizzato. Per istruzioni dettagliate, vedere [domini personalizzati nel proxy di applicazione Azure ad](./application-proxy-configure-custom-domain.md).

    - URL interno:' https://portal.contoso.com/ '
    - URL esterno:' https://portal.contoso.com/ '
    - Pre-autenticazione: Azure Active Directory
    - Converti URL nelle intestazioni: No
    - Convertire gli URL nel corpo dell'applicazione: No

        ![Screenshot che mostra le opzioni usate per creare l'app.](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. Assegnare gli [stessi gruppi](../saas-apps/sharepoint-on-premises-tutorial.md#create-an-azure-ad-security-group-in-the-azure-portal) assegnati all'applicazione raccolta di SharePoint locale.

3. Infine, passare alla sezione **Proprietà** e impostare **visibile per gli utenti** su **No**. Questa opzione garantisce che venga visualizzata solo l'icona della prima applicazione nel portale app personali ( https://myapplications.microsoft.com) .

   ![Screenshot che mostra dove impostare il visibile agli utenti? opzione.](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>Passaggio 3: testare l'applicazione

Utilizzando un browser da un computer in una rete esterna, passare al collegamento configurato durante il passaggio di pubblicazione. Verificare che sia possibile accedere con l'account di test configurato.