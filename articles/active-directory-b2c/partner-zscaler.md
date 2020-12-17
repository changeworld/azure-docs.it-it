---
title: Esercitazione per la configurazione di Azure Active Directory B2C con zScaler
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con zScaler
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 223f8acd6aad7aaf4c37e0b2eae2df882ed2ad1d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629375"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Esercitazione per configurare l'accesso privato a zScaler con Azure Active Directory B2C per l'accesso ibrido sicuro

In questa esercitazione si apprenderà come integrare Azure AD B2C autenticazione con [zScaler private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA offre un accesso sicuro e basato su criteri ad applicazioni e asset privati senza costi, problemi o rischi per la sicurezza di una rete privata virtuale (VPN). L'offerta di accesso ibrido sicuro di zScaler consente una superficie di attacco zero per le applicazioni rivolte agli utenti in combinazione con Azure AD B2C.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Un tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) collegato alla sottoscrizione di Azure.

- [Sottoscrizione di ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di ZPA include i componenti seguenti:

- Azure AD B2C: provider di identità (IdP) responsabile della verifica delle credenziali dell'utente. È anche responsabile dell'iscrizione di un nuovo utente.

- ZPA: servizio responsabile della protezione dell'applicazione Web mediante l'applicazione dell'accesso con [attendibilità zero](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).

- Applicazione Web: ospita il servizio a cui l'utente sta tentando di accedere.

Il diagramma seguente illustra il modo in cui ZPA si integra con Azure AD B2C.

![Immagine che mostra il diagramma dell'architettura zScaler](media/partner-zscaler/zscaler-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a un portale per gli utenti ZPA o a un'applicazione ZPA browser Access.
| 2. | ZPA richiede le informazioni sul contesto utente prima di poter decidere se consentire all'utente di accedere all'applicazione Web. Per autenticare l'utente, ZPA esegue un reindirizzamento SAML alla pagina di accesso Azure AD B2C.  
| 3. | L'utente arriva alla pagina di accesso di Azure AB B2C. Se si tratta di un nuovo utente, l'utente si iscrive per creare un nuovo account. Un utente esistente può accedere usando le credenziali esistenti. Azure AD B2C convalida l'identità dell'utente.
| 4. | Al completamento dell'autenticazione, Azure AD B2C reindirizza di nuovo l'utente a ZPA insieme all'asserzione SAML. ZPA verifica l'asserzione SAML e imposta il contesto utente.
| 5. | ZPA valuta i criteri di accesso per l'utente. Se l'utente è autorizzato ad accedere all'applicazione Web, la connessione può essere passata.

## <a name="onboard-to-zpa"></a>Onboarding in ZPA

In questa esercitazione si presuppone che sia già presente una configurazione funzionante di ZPA. Se si sta iniziando a usare ZPA, vedere [la guida alla configurazione dettagliata per ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-with-azure-ad-b2c"></a>Integrazione con Azure AD B2C

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Parte 1: configurare Azure AD B2C come IdP in ZPA

Per configurare Azure AD B2C come [provider di identità (IDP) in ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign):

1. Accedere al [portale di amministrazione di ZPA](https://admin.private.zscaler.com)

2. Passare ad **Amministrazione**  >  **configurazione IDP**

3. Selezionare **Aggiungi configurazione IDP**

4. Per **1 informazioni IDP**, immettere quanto segue:

   a. **Nome**: Azure ad B2C

   b. **Single Sign-on**: Seleziona utente

   c. **Domini**: scegliere i domini di autenticazione da associare al provider di identità e quindi fare clic su **fine** .

   ![Immagine che mostra le informazioni IDP](media/partner-zscaler/add-idp-configuration.png)

5. Selezionare **Avanti**

6. Per **2 metadati SP**:

   a. Copiare l'URL del provider di servizi e annotarlo per un uso successivo.

   b. Copiare l'ID entità del provider di servizi e annotarlo per un uso successivo.

   ![Immagine che mostra le informazioni sui metadati SP](media/partner-zscaler/sp-metadata.png)

7. Selezionare **Sospendi**

Il resto della configurazione IdP riprenderà dopo la configurazione di Azure AD B2C.

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>Parte 2: configurare i criteri personalizzati in Azure AD B2C

>[!Note]
>Questo passaggio è necessario solo se non sono già presenti criteri personalizzati. Se si dispone già di uno o più criteri personalizzati, è possibile ignorare questo passaggio.

L'articolo [Introduzione ai criteri personalizzati in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) fornisce istruzioni su come configurare i criteri personalizzati nel tenant di Azure ad B2C.

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Parte 3: registrare ZPA come applicazione SAML in Azure AD B2C

L'articolo [registra un'applicazione SAML in Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers) fornisce istruzioni su come configurare un'applicazione saml in Azure ad B2C. Nel [passaggio 3,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)verrà fornito l'URL dei metadati SAML di IDP usato da Azure ad B2C. Questa operazione sarà necessaria per un uso successivo.

Seguire le istruzioni riportate nel [passaggio 4,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest). Nel passaggio 4,2 dell'istruzione è necessario aggiornare il manifesto dell'applicazione. Aggiornare le proprietà come indicato di seguito:

- **identifierUris**: usare l'ID entità del provider di servizi indicato nella **parte 1, passaggio 6a**.

- **samlMetadataUrl**: ignorare questa proprietà perché ZPA non ospita un URL di metadati SAML.

- **replyUrlsWithType**: usare l'URL del provider di servizi indicato nella **parte 1, passaggio 6b**.

- **logoutUrl**: Ignora questa proprietà perché ZPA non supporta un URL di disconnessione.

Il resto della procedura non è pertinente per questa esercitazione.

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Parte 4: estrarre i metadati SAML di IdP da Azure AD B2C

Dal passaggio precedente è necessario ottenere un URL di metadati SAML nel formato seguente:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

dove `<tenant-name>` è il nome del tenant di Azure ad B2C e `<policy-name>` è il nome del criterio SAML personalizzato creato nell'ultimo passaggio.

Ad esempio, " https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata "

Aprire un Web browser e passare all'URL dei metadati SAML. Al caricamento della pagina, fare clic con il pulsante destro del mouse in un punto qualsiasi della pagina. Selezionare **Salva pagina con nome** e salvare il file nel computer. Questa operazione verrà usata nella parte successiva.

### <a name="part-5---complete-idp-configuration-on-zpa"></a>Parte 5: completare la configurazione di IdP in ZPA

Completare la [configurazione del provider di identità nel portale di amministrazione di ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) che è stato parzialmente configurato nella parte 1:

1. Accedere al [portale di amministrazione di ZPA](https://admin.private.zscaler.com)

2. Passare ad **Amministrazione**  >  **configurazione IDP**.

3. Selezionare il provider di identità configurato nella parte 1 e scegliere **Riprendi**.

4. Per **3 creare IDP**

   a. Passare al **file di metadati IDP**  >  **selezionare file** e caricare il file di metadati salvato nella parte 4.

   b. Verificare che lo **stato** della configurazione IDP sia **abilitato**.

   c. Selezionare **Salva**.

      ![Immagine che mostra le informazioni su Crea IDP](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testare la soluzione

Passare a un portale per gli utenti di ZPA o a un'applicazione di accesso al browser e testare il processo di iscrizione o accesso. Il risultato dovrebbe essere una corretta autenticazione SAML.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Introduzione ai criteri personalizzati in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [Registrare un'applicazione SAML in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [Guida dettagliata alla configurazione di ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [Configurazione di un IdP per l'accesso Single Sign-on](https://help.zscaler.com/zpa/configuring-idp-single-sign)
