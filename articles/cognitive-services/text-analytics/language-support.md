---
title: Supporto per la lingua - API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Ecco un elenco di linguaggi naturali supportati dall'API Analisi del testo. Questo articolo illustra i linguaggi supportati per ogni operazione.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
ms.openlocfilehash: c0d91f803822e018f4363bb78d9138e2efe16f8a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531430"
---
# <a name="text-analytics-api-v3-language-support"></a>Analisi del testo della lingua dell'API v3 

#### <a name="sentiment-analysis"></a>[Analisi del sentiment](#tab/sentiment-analysis)

| Linguaggio              | Codice lingua | Supporto v3 | Versione del modello v3 iniziale: |              Note |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Cinese semplificato    |   `zh-hans`   |     ✓      |         2019-10-01         | Accettato anche `zh` |
| Cinese tradizionale   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Olandese                 |     `nl`      |     ✓      |         2019-10-01        |                    |
| Inglese               |     `en`      |     ✓      |         2019-10-01         |                    |
| Francese                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Tedesco                |     `de`      |     ✓      |         2019-10-01         |                    |
| Hindi                 |    `hi`       |     ✓      |         2020-04-01         |                    |
| Italiano               |     `it`      |     ✓      |         2019-10-01         |                    |
| Giapponese              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Coreano                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Norvegese (Bokmål)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Portoghese (Brasile)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| Portoghese (Portogallo) |    `pt-PT`    |     ✓      |         2019-10-01         | Accettato anche `pt` |
| Spagnolo               |     `es`      |     ✓      |         2019-10-01         |                    |
| Turco               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Opinion mining (solo v3.1-preview)

