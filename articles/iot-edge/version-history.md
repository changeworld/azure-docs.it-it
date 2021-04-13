---
title: IoT Edge e la cronologia di esplorazione della versione Azure IoT Edge
description: Scopri le novità di IoT Edge con informazioni sulle nuove funzionalità e funzionalità nelle versioni più recenti.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d3473e975e7c69a83c359b040a2de0defaac69b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310471"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Versioni di Azure IoT Edge e note sulla versione

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge è un prodotto creato dal progetto open source IoT Edge ospitato su GitHub. Tutte le nuove versioni vengono rese disponibili nel [progetto Azure IOT Edge](https://github.com/Azure/azure-iotedge). I contributi e i report sui bug possono essere effettuati nel [progetto open source IOT Edge](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Versioni documentate

La documentazione di IoT Edge in questo sito è disponibile per due versioni diverse del prodotto, in modo che sia possibile scegliere il contenuto che si applica all'ambiente IoT Edge. Attualmente, le due versioni supportate sono:

* **IoT Edge 1,2** contiene contenuti aggiuntivi per le nuove funzionalità e le nuove funzionalità disponibili nella versione stabile più recente.
* **IoT Edge 1,1 (LTS)** è la prima versione del supporto a lungo termine (LTS) di IOT Edge. La documentazione relativa a questa versione copre tutte le funzionalità e le funzionalità di tutte le versioni precedenti fino a 1,1. Questa versione della documentazione sarà stabile attraverso la durata supportata della versione 1,1 e non rifletterà le nuove funzionalità rilasciate nelle versioni successive.

Per ulteriori informazioni sulle versioni di IoT Edge, vedere [Azure IOT Edge sistemi supportati](support.md).

## <a name="version-history"></a>Cronologia delle versioni

Questa tabella fornisce la cronologia delle versioni recenti per IoT Edge versioni dei pacchetti ed evidenzia gli aggiornamenti della documentazione effettuati per ogni versione.

| Note sulla versione e asset | Tipo | Data | Elementi di rilievo |
| ------------------------ | ---- | ---- | ---------- |
| [1,2](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0) | Stable | Aprile 2021 | [Dispositivi IoT Edge dietro i gateway](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[Broker IoT Edge MQTT (anteprima)](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true)<br>Nuovi pacchetti di IoT Edge introdotti con nuovi passaggi di installazione e configurazione. Per ulteriori informazioni, vedere [aggiornamento da 1,0 o 1,1 a 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | Supporto a lungo termine | 2021 febbraio | [Aggiornamenti dei sistemi supportati e del piano di supporto a lungo termine](support.md) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | Ottobre 2020 | [Metodo diretto UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Caricare le metriche di runtime](how-to-access-built-in-metrics.md)<br>[Priorità di route e durata (TTL)](module-composition.md#priority-and-time-to-live)<br>[Ordine di avvio del modulo](module-composition.md#configure-modules)<br>[Provisioning manuale X. 509](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | Marzo 2020 | [Provisioning automatico X. 509 con DPS](how-to-auto-provision-x509-certs.md)<br>[Metodo diretto RestartModule](how-to-edgeagent-direct-method.md#restart-module)<br>[comando support-bundle](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Passaggi successivi

* [Visualizza tutte le versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)
* [Eseguire o rivedere le richieste di funzionalità nel forum dei commenti](https://feedback.azure.com/forums/907045-azure-iot-edge)