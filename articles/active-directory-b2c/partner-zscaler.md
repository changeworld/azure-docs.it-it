---
title: 'Esercitazione: configurare Azure Active Directory B2C con zScaler'
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con zScaler.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: fe427150b15c6bccb97172ae751235d388c95c7b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675027"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Esercitazione: configurare l'accesso privato zScaler con Azure Active Directory B2C

In questa esercitazione si apprenderà come integrare Azure Active Directory B2C (Azure AD B2C) l'autenticazione con [zScaler private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA offre un accesso sicuro e basato su criteri ad applicazioni e asset privati senza costi, problemi o rischi per la sicurezza di una rete privata virtuale (VPN). L'offerta di accesso ibrido sicuro zScaler consente una superficie di attacco zero per le applicazioni rivolte agli utenti quando viene combinata con Azure AD B2C.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).  
- [Un tenant Azure ad B2C](./tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.  
- [Una sottoscrizione ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview).

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di ZPA include i componenti seguenti:

- **Azure ad B2C**: il provider di identità (IDP) responsabile della verifica delle credenziali dell'utente. È anche responsabile dell'iscrizione di un nuovo utente.  
- **ZPA**: servizio responsabile della protezione dell'applicazione Web mediante l'applicazione dell' [accesso con attendibilità zero](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).  
- **Applicazione Web**: ospita il servizio a cui l'utente sta tentando di accedere.

Il diagramma seguente illustra il modo in cui ZPA si integra con Azure AD B2C.

![Diagramma dell'architettura zScaler, che illustra il modo in cui ZPA si integra con Azure AD B2C.](media/partner-zscaler/zscaler-architecture-diagram.png)

La sequenza è descritta nella tabella seguente:

|Passaggio | Descrizione |
| :-----:| :-----------|
| 1 | Un utente arriva a un portale utenti di ZPA o a un'applicazione di accesso browser ZPA.
| 2 | ZPA richiede informazioni sul contesto dell'utente prima di poter decidere se consentire all'utente di accedere all'applicazione Web. Per autenticare l'utente, ZPA esegue un reindirizzamento SAML alla pagina di accesso Azure AD B2C.  
| 3 | L'utente arriva alla pagina di accesso di Azure AB B2C. I nuovi utenti si iscrivono per creare un account e gli utenti esistenti accedono con le credenziali esistenti. Azure AD B2C convalida l'identità dell'utente.
| 4 | Al completamento dell'autenticazione, Azure AD B2C reindirizza di nuovo l'utente a ZPA insieme all'asserzione SAML. ZPA verifica l'asserzione SAML e imposta il contesto utente.
| 5 | ZPA valuta i criteri di accesso per l'utente. Se l'utente è autorizzato ad accedere all'applicazione Web, la connessione può essere passata.

## <a name="onboard-to-zpa"></a>Onboarding in ZPA

In questa esercitazione si presuppone che sia già presente un programma di installazione di ZPA funzionante. Se si sta iniziando a usare ZPA, vedere la [Guida alla configurazione dettagliata per ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-zpa-with-azure-ad-b2c"></a>Integrare ZPA con Azure AD B2C

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Passaggio 1: configurare Azure AD B2C come IdP in ZPA

Per configurare Azure AD B2C come [IDP in ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign), eseguire le operazioni seguenti:

1. Accedere al portale di [amministrazione di ZPA](https://admin.private.zscaler.com).

1. Passare ad **Amministrazione**  >  **configurazione IDP**.

1. Selezionare **Aggiungi configurazione IDP**.

   Viene visualizzato il riquadro **Aggiungi configurazione IDP** .

   ![Screenshot della scheda "informazioni IdP" del riquadro "Aggiungi configurazione IdP".](media/partner-zscaler/add-idp-configuration.png)

1. Selezionare la scheda **IDP Information** , quindi eseguire le operazioni seguenti:

   a. Nella casella **nome** immettere **Azure ad B2C**.  
   b. In **Single Sign-on** selezionare **utente**.  
   c. Nell'elenco a discesa **domini** selezionare i domini di autenticazione da associare a questo IDP.

1. Selezionare **Avanti**.

1. Selezionare la scheda **metadati SP** , quindi eseguire le operazioni seguenti:

   a. In **URL provider di servizi** copiare o prendere nota del valore per utilizzarlo in un secondo momento.  
   b. In **ID entità del provider di servizi** copiare o prendere nota del valore per usarlo in seguito.

   ![Screenshot della scheda "metadati SP" nel riquadro "Aggiungi configurazione IdP".](media/partner-zscaler/sp-metadata.png)

1. Selezionare **Sospendi**.

Dopo aver configurato Azure AD B2C, il resto della configurazione di IdP riprende.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>Passaggio 2: configurare i criteri personalizzati in Azure AD B2C

>[!Note]
>Questo passaggio è necessario solo se non sono già stati configurati criteri personalizzati. Se si dispone già di uno o più criteri personalizzati, è possibile ignorare questo passaggio.

Per configurare i criteri personalizzati nel tenant di Azure AD B2C, vedere [Introduzione ai criteri personalizzati in Azure Active Directory B2C](./custom-policy-get-started.md).

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Passaggio 3: registrare ZPA come applicazione SAML in Azure AD B2C

Per configurare un'applicazione SAML in Azure AD B2C, vedere [registrare un'applicazione SAML in Azure ad B2C](./connect-with-saml-service-providers.md). 

Nel passaggio ["3,2 caricare e testare i metadati dei criteri"](./connect-with-saml-service-providers.md#32-upload-and-test-your-policy-metadata), copiare o annotare l'URL dei metadati SAML IdP usato da Azure ad B2C. Sarà necessario più avanti.

Seguire le istruzioni riportate nel passaggio ["4,2 aggiornare il manifesto dell'applicazione"](./connect-with-saml-service-providers.md#42-update-the-app-manifest). Nel passaggio 4,2 aggiornare le proprietà del manifesto dell'applicazione come segue:

- Per **identifierUris**: usare l'ID entità del provider di servizi copiato o annotato in precedenza in "passaggio 1.6. b".  
- Per **samlMetadataUrl**: ignorare questa proprietà, perché ZPA non ospita un URL di metadati SAML.  
- Per **replyUrlsWithType**: usare l'URL del provider di servizi copiato o annotato in precedenza in "passaggio 1.6. a".  
- Per **logoutUrl**: ignorare questa proprietà, perché ZPA non supporta un URL di disconnessione.

Il resto della procedura non è pertinente per questa esercitazione.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Passaggio 4: estrarre i metadati SAML di IdP da Azure AD B2C

Successivamente, è necessario ottenere un URL di metadati SAML nel formato seguente:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

Si noti che `<tenant-name>` è il nome del tenant di Azure ad B2C e `<policy-name>` è il nome dei criteri SAML personalizzati creati nel passaggio precedente.

L'URL potrebbe essere, ad esempio, `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata`.

Aprire un Web browser e passare all'URL dei metadati SAML. Fare clic con il pulsante destro del mouse in un punto qualsiasi della pagina, selezionare **Salva con nome** e quindi salvare il file nel computer per l'uso nel passaggio successivo.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>Passaggio 5: completare la configurazione di IdP in ZPA

Completare la [configurazione del provider di identità nel portale di amministrazione di ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) che è stato parzialmente configurato in precedenza in "passaggio 1: configurare Azure ad B2C come IDP in ZPA".

1. Nel [portale di amministrazione di ZPA](https://admin.private.zscaler.com)passare a **Amministrazione**  >  **IDP configurazione**.

1. Selezionare il provider di identità configurato in "passaggio 1" e quindi fare clic su **Riprendi**.

1. Nel riquadro **Aggiungi configurazione IDP** selezionare la scheda **Crea IDP** e quindi eseguire le operazioni seguenti:

   a. In **file di metadati IDP** caricare il file di metadati salvato in precedenza in "passaggio 4: estrarre i metadati SAML idp da Azure ad B2C".  
   b. Verificare che sia **abilitato** lo **stato** della configurazione IDP.  
   c. Selezionare **Salva**.

   ![Screenshot della scheda "Crea IdP" del riquadro "Aggiungi configurazione IdP".](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testare la soluzione

Passare a un portale per gli utenti di ZPA o a un'applicazione di accesso al browser e testare il processo di iscrizione o accesso. Il test deve avere esito positivo per l'autenticazione SAML.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Introduzione ai criteri personalizzati in Azure AD B2C](./custom-policy-get-started.md)
- [Registrare un'applicazione SAML in Azure AD B2C](./connect-with-saml-service-providers.md)
- [Guida dettagliata alla configurazione di ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Configurare un IdP per Single Sign-On](https://help.zscaler.com/zpa/configuring-idp-single-sign)