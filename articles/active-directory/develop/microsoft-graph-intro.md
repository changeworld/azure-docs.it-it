---
title: API Microsoft Graph
description: L Microsoft Graph API è un'API Web RESTful che consente di accedere alle risorse del servizio cloud Microsoft.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 2e689e620a5aeb7c5028f1a1b30dd6def8e447ab
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529985"
---
# <a name="microsoft-graph-api"></a>API Microsoft Graph

L Microsoft Graph API è un'API Web RESTful che consente di accedere alle risorse del servizio cloud Microsoft. Dopo aver registrato l'app e averne i token di autenticazione per un utente o un servizio, è possibile effettuare richieste all'API Microsoft Graph. Per altre informazioni, vedere [Panoramica di Microsoft Graph](/graph/overview).

Microsoft Graph espone le API REST e le librerie client per accedere ai dati nei servizi Microsoft 365 seguenti:
- Microsoft 365 servizi: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner e SharePoint
- Servizi di mobilità e sicurezza aziendali: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager e Intune
- Windows 10 servizi: attività, dispositivi, notifiche
- Dynamics 365 Business Central

## <a name="versions"></a>Versioni

Microsoft Graph attualmente supporta due versioni: v1.0 e beta. La versione 1.0 include API disponibili a livello generale. Usare la versione 1.0 per tutte le app di produzione. La versione beta include LE API attualmente in anteprima. Poiché è possibile introdurre modifiche di rilievo alle API beta, è consigliabile usare la versione beta solo per testare le app in fase di sviluppo. non usare API beta nelle app di produzione. Per altre informazioni, vedere [Controllo delle versioni, supporto e criteri di modifica](/graph/versioning-and-support)di rilievo per Microsoft Graph .

Per iniziare a usare le API beta, vedere le informazioni di Microsoft Graph [endpoint beta](/graph/api/overview?view=graph-rest-beta&preserve-view=true)

Per iniziare a usare le API v1.0, Microsoft Graph informazioni di riferimento [sull'API REST v1.0](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)

## <a name="get-started"></a>Introduzione

Per leggere o scrivere in una risorsa, ad esempio un utente o un messaggio di posta elettronica, creare una richiesta simile alla seguente:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Per altre informazioni sugli elementi della richiesta costruita, vedere [Usare l'API Microsoft Graph](/graph/use-the-api)

Sono disponibili esempi di avvio rapido per illustrare come accedere alla potenza dell Microsoft Graph API. Gli esempi disponibili accedono a due servizi con un'autenticazione: account Microsoft e Outlook. Ogni guida introduttiva accede alle informazioni account Microsoft profili degli utenti e visualizza gli eventi dal calendario.
Le guide introduttive prevedono quattro passaggi:
- Selezionare la piattaforma
- Ottenere l'ID app (ID client)
- Compilare l'esempio
- Accedere e visualizzare gli eventi nel calendario

Al termine dell'avvio rapido, è disponibile un'app pronta per l'esecuzione. Per altre informazioni, vedere le domande frequenti Microsoft Graph [guida introduttiva.](/graph/quick-start-faq) Per iniziare a usare gli esempi, vedere Microsoft Graph [Guida introduttiva.](https://developer.microsoft.com/graph/quick-start)

## <a name="tools"></a>Strumenti

Microsoft Graph Explorer è uno strumento basato sul Web che è possibile usare per compilare e testare le richieste usando Microsoft Graph API. È possibile accedere a Microsoft Graph Explorer all'indirizzo: `https://developer.microsoft.com/graph/graph-explorer` .

Postman è uno strumento che è anche possibile usare per compilare e testare le richieste usando Microsoft Graph API. È possibile scaricare Postman all'indirizzo: `https://www.getpostman.com/` . Per interagire con Microsoft Graph in Postman, usare la raccolta Microsoft Graph in Postman. Per altre informazioni, vedere [Usare Postman con l'API Microsoft Graph .](/graph/use-postman)
