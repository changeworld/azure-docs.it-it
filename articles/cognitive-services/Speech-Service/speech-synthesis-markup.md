---
title: Speech Synthesis Markup Language (SSML) - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Uso di Speech Synthesis Markup Language per controllare la pronuncia e la prosodia nella sintesi vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1d21691af4d52892f507695a56331816b14bf517
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588378"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Migliorare la sintesi con Speech Synthesis Markup Language (SSML)

Speech Synthesis Markup Language (SSML) è un linguaggio di markup basato su XML che consente agli sviluppatori di specificare come il testo di input viene convertito in sintesi vocale usando il servizio di sintesi vocale. Rispetto al testo normale, SSML consente agli sviluppatori di ottimizzare il tono, la pronuncia, la velocità di pronuncia, il volume e altro ancora dell'output di sintesi vocale. La punteggiatura normale, ad esempio la sospensione dopo un punto o l'uso dell'intonazione corretta quando una frase termina con un punto interrogativo, viene gestita automaticamente.

L'implementazione del servizio Voce di SSML è basata World Wide Web Consortium [Speech Synthesis Markup Language versione 1.0.](https://www.w3.org/TR/speech-synthesis)

> [!IMPORTANT]
> I caratteri cinese, giapponese e coreano vengono conteggiati come due caratteri per la fatturazione. Per altre informazioni, vedere [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="neural-and-custom-voices"></a>Voci neurali e personalizzate

Usare una voce neurale simile a quella umana o creare una voce personalizzata univoca per il prodotto o il marchio. Per un elenco completo delle lingue, delle impostazioni locali e delle voci supportate, vedere Supporto [della lingua](language-support.md). Per altre informazioni sulle voci neurali e personalizzate, vedere [Panoramica della sintesi vocale.](text-to-speech.md)


> [!NOTE]
> È possibile ascoltare le voci in stili e piazzole diversi leggendo il testo di esempio usando la Sintesi vocale [pagina](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features).


## <a name="special-characters"></a>Caratteri speciali

Quando si usa SSML, tenere presente che i caratteri speciali, ad esempio virgolette, apostrofi e parentesi quadre, devono essere preceduti da caratteri di escape. Per altre informazioni, vedere [Extensible Markup Language (XML) 1.0: Appendice D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Elementi SSML supportati

Ogni documento SSML viene creato con elementi (o tag) SSML. Questi elementi vengono usati per regolare passo, prosodia, volume e altro ancora. Le sezioni seguenti illustrano in dettaglio come viene usato ogni elemento e quando un elemento è obbligatorio o facoltativo.

> [!IMPORTANT]
> Non dimenticare di usare le virgolette doppie per i valori degli attributi. Gli standard per xml valido e ben formato richiedono che i valori degli attributi siano racchiusi tra virgolette doppie. Ad esempio, `<prosody volume="90">` è un elemento valido ben formato, ma non lo `<prosody volume=90>` è. SSML potrebbe non riconoscere valori di attributo non racchiusi tra virgolette.

## <a name="create-an-ssml-document"></a>Creare un documento SSML

`speak` è l'elemento radice ed è **obbligatorio per** tutti i documenti SSML. `speak`L'elemento contiene informazioni importanti, ad esempio la versione, il linguaggio e la definizione del vocabolario di markup.

**Sintassi**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `version` | Indica la versione della specifica SSML usata per interpretare il markup del documento. La versione corrente è 1.0. | Obbligatoria |
| `xml:lang` | Specifica la lingua del documento radice. Il valore può contenere un codice linguistico minuscolo di due lettere (ad esempio , ) o il codice lingua e il `en` paese/area geografica maiuscolo (ad esempio, `en-US` ). | Obbligatoria |
| `xmlns` | Specifica l'URI del documento che definisce il vocabolario di markup (i tipi di elemento e i nomi degli attributi) del documento SSML. L'URI corrente è http://www.w3.org/2001/10/synthesis . | Obbligatoria |

## <a name="choose-a-voice-for-text-to-speech"></a>Scegliere una voce per la sintesi vocale

`voice`L'elemento è obbligatorio. Viene usato per specificare la voce usata per la sintesi vocale.

**Sintassi**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `name` | Identifica la voce usata per l'output di sintesi vocale. Per un elenco completo delle voci supportate, vedere [Supporto della lingua](language-support.md#text-to-speech). | Obbligatorio |

**Esempio**

> [!NOTE]
> In questo esempio viene utilizzata la `en-US-JennyNeural` voce . Per un elenco completo delle voci supportate, vedere [Supporto della lingua](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Usare più voci

`speak`All'interno dell'elemento è possibile specificare più voci per l'output di sintesi vocale. Queste voci possono essere in lingue diverse. Per ogni voce, il testo deve essere racchiuso in un `voice` elemento .

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `name` | Identifica la voce usata per l'output della sintesi vocale. Per un elenco completo delle voci supportate, vedere Supporto [per le lingue.](language-support.md#text-to-speech) | Obbligatoria |

> [!IMPORTANT]
> Più voci non sono compatibili con la funzionalità di confine di parola. Per usare più voci, è necessario che la funzionalità di confine delle parole sia disabilitata.

### <a name="disable-word-boundary"></a>Disabilitare il confine di parola

A seconda del linguaggio di Speech SDK, si imposta la `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` proprietà su `false` in un'istanza `SpeechConfig` dell'oggetto .

# <a name="c"></a>[C#](#tab/csharp)

Per altre informazioni, vedere <a href="/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Per altre informazioni, vedere <a href="/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Per altre informazioni, vedere <a href="/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Per altre informazioni, vedere <a href="/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Per altre informazioni, vedere <a href="/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Per altre informazioni, vedere <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Per altre informazioni, vedere <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        Good morning!
    </voice>
    <voice name="en-US-GuyNeural">
        Good morning to you too Jenny!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Modificare gli stili di pronuncia

Per impostazione predefinita, il servizio di sintesi vocale sintetizza il testo usando uno stile di pronuncia neutra per le voci neurali. È possibile modificare lo stile di pronuncia per esprimere emozioni diverse, ad esempio la soddisfazione, l'empatia e il contrasto, oppure ottimizzare la voce per diversi scenari, ad esempio il servizio clienti, il newscasting e l'assistente vocale, usando l'elemento `mstts:express-as` . Si tratta di un elemento facoltativo univoco del servizio Voce.

Attualmente, le regolazioni dello stile di pronuncia sono supportate per le voci neurali seguenti:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` (anteprima)
* `zh-CN-XiaohanNeural` (anteprima)
* `zh-CN-XiaomoNeural` (anteprima)
* `zh-CN-XiaoxuanNeural` (anteprima)
* `zh-CN-XiaoruiNeural` (anteprima)

L'intensità dello stile di pronuncia può essere ulteriormente modificata per adattarsi meglio al caso d'uso. È possibile specificare uno stile più forte o più softer `styledegree` con per rendere il parlato più espressivo o sottomesso. Attualmente, le regolazioni dello stile di pronuncia sono supportate per le voci neurali in cinese (cinese semplificato).

Oltre a regolare gli stili di pronuncia e il grado di stile, è anche possibile modificare il parametro in modo che la voce rieva una diversa `role` età e sesso. Ad esempio, una voce di sesso uomo può alzare il passo e modificare l'intonazione per intonare una voce femminile, ma il nome della voce non verrà modificato. Attualmente sono supportate le rettifiche di ruolo per queste voci neurali in cinese (cinese semplificato):
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

Le modifiche precedenti vengono applicate a livello di frase e gli stili e i ruoli possono variare in base alla voce. Se uno stile o un gioco di ruolo non è supportato, il servizio restituirà il parlato in modo neutro predefinito. È possibile visualizzare gli stili e i ruoli supportati per ogni voce tramite [l'API](rest-text-to-speech.md#get-a-list-of-voices) dell'elenco vocale o tramite la piattaforma Creazione di contenuto audio [codice.](https://aka.ms/audiocontentcreation)

**Sintassi**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> Al momento supporta solo voci neurali in cinese `styledegree` (mandarino, semplificato). `role` supporta solo zh-CN-XiaomoNeural e zh-CN-XiaoxuanNeural.

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `style` | Specifica lo stile di pronuncia. Attualmente, gli stili di pronuncia sono specifici della voce. | Obbligatorio se si modifica lo stile di pronuncia per una voce neurale. Se si `mstts:express-as` usa , è necessario specificare style. Se viene specificato un valore non valido, questo elemento verrà ignorato. |
| `styledegree` | Specifica l'intensità dello stile parlante. **Valori accettati:** da 0,01 a 2 inclusi. Il valore predefinito è 1, che indica l'intensità di stile predefinita. L'unità minima è 0,01, con una tendenza leggermente tendente allo stile di destinazione. Il valore 2 comporta un raddoppio dell'intensità dello stile predefinito.  | Facoltativo (al momento supporta solo voci neurali in cinese `styledegree` (mandarino, semplificato).|
| `role` | Specifica il gioco di ruolo parlanti. La voce fungerà da età e sesso diversi, ma il nome della voce non verrà modificato.  | Facoltativo (al momento supporta solo `role` zh-CN-XiaomoNeural e zh-CN-XiaoxuanNeural).|

Usare questa tabella per determinare gli stili di pronuncia supportati per ogni voce neurale.

| Chiamata vocale                   | Stile                     | Descrizione                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Esprime un tono formale, sicuro di sé e autorevole per il recapito di notizie |
|                         | `style="newscast-casual"` | Esprime un tono versatile e casuale per il recapito generale di notizie        |
|                         | `style="narration-professional"` | Esprimere un tono professionale e obiettivo per la lettura del contenuto        |
|                         | `style="customerservice"` | Esprime un tono descrittivo e utile per il supporto clienti  |
|                         | `style="chat"`            | Esprime un tono casuale e disteso                         |
|                         | `style="cheerful"`        | Esprime un tono positivo e positivo                         |
|                         | `style="empathetic"`      | Esprime un senso di cura e comprensione               |
| `en-US-JennyNeural`     | `style="customerservice"` | Esprime un tono descrittivo e utile per il supporto clienti  |
|                         | `style="chat"`            | Esprime un tono casuale e disteso                         |
|                         | `style="assistant"`       | Esprime un tono caldo e rilassato per gli assistenti digitali    |
|                         | `style="newscast"`        | Esprime un tono versatile e casuale per il recapito generale di notizie   |
| `en-US-GuyNeural`       | `style="newscast"`        | Esprime un tono formale e professionale per la narrazione delle notizie |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Esprime un atteggiamento cool, raccolto e composto quando si parla. Tono, tono, prosodia è molto più uniforme rispetto ad altri tipi di voce.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Esprime un tono formale e professionale per la narrazione delle notizie |
|                         | `style="customerservice"` | Esprime un tono descrittivo e utile per il supporto clienti  |
|                         | `style="assistant"`       | Esprime un tono caldo e rilassato per gli assistenti digitali    |
|                         | `style="chat"`            | Esprime un tono casuale e rilassato per chit-chat           |
|                         | `style="calm"`            | Esprime un atteggiamento cool, raccolto e composto quando si parla. Tono, tono, prosodia è molto più uniforme rispetto ad altri tipi di voce.                                |
|                         | `style="cheerful"`        | Esprime un tono alleato ed entusiaco, con un tono più alto e un'energia vocale più elevata                         |
|                         | `style="sad"`             | Esprime un tono acuto, con un tono più alto, una minore intensità e una minore energia vocale. Gli indicatori comuni di questa emozione sono i baffi o il grido durante la voce.            |
|                         | `style="angry"`           | Esprime un tono infastidire e infastidire, con tono più basso, maggiore intensità e maggiore energia vocale. Il parlante è in uno stato di irate, dispiaciuta e offeso.       |
|                         | `style="fearful"`         | Esprime un tono impaurito e insodificato, con un tono più alto, una maggiore energia vocale e una velocità più veloce. Il parlante è in uno stato di tensione e disaffezione.                          |
|                         | `style="disgruntled"`     | Esprime un tono disdegnato e lamentoso. La voce di questa emozione mostra disappregazione e oltraggio.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. L'altoparlante spesso sembra più rigido e molto meno rilassato con cadenza ferma.          |
|                         | `style="affectionate"`    | Esprime un tono caldo e affezionato, con tono più alto e energia vocale. Il parlante è in uno stato di attirare l'attenzione del listener. La "personalità" del parlante è spesso di natura affezionata.          |
|                         | `style="gentle"`          | Esprime un tono lieve, educato e piacevole, con tono più basso ed energia vocale         |
|                         | `style="lyrical"`         | Esprime emozioni in modo melodico e affettivo         |
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Esprime un tono descrittivo e utile per il supporto clienti  |
| `zh-CN-YunyeNeural`     | `style="calm"`            | Esprime un atteggiamento cool, raccolto e composto quando si parla. Tono, tono, prosodia è molto più uniforme rispetto ad altri tipi di voce.    |
|                         | `style="cheerful"`        | Esprime un tono alleato ed entusiaco, con un tono più alto e un'energia vocale più elevata                         |
|                         | `style="sad"`             | Esprime un tono acuto, con un tono più alto, una minore intensità e una minore energia vocale. Gli indicatori comuni di questa emozione sono i baffi o il grido durante il parlato.            |
|                         | `style="angry"`           | Esprime un tono acuto e infastiedito, con tono più basso, intensità più elevata ed energia più elevata. L'altoparlante è in stato di ira, dispiaciuta e offeso.       |
|                         | `style="fearful"`         | Esprime un tono acuto e acuto, con un tono più alto, un'energia più elevata e una velocità più veloce. L'altoparlante è in uno stato di tenserezza e disasibilità.                          |
|                         | `style="disgruntled"`     | Esprime un tono disdegnoso e lamentoso. Il parlato di questa emozione mostra disapprompio e oltraggio.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. Il parlante spesso sembra più strano e molto meno rigido con una cadenza rigida.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Esprime un tono alleato e acuto, con un tono più alto e una maggiore energia                         |
|                         | `style="sad"`             | Esprime un tono acuto, con tono più alto, minore intensità ed energia inferiore. Gli indicatori comuni di questa emozione sono i baffi o il grido durante il parlato.            |
|                         | `style="angry"`           | Esprime un tono acuto e infastiedito, con tono più basso, intensità più elevata ed energia più elevata. Il parlante è in uno stato di irate, displeaso e offeso.       |
|                         | `style="fearful"`         | Esprime un tono acuto e acuto, con un tono più alto, un'energia più elevata e una velocità più veloce. L'altoparlante è in uno stato di tenserezza e disasibilità.                          |
|                         | `style="disgruntled"`     | Esprime un tono disdegnoso e lamentoso. Il parlato di questa emozione mostra disapprompio e oltraggio.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. Il parlante spesso sembra più strano e molto meno rigido con una cadenza rigida.    |
|                         | `style="depressed"`       | Esprime un tono acuto e sprezzante con tono inferiore ed energia    |
|                         | `style="embarrassed"`     | Esprime un tono incerto ed esitante quando l'altoparlante è insoddificato   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Esprime un tono alleato e acuto, con un tono più alto e un'energia più elevata                         |
|                         | `style="sad"`             | Esprime un tono acuto, con tono più alto, minore intensità ed energia inferiore. Gli indicatori comuni di questa emozione sono i baffi o il grido durante il parlato.            |
|                         | `style="angry"`           | Esprime un tono acuto e infastiedito, con tono più basso, intensità più elevata ed energia più elevata. L'altoparlante è in stato di ira, dispiaciuta e offeso.       |
|                         | `style="fearful"`         | Esprime un tono acuto e acuto, con un tono più alto, un'energia più elevata e una velocità più veloce. L'altoparlante è in uno stato di tenserezza e disasibilità.                          |
|                         | `style="disgruntled"`     | Esprime un tono disdegnoso e lamentoso. Il parlato di questa emozione mostra disapprompio e oltraggio.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. L'altoparlante spesso sembra più semplice e molto meno rigido con una cadenza rigida.    |
|                         | `style="embarrassed"`     | Esprime un tono incerto ed esitante quando l'altoparlante si disdegna   |
|                         | `style="affectionate"`    | Esprime un tono caldo e affezionato, con tono più alto ed energia acuto. L'altoparlante è in uno stato che attrae l'attenzione del listener. La "personalità" del parlante è spesso di natura azzarre.          |
|                         | `style="gentle"`          | Esprime un tono acuto, cortese e gradevole, con tono più basso ed energia canta         |
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Esprime un tono alleato e acuto, con un tono più alto e una maggiore energia                         |
|                         | `style="angry"`           | Esprime un tono acuto e infastiedito, con tono più basso, maggiore intensità ed energia più elevata. L'altoparlante è in stato di ira, dispiaciuta e offeso.       |
|                         | `style="fearful"`         | Esprime un tono acuto e acuto, con un tono più alto, un'energia più elevata e una velocità più veloce. L'altoparlante è in uno stato di tenserezza e disasibilità.                          |
|                         | `style="disgruntled"`     | Esprime un tono disdegnoso e lamentoso. Il parlato di questa emozione mostra disapprompio e oltraggio.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. Il parlante spesso sembra più strano e molto meno rigido con una cadenza rigida.    |
|                         | `style="depressed"`       | Esprime un tono acuto e sprezzante con tono ed energia inferiori    |
|                         | `style="gentle"`          | Esprime un tono acuto, cortese e gradevole, con tono più basso ed energia canta         |
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Esprime un tono alleato e acuto, con un tono più alto e una maggiore energia                         |
|                         | `style="angry"`           | Esprime un tono acuto e infastiedito, con tono più basso, intensità più elevata ed energia più elevata. Il parlante è in uno stato di irate, displeaso e offeso.       |
|                         | `style="fearful"`         | Esprime un tono acuto e acuto, con un tono più alto, un'energia più elevata e una velocità più veloce. L'altoparlante è in uno stato di tenserezza e disasibilità.                          |
|                         | `style="disgruntled"`     | Esprime un tono disdegnoso e lamentoso. Il parlato di questa emozione mostra disapprompio e oltraggio.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. Il parlante spesso sembra più strano e molto meno rigido con una cadenza rigida.    |
|                         | `style="depressed"`       | Esprime un tono acuto e sprezzante con tono ed energia inferiori    |
|                         | `style="gentle"`          | Esprime un tono acuto, cortese e gradevole, con tono più basso ed energia canta         |
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Esprime un tono acuto, con un tono più alto, una minore intensità e una minore energia vocale. Gli indicatori comuni di questa emozione sono i baffi o il grido durante la voce.            |
|                         | `style="angry"`           | Esprime un tono infastidire e infastidire, con tono più basso, maggiore intensità e maggiore energia vocale. Il parlante è in uno stato di irate, dispiaciuta e offeso.       |
|                         | `style="fearful"`         | Esprime un tono impaurito e insodificato, con un tono più alto, un'energia vocale più elevata e una velocità più veloce. Il parlante è in uno stato di tensione e disaffezione.                          |

Usare questa tabella per controllare i ruoli supportati e le relative definizioni.

|Ruolo                     | Descrizione                |
|-------------------------|----------------------------|
|`role="Girl"`            | La voce imita una femmina. |
|`role="Boy"`             | La voce imita un figlio. |
|`role="YoungAdultFemale"`| La voce imita una giovane adulta.|
|`role="YoungAdultMale"`  | La voce imita un giovane adulto.|
|`role="OlderAdultFemale"`| La voce imita una femmina adulta più vecchia.|
|`role="OlderAdultMale"`  | La voce imita un maschio adulto più vecchio.|
|`role="SeniorFemale"`    | La voce imita una senior female.|
|`role="SeniorMale"`      | La voce imita un maschio senior.|


**Esempio**

Questo frammento di codice SSML illustra come `<mstts:express-as>` viene usato l'elemento per modificare lo stile di pronuncia in `cheerful` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

Questo frammento di codice SSML illustra come viene usato l'attributo per modificare l'intensità dello stile `styledegree` parlante per XiaoxiaoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

Questo frammento di codice SSML illustra come viene usato l'attributo per modificare il `role` ruolo per XiaomoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Aggiungere o rimuovere un'interruzione/sospensione

Usare l'elemento per inserire pause (o interruzioni) tra le parole o impedire le pause aggiunte automaticamente dal servizio `break` di sintesi vocale.

> [!NOTE]
> Usare questo elemento per eseguire l'override del comportamento predefinito della sintesi vocale (TTS) per una parola o una frase se la sintesi vocale per tale parola o frase è innaturale. Impostare `strength` su per impedire `none` un'interruzione prosodice, che viene inserita automaticamente dal servizio di sintesi vocale.

**Sintassi**

```xml
<break strength="string" />
<break time="string" />
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `strength` | Specifica la durata relativa di una pausa utilizzando uno dei valori seguenti:<ul><li>Nessuno</li><li>x-weak</li><li>Debole</li><li>medium (impostazione predefinita)</li><li>complessa</li><li>x-strong</li></ul> | Facoltativo |
| `time` | Specifica la durata assoluta di una pausa in secondi o millisecondi. Questo valore deve essere impostato su un valore inferiore a 5000 ms. Esempi di valori validi sono `2s` e `500ms` | Facoltativo |

| Forza                      | Descrizione |
|-------------------------------|-------------|
| Nessuno o se non viene specificato alcun valore | 0 ms        |
| x-weak                        | 250 ms      |
| Debole                          | 500 ms      |
| media                        | 750 ms      |
| complessa                        | 1000 ms     |
| x-strong                      | 1250 ms     |

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Aggiungi silenzio

Usare `mstts:silence` l'elemento per inserire pause prima o dopo il testo o tra le due frasi adiacenti.

> [!NOTE]
>La differenza tra e è che può essere aggiunta a qualsiasi posizione nel testo, ma il silenzio funziona solo all'inizio o alla fine del testo di input o al limite di `mstts:silence` `break` `break` 2 frasi adiacenti.


**Sintassi**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `type` | Specifica la posizione di silenzio da aggiungere: <ul><li>`Leading` : all'inizio del testo </li><li>`Tailing` : alla fine del testo </li><li>`Sentenceboundary` : tra frasi adiacenti </li></ul> | Obbligatoria |
| `Value` | Specifica la durata assoluta di una pausa in secondi o millisecondi. Questo valore deve essere impostato su un valore inferiore a 5000 ms. Esempi di valori validi sono `2s` e `500ms` | Obbligatoria |

**Esempio** In questo esempio viene `mtts:silence` usato per aggiungere 200 ms di silenzio tra due frasi.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="en-US-AriaNeural">
<mstts:silence  type="Sentenceboundary" value="200ms"/>
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way.
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time.
</voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Specificare paragrafi e frasi

`p` Gli elementi e vengono usati rispettivamente per indicare paragrafi `s` e frasi. In assenza di questi elementi, il servizio di sintesi vocale determina automaticamente la struttura del documento SSML.

`p`L'elemento può contenere testo e gli elementi seguenti: `audio` , , , , , , `break` e `phoneme` `prosody` `say-as` `sub` `mstts:express-as` `s` .

`s`L'elemento può contenere testo e gli elementi seguenti: `audio` , , , , , e `break` `phoneme` `prosody` `say-as` `mstts:express-as` `sub` .

**Sintassi**

```XML
<p></p>
<s></s>
```

**Esempio**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Usare i fonemi per migliorare la pronuncia

`ph`L'elemento viene usato per la pronuncia fonetica nei documenti SSML. `ph`L'elemento può contenere solo testo, nessun altro elemento. Fornire sempre la voce leggibile come fallback.

Gli alfabeti fonetici sono costituiti da telefoni, costituiti da lettere, numeri o caratteri, talvolta in combinazione. Ogni telefono descrive un suono di voce univoco. Questo è in contrasto con l'alfabeto latino, in cui qualsiasi lettera può rappresentare più suoni parlati. Si considerino le diverse pronunce della lettera "c" nelle parole "candy" e "cease" o le diverse pronunce della combinazione di lettere "th" nelle parole "thing" e "those".

> [!NOTE]
> Il tag Phonemes non è attualmente supportato per queste 5 voci (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural e mt-MT-GarceNeural).

**Sintassi**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `alphabet` | Specifica l'alfabeto fonetico da usare per sintetizzare la pronuncia della stringa `ph` nell'attributo . La stringa che specifica l'alfabeto deve essere specificata in lettere minuscole. Di seguito sono riportati i possibili alfabeti che è possibile specificare.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Alfabeto fonetico internazionale</a></li><li>`sapi`&ndash; [Alfabeto fonetico del servizio Voce](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Set di telefoni universali</a></li></ul><br>L'alfabeto si applica solo `phoneme` a nell'elemento . | Facoltativo |
| `ph` | Stringa contenente i telefoni che specificano la pronuncia della parola `phoneme` nell'elemento . Se la stringa specificata contiene telefoni non riconosciuti, il servizio di sintesi vocale rifiuta l'intero documento SSML e non produce nessuno dell'output vocale specificato nel documento. | Obbligatorio se si usano fonemi. |

**esempi**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Usare il lessico personalizzato per migliorare la pronuncia

In alcuni casi il servizio di sintesi vocale non può pronunciare in modo accurato una parola. Ad esempio, il nome di una società o un termine medico. Gli sviluppatori possono definire la modalità di lettura delle singole entità in SSML usando i `phoneme` `sub` tag e . Tuttavia, se è necessario definire la modalità di lettura di più entità, è possibile creare un lessico personalizzato usando il `lexicon` tag .

> [!NOTE]
> Il lessico personalizzato supporta attualmente la codifica UTF-8.

> [!NOTE]
> Il lessico personalizzato non è attualmente supportato per queste 5 voci (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural e mt-MT-GarceNeural).


**Sintassi**

```XML
<lexicon uri="string"/>
```

**Attributes (Attributi)**

| Attributo | Descrizione                               | Obbligatoria / Facoltativa |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Indirizzo del documento PLS esterno. | Obbligatorio.           |

**Utilizzo**

Per definire la modalità di lettura di più entità, è possibile creare un lessico personalizzato, archiviato come file con estensione xml o pls. Di seguito è riportato un file xml di esempio.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias>By the way</alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

`lexicon`L'elemento contiene almeno un elemento `lexeme` . Ogni `lexeme` elemento contiene almeno un elemento e uno o più elementi , e `grapheme` `grapheme` `alias` `phoneme` . `grapheme`L'elemento contiene testo che descrive <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">l'ortografia </a>. Gli elementi vengono usati per indicare la pronuncia di un `alias` acronimo o di un termine abbreviato. `phoneme`L'elemento fornisce testo che descrive come viene `lexeme` pronunciato .

È importante notare che non è possibile impostare direttamente la pronuncia di una frase usando il lessico personalizzato. Se è necessario impostare la pronuncia per un acronimo o un termine abbreviato, specificare prima di tutto , quindi associare a `alias` `phoneme` tale `alias` . Ad esempio:

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>ScotlandMV</alias>
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme>
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

È anche possibile specificare direttamente il previsto `alias` per l'acronimo o il termine abbreviato. Ad esempio:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>Scotland Media Wave</alias>
  </lexeme>
```

> [!IMPORTANT]
> `phoneme`L'elemento non può contenere spazi vuoti quando si usa IPA.

Per altre informazioni sul file lessico personalizzato, vedere [Pronunciation Lexicon Specification (PLS) Version 1.0](https://www.w3.org/TR/pronunciation-lexicon/).

Pubblicare quindi il file lessico personalizzato. Anche se non sono state applicate restrizioni sulla posizione in cui è possibile archiviare questo file, è consigliabile usare Archiviazione BLOB di Azure [.](../../storage/blobs/storage-quickstart-blobs-portal.md)

Dopo aver pubblicato il lessico personalizzato, è possibile fare riferimento a esso da SSML.

> [!NOTE]
> `lexicon`L'elemento deve essere all'interno `voice` dell'elemento .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
          xmlns:mstts="http://www.w3.org/2001/mstts"
          xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

Quando si usa questo lessico personalizzato, "BTW" verrà letto come "A proposito". "Benigni" verrà letto con il "bɛˈniːnji" IPA fornito.

**Limitazioni**
- Dimensioni del file: il limite massimo di dimensioni del file lessico personalizzato è 100 KB, se oltre queste dimensioni la richiesta di sintesi avrà esito negativo.
- Aggiornamento della cache lessico: il lessico personalizzato verrà memorizzato nella cache con URI come chiave nel servizio TTS al primo caricamento. Il lessico con lo stesso URI non verrà ricaricato entro 15 minuti, quindi la modifica personalizzata del lessico deve attendere al massimo 15 minuti per essere effettiva.

**Set fonetici del servizio Voce**

Nell'esempio precedente viene utilizzato l'alfabeto fonetico internazionale, noto anche come set di telefoni IPA. È consigliabile che gli sviluppatori usino il programma IPA, perché è lo standard internazionale. Per alcuni caratteri IPA, hanno la versione "precomposed" e "decomposed" quando vengono rappresentati con Unicode. Il lessico personalizzato supporta solo i caratteri Unicode scomposti.

Considerando che l'IPA non è facile da ricordare, il servizio Voce definisce un set fonetico per sette lingue ( `en-US` , , , , , e `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` `zh-TW` ).

È possibile usare `sapi` come valore per `alphabet` l'attributo con lessico personalizzati, come illustrato di seguito:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Per altre informazioni sull'alfabeto fonetico dettagliato del servizio Voce, vedere Set fonetici del servizio [Voce.](speech-ssml-phonetic-sets.md)

## <a name="adjust-prosody"></a>Regolare la prosodia

L'elemento viene usato per specificare le modifiche a `prosody` tono, contorno, intervallo, frequenza, durata e volume per l'output della sintesi vocale. `prosody`L'elemento può contenere testo e gli elementi seguenti: `audio` , , , , , , `break` e `p` `phoneme` `prosody` `say-as` `sub` `s` .

Poiché i valori degli attributi prosodici possono variare in un intervallo ampio, il riconoscimento vocale interpreta i valori assegnati come un suggerimento dei valori prosodici effettivi della voce selezionata. Il servizio di sintesi vocale limita o sostituisce i valori non supportati. Esempi di valori non supportati sono un passo di 1 MHz o un volume di 120.

**Sintassi**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `pitch` | Indica l'intonazione della linea di base per il testo. È possibile esprimere il passo come:<ul><li>Valore assoluto, espresso come numero seguito da "Hz" (Hertz). Ad esempio: `<prosody pitch="600Hz">some text</prosody>`.</li><li>Valore relativo, espresso come numero preceduto da "+" o "-" e seguito da "Hz" o "st", che specifica la quantità di variazione dell'intonazione. Ad esempio, `<prosody pitch="+80Hz">some text</prosody>` o `<prosody pitch="-2st">some text</prosody>`. "st" indica che l'unità di modifica è semitona, ovvero la metà di un tono (un mezzo passo) sulla scala diatonica standard.</li><li>Valore costante:<ul><li>x-low</li><li>low</li><li>media</li><li>high</li><li>x-high</li><li>default</li></ul></li></ul> | Facoltativo |
| `contour` |Contour supporta ora sia le voci neurali che le voci standard. Il contorno rappresenta le modifiche nel passo. Queste modifiche vengono rappresentate come una matrice di destinazioni in posizioni tempormente specificate nell'output vocale. Ogni destinazione è definita da set di coppie di parametri. Ad esempio: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Il primo valore in ogni set di parametri specifica la posizione della modifica dell'altezza come percentuale della durata del testo. Il secondo valore specifica la quantità di aumento o riduzione del passo, usando un valore relativo o un valore di enumerazione per pitch (vedere `pitch` ). | Facoltativo |
| `range` | Valore che rappresenta l'intervallo di intonazione per il testo. È possibile esprimere `range` usando gli stessi valori assoluti, valori relativi o valori di enumerazione usati per descrivere `pitch` . | Facoltativo |
| `rate` | Indica la frequenza di pronuncia del testo. È possibile esprimere `rate` come:<ul><li>Valore relativo, espresso come numero che funge da moltiplicatore del valore predefinito. Ad esempio, un valore pari a *1* non comporta alcuna modifica della frequenza. Un valore *pari a 0,5* comporta una dimezzamento della frequenza. Il valore *3* determina una tripla della velocità.</li><li>Valore costante:<ul><li>x-slow</li><li>lento</li><li>media</li><li>veloce</li><li>velocità x</li><li>default</li></ul></li></ul> | Facoltativo |
| `duration` | Periodo di tempo che deve trascorrere mentre il servizio di sintesi vocale legge il testo, in secondi o millisecondi. Ad esempio, *2s* o *1800 ms*. La durata supporta solo voci standard.| Facoltativo |
| `volume` | Indica il livello di volume della voce parlante. È possibile esprimere il volume come:<ul><li>Valore assoluto, espresso come numero compreso tra 0,0 e 100,0, dal più silenzioso *al* *più alto.* Ad esempio, 75. Il valore predefinito è 100.0.</li><li>Valore relativo, espresso come numero preceduto da "+" o "-" che specifica una quantità di modifica del volume. Ad esempio, +10 o -5.5.</li><li>Valore costante:<ul><li>nessun suono</li><li>x-soft</li><li>Morbido</li><li>media</li><li>Forte</li><li>x-loud</li><li>default</li></ul></li></ul> | Facoltativo |

### <a name="change-speaking-rate"></a>Cambiare la velocità del parlato

La velocità di pronuncia può essere applicata alle voci neurali e alle voci standard a livello di parola o frase.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Cambiare il volume

Le modifiche al volume possono essere applicate alle voci standard a livello di parola o frase. Mentre le modifiche al volume possono essere applicate solo alle voci neurali a livello di frase.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Cambiare il tono

Le modifiche al tono possono essere applicate alle voci standard a livello di parola o frase. Mentre le modifiche al passo possono essere applicate solo alle voci neurali a livello di frase.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Cambiare il profilo di intonazione

> [!IMPORTANT]
> Le modifiche del contorno del passo sono ora supportate con le voci neurali.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room?
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>Elemento say-as

`say-as` è un elemento facoltativo che indica il tipo di contenuto (ad esempio numero o data) del testo dell'elemento. Vengono fornite indicazioni al motore di sintesi vocale su come pronunciare il testo.

**Sintassi**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `interpret-as` | Indica il tipo di contenuto del testo dell'elemento. Per un elenco dei tipi, vedere la tabella seguente. | Obbligatoria |
| `format` | Fornisce informazioni aggiuntive sulla formattazione precisa del testo dell'elemento per i tipi di contenuto che possono avere formati ambigui. SSML definisce i formati per i tipi di contenuto che li usano (vedere la tabella seguente). | Facoltativo |
| `detail` | Indica il livello di dettaglio da pronunciare. Ad esempio, questo attributo potrebbe richiedere al motore di sintesi vocale di pronunciare segni di punteggiatura. Non sono definiti valori standard per `detail` . | Facoltativo |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Di seguito sono riportati i tipi di contenuto supportati per `interpret-as` gli attributi `format` e . Includere `format` l'attributo solo se `interpret-as` è impostato su data e ora.

| interpret-as | format | Interpretazione |
|--------------|--------|----------------|
| `address` | | Il testo viene pronunciato come indirizzo. Il motore di sintesi vocale pronuncia:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Come "I'm at 150th court north east redmond washington". |
| `cardinal`, `number` | | Il testo viene pronunciato come numero cardinale. Il motore di sintesi vocale pronuncia:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Come "Esistono tre alternative". |
| `characters`, `spell-out` | | Il testo viene pronunciato come singole lettere (digitate). Il motore di sintesi vocale pronuncia:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Come "T E S T". |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | Il testo viene pronunciato come data. `format`L'attributo specifica il formato della data (*d=day, m=month e y=year*). Il motore di sintesi vocale pronuncia:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Come "Oggi è ottobre 2000 100.000". |
| `digits`, `number_digit` | | Il testo viene pronunciato come sequenza di singole cifre. Il motore di sintesi vocale pronuncia:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Come "1 2 3 4 5 6 7 8 9". |
| `fraction` | | Il testo viene pronunciato come numero frazionario. Il motore di sintesi vocale pronuncia:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Come "tre ottavi di pollice". |
| `ordinal` | | Il testo viene pronunciato come numero ordinale. Il motore di sintesi vocale pronuncia:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Come "Selezionare la terza opzione". |
| `telephone` | | Il testo viene pronunciato come numero di telefono. `format`L'attributo può contenere cifre che rappresentano un codice paese. Ad esempio, "1" per la Stati Uniti o "39" per l'Italia. Il motore di sintesi vocale può usare queste informazioni per guidarne la pronuncia di un numero di telefono. Il numero di telefono può includere anche il codice paese e, in tal caso, ha la precedenza sul codice paese in `format` . Il motore di sintesi vocale pronuncia:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Come "Il mio numero è il codice di area otto otto otto cinque cinque cinque uno due uno due". |
| `time` | hms12, hms24 | Il testo viene pronunciato come ora. L'attributo specifica se l'ora viene specificata usando un orologio `format` a 12 ore (hms12) o 24 ore (hms24). Usare i due punti per separare i numeri che rappresentano ore, minuti e secondi. Di seguito sono riportati esempi di ora validi: 12:35, 1:14:32, 08:15 e 02:50:45. Il motore di sintesi vocale pronuncia:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Come "Il treno parte alle 4:00". |

**Utilizzo**

`say-as`L'elemento può contenere solo testo.

**Esempio**

Il motore di sintesi vocale pronuncia l'esempio seguente come "La prima richiesta è stata per una stanza il diciannovesimo ottobre 2010 con arrivo anticipato alle 12.30".

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Aggiungere audio registrato

`audio` è un elemento facoltativo che consente di inserire audio MP3 in un documento SSML. Il corpo dell'elemento audio può contenere testo normale o markup SSML pronunciato se il file audio non è disponibile o non è riproducibile. Inoltre, `audio` l'elemento può contenere testo e gli elementi seguenti: `audio` , , , , , , , e `break` `p` `s` `phoneme` `prosody` `say-as` `sub` .

Qualsiasi audio incluso nel documento SSML deve soddisfare questi requisiti:

* L'MP3 deve essere ospitato in un endpoint HTTPS accessibile da Internet. HTTPS è obbligatorio e il dominio che ospita il file MP3 deve presentare un certificato TLS/SSL attendibile valido.
* Mp3 deve essere un file MP3 valido (MPEG v2).
* La velocità in bit deve essere di 48 kbps.
* La frequenza di campionamento deve essere di 16.000 Hz.
* Il tempo totale combinato per tutti i file di testo e audio in una singola risposta non può superare i 90 (90) secondi.
* L'MP3 non deve contenere informazioni specifiche del cliente o altre informazioni riservate.

**Sintassi**

```xml
<audio src="string"/></audio>
```

**Attributes (Attributi)**

| Attributo | Descrizione                                   | Obbligatoria / Facoltativa                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Specifica il percorso/URL del file audio. | Obbligatorio se si usa l'elemento audio nel documento SSML. |

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Aggiungere audio di sfondo

L'elemento consente di aggiungere audio di sfondo ai documenti SSML o di combinare un `mstts:backgroundaudio` file audio con la sintesi vocale. Con è possibile riprodurre in ciclo un file audio in background, dissolvenza verso l'interno all'inizio della sintesi vocale e dissolvenza in uscita alla fine della `mstts:backgroundaudio` sintesi vocale.

Se l'audio di sfondo fornito è più breve della sintesi vocale o della dissolvenza in uscita, verrà visualizzato un ciclo. Se è più lungo della sintesi vocale, verrà interrotta al termine della dissolvenza.

È consentito un solo file audio di sfondo per ogni documento SSML. È tuttavia possibile intersperare i tag all'interno dell'elemento `audio` per aggiungere audio aggiuntivo al documento `voice` SSML.

**Sintassi**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `src` | Specifica il percorso/URL del file audio di sfondo. | Obbligatorio se si usa l'audio di sfondo nel documento SSML. |
| `volume` | Specifica il volume del file audio di sfondo. **Valori accettati:** `0` da a `100` inclusi. Il valore predefinito è `1`. | Facoltativo |
| `fadein` | Specifica la durata del "dissolvenza" dell'audio di sfondo come millisecondi. Il valore predefinito è `0` , che equivale a nessuna dissolvenza in entrata. **Valori accettati:** `0` in `10000` inclusivi.  | Facoltativo |
| `fadeout` | Specifica la durata della dissolvenza in millisecondi dell'audio in background. Il valore predefinito è , che equivale a nessuna `0` dissolvenza in uscita. **Valori accettati:** `0` in `10000` inclusivi.  | Facoltativo |

**Esempio**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JennyNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="bookmark-element"></a>Elemento Bookmark

L'elemento bookmark consente di inserire marcatori personalizzati in SSML per ottenere l'offset di ogni marcatore nel flusso audio.
Gli elementi del segnalibro non verranno letti.
L'elemento bookmark può essere usato per fare riferimento a una posizione specifica nella sequenza di testo o tag.

> [!NOTE]
> `bookmark` l'elemento funziona solo `en-US-AriaNeural` per la voce per il momento.

**Sintassi**

```xml
<bookmark mark="string"/>
```

**Attributes (Attributi)**

| Attributo | Descrizione                                   | Obbligatoria / Facoltativa                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
|  `mark`   | Specifica il testo di riferimento `bookmark` dell'elemento. | Obbligatorio. |

**Esempio**

Ad esempio, è possibile conoscere l'offset temporale di ogni parola di fiori come segue

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        We are selling <bookmark mark='flower_1'/>roses and <bookmark mark='flower_2'/>daisies.
    </voice>
</speak>
```

### <a name="get-bookmark-using-speech-sdk"></a>Ottenere un segnalibro con Speech SDK

È possibile sottoscrivere `BookmarkReached` l'evento in Speech SDK per ottenere gli offset dei segnalibri.

> [!NOTE]
> `BookmarkReached` l'evento è disponibile solo a partire da Speech SDK versione 1.16.0.

`BookmarkReached` Gli eventi vengono generati quando i dati audio di output diventano disponibili, che saranno più veloci della riproduzione in un dispositivo di output.

* `AudioOffset` indica il tempo trascorso dell'audio di output tra l'inizio della sintesi e l'elemento segnalibro. Questo valore viene misurato in unità di centinaia di nanosecondi (HNS) con 10.000 HNS equivalenti a 1 millisecondo.
* `Text` è il testo di riferimento dell'elemento bookmark, ovvero la stringa impostata `mark` nell'attributo .

# <a name="c"></a>[C#](#tab/csharp)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkreached" target="_blank"> `BookmarkReached` </a>.

```csharp
synthesizer.BookmarkReached += (s, e) =>
{
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    Console.WriteLine($"Bookmark reached. Audio offset: " +
        $"{e.AudioOffset / 10000}ms, bookmark text: {e.Text}.");
};
```

Per l'esempio SSML precedente, l'evento verrà attivato due `BookmarkReached` volte e l'output della console sarà
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="c"></a>[C++](#tab/cpp)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#bookmarkreached" target="_blank"> `BookmarkReached` </a>.

```cpp
synthesizer->BookmarkReached += [](const SpeechSynthesisBookmarkEventArgs& e)
{
    cout << "Bookmark reached. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "bookmark text: " << e.Text << "." << endl;
};
```

Per l'esempio SSML precedente, l'evento verrà attivato due `BookmarkReached` volte e l'output della console sarà
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="java"></a>[Java](#tab/java)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkReached#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_BookmarkReached" target="_blank"> `BookmarkReached` </a>.

```java
synthesizer.BookmarkReached.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Bookmark reached. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("bookmark text: " + e.getText() + ".");
});
```

Per l'esempio SSML precedente, l'evento verrà attivato due `BookmarkReached` volte e l'output della console sarà
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="python"></a>[Python](#tab/python)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#bookmark-reached" target="_blank"> `bookmark_reached` </a>.

```python
# The unit of evt.audio_offset is tick (1 tick = 100 nanoseconds), divide it by 10,000 to convert to milliseconds.
speech_synthesizer.bookmark_reached.connect(lambda evt: print(
    "Bookmark reached: {}, audio offset: {}ms, bookmark text: {}.".format(evt, evt.audio_offset / 10000, evt.text)))
```

Per l'esempio SSML precedente, `bookmark_reached` l'evento verrà attivato due volte e l'output della console sarà
```text
Bookmark reached, audio offset: 825ms, bookmark text: flower_1.
Bookmark reached, audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#bookmarkReached" target="_blank"> `bookmarkReached` </a>.

```javascript
synthesizer.bookmarkReached = function (s, e) {
    window.console.log("(Bookmark reached), Audio offset: " + e.audioOffset / 10000 + "ms, bookmark text: " + e.text);
}
```

Per l'esempio SSML precedente, `bookmarkReached` l'evento verrà attivato due volte e l'output della console sarà
```text
(Bookmark reached), Audio offset: 825ms, bookmark text: flower_1.
(Bookmark reached), Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>.

```objectivec
[synthesizer addBookmarkReachedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisBookmarkEventArgs *eventArgs) {
    // The unit of AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to converted to milliseconds.
    NSLog(@"Bookmark reached. Audio offset: %fms, bookmark text: %@.", eventArgs.audioOffset/10000., eventArgs.text);
}];
```

Per l'esempio SSML precedente, `BookmarkReached` l'evento verrà attivato due volte e l'output della console sarà
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="swift"></a>[Swift](#tab/swift)

Per altre informazioni, vedere <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer" target="_blank"> `addBookmarkReachedEventHandler` </a>.

---

## <a name="next-steps"></a>Passaggi successivi

* [Supporto per le lingue: voci, impostazioni locali, lingue](language-support.md)
