---
title: Supporto per la lingua - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Il servizio Voce supporta numerose lingue per la conversione della voce in testo scritto e la sintesi vocale, insieme a funzionalità di traduzione vocale. Questo articolo fornisce un elenco completo delle lingue supportate per ogni funzionalità del servizio.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 3446de7592c10fd9c0905097a77314192688719d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554009"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Lingue e voci supportate per il servizio Voce

Il supporto per la lingua varia in base alla funzionalità del servizio Voce. Nelle tabelle seguenti viene riepilogato il supporto per la lingua per le offerte di servizio [Riconoscimento vocale](#speech-to-text), [Sintesi vocale](#text-to-speech) e [Traduzione vocale](#speech-translation).

## <a name="speech-to-text"></a>Riconoscimento vocale

Sia Microsoft Speech SDK che l'API REST supportano le lingue seguenti (impostazioni locali). 

Per migliorare l'accuratezza, è possibile personalizzare un subset di lingue caricando **Audio + Human-labeled Transcripts** (Trascrizioni audio o con etichette umane) oppure **Related Text: Sentences** (Testo correlato: Frasi). Il supporto per la personalizzazione del modello acustico con **trascrizioni audio e con etichetta umana** è limitato ai modelli di base specifici elencati di seguito. Altri modelli e linguaggi di base utilizzeranno il testo delle trascrizioni solo per eseguire il training di modelli personalizzati come nel **testo correlato: frasi**. Per altre informazioni sulla personalizzazione, vedere [Introduzione a Riconoscimento vocale personalizzato](./custom-speech-overview.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Linguaggio                 | Impostazioni locali (BCP-47) | Personalizzazioni  | [Rilevamento della lingua](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| Arabo (Bahrein), standard moderno  | `ar-BH` | Testo                                   | Sì                           | 
| Arabo (Egitto)                     | `ar-EG` | Testo                                   | Sì                          |
| Arabo (Iraq)                      | `ar-IQ` | Testo                                   |                           |
| Arabo (Israele)                    | `ar-IL` | Testo                                   |                           |
| Arabo (Giordania)                    | `ar-JO` | Testo                                   |                           |
| Arabo (Kuwait)                    | `ar-KW` | Testo                                   |                           |
| Arabo (Libano)                   | `ar-LB` | Testo                                   |                           |
| Arabo (Oman)                      | `ar-OM` | Testo                                   |                           |
| Arabo (Qatar)                     | `ar-QA` | Testo                                   |                           |
| Arabo (Arabia Saudita)              | `ar-SA` | Testo                                   | Sì                          |
| Arabo (stato della Palestina)        | `ar-PS` | Testo                                   |                           |
| Arabo (Siria)                     | `ar-SY` | Testo                                   | Sì                          |
| Arabo (Emirati Arabi Uniti)      | `ar-AE` | Testo                                   |                           |
| Bulgaro (Bulgaria)               | `bg-BG` | Testo                                   |                           |
| Catalano (Spagna)                    | `ca-ES` | Testo                                   | Sì                          |
| Cinese (cantonese, tradizionale)   | `zh-HK` | Audio (20201015)<br>Testo                 |        Sì                   |
| Cinese (mandarino, semplificato)     | `zh-CN` | Audio (20200910)<br>Testo                 |     Sì                      |
| Cinese (mandarino taiwanese)       | `zh-TW` | Audio (20190701, 20201015)<br>Testo                 |           Sì                |
| Croato (Croazia)                 | `hr-HR` | Testo                                   |                           |
| Ceco (Repubblica Ceca)             | `cs-CZ` | Testo                                   |                           |
| Danese (Danimarca)                   | `da-DK` | Testo                                   | Sì                          |
| Olandese (Paesi Bassi)                | `nl-NL` | Audio (20201015)<br>Testo                                   |    Sì                       |
| Inglese (Australia)                | `en-AU` | Audio (20201019)<br>Testo                 | Sì                          |
| Inglese (Canada)                   | `en-CA` | Audio (20201019)<br>Testo                 | Sì                          |
| Inglese (Ghana)                    | `en-GH` | Testo                                   |                           |
| Inglese (Hong Kong)                | `en-HK` | Testo                                   |                           |
| Inglese (India)                    | `en-IN` | Audio (20200923)<br>Testo                 | Sì                          |
| Inglese (Irlanda)                  | `en-IE` | Testo                                   |                           |
| Inglese (Kenya)                    | `en-KE` | Testo                                   |                           |
| Inglese (Nuova Zelanda)              | `en-NZ` | Audio (20201019)<br>Testo                 |  Sì                         |
| Inglese (Nigeria)                  | `en-NG` | Testo                                   |                           |
| Inglese (Filippine)              | `en-PH` | Testo                                   |                           |
| Inglese (Singapore)                | `en-SG` | Testo                                   |                           |
| Inglese (Sudafrica)             | `en-ZA` | Testo                                   |                           |
| Inglese (Tanzania)                 | `en-TZ` | Testo                                   |                           |
| Inglese (Regno Unito)           | `en-GB` | Audio (20201019)<br>Testo<br>Pronuncia| Sì                          |
| Inglese (Stati Uniti)            | `en-US` | Audio (20201019)<br>Testo<br>Pronuncia| Sì                          |
| Estone (Estonia)                  | `et-EE` | Testo                                   |                           |
| Filippino (Filippine)             | `fil-PH`| Testo                                   |                           |
| Finlandese (Finlandia)                  | `fi-FI` | Testo                                   |     Sì                      |
| Francese (Canada)                    | `fr-CA` | Audio (20201015)<br>Testo                 |     Sì                      |
| Francese (Francia)                    | `fr-FR` | Audio (20201015)<br>Testo<br>Pronuncia|      Sì                     |
| Francese (Svizzera)               | `fr-CH` | Testo                                   |                           |
| Tedesco (Austria)                   | `de-AT` | Testo                                   |                           |
| Tedesco (Germania)                   | `de-DE` | Audio (20190701, 20200619, 20201127)<br>Testo<br>Pronuncia|  Sì                         |
| Greco (Grecia)                     | `el-GR` | Testo                                   |                           |
| Gujarati (India)                  | `gu-IN` | Testo                                   |                           |
| Hindi (India)                      | `hi-IN` | Audio (20200701)<br>Testo                 |     Sì                      |
| Ungherese (Ungheria)                | `hu-HU` | Testo                                   |                           |
| Indonesiano (Indonesia)             | `id-ID` | Testo                                   |                           |
| Irlandese (Irlanda)                     | `ga-IE` | Testo                                   |                           |
| Italiano (Italia)                    | `it-IT` | Audio (20201016)<br>Testo<br>Pronuncia|      Sì                     |
| Giapponese (Giappone)                   | `ja-JP` | Testo                                   |      Sì                     |
| Coreano (Corea)                     | `ko-KR` | Audio (20201015)<br>Testo                 |      Sì                     |
| Lettone (Lettonia)                   | `lv-LV` | Testo                                   |                           |
| Lituano (Lituania)             | `lt-LT` | Testo                                   |                           |
| Malese (Malaysia)                   | `ms-MY` | Testo                                   |                           |
| Maltese (Malta)                    | `mt-MT` | Testo                                   |                           |
| Marathi (India)                    | `mr-IN` | Testo                                   |                           |
| Norvegese (Bokmål, Norvegia)         | `nb-NO` | Testo                                   |     Sì                      |
| Polacco (Polonia)                    | `pl-PL` | Testo                                   |       Sì                    |
| Portoghese (Brasile)                | `pt-BR` | Audio (20190620, 20201015)<br>Testo<br>Pronuncia|          Sì                 |
| Portoghese (Portogallo)              | `pt-PT` | Testo                                   |             Sì              |
| Romeno (Romania)                 | `ro-RO` | Testo                                   |                           |
| Russo (Russia)                   | `ru-RU` | Audio (20200907)<br>Testo                 |                Sì           |
| Slovacco (Slovacchia)                  | `sk-SK` | Testo                                   |                           |
| Sloveno (Slovenia)               | `sl-SI` | Testo                                   |                           |
| Spagnolo (Argentina)                | `es-AR` | Testo                                   |                           |
| Spagnolo (Bolivia)                  | `es-BO` | Testo                                   |                           |
| Spagnolo (Cile)                    | `es-CL` | Testo                                   |                           |
| Spagnolo (Colombia)                 | `es-CO` | Testo                                   |                           |
| Spagnolo (Costa Rica)               | `es-CR` | Testo                                   |                           |
| Spagnolo (Cuba)                     | `es-CU` | Testo                                   |                           |
| Spagnolo (Repubblica dominicana)       | `es-DO` | Testo                                   |                           |
| Spagnolo (Ecuador)                  | `es-EC` | Testo                                   |                           |
| Spagnolo (El Salvador)              | `es-SV` | Testo                                   |                           |
| Spagnolo (Guinea Equatoriale)        | `es-GQ` | Testo                                   |                           |
| Spagnolo (Guatemala)                | `es-GT` | Testo                                   |                           |
| Spagnolo (Honduras)                 | `es-HN` | Testo                                   |                           |
| Spagnolo (Messico)                   | `es-MX` | Audio (20200907)<br>Testo                 |    Sì                       |
| Spagnolo (Nicaragua)                | `es-NI` | Testo                                   |                           |
| Spagnolo (Panama)                   | `es-PA` | Testo                                   |                           |
| Spagnolo (Paraguay)                 | `es-PY` | Testo                                   |                           |
| Spagnolo (Perù)                     | `es-PE` | Testo                                   |                           |
| Spagnolo (Puerto Rico)              | `es-PR` | Testo                                   |                           |
| Spagnolo (Spagna)                    | `es-ES` | Audio (20201015)<br>Testo                 |  Sì                         |
| Spagnolo (Uruguay)                  | `es-UY` | Testo                                   |                           |
| Spagnolo (Stati Uniti)                      | `es-US` | Testo                                   |                           |
| Spagnolo (Venezuela)                | `es-VE` | Testo                                   |                           |
| Svedese (Svezia)                   | `sv-SE` | Testo                                   |   Sì                        |
| Tamil (India)                      | `ta-IN` | Testo                                   |                           |
| Telugu (India)                     | `te-IN` | Testo                                   |                           |
| Thai (Thailandia)                    | `th-TH` | Testo                                   |      Sì                     |
| Turco (Turchia)                   | `tr-TR` | Testo                                   |                           |
| Vietnamita (Vietnam)               | `vi-VN` | Testo                                   |                           |

## <a name="text-to-speech"></a>Sintesi vocale

Sia Microsoft Speech SDK che le API REST supportano le voci seguenti, ognuna delle quali supporta una lingua e un dialetto specifici, identificate dalle impostazioni locali. È anche possibile ottenere un elenco completo di lingue e voci supportate per ogni area/endpoint specifico tramite l'API [voices/list](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> I prezzi variano per voci standard, personalizzate e neurali. Per informazioni aggiuntive, visitare la pagina [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voci neurali

La sintesi vocale neurale è un nuovo tipo di sintesi vocale basata su reti neurali profonde. Quando si usa una voce neurale, è praticamente impossibile distinguere la sintesi vocale dalle registrazioni umane.

Le voci neurali possono essere usate per rendere più naturali e coinvolgenti le interazioni con chatbot e assistenti vocali, per convertire testo digitale, come gli e-book, in audiolibri e per migliorare i sistemi dei navigatori per le automobili. Con la prosodia naturale simile al linguaggio umano e l'articolazione chiara delle parole, le voci neurali riducono in modo significativo le difficoltà di ascolto durante l'interazione degli utenti con i sistemi di intelligenza artificiale.

> [!NOTE]
> Le voci neurali vengono create da campioni che usano una frequenza di campionamento di 24 kHz.
> Tutte le voci possono eseguire il campionamento o Downsample ad altre frequenze di campionamento durante la sintesi.


| Linguaggio | Locale | Sesso | Nome della voce | Supporto dello stile |
|---|---|---|---|---|
| Arabo (Egitto) | `ar-EG` | Female | `ar-EG-SalmaNeural` | Generale |
| Arabo (Egitto) | `ar-EG` | Male | `ar-EG-ShakirNeural` | Generale |
| Arabo (Arabia Saudita) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | Generale |
| Arabo (Arabia Saudita) | `ar-SA` | Male | `ar-SA-HamedNeural` | Generale |
| Bulgaro (Bulgaria) | `bg-BG` | Female | `bg-BG-KalinaNeural` | Generale |
| Bulgaro (Bulgaria) | `bg-BG` | Male | `bg-BG-BorislavNeural` | Generale |
| Catalano (Spagna) | `ca-ES` | Female | `ca-ES-AlbaNeural` | Generale |
| Catalano (Spagna) | `ca-ES` | Female | `ca-ES-JoanaNeural` | Generale |
| Catalano (Spagna) | `ca-ES` | Male | `ca-ES-EnricNeural` | Generale |
| Cinese (cantonese, tradizionale) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | Generale |
| Cinese (cantonese, tradizionale) | `zh-HK` | Female | `zh-HK-HiuMaanNeural` | Generale |
| Cinese (cantonese, tradizionale) | `zh-HK` | Male | `zh-HK-WanLungNeural` | Generale |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | Generale, più stili vocali disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | Voce figlio, ottimizzata per la narrazione di storie |
| Cinese (mandarino, semplificato) | `zh-CN` | Male | `zh-CN-YunyangNeural` | Ottimizzato per la lettura di notizie,<br /> più stili vocali disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Male | `zh-CN-YunyeNeural` | Ottimizzata per la narrazione di storie |
| Cinese (mandarino taiwanese) | `zh-TW` | Female | `zh-TW-HsiaoChenNeural` | Generale |
| Cinese (mandarino taiwanese) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | Generale |
| Cinese (mandarino taiwanese) | `zh-TW` | Male | `zh-TW-YunJheNeural` | Generale |
| Croato (Croazia) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` | Generale |
| Croato (Croazia) | `hr-HR` | Male | `hr-HR-SreckoNeural` | Generale |
| Ceco (Ceco) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` | Generale |
| Ceco (Ceco) | `cs-CZ` | Male | `cs-CZ-AntoninNeural` | Generale |
| Danese (Danimarca) | `da-DK` | Female | `da-DK-ChristelNeural` | Generale |
| Danese (Danimarca) | `da-DK` | Male | `da-DK-JeppeNeural` | Generale |
| Olandese (Belgio) | `nl-BE` | Female | `nl-BE-DenaNeural` <sup>Nuova</sup> | Generale | 
| Olandese (Belgio) | `nl-BE` | Male | `nl-BE-ArnaudNeural` <sup>Nuova</sup> | Generale | 
| Olandese (Paesi Bassi) | `nl-NL` | Female | `nl-NL-ColetteNeural` | Generale |
| Olandese (Paesi Bassi) | `nl-NL` | Female | `nl-NL-FennaNeural` | Generale |
| Olandese (Paesi Bassi) | `nl-NL` | Male | `nl-NL-MaartenNeural` | Generale |
| Inglese (Australia) | `en-AU` | Female | `en-AU-NatashaNeural` | Generale |
| Inglese (Australia) | `en-AU` | Male | `en-AU-WilliamNeural` | Generale |
| Inglese (Canada) | `en-CA` | Female | `en-CA-ClaraNeural` | Generale |
| Inglese (Canada) | `en-CA` | Male | `en-CA-LiamNeural` | Generale |
| Inglese (India) | `en-IN` | Female | `en-IN-NeerjaNeural` | Generale |
| Inglese (India) | `en-IN` | Male | `en-IN-PrabhatNeural` | Generale |
| Inglese (Irlanda) | `en-IE` | Female | `en-IE-EmilyNeural` | Generale |
| Inglese (Irlanda) | `en-IE` | Male | `en-IE-ConnorNeural` | Generale |
| Inglese (Filippine) | `en-PH` | Female | `en-PH-RosaNeural` <sup>Nuova</sup> | Generale | 
| Inglese (Filippine) | `en-PH` | Male | `en-PH-JamesNeural` <sup>Nuova</sup> | Generale | 
| Inglese (Regno Unito) | `en-GB` | Female | `en-GB-LibbyNeural` | Generale |
| Inglese (Regno Unito) | `en-GB` | Female | `en-GB-MiaNeural` | Generale |
| Inglese (Regno Unito) | `en-GB` | Male | `en-GB-RyanNeural` | Generale |
| Inglese (Stati Uniti) | `en-US` | Female | `en-US-AriaNeural` | Generale, più stili vocali disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Inglese (Stati Uniti) | `en-US` | Female | `en-US-JennyNeural` | Generale |
| Inglese (Stati Uniti) | `en-US` | Male | `en-US-GuyNeural` | Generale |
| Estone (Estonia) | `et-EE` | Female | `et-EE-AnuNeural` | Generale |
| Estone (Estonia) | `et-EE` | Male | `et-EE-KertNeural` | Generale |
| Finlandese (Finlandia) | `fi-FI` | Female | `fi-FI-NooraNeural` | Generale |
| Finlandese (Finlandia) | `fi-FI` | Female | `fi-FI-SelmaNeural` | Generale |
| Finlandese (Finlandia) | `fi-FI` | Male | `fi-FI-HarriNeural` | Generale |
| Francese (Belgio) | `fr-BE` | Female | `fr-BE-CharlineNeural` <sup>Nuova</sup> | Generale | 
| Francese (Belgio) | `fr-BE` | Male | `fr-BE-GerardNeural` <sup>Nuova</sup> | Generale | 
| Francese (Canada) | `fr-CA` | Female | `fr-CA-SylvieNeural` | Generale |
| Francese (Canada) | `fr-CA` | Male | `fr-CA-AntoineNeural` | Generale |
| Francese (Canada) | `fr-CA` | Male | `fr-CA-JeanNeural` | Generale |
| Francese (Francia) | `fr-FR` | Female | `fr-FR-DeniseNeural` | Generale |
| Francese (Francia) | `fr-FR` | Male | `fr-FR-HenriNeural` | Generale |
| Francese (Svizzera) | `fr-CH` | Female | `fr-CH-ArianeNeural` | Generale |
| Francese (Svizzera) | `fr-CH` | Male | `fr-CH-FabriceNeural` | Generale |
| Tedesco (Austria) | `de-AT` | Female | `de-AT-IngridNeural` | Generale |
| Tedesco (Austria) | `de-AT` | Male | `de-AT-JonasNeural` | Generale |
| Tedesco (Germania) | `de-DE` | Female | `de-DE-KatjaNeural` | Generale |
| Tedesco (Germania) | `de-DE` | Male | `de-DE-ConradNeural` | Generale |
| Tedesco (Svizzera) | `de-CH` | Female | `de-CH-LeniNeural` | Generale |
| Tedesco (Svizzera) | `de-CH` | Male | `de-CH-JanNeural` | Generale |
| Greco (Grecia) | `el-GR` | Female | `el-GR-AthinaNeural` | Generale |
| Greco (Grecia) | `el-GR` | Male | `el-GR-NestorasNeural` | Generale |
| Ebraico (Israele) | `he-IL` | Female | `he-IL-HilaNeural` | Generale |
| Ebraico (Israele) | `he-IL` | Male | `he-IL-AvriNeural` | Generale |
| Hindi (India) | `hi-IN` | Female | `hi-IN-SwaraNeural` | Generale |
| Hindi (India) | `hi-IN` | Male | `hi-IN-MadhurNeural` | Generale |
| Ungherese (Ungheria) | `hu-HU` | Female | `hu-HU-NoemiNeural` | Generale |
| Ungherese (Ungheria) | `hu-HU` | Male | `hu-HU-TamasNeural` | Generale |
| Indonesiano (Indonesia) | `id-ID` | Female | `id-ID-GadisNeural` | Generale |
| Indonesiano (Indonesia) | `id-ID` | Male | `id-ID-ArdiNeural` | Generale |
| Irlandese (Irlanda) | `ga-IE` | Female | `ga-IE-OrlaNeural` | Generale |
| Irlandese (Irlanda) | `ga-IE` | Male | `ga-IE-ColmNeural` | Generale |
| Italiano (Italia) | `it-IT` | Female | `it-IT-ElsaNeural` | Generale |
| Italiano (Italia) | `it-IT` | Female | `it-IT-IsabellaNeural` | Generale |
| Italiano (Italia) | `it-IT` | Male | `it-IT-DiegoNeural` | Generale |
| Giapponese (Giappone) | `ja-JP` | Female | `ja-JP-NanamiNeural` | Generale |
| Giapponese (Giappone) | `ja-JP` | Male | `ja-JP-KeitaNeural` | Generale |
| Coreano (Corea) | `ko-KR` | Female | `ko-KR-SunHiNeural` | Generale |
| Coreano (Corea) | `ko-KR` | Male | `ko-KR-InJoonNeural` | Generale |
| Lettone (Lettonia) | `lv-LV` | Female | `lv-LV-EveritaNeural` | Generale |
| Lettone (Lettonia) | `lv-LV` | Male | `lv-LV-NilsNeural` | Generale |
| Lituano (Lituania) | `lt-LT` | Female | `lt-LT-OnaNeural` | Generale |
| Lituano (Lituania) | `lt-LT` | Male | `lt-LT-LeonasNeural` | Generale |
| Malese (Malaysia) | `ms-MY` | Female | `ms-MY-YasminNeural` | Generale |
| Malese (Malaysia) | `ms-MY` | Male | `ms-MY-OsmanNeural` | Generale |
| Maltese (Malta) | `mt-MT` | Female | `mt-MT-GraceNeural` | Generale |
| Maltese (Malta) | `mt-MT` | Male | `mt-MT-JosephNeural` | Generale |
| Norvegese (Bokmål, Norvegia) | `nb-NO` | Female | `nb-NO-IselinNeural` | Generale |
| Norvegese (Bokmål, Norvegia) | `nb-NO` | Female | `nb-NO-PernilleNeural` | Generale |
| Norvegese (Bokmål, Norvegia) | `nb-NO` | Male | `nb-NO-FinnNeural` | Generale |
| Polacco (Polonia) | `pl-PL` | Female | `pl-PL-AgnieszkaNeural` | Generale |
| Polacco (Polonia) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | Generale |
| Polacco (Polonia) | `pl-PL` | Male | `pl-PL-MarekNeural` | Generale |
| Portoghese (Brasile) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | Generale, più stili vocali disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Portoghese (Brasile) | `pt-BR` | Male | `pt-BR-AntonioNeural` | Generale |
| Portoghese (Portogallo) | `pt-PT` | Female | `pt-PT-FernandaNeural` | Generale |
| Portoghese (Portogallo) | `pt-PT` | Female | `pt-PT-RaquelNeural` | Generale |
| Portoghese (Portogallo) | `pt-PT` | Male | `pt-PT-DuarteNeural` | Generale |
| Romeno (Romania) | `ro-RO` | Female | `ro-RO-AlinaNeural` | Generale |
| Romeno (Romania) | `ro-RO` | Male | `ro-RO-EmilNeural` | Generale |
| Russo (Russia) | `ru-RU` | Female | `ru-RU-DariyaNeural` | Generale |
| Russo (Russia) | `ru-RU` | Female | `ru-RU-SvetlanaNeural` | Generale |
| Russo (Russia) | `ru-RU` | Male | `ru-RU-DmitryNeural` | Generale |
| Slovacco (Slovacchia) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` | Generale |
| Slovacco (Slovacchia) | `sk-SK` | Male | `sk-SK-LukasNeural` | Generale |
| Sloveno (Slovenia) | `sl-SI` | Female | `sl-SI-PetraNeural` | Generale |
| Sloveno (Slovenia) | `sl-SI` | Male | `sl-SI-RokNeural` | Generale |
| Spagnolo (Messico) | `es-MX` | Female | `es-MX-DaliaNeural` | Generale |
| Spagnolo (Messico) | `es-MX` | Male | `es-MX-JorgeNeural` | Generale |
| Spagnolo (Spagna) | `es-ES` | Female | `es-ES-ElviraNeural` | Generale |
| Spagnolo (Spagna) | `es-ES` | Male | `es-ES-AlvaroNeural` | Generale |
| Svedese (Svezia) | `sv-SE` | Female | `sv-SE-HilleviNeural` | Generale |
| Svedese (Svezia) | `sv-SE` | Female | `sv-SE-SofieNeural` | Generale |
| Svedese (Svezia) | `sv-SE` | Male | `sv-SE-MattiasNeural` | Generale |
| Tamil (India) | `ta-IN` | Female | `ta-IN-PallaviNeural` | Generale |
| Tamil (India) | `ta-IN` | Male | `ta-IN-ValluvarNeural` | Generale |
| Telugu (India) | `te-IN` | Female | `te-IN-ShrutiNeural` | Generale |
| Telugu (India) | `te-IN` | Male | `te-IN-MohanNeural` | Generale |
| Thai (Thailandia) | `th-TH` | Female | `th-TH-AcharaNeural` | Generale |
| Thai (Thailandia) | `th-TH` | Female | `th-TH-PremwadeeNeural` | Generale |
| Thai (Thailandia) | `th-TH` | Male | `th-TH-NiwatNeural` | Generale |
| Turco (Turchia) | `tr-TR` | Female | `tr-TR-EmelNeural` | Generale |
| Turco (Turchia) | `tr-TR` | Male | `tr-TR-AhmetNeural` | Generale |
| Ucraino (Ucraina) | `uk-UA` | Female | `en-ZA-LeahNeural` <sup>Nuova</sup> | Generale | 
| Ucraino (Ucraina) | `uk-UA` | Male | `en-ZA-LukeNeural` <sup>Nuova</sup> | Generale | 
| Urdu (Pakistan) | `ur-PK` | Female | `uk-UA-PolinaNeural` <sup>Nuova</sup> | Generale | 
| Urdu (Pakistan) | `ur-PK` | Male | `uk-UA-OstapNeural` <sup>Nuova</sup> | Generale | 
| Vietnamita (Vietnam) | `vi-VN` | Female | `vi-VN-HoaiMyNeural` | Generale |
| Vietnamita (Vietnam) | `vi-VN` | Male | `vi-VN-NamMinhNeural` | Generale |
| Gallese (Regno Unito) | `cy-GB` | Female | `cy-GB-NiaNeural` <sup>Nuova</sup> | Generale | 
| Gallese (Regno Unito) | `cy-GB` | Male | `cy-GB-AledNeural` <sup>Nuova</sup> | Generale | 

#### <a name="neural-voices-in-preview"></a>Voci neurali in anteprima

Sotto le voci neurali sono disponibili in anteprima pubblica. 

| Linguaggio                         | Locale  | Sesso | Nome della voce                             | Supporto dello stile |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaohanNeural` | Generale, più stili disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaomoNeural` | Generale, più Riproduci ruoli e stili disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` | Voce Senior, più stili disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` | Generale, più Riproduci ruoli e stili disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Cinese (mandarino, semplificato) | `zh-CN` | Male   | `zh-CN-YunxiNeural` | Generale, più stili disponibili [con SSML](speech-synthesis-markup.md#adjust-speaking-styles) |

> [!IMPORTANT]
> Le voci nell'anteprima pubblica sono disponibili solo in 3 aree del servizio: Stati Uniti orientali, Europa occidentale e Asia sudorientale.

Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#neural-and-standard-voices).

Per informazioni su come configurare e modificare le voci neurali, ad esempio gli stili di pronuncia, vedere [linguaggio di markup sintesi vocale](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> La voce `en-US-JessaNeural` è stata modificata in `en-US-AriaNeural`. Se in precedenza si usava "Jessa", convertirla in "Aria".

> [!TIP]
> Nelle richieste di sintesi vocale è possibile continuare a usare il mapping del nome completo del servizio, ad esempio "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)".

### <a name="standard-voices"></a>Voci standard

Sono disponibili più di 75 voci standard in oltre 45 lingue e impostazioni locali, che consentono di convertire il testo in contenuto vocale sintetizzato. Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#neural-and-standard-voices).

> [!NOTE]
> Con due eccezioni, le voci standard vengono create da campioni che usano una frequenza di campionamento di 16 kHz.
> Anche **le voci en-US-AriaRUS** e **en-US-GuyRUS** vengono create da esempi che usano una frequenza di campionamento di 24 kHz.
> Tutte le voci possono eseguire il campionamento o Downsample ad altre frequenze di campionamento durante la sintesi.

| Linguaggio | Impostazioni locali (BCP-47) | Sesso | Nome della voce |
|--|--|--|--|
| Arabo (arabo) | `ar-EG` | Female | `ar-EG-Hoda`|
| Arabo (Arabia Saudita) | `ar-SA` | Male | `ar-SA-Naayf`|
| Bulgaro (Bulgaria) | `bg-BG` | Male | `bg-BG-Ivan`|
| Catalano (Spagna) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| Cinese (cantonese, tradizionale) | `zh-HK` | Male | `zh-HK-Danny`|
| Cinese (cantonese, tradizionale) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| Cinese (mandarino, semplificato) | `zh-CN` | Male | `zh-CN-Kangkang`|
| Cinese (mandarino, semplificato) | `zh-CN` | Female | `zh-CN-Yaoyao`|
| Cinese (mandarino taiwanese) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| Cinese (mandarino taiwanese) |  `zh-TW` | Female | `zh-TW-Yating`|
| Cinese (mandarino taiwanese) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| Croato (Croazia) | `hr-HR` | Male | `hr-HR-Matej`|
| Ceco (Repubblica Ceca) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| Danese (Danimarca) | `da-DK` | Female | `da-DK-HelleRUS`|
| Olandese (Paesi Bassi) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| Inglese (Australia) | `en-AU` | Female | `en-AU-Catherine`|
| Inglese (Australia) | `en-AU` | Female | `en-AU-HayleyRUS`|
| Inglese (Canada) | `en-CA` | Female | `en-CA-HeatherRUS`|
| Inglese (Canada) | `en-CA` | Female | `en-CA-Linda`|
| Inglese (India) | `en-IN` | Female | `en-IN-Heera`|
| Inglese (India) | `en-IN` | Female | `en-IN-PriyaRUS`|
| Inglese (India) | `en-IN` | Male | `en-IN-Ravi`|
| Inglese (Irlanda) | `en-IE` | Male | `en-IE-Sean`|
| Inglese (Regno Unito) | `en-GB` | Male | `en-GB-George`|
| Inglese (Regno Unito) | `en-GB` | Female | `en-GB-HazelRUS`|
| Inglese (Regno Unito) | `en-GB` | Female | `en-GB-Susan`|
| Inglese (Stati Uniti) | `en-US` | Male | `en-US-BenjaminRUS`|
| Inglese (Stati Uniti) | `en-US` | Male | `en-US-GuyRUS`|
| Inglese (Stati Uniti) | `en-US` | Female | `en-US-AriaRUS`|
| Inglese (Stati Uniti) | `en-US` | Female | `en-US-ZiraRUS`|
| Finlandese (Finlandia) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| Francese (Canada) | `fr-CA` | Female | `fr-CA-Caroline`|
| Francese (Canada) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| Francese (Francia) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| Francese (Francia) | `fr-FR` | Female | `fr-FR-Julie`|
| Francese (Francia) | `fr-FR` | Male | `fr-FR-Paul`|
| Francese (Svizzera) | `fr-CH` | Male | `fr-CH-Guillaume`|
| Tedesco (Austria) | `de-AT` | Male | `de-AT-Michael`|
| Tedesco (Germania) | `de-DE` | Female | `de-DE-HeddaRUS`|
| Tedesco (Germania) | `de-DE` | Male | `de-DE-Stefan`|
| Tedesco (Svizzera) | `de-CH` | Male | `de-CH-Karsten`|
| Greco (Grecia) | `el-GR` | Male | `el-GR-Stefanos`|
| Ebraico (Israele) | `he-IL` | Male | `he-IL-Asaf`|
| Hindi (India) | `hi-IN` | Male | `hi-IN-Hemant`|
| Hindi (India) | `hi-IN` | Female | `hi-IN-Kalpana`|
| Ungherese (Ungheria) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| Indonesiano (Indonesia) | `id-ID` | Male | `id-ID-Andika`|
| Italiano (Italia) | `it-IT` | Male | `it-IT-Cosimo`|
| Italiano (Italia) | `it-IT` | Female | `it-IT-LuciaRUS`|
| Giapponese (Giappone) | `ja-JP` | Female | `ja-JP-Ayumi`|
| Giapponese (Giappone) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| Giapponese (Giappone) | `ja-JP` | Male | `ja-JP-Ichiro`|
| Coreano (Corea) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| Malese (Malaysia) | `ms-MY` | Male | `ms-MY-Rizwan`|
| Norvegese (Bokmål, Norvegia) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| Polacco (Polonia) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| Portoghese (Brasile) | `pt-BR` | Male | `pt-BR-Daniel`|
| Portoghese (Brasile) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| Portoghese (Portogallo) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| Romeno (Romania) | `ro-RO` | Male | `ro-RO-Andrei`|
| Russo (Russia) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| Russo (Russia) | `ru-RU` | Female | `ru-RU-Irina`|
| Russo (Russia) | `ru-RU` | Male | `ru-RU-Pavel`|
| Slovacco (Slovacchia) | `sk-SK` | Male | `sk-SK-Filip`|
| Sloveno (Slovenia) | `sl-SI` | Male | `sl-SI-Lado`|
| Spagnolo (Messico) | `es-MX` | Female | `es-MX-HildaRUS`|
| Spagnolo (Messico) | `es-MX` | Male | `es-MX-Raul`|
| Spagnolo (Spagna) | `es-ES` | Female | `es-ES-HelenaRUS`|
| Spagnolo (Spagna) | `es-ES` | Female | `es-ES-Laura`|
| Spagnolo (Spagna) | `es-ES` | Male | `es-ES-Pablo`|
| Svedese (Svezia) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| Tamil (India) | `ta-IN` | Male | `ta-IN-Valluvar`|
| Telugu (India) | `te-IN` | Female | `te-IN-Chitra`|
| Thai (Thailandia) | `th-TH` | Male | `th-TH-Pattara`|
| Turco (Turchia) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| Vietnamita (Vietnam) | `vi-VN` | Male | `vi-VN-An` |

> [!IMPORTANT]
> La voce `en-US-Jessa` è stata modificata in `en-US-Aria`. Se in precedenza si usava "Jessa", convertirla in "Aria".

> [!TIP]
> Nelle richieste di sintesi vocale è possibile continuare a usare il mapping del nome completo del servizio, ad esempio "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)".

### <a name="customization"></a>Personalizzazione

La voce personalizzata è disponibile nel livello standard e neurale. Le lingue supportate sono diverse per questi due livelli. 

| Linguaggio | Locale | Standard | Neurale |
|--|--|--|--|
| Cinese (mandarino, semplificato) | `zh-CN` | Sì | Sì |
| Cinese (mandarino, semplificato), inglese bilingue | `zh-CN` bilingue | Sì | Sì |
| Inglese (Australia) | `en-AU` | No | Sì |
| Inglese (India) | `en-IN` | Sì | Sì |
| Inglese (Regno Unito) | `en-GB` | Sì | Sì |
| Inglese (Stati Uniti) | `en-US` | Sì | Sì |
| Francese (Canada) | `fr-CA` | No | Sì |
| Francese (Francia) | `fr-FR` | Sì | Sì |
| Tedesco (Germania) | `de-DE` | Sì | Sì |
| Italiano (Italia) | `it-IT` | Sì | Sì |
| Giapponese (Giappone) | `ja-JP` | No | Sì |
| Coreano (Corea) | `ko-KR` | No | Sì |
| Portoghese (Brasile) | `pt-BR` | Sì | Sì |
| Spagnolo (Messico) | `es-MX` | Sì | Sì |
| Spagnolo (Spagna) | `es-ES` | No | Sì |

Selezionare le impostazioni locali corrette che corrispondono ai dati di training disponibili per il training di un modello vocale personalizzato. Se, ad esempio, i dati della registrazione sono pronunciati in inglese con un accento britannico, selezionare `en-GB`.

> [!NOTE]
> Il training del modello bilingue nella voce personalizzata non è supportato, ad eccezione del modello bilingue inglese-cinese. Per eseguire il training di una voce cinese in grado di parlare anche in inglese, selezionare "Chinese-English bilingual". Chinese-English training del modello bilingue con il metodo standard è disponibile solo in Europa settentrionale e Stati Uniti centro-settentrionali. Il training personalizzato per la voce neurale è disponibile nelle Regno Unito meridionale e negli Stati Uniti orientali.

## <a name="speech-translation"></a>Traduzione vocale

L'API **Traduzione vocale** supporta lingue diverse per la traduzione vocale e con riconoscimento vocale. La lingua di origine deve essere sempre inclusa nella tabella delle lingue per il riconoscimento vocale. Le lingue di destinazione disponibili variano a seconda del fatto che siano parlate o scritte. È possibile tradurre la voce in ingresso in più di [60 lingue](https://www.microsoft.com/translator/business/languages/). Un subset di queste lingue è disponibile per la [sintesi vocale](language-support.md#text-languages).

### <a name="text-languages"></a>Lingue per il testo

| Lingua per il testo           | Codice lingua |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabo                  | `ar`          |
| Bengalese                  | `bn`          |
| Bosniaco (latino)         | `bs`          |
| Bulgaro               | `bg`          |
| Cantonese (tradizionale) | `yue`         |
| Catalano                 | `ca`          |
| Cinese semplificato      | `zh-Hans`     |
| Cinese tradizionale     | `zh-Hant`     |
| Croato                | `hr`          |
| Ceco                   | `cs`          |
| Danese                  | `da`          |
| Olandese                   | `nl`          |
| Inglese                 | `en`          |
| Estone                | `et`          |
| Figiano                  | `fj`          |
| Filippino                | `fil`         |
| Finlandese                 | `fi`          |
| Francese                  | `fr`          |
| Tedesco                  | `de`          |
| Greco                   | `el`          |
| Gujarati                | `gu`          |
| Creolo haitiano          | `ht`          |
| Ebraico                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Daw               | `mww`         |
| Ungherese               | `hu`          |
| Indonesiano              | `id`          |
| Irlandese                   | `ga`          |
| Italiano                 | `it`          |
| Giapponese                | `ja`          |
| Kannada                 | `kn`          |
| Kiswahili               | `sw`          |
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
| Coreano                  | `ko`          |
| Lettone                 | `lv`          |
| Lituano              | `lt`          |
| Malgascio                | `mg`          |
| Malese                   | `ms`          |
| Malayalam               | `ml`          |
| Maltese                 | `mt`          |
| Maori                   | `mi`          |
| Marathi                 | `mr`          |
| Norvegese               | `nb`          |
| Persiano                 | `fa`          |
| Polacco                  | `pl`          |
| Portoghese (Brasile)     | `pt-br`       |
| Portoghese (Portogallo)   | `pt-pt`       |
| Punjabi                 | `pa`          |
| Querétaro Otomi         | `otq`         |
| Rumeno                | `ro`          |
| Russo                 | `ru`          |
| Samoano                  | `sm`          |
| Serbo (alfabeto cirillico)      | `sr-Cyrl`     |
| Serbo (alfabeto latino)         | `sr-Latn`     |
| Slovacco                  | `sk`          |
| Sloveno               | `sl`          |
| Spagnolo                 | `es`          |
| Svedese                 | `sv`          |
| Tahitiano                | `ty`          |
| Tamil                   | `ta`          |
| Telugu                  | `te`          |
| Thai                    | `th`          |
| Tongano                  | `to`          |
| Turco                 | `tr`          |
| Ucraino               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamita              | `vi`          |
| Gallese                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="speaker-recognition"></a>Riconoscimento del parlante

Vedere la tabella seguente per le lingue supportate per le diverse API di Riconoscimento del parlante. Per altre informazioni su Riconoscimento del parlante, vedere la [panoramica](speaker-recognition-overview.md).

| Linguaggio | Impostazioni locali (BCP-47) | Verifica dipendente dal testo | Verifica indipendente dal testo | Identificazione indipendente dal testo |
|----|----|----|----|----|
|Inglese (Stati Uniti)  |  en-US  |  sì  |  sì  |  sì |
|Cinese (mandarino, semplificato) | zh-CN     |     n/d |     sì |     sì|
|Inglese (Australia)     | en-AU     | n/d     | sì     | sì|
|Inglese (Canada)     | en-CA     | n/d |     sì |     sì|
|Inglese (Regno Unito)     | en-GB     | n/d     | sì     | sì|
|Francese (Canada)     | fr-CA     | n/d     | sì |     sì|
|Francese (Francia)     | fr-FR     | n/d     | sì     | sì|
|Tedesco (Germania)     | de-DE     | n/d     | sì     | sì|
|Italiano | it-IT     |     n/d     | sì |     sì|
|Giapponese     | ja-JP | n/d     | sì     | sì|
|Portoghese (Brasile) | pt-BR |     n/d |     sì |     sì|
|Spagnolo (Messico)     | es-MX     | n/d |     sì |     sì|
|Spagnolo (Spagna)     | es-ES | n/d     | sì |     sì|

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account Azure gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
