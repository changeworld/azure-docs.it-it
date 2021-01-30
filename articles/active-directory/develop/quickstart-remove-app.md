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
ms.openlocfilehash: 8953f9ca174414c55e6e06774167d3951d0bbd41
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090239"
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

Per eliminare un'applicazione, è possibile elencarla come proprietario dell'applicazione o disporre di privilegi di amministratore.

1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a> con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
1. Nel riquadro di spostamento sinistro selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app**. Trovare e selezionare l'applicazione che si vuole configurare. Dopo la selezione dell'app verrà visualizzata la pagina **Panoramica** dell'applicazione.
1. Nella pagina **Panoramica** selezionare **Elimina**.
1. Selezionare **Sì** per confermare l'eliminazione dell'app.

## <a name="remove-an-application-authored-by-another-organization"></a>Rimuovere un'applicazione creata da un'altra organizzazione

Se si visualizza **Registrazioni app** nel contesto di un tenant, un sottoinsieme delle applicazioni visualizzate nella scheda **Tutte le app** appartiene a un altro tenant ed è stato registrato nel tenant durante il processo di consenso. Più precisamente, sono rappresentate solo da un oggetto entità servizio nel tenant, senza oggetto applicazione corrispondente. Per altre informazioni sulle differenze fra oggetti applicazione e oggetti entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure AD](./app-objects-and-service-principals.md).

Per rimuovere l'accesso di un'applicazione alla directory (dopo aver concesso il consenso), l'amministratore della società deve rimuovere la relativa entità servizio. L'amministratore deve avere l'accesso Admininstrator globale e può rimuovere l'applicazione tramite il portale di Azure o usare i [cmdlet Azure ad PowerShell](/previous-versions/azure/jj151815(v=azure.100)) per rimuovere l'accesso.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli [oggetti applicazione ed entità servizio](app-objects-and-service-principals.md) in Microsoft Identity Platform.
