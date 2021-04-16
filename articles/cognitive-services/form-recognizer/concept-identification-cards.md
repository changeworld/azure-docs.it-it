---
title: ID - riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi all'estrazione dei dati dai documenti di identità con l'API riconoscimento modulo ID predefiniti.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: 00e51d2c9515191b6d127355f49eeed3000a46ed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514714"
---
# <a name="form-recognizer-prebuilt-identification-id-document-model"></a>riconoscimento modulo modello di documento di identificazione (ID) predefinito

Azure riconoscimento modulo analizzare ed estrarre informazioni dai documenti di identificazione rilasciati dagli enti pubblici usando il modello di ID predefinito. Combina le potenti funzionalità [OCR (Optical Character Recognition)](../computer-vision/overview-ocr.md) con le funzionalità di riconoscimento ID per estrarre informazioni chiave da Worldwide Passports e U.S. Driver's Licenses (tutti i 50 stati e D.C.). L'API ID estrae le informazioni chiave da questi documenti di identità, ad esempio nome, cognome, data di nascita, numero di documento e altro ancora. Questa API è disponibile nell'anteprima riconoscimento modulo 2.1 come servizio cloud e come contenitore locale.

## <a name="what-does-the-id-service-do"></a>Che cosa fa il servizio ID?

Il servizio ID predefinito estrae i valori chiave dai passaporti e dalle licenze del driver degli Stati Uniti e li restituisce in una risposta JSON strutturata organizzata.

### <a name="drivers-license-example"></a>**Esempio di licenza del driver**

![Licenza del driver di esempio](./media/id-example-drivers-license.JPG)

### <a name="passport-example"></a>**Esempio di Passport**

![Esempio di Passport](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Campi estratti

|Nome| Tipo | Descrizione | Valore |
|:-----|:----|:----|:----|
|  Paese | country | Codice paese conforme allo standard ISO 3166 | "USA" |
|  DateOfBirth | data | DOB in formato AAAA-MM-GG | "1980-01-01" |
|  DateOfExpiration | data | Data di scadenza nel formato AAAA-MM-GG | "2019-05-05" |
|  DocumentNumber | string | Numero di passaporto pertinente, numero di patente e così via. | "340020013" |
|  FirstName | string | Estratto nome e secondo iniziale, se applicabile | "JENNIFER" |
|  LastName | string | Cognome estratto | "INSODE" |
|  Nationality | country | Codice paese conforme allo standard ISO 3166 | "USA" |
|  Sesso | gender | I possibili valori estratti includono "M", "F" e "X" | "F" |
|  MachineReadableZone | object | MrZ passport estratto, incluse due righe di 44 caratteri ciascuna | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | string | Tipo di documento, ad esempio Passport, Driver's License | "passport" |
|  Indirizzo | string | Indirizzo estratto (solo licenza del driver) | "123 STREET ADDRESS YOUR CITY WA 999999-1234"|
|  Region | string | Area estratta, stato, provincia e così via (solo licenza del driver) | "Washington" |

### <a name="additional-features"></a>Funzionalità aggiuntive

L'API ID restituisce anche le informazioni seguenti:

* Livello di attendibilità del campo (ogni campo restituisce un valore di attendibilità associato)
* Testo non elaborato OCR (output di testo estratto da OCR per l'intera ricevuta)
* Rettangolo di selezione di ogni campo estratto nelle licenze del driver degli Stati Uniti
* Rettangolo di selezione per machine readable zone (MRZ) su Passports

  > [!NOTE]
  > Gli ID predefiniti non rilevano l'autenticità dell'ID
  >
  > riconoscimento modulo ID predefiniti estrae i dati chiave dai dati ID. Tuttavia, non rileva la validità o l'autenticità del documento di identità originale.

## <a name="try-it-out"></a>Provare questa operazione

Per provare il servizio ID riconoscimento modulo, passare a Strumento di interfaccia utente di esempio online:

> [!div class="nextstepaction"]
> [Provare i modelli predefiniti](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Requisiti di input

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Tipi di ID supportati

* **ID predefiniti v2.1-preview.3** Estrae i valori chiave dai passaporti in tutto il mondo e dalle licenze del driver degli Stati Uniti.

  > [!NOTE]
  > Supporto del tipo di ID
  >
  > I tipi di ID attualmente supportati includono il passport globale e le licenze del driver degli Stati Uniti. Microsoft sta cercando di espandere il supporto id ad altri documenti di identità in tutto il mondo.

## <a name="post-analyze-id-document"></a>Documento ID analisi POST

[L'operazione Analizza ID](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) accetta un'immagine o un PDF di un ID come input ed estrae i valori di interesse. La chiamata restituisce un campo di intestazione della risposta denominato `Operation-Location` . Il `Operation-Location` valore è un URL che contiene l'ID risultato da usare nel passaggio successivo.

|Intestazione risposta| URL risultato |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>GET Analyze Id Document Result

<!---
Need to update this with updated APIM links when available
-->

Il secondo passaggio consiste nel chiamare [**l'operazione Get Analyze idDocument Result.**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) Questa operazione accetta come input l'ID risultato creato dall'operazione Analizza ID. Restituisce una risposta JSON che contiene un campo **di** stato con i valori possibili seguenti. Questa operazione viene chiamata in modo iterativo fino a quando non viene restituito con **il valore completato.** Usare un intervallo da 3 a 5 secondi per evitare di superare la frequenza delle richieste al secondo( RPS).

|Campo| Tipo | Valori possibili |
|:-----|:----:|:----|
|status | string | notStarted: l'operazione di analisi non è stata avviata. |
| |  | running: l'operazione di analisi è in corso. |
| |  | failed: l'operazione di analisi non è riuscita. |
| |  | succeeded: l'operazione di analisi è riuscita. |

Quando il **campo status** ha il **valore succeeded,** la risposta JSON includerà il riconoscimento della ricevuta e i risultati del riconoscimento del testo. Il risultato degli ID è organizzato come dizionario di valori di campo denominati, in cui ogni valore contiene il testo estratto, il valore normalizzato, il rettangolo di selezione, l'attendibilità e gli elementi delle parole corrispondenti. Il risultato del riconoscimento del testo è organizzato in una gerarchia di righe e parole, con testo, rettangolo di selezione e informazioni di attendibilità.

![risultati della ricevuta di esempio](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Output JSON di esempio

Vedere l'esempio seguente di risposta JSON con esito positivo: Il `readResults` nodo contiene tutto il testo riconosciuto. Il testo è organizzato in base alla pagina, quindi alla riga, infine in base a singole parole. Il `documentResults` nodo contiene i valori ID individuati dal modello. In questo nodo sono inoltre disponibili coppie chiave/valore utili, ad esempio nome, cognome, numero di documento e altro ancora.

```json
{
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],

     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Provare i propri ID ed esempi nell'interfaccia [riconoscimento modulo di esempio.](https://fott-preview.azurewebsites.net/)
* Completare un [riconoscimento modulo di](quickstarts/client-library.md) avvio rapido per iniziare a scrivere un'app di elaborazione id con riconoscimento modulo nel linguaggio di sviluppo preferito.

## <a name="see-also"></a>Vedi anche

* [**Informazioni su Riconoscimento modulo**](./overview.md)
* [**Documentazione di riferimento sulle API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
