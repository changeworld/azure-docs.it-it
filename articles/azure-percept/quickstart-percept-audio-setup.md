---
title: Introduzione ad Azure Percept audio
description: Informazioni su come connettere il dispositivo audio Azure Percept ad Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665499"
---
# <a name="azure-percept-audio-setup"></a>Installazione audio di Azure Percept

Azure Percept audio è compatibile con Azure Percept DK. Non è necessaria alcuna installazione univoca.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- Audio di Azure Percept
- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- [Esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md): la DevKit è stata connessa a una rete di Wi-Fi, è stato creato un hub Internet e la DevKit è stata connessa all'hub Internet.

## <a name="connecting-your-devices"></a>Connessione dei dispositivi

1. Connettere il dispositivo audio Azure Percept alla lavagna del vettore di Azure Percept DK con il cavo USB Micro Type-B a tipo USB-A. Connettere il tipo micro-B end del cavo al connettore audio SoM e il tipo-A end alla lavagna del vettore Percept DK.

1. Accendere la devkit.

    - Il LED L01 sul SoM audio cambierà in verde continuo per indicare che il dispositivo è stato acceso.
    - Il L02 LED cambierà in verde lampeggiante per indicare che il modello audio è autenticato.

1. Attendere il completamento del processo di autenticazione. questa operazione può richiedere fino a 3 minuti.

1. Si è pronti per iniziare la fase di prototipo quando viene visualizzato uno dei seguenti elementi:

    - Il L01 LED si disattiva e L02 è bianco. Ciò indica che l'autenticazione è stata completata e che il DevKit non è stato ancora configurato con una parola chiave.
    - Tutti e tre i LED diventano blu. Indica che l'autenticazione è stata completata e che DevKit è configurato con una parola chiave.

    > [!NOTE]
    > Contattare per supportare se il DevKit non esegue l'autenticazione.

## <a name="next-steps"></a>Passaggi successivi

Creare una [soluzione di riconoscimento vocale senza codice](./tutorial-no-code-speech.md).