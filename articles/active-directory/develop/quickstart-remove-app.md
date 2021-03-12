---
title: "Procedura: Rimuovere un'app registrata da Microsoft Identity Platform | Azure"
titleSuffix: Microsoft identity platform
description: Questa procedura descrive come rimuovere un'applicazione registrata con Microsoft Identity Platform.
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
ms.openlocfilehash: e04884c078bd9a5693ddcbc4e71470bb23e13d60
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199806"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Come rimuovere un'applicazione registrata con Microsoft Identity Platform

Gli sviluppatori aziendali e i provider SaaS (software-as-a-Service) con applicazioni registrate con la piattaforma di identità Microsoft potrebbero dover rimuovere la registrazione di un'applicazione.

Nelle sezioni seguenti si apprenderà come:

* Rimuovere un'applicazione creata dall'utente o dalla relativa organizzazione
* Rimuovere un'applicazione creata da un'altra organizzazione

## <a name="prerequisites"></a>Prerequisiti

* Un'[applicazione registrata nel tenant di Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Rimuovere un'applicazione creata dall'utente o dalla relativa organizzazione

Le applicazioni registrate dall'utente o dalla relativa organizzazione vengono rappresentate nel tenant sia da un oggetto applicazione che da un oggetto entità servizio. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio](./app-objects-and-service-principals.md).

> [!NOTE]
> L'eliminazione di un'applicazione eliminerà anche l'oggetto entità servizio nella directory principale dell'applicazione. Per le applicazioni multi-tenant, gli oggetti entità servizio in altre directory non verranno eliminati.

Per eliminare un'applicazione, è possibile elencarla come proprietario dell'applicazione o disporre di privilegi di amministratore.

1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
1. Se si ha accesso a più tenant, usare il filtro **directory + sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto per selezionare il tenant in cui è registrata l'app.
1. Cercare e selezionare il **Azure Active Directory**. 
1. In **Gestisci** selezionare **registrazioni app**  e selezionare l'applicazione che si desidera configurare. Dopo la selezione dell'app verrà visualizzata la pagina **Panoramica** dell'applicazione.
1. Nella pagina **Panoramica** selezionare **Elimina**.
1. Leggere le conseguenze dell'eliminazione.  Selezionare la casella se visualizzata nella parte inferiore del riquadro.
1. Selezionare **Elimina** per confermare che si vuole eliminare l'app.

## <a name="remove-an-application-authored-by-another-organization"></a>Rimuovere un'applicazione creata da un'altra organizzazione

Se si visualizza **Registrazioni app** nel contesto di un tenant, un sottoinsieme delle applicazioni visualizzate nella scheda **Tutte le app** appartiene a un altro tenant ed è stato registrato nel tenant durante il processo di consenso. Più precisamente, sono rappresentate solo da un oggetto entità servizio nel tenant, senza oggetto applicazione corrispondente. Per altre informazioni sulle differenze fra oggetti applicazione e oggetti entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure AD](./app-objects-and-service-principals.md).

Per rimuovere l'accesso di un'applicazione alla directory (dopo aver concesso il consenso), l'amministratore della società deve rimuovere la relativa entità servizio. L'amministratore deve avere l'accesso Admininstrator globale e può rimuovere l'applicazione tramite il portale di Azure o usare i [cmdlet Azure ad PowerShell](/previous-versions/azure/jj151815(v=azure.100)) per rimuovere l'accesso.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli [oggetti applicazione ed entità servizio](app-objects-and-service-principals.md) in Microsoft Identity Platform.
