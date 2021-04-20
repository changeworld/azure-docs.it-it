---
title: Integrare Google Tag Manager nel portale per sviluppatori
titleSuffix: Azure API Management
description: Informazioni su come collegare Google Tag Manager al portale per sviluppatori gestito o self-hosted in Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c209eb782787146d947b4684d41c5d1e9bb6364e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741723"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>Integrare Google Tag Manager per API Management per sviluppatori

[Google Tag Manager](https://developers.google.com/tag-manager) è un sistema di gestione dei tag creato da Google. È possibile usarlo per gestire i tag JavaScript e HTML usati per il rilevamento e l'analisi nei siti Web. Ad esempio, è possibile usare Google Tag Manager per integrare Google Analytics, mappe termoreche o chatbot come LiveChat.

Seguire la procedura descritta in questo articolo per Google Tag Manager al portale per sviluppatori gestito o self-hosted in Azure API Management.

## <a name="add-google-tag-manager-to-your-portal"></a>Aggiungere Google Tag Manager al portale

Seguire questa procedura per collegare Google Tag Manager al portale per sviluppatori gestito o self-hosted.

> [!IMPORTANT]
> I passaggi 1 e 2 non sono necessari per i portali gestiti. Se si dispone di un portale gestito, andare al passaggio 4.

1. Configurare un [ambiente locale per](developer-portal-self-host.md#step-1-set-up-local-environment) la versione più recente del portale per sviluppatori.

1. Installare il **pacchetto npm** per aggiungere [Paperbit per Google Tag Manager](https://github.com/paperbits/paperbits-gtm):

    ```sh
    npm install @paperbits/gtm --save
    ```

1. Nel `startup.publish.ts` file nella cartella `src` importare e registrare il modulo GTM:

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. Estendere la configurazione del sito dal passaggio precedente con Google Tag Manager configurazione:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Automatizzare le distribuzioni del portale](automate-portal-deployments.md)
- [Ospitare in modo self-host il portale per sviluppatori](developer-portal-self-host.md)