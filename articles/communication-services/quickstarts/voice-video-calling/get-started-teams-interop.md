---
title: 'Avvio rapido: Interoperabilità di Teams con Servizi di comunicazione di Azure'
titleSuffix: An Azure Communication Services quickstart
description: Questa guida di avvio rapido illustra come partecipare a una riunione di Teams con Azure Communication Calling SDK.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 0e75d2b480a9cbfd2977d9d449c1ea12bdfe4920
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095612"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Avvio rapido: Aggiungere l'app di chiamata a una riunione di Teams

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> Per abilitare o disabilitare l' [interoperabilità del tenant teams](../../concepts/teams-interop.md), completare [il modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Inizia a usare i servizi di comunicazione di Azure connettendo la tua soluzione di chiamata a Microsoft teams mediante JavaScript SDK.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

La funzionalità descritta in questo documento usa la versione disponibilità generale degli SDK di servizi di comunicazione. L'interoperabilità dei team richiede la versione beta degli SDK di servizi di comunicazione. Gli SDK beta possono essere esplorati nella [pagina note sulla versione](https://github.com/Azure/Communication/tree/master/releasenotes).

Quando si esegue il passaggio "Installa pacchetto" con gli SDK beta, modificare la versione del pacchetto nella versione beta più recente specificando la versione `@1.0.0-beta.10` (versione al momento della stesura di questo articolo) nel `communication-calling` nome del pacchetto. Non è necessario modificare il `communication-common` comando del pacchetto. Ad esempio:

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

- Consultare l'[esempio hero per le chiamate](../../samples/calling-hero-sample.md)
- Informazioni sulla [chiamata di funzionalità SDK](./calling-client-samples.md)
- Leggere altre informazioni sul [funzionamento delle chiamate](../../concepts/voice-video-calling/about-call-types.md)