| Linguaggio              | Codice lingua | A partire dalla versione del modello v3: |              Note |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Inglese               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Riconoscimento entità denominata (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Vengono restituite solo le entità "Person", "Location" e "Organization" per le lingue contrassegnate con *.

| Linguaggio               | Codice lingua | Supporto v3 | A partire dalla versione del modello v3: |       Note        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arabo                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Cinese semplificato     |   `zh-hans`   |     ✓      |               2021-01-15        | Accettato anche `zh` |
| Cinese tradizionale   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Ceco                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Danese                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Olandese                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| Inglese                |     `en`      |     ✓      |               2019-10-01        |                    |
| Finlandese               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Francese                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Tedesco                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Ebraico                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Ungherese             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Italiano               |     `it`      |     ✓       |               2021-01-15        |                    |
| Giapponese              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Coreano                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Norvegese (Bokmål)   |     `no`      |     ✓*      |               2019-10-01        | Accettato anche `nb` |
| Polacco                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portoghese (Brasile)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portoghese (Portogallo) |    `pt-PT`    |     ✓       |               2021-01-15        | Accettato anche `pt` |
| Russo              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Spagnolo               |     `es`      |     ✓       |               2020-04-01        |                    |
| Svedese               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Turco               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Estrazione delle frasi chiave](#tab/key-phrase-extraction)

| Linguaggio              | Codice lingua |  Supporto v3 | Disponibile a partire dalla versione del modello v3: |       Note        |
|:----------------------|:-------------:|:----------:|:-----------------------------------------:|:------------------:|
| Danese                |     `da`      |     ✓     |                2019-10-01                 |                    |
| Olandese                 |     `nl`      |     ✓      |                2019-10-01                 |                    |
| Inglese               |     `en`      |     ✓      |                2019-10-01                 |                    |
| Finlandese               |     `fi`      |     ✓      |                2019-10-01                 |                    |
| Francese                |     `fr`      |     ✓      |                2019-10-01                 |                    |
| Tedesco                |     `de`      |     ✓      |                2019-10-01                 |                    |
| Italiano               |     `it`      |     ✓      |                2019-10-01                 |                    |
| Giapponese              |     `ja`      |     ✓      |                2019-10-01                 |                    |
| Coreano                |     `ko`      |     ✓      |                2019-10-01                 |                    |
| Norvegese (Bokmål)   |     `no`      |     ✓      |                2020-07-01                 | Accettato anche `nb` |
| Polacco                |     `pl`      |    ✓      |                2019-10-01                 |                    |
| Portoghese (Brasile)   |    `pt-BR`    |     ✓      |                2019-10-01                 |                    |
| Portoghese (Portogallo) |    `pt-PT`    |    ✓      |                2019-10-01                 | Accettato anche `pt` |
| Russo               |     `ru`      |     ✓      |                2019-10-01                 |                    |
| Spagnolo               |     `es`      |     ✓      |                2019-10-01                 |                    |
| Svedese               |     `sv`      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Collegamento di entità](#tab/entity-linking)

| Linguaggio | Codice lingua |  Supporto v3 | Disponibile a partire dalla versione del modello v3: | Note |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Inglese  |     `en`      |     ✓      |                2019-10-01                 |       |
| Spagnolo  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Informazioni personali](#tab/pii)

| Linguaggio               | Codice lingua | Supporto v3 | A partire dalla versione del modello v3: |       Note        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Cinese semplificato     |   `zh-hans`   |     ✓      |               2021-01-15        | Accettato anche `zh` |
| Inglese                |     `en`      |     ✓      |               2020-07-01        |                    |
| Francese                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Tedesco                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Italiano               |     `it`      |     ✓       |               2021-01-15        |                    |
| Giapponese              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Coreano                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Portoghese (Brasile)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portoghese (Portogallo) |    `pt-PT`    |     ✓       |               2021-01-15        | Accettato anche `pt` |
| Spagnolo               |     `es`      |     ✓       |               2020-04-01        |                    |

#### <a name="language-detection"></a>[Rilevamento lingua](#tab/language-detection)

L Analisi del testo API può rilevare un'ampia gamma di lingue, varianti, dialetti e alcune lingue regionali/culturali e restituire le lingue rilevate con il nome e il codice. Analisi del testo Rilevamento lingua i parametri del codice del linguaggio sono conformi allo standard [BCP-47,](https://tools.ietf.org/html/bcp47) la maggior parte dei quali è conforme agli identificatori [ISO-639-1.](https://www.iso.org/iso-639-language-codes.html) 

Se si ha contenuto espresso in un lingua usata con minore frequenza, si può provare Rilevamento lingua per vedere se viene restituito un codice. La risposta per le lingue che non è possibile rilevare è `unknown`.

| Linguaggio | Codice lingua | Supporto v3 | Disponibile a partire dalla versione del modello v3: |
|:-|:-:|:-:|:-:|
|Afrikaans|`af`|✓|    |
|Albanese|`sq`|✓|    |
|Amharico|`am`|✓|2021-01-05|
|Arabo|`ar`|✓|    |
|Armeno|`hy`|✓|    |
|Assamese|`as`|✓|2021-01-05|
|Azerbaigiano|`az`|✓|2021-01-05|
|Basco|`eu`|✓|    |
|Bielorusso|`be`|✓|    |
|Bengalese|`bn`|✓|    |
|Bosniaco|`bs`|✓|2020-09-01|
|Bulgaro|`bg`|✓|    |
|Birmano|`my`|✓|    |
|Catalano|`ca`|✓|    |
|Central Khmer|`km`|✓|    |
|Cinese|`zh`|✓|    |
|Cinese semplificato|`zh_chs`|✓|    |
|Cinese tradizionale|`zh_cht`|✓|    |
|Corso|`co`|✓|2021-01-05|
|Croato|`hr`|✓|    |
|Ceco|`cs`|✓|    |
|Danese|`da`|✓|    |
|Dari|`prs`|✓|2020-09-01|
|Divehi|`dv`|✓|    |
|Olandese|`nl`|✓|    |
|Inglese|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Estone|`et`|✓|    |
|Figiano|`fj`|✓|2020-09-01|
|Finlandese|`fi`|✓|    |
|Francese|`fr`|✓|    |
|Galiziano|`gl`|✓|    |
|Georgiano|`ka`|✓|    |
|Tedesco|`de`|✓|    |
|Greco|`el`|✓|    |
|Gujarati|`gu`|✓|    |
|Haitiano|`ht`|✓|    |
|Hausa|`ha`|✓|2021-01-05|
|Ebraico|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong Daw|`mww`|✓|2020-09-01|
|Ungherese|`hu`|✓|    |
|Islandese|`is`|✓|    |
|Igbo|`ig`|✓|2021-01-05|
|Indonesiano|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Irlandese|`ga`|✓|    |
|Italiano|`it`|✓|    |
|Giapponese|`ja`|✓|    |
|Giavanese|`jv`|✓|2021-01-05|
|Kannada|`kn`|✓|    |
|Kazako|`kk`|✓|2020-09-01|
|Kinyarwanda|`rw`|✓|2021-01-05|
|Kirghiso|`ky`|✓|2021-01-05|
|Coreano|`ko`|✓|    |
|Curdo|`ku`|✓|    |
|Lao|`lo`|✓|    |
|Latino|`la`|✓|    |
|Lettone|`lv`|✓|    |
|Lituano|`lt`|✓|    |
|Lussemburghese|`lb`|✓|2021-01-05|
|Macedone|`mk`|✓|    |
|Malgascio|`mg`|✓|2020-09-01|
|Malese|`ms`|✓|    |
|Malayalam|`ml`|✓|    |
|Maltese|`mt`|✓|    |
|Maori|`mi`|✓|2020-09-01|
|Marathi|`mr`|✓|2020-09-01|
|Mongolo|`mn`|✓|2021-01-05|
|Nepalese|`ne`|✓|2021-01-05|
|Norvegese|`no`|✓|    |
|Norvegese Nynorsk|`nn`|✓|    |
|Oriya|`or`|✓|    |
|Pasht|`ps`|✓|    |
|Persiano|`fa`|✓|    |
|Polacco|`pl`|✓|    |
|Portoghese|`pt`|✓|    |
|Punjabi|`pa`|✓|    |
|Querétaro Otomi|`otq`|✓|2020-09-01|
|Rumeno|`ro`|✓|    |
|Russo|`ru`|✓|    |
|Samoano|`sm`|✓|2020-09-01|
|Serbo|`sr`|✓|    |
|Shona|`sn`|✓|2021-01-05|
|Sindhi|`sd`|✓|2021-01-05|
|Singalese|`si`|✓|    |
|Slovacco|`sk`|✓|    |
|Sloveno|`sl`|✓|    |
|Somalo|`so`|✓|    |
|Spagnolo|`es`|✓|    |
|Sundanese|`su`|✓|2021-01-05|
|Swahili|`sw`|✓|    |
|Svedese|`sv`|✓|    |
|Tagalog|`tl`|✓|    |
|Tahitiano|`ty`|✓|2020-09-01|
|Tagico|`tg`|✓|2021-01-05|
|Tamil|`ta`|✓|    |
|Tatar|`tt`|✓|2021-01-05|
|Telugu|`te`|✓|    |
|Thai|`th`|✓|    |
|Tibetano|`bo`|✓|2021-01-05|
|Tigrinya|`ti`|✓|2021-01-05|
|Tongano|`to`|✓|2020-09-01|
|Turco|`tr`|✓|2021-01-05|
|Turkmeno|`tk`|✓|2021-01-05|
|Xhosa|`xh`|✓|2021-01-05|
|Yoruba|`yo`|✓|2021-01-05|
|Zulù|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Vedi anche

* [Che cos'è Analisi del testo API?](overview.md)   
