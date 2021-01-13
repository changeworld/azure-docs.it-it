---
title: Riconoscimento moduli
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi all'analisi dei biglietti aziendali con il modulo API Recognizer-uso e limiti.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 1fd4279cd35e54e2e04f88973c4a825218a75142
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131127"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Modello per il riconoscimento delle schede business predefinite 

Il sistema di riconoscimento dei moduli di Azure è in grado di analizzare ed estrarre le informazioni di contatto dai biglietti da visita usando il modello predefinito di schede business. Combina potenti funzionalità di riconoscimento ottico dei caratteri (OCR) con il modello di informazioni sui biglietti da visita per estrarre le informazioni chiave dai biglietti da visita in inglese. Estrae le informazioni di contatto personali, il nome della società, il titolo del processo e altro ancora. L'API del business card predefinita è disponibile pubblicamente nel form Recognizer v 2.1 Preview. 

## <a name="what-does-the-business-card-service-do"></a>Che cosa fa il servizio business card?

L'API del business card predefinita estrae i campi chiave dai biglietti da visita e li restituisce in una risposta JSON organizzata.

![Immagine dell'elemento Contoso dall'output di FOTT + JSON](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>Campi estratti:

|Nome| Type | Descrizione | Testo | 
|:-----|:----|:----|:----|
| ContactNames | matrice di oggetti | Nome del contatto Estratto dal biglietto da lavoro | [{"FirstName": "John", "LastName": "Doe"}] |
| FirstName | string | Primo (dato) nome del contatto | "John" | 
| LastName | string | Cognome (famiglia) del contatto |   "Doe" | 
| CompanyName | matrice di stringhe | Nome della società Estratto dal biglietto da lavoro | ["Contoso"] | 
| Departments | matrice di stringhe | Reparto o organizzazione del contatto | ["R&D"] | 
| JobTitles | matrice di stringhe | Titolo del processo elencato del contatto | ["Ingegnere del software"] | 
| Messaggi di posta elettronica | matrice di stringhe | Posta elettronica di contatto estratta dal biglietto da lavoro | ["johndoe@contoso.com"] | 
| Siti Web | matrice di stringhe | Sito Web Estratto dal biglietto da lavoro | ["https://www.contoso.com"] | 
| Indirizzi | matrice di stringhe | Indirizzo Estratto dal biglietto da lavoro | ["123 via principale, Redmond, WA 98052"] | 
| Cellulari | matrice dei numeri di telefono | Numero di telefono cellulare Estratto dal biglietto da lavoro | ["+ 19876543210"] |
| Fax | matrice dei numeri di telefono | Numero di telefono del fax Estratto dal biglietto da lavoro | ["+ 19876543211"] |
| WorkPhones | matrice dei numeri di telefono | Numero di telefono dell'ufficio Estratto dal biglietto da lavoro | ["+ 19876543231"] |
| OtherPhones    | matrice dei numeri di telefono | Altro numero di telefono Estratto dal biglietto da lavoro | ["+ 19876543233"] |


L'API del business card può restituire anche tutto il testo riconosciuto dal biglietto da business. Questo output OCR è incluso nella risposta JSON.  

### <a name="input-requirements"></a>Requisiti relativi all'input 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>Operazione Analyze business card

La [scheda analizza business](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync) acquisisce un'immagine o un PDF di un biglietto da lavoro come input ed estrae i valori di interesse. La chiamata restituisce un campo di intestazione della risposta denominato `Operation-Location` . Il `Operation-Location` valore è un URL che contiene l'ID risultato da usare nel passaggio successivo.

|Intestazione risposta| URL risultato |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>Operazione Get Analyze result business card

Il secondo passaggio consiste nel chiamare l'operazione [Get Analyze business card result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult) . Questa operazione accetta come input l'ID del risultato creato dall'operazione analizza business card. Restituisce una risposta JSON che contiene un campo di **stato** con i valori possibili seguenti. Questa operazione viene chiamata in modo iterativo fino a quando non viene restituita con il valore **succeeded** . Utilizzare un intervallo da 3 a 5 secondi per evitare il superamento della frequenza di richieste al secondo (RPS).

|Campo| Type | Valori possibili |
|:-----|:----:|:----|
|status | string | notStarted: l'operazione di analisi non è stata avviata.<br /><br />Running: l'operazione di analisi è in corso.<br /><br />non riuscito: l'operazione di analisi non è riuscita.<br /><br />Succeeded: l'operazione di analisi ha avuto esito positivo.|

Quando il campo **stato** presenta il valore **succeeded** , la risposta JSON includerà i risultati relativi a business card e al riconoscimento del testo facoltativo, se richiesto. Il risultato della comprensione del business card è organizzato come un dizionario di valori di campo denominati, dove ogni valore contiene il testo estratto, il valore normalizzato, il rettangolo di delimitazione, la confidenza e gli elementi di parola corrispondenti. Il risultato del riconoscimento del testo è organizzato come una gerarchia di righe e parole, con testo, rettangolo di delimitazione e informazioni sulla confidenza.

![esempio di output della scheda di business](./media/business-card-results.png)

### <a name="sample-json-output"></a>Output JSON di esempio

Vedere l'esempio seguente di una risposta JSON riuscita: il nodo "readResults" contiene tutto il testo riconosciuto. Il testo è organizzato in base alla pagina, quindi alla riga, infine in base a singole parole. Il nodo "documentResults" contiene i valori specifici della scheda business individuati dal modello. Qui è possibile trovare informazioni utili sul contatto, ad esempio il nome, il cognome, il nome della società e altro ancora.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Seguire la Guida introduttiva di [avvio rapido](./QuickStarts/client-library.md) per implementare l'estrazione dei dati delle schede business con Python e l'API REST.

## <a name="customer-scenarios"></a>Scenari utente  

I dati estratti con l'API Business Card possono essere utilizzati per eseguire diverse attività. L'estrazione di queste informazioni di contatto consente di risparmiare automaticamente tempo per i ruoli del client. Di seguito sono riportati alcuni esempi di ciò che i clienti hanno ottenuto con l'API dei biglietti da visita:

* Estrai le informazioni di contatto dai biglietti da visita e crea rapidamente contatti telefonici. 
* Eseguire l'integrazione con CRM per creare automaticamente il contatto utilizzando le immagini dei biglietti aziendali. 
* Tenere traccia dei lead di vendita.  
* Estrai le informazioni di contatto in blocco dalle immagini dei biglietti aziendali esistenti. 

L'API per i biglietti da business è anche la [funzionalità di elaborazione delle schede business](/ai-builder/prebuilt-business-card)per i Builder.

## <a name="next-steps"></a>Passaggi successivi

- Seguire la [Guida introduttiva](./quickstarts/client-library.md) per iniziare a riconoscere i biglietti da visita.

## <a name="see-also"></a>Vedere anche

* [Informazioni su Riconoscimento modulo](./overview.md)
* [Documentazione di riferimento per l'API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
