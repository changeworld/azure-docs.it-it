---
title: Gestire un ambiente di generazione 2-Azure Time Series | Microsoft Docs
description: Informazioni su come gestire un ambiente Azure Time Series Insights generazione 2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461896"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Gestisci Azure Time Series Insights Gen2

Dopo aver creato l'ambiente Azure Time Series Insights Gen2 usando [l'interfaccia](./how-to-create-environment-using-cli.md) della riga di comando di Azure o [l'portale di Azure](./how-to-create-environment-using-portal.md), è possibile modificare i criteri di accesso e altri attributi dell'ambiente in base alle esigenze aziendali.

## <a name="manage-the-environment"></a>Gestire l'ambiente

È possibile gestire l'ambiente Gen2 Azure Time Series Insights usando il [portale di Azure](https://portal.azure.com/). Esistono alcune differenze principali tra un ambiente Gen2 e gli ambienti Gen1 S1 o Gen1 S2 da tenere presente quando si gestisce l'ambiente tramite la portale di Azure:

* Il riquadro della **Panoramica** di portale di Azure Gen2 presenta le modifiche seguenti:

  * La capacità viene rimossa perché non è applicabile agli ambienti Gen2.
  * Viene aggiunta la proprietà **ID della serie temporale** . che determina come vengono partizionati i dati.
  * I set di dati di riferimento vengono rimossi.
  * L'URL visualizzato indica il [Azure Time Series Insights Explorer](./concepts-ux-panels.md).
  * Viene elencato il nome dell'account di archiviazione di Azure.

* Il riquadro di **configurazione** del portale di Azure viene rimosso perché le unità di scala non si applicano agli ambienti Azure Time Series Insights Gen2. Tuttavia, è possibile usare la **configurazione di archiviazione** per configurare l'archivio warm appena introdotto.

* Il riquadro **dei dati di riferimento** del portale di Azure è stato rimosso in Azure Time Series Insights Gen2 perché il concetto di dati di riferimento è stato sostituito dal [modello Time Series (TSM)](./concepts-model-overview.md).

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure Time Series Insights ambiente Gen2 nell'portale di Azure":::

## <a name="next-steps"></a>Passaggi successivi

* Esaminare l'elenco delle [procedure consigliate](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) per l'inserimento di flussi
* Informazioni su come [diagnosticare e risolvere i problemi](./how-to-diagnose-troubleshoot.md)
