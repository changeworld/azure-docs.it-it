---
title: Panoramica del dispositivo audio Azure Percept
description: Scopri di più su Azure Percept audio
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 23fae249cfceec75af0b7c49a3875ab447ecbafd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564263"
---
# <a name="introduction-to-azure-percept-audio"></a>Introduzione ad Azure Percept audio

Audio Percept di Azure è un dispositivo accessorio che aggiunge funzionalità di intelligenza artificiale per il riconoscimento vocale ad [Azure PERCEPT dk](./overview-azure-percept-dk.md). Contiene un processore audio preconfigurato e un array lineare a quattro microfoni, che consente di usare i comandi vocali, l'individuazione delle parole chiave e la sintesi vocale dei campi, con l'aiuto dei servizi cognitivi di Azure. È integrato con Azure Percept DK, [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)e altri servizi di gestione di Azure Edge. L'audio di Azure Percept è disponibile per l'acquisto in [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Acquista ora](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Componenti audio di Azure Percept

Azure Percept audio contiene i componenti principali seguenti:

- Azure Percept Audio Device (SoM) per la produzione con un array lineare a quattro microfoni e l'elaborazione audio tramite il codec XMOS
- Scheda Developer (Interposer): 2x Buttons, 3x LEDs, micro USB e 3,5 mm audio jack
- Cavi necessari: cavo FPC, USB Micro Type-B to USB-A
- Scheda di benvenuto
- Piastra di montaggio meccanico con montaggio serie 80/20 1010 integrato

## <a name="compute-capabilities"></a>Funzionalità di calcolo 

L'audio di Azure Percept passa l'input audio attraverso lo stack di riconoscimento vocale che viene eseguito sulla CPU della lavagna del vettore di Azure Percept DK in modo ibrido per il cloud Edge. Di conseguenza, l'audio di Azure Percept richiede una lavagna del vettore con un sistema operativo che supporta lo stack vocale per eseguire. 

L'elaborazione audio viene eseguita nel modo seguente: 

- Azure Percept audio: acquisisce e converte l'audio e lo invia al DK e audio Jack.

- Azure Percept DK: lo stack di riconoscimento vocale esegue il Forming del fascio e l'annullamento Echo ed elabora l'audio in arrivo per ottimizzare la voce. Al termine dell'elaborazione, viene eseguita l'individuazione delle parole chiave.

- Cloud: elabora comandi e frasi in linguaggio naturale, verifica delle parole chiave e ripetizione del training. 

- Offline: se il dispositivo è offline, viene rilevata la parola chiave e vengono acquisiti i dati di telemetria dello stato della connessione Internet. È possibile che venga osservata una velocità di accettazione più falsa per l'individuazione delle parole chiave perché non è possibile eseguire la verifica delle parole chiave nel cloud. 

## <a name="getting-started"></a>Introduzione

- [Assemblare Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Connettere il dispositivo audio Azure Percept alla DevKit](./quickstart-percept-audio-setup.md)
- [Completare l'esperienza di installazione di Azure Percept DK](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>Creazione di un prototipo senza codice

Crea una [soluzione di riconoscimento vocale senza codice](./tutorial-no-code-speech.md) in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) usando i modelli di Assistente vocale di Azure Percept per gli scenari di ospitalità, sanità, inventario e auto.

### <a name="manage-your-no-code-speech-solution"></a>Gestire la soluzione di riconoscimento vocale senza codice

- [Configurare l'Assistente vocale in Azure Percept Studio](./how-to-manage-voice-assistant.md)
- [Configurare l'Assistente vocale nell'hub Internet](./how-to-configure-voice-assistant.md)
- [Risoluzione dei problemi audio di Azure Percept](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Ulteriori informazioni tecniche

- [Foglio dati audio di Azure Percept](./azure-percept-audio-datasheet.md)
- [Comportamento dei pulsanti e dei LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquistare un dispositivo audio Azure Percept da Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
