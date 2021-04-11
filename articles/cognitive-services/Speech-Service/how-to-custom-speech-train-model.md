---
title: Eseguire il training e la distribuzione di un modello di Riconoscimento vocale personalizzato-servizio vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire il training e distribuire modelli di Riconoscimento vocale personalizzato. Il training di un modello di sintesi vocale può migliorare l'accuratezza del riconoscimento per il modello Baseline Microsoft o per un modello personalizzato.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 4ba046b5461c3b734d54ad3694f0ceea2e5127c6
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387044"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Eseguire il training e distribuire un modello di Riconoscimento vocale personalizzato

In questo articolo si apprenderà come eseguire il training e la distribuzione di Riconoscimento vocale personalizzato modelli. Il training di un modello di sintesi vocale può migliorare l'accuratezza del riconoscimento per il modello Baseline Microsoft. Per eseguire il training di un modello, si utilizzano trascrizioni con etichetta umana e testo correlato. Questi set di dati, insieme ai dati audio caricati in precedenza, vengono usati per perfezionare ed eseguire il training del modello di riconoscimento vocale.

## <a name="use-training-to-resolve-accuracy-problems"></a>Usare il training per risolvere i problemi di accuratezza

Se si riscontrano problemi di riconoscimento con un modello di base, è possibile usare trascrizioni con etichetta umana e dati correlati per eseguire il training di un modello personalizzato e migliorare la precisione. Usare questa tabella per determinare il set di dati da usare per risolvere i problemi:

| Caso d'uso | Tipo di dati |
| -------- | --------- |
| Migliorare l'accuratezza del riconoscimento su vocabolario e grammatica specifici del settore, come la terminologia medica o il gergo IT | Testo correlato (frasi/espressioni) |
| Definire il form fonetico e visualizzato di una parola o di un termine con una pronuncia non standard, ad esempio nomi di prodotto o acronimi | Testo correlato (pronuncia) |
| Migliorare l'accuratezza del riconoscimento su stili di pronuncia, accenti o rumori di fondo specifici | Trascrizioni audio + con etichetta umana |

## <a name="train-and-evaluate-a-model"></a>Eseguire il training di un modello e valutarlo

Il primo passaggio per eseguire il training di un modello consiste nel caricare i dati di training. Per istruzioni dettagliate su come preparare le trascrizioni con etichetta umana e il testo correlato (espressioni e pronunce), vedere [preparare e testare i dati](./how-to-custom-speech-test-and-train.md) . Dopo aver caricato i dati di training, seguire queste istruzioni per avviare il training del modello:

1. Accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech). Se si prevede di eseguire il training di un modello con i set di impostazioni per la trascrizione audio e con etichetta umana, scegliere una sottoscrizione vocale in un' [area con hardware dedicato](custom-speech-overview.md#set-up-your-azure-account) per il training.
2. Passare a **riconoscimento vocale**  >  **riconoscimento vocale personalizzato**  >  **[nome del progetto]**  >  **formazione**.
3. Selezionare **Train Model**.
4. Assegnare un **nome** e una **Descrizione** al training.
5. Nell'elenco **scenario e modello di base** selezionare lo scenario più adatto al dominio. Se non si è certi dello scenario da scegliere, selezionare **generale**. Il modello di base è il punto di partenza per il training. Il modello più recente è in genere la scelta migliore.
6. Nella pagina **selezione dati di training** scegliere uno o più set di dati di testo correlati oppure i set di dati di trascrizione audio e con etichetta umana che si desidera utilizzare per il training.

> [!NOTE]
> Quando si esegue il training di un nuovo modello, iniziare con il testo correlato; il training con audio e la trascrizione con etichetta umana potrebbe richiedere molto più tempo **(fino a [diversi giorni](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)**).

> [!NOTE]
> Non tutti i modelli di base supportano il training con audio. Se un modello di base non la supporta, il servizio di riconoscimento vocale utilizzerà solo il testo delle trascrizioni e ignorerà l'audio. Per un elenco dei modelli di base che supportano il training con dati audio, vedere Supporto per le [lingue](language-support.md#speech-to-text) .

