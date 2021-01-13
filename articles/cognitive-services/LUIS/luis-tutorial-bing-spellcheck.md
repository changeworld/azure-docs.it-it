---
title: Correggi parole errate-LUIS
titleSuffix: Azure Cognitive Services
description: È possibile correggere le parole in espressioni errate aggiungendo API Controllo ortografico Bing v7 alle query di endpoint LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: f416fe8ef4f6e89d07e6065d4c9435642d9bacb9
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179640"
---
# <a name="correct-misspelled-words-with-bing-search-resource"></a>Correggere le parole errate con Ricerca Bing risorsa

È possibile integrare l'app LUIS con [ricerca Bing](https://ms.portal.azure.com/#create/Microsoft.BingSearch) per correggere parole con errori di ortografia nelle espressioni prima che Luis predichi il punteggio e le entità dell'espressione.

## <a name="create-endpoint-key"></a>Creare una chiave endpoint

Per creare una risorsa Ricerca Bing nel portale di Azure, seguire queste istruzioni:

1. Accedere al [Portale di Azure](https://portal.azure.com).

2. Nell'angolo superiore sinistro selezionare **Crea una risorsa**.

3. Nella casella di ricerca immettere `Bing Search V7` e selezionare il servizio.

4. A destra viene visualizzato un pannello informativo contenente informazioni, tra cui le note legali. Selezionare **Crea** per avviare il processo di creazione della sottoscrizione.

    :::image type="content" source="./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png" alt-text="Risorsa Controllo ortografico Bing API V7":::

5. Nella pannello successivo immettere le impostazioni del servizio. Attendere la fine del processo di creazione del servizio.

6. Dopo aver creato la risorsa, passare al pannello **chiavi ed endpoint** a sinistra. 

7. Copiare una delle chiavi da aggiungere all'intestazione della richiesta di stima. Sarà necessaria solo una delle due chiavi.

8. Aggiungere la chiave a `mkt-bing-spell-check-key` nell'intestazione della richiesta di stima.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Aggiungere la chiave all'URL dell'endpoint
Per ogni query a cui si desidera applicare la correzione ortografica, per la query dell'endpoint è necessaria la chiave di risorsa del controllo ortografico Bing passata nel parametro di intestazione della query. Si può avere un chatbot che chiama LUIS o si può chiamare direttamente l'API dell'endpoint LUIS. Indipendentemente dal modo in cui viene chiamato l'endpoint, ogni chiamata deve includere le informazioni necessarie nella richiesta dell'intestazione affinché le correzioni ortografiche funzionino correttamente. È necessario impostare il valore con **MKT-Bing-spell-check-Key** sul valore della chiave.


## <a name="send-misspelled-utterance-to-luis"></a>Inviare un’espressione con errori di ortografia a LUIS
1. Aggiungere un'espressione con errori di ortografia nella query di stima che verrà inviata, ad esempio "quanto è la montagna?". In inglese, `mountain` con una `n` è la versione corretta.

2. LUIS risponde con un risultato JSON per `How far is the mountain?`. Se l’API Controllo ortografico Bing v7 rileva un errore di ortografia, il campo `query` nella risposta JSON dell'applicazione LUIS contiene la query originale e il campo `alteredQuery` contiene la query corretta inviata a LUIS.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorare errori di ortografia

Se non si vuole usare il servizio Ricerca Bing API V7, è necessario aggiungere l'ortografia corretta e non corretta.

Sono disponibili due soluzioni:

* Etichettare espressioni di esempio con tutte le ortografie diverse in modo che LUIS possa apprendere l'ortografia corretta e gli errori di digitazione. Questa opzione richiede un maggiore sforzo di etichettatura rispetto all'uso di un correttore ortografico.
* Creare un elenco di frasi con tutte le varianti della parola. Con questa soluzione non è necessario etichettare le varianti di parola nelle espressioni di esempio.


> [!div class="nextstepaction"]
> [Altre informazioni sulle espressioni di esempio](./luis-how-to-add-entities.md)
