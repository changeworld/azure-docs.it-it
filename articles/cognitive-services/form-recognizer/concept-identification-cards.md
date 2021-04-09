---
title: ID-riconoscimento form
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi all'estrazione dei dati dai documenti di identità con l'API degli ID predefiniti di riconoscimento moduli.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 01a73e7940f88a3eb6e040f26d255448294cab18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467840"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>Modello di identificazione (ID) predefinito del riconoscimento moduli

Il sistema di riconoscimento dei moduli di Azure può analizzare ed estrarre le informazioni da ID (Government Identification card) usando il modello di ID predefinito. Combina le potenti funzionalità di [riconoscimento ottico dei caratteri (OCR)](../computer-vision/concept-recognizing-text.md) con funzionalità di riconoscimento ID per estrarre le informazioni chiave da passaporti in tutto il mondo e dalle licenze dei driver statunitensi (tutti gli stati 50 e D.C.). L'API IDs estrae le informazioni chiave da questi documenti di identità, ad esempio nome, cognome, data di nascita, numero di documento e altro ancora. Questa API è disponibile in formato Recognizer v 2.1 Preview come servizio cloud e come contenitore locale.

## <a name="what-does-the-id-service-do"></a>Che cosa fa il servizio ID? 

Il servizio ID predefinito estrae i valori delle chiavi dalle licenze per i passaporti e dei driver statunitensi in tutto il mondo e li restituisce in una risposta JSON strutturata organizzata. 

![Licenza del driver di esempio](./media/id-example-drivers-license.JPG)

![Passport di esempio](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Campi estratti

|Nome| Tipo | Descrizione | Valore | 
|:-----|:----|:----|:----|
|  Paese | country | Codice paese conforme allo standard ISO 3166 | USA | 
|  DateOfBirth | Data | DOB nel formato AAAA-MM-GG | "1980-01-01" | 
|  DateOfExpiration | Data | Data di scadenza nel formato AAAA-MM-GG | "2019-05-05" |  
|  DocumentNumber | string | Numero di passaporto pertinente, numero di patente del driver e così via. | "340020013" |  
|  FirstName | string | Nome estratto e iniziale secondo se applicabile | Jennifer | 
|  LastName | string | Cognome Estratto | Brooks |   
|  Nationality | country | Codice paese conforme allo standard ISO 3166 | USA |
|  Sesso | gender | I valori estratti possibili includono "M", "F" e "X" | "F" | 
|  MachineReadableZone | object | MRZ Passport estratti, incluse due righe di 44 caratteri ciascuna | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | string | Tipo di documento, ad esempio Passport, licenza del driver | Passport |  
|  Indirizzo | string | Indirizzo Estratto (solo licenza del driver) | "123 STREET ADDRESS YOUR CITY WA 99999-1234"|
|  Region | string | Area estratta, stato, Provincia e così via (solo licenza del driver) | Washington | 

### <a name="additional-features"></a>Funzionalità aggiuntive

L'API IDs restituisce anche le informazioni seguenti:

* Livello di confidenza del campo (ogni campo restituisce un valore di attendibilità associato)
* Testo non elaborato OCR (output di testo estratto dall'OCR per l'intera ricezione)
* Rettangolo di delimitazione di ogni campo Estratto nelle licenze del driver U.S.
* Rettangolo di delimitazione per la zona leggibile del computer (MRZ) su Passport

  > [!NOTE]
  > Gli ID predefiniti non rilevano l'autenticità dell'ID
  >
  > Gli ID predefiniti del riconoscimento form estraggono i dati delle chiavi dai dati ID. Tuttavia, non rileva la validità o l'autenticità del documento di identità originale. 

## <a name="try-it-out"></a>Provare questa operazione

Per provare il servizio ID riconoscimento moduli, passare allo strumento dell'interfaccia utente di esempio online:

> [!div class="nextstepaction"]
> [Provare i modelli predefiniti](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Requisiti di input

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Tipi di ID supportati  

* **ID predefiniti versione 2.1-anteprima. 3** Estrae i valori delle chiavi da passaporti in tutto il mondo e dalle licenze dei driver statunitensi. 

  > [!NOTE]
  > Supporto del tipo di ID 
  >
  > I tipi di ID attualmente supportati includono passaporto globale e licenze per i driver statunitensi. Microsoft sta attivamente cercando di espandere il supporto ID per altri documenti di identità in tutto il mondo.

## <a name="post-analyze-id-document"></a>Documento POST Analyze ID

L'operazione [Analyze ID](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) acquisisce un'immagine o un PDF di un ID come input ed estrae i valori di interesse. La chiamata restituisce un campo di intestazione della risposta denominato `Operation-Location` . Il `Operation-Location` valore è un URL che contiene l'ID risultato da usare nel passaggio successivo.

|Intestazione risposta| URL risultato |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>OTTENERE il risultato dell'analisi dell'ID del documento

<!---
Need to update this with updated APIM links when available
-->

Il secondo passaggio consiste nel chiamare l'operazione [**Get Analyze IdDocument result**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) . Questa operazione accetta come input l'ID del risultato creato dall'operazione analizza ID. Restituisce una risposta JSON che contiene un campo di **stato** con i valori possibili seguenti. Questa operazione viene chiamata in modo iterativo fino a quando non viene restituita con il valore **succeeded** . Utilizzare un intervallo da 3 a 5 secondi per evitare il superamento della frequenza di richieste al secondo (RPS).

|Campo| Tipo | Valori possibili |
|:-----|:----:|:----|
|status | string | notStarted: l'operazione di analisi non è stata avviata. |
| |  | Running: l'operazione di analisi è in corso. |
| |  | non riuscito: l'operazione di analisi non è riuscita. |
| |  | Succeeded: l'operazione di analisi ha avuto esito positivo. |

Quando il campo **stato** presenta il valore **succeeded** , la risposta JSON includerà i risultati relativi alla ricezione e al riconoscimento del testo. Il risultato degli ID è organizzato come un dizionario di valori di campo denominati, dove ogni valore contiene il testo estratto, il valore normalizzato, il rettangolo di delimitazione, la confidenza e gli elementi di parola corrispondenti. Il risultato del riconoscimento del testo è organizzato come una gerarchia di righe e parole, con testo, rettangolo di delimitazione e informazioni sulla confidenza.

![Risultati della ricezione di esempio](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Output JSON di esempio

Vedere l'esempio seguente di una risposta JSON riuscita: il `readResults` nodo contiene tutto il testo riconosciuto. Il testo è organizzato in base alla pagina, quindi alla riga, infine in base a singole parole. Il `documentResults` nodo contiene i valori ID individuati dal modello. In questo nodo sono inoltre disponibili coppie chiave/valore utili, ad esempio il nome, il cognome, il numero del documento e altro ancora.

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

- Provare i propri ID ed esempi nell' [interfaccia utente di esempio di riconoscimento moduli](https://fott-preview.azurewebsites.net/).
- Completare una [Guida introduttiva](quickstarts/client-library.md) per il riconoscimento dei moduli per iniziare a scrivere un'app di elaborazione di ID con il riconoscimento del modulo nel linguaggio di sviluppo preferito.

## <a name="see-also"></a>Vedi anche

* [**Informazioni su Riconoscimento modulo**](./overview.md)
* [**Documentazione di riferimento per l'API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
