---
title: Acquisire immagini per una soluzione di visione senza codice in Azure Percept Studio
description: Informazioni su come acquisire immagini con Azure Percept DK in Azure Percept Studio per una soluzione per la visione senza codice
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: d6cece6ee3079ba9f400f40026ca26ea36668710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024643"
---
# <a name="capture-images-for-a-vision-project-in-azure-percept-studio"></a>Acquisire immagini per un progetto visione in Azure Percept Studio

Seguire questa guida per acquisire immagini usando il SoM visione di Azure Percept DK per un progetto di visione esistente in Azure Percept Studio. Se non è ancora stato creato un progetto di visione, vedere l' [esercitazione relativa alla visione senza codice](./tutorial-nocode-vision.md).

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- [Esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md): la DevKit è stata connessa a una rete di Wi-Fi, è stato creato un hub Internet e la DevKit è stata connessa all'hub Internet.
- [Progetto di visione senza codice](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Acquisisci immagini

1. Accendere la devkit.

1. Passare ad [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Sul lato sinistro della pagina Panoramica fare clic su **dispositivi**.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Schermata di Panoramica di Azure Percept Studio." lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Selezionare il DevKit dall'elenco.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Elenco di dispositivi Percept.":::

1. Nella pagina del dispositivo fare clic su **Acquisisci immagini per un progetto**.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Pagina dispositivi Percept con le azioni disponibili elencate.":::

1. Nella finestra **acquisizione immagine** eseguire le operazioni seguenti:

    1. Nel menu a discesa **progetto** selezionare il progetto di visione per cui si desidera raccogliere le immagini.

    1. Fare clic su **Visualizza flusso del dispositivo** per assicurarsi che la fotocamera del som della visione sia posizionata correttamente.

    1. Fare clic su **scatta foto** per acquisire un'immagine.

    1. In alternativa, selezionare la casella accanto a **acquisizione automatica immagini** per configurare un timer per l'acquisizione delle immagini:

        1. Selezionare la velocità di imaging preferita in **frequenza di acquisizione**.
        1. Selezionare il numero totale di immagini che si desidera raccogliere in **destinazione**.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Schermata di acquisizione dell'immagine.":::

Tutte le immagini saranno accessibili in [visione personalizzata](https://www.customvision.ai/).

## <a name="next-steps"></a>Passaggi successivi

[Testare e ripetere il training del modello di visione senza codice](../cognitive-services/custom-vision-service/test-your-model.md).