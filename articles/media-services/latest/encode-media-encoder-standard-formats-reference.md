---
title: Formati e codec standard del codificatore-Azure
description: Questo articolo contiene un elenco dei formati di file di importazione ed esportazione più comuni che è possibile usare con StandardEncoderPreset.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: 2fe8f5d0acb2ab7e2f1c64dde1bedb19b96111e9
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492291"
---
# <a name="standard-encoder-formats-and-codecs"></a>Formati e codec standard del codificatore

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo contiene un elenco dei formati di file di importazione ed esportazione più comuni che è possibile usare con [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Per informazioni su come creare set di impostazioni personalizzati usando **StandardEncoderPreset**, vedere [Creare una trasformazione con un set di impostazioni personalizzato](transform-custom-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Contenitore di input/formati di file

| Formato di file (estensioni di file) | Supportato |
| --- | --- |
| FLV (con codec H. 264 e AAC) (.flv) |Sì |
| MXF    (.mxf) |Sì |
| GXF    (.gxf) |Sì |
| MPEG2 PS, MPEG2-TS, 3GP (TS, PS, 3GP, .3gpp, mpg) |Sì |
| Windows Media Video (WMV) (.wmv) |Sì |
| AVI (non compresso 8 bit/10 bit) (.avi) |Sì |
| MP4 (MP4, M4A,. m4v) / ISMV (ISMA, con estensione .ismv) |Sì |
| [Microsoft Digital Video Recording (DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Sì |
| Matroska/WebM (.mkv) |Sì |
| WAVE/WAV (.wav) |Sì |
| QuickTime (.mov) |Sì |

### <a name="audio-formats-in-input-containers"></a>Formati audio nei contenitori di input

Il codificatore standard supporta il trasporto dei seguenti formati audio nei contenitori di input:

* file MXF, GXF e QuickTime che dispongono di tracce audio con esempi di stereo interleaved o 5.1

oppure

* File MXF, GXF e QuickTime in cui l'audio viene trasportato in tracce PCM separate, ma il mapping del canale (in stereo o 5.1) può essere dedotto dai metadati del file

## <a name="input-video-codecs"></a>Codec video di input
| Codec video di input | Supportato |
| --- | --- |
| AVC 8 bit/10 bit, fino a 4:2:2, incluso AVCIntra |4:2:0 e 4:2:2 a 8 bit |
| Avid DNxHD (in MXF) |Sì |
| DVCPro/DVCProHD (in MXF) |Sì |
| Video digitale (DV) (in file AVI) |Sì |
| JPEG 2000 |Sì |
| MPEG-2 (fino a 422 Profile e High Level; incluse varianti quali XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Fino a 422 Profile |
| MPEG-1 |Sì |
| FORMATO VC-1/WMV9 |Sì |
| Canopus HQ/HQX |No |
| MPEG-4 parte 2 |Sì |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sì |
| YUV420 non compressi o mezzanine |Sì |
| Apple ProRes 422 |Sì |
| Apple ProRes 422 LT |Sì |
| Apple ProRes 422 HQ |Sì |
| Apple ProRes Proxy |Sì |
| Apple ProRes 4444 |Sì |
| Apple ProRes 4444 XQ |Sì |
| HEVC/H.265| Main Profile|

## <a name="input-audio-codecs"></a>Codec audio di input
| Codec audio di input | Supportato |
| --- | --- |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; fino a 5.1) |Sì |
| MPEG Layer 2 |Sì |
| MP3 (MPEG-1 Audio Layer 3) |Sì |
| Windows Media Audio |Sì |
| WAV/PCM |Sì |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sì |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Sì |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sì |
| AMR (velocità multipla adattiva) |Sì |
| AES (SMPTE 331M e 302M, AES3-2003) |No |
| Dolby® E |No |
| Dolby® Digital (AC3) |No |
| Dolby® Digital Plus (E-AC3) |No |

## <a name="output-formats-and-codecs"></a>Formati e codec di output
La tabella seguente include l'elenco dei codec e dei formati di file supportati per l'esportazione.

| Formato file | Codec video | Codec audio |
| --- | --- | --- |
| MP4 <br/><br/> (multi-bitrate MP4 container inclusi) |H. 264 (High, Main e Baseline Profile), HEVC (H. 265) a 8 bit |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (High, Main e Baseline Profile) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Passaggi successivi

[Creare una trasformazione con un set di impostazioni personalizzato](transform-custom-presets-how-to.md)
