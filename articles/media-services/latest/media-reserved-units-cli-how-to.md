---
title: INTERFACCIA della riga di comando MRU (media reserved Unit)
description: In questo argomento viene spiegato come usare l’interfaccia della riga di comando per ridimensionare l'elaborazione di servizi multimediali con Servizi multimediali di Azure.
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
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: a07c4a20b854e09daf3b320b8c99757ca99b2578
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213811"
---
# <a name="how-to-scale-media-reserved-units"></a>Come ridimensionare media reserved unit

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo illustra come ridimensionare media reserved Unit (MRSs) per una codifica più rapida.

## <a name="prerequisites"></a>Prerequisiti

[Creare un account di Servizi multimediali di Azure](./create-account-howto.md).

Informazioni sulle [unità riservate multimediali](concept-media-reserved-units.md).

## <a name="scale-media-reserved-units-with-cli"></a>Ridimensionare le Media Reserved Unit con l'interfaccia della riga di comando

Eseguire il comando `mru`.

Il comando [az ams account mru](/cli/azure/ams/account/mru) seguente imposta le Media Reserved Unit sull'account "amsaccount" usando i parametri **count** e **type**.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fatturazione

L'addebito è basato sul numero di minuti per cui viene effettuato il provisioning di media reserved Unit nell'account. Ciò si verifica indipendentemente dalla presenza di processi in esecuzione nell'account. Per una spiegazione dettagliata, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Passaggio successivo

[Analizzare i video](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Vedi anche

* [Quote e limiti](limits-quotas-constraints.md)
