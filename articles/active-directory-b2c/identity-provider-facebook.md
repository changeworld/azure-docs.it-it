---
title: Configurare l'iscrizione e l'accesso con un account Facebook
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account Facebook alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 37fc33ae8084a2b4e99e7b5dc417eac70060eef5
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516194"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Facebook tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Creare un'applicazione Facebook

Per usare un account Facebook come [provider di identità](authorization-code-flow.md) in Azure Active Directory B2C (Azure ad B2C), è necessario creare un'applicazione nel tenant che la rappresenta. Se non si possiede già un account Facebook, è possibile crearlo sul sito [https://www.facebook.com/](https://www.facebook.com/).

1. Accedere al [sito Web Facebook for developers](https://developers.facebook.com/) con le credenziali dell'account Facebook.
1. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A tale scopo, selezionare **Accedi** nell'angolo in alto a destra della pagina, accettare le condizioni di Facebook e completare la procedura di registrazione.
1. Selezionare **Le mie app** e quindi **Creazione di un'app**.
1. Selezionare **Crea esperienze connesse**.
1. Inserire un **nome visualizzato** e una **email di contatto** valida.
1. Selezionare **Crea ID app**. Potrebbe essere necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.
1. Selezionare **Impostazioni** > **Base**.
    1. Scegliere una **Categoria**, ad esempio `Business and Pages`. Questo valore è richiesto da Facebook, ma non usato per Azure AD B2C.
    1. Immettere un URL per l' **URL delle condizioni** per il servizio, ad esempio `http://www.contoso.com/tos` . L'URL dei criteri è una pagina che viene mantenuta per fornire i termini e le condizioni per l'applicazione.
    1. Immettere un URL per l'**URL dell'Informativa sulla privacy**, ad esempio `http://www.contoso.com/privacy`. L'URL del criterio è una pagina da mantenere per fornire informazioni sulla privacy per l'applicazione.
1. Nella parte inferiore della pagina, selezionare **Aggiungi piattaforma**, quindi selezionare **Sito Web**.
1. In **URL sito** immettere l'indirizzo del sito Web, ad esempio `https://contoso.com` . 
1. Selezionare **Save changes** (Salva modifiche).
1. Nella parte superiore della pagina copiare l'**ID app**.
1. Fare clic su **Mostra** e copiare il valore di **Chiave segreta**. Sono necessari entrambi per configurare Facebook come provider di identità nel tenant. **App Segreta** è una credenziale di sicurezza importante.
1. Dal menu selezionare il segno **più** accanto a **prodotti**. In **Aggiungi prodotti all'app** selezionare **imposta** in account di accesso di **Facebook**.
1. Dal menu selezionare account di **accesso di Facebook** e selezionare **Impostazioni**.
1. In **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth valide) immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Sostituire `your-tenant-name` con il nome del tenant. Fare clic su **Salva le modifiche** nella parte inferiore della pagina.
1. Per rendere disponibile l'applicazione Facebook per Azure AD B2C, selezionare il selettore di stato nella parte superiore destra della pagina e **attivarlo per rendere** pubblica l'applicazione, quindi selezionare **modalità switch**.  A questo punto lo stato dovrebbe cambiare da **Sviluppo** a **Live**.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurare un account Facebook come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **Provider di identità** e quindi selezionare **Facebook**.
1. Immettere un **Nome**. Ad esempio, *Facebook*.
1. Per **ID client**, immettere l'ID app dell'applicazione Facebook creata in precedenza.
1. Per **Segreto client** immettere il Segreto app annotato in precedenza.
1. Selezionare **Salva**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-facebook-as-an-identity-provider"></a>Aggiungere Facebook come provider di identità

1. Nel file `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** sostituire il valore di `client_id` con l'ID dell'applicazione Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Aggiungere il provider di identità Facebook a un flusso utente 

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente che si vuole usare per il provider di identità Facebook.
1. In provider di identità basati su **Social Network** selezionare **Facebook**.
1. Selezionare **Salva**.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui flusso utente**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="upload-and-test-the-policy"></a>Caricare e testare i criteri

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Caricare il file *TrustFrameworkExtensions.xml* nel tenant.
1. In **Criteri personalizzati** selezionare **B2C_1A_signup_signin**.
1. Per **Seleziona applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **Esegui ora** e quindi Facebook per accedere con Facebook e testare i criteri personalizzati.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [passare il token di Facebook all'applicazione](idp-pass-through-user-flow.md).
