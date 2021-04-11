---
title: Modelli personalizzati-riconoscimento moduli
titleSuffix: Azure Cognitive Services
description: "Informazioni sui concetti relativi ai modelli personalizzati dell'API di riconoscimento form: utilizzo e limiti."
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 9d339d02310fddc3e5fd463f7d37e253614dba17
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067589"
---
# <a name="form-recognizer-custom-models"></a>Modelli personalizzati del riconoscimento form

Il riconoscimento del modulo usa una tecnologia di Machine Learning avanzata per analizzare ed estrarre i dati da moduli e documenti. Un modello di riconoscimento form è una rappresentazione dei dati estratti usati come riferimento per l'analisi del contenuto specifico. Esistono due tipi di modelli di riconoscimento del modulo:

* **Modelli personalizzati**. I modelli personalizzati del riconoscimento form rappresentano i dati estratti da _moduli_ specifici dell'azienda. È necessario eseguire il training dei modelli personalizzati per analizzare i dati del modulo distinti.

* **Modelli predefiniti**. Il riconoscimento dei moduli attualmente supporta i modelli predefiniti per le _ricevute, i biglietti da visita_, le schede di identificazione e le _fatture_. I modelli predefiniti rilevano ed estraggono informazioni dalle immagini dei documenti e restituiscono i dati estratti in un output JSON strutturato.

## <a name="what-does-a-custom-model-do"></a>Che cosa fa un modello personalizzato?

Con il riconoscimento del modulo, è possibile eseguire il training di un modello che estrae informazioni dai moduli rilevanti per il caso d'uso. Per iniziare, sono necessari solo cinque esempi dello stesso tipo di modulo. Il training del modello personalizzato può essere eseguito con o senza set di impostazioni con etichetta.

## <a name="create-use-and-manage-your-custom-model"></a>Creazione, utilizzo e gestione del modello personalizzato

A un livello elevato, i passaggi per la compilazione, il training e l'utilizzo del modello personalizzato sono i seguenti:

> [!div class="nextstepaction"]
>[&#120783;. Assemblare il set di dati di training](build-training-data-set.md#custom-model-input-requirements)

La creazione di un modello personalizzato inizia con la definizione del set di dati di training. Per il set di dati di esempio sono necessari almeno cinque forme completate dello stesso tipo. Possono essere di tipi di file diversi e contenere sia testo che grafia. I form devono essere dello stesso tipo di documento e seguire i [requisiti di input](build-training-data-set.md#custom-model-input-requirements) per il riconoscimento del modulo.  

> [!div class="nextstepaction"]
> [&#120784;. Caricare il set di dati di training](build-training-data-set.md#upload-your-training-data)

È necessario caricare i dati di training in un contenitore di archiviazione BLOB di Azure. Se non si conosce la procedura per creare un account di archiviazione di Azure con un contenitore, *vedere* [Guida introduttiva di archiviazione di Azure per portale di Azure](../../storage/blobs/storage-quickstart-blobs-portal.md). Usare il piano tariffario gratuito (F0) per provare il servizio ed eseguire l'aggiornamento in un secondo momento a un livello a pagamento per la produzione.  

> [!div class="nextstepaction"]
>[&#120785;. Eseguire il training del modello personalizzato](quickstarts/client-library.md#train-a-custom-model)

È possibile eseguire il training del modello [senza](quickstarts/client-library.md#train-a-model-without-labels) o [con set di dati con](quickstarts/client-library.md#train-a-model-with-labels) etichetta. I set di dati senza etichetta si basano esclusivamente sull'API del layout per rilevare e identificare le informazioni chiave senza aggiungere input umano. I set di dati con etichetta si basano anche sull'API layout, ma è incluso un input umano supplementare, ad esempio le etichette specifiche e le posizioni dei campi. Per usare i dati con etichetta e senza etichetta, iniziare con almeno cinque forme completate dello stesso tipo per i dati di training con etichetta e quindi aggiungere i dati senza etichetta al set di dati richiesto.  

>[!div class="nextstepaction"]
>[&#120786;. Analizzare i documenti con il modello personalizzato](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

Testare il modello appena sottoposto a training usando un modulo che non faceva parte del set di dati di training. È possibile continuare a eseguire ulteriori training per migliorare le prestazioni del modello personalizzato.  

> [!div class="nextstepaction"]
>[&#120787;. Gestire i modelli personalizzati](quickstarts/client-library.md#manage-custom-models)

In qualsiasi momento, è possibile visualizzare un elenco di tutti i modelli personalizzati nella sottoscrizione, recuperare informazioni su un modello personalizzato specifico o eliminare un modello personalizzato dall'account.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla libreria client del modulo Recognizer, vedere la documentazione di riferimento per le API.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API riconoscimento form](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
>
