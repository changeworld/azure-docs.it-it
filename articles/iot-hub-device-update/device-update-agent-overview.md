---
title: Informazioni su Device Update per hub IoT di Azure Agent| Microsoft Docs
description: Informazioni su Device Update per hub IoT di Azure Agent.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d97287657b1e1fe7d540e8811c90794aaa5fece
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739567"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Panoramica dell'agente dell'hub IoT di Aggiornamento dispositivi

L'agente di aggiornamento dispositivi è costituito da due livelli concettuali:

* Il livello di interfaccia si basa su [Azure IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) che consente il flusso della messaggistica tra l'agente di aggiornamento del dispositivo e Device Update Services.
* Il livello piattaforma è responsabile delle azioni di aggiornamento di alto livello di Download, Installazione e Applica che possono essere specifiche della piattaforma o del dispositivo.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Implementazioni dell'agente." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Livello dell'interfaccia

Il livello Interfaccia è costituito [dall'interfaccia principale ADU e](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) dall'interfaccia delle informazioni sul [dispositivo.](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)

Queste interfacce si basano su un file di configurazione per i valori predefiniti. I valori predefiniti includono aduc_manufacturer e aduc_model per l'interfaccia AzureDeviceUpdateCore e il modello e il produttore per l'interfaccia DeviceInformation. [Altre informazioni](device-update-configuration-file.md) sul file di configurazione.

### <a name="adu-core-interface"></a>Interfaccia ADU Core

L'interfaccia "ADU Core" è il canale di comunicazione principale tra l'agente di aggiornamento dispositivi e i servizi. [Altre informazioni](device-update-plug-and-play.md#adu-core-interface) su questa interfaccia.

### <a name="device-information-interface"></a>Interfaccia delle informazioni sul dispositivo

L'interfaccia di informazioni sul dispositivo viene usata per implementare `Azure IoT PnP DeviceInformation` l'interfaccia . [Altre informazioni](device-update-plug-and-play.md#device-information-interface) su questa interfaccia.

## <a name="the-platform-layer"></a>Livello piattaforma

Esistono due implementazioni del livello piattaforma. Il livello della piattaforma del simulatore ha un'implementazione semplice delle azioni di aggiornamento e viene usato per testare e valutare rapidamente l'aggiornamento del dispositivo per i servizi e la configurazione dell'hub IoT. Quando l'agente di aggiornamento del dispositivo viene compilato con il livello della piattaforma del simulatore, viene fatto riferimento a esso come agente del simulatore di aggiornamento del dispositivo o semplicemente simulatore. [Altre informazioni](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) su come usare l'agente simulatore. Linux Platform Layer si [](https://github.com/microsoft/do-client) integra con Ottimizzazione recapito per i download e viene usato nell'immagine di riferimento di Raspberry Pi e in tutti i client eseguiti in sistemi Linux.

### <a name="simulator-platform-layer"></a>Livello piattaforma simulatore

L'implementazione del livello piattaforma del simulatore è disponibile in e può essere usata per testare e `src/platform_layers/simulator_platform_layer` valutare l'aggiornamento dei dispositivi per l'hub IoT.  Molte delle azioni nell'implementazione del "simulatore" vengono simulate per ridurre le modifiche fisiche per sperimentare l'aggiornamento dei dispositivi per l'hub IoT.  È possibile eseguire un aggiornamento "simulato" end-to-end usando questo livello della piattaforma. [Altre informazioni sull'esecuzione](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) dell'agente simulatore.

### <a name="linux-platform-layer"></a>Livello della piattaforma Linux

L'implementazione del livello piattaforma Linux è disponibile in e si integra con il client Ottimizzazione recapito per i download e viene usata nell'immagine di riferimento di Raspberry Pi e in tutti i client eseguiti in `src/platform_layers/linux_platform_layer` sistemi Linux. [](https://github.com/microsoft/do-client/releases)

Questo livello può integrarsi con gestori di aggiornamento diversi per implementare il programma di installazione. Ad esempio, il `SWUpdate` gestore di aggiornamento richiama uno script della shell da chiamare nel file `SWUpdate` eseguibile per eseguire un aggiornamento.

## <a name="update-handlers"></a>Gestori di aggiornamento

I gestori di aggiornamento sono componenti che gestiscono il contenuto o parti specifiche del programma di installazione dell'aggiornamento. Le implementazioni del gestore di aggiornamento sono in `src/content_handlers` .

### <a name="simulator-update-handler"></a>Gestore di aggiornamento del simulatore

Il gestore di aggiornamento del simulatore viene usato dal livello piattaforma simulatore e può essere usato con il livello della piattaforma Linux per finte interazioni con un gestore di contenuto. Il gestore di aggiornamento del simulatore implementa le API del gestore di aggiornamento con quasi nessuna operazione. L'implementazione del gestore di aggiornamento del simulatore è disponibile di seguito:
* [Simulatore di aggiornamento delle immagini](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Simulatore apt per l'aggiornamento del pacchetto](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Nota: il campo InstalledCriteria nell'interfaccia PnP AzureDeviceUpdateCore deve essere l'hash sha256 del contenuto. Si tratta dello stesso hash presente nell'oggetto [import manifest.](import-update.md#create-a-device-update-import-manifest) [Altre informazioni](device-update-plug-and-play.md) su `installedCriteria` e `AzureDeviceUpdateCore` sull'interfaccia.

### <a name="swupdate-update-handler"></a>`SWUpdate` Gestore di aggiornamento

Il gestore di aggiornamento" si integra con l'eseguibile della riga di comando e altri comandi della shell per implementare gli aggiornamenti A/B in modo specifico per l'immagine di riferimento `SWUpdate` `SWUpdate` di Raspberry Pi. Trovare l'immagine di riferimento più recente di Raspberry Pi [qui.](https://github.com/Azure/iot-hub-device-update/releases) Il `SWUpdate` gestore di aggiornamento viene implementato in [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>Gestore di aggiornamento APT

Il gestore di aggiornamento APT elabora un manifesto di aggiornamento specifico di APT e richiama APT per installare o aggiornare i pacchetti Debian specificati.

## <a name="self-update-device-update-agent"></a>Aggiornamento automatico dell'agente di aggiornamento del dispositivo

L'agente di aggiornamento del dispositivo e le relative dipendenze possono essere aggiornati tramite la pipeline Aggiornamento dispositivo per l'hub IoT. Se si usa un aggiornamento basato su immagine, includere l'agente di aggiornamento del dispositivo più recente nella nuova immagine. Se si usa un aggiornamento basato su pacchetto, includere l'agente di aggiornamento del dispositivo e la versione desiderata nel manifesto apt come qualsiasi altro pacchetto. [Altre informazioni](device-update-apt-manifest.md) sul manifesto apt. È possibile controllare la versione installata dell'agente di aggiornamento del dispositivo Ottimizzazione recapito nella sezione Proprietà dispositivo del dispositivo [gemello IoT.](../iot-hub/iot-hub-devguide-device-twins.md) [Per altre informazioni sulle proprietà del dispositivo, vedere Interfaccia ADU Core.](device-update-plug-and-play.md#device-properties)

## <a name="next-steps"></a>Passaggi successivi
[Informazioni sul file di configurazione dell'agente di aggiornamento del dispositivo](device-update-configuration-file.md)