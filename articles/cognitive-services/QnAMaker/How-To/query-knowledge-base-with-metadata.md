---
title: Filtrare in base al contesto usando i metadati
titleSuffix: Azure Cognitive Services
description: QnA Maker filtra le coppie QnA in base ai metadati.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103022102"
---
# <a name="filter-responses-with-metadata"></a>Filtrare le risposte con i metadati

QnA Maker consente di aggiungere metadati, sotto forma di coppie chiave-valore, alle coppie di domande e risposte. È quindi possibile usare queste informazioni per filtrare i risultati per le query utente e per archiviare informazioni aggiuntive che possono essere usate nelle conversazioni di completamento.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Archiviare domande e risposte con un'entità QnA

È importante comprendere in che modo QnA Maker archivia i dati relativi a domande e risposte. La figura seguente mostra un'entità di tipo Domande e risposte:

![Illustrazione di un'entità QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Ogni entità di tipo Domande e risposte ha un ID univoco e persistente. È possibile usare l'ID per apportare aggiornamenti a una particolare entità QnA.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Usare i metadati per filtrare le risposte in base ai tag dei metadati personalizzati

L'aggiunta di metadati consente di filtrare le risposte in base a questi tag dei metadati. Aggiungere la colonna metadati dal menu **Opzioni di visualizzazione** . Aggiungere metadati alla Knowledge base selezionando l'icona dei metadati **+** per aggiungere una coppia di metadati. Questa coppia è costituita da una chiave e un valore.

![Screenshot dell'aggiunta di metadati](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrare i risultati con strictFilters per i tag dei metadati

Si consideri la domanda utente "quando l'hotel si chiude?", dove lo scopo è implicito per il ristorante "Paradise".

Poiché i risultati sono necessari solo per il ristorante "Paradise", è possibile impostare un filtro nella chiamata GenerateAnswer sui metadati "nome ristorante". Nell'esempio seguente viene illustrato quanto segue:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>Logical e per impostazione predefinita

Per combinare più filtri di metadati nella query, aggiungere i filtri di metadati aggiuntivi alla matrice della `strictFilters` Proprietà. Per impostazione predefinita, i valori vengono combinati in modo logico (e). Una combinazione logica richiede che tutti i filtri corrispondano alle coppie QnA in modo che la coppia venga restituita nella risposta.

Equivale all'utilizzo della `strictFiltersCompoundOperationType` proprietà con il valore di `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>OR logico con la proprietà strictFiltersCompoundOperationType

Quando si combinano diversi filtri di metadati, se si è interessati solo a uno o alcuni dei filtri corrispondenti, utilizzare la `strictFiltersCompoundOperationType` proprietà con il valore di `OR` .

Ciò consente alla Knowledge base di restituire le risposte quando qualsiasi filtro corrisponde, ma non restituirà risposte senza metadati.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Esempi di metadati nelle guide introduttive

Altre informazioni sui metadati sono disponibili nella Guida introduttiva del portale QnA Maker per i metadati:
* [Creazione e modifica - aggiungere metadati a una coppia di domanda e risposta](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Eseguire query sulle previsioni - filtrare le risposte in base ai metadati](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usare i risultati di domande e risposte per gestire il contesto di conversazione

La risposta a GenerateAnswer contiene le informazioni sui metadati corrispondenti della coppia di domande e risposte corrispondenti. È possibile utilizzare queste informazioni nell'applicazione client per archiviare il contesto della conversazione precedente da utilizzare nelle conversazioni successive.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizza la Knowledge base](../How-to/get-analytics-knowledge-base.md)
