---
title: Panoramica dell'inserimento-Azure Time Series Insights Gen2 | Microsoft Docs
description: Informazioni sull'inserimento di dati in Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: b688bbf454b3df9f6ae368c66a62657a5522d720
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505451"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Panoramica dell'inserimento dati in Azure Time Series Insights Gen2

L'ambiente Gen2 Azure Time Series Insights contiene un *motore* di inserimento per la raccolta, l'elaborazione e l'archiviazione dei dati delle serie temporali di streaming. Con l'arrivo dei dati nelle origini eventi, Azure Time Series Insights Gen2 utilizzerà e memorizzerà i dati quasi in tempo reale.

[![Panoramica dell'inserimento dati](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Argomenti relativi all'inserimento

Gli articoli seguenti illustrano in dettaglio l'elaborazione dei dati, incluse le procedure consigliate da seguire:

* Leggere le informazioni sulle [origini eventi](./concepts-streaming-ingestion-event-sources.md) e informazioni aggiuntive sulla selezione di un timestamp dell'origine evento.

* Esaminare i [tipi di dati](./concepts-supported-data-types.md) supportati

* Informazioni su come il motore di inserimento applicherà un set di [regole](./concepts-json-flattening-escaping-rules.md) alle proprietà JSON per creare le colonne dell'account di archiviazione.

* Esaminare le [limitazioni della velocità effettiva](./concepts-streaming-ingress-throughput-limits.md) dell'ambiente per pianificare le esigenze di scalabilità.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulle [origini eventi](./concepts-streaming-ingestion-event-sources.md) per l'ambiente Azure Time Series Insights Gen2, continuare.
