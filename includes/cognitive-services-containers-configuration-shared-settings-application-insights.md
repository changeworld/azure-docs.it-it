---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001176"
---
L'impostazione `ApplicationInsights` consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](/azure/application-insights). Application Insights offre funzionalità di monitoraggio avanzate del contenitore. È possibile monitorare con facilità la disponibilità, le prestazioni e l'utilizzo del contenitore. È anche possibile identificare e diagnosticare rapidamente gli errori nel contenitore.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `ApplicationInsights`.

|Necessario| Nome | Tipo di dati | Descrizione |
|--|------|-----------|-------------|
|No| `InstrumentationKey` | string | Chiave di strumentazione dell'istanza di Application Insights a cui vengono inviati i dati di telemetria per il contenitore. Per altre informazioni, vedere [Application Insights per ASP.NET Core](../articles/azure-monitor/app/asp-net-core.md). <br><br>Esempio:<br>`InstrumentationKey=123456789`|