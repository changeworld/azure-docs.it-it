---
title: Visualizzare il flusso video RTSP di Azure Percept DK
description: Informazioni su come visualizzare il flusso video RTSP dal SoM della visione di Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 20fb8495e17d4294351a50c3bc97436de9f03450
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662984"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>Visualizzare il flusso video RTSP di Azure Percept DK

Seguire questa guida per visualizzare il flusso video RTSP dal SoM della visione di Azure Percept DK in Azure Percept Studio. L'inferenza dei modelli di intelligenza artificiale distribuiti nel dispositivo sarà visualizzabile nel flusso di lavoro.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- [Esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md): la DevKit è stata connessa a una rete di Wi-Fi, è stato creato un hub Internet e la DevKit è stata connessa all'hub Internet.

## <a name="view-the-rtsp-video-stream"></a>Visualizzare il flusso video RTSP

1. Accendere la devkit.

1. Passare ad [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Sul lato sinistro della pagina Panoramica fare clic su **dispositivi**.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Schermata di Panoramica di Azure Percept Studio." lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Selezionare il DevKit dall'elenco.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Schermata di Panoramica di Azure Percept Studio.":::

1. Fare clic su **Visualizza flusso del dispositivo**.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Schermata di Panoramica di Azure Percept Studio.":::

    Verrà visualizzata una scheda separata che mostra il flusso webstream Live dal SoM della visione di Azure Percept DK.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Schermata di Panoramica di Azure Percept Studio.":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come visualizzare i dati di [telemetria di Azure PERCEPT dk](./how-to-view-telemetry.md).