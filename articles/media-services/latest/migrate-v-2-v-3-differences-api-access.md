---
title: Accesso all'API di servizi multimediali v2 vs V3
description: Questo articolo descrive le differenze di accesso alle API tra i servizi multimediali di Azure da V2 a V3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 831bc737fbd54685cdc8358642bc37e7b2df2c42
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953717"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Differenze di accesso alle API tra servizi multimediali di Azure V2 e l'API V3

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-2.svg)

Questo articolo descrive le differenze di accesso alle API tra i servizi multimediali di Azure da V2 a V3.

## <a name="api-access"></a>Accesso all'API

Tutti gli account di servizi multimediali avranno accesso all'API V3. Tuttavia, si consiglia vivamente di eseguire lo sviluppo della migrazione in un nuovo account prima di applicare il codice aggiornato a un account V2 esistente. Ciò è dovuto al fatto che le entità V3 non sono compatibili con la versione V2. Alcune entità v2 come asset sono compatibili con le versioni successive con v3.
È possibile continuare a usare gli account esistenti se non si combinano le API v2 e V3 e quindi si prova a tornare alla versione V2, ma questa operazione è sconsigliata.

L'accesso all'API v2 sarà disponibile fino a quando non verrà ritirato in 2024.

Durante la migrazione è possibile creare un account V3 che ha ancora accesso alla versione V2.  La creazione dell'account può essere eseguita con:

- L'API REST e la versione precedente
- Selezione della casella di controllo nel portale.

> [!div class="mx-imgBorder"]
> [![creazione dell'account nel portale ](./media/migration-guide/v-3-v-2-access-account-creation-small.png)](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Tutti i .NET, l'interfaccia della riga di comando e altri SDK saranno destinati alla versione più recente dell'API 2020-05-01, quindi è possibile trovare o configurare le versioni precedenti dell'API.

> [!NOTE]
> I nuovi account creati con l'API 2020-05-01 non possono usare le API v2.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
