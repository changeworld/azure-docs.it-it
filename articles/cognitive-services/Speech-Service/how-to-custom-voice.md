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
ms.openlocfilehash: 99af0ee46e2827ac8a5749d58bb19cf85f96aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577462"
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

La voce personalizzata supporta attualmente sia i livelli standard che quelli neurali. La voce neurale personalizzata consente agli utenti di creare modelli vocali di qualità superiore, richiedendo meno dati e fornisce misure che consentono di distribuire l'intelligenza artificiale in modo responsabile. Si consiglia di utilizzare una voce neurale personalizzata per sviluppare voci più realistiche per interfacce di conversazione più naturali e consentire ai clienti e agli utenti finali di trarre vantaggio dalla più recente tecnologia di sintesi vocale, in modo responsabile. [Altre informazioni sulla voce neurale personalizzata](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

> [!NOTE]
> Nell'ambito dell'impegno di Microsoft nella progettazione di intelligenza artificiale, abbiamo limitato l'uso di una voce neurale personalizzata. Il licenziatario potrà accedere alla tecnologia solo dopo che le applicazioni sono state rivedute e si è impegnata a usarla in linea con i principi di intelligenza artificiale responsabili. Per altre informazioni sui [criteri](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) , vedere l'articolo su come limitare l'accesso e [applicarlo qui](https://aka.ms/customneural). Le [lingue](language-support.md#customization) e le [aree](regions.md#custom-voices) supportate per la versione standard e neurale della voce personalizzata sono diverse. Verificare i dettagli prima di iniziare.  

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

Contenuto come dati, modelli, test ed endpoint sono organizzati in **progetti** nel portale vocale personalizzato. Ogni progetto è specifico per un paese/lingua e il sesso della voce che si vuole creare. Ad esempio, è possibile creare un progetto per una voce femminile per i bot della chat del Call Center che usano la lingua inglese nella Stati Uniti (' en-US ').

Per creare il primo progetto, selezionare la scheda **voce vocale/voce personalizzata** , quindi fare clic su **nuovo progetto**. Seguire le istruzioni fornite dalla procedura guidata per creare il progetto. Dopo aver creato un progetto, vengono visualizzate quattro schede: **dati**, **Training**, **testing** e **distribuzione**. Usare i collegamenti forniti nei [passaggi successivi](#next-steps) per informazioni su come usare ogni scheda.

> [!IMPORTANT]
> Il [portale Voice personalizzato](https://aka.ms/custom-voice) è stato aggiornato di recente. Se sono stati creati dati, modelli, test e endpoint pubblicati in precedenza nel portale di CRIS.ai o con le API, è necessario creare un nuovo progetto nel nuovo portale per connettersi a queste entità obsolete.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Come eseguire la migrazione a una voce neurale personalizzata

Il livello di training standard/non neurale (adattivo, parametrico statistico, concacenative) della voce personalizzata è deprecato. Il annoucement è stato inviato a tutte le sottoscrizioni vocali esistenti prima del 2/28/2021. Durante il periodo di deprecazione (3/1/2021-2/29/2024), gli utenti del livello standard esistenti possono continuare a usare i modelli non neurali creati. Tutti i nuovi utenti/nuove risorse vocali devono passare al livello neurale o alla voce neurale personalizzata. Dopo 2/29/2024, tutte le voci standard e non neurali non saranno più supportate. 

Se si usa una voce personalizzata non neurale/standard, eseguire la migrazione alla voce neurale personalizzata immediatamente dopo la procedura riportata di seguito. Il passaggio a una voce neurale personalizzata ti aiuterà a sviluppare voci più realistiche per interfacce di conversazione ancora più naturali e consentire ai tuoi clienti e agli utenti finali di trarre vantaggio dalla più recente tecnologia di sintesi vocale, in modo responsabile. 

1. Per altre informazioni sui [criteri](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) , vedere l'articolo su come limitare l'accesso e [applicarlo qui](https://aka.ms/customneural). Si noti che l'accesso al servizio di Voice Neural personalizzato è soggetto alla esclusiva discrezione di Microsoft in base ai criteri di idoneità. I clienti possono accedere alla tecnologia solo dopo che l'applicazione è stata rivista e si sono impegnate a usarla in linea con i [principi di intelligenza artificiale responsabili](https://microsoft.com/ai/responsible-ai) e il [codice di comportamento](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 
2. Una volta approvata l'applicazione, verrà fornito l'accesso alla funzionalità di training "neurale". Assicurarsi di accedere al [portale vocale personalizzato](https://speech.microsoft.com/customvoice) usando la stessa sottoscrizione di Azure fornita nell'applicazione. 
    > [!IMPORTANT]
    > Per proteggere il talento vocale e impedire il training di modelli vocali con la registrazione non autorizzata o senza la conferma da parte del talento vocale, è necessario che il cliente carichi un rendiconto registrato del talento vocale per concedere il suo consenso. Quando si prepara lo script di registrazione, assicurarsi di includere la frase. "I [dichiarare il nome e il cognome] sono consapevoli che le registrazioni della mia voce verranno usate da [stato nome della società] per creare e usare una versione sintetica della mia voce".
    > Questa frase deve essere caricata nella scheda **Voice Talent** come file di consenso verbale. Verrà usato per verificare se le registrazioni nei set di impostazioni di training vengono eseguite dalla stessa persona che rilascia il consenso.
3. Dopo aver creato il modello di Voice neurale personalizzato, distribuire il modello vocale in un nuovo endpoint. Per creare un nuovo endpoint Voice personalizzato con il modello di voce neurale, passare a **Text-to-Speech > distribuzione voice > personalizzata**. Selezionare **Distribuisci modello** e immettere un **nome** e una **Descrizione** per l'endpoint personalizzato. Selezionare quindi il modello di voce neurale personalizzato da associare a questo endpoint e confermare la distribuzione.  
4. Aggiornare il codice nelle app se è stato creato un nuovo endpoint con un nuovo modello. 

## <a name="next-steps"></a>Passaggi successivi

- [Preparare i dati vocali personalizzati](how-to-custom-voice-prepare-data.md)
- [Creare una voce personalizzata](how-to-custom-voice-create-voice.md)
- [Guida: registrare gli esempi di voce](record-custom-voice-samples.md)
