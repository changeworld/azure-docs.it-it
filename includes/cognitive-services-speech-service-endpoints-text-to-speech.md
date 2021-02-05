---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: df6f7311613057c445ae714b8b11240d0d5be14b
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569657"
---
### <a name="standard-and-neural-voices"></a>Voci standard e neurali

Usare questa tabella per determinare la disponibilità delle voci standard e neurali per area/endpoint:

| Region | Endpoint | Voci standard | Voci neurali |
|--------|----------|-----------------|---------------|
| Australia orientale | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Brasile meridionale | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
| Canada centrale | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Stati Uniti centrali | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
| Asia orientale | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
| Stati Uniti orientali | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Stati Uniti orientali 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
| Francia centrale | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
| India centrale | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Giappone orientale | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
| Giappone occidentale | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
| Corea centrale | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
| Stati Uniti centro-settentrionali | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
| Europa settentrionale | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
| Stati Uniti centro-meridionali | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Asia sud-orientale | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Regno Unito meridionale | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Europa occidentale | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | Sì |
| Stati Uniti occidentali | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Sì | No |
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
