---
title: Panoramica del dispositivo audio Azure Percept
description: Scopri di più su Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 8f57c63868d6f70094cf040a92c0124b46477758
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490195"
---
# <a name="introduction-to-azure-percept-audio"></a>Introduzione ad Azure Percept audio

Audio Percept di Azure è un dispositivo accessorio che aggiunge funzionalità di intelligenza artificiale per il riconoscimento vocale ad Azure Percept DK. Contiene un processore audio preconfigurato e un array lineare a quattro microfoni, che consente di applicare i comandi vocali, l'individuazione delle parole chiave e la sintesi vocale del campo ai dispositivi in ascolto locale usando servizi cognitivi di Azure. Azure Percept audio consente ai produttori di dispositivi di estendere Azure Percept DK oltre le funzionalità di visione ai nuovi dispositivi intelligenti attivati dalla voce. È integrato con Azure Percept DK, Azure Percept studio e altri servizi di gestione di Azure Edge. È disponibile per l'acquisto in [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Acquista ora](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Dispositivo audio Percept di Azure.":::

## <a name="azure-percept-audio-components"></a>Componenti audio di Azure Percept

Azure Percept audio contiene i componenti principali seguenti:

- Azure Percept Audio Device (SoM) pronto per la produzione con una matrice lineare a quattro microfoni e l'elaborazione audio eseguita da un codec XMOS
- Scheda Developer (Interposer) (include 2x pulsanti, 3x LED, micro USB e 3,5 mm audio jack)
- Cavi necessari: cavo FPC, USB Micro Type-B to USB-A
- Scheda di benvenuto
- Piastra di montaggio meccanico con montaggio serie 80/20 1010 integrato

## <a name="compute-capabilities"></a>Funzionalità di calcolo 

Azure Percept audio passa l'input audio tramite lo stack di riconoscimento vocale che viene eseguito sulla CPU della lavagna del vettore di Azure Percept DK in modo ibrido-cloud Edge. Di conseguenza, l'audio di Azure Percept richiede una lavagna del vettore con un sistema operativo che supporta lo stack vocale per eseguire. 

L'elaborazione viene eseguita nel modo seguente: 

- Azure Percept audio: acquisisce e converte l'audio e lo invia al DK e audio Jack.

- Azure Percept DK: lo stack di riconoscimento vocale esegue il Forming del fascio e l'annullamento Echo ed elabora l'audio in arrivo per ottimizzare la voce. Esegue quindi l'individuazione delle parole chiave.

- Cloud: elabora comandi e frasi in linguaggio naturale, verifica delle parole chiave e ripetizione del training. 

- Offline: se il dispositivo è offline, viene rilevata la parola chiave e vengono acquisiti i dati di telemetria dello stato della connessione Internet. È possibile che venga osservata una velocità di accettazione più falsa per l'individuazione delle parole chiave perché non è possibile eseguire la verifica delle parole chiave nel cloud. 

## <a name="getting-started"></a>Guida introduttiva

- [Assemblare Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Completare l'esperienza di installazione di Azure Percept DK](./quickstart-percept-dk-set-up.md)
- [Connettere il dispositivo audio Azure Percept alla DevKit](./quickstart-percept-audio-setup.md)

## <a name="build-a-no-code-prototype"></a>Creazione di un prototipo senza codice

Crea una [soluzione di riconoscimento vocale senza codice](./tutorial-no-code-speech.md) in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) usando i modelli di Assistente vocale di Azure Percept per gli scenari di ospitalità, sanità, inventario e auto.

### <a name="manage-your-no-code-speech-solution"></a>Gestire la soluzione di riconoscimento vocale senza codice

- [Configurare l'Assistente vocale nell'hub Internet](./how-to-manage-voice-assistant.md)
- [Configurare l'Assistente vocale in Azure Percept Studio](./how-to-configure-voice-assistant.md)
- [Risoluzione dei problemi audio di Azure Percept](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Ulteriori informazioni tecniche

- [Foglio dati audio di Azure Percept](./azure-percept-audio-datasheet.md)
- [Comportamento dei pulsanti e dei LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquistare un dispositivo audio Azure Percept da Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
