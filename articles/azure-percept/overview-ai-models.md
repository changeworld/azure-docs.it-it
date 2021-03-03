---
title: Modelli di intelligenza artificiale di Azure Percept
description: Altre informazioni sui modelli di intelligenza artificiale disponibili per la distribuzione e la prototipazione
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: e61fb00b4ddd309124c11d1749f448c742d660d0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663185"
---
# <a name="azure-percept-ai-models"></a>Modelli di intelligenza artificiale di Azure Percept

Azure Percept consente di sviluppare e distribuire modelli di intelligenza artificiale direttamente in Azure Percept DK da [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). La distribuzione del modello usa l'hub e la [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview)di [Azure](https://azure.microsoft.com/services/iot-hub/) .

## <a name="sample-ai-models"></a>Modelli di intelligenza artificiale di esempio

Azure Percept Studio contiene modelli di esempio per le applicazioni seguenti:

- rilevamento volti
- rilevamento persone
- rilevamento del veicolo
- rilevamento oggetti generali
- prodotti-rilevamento su scaffale

Con i modelli con training preliminare, non è necessaria alcuna raccolta di dati di codifica o di training. È sufficiente distribuire il modello desiderato in Azure Percept DK dal portale e aprire il flusso video di DevKit per vedere l'inferenza del modello in azione. È possibile accedere ai dati di telemetria di inferenza del modello anche tramite lo strumento [Azure Internet Explorer](https://github.com/Azure/azure-iot-explorer/releases) .

## <a name="pre-built-solutions"></a>Soluzioni predefinite

È disponibile anche una [soluzione predefinita di analisi spaziale per il rilevamento di persone](https://github.com/george-moore/Santa-Cruz-AI-App) . La soluzione predefinita è un'applicazione open source per intelligenza artificiale che fornisce il conteggio di persone basate su Edge con eventi di ingresso/uscita della zona definiti dall'utente. L'output di video e intelligenza artificiale dal dispositivo perimetrale locale è uscita [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/), con l'interfaccia utente in esecuzione come sito Web di Azure. L'inferenza di intelligenza artificiale viene fornita da un modello di intelligenza artificiale open source per il rilevamento degli utenti.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Gif della soluzione predefinita di analisi spaziale.":::

## <a name="custom-no-code-solutions"></a>Soluzioni senza codice personalizzate

Grazie ad Azure Percept studio, è possibile sviluppare soluzioni personalizzate per la [visione](./tutorial-nocode-vision.md) e la sintesi vocale, senza necessità di scrivere codice.

Per le soluzioni di visione artificiale personalizzate, sono disponibili sia il rilevamento degli oggetti che i modelli di intelligenza artificiale. È sufficiente caricare e contrassegnare le immagini di training, che possono essere eseguite direttamente con il modello di Azure Percept Vision SoM di Azure Percept DK, se lo si desidera. Il training e la valutazione del modello vengono eseguiti facilmente in [visione personalizzata](https://www.customvision.ai/), che fa parte dei [Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/#overview).

Per le soluzioni di riconoscimento vocale personalizzato, i modelli di Assistente vocale sono attualmente disponibili per le applicazioni seguenti:

- Ospitalità: Room Hotel attrezzata con Smart Device controllati da Voice.
- Healthcare: funzionalità di assistenza attrezzata con Smart Device controllati da Voice.
- Inventario: Hub di inventario dotato di Smart Device controllati dalla voce.
- Automotive: Hub automobilistico dotato di Smart Device controllati dalla voce.

Le parole chiave e i comandi degli assistenti vocali predefiniti sono disponibili direttamente tramite il portale. Le parole chiave e i comandi personalizzati possono essere creati e sottoposti a training in [speech studio](https://speech.microsoft.com/), che fa parte anche dei servizi cognitivi di Azure.

## <a name="advanced-development"></a>Sviluppo avanzato

Per gli sviluppatori avanzati, il [notebook di Jupyter](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) disponibile esegue l'apprendimento del trasferimento usando un modello TensorFlow con training preliminare (MobileNetSSDV2Lite) in Python con un set di dati personalizzato per il rilevamento degli oggetti. Il notebook usa le istanze di calcolo remote tramite [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/#product-overview) e può essere eseguito nel cloud usando il portale di AzureML o localmente in [Visual Studio Code](https://code.visualstudio.com/).

Sono inclusi anche alcuni [script](https://github.com/microsoft/Project-Santa-Cruz-Preview/tree/main/Sample-Scripts-and-Notebooks/Official/Scripts) Python utili per la gestione dei set di impostazioni e il [programma di installazione di Dev Tools Pack](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/dev-tools-installer.md), che consente di installare e configurare tutti gli strumenti necessari per sviluppare una soluzione avanzata di intelligenza artificiale.
