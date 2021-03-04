---
title: Visualizzare i dati di telemetria dell'inferenza del modello di Azure Percept DK
description: Informazioni su come visualizzare i dati di telemetria dell'inferenza del modello di visione di Azure Percept DK in Azure Internet Explorer
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 07abbc5f5e85c75b73774d11b6b81dd2085735b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095324"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>Visualizzare i dati di telemetria dell'inferenza del modello di Azure Percept DK

Seguire questa guida per visualizzare i dati di telemetria dell'inferenza del modello di visione di Azure Percept DK in [Azure Internet Explorer](https://github.com/Azure/azure-iot-explorer/releases).

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- [Esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md): la DevKit è stata connessa a una rete di Wi-Fi, è stato creato un hub Internet e la DevKit è stata connessa all'hub Internet.
- [Il modello visione artificiale è stato distribuito in Azure Percept DK](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>Visualizzare i dati di telemetria

1. Accendere la devkit.

1. Scaricare e installare [Azure Internet Explorer](https://github.com/Azure/azure-iot-explorer/releases). Se si è un utente di Windows, selezionare il file con estensione msi.

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Schermata di download per Azure Internet Explorer.":::

1. Connettere l'hub Internet ad Azure Internet Explorer:

    1. Accedere al [portale di Azure](https://portal.azure.com).

    1. Selezionare **Tutte le risorse**.

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Portale di Azure Home page.":::

    1. Selezionare l'hub delle cose a cui è connesso Azure Percept DK.

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="Elenco degli hub Internet in portale di Azure.":::

    1. Sul lato sinistro della pagina hub tutto selezionare **criteri di accesso condiviso**.

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="Pagina hub Internet che mostra i criteri di accesso condiviso.":::

    1. Fare clic su **iothubowner**.

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="Schermata Criteri di accesso condiviso con iothubowner evidenziato.":::

    1. Fare clic sull'icona copia blu accanto a **stringa di connessione-chiave primaria**.

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="finestra iothubowner con il pulsante copia stringa di connessione evidenziato.":::

    1. Aprire Azure Internet Explorer e fare clic su **+ Aggiungi connessione**.

    1. Incollare la stringa di connessione nella casella **stringa** di connessione nella finestra **Aggiungi stringa di connessione** e fare clic su **Salva**.

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="Finestra di Azure Internet Explorer con box per incollare la stringa di connessione in.":::

    1. Puntare il SoM della visione a un oggetto per l'inferenza del modello.

    1. Selezionare la **telemetria**.

    1. Fare clic su **Start** per visualizzare gli eventi di telemetria dal dispositivo.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come visualizzare il [flusso video di Azure PERCEPT dk](./how-to-view-video-stream.md).