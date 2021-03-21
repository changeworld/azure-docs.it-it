---
title: Distribuire un modello di intelligenza artificiale in Azure Percept DK
description: Informazioni su come distribuire un modello visione artificiale in Azure Percept DK da Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 01bd3709050d8a2b57c1bf51920308188546fb31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035484"
---
# <a name="deploy-a-vision-ai-model-to-your-azure-percept-dk"></a>Distribuire un modello di intelligenza artificiale in Azure Percept DK

Seguire questa guida per distribuire un modello visione artificiale in Azure Percept DK dall'interno di Azure Percept Studio.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- [Esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md): la DevKit è stata connessa a una rete di Wi-Fi, è stato creato un hub Internet e la DevKit è stata connessa all'hub Internet.

## <a name="model-deployment"></a>Distribuzione del modello

1. Accendere la devkit.

1. Passare ad [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Sul lato sinistro della pagina Panoramica fare clic su **dispositivi**.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Schermata di Panoramica di Azure Percept Studio." lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Selezionare il DevKit dall'elenco.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Elenco di dispositivi Percept.":::

1. Nella pagina successiva fare clic su **Distribuisci un modello di esempio** se si desidera distribuire uno dei modelli di visione artificiale di esempio pre-sottoposti a training. Se si vuole distribuire una [soluzione personalizzata per la visione senza codice](./tutorial-nocode-vision.md), fare clic su **distribuisci un visione personalizzata progetto**.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Opzioni del modello per la distribuzione.":::

1. Se si è scelto di distribuire una soluzione per la visione senza codice, selezionare il progetto e l'iterazione del modello preferita e fare clic su **Distribuisci**.

1. Se si è scelto di distribuire un modello di esempio, selezionare il modello e fare clic su **Distribuisci nel dispositivo**.

1. Quando la distribuzione del modello ha esito positivo, verrà visualizzato un messaggio di stato nell'angolo superiore destro dello schermo. Per visualizzare l'inferenza del modello in azione, fare clic sul collegamento **Visualizza flusso** nel messaggio di stato per visualizzare il flusso video RTSP dal som della visione del devkit.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come visualizzare i dati di [telemetria di Azure PERCEPT dk](how-to-view-telemetry.md).