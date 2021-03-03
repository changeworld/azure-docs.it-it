---
title: Panoramica del dispositivo audio Azure Percept
description: Scopri di più su Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663684"
---
# <a name="introduction-to-azure-percept-audio"></a>Introduzione ad Azure Percept audio

Audio Percept di Azure è un dispositivo accessorio che aggiunge funzionalità di intelligenza artificiale per il riconoscimento vocale ad Azure Percept DK. Contiene un acceleratore di intelligenza artificiale preconfigurato e un array lineare a quattro microfoni, che consente di applicare comandi personalizzati, spotting di parole chiave e la sintesi vocale di un campo a dispositivi in ascolto locali. Azure Percept audio consente ai produttori di dispositivi di estendere le funzionalità di visione di Azure Percept DK ai nuovi dispositivi intelligenti attivati dalla voce. È integrato con Azure Percept DK, Azure Percept studio e altri servizi di gestione di Azure Edge. È disponibile per l'acquisto in [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

![Dispositivo audio Percept di Azure.](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Componenti audio di Azure Percept

Azure Percept audio contiene i componenti principali seguenti:

- Azure Percept Audio Device (SoM) pronto per la produzione con array lineare a quattro microfoni
- Bacheca per sviluppatori (include 2x pulsanti, 3x LED, USB micro e 3,5 mm audio jack)
- Cavi necessari: cavo Flex, USB Micro Type-B to USB-A
- Scheda di benvenuto
- Piastra di montaggio meccanico con montaggio serie 80/20 1010 integrato


<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>Guida introduttiva

- [Assemblare Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Completare l'esperienza di installazione di Azure Percept DK](./quickstart-percept-dk-set-up.md)
- [Connettere il dispositivo audio Azure Percept alla DevKit](./quickstart-percept-audio-setup.md)

### <a name="build-a-no-code-prototype"></a>Creazione di un prototipo senza codice

Crea una [soluzione di sintesi vocale senza codice](./tutorial-no-code-speech.md) usando i modelli di Assistente vocale di Azure Percept per gli scenari di ospitalità, sanità, inventario e auto.

## <a name="additional-technical-information"></a>Ulteriori informazioni tecniche

- [Foglio dati audio di Azure Percept](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>Passaggi successivi

Ordinare un dispositivo audio Percept di Azure in [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).