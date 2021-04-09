---
title: Configurare un flusso di modifica del profilo
titleSuffix: Azure AD B2C
description: Informazioni su come configurare un flusso di modifica del profilo in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f4f785feed022226ed533d378a8fa52080b9ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581899"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Configurare un flusso di modifica del profilo in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Flusso di modifica del profilo

I criteri di modifica del profilo consentono agli utenti di gestire gli attributi del profilo, ad esempio nome visualizzato, cognome, nome, città e altri. Il flusso di modifica del profilo prevede i passaggi seguenti: 

1. Iscriversi o accedere con un account locale o di social networking. Se la sessione è ancora attiva, Azure AD B2C autorizza l'utente e passa al passaggio successivo.
1. Azure AD B2C legge il profilo utente dalla directory e consente all'utente di modificare gli attributi.

![Flusso di modifica del profilo](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Prerequisiti

Se non è già stato fatto, [registrare un'applicazione Web in Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Creare un flusso utente di modifica del profilo

Se si vuole abilitare nell'applicazione la modifica del profilo da parte degli utenti, usare un flusso utente di modifica del profilo.

1. Nel menu della pagina di panoramica del tenant Azure AD B2C selezionare **Flussi utente** e quindi **Nuovo flusso utente**.
1. Nella pagina **Crea un flusso utente** selezionare il flusso utente **Modifica del profilo**. 
1. In **Selezionare una versione** selezionare **Consigliata**, quindi selezionare **Crea**.
1. Immettere un **nome** per il flusso utente. Ad esempio, *profileediting1*.
1. Per i **provider di identità** selezionare **accesso alla posta elettronica**.
1. Per **Attributi utente** scegliere gli attributi che vuole che il cliente possa modificate nel proprio profilo. Ad esempio, selezionare **Mostra più** e quindi scegliere gli attributi e le attestazioni per **Nome visualizzato** e **Posizione**. Fare clic su **OK**.
1. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, quindi accedere con l'account creato in precedenza.
1. A questo punto si ha la possibilità di modificare il nome visualizzato e la posizione dell'utente. Fare clic su **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Creare i criteri di modifica del profilo

I criteri personalizzati sono un set di file XML che vengono caricati nel tenant di Azure AD B2C per definire i percorsi utente. Sono disponibili Starter Pack con diversi criteri predefiniti, tra cui: iscrizione, accesso, reimpostazione della password e criteri di modifica del profilo. Per ulteriori informazioni, vedere [Introduzione ai criteri personalizzati in Azure ad B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

* Aggiungere un [accesso con il provider di identità di social](add-identity-provider.md)networking.