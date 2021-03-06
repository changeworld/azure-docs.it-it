---
title: Esempio di script CLI di Azure-creare una trasformazione
description: Le trasformazioni descrivono un semplice flusso di lavoro di attività per l'elaborazione dei file video o audio, detto spesso "recipe". Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come creare una trasformazione.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 75b45067be49475ecd2e07aed9c4479b147fdd29
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490268"
---
# <a name="create-a-transform"></a>Creare una trasformazione

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come creare una trasformazione. Le trasformazioni descrivono un semplice flusso di lavoro di attività per l'elaborazione dei file video o audio, detto spesso "recipe". È necessario verificare sempre se esiste già una trasformazione con il nome e il "recipe" desiderati. In caso affermativo, è consigliabile riutilizzarla.

## <a name="prerequisites"></a>Prerequisiti

[Creare un account di Servizi multimediali di Azure](./account-create-how-to.md).

## <a name="cli"></a>[CLI](#tab/cli/)

> [!NOTE]
> È possibile specificare solo un percorso di un file JSON predefinito del codificatore standard personalizzato per [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Vedere l'esempio di [codifica con una trasformazione personalizzata](transform-custom-preset-cli-how-to.md).
>
> Non è possibile passare un nome file quando si usa [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset).

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
