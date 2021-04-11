---
title: 'Guida introduttiva: aggiungere risorse di Azure alla soluzione Internet delle cose'
description: In questo avvio rapido sono disponibili informazioni su come configurare la soluzione IoT end-to-end con Azure Defender per IoT.
ms.topic: quickstart
ms.date: 01/25/2021
ms.openlocfilehash: 6a90e8c3007f7b3448fd3f1b6e4fa46ba861081b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384581"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Avvio rapido: Configurare la soluzione Azure Defender per IoT

Questo articolo descrive come eseguire la configurazione iniziale della soluzione di sicurezza IoT con Defender per IoT.

## <a name="prerequisites"></a>Prerequisiti

- nessuno

## <a name="what-is-defender-for-iot"></a>Che cos'è l'offerta Defender per IoT?

Defender per IoT offre sicurezza end-to-end completa per soluzioni IoT basate su Azure.

Con Defender è possibile monitorare l'intera soluzione Internet delle cose in un unico dashboard, mostrando tutti i dispositivi, le piattaforme e le risorse di back-end in Azure.

Dopo l'abilitazione sull'hub IoT, Defender per IoT identifica automaticamente gli altri servizi di Azure, anch'essi connessi all'hub IoT e correlati alla soluzione IoT.

Oltre al rilevamento automatico delle relazioni, è anche possibile scegliere quali altri gruppi di risorse di Azure contrassegnare come parte della soluzione IoT.

Le opzioni selezionate consentono di aggiungere intere sottoscrizioni, gruppi di risorse o singole risorse.

Dopo aver definito tutte le relazioni tra le risorse, Defender for Internet usa Defender per fornire avvisi e raccomandazioni sulla sicurezza per queste risorse.

## <a name="add-azure-resources-to-your-iot-solution"></a>Aggiungere le risorse di Azure alla soluzione IoT

Per aggiungere una nuova risorsa alla soluzione Internet delle cose:

1. Aprire l'**hub IoT** nel portale di Azure.

1. In **Sicurezza** selezionare **Panoramica**, **Impostazioni** e quindi **Risorse monitorate**.

1. Selezionare **Modifica** e scegliere le risorse monitorate che appartengono alla soluzione IoT.

1. Selezionare **Aggiungi**.

Congratulazioni! È stato aggiunto un nuovo gruppo di risorse alla soluzione IoT.

Defender per IoT ora monitora i gruppi di risorse appena aggiunti e visualizza le raccomandazioni e gli avvisi di sicurezza pertinenti come parte della soluzione IoT.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come creare un Defender-Internet...

> [!div class="nextstepaction"]
> [Creazione di Defender-un sacco-Micro-Agents](quickstart-create-security-twin.md)
