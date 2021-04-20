---
title: Panoramica del portale per sviluppatori in Azure API Management
titleSuffix: Azure API Management
description: Informazioni sul portale per sviluppatori in API Management un sito Web personalizzabile, in cui gli utenti delle API possono esplorare le API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: aaf02affce924232deb56bf7694771c838b0b323
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739603"
---
# <a name="overview-of-the-developer-portal"></a>Panoramica del portale per sviluppatori

Il portale per sviluppatori è un sito Web completamente personalizzabile generato automaticamente con la documentazione delle API. È qui che i consumer di API possono individuare le API, imparare a usarle, richiedere l'accesso e provarle.

Come illustrato in questo articolo, è possibile personalizzare ed estendere il portale per sviluppatori per scenari specifici. 

![Portale per sviluppatori di Gestione API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migrazione dal portale legacy

> [!IMPORTANT]
> Il portale per sviluppatori legacy è ora deprecato e riceverà solo gli aggiornamenti della sicurezza. È possibile continuare a usarlo come al solito fino al ritiro nell'ottobre 2023, quando verrà rimosso da tutti i servizi di Gestione API.

La migrazione al nuovo portale per sviluppatori è descritta [nell'articolo della documentazione dedicata](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Personalizzazione e applicazione di stili

Il portale per sviluppatori può essere personalizzato e con stile tramite l'editor visivo incorporato con trascinamento della selezione. Per [altri dettagli,](api-management-howto-developer-portal-customize.md) vedere questa esercitazione.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Estendibilità

Il API Management include un portale per sviluppatori gestito, sempre  aggiornato e integrato. È possibile accedervi dall'portale di Azure dati.

Se è necessario estenderlo con la logica personalizzata, che non è supportata, è possibile modificarne la codebase. La codebase del portale è [disponibile in un repository GitHub.](https://github.com/Azure/api-management-developer-portal) Ad esempio, è possibile implementare un nuovo widget, che si integra con un sistema di supporto di terze parti. Quando si implementano nuove funzionalità, è possibile scegliere una delle opzioni seguenti:

- **Ospitare autonomamente** il portale risultante all'esterno del API Management servizio. Quando si ospita autonomamente il portale, si diventa il responsabile della manutenzione e si è responsabili degli aggiornamenti. supporto di Azure'assistenza è limitata solo alla configurazione di base dei portali [self-hosted.](developer-portal-self-host.md)
- Aprire una richiesta pull per il team API Management per unire nuove funzionalità **alla** codebase del portale gestito.

Per informazioni dettagliate e istruzioni sull'estendibilità, vedere il [repository GitHub](https://github.com/Azure/api-management-developer-portal) e l'esercitazione per [implementare un widget.](developer-portal-implement-widgets.md) L'esercitazione [per personalizzare](api-management-howto-developer-portal-customize.md) il portale gestito illustra il pannello amministrativo  del portale, comune per le versioni gestite **e self-hosted.**


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul nuovo portale per sviluppatori:

- [Accedere al portale per sviluppatori gestito e personalizzarlo](api-management-howto-developer-portal-customize.md)
- [Configurare la versione self-hosted del portale](developer-portal-self-host.md)
- [Implementare un widget personalizzato](developer-portal-implement-widgets.md)

Esplorare altre risorse:

- [Repository GitHub con il codice sorgente](https://github.com/Azure/api-management-developer-portal)
- [Domande frequenti sul portale per sviluppatori](developer-portal-faq.md)
