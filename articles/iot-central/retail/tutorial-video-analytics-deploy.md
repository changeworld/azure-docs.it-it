---
title: 'Esercitazione: How to deploy the video Analytics-Object and Motion Detection Azure IoT Central Application Template'
description: "Esercitazione: questa guida riepiloga i passaggi per distribuire un'applicazione IoT Central di Azure usando il modello di applicazione video Analytics-oggetto e rilevamento movimento."
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: e524905aaeed54de6501eb6f024bf28dbab9c594
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362166"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Esercitazione: come distribuire un'applicazione IoT Central usando il modello di applicazione video Analytics-oggetto e rilevamento movimento

Per una panoramica dei componenti principali dell'applicazione *analisi dei movimenti di oggetti e di rilevamento del movimento* , vedere l'articolo relativo all' [architettura delle applicazioni di analisi video di rilevamento di oggetti e movimento](architecture-video-analytics.md).

Il video seguente offre una procedura dettagliata su come usare il _modello applicazione video Analytics-oggetto e rilevamento movimento_ per distribuire una soluzione IOT Central:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

In questa serie di esercitazioni si apprenderà come:

> [!div class="checklist"]
> * Distribuire l'applicazione
> * Distribuire un'istanza di IoT Edge che si connette all'applicazione
> * Monitorare e gestire l'applicazione

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione di Azure (consigliata). In alternativa, è possibile usare una versione di valutazione gratuita valida per sette giorni. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).

## <a name="deploy-the-application"></a>Distribuire l'applicazione

Completare i passaggi seguenti per distribuire un'applicazione IoT Central usando il modello applicazione di analisi video:

1. Completare l'esercitazione [creare un'applicazione di analisi video in azure IOT Central (Yolo V3)](tutorial-video-analytics-create-app-yolo-v3.md) o [creare un'analisi video in Azure IOT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) per:
    - Creare un account di Servizi multimediali di Azure.
    - Creare l'applicazione IoT Central dal modello di applicazione video Analytics-oggetto e rilevamento movimento.
    - Configurare un dispositivo gateway nell'applicazione IoT Central. Il gateway consente ai dispositivi della fotocamera di connettersi all'applicazione.

1. Completare l'esercitazione [creare un'istanza di IOT Edge per l'analisi video (macchina virtuale Linux)](tutorial-video-analytics-iot-edge-vm.md) o l' [esercitazione: creare un'istanza di IOT Edge per analisi video (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) per:
    - Creare una macchina virtuale di Azure con il runtime di Azure IoT Edge installato.-preparare l'installazione del IoT Edge per ospitare il modulo di analisi video.
    - Connettere il dispositivo IoT Edge all'applicazione IoT Central.

1. Completare l'esercitazione [monitorare e gestire un'applicazione di analisi video](tutorial-video-analytics-manage.md) per:
    - Aggiungere le fotocamere di rilevamento di oggetti e movimenti al gateway nell'applicazione IoT Central.
    - Avviare l'elaborazione della fotocamera.
    - Installare un lettore multimediale locale per visualizzare il video acquisito in AMS.
    - Visualizza il video acquisito che Mostra gli oggetti rilevati.
    - Riordina.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'applicazione, è possibile rimuovere tutte le risorse create come indicato di seguito:

1. Nell'applicazione IoT Central passare alla pagina **Applicazione** nella sezione **Amministrazione**. Selezionare **Elimina**.
1. Nel portale di Azure eliminare il gruppo di risorse **lva-rg**.
1. Nel computer locale arrestare il contenitore Docker **amp-viewer**.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è disponibile una panoramica dei passaggi per distribuire e usare il modello applicazione di analisi video, vedere

> [!div class="nextstepaction"]
> [Creare un'applicazione di analisi video in Azure IOT Central (Yolo V3)](tutorial-video-analytics-create-app-yolo-v3.md) o

> [!div class="nextstepaction"]
> Per iniziare, [creare un'analisi video in Azure IOT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) .
