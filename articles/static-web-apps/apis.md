---
title: Supporto delle API in App Web statiche di Azure con Funzioni di Azure
description: Informazioni sulle funzionalità API supportate in App Web statiche di Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1fc5e1e6982686e7042e5b8ad55d72a4560b6aee
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737479"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Supporto delle API in App Web statiche di Azure (anteprima) con Funzioni di Azure

App Web statiche di Azure offre endpoint API serverless tramite [Funzioni di Azure](../azure-functions/functions-overview.md). Grazie a Funzioni di Azure, le API vengono dimensionate in modo dinamico in base alla richiesta e includono le funzionalità seguenti:

- **Sicurezza integrata** con accesso diretto ai dati utente su [autenticazione e autorizzazione basata su ruoli](user-information.md).
- **Routing automatico** in modo che la route _api_ sia disponibile in modo sicuro per l'app Web senza richiedere regole CORS personalizzate.
- **Trigger HTTP e** associazioni di input/output.

## <a name="configuration"></a>Configurazione

Gli endpoint API sono disponibili per l'app Web tramite la route _api_. Mentre questa route è fissa, è possibile controllare la cartella e il progetto in cui si trova l'app Funzioni di Azure app. Per cambiare questo percorso, [modificare il file YAML del flusso di lavoro](github-actions-workflow.md#build-and-deploy) che si trova nella cartella _.github/workflows_ del repository.

## <a name="constraints"></a>Vincoli

App Web statiche di Azure fornisce un'API tramite Funzioni di Azure. Le funzionalità di Funzioni di Azure sono incentrate su un set specifico di funzionalità che consentono di creare un'API per un'app Web e permettono all'app Web di connettersi all'API in modo sicuro. Queste funzionalità prevedono alcuni vincoli, tra cui:

- Il prefisso della route dell'API deve essere _api_.
- L'API deve essere Node.js 12, .NET Core 3.1 o Python 3.8 Funzioni di Azure app.
- Le regole di route per le funzioni API supportano [solo i reindirizzamenti](routes.md#redirects) [e la protezione delle route con ruoli](routes.md#securing-routes-with-roles).
- I trigger sono limitati a [HTTP.](../azure-functions/functions-bindings-http-webhook.md)
  - Sono supportate le [associazioni di](../azure-functions/functions-triggers-bindings.md#supported-bindings) input e output.
- I log sono disponibili solo se all'app per le funzioni si aggiunge [Application Insights](../azure-functions/functions-monitoring.md).
- Alcune impostazioni dell'applicazione vengono gestite dal servizio. Non è possibile configurare le impostazioni dell'app che iniziano con i prefissi seguenti: `APPSETTING_` , , , , , , , `AZUREBLOBSTORAGE_` , , `AZUREFILESSTORAGE_` , , `AZURE_FUNCTION_` , `CONTAINER_` `DIAGNOSTICS_` `DOCKER_` `FUNCTIONS_` `IDENTITY_` `MACHINEKEY_` `MAINSITE_` `MSDEPLOY_` `SCMSITE_` `SCM_` `WEBSITES_` `WEBSITE_` `WEBSOCKET_` `AzureWeb` .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'API](add-api.md)
