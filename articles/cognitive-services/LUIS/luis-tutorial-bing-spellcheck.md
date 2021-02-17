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
ms.openlocfilehash: ef9cb083c9bbe6eae5c34cd3799debde771231b6
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558217"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Correggere le parole errate con la risorsa Bing

L'API di stima V3 supporta ora l' [API controllo ortografico Bing](https://docs.microsoft.com/bing/search-apis/bing-spell-check/overview). Aggiungere il controllo ortografico all'applicazione includendo la chiave per la risorsa di ricerca Bing nell'intestazione delle richieste. È possibile usare una risorsa Bing esistente se ne è già proprietaria o [crearne una nuova](https://portal.azure.com/#create/Microsoft.BingSearch) per usare questa funzionalità. 

Esempio di output di stima per una query con errori di ortografia:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

Le correzioni all'ortografia vengono effettuate prima della stima dell'espressione utente LUIS. Nella risposta è possibile visualizzare tutte le modifiche apportate all'espressione originale, inclusa l'ortografia.

## <a name="create-bing-search-resource"></a>Crea risorsa Ricerca Bing

Per creare una risorsa Ricerca Bing nel portale di Azure, seguire queste istruzioni:

1. Accedere al [Portale di Azure](https://portal.azure.com).

2. Nell'angolo superiore sinistro selezionare **Crea una risorsa**.

3. Nella casella di ricerca immettere `Bing Search V7` e selezionare il servizio.

4. A destra viene visualizzato un pannello informativo contenente informazioni, tra cui le note legali. Selezionare **Crea** per avviare il processo di creazione della sottoscrizione.

> [!div class="mx-imgBorder"]
> ![Risorsa Controllo ortografico Bing API V7](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. Nella pannello successivo immettere le impostazioni del servizio. Attendere la fine del processo di creazione del servizio.

6. Dopo aver creato la risorsa, passare al pannello **chiavi ed endpoint** a sinistra. 

7. Copiare una delle chiavi da aggiungere all'intestazione della richiesta di stima. Sarà necessaria solo una delle due chiavi.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="enable-spell-check-from-ui"></a>Abilitare il controllo ortografico dall'interfaccia utente 
È possibile abilitare il controllo ortografico per la query di esempio tramite il [portale Luis](https://www.luis.ai). Selezionare **Gestisci** nella parte superiore della schermata e **risorse di Azure** nel percorso di spostamento a sinistra. Dopo aver associato una risorsa di stima all'applicazione, è possibile selezionare **modifica parametri query** nella parte inferiore della pagina e incollare la chiave di risorsa nel campo **Abilita controllo ortografico** .
    
   > [!div class="mx-imgBorder"]
   > ![Abilita controllo ortografico](./media/luis-tutorial-bing-spellcheck/spellcheck-query-params.png)


## <a name="adding-the-key-to-the-endpoint-url"></a>Aggiungere la chiave all'URL dell'endpoint
Per ogni query a cui si desidera applicare la correzione ortografica, per la query dell'endpoint è necessaria la chiave di risorsa del controllo ortografico Bing passata nel parametro di intestazione della query. Si può avere un chatbot che chiama LUIS o si può chiamare direttamente l'API dell'endpoint LUIS. Indipendentemente dal modo in cui viene chiamato l'endpoint, ogni chiamata deve includere le informazioni necessarie nella richiesta dell'intestazione affinché le correzioni ortografiche funzionino correttamente. È necessario impostare il valore con **MKT-Bing-spell-check-Key** sul valore della chiave.

|Chiave di intestazione|Valore intestazione|
|--|--|
|`mkt-bing-spell-check-key`|Chiavi trovate nel pannello **chiavi ed endpoint** della risorsa|

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
