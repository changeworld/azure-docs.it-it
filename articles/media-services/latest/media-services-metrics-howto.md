---
title: Visualizzare le metriche con monitoraggio di Azure
description: Questo articolo illustra come monitorare le metriche con i grafici portale di Azure e l'interfaccia della riga di comando di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: ab5871749630b047f6498a2439f77693a999c798
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493922"
---
# <a name="monitor-media-services-metrics"></a>Monitorare le metriche di Servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle applicazioni. Per una descrizione dettagliata di questa funzionalità e per comprendere il motivo per cui è consigliabile usare i log di diagnostica e le metriche di servizi multimediali di Azure, vedere [monitorare le metriche di servizi multimediali e i log di diagnostica](monitoring/monitor-media-services-data-reference.md).

Monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query usando l'interfaccia della riga di comando di Azure. Questo articolo illustra come monitorare le metriche con i grafici portale di Azure e l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimediali di Azure.](./create-account-howto.md)
- Esaminare  [le metriche dei servizi multimediali e i log di diagnostica](monitoring/monitor-media-services-data-reference.md)

## <a name="view-metrics-in-azure-portal"></a>Visualizzare le metriche in portale di Azure

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Passare all'account di servizi multimediali di Azure e selezionare **metriche**.
1. Fare clic sulla casella **ambito** e selezionare la risorsa che si desidera monitorare.

    Nella parte destra della finestra **Seleziona un ambito** verrà visualizzato l'elenco delle risorse disponibili. In questo caso, viene visualizzato quanto segue:

    * &lt;Nome dell'account di servizi multimediali&gt;
    * &lt;Nome dell'endpoint di &gt; / &lt; streaming del nome dell'account di servizi multimediali&gt;
    * &lt;nome dell'account di archiviazione&gt;

    Filtrare quindi selezionare la risorsa e fare clic su **applica**. Per informazioni dettagliate sulle risorse e sulle metriche supportate, vedere [monitorare le metriche di servizi multimediali](monitoring/monitor-media-services-data-reference.md).

    > [!NOTE]
    > Per spostarsi tra le risorse che si desidera monitorare, fare di nuovo clic sulla casella di **origine** e ripetere questo passaggio.

1. Facoltativo: assegnare un nome al grafico (modificare il nome premendo la matita nella parte superiore).
1. Aggiungere la metrica che si desidera visualizzare.
1. È possibile aggiungere il grafico al dashboard.

## <a name="view-metrics-with-azure-cli"></a>Visualizzare le metriche con l'interfaccia della riga di comando di Azure

Per ottenere metriche "in uscita" con l'interfaccia della riga di comando di Azure, eseguire il `az monitor metrics` comando seguente:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Per ottenere altre metriche, sostituire "in uscita" con il nome della metrica a cui si è interessati.

## <a name="see-also"></a>Vedi anche

- [Metriche di Monitoraggio di Azure](../../azure-monitor/data-platform.md)
- [Creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Passaggi successivi

[Log di diagnostica](media-services-diagnostic-logs-howto.md)
