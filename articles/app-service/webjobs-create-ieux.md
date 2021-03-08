---
title: Eseguire attività in background con processi Web
description: Informazioni su come usare i processi Web per eseguire attività in background nel servizio app Azure. È possibile scegliere tra un'ampia gamma di formati di script ed eseguirli con espressioni CRON.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0d49323e2bc3c0522b1fb9ad49ffcc14f476e2dc
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452799"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Eseguire attività in background con processi Web nel servizio app Azure

Il concetto di esecuzione di [attività in background](./webjobs-create-ieux-conceptual.md) in Azure è fornito con processi Web del servizio app Azure. Informazioni su come eseguire la distribuzione <abbr title="Programma o script nella stessa istanza di un'app Web, un'app per le API o un'app per dispositivi mobili.">Processi Web</abbr> usando il [portale di Azure](https://portal.azure.com) per caricare un eseguibile o uno script. 

Tre processi Web supportati includono:

* **Continuous**: viene avviato immediatamente, in genere in esecuzione in un ciclo infinito.
* **Pianificato**: avvia dal trigger pianificato
* **Manuale**: avvio da trigger manuale

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> Passaggi successivi

* [Altre informazioni sulle attività in background come processi Web](./webjobs-create-ieux-conceptual.md)
* [Visualizzare la cronologia dei log dei processi Web](./webjobs-create-ieux-view-log.md)

* Usare [Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) per semplificare molte attività di programmazione

* Informazioni su come [sviluppare e distribuire processi Web con Visual Studio](webjobs-dotnet-deploy-vs.md)
