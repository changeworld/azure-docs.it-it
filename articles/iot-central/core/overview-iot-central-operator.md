---
title: Guida dell'operatore di Azure IoT Central
description: Azure IoT Central è una piattaforma di applicazioni IoT che semplifica la creazione di soluzioni Internet. In questo articolo viene fornita una panoramica del ruolo Operatore in IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669942"
---
# <a name="iot-central-operator-guide"></a>Guida dell'operatore IoT Central

*Questo articolo si applica agli operatori.*

Un'applicazione IoT Central consente di monitorare e gestire milioni di dispositivi durante il relativo ciclo di vita. Questa guida è destinata agli operatori che usano un'applicazione IoT Central per gestire i dispositivi Internet.

Un operatore:

- Consente di monitorare e gestire i dispositivi connessi all'applicazione.
- Risoluzione dei problemi e correzione dei problemi relativi ai dispositivi.
- Effettua il provisioning di nuovi dispositivi.

## <a name="monitor-and-manage-devices"></a>Monitorare e gestire i dispositivi

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Screenshot che mostra una visualizzazione del dispositivo":::

Per monitorare i dispositivi, un operatore può usare le visualizzazioni del dispositivo definite dal generatore di soluzioni come parte del modello di dispositivo. Queste visualizzazioni possono visualizzare i dati di telemetria del dispositivo e i valori delle proprietà. Un esempio è la visualizzazione **Panoramica** illustrata nella schermata precedente.

Per informazioni più dettagliate, un operatore può usare i gruppi di dispositivi e le funzionalità di analisi predefinite. Per altre informazioni, vedere [come usare Analytics per analizzare i dati del dispositivo](howto-create-analytics.md).

Per gestire i singoli dispositivi, un operatore può usare le visualizzazioni dei dispositivi per impostare le proprietà del dispositivo e del cloud e chiamare i comandi del dispositivo. Gli esempi includono le visualizzazioni **Gestisci dispositivo** e **comandi** nello screenshot precedente.

Per gestire i dispositivi in blocco, un operatore può creare e pianificare i processi. I processi possono aggiornare le proprietà ed eseguire comandi su più dispositivi. Per altre informazioni, vedere [creare ed eseguire un processo nell'applicazione IoT Central di Azure](howto-run-a-job.md).

## <a name="troubleshoot-and-remediate-issues"></a>Risolvere i problemi e correggere i problemi

L'operatore è responsabile dell'integrità dell'applicazione e dei relativi dispositivi. La [Guida alla risoluzione dei problemi](troubleshoot-connection.md) consente agli operatori di diagnosticare e correggere i problemi comuni. Un operatore può usare la pagina **dispositivi** per bloccare i dispositivi che sembrano non funzionare correttamente fino a quando il problema non viene risolto.

## <a name="add-and-remove-devices"></a>Aggiungere e rimuovere dispositivi

L'operatore può aggiungere e rimuovere dispositivi per l'applicazione IoT Central sia singolarmente che in blocco. Per altre informazioni, vedere [gestire i dispositivi nell'applicazione IoT Central di Azure](howto-manage-devices.md).

## <a name="personalize"></a>Personalizza

Gli operatori possono creare dashboard personalizzati in un'applicazione IoT Central che contengono collegamenti alle risorse che usano più spesso. Per altre informazioni, vedere [Manage Dashboards](howto-create-personal-dashboards.md#manage-dashboards).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di IoT Central, vedere le guide introduttive, a partire da [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md).
