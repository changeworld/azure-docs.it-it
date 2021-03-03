---
title: Plug-in angolare per Application Insights JavaScript SDK
description: Come installare e usare il plug-in angolare per Application Insights JavaScript SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: d45d8bed328dc91dfeeabd6ce878074fa1218623
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737019"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Plug-in angolare per Application Insights JavaScript SDK

Il plug-in angolare per il Application Insights JavaScript SDK, Abilita:

- Rilevamento delle modifiche del router

> [!WARNING]
> Il plug-in angolare non è compatibile con ECMAScript 3 (ES3).

## <a name="getting-started"></a>Guida introduttiva

Installare il pacchetto NPM:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Uso di base

Configurare un'istanza di Application Insights nel componente entry nell'app:

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su JavaScript SDK, vedere la [documentazione di Application Insights JavaScript SDK](javascript.md) .
- [Plug-in angolare su GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
