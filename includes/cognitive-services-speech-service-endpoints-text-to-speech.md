---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 6b16dea3c4f9241133b91b092c90c9056da57de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100514970"
---
### <a name="standard-and-neural-voices"></a>Voci standard e neurali

Usare questa tabella per determinare la disponibilità delle voci standard e neurali per area/endpoint:

| Region | Endpoint | Voci neurali | Voci standard |
|--------|----------|-----------------|---------------|
| Australia orientale | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Brasile meridionale | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| Canada centrale | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Stati Uniti centrali | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| Asia orientale | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| Stati Uniti orientali | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Stati Uniti orientali 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| Francia centrale | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| India centrale | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Giappone orientale | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| Giappone occidentale | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| Corea centrale | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| Stati Uniti centro-settentrionali | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| Europa settentrionale | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| Stati Uniti centro-meridionali | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Asia sud-orientale | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Regno Unito meridionale | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Europa occidentale | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Stati Uniti occidentali | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | No | Sì |
| Stati Uniti occidentali 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |

> [!TIP]
> Le [voci in anteprima](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) sono disponibili solo nelle tre aree seguenti: Stati Uniti orientali, Europa occidentale e Asia sudorientale.

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
