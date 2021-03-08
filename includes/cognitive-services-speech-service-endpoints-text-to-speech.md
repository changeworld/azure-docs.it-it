---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 1eefb7097f672552956e90a19e0a7b411cae8a24
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455688"
---
### <a name="standard-and-neural-voices"></a>Voci standard e neurali

Usare questa tabella per determinare la **disponibilità delle voci standard** per area/endpoint:

| Region | Endpoint |
|--------|----------|
| Australia orientale | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Brasile meridionale | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Canada centrale | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Stati Uniti centrali | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Asia orientale | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Stati Uniti orientali | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Stati Uniti orientali 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| Francia centrale | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| India centrale | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Giappone orientale | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Giappone occidentale | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| Corea centrale | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Stati Uniti centro-settentrionali | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa settentrionale | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| Stati Uniti centro-meridionali | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Asia sud-orientale | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Regno Unito meridionale | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa occidentale | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| Stati Uniti occidentali | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Stati Uniti occidentali 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

> [!TIP]
> Le [voci in anteprima](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) sono disponibili solo nelle tre aree seguenti: Stati Uniti orientali, Europa occidentale e Asia sudorientale.

Usare questa tabella per determinare la **disponibilità delle voci neurali** per area/endpoint:

| Region | Endpoint |
|--------|----------|
| Australia orientale | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | 
| Canada centrale | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Stati Uniti orientali | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| India centrale | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Stati Uniti centro-meridionali | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Asia sud-orientale | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Regno Unito meridionale | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa occidentale | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| Stati Uniti occidentali 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

### <a name="custom-voices"></a>Voci personalizzate

Se è stato creato un tipo di carattere vocale personalizzato, usare l'endpoint creato. È anche possibile usare gli endpoint elencati di seguito, sostituendo `{deploymentId}` con l'ID distribuzione per il modello vocale.

| Region | Endpoint |
|--------|----------|
| Australia orientale | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brasile meridionale | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Canada centrale | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Stati Uniti centrali | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asia orientale | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Stati Uniti orientali | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Stati Uniti orientali 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Francia centrale | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| India centrale | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Giappone orientale | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Giappone occidentale | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Corea centrale | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Stati Uniti centro-settentrionali | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa settentrionale | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Stati Uniti centro-meridionali | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asia sud-orientale | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Regno Unito meridionale | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa occidentale | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Stati Uniti occidentali | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Stati Uniti occidentali 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>Voce neurale personalizzata

La tabella seguente illustra il supporto regionale per le funzionalità neurali personalizzate.

| Funzionalità | Aree supportate |
|---|---|
| Hosting del modello vocale | Stati Uniti orientali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Europa occidentale, Australia orientale |
| Caratteri in tempo reale | Stati Uniti orientali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Europa occidentale, Australia orientale |
| Caratteri audio lunghi | Stati Uniti orientali, Europa occidentale, Regno Unito meridionale, Asia sudorientale, India centrale |
| Training neurale personalizzato | Stati Uniti orientali, Regno Unito meridionale |

### <a name="long-audio-api"></a>API Long audio

L'API Long audio è disponibile in più aree con endpoint univoci.

| Region | Endpoint |
|--------|----------|
| Stati Uniti orientali | `https://eastus.customvoice.api.speech.microsoft.com` |
| India centrale | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Asia sud-orientale | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Regno Unito meridionale | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa occidentale | `https://westeurope.customvoice.api.speech.microsoft.com` |
