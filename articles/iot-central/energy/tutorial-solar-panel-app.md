---
title: "Esercitazione: Creare un'app di monitoraggio dei pannelli solari con Azure IoT Central"
description: "Esercitazione: Informazioni su come creare un'applicazione per pannelli solari usando i modelli di applicazione di Azure IoT Central."
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9ea1db982a6944bd12b458624545b3888881508f
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881919"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Esercitazione: Creare ed esplorare il modello di app per il monitoraggio dei pannelli solari 

Questa esercitazione assiste l'utente durante il processo di creazione di un'applicazione di monitoraggio dei pannelli solari, che include un modello di dispositivo di esempio con dati simulati. In questa esercitazione si apprenderà come:


> [!div class="checklist"]
> * Creare gratuitamente un'app per pannelli solari
> * Esaminare l'applicazione
> * Pulire le risorse


Se non si ha una sottoscrizione, [creare un account di valutazione gratuito](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prerequisiti

Non sono previsti prerequisiti per completare questa esercitazione. Una sottoscrizione di Azure è consigliata, ma non obbligatoria.


## <a name="create-a-solar-panel-monitoring-app"></a>Creare un'app per il monitoraggio pannelli solari 

È possibile creare questa applicazione in tre semplici passaggi:

1. Passare ad [Azure IoT Central](https://apps.azureiotcentral.com). Per creare una nuova applicazione, selezionare **Crea**. 

1. Selezionare la scheda **Energia**. In **Monitoraggio pannelli solari** selezionare **Crea app**. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot delle opzioni di creazione di Azure IoT Central.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. Nella finestra di dialogo **Nuova applicazione** immettere i dettagli richiesti e quindi selezionare **Crea**:
    * **Nome applicazione**: scegliere un nome per l'applicazione Azure IoT Central. 
    * **URL**: selezionare un URL di Azure IoT Central. La piattaforma ne verifica l'univocità.
    * **Piano tariffario**: se si ha già una sottoscrizione di Azure, è consigliabile usare l'impostazione predefinita. Se non si ha una sottoscrizione di Azure, iniziare con una versione di valutazione gratuita.
    * **Info di fatturazione**: L'applicazione stessa è gratuita. Per effettuare il provisioning delle risorse per l'app sono necessari i dettagli relativi alla directory, alla sottoscrizione di Azure e all'area.
        ![Screenshot di Nuova applicazione.](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Screenshot di Info di fatturazione.](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Verificare l'applicazione e i dati simulati

È possibile modificare la nuova app per pannelli solari in qualsiasi momento. Per il momento, assicurarsi che l'app venga distribuita e funzioni come previsto prima di modificarla.

Per verificare la creazione dell'app e la simulazione dei dati, passare al **Dashboard**. Se i riquadri presentano alcuni dati, la distribuzione dell'app ha avuto esito positivo. La simulazione dei dati può richiedere alcuni minuti per la generazione dei dati. 

## <a name="application-walk-through"></a>Procedura dettagliata per l'applicazione
Dopo aver distribuito il modello di app, è possibile esaminare l'app in dettaglio. Si noterà che include di un dispositivo contatore intelligente, un modello di dispositivo e un dashboard di esempio.

Adatum è una società fornitrice di energia fittizia che monitora e gestisce pannelli solari. Nel dashboard di monitoraggio del pannello solare sono presenti le proprietà del pannello solare, i dati e i comandi di esempio. Questo dashboard consente all'utente o al team di supporto di eseguire le attività seguenti in modo proattivo, prima che eventuali problemi richiedano ulteriori interventi:
* Esaminare le informazioni sul pannello più recenti e la posizione di installazione sulla mappa.
* Controllare lo stato del pannello e lo stato della connessione.
* Esaminare le tendenze relative alla generazione di energia e alla temperatura per rilevare modelli anomali.
* Tenere traccia della generazione di energia totale ai fini della pianificazione e della fatturazione.
* Attivare un pannello e aggiornare la versione del firmware, se necessario. Nel modello i pulsanti di comando mostrano le funzionalità possibili senza inviare comandi reali.

> [!div class="mx-imgBorder"]
> ![Screenshot del dashboard del modello Monitoraggio pannelli solari.](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Dispositivi
L'app include un dispositivo pannello solare di esempio. Per visualizzare i dettagli del dispositivo, selezionare **Dispositivi**.

> [!div class="mx-imgBorder"]
> ![Screenshot dei dispositivi del modello Monitoraggio pannelli solari.](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

Selezionare il dispositivo di esempio **SP0123456789**. Nella scheda **Aggiorna proprietà** è possibile aggiornare le proprietà scrivibili del dispositivo e visualizzare un oggetto visivo dei valori aggiornati nel dashboard. 

> [!div class="mx-imgBorder"]
> ![Screenshot della scheda Aggiorna proprietà del modello Monitoraggio pannelli solari.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Modello di dispositivo
Per visualizzare il modello di dispositivo per pannelli solari, selezionare la scheda **Modelli di dispositivo**. Il modello include interfacce predefinite per i dati, le proprietà, i comandi e le visualizzazioni.

> [!div class="mx-imgBorder"]
> ![Screenshot dei modelli di dispositivo del modello Monitoraggio pannelli solari.](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Se si decide di non continuare a usare questa applicazione, seguire questa procedura per eliminare l'applicazione:

1. Nel riquadro sinistro selezionare **Amministrazione**.
1. Selezionare **Impostazioni applicazione** > **Elimina**. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'amministrazione del modello Monitoraggio pannelli solari.](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Passaggi successivi
 
> [!div class="nextstepaction"]
> [Azure IoT Central - Architettura dell'app per pannelli solari](./concept-iot-central-solar-panel-app.md)
* [Creare gratuitamente modelli di applicazione per pannelli solari](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* [Panoramica di Azure IoT Central](../index.yml)
