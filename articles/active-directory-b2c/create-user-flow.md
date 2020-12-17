---
title: Creare un flusso utente - Azure Active Directory B2C
description: Informazioni su come creare flussi utente nel portale di Azure per consentire l'iscrizione, l'accesso e la modifica dei profili utente per le applicazioni in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 418446e0d465b606b8d580297cebd73c466d4841
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109013"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Creare un flusso utente in Azure Active Directory B2C

È possibile creare [flussi utente](user-flow-overview.md) di tipi diversi nel proprio tenant di Azure Active Directory B2C (Azure AD B2C) e usarli nelle applicazioni in base alle esigenze. I flussi utente possono essere usati per più applicazioni.

> [!IMPORTANT]
> Il modo in cui si fa riferimento alle versioni dei flussi utente è stato cambiato. In precedenza, venivano offerte le versioni V1 (pronte per l'ambiente di produzione) e le versioni V 1.1 e V2 (anteprima). Ora questi flussi utente sono stati consolidati nelle versioni **Consigliata** (anteprima di nuova generazione) e **Standard** (disponibile a livello generale). Tutti i flussi utente delle anteprime legacy V 1.1 e V2 verranno deprecati entro il **1° agosto 2021**. Per informazioni dettagliate, vedere [Versioni dei flussi utente in Azure AD B2C](user-flow-versions.md).

## <a name="before-you-begin"></a>Prima di iniziare

- **Registrare l'applicazione** che si vuole usare per testare il nuovo flusso utente. Per un esempio, vedere l'[Esercitazione: Registrare un'applicazione Web in Azure AD B2C](tutorial-register-applications.md).
- **Aggiungere provider di identità esterni** se si vuole consentire agli utenti di accedere con provider come Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft o Twitter. Per un esempio, vedere [Esercitazione: Aggiungere provider di identità alle applicazioni in Azure AD B2C](tutorial-add-identity-providers.md).
- **Configurare il provider di identità dell'account locale** per specificare i tipi di identità (posta elettronica, nome utente, numero di telefono) che si vogliono supportare per gli account locali nel tenant. È quindi possibile scegliere tra questi tipi di identità supportati quando si creano flussi utente singoli. Quando un utente completa il flusso utente, viene creato un account locale nella directory Azure AD B2C e il provider di identità dell'**account locale** autentica le informazioni dell'utente. Configurare il provider di identità dell'account locale del tenant attenendosi alla procedura seguente:

   1. Accedere al [portale di Azure](https://portal.azure.com/). 
   2. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory contenente il tenant di Azure AD B2C.
   3. Nella barra di ricerca in alto nel portale di Azure cercare e selezionare **Azure AD B2C**.
   4. In **Gestisci** selezionare **Provider di identità**.
   5. Nell'elenco dei provider di identità, selezionare **Account locale**.
   6. Nella pagina **Configure local IDP** selezionare tutti i tipi di identità che si vogliono supportare. Selezionare queste opzioni per renderle disponibili per i flussi utente creati in un secondo momento:
      - **Telefono** (anteprima): Consente a un utente di immettere un numero di telefono, che viene verificato al momento dell'iscrizione e diventa il suo ID utente.
      - **E-mail** (predefinito): Consente a un utente di immettere un indirizzo e-mail, che viene verificato al momento dell'iscrizione e diventa il suo ID utente.
      - **Nome utente**: Consente a un utente di creare il proprio ID utente univoco. Viene raccolto e verificato un indirizzo di posta elettronica dell'utente.
    7. Selezionare **Salva**.

## <a name="create-a-user-flow"></a>Creare un flusso utente

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

    ![Tenant B2C, riquadro Directory e sottoscrizione, portale di Azure](./media/create-user-flow/directory-subscription-pane.png)

3. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
4. In **Criteri** selezionare **Flussi utente** e quindi **Nuovo flusso utente**.

    ![Pagina Flussi utente nel portale con il pulsante Nuovo flusso utente evidenziato](./media/create-user-flow/signup-signin-user-flow.png)

5. Nella pagina **Creare un flusso utente**, selezionare il tipo di flusso utente che si vuole creare (vedere [Flussi utente in Azure AD B2C](user-flow-overview.md) per una panoramica).

    ![Pagina Selezionare un tipo di flusso utente con il flusso Iscrizione e accesso evidenziato](./media/create-user-flow/select-user-flow-type.png)

6. In **Selezionare una versione** selezionare **Consigliata**, quindi selezionare **Crea**. Per altre informazioni sulle versioni dei flussi utente, vedere [qui](user-flow-versions.md).

    ![Pagina Crea un flusso utente nel portale di Azure con proprietà evidenziate](./media/create-user-flow/select-version.png)

7. Immettere un **Nome** per il flusso utente (ad esempio, *signupsignin1*, *profileediting1*, *passwordreset1*).
8. In **Provider di identità** scegliere le opzioni a seconda del tipo di flusso utente che si sta creando:

   - **Account locale**. Se si vuole consentire agli utenti di creare account locali nel tenant di Azure AD B2C, selezionare il tipo di identificatore che dovranno usare, ad esempio posta elettronica, ID utente o telefono. Sono elencati solo i tipi di identità configurati nelle impostazioni del [provider di identità dell'account locale](#before-you-begin).

   - **Provider di identità basati su social network**. Se si vuole consentire agli utenti di accedere con [provider di identità basati su social network](tutorial-add-identity-providers.md), come Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft o Twitter, selezionare i provider dall'elenco.

9. Per **Attributi e attestazioni utente**, scegliere le attestazioni e gli attributi che si vogliono raccogliere e inviare all'utente durante l'iscrizione. Selezionare **Mostra altro**. Selezionare gli attributi e le attestazioni, quindi selezionare **OK**.

    ![Pagina di selezione di attributi e attestazioni utente con tre attestazioni selezionate](./media/create-user-flow/signup-signin-attributes.png)

10. Selezionare **Crea** per aggiungere il flusso utente. Viene automaticamente aggiunto al nome il prefisso *B2C_1*.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare **Criteri** > **Flussi utente** e quindi selezionare il flusso utente creato. Nella pagina Panoramica del flusso utente, selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web registrata al passaggio 1. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **Esegui il flusso utente**.
2. A seconda del tipo di flusso utente che si sta testando, iscriversi usando un indirizzo di posta elettronica valido e seguire il flusso di iscrizione oppure accedere con un account creato in precedenza.

    ![Pagina Esegui il flusso utente nel portale con il pulsante Esegui il flusso utente evidenziato](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. Seguire i prompt dei flussi utente. Quando si completa il flusso utente, Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

> [!NOTE]
> L'esperienza "Esegui il flusso utente" non è attualmente compatibile con l'URL di risposta di tipo applicazione a pagina singola con il flusso di codice di autorizzazione. Per usare l'esperienza "Esegui il flusso utente" con questi tipi di app, registrare un URL di risposta di tipo "Web" e abilitare il flusso implicito come descritto [qui](tutorial-register-spa.md).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere l'accesso condizionale ai flussi utente di Azure AD B2C](conditional-access-user-flow.md)
- [Personalizzare l'interfaccia utente in un flusso utente di Azure AD B2C](customize-ui-with-html.md)
