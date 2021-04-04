---
title: Cloud sovrani-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare i cloud sovrani
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98061753"
---
# <a name="speech-services-in-sovereign-clouds"></a>Servizi di riconoscimento vocale nei cloud sovrani

## <a name="azure-government-united-states"></a>Azure per enti pubblici (Stati Uniti)

Disponibile solo per enti pubblici degli Stati Uniti e per i rispettivi partner. Per altre informazioni su Azure per enti pubblici, vedere [qui](../../azure-government/documentation-government-welcome.md) e [qui.](../../azure-government/compare-azure-government-global-azure.md)

- **portale di Azure:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Regioni**
  - US Gov Arizona
  - US Gov Virginia
- **Piani tariffari disponibili:**
  - Gratuito (F0) e standard (S0). Per altri dettagli, vedere [qui](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
- **Funzionalità supportate:**
  - Riconoscimento vocale
    - Riconoscimento vocale personalizzato (modello acustico (AM) e adattamento del modello di linguaggio (LM)
      - [Speech Studio](https://speech.azure.us/)
  - Sintesi vocale
    - Voce standard
    - Voce neurale
  - Traduttore vocale
- **Funzionalità non supportate:**
  - Voce personalizzata
- **Lingue supportate:**
  - Vedere l'elenco delle lingue supportate [qui](language-support.md)

### <a name="endpoint-information"></a>Informazioni sugli endpoint

Questa sezione contiene informazioni sull'endpoint di servizi vocali per l'uso con l' [SDK](speech-sdk.md)di riconoscimento vocale, l' [API REST di sintesi](rest-speech-to-text.md)vocale e l' [API REST di sintesi vocale](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>API REST di servizi vocali

Gli endpoint dell'API REST di servizi vocali in Azure per enti pubblici hanno il formato seguente:

|  Tipo/operazione API REST | Formato dell'endpoint |
|--|--|
| Token di accesso | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [API REST per sintesi vocale v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [API REST per sintesi vocale per audio breve](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [API REST di sintesi vocale](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Sostituire `<REGION_IDENTIFIER>` con l'identificatore corrispondente all'area della sottoscrizione da questa tabella:

|                     | Identificatore area |
|--|--|
| **US Gov Arizona**  | `usgovarizona` |
| **US Gov Virginia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Speech SDK

Per l'SDK di riconoscimento vocale nei cloud sovrani è necessario usare l'istanza "da host" della `SpeechConfig` classe o l' `--host` opzione dell' [interfaccia](spx-overview.md)della riga di comando vocale. È anche possibile usare la creazione di istanze "da endpoint" e `--endpoint` Opzione dell'interfaccia della riga di comando vocale).

`SpeechConfig` è necessario creare un'istanza della classe come segue:
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
Usare l'interfaccia della riga di comando vocale come questa (nota l' `--host` opzione):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Sostituire `subscriptionKey` con la chiave della risorsa vocale. Sostituire `usGovHost` con l'espressione che corrisponde all'offerta di servizio richiesta e l'area della sottoscrizione da questa tabella:

|  Area/offerta di servizio | Espressione host |
|--|--|
| **US Gov Arizona** | |
| Riconoscimento vocale | `wss://usgovarizona.stt.speech.azure.us` |
| Sintesi vocale | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov Virginia** | |
| Riconoscimento vocale | `wss://usgovvirginia.stt.speech.azure.us` |
| Sintesi vocale | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure Cina

Disponibile per le organizzazioni con una presenza aziendale in Cina. Per altre informazioni su Azure Cina [, vedere qui.](/azure/china/overview-operations) 


- **portale di Azure:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Regioni**
  - Cina orientale 2
- **Piani tariffari disponibili:**
  - Gratuito (F0) e standard (S0). Per altri dettagli, vedere [qui](https://www.azure.cn/pricing/details/cognitive-services/index.html)
- **Funzionalità supportate:**
  - Riconoscimento vocale
    - Riconoscimento vocale personalizzato (modello acustico (AM) e adattamento del modello di linguaggio (LM)
      - [Speech Studio](https://speech.azure.cn/)
  - Sintesi vocale
    - Voce standard
    - Voce neurale
  - Traduttore vocale
- **Funzionalità non supportate:**
  - Voce personalizzata
- **Lingue supportate:**
  - Vedere l'elenco delle lingue supportate [qui](language-support.md)

### <a name="endpoint-information"></a>Informazioni sugli endpoint

Questa sezione contiene informazioni sull'endpoint di servizi vocali per l'uso con l' [SDK](speech-sdk.md)di riconoscimento vocale, l' [API REST di sintesi](rest-speech-to-text.md)vocale e l' [API REST di sintesi vocale](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>API REST di servizi vocali

Gli endpoint dell'API REST di servizi vocali in Azure Cina hanno il formato seguente:

|  Tipo/operazione API REST | Formato dell'endpoint |
|--|--|
| Token di accesso | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [API REST per sintesi vocale v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [API REST per sintesi vocale per audio breve](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [API REST di sintesi vocale](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Sostituire `<REGION_IDENTIFIER>` con l'identificatore corrispondente all'area della sottoscrizione da questa tabella:

|                     | Identificatore area |
|--|--|
| **Cina orientale 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>Speech SDK

Per l'SDK di riconoscimento vocale nei cloud sovrani è necessario usare l'istanza "da host" della `SpeechConfig` classe o l' `--host` opzione dell' [interfaccia](spx-overview.md)della riga di comando vocale. È anche possibile usare la creazione di istanze "da endpoint" e `--endpoint` Opzione dell'interfaccia della riga di comando vocale).

`SpeechConfig` è necessario creare un'istanza della classe come segue:
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
Usare l'interfaccia della riga di comando vocale come questa (nota l' `--host` opzione):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Sostituire `subscriptionKey` con la chiave della risorsa vocale. Sostituire `azCnHost` con l'espressione che corrisponde all'offerta di servizio richiesta e l'area della sottoscrizione da questa tabella:

|  Area/offerta di servizio | Espressione host |
|--|--|
| **Cina orientale 2** | |
| Riconoscimento vocale | `wss://chinaeast2.stt.speech.azure.cn` |
| Sintesi vocale | `https://chinaeast2.tts.speech.azure.cn` |