---
title: Prerequisiti per accedere ai dati di analisi a livello di codice
description: Informazioni sui requisiti che è necessario soddisfare per poter accedere ai dati di analisi del Marketplace commerciale a livello di codice.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: b61608c0cb53ab808c5d3d789ec5ddc318c6923d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106803"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>Prerequisiti per accedere ai dati di analisi a livello di codice

Prima di poter accedere ai dati di analisi del Marketplace commerciale a livello di codice, è necessario soddisfare i requisiti seguenti.

## <a name="commercial-marketplace-enrollment"></a>Registrazione del Marketplace commerciale

Per accedere ai dati di analisi del Marketplace commerciale a livello di codice, è necessario essere registrati nel programma commerciale Marketplace e avere un account del centro per i partner. Per informazioni, vedere [creare un account Marketplace commerciale nel centro per i partner](create-account.md).

## <a name="create-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Le credenziali utente normali non possono essere usate per l'accesso a livello di codice ai dati di analisi del Marketplace commerciale. È necessario creare un'applicazione di Azure Active Directory (Azure AD) insieme a un segreto per accedere alle API di analisi. Per informazioni su come creare un'applicazione Azure AD e un segreto, vedere [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](../active-directory/develop/quickstart-register-app.md).

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>Associare l'applicazione Azure AD al tenant del centro per i partner

L'applicazione Azure AD creata in portale di Azure deve essere collegata all'account del centro per i partner. Attenersi alla procedura seguente:

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard).
1. In alto a destra selezionare l'icona a forma di ingranaggio e quindi selezionare **Impostazioni account**.
1. Nel menu **Impostazioni account** selezionare **Gestione utenti**.
1. Selezionare **Azure ad applicazioni** , quindi selezionare **+ Crea Azure ad applicazione**.
1. Selezionare l'applicazione Azure AD creata in portale di Azure e quindi fare clic su **Avanti**.
1. Selezionare la casella di controllo **gestione (Windows)** , quindi selezionare **Aggiungi**.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="Viene illustrata la pagina Crea applicazione Azure AD con le caselle di controllo per la selezione dei ruoli.":::

## <a name="generate-an-azure-ad-token"></a>Genera un token di Azure AD

È necessario generare un token di Azure AD usando l'ID applicazione (client). Questo ID consente di identificare in modo univoco l'applicazione client nella piattaforma di identità Microsoft e il segreto client del passaggio precedente. Per la procedura per generare un token di Azure AD, vedere [chiamate da servizio a servizio tramite le credenziali client (segreto condiviso o certificato)](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).

> [!NOTE]
> Il token è valido per un'ora.

## <a name="next-steps"></a>Passaggi successivi

- [Paradigma di accesso a livello di codice](analytics-programmatic-access.md)