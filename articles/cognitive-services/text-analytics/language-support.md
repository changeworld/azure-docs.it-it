---
title: Supporto per la lingua - API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Ecco un elenco di linguaggi naturali supportati dall'API Analisi del testo. Questo articolo illustra le lingue supportate per ogni operazione.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: af7c3f85a75cfb425003b0da2af268fbba1d1d10
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092585"
---
# <a name="text-analytics-api-v3-language-support"></a>Supporto del linguaggio API Analisi del testo V3 

#### <a name="sentiment-analysis"></a>[Analisi del sentiment](#tab/sentiment-analysis)

| Linguaggio              | Codice lingua | supporto V2 | supporto V3 | Avvio della versione del modello V3: |              Note |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Cinese semplificato    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | Accettato anche `zh` |
| Cinese tradizionale   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Danese               |     `da`      |     ✓      |            |                            |                    |
| Olandese                 |     `nl`      |     ✓      |            |                            |                    |
| Inglese               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finlandese               |     `fi`      |     ✓      |            |                            |                    |
| Francese                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Tedesco                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Greco                 |     `el`      |     ✓      |            |                            |                    |
| Hindi                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Giapponese              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Coreano                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norvegese (Bokmål)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Polacco                |     `pl`      |     ✓      |            |                            |                    |
| Portoghese (Brasile)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
| Portoghese (Portogallo) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | Accettato anche `pt` |
| Russo               |     `ru`      |     ✓      |            |                            |                    |
| Spagnolo               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Svedese               |     `sv`      |     ✓      |            |                            |                    |
| Turco               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Opinion Mining (versione 3.1-solo anteprima)

| Linguaggio              | Codice lingua | A partire dalla versione del modello V3: |              Note |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Inglese               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Riconoscimento di entità denominate (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Vengono restituite solo le entità "person", "location" e "Organization" per le lingue contrassegnate con *.

| Linguaggio               | Codice lingua | supporto V3 | A partire dalla versione del modello V3: |       Note        |
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

| Linguaggio              | Codice lingua | supporto V2 | supporto V3 | Disponibile a partire dalla versione del modello V3: |       Note        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Danese                |     `da`      |     ✓      |      ✓     |                2019-10-01                 |                    |
| Olandese                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Inglese               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finlandese               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Francese                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Tedesco                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Giapponese              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Coreano                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norvegese (Bokmål)   |     `no`      |     ✓      |     ✓      |                2020-07-01                 | Accettato anche `nb` |
| Polacco                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portoghese (Brasile)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portoghese (Portogallo) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | Accettato anche `pt` |
| Russo               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Spagnolo               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Svedese               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Collegamento di entità](#tab/entity-linking)

| Linguaggio | Codice lingua | supporto V2 | supporto V3 | Disponibile a partire dalla versione del modello V3: | Note |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Inglese  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Spagnolo  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Rilevamento lingua](#tab/language-detection)

Il API Analisi del testo è in grado di rilevare un'ampia gamma di linguaggi, varianti, dialetti e lingue regionali/culturali e di restituire le lingue rilevate con il nome e il codice. Analisi del testo Rilevamento lingua parametri del codice lingua sono conformi allo standard [BCP-47](https://tools.ietf.org/html/bcp47) con la maggior parte di essi conformi agli identificatori [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) . 

Se si ha contenuto espresso in un lingua usata con minore frequenza, si può provare Rilevamento lingua per vedere se viene restituito un codice. La risposta per le lingue che non è possibile rilevare è `unknown`.

| Linguaggio | Codice lingua | supporto V3 | Disponibile a partire dalla versione del modello V3: |
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
|Khmer centrale|`km`|✓|    |
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
|Turkmeno|`tk`|✓|2021-01-05|
|Xhosa|`xh`|✓|2021-01-05|
|Yoruba|`yo`|✓|2021-01-05|
|Zulù|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Vedi anche

* [Qual è la API Analisi del testo?](overview.md)   
