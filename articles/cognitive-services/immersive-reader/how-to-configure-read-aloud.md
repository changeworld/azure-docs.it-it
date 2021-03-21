---
title: Configurare la lettura ad alta voce
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come configurare le varie opzioni per la lettura a voce alta.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 8b45fe07b4bd42059199197bc5b99f20f6b2a8cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608717"
---
# <a name="how-to-configure-read-aloud"></a>Come configurare la lettura a voce alta

Questo articolo illustra come configurare le diverse opzioni per la lettura a voce alta nel lettore immersivo.

## <a name="automatically-start-read-aloud"></a>Avvia automaticamente lettura ad alta voce

Il `options` parametro contiene tutti i flag che possono essere usati per configurare la lettura a voce alta. Impostare `autoplay` su `true` per abilitare l'avvio automatico della lettura ad alta voce dopo l'avvio del lettore immersivo.

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> A causa delle limitazioni del browser, la riproduzione automatica non è supportata in Safari.

## <a name="configure-the-voice"></a>Configurare la voce

Impostare `voice` su `male` o su `female` . Non tutti i linguaggi supportano entrambe le voci. Per ulteriori informazioni, vedere la pagina relativa al supporto per le [lingue](./language-support.md) .

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>Configurare la velocità di riproduzione

Imposta `speed` su un numero compreso tra `0.5` (50%) e `2.5` (250%) Inclusive. I valori non compresi in questo intervallo vengono fissati a 0,5 o 2,5.

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [informazioni di riferimento su Immersive Reader SDK](./reference.md)