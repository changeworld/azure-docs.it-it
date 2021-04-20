---
title: Integrare Application Insights nel portale per sviluppatori
titleSuffix: Azure API Management
description: Informazioni su come integrare Application Insights nel portale per sviluppatori gestito o self-hosted.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 5e1c9caa55d0b3b7820f766a30c878fdc01f5137
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741722"
---
# <a name="integrate-application-insights-to-developer-portal"></a>Integrare Application Insights nel portale per sviluppatori

Una funzionalità comune di Monitoraggio di Azure è Application Insights. Si tratta di un servizio APM (Extensible Application Performance Management) per sviluppatori e professionisti DevOps. Usarlo per monitorare il portale per sviluppatori e rilevare le anomalie delle prestazioni. Application Insights strumenti di analisi avanzati che consentono di apprendere le effettive attività degli utenti durante la visita del portale per sviluppatori.

## <a name="add-application-insights-to-your-portal"></a>Aggiungere Application Insights al portale

Seguire questa procedura per collegare Application Insights al portale per sviluppatori gestito o self-hosted.

> [!IMPORTANT]
> I passaggi 1 e 2 non sono necessari per i portali gestiti. Se si dispone di un portale gestito, andare al passaggio 4.

1. Configurare un [ambiente locale per](developer-portal-self-host.md#step-1-set-up-local-environment) la versione più recente del portale per sviluppatori.

1. Installare il **pacchetto npm** per aggiungere [Paperbits per Azure:](https://github.com/paperbits/paperbits-azure)

    ```console
    npm install @paperbits/azure --save
    ```

1. Nel `startup.publish.ts` file nella cartella `src` importare e registrare il modulo Application Insights:

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. Recuperare la configurazione del portale:

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Estendere la configurazione del sito dal passaggio precedente con Application Insights configurazione:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)
- [Automatizzare le distribuzioni del portale](automate-portal-deployments.md)
- [Ospitare in modo self-host il portale per sviluppatori](developer-portal-self-host.md)
