---
title: Migliorare la sintesi con il servizio di riconoscimento vocale personalizzato
titleSuffix: Azure Cognitive Services
description: Custom Voice è un set di strumenti online che ti permettono di creare una voce unica e riconoscibile per il tuo marchio. Per iniziare sono disponibili solo alcuni file audio e le trascrizioni associate. Per iniziare a creare un'esperienza di riconoscimento vocale personalizzata, seguire i collegamenti seguenti.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: e48dfd224a9656c7d8327dd77f1b55e9a744f3af
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573089"
---
# <a name="get-started-with-custom-voice"></a>Introduzione a Voce personalizzata

[Custom Voice](https://aka.ms/customvoice) è un set di strumenti online che ti permettono di creare una voce unica e riconoscibile per il tuo marchio. Per iniziare sono disponibili solo alcuni file audio e le trascrizioni associate. Per iniziare a creare un'esperienza di sintesi vocale personalizzata, seguire i collegamenti seguenti.

## <a name="whats-in-custom-voice"></a>Che cos'è la voce personalizzata?

Prima di iniziare con la voce personalizzata, sono necessari un account Azure e una sottoscrizione al servizio vocale. Dopo aver creato un account, è possibile preparare i dati, eseguire il training e testare i modelli, valutare la qualità vocale e infine distribuire il modello Voice personalizzato.

Il diagramma seguente illustra i passaggi per creare un modello Voice personalizzato usando il [portale vocale personalizzato](https://aka.ms/customvoice). Usare i collegamenti per altre informazioni.

![Diagramma dell'architettura vocale personalizzata](media/custom-voice/custom-voice-diagram.png)

1. [Sottoscrivere e creare un progetto](#set-up-your-azure-account) : creare un account Azure e creare una sottoscrizione al servizio di riconoscimento vocale. Questa sottoscrizione unificata consente di accedere a sintesi vocale, sintesi vocale, traduzione vocale e il portale vocale personalizzato. Quindi, usando la sottoscrizione al servizio vocale, creare il primo progetto vocale personalizzato.

2. [Caricare dati](how-to-custom-voice-create-voice.md#upload-your-datasets) : caricare dati (audio e testo) usando il portale vocale personalizzato o l'API Voice personalizzata. Dal portale è possibile esaminare e valutare i punteggi di pronuncia e i rapporti tra segnali e rumori. Per ulteriori informazioni, vedere [come preparare i dati per la voce personalizzata](how-to-custom-voice-prepare-data.md).

3. Eseguire il [training del modello](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) : usare i dati per creare un modello vocale personalizzato per la sintesi vocale. È possibile eseguire il training di un modello in lingue diverse. Dopo il training, testare il modello e, se si è soddisfatti del risultato, è possibile distribuire il modello.

4. [Distribuire il modello](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) : creare un endpoint personalizzato per il modello vocale da testo a riconoscimento vocale e usarlo per la sintesi vocale in prodotti, strumenti e applicazioni.

## <a name="custom-neural-voices"></a>Voci neurali personalizzate

La voce personalizzata supporta attualmente sia i livelli standard che quelli neurali. La voce neurale personalizzata consente agli utenti di creare modelli vocali di qualità superiore, richiedendo meno dati e fornisce misure che consentono di distribuire l'intelligenza artificiale in modo responsabile. Si consiglia di utilizzare una voce neurale personalizzata per sviluppare voci più realistiche per interfacce di conversazione più naturali e consentire ai clienti e agli utenti finali di trarre vantaggio dalla più recente tecnologia di sintesi vocale, in modo responsabile. [Altre informazioni sulla voce neurale personalizzata](https://aka.ms/CNV-Transparency-Note). 

> [!NOTE]
> Nell'ambito dell'impegno di Microsoft nella progettazione di intelligenza artificiale, abbiamo limitato l'uso di una voce neurale personalizzata. Il licenziatario potrà accedere alla tecnologia solo dopo che le applicazioni sono state rivedute e si è impegnata a usarla in linea con i principi di intelligenza artificiale responsabili. Per altre informazioni sui [criteri](https://aka.ms/gating-overview) , vedere l'articolo su come limitare l'accesso e [applicarlo qui](https://aka.ms/customneural). Le [lingue](language-support.md#customization) e le [aree](regions.md#custom-voices) supportate per la versione standard e neurale della voce personalizzata sono diverse. Verificare i dettagli prima di iniziare.  

## <a name="set-up-your-azure-account"></a>Configurare l'account Azure

Prima di poter usare il portale di Riconoscimento vocale personalizzato per creare un modello personalizzato, è necessaria una sottoscrizione al servizio di riconoscimento vocale. Seguire queste istruzioni per creare una sottoscrizione del servizio vocale in Azure. Se non si dispone di un account Azure, è possibile iscriversi per un nuovo account.  

Dopo aver creato un account Azure e una sottoscrizione al servizio vocale, sarà necessario accedere al portale vocale personalizzato e connettere la sottoscrizione.

1. Ottenere la chiave di sottoscrizione del servizio vocale dalla portale di Azure.
2. Accedere al [portale vocale personalizzato](https://aka.ms/custom-voice).
3. Selezionare la sottoscrizione e creare un progetto vocale.
4. Se si vuole passare a un'altra sottoscrizione vocale, usare l'icona a cremagliera che si trova nella parte superiore dello spostamento.

> [!NOTE]
> Prima di poter usare il servizio, è necessario disporre di una chiave del servizio vocale F0 o S0 creata in Azure. La voce neurale personalizzata supporta solo il livello s0. 

## <a name="how-to-create-a-project"></a>Come creare un progetto

Contenuto come dati, modelli, test ed endpoint sono organizzati in **progetti** nel portale vocale personalizzato. Ogni progetto è specifico per un paese/lingua e il sesso della voce che si vuole creare. Ad esempio, è possibile creare un progetto per una voce femminile per i bot della chat del Call Center che usano la lingua inglese nella Stati Uniti (en-US).

Per creare il primo progetto, selezionare la scheda **voce vocale/voce personalizzata** , quindi fare clic su **nuovo progetto**. Seguire le istruzioni fornite dalla procedura guidata per creare il progetto. Dopo aver creato un progetto, vengono visualizzate quattro schede: **dati**, **Training**, **testing** e **distribuzione**. Usare i collegamenti forniti nei [passaggi successivi](#next-steps) per informazioni su come usare ogni scheda.

> [!IMPORTANT]
> Il [portale Voice personalizzato](https://aka.ms/custom-voice) è stato aggiornato di recente. Se sono stati creati dati, modelli, test e endpoint pubblicati in precedenza nel portale di CRIS.ai o con le API, è necessario creare un nuovo progetto nel nuovo portale per connettersi a queste entità obsolete.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare i dati vocali personalizzati](how-to-custom-voice-prepare-data.md)
- [Creare una voce personalizzata](how-to-custom-voice-create-voice.md)
- [Guida: registrare gli esempi di voce](record-custom-voice-samples.md)
