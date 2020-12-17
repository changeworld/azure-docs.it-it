---
title: Configurare un flusso di reimpostazione della password
titleSuffix: Azure AD B2C
description: Informazioni su come configurare un flusso di reimpostazione della password in Azure Active Directory B2C.
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
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618832"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Configurare un flusso di reimpostazione della password in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>Flusso rest della password

I criteri di reimpostazione della password consentono agli utenti di reimpostare la propria password dimenticata. Il flusso di reimpostazione della password prevede i passaggi seguenti: 
1. Dalla pagina di iscrizione e accesso, l'utente fa clic su "password dimenticata?" la creazione. Azure AD B2C restituisce all'app il codice di errore AADB2C90118. Il codice di errore viene gestito dall'app richiamando i criteri di reimpostazione della password. 
1. Gli utenti forniscono e verificano la posta elettronica con un codice di accesso temporizzato.
1. Immettere una nuova password.

![Flusso di reimpostazione della password](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>Prerequisiti

Se non è già stato fatto, [registrare un'applicazione Web in Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>Creare un flusso utente di reimpostazione delle password

Per consentire agli utenti dell'applicazione di reimpostare la propria password, si può usare il flusso utente Reimpostazione password.

1. Nel menu della pagina di panoramica del tenant Azure AD B2C selezionare **Flussi utente** e quindi **Nuovo flusso utente**.
1. Nella pagina **Crea un flusso utente** selezionare il flusso utente **Reimpostazione password**. 
1. In **Selezionare una versione** selezionare **Consigliata**, quindi selezionare **Crea**.
1. Immettere un **nome** per il flusso utente. Ad esempio, *passwordreset1*.
1. Per **Provider di identità** abilitare **Ripristinare la password usando la verifica tramite posta elettronica**.
2. In Attestazioni dell'applicazione fare clic su **Mostra altro** e scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione. Selezionare ad esempio **ID oggetto dell'utente**.
3. Fare clic su **OK**.
4. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, verificare l'indirizzo di posta elettronica dell'account creato in precedenza e selezionare **Continua**.
1. Ora è possibile modificare la password per l'utente. Cambiare la password e selezionare **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>Creare i criteri di reimpostazione delle password

I criteri personalizzati sono un set di file XML che vengono caricati nel tenant di Azure AD B2C per definire i percorsi utente. Sono disponibili Starter Pack con diversi criteri predefiniti, tra cui: iscrizione, accesso, reimpostazione della password e criteri di modifica del profilo. Per ulteriori informazioni, vedere [Introduzione ai criteri personalizzati in Azure ad B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Configurare un [flusso di modifica del profilo](add-profile-editing-policy.md).