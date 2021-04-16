---
title: Azure IoT gestione dei dispositivi w/Visual Studio Cloud Explorer
description: Usare Cloud Explorer per Visual Studio per la gestione dei dispositivi dell'hub IoT di Azure, con opzioni di gestione come i metodi diretti e le proprietà desiderate in dispositivi gemelli.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 96f93325e0f17daaaf2bad91123fea81531ca152
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566963"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Usare Cloud Explorer per Visual Studio per la gestione dei dispositivi dell'hub IoT di Azure

![Diagramma end-to-end](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

Questo articolo illustra come usare il Cloud Explorer per Visual Studio con varie opzioni di gestione nel computer di sviluppo. [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) è un'utile estensione di Visual Studio che consente di visualizzare le risorse di Azure, controllare le relative proprietà ed eseguire azioni fondamentali per gli sviluppatori dall'interno di Visual Studio. Include opzioni di gestione che consentono di eseguire varie attività.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opzione di gestione          | Attività                    |
|----------------------------|--------------------------------|
| Metodi diretti             | Far eseguire un'attività al dispositivo, quale l'avvio o l'arresto dell'invio di messaggi o il riavvio del dispositivo.                                        |
| Lettura dispositivo gemello           | Ottenere lo stato restituito da un dispositivo. Ad esempio, il dispositivo segnala che il LED sta lampeggiando.                                    |
| Aggiornamento dispositivo gemello         | Impostare determinati stati di un dispositivo, ad esempio impostare la luce verde per un LED o impostare l'intervallo di invio dei dati di telemetria su 30 minuti.         |
| Messaggi da cloud a dispositivo   | Inviare notifiche a un dispositivo. Ad esempio "Oggi è molto probabile che piova. Meglio non dimenticare l'ombrello".              |

Per altre informazioni sulle differenze e sull'uso di queste opzioni, vedere [Device-to-cloud communication guidance](iot-hub-devguide-d2c-guidance.md) (Indicazioni sulla comunicazione da dispositivo a cloud) e [Cloud-to-device communication guidance](iot-hub-devguide-c2d-guidance.md) (Indicazioni sulla comunicazione da cloud a dispositivo).

I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi, ad esempio metadati, configurazioni e condizioni. L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette. Per altre informazioni sui dispositivi gemelli, vedere [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure attiva.

- Un hub IoT di Azure nella sottoscrizione.

- Microsoft Visual Studio 2017 Update 9 o versione successiva. Questo articolo usa [Visual Studio 2017 o Visual Studio 2019.](https://www.visualstudio.com/vs/)

- Cloud Explorer componente da Programma di installazione di Visual Studio, selezionato per impostazione predefinita con Carico di lavoro di Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Aggiornare Cloud Explorer alla versione più recente

Il Cloud Explorer di Programma di installazione di Visual Studio per Visual Studio 2017 supporta solo il monitoraggio dei messaggi da dispositivo a cloud e da cloud a dispositivo. Per usare Visual Studio 2017, scaricare e installare la versione più [recente Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Accedere per accedere all'hub

1. In Visual Studio selezionare **Visualizza Cloud Explorer**  >   per aprire Cloud Explorer.

1. Selezionare l'icona Gestione account per visualizzare le sottoscrizioni.

    ![Icona Gestione account](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se si è connessi ad Azure, vengono visualizzati gli account. Per accedere ad Azure per la prima volta, scegliere **Aggiungi un account**.

1. Selezionare le sottoscrizioni di Azure da usare e scegliere **Applica.**

1. Espandere la sottoscrizione e quindi **Hub IoT**.  In ogni hub è possibile visualizzare i dispositivi per tale hub. Fare clic con il pulsante destro del mouse su un dispositivo per accedere alle opzioni di gestione.

    ![Opzioni di gestione](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Metodi diretti

Per usare i metodi diretti, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Invoke Device Direct Method** (Richiama metodo diretto dispositivo).

1. Immettere il nome del metodo e il payload in **Invoke Direct Method (Richiama metodo diretto)** e quindi selezionare **OK**.

    I risultati vengono visualizzati in **Output**.

## <a name="update-device-twin"></a>Aggiornamento dispositivo gemello

Per modificare un dispositivo gemello, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Edit Device Twin** (Modifica dispositivo gemello).

   Viene **azure-iot-device-twin.jsun'operazione sul** file con il contenuto del dispositivo gemello.

1. Apportare alcune modifiche **ai tag** o ai **campi properties.desired** azure-iot-device-twin.js **nel** file .

1. Premere **CTRL+S** per aggiornare il dispositivo gemello.

   I risultati vengono visualizzati in **Output.**

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:

1. Fare clic con il pulsante destro sul dispositivo e selezionare **Invia messaggio da cloud a dispositivo**.

1. Immettere il messaggio in **Send C2D message (Invia messaggio C2D)** e selezionare **OK.**

   I risultati vengono visualizzati in **Output.**

## <a name="next-steps"></a>Passaggi successivi

È stato descritto come usare Cloud Explorer per Visual Studio con varie opzioni di gestione.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
