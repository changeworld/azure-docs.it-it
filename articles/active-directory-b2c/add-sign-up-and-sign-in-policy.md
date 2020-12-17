---
title: Configurare un flusso di iscrizione e accesso
titleSuffix: Azure AD B2C
description: Informazioni su come configurare un flusso di iscrizione e accesso in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 29dd67e9e6e15aaafec0cc47d89da32cbf369938
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618821"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Configurare un flusso di iscrizione e accesso in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Flusso di iscrizione e accesso

I criteri di iscrizione e accesso consentono agli utenti di: 

* Iscriversi con l'account locale
* Accedi con account locale
* Iscriversi o accedere con un account di social networking
* Reimpostazione della password

![Flusso di modifica del profilo](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Prerequisiti

Se non è già stato fatto, [registrare un'applicazione Web in Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Creare un flusso utente di iscrizione e accesso

Il flusso utente di iscrizione e accesso consente di gestire le esperienze di iscrizione e accesso tramite una singola configurazione. Gli utenti dell'applicazione vengono indirizzati sul percorso corretto a seconda del contesto.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Flussi utente** e quindi **Nuovo flusso utente**.

    ![Pagina Flussi utente nel portale con il pulsante Nuovo flusso utente evidenziato](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. Nella pagina **Crea un flusso utente** selezionare il flusso utente **Iscrizione e accesso**.

    ![Pagina Selezionare un tipo di flusso utente con il flusso Iscrizione e accesso evidenziato](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. In **Selezionare una versione** selezionare **Consigliata**, quindi selezionare **Crea**. Per altre informazioni sulle versioni dei flussi utente, vedere [qui](user-flow-versions.md).

    ![Pagina Crea un flusso utente nel portale di Azure con proprietà evidenziate](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Immettere un **nome** per il flusso utente. Ad esempio, *signupsignin1*.
1. Per i **provider di identità** selezionare **iscrizione posta elettronica**.
1. Per **Attributi e attestazioni utente**, scegliere le attestazioni e gli attributi che si vogliono raccogliere e inviare all'utente durante l'iscrizione. Ad esempio, selezionare **Mostra più** e quindi scegliere gli attributi e le attestazioni per **Paese/Area geografica**, **Nome visualizzato** e **Codice postale**. Fare clic su **OK**.

    ![Pagina di selezione di attributi e attestazioni utente con tre attestazioni selezionate](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Fare clic su **Crea** per aggiungere il flusso utente. Viene automaticamente aggiunto al nome il prefisso *B2C_1*.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, quindi selezionare **Iscriversi adesso**.

    ![Pagina Esegui il flusso utente nel portale con il pulsante Esegui il flusso utente evidenziato](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.PNG)

1. Immettere un indirizzo di posta elettronica valido, fare clic su **Invia codice di verifica**, immettere il codice di verifica ricevuto e quindi selezionare **Verifica codice**.
1. Immettere una nuova password e confermarla.
1. Selezionare il paese e l'area geografica, immettere il nome visualizzato desiderato, immettere un codice postale e quindi fare clic su **Crea**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.
1. Ora si può eseguire di nuovo il flusso utente e dovrebbe essere possibile accedere con l'account creato. Il token restituito include le attestazioni selezionate di paese/area geografica, nome e codice postale.

> [!NOTE]
> L'esperienza "Esegui il flusso utente" non è attualmente compatibile con l'URL di risposta di tipo applicazione a pagina singola con il flusso di codice di autorizzazione. Per usare l'esperienza "Esegui il flusso utente" con questi tipi di app, registrare un URL di risposta di tipo "Web" e abilitare il flusso implicito come descritto [qui](tutorial-register-spa.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Creare criteri di iscrizione e di accesso

I criteri personalizzati sono un set di file XML che vengono caricati nel tenant di Azure AD B2C per definire i percorsi utente. Sono disponibili Starter Pack con diversi criteri predefiniti, tra cui: iscrizione, accesso, reimpostazione della password e criteri di modifica del profilo. Per ulteriori informazioni, vedere [Introduzione ai criteri personalizzati in Azure ad B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

* Aggiungere un [accesso con il provider di identità di social](add-identity-provider.md)networking.
* Configurare un [flusso di reimpostazione della password](add-password-reset-policy.md).
