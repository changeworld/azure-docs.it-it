---
title: INTERFACCIA della riga di comando MRU (media reserved Unit)
description: In questo argomento viene spiegato come usare l’interfaccia della riga di comando per ridimensionare l'elaborazione di servizi multimediali con Servizi multimediali di Azure.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 06c0c6333b84697415ef598d4c5e853d5c006f08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870161"
---
# <a name="how-to-scale-media-reserved-units"></a>Come ridimensionare media reserved unit

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo illustra come ridimensionare media reserved Unit (MRSs) per una codifica più rapida.

> [!WARNING]
> Questo comando non funzionerà più per gli account di servizi multimediali creati con la versione 2020-05-01 dell'API o versioni successive. Per questi account, le media reserved unit non sono più necessarie perché il sistema verrà ridimensionato automaticamente in base al carico. Se non viene visualizzata l'opzione per gestire MRU nel portale di Azure, si usa un account creato con l'API 2020-05-01 o versione successiva.

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