> [!NOTE]
> Nei casi in cui si modifica il modello di base utilizzato per il training e si dispone di audio nel set di dati di training, verificare *sempre* se il nuovo modello di base selezionato [supporta il training con dati audio](language-support.md#speech-to-text). Se il modello di base usato in precedenza non supporta il training con dati audio e il set di dati di training contiene audio, i tempi di training con il nuovo modello di base aumenteranno **drasticamente** e potranno passare da diverse ore a diversi giorni. Ciò vale soprattutto se la sottoscrizione al servizio vocale **non** si trova in un' [area con l'hardware dedicato per il](custom-speech-overview.md#set-up-your-azure-account) training.
>
> Se si affronta il problema descritto nel paragrafo precedente, è possibile ridurre rapidamente il tempo di training riducendo la quantità di audio nel set di dati o rimuovendo completamente il testo e lasciando solo il testo. La seconda opzione è consigliata se la sottoscrizione al servizio vocale **non** si trova in un' [area con l'hardware dedicato per il](custom-speech-overview.md#set-up-your-azure-account) training.

7. Al termine del training, è possibile eseguire test di accuratezza sul modello appena sottoposto a training. Questo passaggio è facoltativo.
8. Selezionare **Crea** per compilare il modello personalizzato.

Nella tabella **Training** viene visualizzata una nuova voce che corrisponde al nuovo modello. La tabella Visualizza anche lo stato: **elaborazione**, **esito positivo** o **non riuscito**.

Vedere le [procedure per](how-to-custom-speech-evaluate-data.md) valutare e migliorare riconoscimento vocale personalizzato accuratezza del modello. Se si sceglie di verificare l'accuratezza, è importante selezionare un set di dati acustico diverso da quello usato con il modello per ottenere un senso realistico delle prestazioni del modello.

> [!NOTE]
> Sia i modelli di base che i modelli personalizzati possono essere usati solo fino a una determinata data (vedere ciclo di vita di modelli [ed endpoint](./how-to-custom-speech-model-and-endpoint-lifecycle.md)). Speech studio Mostra questa data nella colonna **scadenza** per ogni modello ed endpoint. Dopo la richiesta di una data a un endpoint o la trascrizione in batch potrebbe avere esito negativo o eseguire il fallback al modello di base.
>
> Ripetere il training del modello usando il modello di base più recente per trarre vantaggio dai miglioramenti di accuratezza e per evitare che il modello scada.

## <a name="deploy-a-custom-model"></a>Distribuire un modello personalizzato

Al termine del caricamento e del controllo dei dati, della valutazione dell'accuratezza e del training di un modello personalizzato, è possibile distribuire un endpoint personalizzato da usare con le app, gli strumenti e i prodotti. 

Per creare un endpoint personalizzato, accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech). Selezionare **distribuzione** nel menu **riconoscimento vocale personalizzato** nella parte superiore della pagina. Se si tratta della prima esecuzione, si noterà che nella tabella non sono elencati endpoint. Dopo aver creato un endpoint, è possibile utilizzare questa pagina per tenere traccia di ogni endpoint distribuito.

Selezionare quindi **Aggiungi endpoint** e immettere un **nome** e una **Descrizione** per l'endpoint personalizzato. Selezionare quindi il modello personalizzato che si desidera associare all'endpoint.  È anche possibile abilitare la registrazione da questa pagina. La registrazione consente di monitorare il traffico dell'endpoint. Se la registrazione è disabilitata, il traffico non verrà archiviato.

![Screenshot che mostra la pagina nuovo endpoint.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Non dimenticare di accettare le condizioni per l'utilizzo e i dettagli relativi ai prezzi.

Quindi selezionare **Crea**. Questa azione consente di tornare alla pagina **distribuzione** . La tabella include ora una voce che corrisponde all'endpoint personalizzato. Lo stato dell'endpoint Mostra lo stato corrente. Possono essere necessari fino a 30 minuti per creare un'istanza di un nuovo endpoint usando i modelli personalizzati. Quando lo stato della distribuzione diventa **completato**, l'endpoint è pronto per l'utilizzo.

Dopo la distribuzione dell'endpoint, il nome dell'endpoint viene visualizzato come collegamento. Selezionare il collegamento per visualizzare informazioni specifiche per l'endpoint, ad esempio la chiave dell'endpoint, l'URL dell'endpoint e il codice di esempio. Prendere nota della data di scadenza e aggiornare il modello dell'endpoint prima di tale data per garantire il servizio senza interruzioni.

## <a name="view-logging-data"></a>Visualizzare i dati di registrazione

I dati di registrazione sono disponibili per l'esportazione se si passa alla pagina dell'endpoint in **distribuzioni**.
> [!NOTE]
>I dati di registrazione sono disponibili per 30 giorni nell'archiviazione di proprietà di Microsoft. Verrà rimosso in seguito. Se un account di archiviazione di proprietà del cliente è collegato alla sottoscrizione di servizi cognitivi, i dati di registrazione non verranno eliminati automaticamente.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come usare il modello personalizzato](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Preparare e testare i dati](./how-to-custom-speech-test-and-train.md)
- [Esaminare i dati](how-to-custom-speech-inspect-data.md)
- [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
