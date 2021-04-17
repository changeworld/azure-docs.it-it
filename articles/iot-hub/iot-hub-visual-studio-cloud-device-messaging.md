---
title: Usare VS Cloud Explorer per gestire la messaggistica hub IoT di Azure dispositivo
description: Informazioni su come usare Cloud Explorer per Visual Studio per monitorare dispositivi per i messaggi al cloud e inviare messaggi al dispositivo nell'hub IoT di Azure cloud.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 8461a77d06a63c2ac319323a91b5577ca4dce1cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567031"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Usare Cloud Explorer per Visual Studio per inviare e ricevere messaggi tra il dispositivo e l'hub IoT

![Diagramma end-to-end](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

Questo articolo illustra come usare Cloud Explorer per Visual Studio per monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo. I messaggi da dispositivo a cloud possono includere dati di sensori raccolti dal dispositivo e inviati all'hub IoT. I messaggi da cloud a dispositivo possono includere comandi inviati dall'hub IoT al dispositivo, ad esempio per far lampeggiare un LED connesso a quest'ultimo.

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) è un'utile estensione di Visual Studio che consente di visualizzare le risorse di Azure, controllare le relative proprietà ed eseguire azioni fondamentali per gli sviluppatori dall'interno di Visual Studio. Questo articolo illustra come usare Cloud Explorer per inviare e ricevere messaggi tra il dispositivo e l'hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure attiva.

- Un hub IoT di Azure nella sottoscrizione.

- Microsoft Visual Studio 2017 Update 9 o versione successiva. Questo articolo usa [Visual Studio 2019.](https://www.visualstudio.com/vs/)

- Componente Cloud Explorer da Programma di installazione di Visual Studio, selezionato per impostazione predefinita con Carico di lavoro di Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Aggiornare Cloud Explorer alla versione più recente

Il Cloud Explorer di Programma di installazione di Visual Studio per Visual Studio 2017 supporta solo il monitoraggio dei messaggi da dispositivo a cloud e da cloud a dispositivo. Per usare Visual Studio 2017, scaricare e installare la versione più [recente Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Accedere per accedere all'hub

Per accedere all'hub, seguire questa procedura:

1. In Visual Studio selezionare **Visualizza Cloud Explorer**  >   per aprire Cloud Explorer.

1. Selezionare l'icona Gestione account per visualizzare le sottoscrizioni.

    ![Icona Gestione account](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se si è connessi ad Azure, vengono visualizzati gli account. Per accedere ad Azure per la prima volta, scegliere **Aggiungi un account**.

1. Selezionare le sottoscrizioni di Azure da usare e scegliere **Applica.**

1. Espandere la sottoscrizione e quindi **Hub IoT**.  In ogni hub è possibile visualizzare i dispositivi per tale hub.

    ![Elenco dei dispositivi](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorare i messaggi da dispositivo a cloud

Per monitorare i messaggi inviati dal dispositivo all'hub IoT, seguire questi passaggi:

1. Fare clic con il pulsante destro sull'hub IoT o sul dispositivo e selezionare **Start Monitoring D2C Message** (Avvia monitoraggio messaggi da dispositivo a cloud).

    ![Avvio del monitoraggio dei messaggi da dispositivo a cloud](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. I messaggi monitorati vengono visualizzati in **Output**.

    ![Risultato del monitoraggio dei messaggi da dispositivo a cloud](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Per interrompere il monitoraggio, fare clic su qualsiasi dispositivo o hub IoT e selezionare **Stop Monitoring D2C Message** (Interrompi monitoraggio messaggi da dispositivo a cloud).

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:

1. Fare clic con il pulsante destro sul dispositivo e selezionare **Invia messaggio da cloud a dispositivo**.

1. Immettere il messaggio nella casella di input.

    ![Invio di un messaggio da cloud a dispositivo](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    I risultati vengono visualizzati in **Output**.

    ![Risultato dell'invio di un messaggio da cloud a dispositivo](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Passaggi successivi

È stato appreso come monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo tra il dispositivo IoT e l'hub IoT di Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]