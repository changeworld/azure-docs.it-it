---
title: Mapping delle aree di Insights di container
description: Descrive i mapping dell'area supportati tra il contenitore Insights, Log Analytics area di lavoro e le metriche personalizzate.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: f9e910b1352109608becb82609e85e26d27d2cd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728876"
---
# <a name="region-mappings-supported-by-container-insights"></a>Mapping dell'area supportati da informazioni dettagliate sul contenitore

 Quando si Abilita il contenitore Insights, sono supportate solo alcune aree per il collegamento di un'area di lavoro Log Analytics e un cluster AKS e la raccolta di metriche personalizzate inviate a monitoraggio di Azure.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapping supportati dall'area di lavoro Log Analytics

Le aree AKS supportate sono elencate in [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service). L'area di lavoro Log Analytics deve trovarsi nella stessa area, ad eccezione delle aree elencate nella tabella seguente. Vedere le [Note sulla versione di AKS](https://github.com/Azure/AKS/releases) per gli aggiornamenti.


|**Area del cluster AKS** | **Area dell'area di lavoro Log Analytics** |
|-----------------------|------------------------------------|
|**Africa** | |
|SouthAfricaNorth |Europa occidentale |
|SouthAfricaWest |Europa occidentale |
|**Australia** | |
|AustraliaCentral2 |AustraliaCentral |
|**Brasile** | |
|BrazilSouth | SouthCentralUS |
|**Canada** ||
|CanadaEast |CanadaCentral |
|**Europa** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**India** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Giappone** | |
|JapanWest |JapanEast |
|**Corea del Sud** | |
|KoreaSouth |KoreaCentral |
|**Stati Uniti** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> a causa dei vincoli di capacità, l'area non è disponibile quando si creano nuove risorse. Sono incluse un'area di lavoro Log Analytics. Tuttavia, le risorse collegate preesistenti nell'area dovrebbero continuare a funzionare.

## <a name="custom-metrics-supported-regions"></a>Aree supportate per le metriche personalizzate

Per la pubblicazione come metrica personalizzata solo nelle [aree di Azure](../essentials/metrics-custom-overview.md#supported-regions)seguenti sono supportate le metriche dei nodi e dei cluster di Azure Kubernetes Services (AKS).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a monitorare il cluster AKS, vedere [come abilitare il contenitore Insights](container-insights-onboard.md) per comprendere i requisiti e i metodi disponibili per abilitare il monitoraggio.  
