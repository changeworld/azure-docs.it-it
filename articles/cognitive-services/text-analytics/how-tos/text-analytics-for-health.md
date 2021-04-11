---
title: Come usare Analisi del testo per l'integrità
titleSuffix: Azure Cognitive Services
description: Informazioni su come estrarre ed etichettare le informazioni mediche da testo clinico non strutturato con Analisi del testo per l'integrità.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 80a943d235783852f57832363b5af8048f010575
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599434"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Procedura: usare Analisi del testo per l'integrità (anteprima)

> [!IMPORTANT] 
> Analisi del testo per l'integrità è una funzionalità di anteprima fornita "così com'è" e "con tutti gli errori". Di conseguenza, **analisi del testo per Health (Preview) non devono essere implementate o distribuite in alcun uso in produzione.** Analisi del testo per l'integrità non è intenzionale o reso disponibile per l'utilizzo come dispositivo medico, supporto clinico, strumento di diagnostica o altra tecnologia da utilizzare per la diagnosi, la cura, la mitigazione, il trattamento o la prevenzione di patologie o altre condizioni, né la concessione di alcuna licenza o diritto da parte di Microsoft di utilizzare questa funzionalità per tali scopi. Questa funzionalità non è progettata o progettata per essere implementata o distribuita come sostituto di consigli medici professionali o di opinione sanitaria, diagnosi, trattamento o giudizio clinico di un professionista sanitario e non deve essere usato come tali. Il cliente è esclusivamente responsabile dell'utilizzo di Analisi del testo per l'integrità. Microsoft non garantisce che Analisi del testo per l'integrità o i materiali forniti in relazione alla funzionalità saranno sufficienti per qualsiasi scopo medico o per soddisfare i requisiti sanitari o medici di qualsiasi persona. 


Analisi del testo per l'integrità è una funzionalità del servizio API Analisi del testo che estrae ed etichetta le informazioni mediche rilevanti da testi non strutturati, come le note del medico, i riepiloghi di scaricamento, i documenti clinici e i record di integrità elettronici.  È possibile utilizzare questo servizio in due modi: 

