---
title: includere file
description: includere file
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763444"
---
L'applicazione di esempio include due dispositivi simulati e un gateway IoT Edge. Le esercitazioni seguenti mostrano due approcci per sperimentare e comprendere le funzionalità del gateway:

* Creare il gateway IoT Edge in una macchina virtuale di Azure e connettere una telecamera simulata.
* Creare il gateway IoT Edge in un dispositivo reale come un dispositivo Intel NUC e connettere una telecamera reale.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Usare il modello di applicazione di analisi video di Azure IoT Central per creare un'applicazione per punti vendita al dettaglio
> * Personalizzare le impostazioni dell'applicazione
> * Creare un modello di dispositivo per un dispositivo gateway IoT Edge
> * Aggiungere un dispositivo gateway all'applicazione IoT Central

## <a name="prerequisites"></a>Prerequisiti

Per completare questa serie di esercitazioni, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).
* Se si usa una telecamera reale, sono necessari la connettività tra il dispositivo IoT Edge e la telecamera e il canale **RTSP (Real Time Streaming Protocol)** .

## <a name="initial-setup"></a>Configurazione iniziale

In queste esercitazioni si aggiornano e si usano diversi file di configurazione. Le versioni iniziali di questi file sono disponibili nel repository GitHub [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway). Il repository include un file di testo [scratchpad ](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) che è possibile scaricare e usare per registrare i valori di configurazione dei servizi distribuiti. Questo file consente di completare i passaggi successivi delle esercitazioni.

Creare una cartella denominata *lva-configuration* nel computer locale per salvare copie di questi file. Fare quindi clic con il pulsante destro del mouse su ognuno dei collegamenti seguenti e scegliere **Salva con nome** per salvare il file nella cartella *lva-configuration*:
