---
title: Introduzione ad Azure Percept audio
description: Informazioni su come connettere il dispositivo audio Azure Percept ad Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 660f03ce248a27a00fdd443964fbdba2fe3adeb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179273"
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

1. Connettere il dispositivo audio Azure Percept alla lavagna del vettore di Azure Percept DK con il cavo micro USB per il tipo USB-A. Connettere l'estremità micro USB del cavo alla lavagna degli sviluppatori (Developer) e al tipo-A-end per la lavagna del vettore Percept DK.
1. (Facoltativo) connettere l'altoparlante o le cuffie all'audio di Azure Percept tramite il jack audio, che è denominato "line out". Questo consentirà di ricevere le risposte audio dell'Assistente vocale. Se non si connette un altoparlante o cuffie, sarà comunque possibile visualizzare le risposte come testo nella finestra demo. 

1. Accendere la devkit. Il LED L02 sulla lavagna dell'interposer cambierà in bianco lampeggiante per indicare che il dispositivo è stato acceso e che il modello di audio è autenticato.

1. Attendere il completamento del processo di autenticazione. questa operazione può richiedere fino a 3 minuti.

1. Si è pronti per iniziare la fase di prototipo quando viene visualizzato uno dei seguenti elementi:

    - Il L02 LED cambierà in bianco continuo. Ciò indica che l'autenticazione è stata completata e che il DevKit non è stato ancora configurato con una parola chiave.
    - Tutti e tre i LED diventano blu. Indica che l'autenticazione è stata completata e che DevKit è configurato con una parola chiave.

## <a name="next-steps"></a>Passaggi successivi

Creare una [soluzione di riconoscimento vocale senza codice](./tutorial-no-code-speech.md) in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
