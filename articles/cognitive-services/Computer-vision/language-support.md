---
title: Supporto per la lingua-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Questo articolo fornisce un elenco dei linguaggi naturali supportati dalle funzionalità di Visione artificiale; OCR, analisi delle immagini.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1abb857c9f03be502db02099383c6fe0b5110461
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736819"
---
# <a name="language-support-for-computer-vision"></a>Supporto delle lingue per Visione artificiale

Alcune funzionalità di Visione artificiale supportano più lingue; le funzionalità non indicate in questo documento supportano solo l'inglese.

## <a name="optical-character-recognition-ocr"></a>Riconoscimento ottico dei caratteri (OCR)

Le API OCR di Visione artificiale supportano diverse lingue. Non è necessario specificare un codice di lingua. Per ulteriori informazioni, vedere [riconoscimento ottico dei caratteri (OCR)](concept-recognizing-text.md) .

|Linguaggio| Codice lingua | API OCR | Leggi 3,0 e 3,1 | Leggere v 3.2-Preview. 1 |
|:-----|:----:|:-----:|:---:|:---:|
|Arabo | `ar`|✔ | | |
|Cinese (semplificato) | `zh-Hans`|✔ | |✔ |
|Cinese (tradizionale) | `zh-Hant`|✔ | | |
|Ceco | `cs` |✔ | | |
|Danese | `da` |✔ | | |
|Olandese | `nl` |✔ |✔ |✔ |
|Inglese | `en` |✔ |✔ |✔ |
|Finlandese | `fi` |✔ | | |
|Francese | `fr` |✔ |✔ |✔ |
|Tedesco | `de` |✔ |✔ |✔ |
|Greco | `el` |✔ | | |
|Ungherese | `hu` |✔ | | |
|Italiano | `it` |✔ |✔ |✔ |
|Giapponese | `ja` |✔ | |✔ |
|Coreano | `ko` |✔ | | |
|Norvegese | `nb` |✔ | | |
|Polacco | `pl` |✔ | | |
|Portoghese | `pt` |✔ |✔ |✔ |
|Romeno | `ro` |✔ | | |
|Russo | `ru` |✔ | | |
|Serbo (alfabeto cirillico) | `sr-Cyrl` |✔ | | |
|Serbo (alfabeto latino) | `sr-Latn` |✔ | | |
|Slovacco | `sk` |✔ | | |
|Spagnolo | `es` |✔ |✔ |✔ |
|Svedese | `sw` |✔ | | |
|Turco | `tr` |✔ | | |

## <a name="image-analysis"></a>Analisi di immagini

Alcune azioni dell'API [Analyze-image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) possono restituire i risultati in altri linguaggi, specificati con il `language` parametro di query. Altre azioni restituiscono i risultati in inglese indipendentemente dalla lingua specificata e altre generano un'eccezione per le lingue non supportate. Le azioni vengono specificate con `visualFeatures` i `details` parametri di query e. per un elenco di tutte le azioni che è possibile eseguire con l'analisi delle immagini, vedere la [Panoramica](overview.md) .

|Linguaggio | Codice lingua | Categorie | Tag | Descrizione | Adult (Adulti) | Marchi | Colore | Smile | ImageType | Oggetti | Celebrità | Punti di riferimento |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Cinese | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglese | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Giapponese | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portoghese | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spagnolo | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare le funzionalità di Visione artificiale citate in questa guida.

* [Analizzare un'immagine locale (REST)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-analyze.md)
* [Estrai testo stampato (REST)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-print-text.md)