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
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 5f3c6526139389da3bfdbc3c43cf8b6d2a1dbccf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567968"
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

## <a name="create-a-v3-account"></a>Creare un account V3

Durante la migrazione è possibile creare un account V3 che ha ancora accesso alla versione V2.  La creazione dell'account può essere eseguita con:

- L'API REST e la versione precedente
- Selezione della casella di controllo nel portale.

> [!div class="mx-imgBorder"]
> [![creazione dell'account nel portale ](./media/migration-guide/v-3-v-2-access-account-creation-small.png)](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Tutti i .NET, l'interfaccia della riga di comando e altri SDK saranno destinati alla versione più recente dell'API 2020-05-01, quindi è possibile trovare o configurare le versioni precedenti dell'API.

> [!NOTE]
> I nuovi account creati con l'API 2020-05-01 non possono usare le API v2.