* [API basata sul Web (asincrona)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Un contenitore Docker (sincrono)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Funzionalità

Analisi del testo per l'integrità esegue il riconoscimento delle entità denominate (NER), l'estrazione della relazione, la negazione dell'entità e il collegamento di entità sul testo in lingua inglese per individuare informazioni dettagliate in testo clinico e biomedico non strutturato.

### <a name="named-entity-recognition"></a>[Riconoscimento di entità denominate](#tab/ner)

Il riconoscimento delle entità denominato rileva le parole e le frasi indicate nel testo non strutturato che possono essere associate a uno o più tipi semantici, ad esempio la diagnosi, il nome del farmaco, il sintomo/segno o l'età.

> [!div class="mx-imgBorder"]
> ![NER integrità](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Estrazione di relazioni](#tab/relation-extraction)

L'estrazione di relazioni identifica connessioni significative tra i concetti indicati nel testo. Ad esempio, una relazione "ora della condizione" viene rilevata associando il nome di una condizione a un'ora o tra un'abbreviazione e la descrizione completa.  

> [!div class="mx-imgBorder"]
> ![Integrità RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Collegamento delle entità](#tab/entity-linking)

Il collegamento di entità ambiguità entità distinte associando entità denominate indicate in testo ai concetti trovati in un database di concetti predefiniti, incluso Unified Medical Language System (UMLS). Ai concetti medici viene assegnata anche la denominazione preferita, come forma aggiuntiva di normalizzazione.

> [!div class="mx-imgBorder"]
> ![EL di stato](../media/ta-for-health/health-entity-linking.png)

Analisi del testo per l'integrità supporta il collegamento all'integrità e ai vocabolari biomedici disponibili nella Knowledge base del Metathesaurus[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)(Unified Medical Language System).

### <a name="assertion-detection"></a>[Rilevamento dell'asserzione](#tab/assertion-detection) 

Il significato del contenuto medico è molto influenzato dai modificatori, ad esempio asserzioni negative o condizionali, che possono avere implicazioni critiche se non sono rappresentate. Analisi del testo per Health supporta tre categorie di rilevamento dell'asserzione per le entità nel testo: 

* certezza
* condizionali
* associazione

> [!div class="mx-imgBorder"]
> ![Stato negativo](../media/ta-for-health/assertions.png)

---

Vedere le [categorie di entità](../named-entity-types.md?tabs=health) restituite da analisi del testo per l'integrità per un elenco completo delle entità supportate. Per informazioni sui punteggi di confidenza, vedere la [Nota sulla trasparenza analisi del testo](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

### <a name="supported-languages-and-regions"></a>Lingue e aree supportate

Analisi del testo per l'integrità supporta solo documenti in lingua inglese. 

Il Analisi del testo per l'API Web ospitata sull'integrità è attualmente disponibile solo in queste aree: Stati Uniti occidentali 2, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale ed Europa occidentale.

## <a name="request-access-to-the-public-preview"></a>Richiedere l'accesso all'anteprima pubblica

Compilare e inviare il [modulo di richiesta di servizi cognitivi](https://aka.ms/csgate) per richiedere l'accesso alla analisi del testo per l'anteprima pubblica dell'integrità. Non verranno addebitate Analisi del testo per l'utilizzo dell'integrità. 

Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo aver inviato il modulo, il team di servizi cognitivi di Azure lo esaminerà e invierà un messaggio di posta elettronica con una decisione.

> [!IMPORTANT]
> * Nel modulo è necessario usare un indirizzo di posta elettronica associato a un ID sottoscrizione di Azure.
> * La risorsa di Azure usata deve essere stata creata con l'ID sottoscrizione di Azure approvato. 
> * Controllare la posta elettronica (cartella posta in arrivo e cartelle indesiderate) per gli aggiornamenti sullo stato dell'applicazione da Microsoft.

## <a name="using-the-docker-container"></a>Uso del contenitore Docker 

Per eseguire il Analisi del testo per il contenitore di integrità nel proprio ambiente, seguire queste [istruzioni per scaricare e installare il contenitore](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Uso della libreria client

La versione provvisoria più recente della libreria client di Analisi del testo consente di chiamare Analisi del testo per l'integrità tramite un oggetto client. Fare riferimento alla documentazione di riferimento e vedere gli esempi su GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Invio di una richiesta all'API REST 

### <a name="preparation"></a>Preparazione

Analisi del testo per l'integrità produce un risultato di qualità superiore quando si assegnano più piccole quantità di testo da utilizzare. Questo è contrario ad alcune delle altre Analisi del testo funzionalità, ad esempio l'estrazione di frasi chiave, che garantisce prestazioni migliori in blocchi di testo di dimensioni maggiori. Per ottenere risultati ottimali da queste operazioni, provare a ristrutturare gli input di conseguenza.

È necessario disporre di documenti JSON nel formato seguente: ID, testo e lingua. 

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento. Per informazioni sul numero massimo di documenti consentiti in una raccolta, vedere l'articolo sui [limiti per i dati](../concepts/data-limits.md?tabs=version-3) in Concetti. La raccolta viene inviata nel corpo della richiesta.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Strutturare la richiesta API per l'API Web asincrona ospitata

Sia per il contenitore che per l'API Web ospitata, è necessario creare una richiesta POST. Per creare rapidamente e inviare una richiesta POST all'API Web ospitata nella propria area geografica, è possibile [usare post](text-analytics-how-to-call-api.md), un comando curl o la [analisi del testo](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) **console test API** . 

> [!NOTE]
> Gli `/analyze` endpoint e asincroni `/health` sono disponibili solo nelle aree seguenti: Stati Uniti occidentali 2, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale ed Europa occidentale.  Per eseguire le richieste con esito positivo a questi endpoint, assicurarsi che la risorsa venga creata in una di queste aree.

Di seguito è riportato un esempio di un file JSON collegato alla Analisi del testo per il corpo POST della richiesta dell'API di integrità:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Risposta API Web asincrona ospitata 

Poiché questa richiesta POST viene utilizzata per inviare un processo per l'operazione asincrona, non è presente alcun testo nell'oggetto Response.  Tuttavia, per eseguire una richiesta GET per verificare lo stato del processo e l'output, è necessario il valore della chiave Operation-location nelle intestazioni di risposta.  Di seguito è riportato un esempio del valore della chiave Operation-location nell'intestazione Response della richiesta POST:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/health/jobs/<jobID>`

Per controllare lo stato del processo, effettuare una richiesta GET all'URL nel valore dell'intestazione Operation-location KEY della risposta POST.  Per riflettere lo stato di un processo vengono usati gli Stati seguenti: `NotStarted` , `running` , `succeeded` , `failed` , `rejected` , `cancelling` e `cancelled` .  

È possibile annullare un processo con uno `NotStarted` `running` stato o con una chiamata http delete allo stesso URL della richiesta GET.  Altre informazioni sulla chiamata DELETE sono disponibili nella [analisi del testo per informazioni di riferimento sull'API ospitata in integrità](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Di seguito è riportato un esempio della risposta di una richiesta GET.  L'output è disponibile per il recupero finché non `expirationDateTime` viene superato il (24 ore dal momento della creazione del processo) dopo il quale l'output viene eliminato.

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-03-11T05:43:37Z",
    "createdDateTime": "2021-03-11T05:42:32Z",
    "expirationDateTime": "2021-03-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.94
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-03-01"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Strutturare la richiesta API per il contenitore

Per inviare [](text-analytics-how-to-call-api.md) una query al contenitore distribuito, sostituendo la `serverURL` variabile con il valore appropriato, è possibile usare il metodo post o la richiesta curl di esempio riportata di seguito.  Si noti che la versione dell'API nell'URL per il contenitore è diversa da quella dell'API ospitata.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Il codice JSON seguente è un esempio di file JSON collegato alla Analisi del testo per il corpo POST della richiesta dell'API di integrità:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Corpo della risposta del contenitore

Il codice JSON seguente è un esempio di Analisi del testo per il corpo della risposta dell'API di integrità dalla chiamata sincrona in contenitori:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>Output dell'asserzione

Analisi del testo per Health restituisce i modificatori di asserzione, che sono attributi informativi assegnati ai concetti medici che forniscono una maggiore comprensione del contesto dei concetti all'interno del testo. Questi modificatori sono divisi in tre categorie, ognuna delle quali si concentra su un aspetto diverso e contiene un set di valori che si escludono a vicenda. A ciascuna entità viene assegnato un solo valore per categoria. Il valore più comune per ogni categoria è il valore predefinito. La risposta di output del servizio contiene solo modificatori di asserzione diversi dal valore predefinito.

**Certezza**  : vengono fornite informazioni relative alla presenza (presente e assente) del concetto e al modo in cui il testo è relativo alla presenza (definito rispetto al possibile).
*   **Positivo** [predefinito]: il concetto esiste o è stato eseguito.
* **Negativo**: il concetto non esiste adesso o non è mai stato eseguito.
* **Positive_Possible**: è probabile che il concetto esista, ma vi sono alcune incertezze.
* **Negative_Possible**: l'esistenza del concetto è improbabile, ma vi sono alcune incertezze.
* **Neutral_Possible**: il concetto può esistere o meno senza alcuna tendenza a entrambi i lati.

**Condizionalità** : fornisce informazioni sul fatto che l'esistenza di un concetto dipenda da determinate condizioni. 
*   **None** [impostazione predefinita]: il concetto è un fatto e non ipotetico e non dipende da determinate condizioni.
*   **Ipotetico**: il concetto può svilupparsi o verificarsi in futuro.
*   **Condizionale**: il concetto esiste o si verifica solo in determinate condizioni.

**Association** : descrive se il concetto è associato all'oggetto del testo o a un altro utente.
*   **Subject** [default]: il concetto è associato all'oggetto del testo, in genere il paziente.
*   **Someone_Else**: il concetto è associato a un utente che non è l'oggetto del testo.


Il rilevamento delle asserzioni rappresenta le entità negate come valore negativo per la categoria di sicurezza, ad esempio:

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty&quot;: &quot;negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id&quot;: &quot;C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id&quot;: &quot;0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>Output estrazione relazione

Analisi del testo per l'integrità riconosce le relazioni tra i diversi concetti, incluse le relazioni tra l'attributo e l'entità, ad esempio la direzione della struttura del corpo, il dosaggio dei farmaci e tra le entità, ad esempio il rilevamento delle abbreviazioni.

**ABBREVIAZIONE**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

> [!NOTE]
> * Le relazioni che fanno riferimento a una condizione possono fare riferimento al tipo di entità diagnosi o al tipo di entità SYMPTOM_OR_SIGN.
> * Le relazioni che fanno riferimento a un farmaco possono fare riferimento al tipo di entità MEDICATION_NAME o al tipo di entità MEDICATION_CLASS.
> * Le relazioni che fanno riferimento a TIME possono fare riferimento al tipo di entità TIME o DATE.

L'output dell'estrazione della relazione contiene riferimenti a URI e ruoli assegnati delle entità del tipo di relazione. Ad esempio:

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role&quot;: &quot;Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role&quot;: &quot;Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role&quot;: &quot;Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role&quot;: &quot;Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Categorie di entità denominate](../named-entity-types.md)
* [Novità](../whats-new.md)
