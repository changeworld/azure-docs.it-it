---
title: Che cos'è App Web statiche di Azure?
description: Caratteristiche e funzionalità principali di App Web statiche di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: e81f0a9e4fc50cf0d80f2905b9328af3c721865c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166403"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Che cos'è App Web statiche di Azure (anteprima)?

App Web statiche di Azure è un servizio che compila e distribuisce automaticamente app Web di stack completo in Azure da un repository di codice.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Diagramma di panoramica delle app Web statiche di Azure":::

Il flusso di lavoro di App Web statiche di Azure è personalizzato in base al flusso di lavoro giornaliero di uno sviluppatore. Le app vengono compilate e distribuite in base alle modifiche del codice.

Quando si crea una risorsa di app Web statiche di Azure, Azure interagisce direttamente con GitHub o Azure DevOps per monitorare un ramo di propria scelta. Ogni volta che si esegue il push di commit o si accettano richieste pull nel ramo controllato, viene eseguita automaticamente una compilazione e l'app e l'API vengono distribuite in Azure.

Le app Web statiche vengono in genere create usando librerie e Framework come angolari, React, svelte, VME o blazer, in cui il rendering lato server non è necessario. Queste app includono asset HTML, CSS, JavaScript e immagine che costituiscono l'applicazione. Con un server Web tradizionale, questi asset vengono gestiti da un unico server unitamente a tutti gli endpoint API necessari.

Con App Web statiche gli asset statici vengono separati da un server Web tradizionale e sono gestiti da punti distribuiti geograficamente in tutto il mondo. Grazie a questa distribuzione la gestione dei file risulta molto più rapida quanto più sono fisicamente vicini agli utenti finali. Per ospitare gli endpoint dell'API si usa inoltre un'[architettura serverless](../azure-functions/functions-overview.md), che consente di evitare l'uso di un server back-end completo.

## <a name="key-features"></a>Funzionalità principali

- **Hosting Web** per contenuti statici, come HTML, CSS, JavaScript e immagini.
- Supporto di **API integrato** fornito da Funzioni di Azure.
- L' **integrazione di GitHub e Azure DevOps di primo livello** in cui le modifiche del repository attivano compilazioni e distribuzioni.
- Contenuto statico **distribuito a livello globale**, per avvicinare il contenuto agli utenti.
- **Certificati SSL gratuiti**, che vengono rinnovati automaticamente.
- **Domini personalizzati** per aggiungere personalizzazioni all'app.
- **Modello di sicurezza automatico** con proxy inverso quando si chiamano le API, che non richiede alcuna configurazione CORS.
- **Integrazioni dei provider di autenticazione** con Azure Active Directory, Facebook, Google, GitHub e Twitter.
- Definizione e assegnazione di **ruoli di autorizzazione personalizzabili**.
- **Regole di routing back-end** per il controllo completo sul contenuto e sulle route gestite.
- **Generazione di versioni di staging** basate su richieste pull che consentono di creare versioni di anteprima del sito prima della pubblicazione.

## <a name="what-you-can-do-with-static-web-apps"></a>Operazioni possibili con App Web statiche

- **Creare applicazioni Web moderne** con framework e librerie JavaScript quali [Angular](getting-started.md?tabs=angular), [React](getting-started.md?tabs=react), [Svelte](/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md?tabs=react) o usando [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) per creare applicazioni WebAssembly con [Funzioni di Azure](apis.md) come back-end.
- **Pubblicare siti statici** con framework come [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Distribuire applicazioni Web** con framework come [Next.js](deploy-nextjs.md) e [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare la prima app statica](getting-started.md)