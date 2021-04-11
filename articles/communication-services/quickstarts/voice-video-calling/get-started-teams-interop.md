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
ms.openlocfilehash: 39c35bc90986126d099e68023e15b97c58d0ac26
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108187"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Avvio rapido: Aggiungere l'app di chiamata a una riunione di Teams

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

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

- Consultare l'[esempio hero per le chiamate](../../samples/calling-hero-sample.md)
- Informazioni sulla [chiamata di funzionalità SDK](./calling-client-samples.md)
- Leggere altre informazioni sul [funzionamento delle chiamate](../../concepts/voice-video-calling/about-call-types.md)
