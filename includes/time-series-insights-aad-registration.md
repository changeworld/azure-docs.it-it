---
title: includere file
description: includere file
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 22411e5a80f555a3ead05d39466a7a175923d9bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104040"
---
* Dopo aver selezionato la piattaforma appropriata nel passaggio 4 di configurare le impostazioni della [piattaforma](../articles/active-directory/develop/quickstart-register-app.md#configure-platform-settings) , configurare gli **URI di reindirizzamento** e i token di **accesso** nel pannello laterale a destra dell'interfaccia utente.

    * È necessario che quanto specificato per **URI di reindirizzamento** corrisponda all'indirizzo fornito dalla richiesta di autenticazione:

        * Per le app ospitate in un ambiente di sviluppo locale, selezionare **Client pubblico (per dispositivi mobili e desktop)** . Assicurarsi di impostare **client pubblico** su **Sì**.
        * Per le app a pagina singola ospitate nel servizio app di Azure, selezionare **Web**.

    * Determinare se è appropriato impostare un **URL di disconnessione**.

    * Abilitare il flusso di concessione implicita selezionando **Token di accesso** o **Token ID**.

    [![Creare gli URI di reindirizzamento](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Fare clic su **Configura** e quindi su **Salva**.

* Associare l'app Azure Active Directory Azure Time Series Insights. Selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione** > **API usate dall'organizzazione**.

    [![Associare un'API all'app Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Digitare `Azure Time Series Insights` nella barra di ricerca e quindi selezionare `Azure Time Series Insights`.

* Specificare quindi il tipo di autorizzazione API richiesto dall'app. Per impostazione predefinita sarà evidenziata l'opzione **Autorizzazioni delegate**. Scegliere un tipo di autorizzazione e quindi selezionare **Aggiungi autorizzazioni**.

    [![Specificare il tipo di autorizzazione API richiesto dall'app](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* [Aggiungere le credenziali](../articles/active-directory/develop/quickstart-register-app.md#add-credentials) se l'applicazione chiamerà le API dell'ambiente in modo autonomo. Le credenziali consentono all'applicazione di eseguire l'autenticazione in modo autonomo, senza richiedere alcuna interazione utente in fase di esecuzione.