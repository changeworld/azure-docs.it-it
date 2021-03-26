---
title: Imposta criteri dei cookie di lettura immersivi
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come impostare i criteri dei cookie per il lettore immersivo.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 8b0a1f4a948aa6fec565130acb5267476a1d4401
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048647"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Come impostare i criteri dei cookie per il lettore immersivo

Per impostazione predefinita, il lettore immersivo Disabilita l'utilizzo dei cookie. Se si Abilita l'utilizzo dei cookie, il lettore immersivo può utilizzare i cookie per gestire le preferenze dell'utente e tenere traccia dell'utilizzo delle funzionalità. Se si Abilita l'utilizzo dei cookie nel lettore immersivo, prendere in considerazione i requisiti dei criteri di conformità dei cookie UE. È responsabilità dell'applicazione host ottenere il consenso dell'utente necessario in base ai criteri di conformità dei cookie dell'Unione europea.

I criteri dei cookie possono essere impostati tramite le [Opzioni](../reference.md#options)del lettore immersive.

## <a name="enable-cookie-usage"></a>Abilita utilizzo cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Disabilitare l'utilizzo di cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare l'[esercitazione per Node.js](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Node.js
* Visualizzare l'[esercitazione per Android](../how-to-launch-immersive-reader.md) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Java o Kotlin per Android
* Visualizzare l'[esercitazione per iOS](../how-to-launch-immersive-reader.md) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Swift per iOS
* Visualizzare l'[esercitazione per Python](../how-to-launch-immersive-reader.md) per scoprire quali altre attività è possibile eseguire con Immersive Reader SDK usando Python
* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](../reference.md)