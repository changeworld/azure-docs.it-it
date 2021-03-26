---
title: Come ritagliare i file video con servizi multimediali-.NET | Microsoft Docs
description: Il ritaglio è il processo di selezione di una finestra rettangolare all'interno del fotogramma video per codificare solo i pixel all'interno di quella finestra. Questo argomento illustra come ritagliare i file video con servizi multimediali usando .NET.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572375"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Come ritagliare i file video con servizi multimediali-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

È possibile usare servizi multimediali per ritagliare un video di input. Il ritaglio è il processo di selezione di una finestra rettangolare all'interno del fotogramma video per codificare solo i pixel all'interno di quella finestra. Il diagramma seguente illustra il processo.

## <a name="pre-processing-stage"></a>Fase di pre-elaborazione

Il ritaglio è una fase di pre-elaborazione, quindi i *parametri di ritaglio* nel set di impostazioni di codifica si applicano al video di *input* . La codifica è una fase successiva e le impostazioni di larghezza/altezza si applicano al video *pre-elaborato* e non al video originale. Quando si progetta il set di impostazioni, eseguire le operazioni seguenti:

1. Selezionare i parametri di ritaglio in base al video di input originale
1. Selezionare le impostazioni di codifica in base al video ritagliato.

> [!WARNING]
> Se le impostazioni di codifica non corrispondono al video ritagliato, l'output non sarà quello previsto.

Ad esempio, il video di input ha una risoluzione di 1920x1080 pixel (16:9 proporzioni), ma ha barre nere (caselle pilastro) a sinistra e a destra, in modo che solo una finestra 4:3 o 1440x1080 pixel contenga il video attivo. È possibile ritagliare le barre nere e codificare l'area 1440x1080.

## <a name="transform-code"></a>Trasforma codice

Il frammento di codice seguente illustra come scrivere una trasformazione in .NET per ritagliare i video.  Il codice presuppone che si disponga di un file locale da usare.

- Left è la posizione più a sinistra della ritaglio.
- Top è la posizione più in alto della raccolta.
- Width è la larghezza finale della ritaglio.
- Height è l'altezza finale della ritaglio.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
