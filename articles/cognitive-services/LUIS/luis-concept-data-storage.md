---
title: Archiviazione dati-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS archivia i dati crittografati in un archivio dati di Azure corrispondente alla regione specificata dalla chiave.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a58bcff4e39c4a4a907cd8567b47b074ff299bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "97008453"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Archiviazione e rimozione dei dati nei Servizi cognitivi di LUIS (Language Understanding)

LUIS archivia i dati crittografati in un archivio dati di Azure corrispondente all' [area](luis-reference-regions.md) specificata dalla chiave. 

* I dati utilizzati per il training del modello, ad esempio entità, Intent e espressioni, verranno salvati in LUIS per la durata dell'applicazione. Se un proprietario o un collaboratore Elimina l'app, questi dati verranno eliminati con esso. Se un'applicazione non è stata usata in 90 giorni, verrà eliminata. 

* Gli autori di applicazioni possono scegliere di [abilitare la registrazione](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning) per le espressioni inviate a un'applicazione pubblicata. Se abilitata, le espressioni verranno salvate per 30 giorni e potranno essere visualizzate dall'autore dell'applicazione. Se la registrazione non è abilitata al momento della pubblicazione dell'applicazione, questi dati non vengono archiviati.

## <a name="export-and-delete-app"></a>Esportare ed eliminare l'app
Gli utenti hanno il controllo completo sull'[esportazione](luis-how-to-start-new-app.md#export-app) e sull'[eliminazione](luis-how-to-start-new-app.md#delete-app) dell'app. 

## <a name="utterances"></a>Espressioni

Le espressioni possono essere archiviate in due posizioni diverse. 

* Durante **il processo di creazione**, le espressioni vengono create e archiviate nello scopo. Le espressioni negli Intent sono necessarie per un'app LUIS riuscita. Dopo la pubblicazione dell'app e la ricezione di query sull'endpoint, la QueryString della richiesta dell'endpoint `log=false` determina se l'espressione dell'endpoint è archiviata. Se l'endpoint è archiviato, diventa parte delle espressioni di formazione attive disponibili nella sezione **Build** del portale, nella sezione **Review endpoint enunciations** . 
* Quando si **esaminano le espressioni dell'endpoint** e si aggiunge un enunciato a un preventivo, l'espressione non viene più archiviata come parte delle espressioni di endpoint da rivedere. Viene aggiunto agli Intent dell'app. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Elimina le espressioni di esempio da un Intent

Eliminare le espressioni di esempio usate per il training di [LUIS](luis-reference-regions.md). Le espressioni di esempio eliminate dall'app LUIS vengono rimosse dal servizio Web LUIS e non sono più disponibili per l'esportazione.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Elimina le espressioni nella revisione dall'apprendimento attivo

È possibile eliminare le espressioni dall'elenco di espressioni utente suggerite da LUIS nella pagina **[Review endpoint utterances](luis-how-to-review-endpoint-utterances.md)** (Esamina espressioni endpoint). Le espressioni eliminate da questo elenco non vengono più suggerite, ma non vengono eliminate dai log.

Se non si desiderano espressioni di apprendimento attive, è possibile [disabilitare l'apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). La disabilitazione dell'apprendimento attivo Disabilita anche la registrazione.

### <a name="disable-logging-utterances"></a>Disabilitare le espressioni di registrazione
La [disabilitazione dell'apprendimento attivo Disabilita](luis-how-to-review-endpoint-utterances.md#disable-active-learning) la registrazione.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Eliminare un account
Se non si esegue la migrazione, è possibile eliminare l'account e tutte le app verranno eliminate insieme alle espressioni e ai log di esempio. I dati vengono conservati per 90 giorni prima che l'account e i dati vengano eliminati in modo permanente.

L'eliminazione dell'account può essere eseguita nella pagina **Settings** (Impostazioni). Selezionare il nome dell'account nella barra di spostamento in alto a destra per andare alla pagina **Settings** (Impostazioni).

## <a name="delete-an-authoring-resource"></a>Eliminare una risorsa di creazione
Se è stata [eseguita la migrazione a una risorsa di creazione](./luis-migration-authoring.md), l'eliminazione della risorsa stessa dal portale di Azure eliminerà tutte le applicazioni associate a tale risorsa, insieme alle espressioni e ai log di esempio. I dati vengono conservati per 90 giorni prima di essere eliminati in modo permanente.    

Per eliminare la risorsa, passare alla [portale di Azure](https://ms.portal.azure.com/#home) e selezionare la risorsa Luis authoring. Passare alla scheda **Panoramica** e fare clic sul pulsante **Elimina** nella parte superiore della pagina. Quindi verificare che la risorsa sia stata eliminata. 

## <a name="data-inactivity-as-an-expired-subscription"></a>Inattività dei dati come sottoscrizione scaduta
Ai fini della conservazione e dell'eliminazione dei dati, un'app LUIS inattiva potrebbe, a _discrezione di Microsoft_, essere considerata una sottoscrizione scaduta. Un'app è considerata inattiva se per gli ultimi 90 giorni soddisfa i criteri seguenti: 

* **Non** ha ricevuto chiamate.
* Non è stata modificata.
* Non è stata assegnata a essa alcuna chiave corrente.
* Nessun utente ha effettuato l'accesso a essa.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'esportazione e sull'eliminazione di un'app](luis-how-to-start-new-app.md)