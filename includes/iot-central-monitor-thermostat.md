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
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017499"
---
Nell'applicazione Azure IoT Central un operatore può:

* Visualizzare i dati di telemetria inviati dal dispositivo nella pagina **Panoramica**:

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Visualizzare i dati di telemetria dei dispositivi":::

* Visualizzare le proprietà del dispositivo nella pagina **Informazioni su**:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Visualizzare le proprietà del dispositivo":::

## <a name="customize-the-device-template"></a>Personalizzare il modello di dispositivo

Gli sviluppatori di soluzioni possono personalizzare il modello di dispositivo che IoT Central ha creato automaticamente quando il dispositivo termostato è stato connesso.

Per aggiungere una proprietà cloud per archiviare il nome del cliente associato al dispositivo:

1. Nell'applicazione IoT Central passare al modello di dispositivo **Termostato** nella pagina **Modelli di dispositivo**.

1. Nel modello di dispositivo **Termostato** selezionare **Proprietà cloud**.

1. Selezionare **Aggiungi proprietà cloud**. Immettere *Nome del cliente* come **Nome visualizzato** e scegliere **Stringa** come **Schema**. Selezionare quindi **Salva**.

Per personalizzare la modalità di visualizzazione del comando **Get Max-Min report** nell'applicazione IoT Central, selezionare **Personalizza** nel modello di dispositivo. Sostituire **Get Max-Min report** con *Get status report*. Selezionare quindi **Salva**.

Il modello di **termostato** include la proprietà scrivibile **Target temperature**, il modello di dispositivo include la proprietà cloud **Customer name**. Creare una visualizzazione che può essere usata da un operatore per modificare queste proprietà:

1. Selezionare **Visualizzazioni** e quindi il riquadro **Modifica dei dati del dispositivo e del cloud**.

1. Immettere _Properties_ come nome del modulo.

1. Selezionare le proprietà del dispositivo **Target Temperature** (Temperatura di destinazione) e **Nome cliente**. Fare quindi clic su **Aggiungi sezione**.

1. Salvare le modifiche.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Visualizzare per aggiornare i valori delle proprietà":::

## <a name="publish-the-device-template"></a>Pubblicare il modello di dispositivo

Prima che un operatore possa visualizzare e usare le personalizzazioni apportate, è necessario pubblicare il modello di dispositivo.

Nel modello di dispositivo **Termostato** selezionare **Pubblica**. Nel pannello **Pubblica questo modello di dispositivo nell'applicazione** selezionare **Pubblica**.

Un operatore può ora usare la visualizzazione **Proprietà** per aggiornare i valori delle proprietà e chiamare un comando denominato **Get status report** (Ottieni report di stato) nella pagina comandi del dispositivo:

* Aggiornare i valori delle proprietà scrivibili nella pagina **Proprietà**:

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Aggiornare le proprietà del dispositivo":::

* Chiamare i comandi dalla pagina **Comandi**:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Chiamare il comando":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Visualizzare la risposta del comando":::
