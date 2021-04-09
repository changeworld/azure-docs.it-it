---
title: Configurare un flusso di accesso
titleSuffix: Azure Active Directory B2C
description: Informazioni su come configurare un flusso di accesso in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2310bd39c39036b6d6ac919517fa5539d7b70779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581865"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Configurare un flusso di accesso in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Panoramica del flusso di accesso

I criteri di accesso consentono agli utenti di: 

* Gli utenti possono accedere con un account Azure AD B2C locale
* Iscriversi o accedere con un account di social networking
* Reimpostazione della password
* Gli utenti non possono iscriversi per un account Azure AD B2C locale. Per creare un account, un amministratore può utilizzare [portale di Azure](manage-users-portal.md#create-a-consumer-user)o [MS API Graph](microsoft-graph-operations.md).

![Flusso di modifica del profilo](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Prerequisiti

Se non è già stato fatto, [registrare un'applicazione Web in Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Creare un flusso utente di accesso

Per aggiungere i criteri di accesso:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Flussi utente** e quindi **Nuovo flusso utente**.
1. Nella pagina **Crea un flusso utente** selezionare il flusso utente **di accesso** .
1. In **Selezionare una versione** selezionare **Consigliata**, quindi selezionare **Crea**. Per altre informazioni sulle versioni dei flussi utente, vedere [qui](user-flow-versions.md).
1. Immettere un **nome** per il flusso utente. Ad esempio, *signupsignin1*.
1. Per i **provider di identità** selezionare **accesso alla posta elettronica**.
1. Per le **attestazioni dell'applicazione**, scegliere le attestazioni e gli attributi da inviare all'applicazione. Ad esempio, selezionare **Mostra altro**, quindi scegliere attributi e attestazioni per **nome visualizzato**, **nome**,  **Cognome** e **ID oggetto dell'utente**. Fare clic su **OK**.
1. Fare clic su **Crea** per aggiungere il flusso utente. Viene automaticamente aggiunto al nome il prefisso *B2C_1*.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**.
1. Dovrebbe essere possibile accedere con l'account creato (usando MS API Graph), senza il collegamento per l'iscrizione. Il token restituito include le attestazioni selezionate.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>Rimuovere il collegamento di iscrizione

Il profilo tecnico **SelfAsserted-LocalAccountSignin-email** è un [autocertificato](self-asserted-technical-profile.md), che viene richiamato durante il flusso di iscrizione o di accesso. Per rimuovere il collegamento di iscrizione, impostare i `setting.showSignupLink` metadati su `false` . Eseguire l'override dei profili tecnici SelfAsserted-LocalAccountSignin-email nel file di estensione. 

1. Aprire il file di estensioni dei criteri, ad esempio _`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_.
1. Trovare l'elemento `ClaimsProviders`. Se l'elemento non esiste, aggiungerlo.
1. Aggiungere il provider di attestazioni seguente all' `ClaimsProviders` elemento:

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. All'interno `<BuildingBlocks>` dell'elemento aggiungere il [ContentDefinition](contentdefinitions.md) seguente per fare riferimento alla versione 1.2.0 o all'URI dei dati più recente:

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>Aggiornare e testare i criteri

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD. A tale scopo, selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Fare clic su **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **carica criteri personalizzati**, quindi caricare il file di criteri modificato *TrustFrameworkExtensions.xml*.
1. Selezionare i criteri di accesso caricati, quindi fare clic sul pulsante **Esegui adesso** .
1. Dovrebbe essere possibile accedere con l'account creato (usando MS API Graph), senza il collegamento per l'iscrizione.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

* Aggiungere un [accesso con il provider di identità di social](add-identity-provider.md)networking.
* Configurare un [flusso di reimpostazione della password](add-password-reset-policy.md).
