---
title: Configurare l'audio di Azure Percept
description: Informazioni su come connettere il dispositivo audio Azure Percept ad Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605791"
---
# <a name="azure-percept-audio-setup"></a>Installazione audio di Azure Percept

Azure Percept audio è compatibile con Azure Percept DK. Non è necessaria alcuna installazione univoca.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- Audio di Azure Percept
- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- [Esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md): la DevKit è stata connessa a una rete di Wi-Fi, è stato creato un hub Internet e la DevKit è stata connessa all'hub Internet.
- Altoparlante o cuffie che possono connettersi a un jack audio da 3,5 mm (facoltativo)

## <a name="connecting-your-devices"></a>Connessione dei dispositivi

1. Connettere il dispositivo audio Azure Percept alla lavagna del vettore di Azure Percept DK con il cavo micro USB per il tipo USB-A. Connettere l'estremità micro USB del cavo alla lavagna degli sviluppatori audio (Developer) e al tipo-A-end della lavagna del vettore Percept DK.

1. (Facoltativo) connettere l'altoparlante o le cuffie al dispositivo audio Azure Percept tramite il jack audio, che è denominato "line out". In questo modo sarà possibile ascoltare le risposte audio.

1. Accendere la devkit. Il LED L02 sulla lavagna audio interposer cambierà in lampeggio bianco per indicare che il dispositivo è stato acceso e che il SoM audio è autenticato.

1. Attendere il completamento del processo di autenticazione. questa operazione può richiedere fino a 3 minuti.

1. Si è pronti per iniziare la fase di prototipo quando viene visualizzato uno dei seguenti elementi:

    - Il L02 LED cambierà in bianco solido: indica che l'autenticazione è stata completata e che DevKit non è ancora stato configurato con una parola chiave.
    - Tutti e tre i LED diventano blu: indica che l'autenticazione è stata completata e che DevKit è configurato con una parola chiave.

## <a name="next-steps"></a>Passaggi successivi

Creare una [soluzione di riconoscimento vocale senza codice](./tutorial-no-code-speech.md) in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
