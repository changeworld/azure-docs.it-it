---
title: Modelli di intelligenza artificiale di Azure Percept
description: Altre informazioni sui modelli di intelligenza artificiale disponibili per la distribuzione e la prototipazione
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: f10a330de52d40f728cd628a1d7cb83b54ad1ff6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557361"
---
# <a name="azure-percept-ai-models"></a>Modelli di intelligenza artificiale di Azure Percept

Azure Percept consente di sviluppare e distribuire modelli di intelligenza artificiale direttamente in [Azure PERCEPT dk](./overview-azure-percept-dk.md) da [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). La distribuzione del modello usa l'hub e la [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview)di [Azure](https://azure.microsoft.com/services/iot-hub/) .

## <a name="sample-ai-models"></a>Modelli di intelligenza artificiale di esempio

Azure Percept Studio contiene modelli di esempio per le applicazioni seguenti:

- rilevamento persone
- rilevamento del veicolo
- rilevamento oggetti generali
- prodotti-rilevamento su scaffale

Con i modelli con training preliminare, non è necessaria alcuna raccolta di dati di codifica o di training. È sufficiente [distribuire il modello desiderato](./how-to-deploy-model.md) in Azure Percept DK dal portale e aprire il [flusso video](./how-to-view-video-stream.md) di DevKit per vedere l'inferenza del modello in azione. È possibile accedere ai dati di [telemetria di inferenza del modello](./how-to-view-telemetry.md) anche tramite lo strumento [Azure Internet Explorer](https://github.com/Azure/azure-iot-explorer/releases) .

## <a name="reference-solutions"></a>Soluzioni di riferimento

È disponibile anche una soluzione di riferimento per il [conteggio delle persone](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) . Questa soluzione di riferimento è un'applicazione open source per intelligenza artificiale che fornisce le persone basate su Edge che contano eventi di ingresso/uscita della zona definiti dall'utente. L'output di video e intelligenza artificiale dal dispositivo perimetrale locale è uscita [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/), con l'interfaccia utente in esecuzione come sito Web di Azure. L'inferenza di intelligenza artificiale viene fornita da un modello di intelligenza artificiale open source per il rilevamento degli utenti.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Gif della soluzione predefinita di analisi spaziale.":::

## <a name="custom-no-code-solutions"></a>Soluzioni senza codice personalizzate

Grazie ad Azure Percept studio, è possibile sviluppare soluzioni personalizzate per la [visione](./tutorial-nocode-vision.md) e la [sintesi vocale](./tutorial-no-code-speech.md) , senza necessità di scrivere codice.

Per le soluzioni di visione artificiale personalizzate, sono disponibili sia il rilevamento degli oggetti che i modelli di intelligenza artificiale. È sufficiente caricare e contrassegnare le immagini di training, che possono essere eseguite direttamente con il modello di Azure Percept Vision SoM di Azure Percept DK, se lo si desidera. Il training e la valutazione del modello vengono eseguiti facilmente in [visione personalizzata](https://www.customvision.ai/), che fa parte dei [Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/#overview).

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

Per le soluzioni di riconoscimento vocale personalizzato, i modelli di Assistente vocale sono attualmente disponibili per le applicazioni seguenti:

- Ospitalità: Room Hotel attrezzata con Smart Device controllati da Voice.
- Healthcare: funzionalità di assistenza attrezzata con Smart Device controllati da Voice.
- Inventario: Hub di inventario dotato di Smart Device controllati dalla voce.
- Automotive: Hub automobilistico dotato di Smart Device controllati dalla voce.

Le parole chiave e i comandi degli assistenti vocali predefiniti sono disponibili direttamente tramite il portale. Le parole chiave e i comandi personalizzati possono essere creati e sottoposti a training in [speech studio](https://speech.microsoft.com/), che fa parte anche dei servizi cognitivi di Azure.

## <a name="advanced-development"></a>Sviluppo avanzato

Per informazioni aggiornate, esercitazioni ed esempi relativi ad esempio, vedere [Azure PERCEPT dk Advanced Development GitHub](https://github.com/microsoft/azure-percept-advanced-development) .

- Distribuzione di un modello di intelligenza artificiale personalizzato in Azure Percept DK
- Aggiornamento di un modello supportato con l'apprendimento del trasferimento
- E altre informazioni
