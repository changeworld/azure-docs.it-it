---
title: includere file
description: File di inclusione
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491139"
---
<!-- All needs updating -->
Nell'applicazione Azure IoT Central un operatore può:

* Visualizzare i dati di telemetria inviati dai due componenti del termostato nella pagina **Panoramica** :

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Visualizzare i dati di telemetria dei dispositivi":::

* Visualizzare le proprietà del dispositivo nella pagina **informazioni su** . Questa pagina mostra le proprietà del componente informazioni sul dispositivo e dei due componenti del termostato:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Visualizzare le proprietà del dispositivo":::

## <a name="customize-the-device-template"></a>Personalizzare il modello di dispositivo

Gli sviluppatori di soluzioni possono personalizzare il modello di dispositivo che IoT Central creato automaticamente quando si connette il dispositivo del controller di temperatura.

Per aggiungere una proprietà cloud per archiviare il nome del cliente associato al dispositivo:

1. Nell'applicazione IoT Central passare al modello di dispositivo del **controller di temperatura** nella pagina **modelli di dispositivo** .

1. Nel modello di dispositivo **controller temperatura** selezionare **Proprietà Cloud**.

1. Selezionare **Aggiungi proprietà cloud**. Immettere *Nome del cliente* come **Nome visualizzato** e scegliere **Stringa** come **Schema**. Selezionare quindi **Salva**.

Per personalizzare il modo in cui i comandi **Get Max-Min report vengono** visualizzati nell'applicazione IoT Central:

1. Selezionare **Personalizza** nel modello di dispositivo.

1. Per **getMaxMinReport (thermostat1)**, sostituire *Get Max-Min report.* con il *report di stato Get thermostat1*.

1. Per **getMaxMinReport (thermostat2)**, sostituire *Get Max-Min report.* con il *report di stato Get thermostat2*.

1. Selezionare **Salva**.

Per personalizzare la modalità di visualizzazione delle proprietà scrivibili della **temperatura di destinazione** nell'applicazione IoT Central:

1. Selezionare **Personalizza** nel modello di dispositivo.

1. Per **targetTemperature (thermostat1)**, sostituire *temperature di destinazione* con *temperature di destinazione (1)*.

1. Per **targetTemperature (thermostat2)**, sostituire *temperature di destinazione* con *temperature di destinazione (2)*.

1. Selezionare **Salva**.

I componenti del termostato nel modello di **controller di temperatura** includono la proprietà **temperatura di destinazione** scrivibile, il modello di dispositivo include la proprietà del cloud nome del **cliente** . Creare una visualizzazione che può essere usata da un operatore per modificare queste proprietà:

1. Selezionare **Visualizzazioni** e quindi il riquadro **Modifica dei dati del dispositivo e del cloud**.

1. Immettere _Properties_ come nome del modulo.

1. Selezionare la **temperatura di destinazione (1)**, la  **temperatura di destinazione (2)** e le proprietà **del nome del cliente** . Fare quindi clic su **Aggiungi sezione**.

1. Salvare le modifiche.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Visualizzare per aggiornare i valori delle proprietà":::

## <a name="publish-the-device-template"></a>Pubblicare il modello di dispositivo

Prima che un operatore possa visualizzare e usare le personalizzazioni apportate, è necessario pubblicare il modello di dispositivo.

Nel modello di dispositivo **Termostato** selezionare **Pubblica**. Nel pannello **Pubblica questo modello di dispositivo nell'applicazione** selezionare **Pubblica**.

Un operatore può ora usare la visualizzazione **Proprietà** per aggiornare i valori delle proprietà e chiamare i comandi chiamati **Get thermostat1 status report** e **Get thermostat2 status report** nella pagina dei comandi del dispositivo:

* Aggiornare i valori delle proprietà scrivibili nella pagina **Proprietà**:

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Aggiornare le proprietà del dispositivo":::

* Chiamare i comandi dalla pagina **Comandi**:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Chiamare il comando":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Visualizzare la risposta del comando":::
