---
title: "Procedura: Cambiare i tipi di account supportati da un'applicazione | Azure"
titleSuffix: Microsoft identity platform
description: In questa procedura si configura un'applicazione registrata con Microsoft Identity Platform per cambiare chi o quali account possono accedervi.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 3ae6616263de605d5910f244423b9e7ffc036c5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103958"
---
# <a name="how-to-modify-the-accounts-supported-by-an-application"></a>Come modificare gli account supportati da un'applicazione

Quando si registra un'applicazione con Microsoft Identity Platform, si specifica chi o quali tipi di account possono accedervi. Ad esempio, è possibile specificare gli account solo della propria organizzazione, per cui l'app è a *tenant singolo*. Oppure è possibile specificare gli account di qualsiasi organizzazione (inclusa la propria), per cui l'app è *multi-tenant*.

Le sezioni seguenti illustrano come modificare la registrazione dell'app nel portale di Azure per cambiare chi o quali tipi di account possono accedere all'applicazione.

## <a name="prerequisites"></a>Prerequisiti

* Un'[applicazione registrata nel tenant di Azure AD](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Modificare la registrazione dell'applicazione per supportare account diversi

Per specificare un'impostazione diversa per i tipi di account supportati da una registrazione dell'app esistente:

1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Registrazioni app**, quindi selezionare l'applicazione.
1. Specificare ora chi può usare l'applicazione, ovvero i *destinatari per l'accesso*.

    | Tipi di account supportati | Descrizione |
    |-------------------------|-------------|
    | **Account solo in questa directory organizzativa** | Selezionare questa opzione se si intende creare un'applicazione utilizzabile solo da utenti (o guest) nel tenant *personale*.<br><br>Nota anche come applicazione *line-of-business* (LOB), si tratta di un'applicazione a **singolo tenant** in Microsoft Identity Platform. |
    | **Account in qualsiasi directory organizzativa** | Selezionare questa opzione se si vuole che gli utenti in *qualsiasi* tenant di Azure AD possano usare l'applicazione. Questa opzione è appropriata se, ad esempio, si sta creando un'applicazione SaaS (Software-As-A-Service) che si intende fornire a più organizzazioni.<br><br>È nota come applicazione **multi-tenant** in Microsoft Identity Platform. |
1. Selezionare **Salva**.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Perché il passaggio al multi-tenant può non riuscire

Il passaggio di una registrazione dell'app da singolo a multi-tenant a volte può non riuscire a causa di conflitti di nomi nell'URI dell'ID applicazione. Un esempio di URI dell'ID app è `https://contoso.onmicrosoft.com/myapp`.

L'URI dell'ID App è uno dei modi in cui un'applicazione viene identificata nei messaggi di protocollo. Per un'applicazione a tenant singolo, è sufficiente che l'URI dell'ID app sia univoco all'interno del tenant. Per un'applicazione multi-tenant, è necessario che sia univoco a livello globale in modo da Azure AD possa trovare l'app in tutti i tenant. L'univocità globale viene applicata richiedendo che il nome host dell'URI dell'ID app corrisponda a uno dei [domini verificati dell'entità di pubblicazione](howto-configure-publisher-domain.md) del tenant di Azure AD.

Se ad esempio il nome del tenant è *contoso.onmicrosoft.com*, `https://contoso.onmicrosoft.com/myapp` sarà un URI dell'ID app valido. Se il tenant ha il domini verificato *contoso.com*, un URI dell'ID app valido sarà anche `https://contoso.com/myapp`. Se l'URI dell'ID app non segue il secondo modello, `https://contoso.com/myapp`, la conversione della registrazione dell'app in multi-tenant non riesce.

Per altre informazioni sulla configurazione di un dominio dell'entità di pubblicazione verificato, vedere [Configurare un dominio verificato](howto-configure-publisher-domain.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui requisiti per [convertire un'app da tenant singolo a multitenant](howto-convert-app-to-be-multi-tenant.md).
