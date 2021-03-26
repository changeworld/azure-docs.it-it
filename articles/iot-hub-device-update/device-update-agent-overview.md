---
title: Informazioni sull'aggiornamento del dispositivo per l'agente Hub Azure Internet | Microsoft Docs
description: Informazioni sull'aggiornamento del dispositivo per l'agente Hub Azure.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: efe5d0171463668bda19a0d0445fc67f3734aaee
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561237"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Panoramica dell'agente dell'hub IoT di Aggiornamento dispositivi

L'agente di aggiornamento del dispositivo è costituito da due livelli concettuali:

* Il livello di interfaccia si basa sul [plug and Play di Azure Internet (PNP)](../iot-pnp/overview-iot-plug-and-play.md) che consente il flusso della messaggistica tra l'agente di aggiornamento del dispositivo e i servizi di aggiornamento dei dispositivi.
* Il livello piattaforma è responsabile delle azioni di aggiornamento di alto livello di download, installazione e applicazione che possono essere una piattaforma o un dispositivo specifico.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Implementazioni degli agenti." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Livello di interfaccia

Il livello di interfaccia è costituito dall'interfaccia di [base di adu](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) e dall'interfaccia per [informazioni sul dispositivo](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

Queste interfacce si basano su un file di configurazione per i valori predefiniti. I valori predefiniti includono aduc_manufacturer e aduc_model per l'interfaccia AzureDeviceUpdateCore e il modello e il produttore per l'interfaccia DeviceInformation. [Altre informazioni](device-update-configuration-file.md) sul file di configurazione.

### <a name="adu-core-interface"></a>Interfaccia di base di ADU

L'interfaccia "ADU core" è il canale di comunicazione principale tra i servizi e l'agente di aggiornamento del dispositivo. [Altre](device-update-plug-and-play.md#adu-core-interface) informazioni su questa interfaccia.

### <a name="device-information-interface"></a>Interfaccia informazioni sul dispositivo

L'interfaccia informazioni sul dispositivo viene utilizzata per implementare l' `Azure IoT PnP DeviceInformation` interfaccia. [Altre](device-update-plug-and-play.md#device-information-interface) informazioni su questa interfaccia.

## <a name="the-platform-layer"></a>Livello piattaforma

Sono disponibili due implementazioni del livello piattaforma. Il livello piattaforma del simulatore ha un'implementazione semplice delle azioni di aggiornamento e viene usato per testare e valutare rapidamente l'aggiornamento dei dispositivi per i servizi dell'hub Internet e l'installazione. Quando l'agente di aggiornamento del dispositivo viene compilato con il livello piattaforma del simulatore, viene fatto riferimento come agente del simulatore di aggiornamento del dispositivo o solo simulatore. [Altre](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) informazioni su come usare l'agente del simulatore. Il livello piattaforma Linux si integra con l' [ottimizzazione della distribuzione](https://github.com/microsoft/do-client) per i download e viene usato nell'immagine di riferimento di Raspberry Pi e in tutti i client in esecuzione su sistemi Linux.

### <a name="simulator-platform-layer"></a>Livello piattaforma del simulatore

L'implementazione del livello piattaforma del simulatore è disponibile in `src/platform_layers/simulator_platform_layer` e può essere usata per il test e la valutazione dell'aggiornamento del dispositivo per l'hub Internet.  Molte delle azioni nell'implementazione del "simulatore" vengono simulate per ridurre le modifiche fisiche per sperimentare l'aggiornamento dei dispositivi per l'hub Internet.  È possibile eseguire un aggiornamento "simulato" end-to-end usando questo livello piattaforma. [Altre](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) informazioni sull'esecuzione dell'agente del simulatore.

### <a name="linux-platform-layer"></a>Livello piattaforma Linux

L'implementazione del livello piattaforma Linux è disponibile in `src/platform_layers/linux_platform_layer` e si integra con il client di [Ottimizzazione recapito](https://github.com/microsoft/do-client/releases) per i download e viene usata nell'immagine di riferimento di Raspberry Pi e in tutti i client in esecuzione su sistemi Linux.

Questo livello può essere integrato con gestori di aggiornamenti diversi per implementare il programma di installazione. Ad esempio, il `SWUpdate` gestore di aggiornamento richiama uno script della Shell per chiamare il `SWUpdate` file eseguibile per eseguire un aggiornamento.

## <a name="update-handlers"></a>Gestori degli aggiornamenti

I gestori di aggiornamento sono componenti che gestiscono il contenuto o le parti specifiche del programma di installazione dell'aggiornamento. Le implementazioni del gestore di aggiornamento sono disponibili in `src/content_handlers` .

### <a name="simulator-update-handler"></a>Gestore di aggiornamento del simulatore

Il gestore di aggiornamento del simulatore viene usato dal livello piattaforma del simulatore e può essere usato con il livello piattaforma Linux per simulare le interazioni con un gestore di contenuto. Il gestore di aggiornamento del simulatore implementa le API del gestore di aggiornamento con la maggior parte delle operazioni No. L'implementazione del gestore di aggiornamento del simulatore è disponibile di seguito:
* [Simulatore di aggiornamento immagini](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Simulatore APT di aggiornamento pacchetti](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Nota: il campo InstalledCriteria nell'interfaccia PnP AzureDeviceUpdateCore deve essere l'hash SHA256 del contenuto. Si tratta dello stesso hash presente nell' [oggetto manifesto di importazione](import-update.md#create-device-update-import-manifest). [Altre](device-update-plug-and-play.md) informazioni su `installedCriteria` e sull' `AzureDeviceUpdateCore` interfaccia.

### <a name="swupdate-update-handler"></a>`SWUpdate` Gestore di aggiornamento

Il `SWUpdate` gestore di aggiornamento "si integra con il `SWUpdate` file eseguibile della riga di comando e altri comandi della Shell per implementare gli aggiornamenti di a/B in modo specifico per l'immagine di riferimento di Raspberry Pi. Trovare la versione più recente dell'immagine di riferimento di Raspberry Pi [qui](https://github.com/Azure/iot-hub-device-update/releases). Il `SWUpdate` gestore di aggiornamento viene implementato in [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>Gestore di aggiornamento APT

Il gestore di aggiornamento APT elabora un manifesto di aggiornamento specifico di APT e richiama APT per installare o aggiornare i pacchetti Debian specificati.

## <a name="self-update-device-update-agent"></a>Aggiornamento automatico dell'agente di aggiornamento del dispositivo

L'agente di aggiornamento del dispositivo e le relative dipendenze possono essere aggiornati tramite l'aggiornamento del dispositivo per la pipeline dell'hub Internet. Se si usa un aggiornamento basato su immagine, includere la versione più recente dell'agente di aggiornamento dispositivi nella nuova immagine. Se si usa un aggiornamento basato su pacchetti, includere l'agente di aggiornamento del dispositivo e la versione desiderata nel manifesto APT come qualsiasi altro pacchetto. [Altre](device-update-apt-manifest.md) informazioni sul manifesto apt. È possibile controllare la versione installata dell'agente di aggiornamento del dispositivo e l'agente di ottimizzazione recapito nella sezione proprietà del dispositivo del dispositivo Internet del [dispositivo](../iot-hub/iot-hub-devguide-device-twins.md). [Altre informazioni sulle proprietà del dispositivo sono disponibili nell'interfaccia di base di adu](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Passaggi successivi
[Informazioni sul file di configurazione dell'agente di aggiornamento del dispositivo](device-update-configuration-file.md)