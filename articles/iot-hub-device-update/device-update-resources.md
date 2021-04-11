---
title: Informazioni sull'aggiornamento del dispositivo per le risorse dell'hub Azure Microsoft Docs
description: Informazioni sull'aggiornamento del dispositivo per le risorse dell'hub Azure
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663645"
---
# <a name="device-update-resources"></a>Risorse aggiornamento dispositivo

Per usare l'aggiornamento del dispositivo per l'hub Internet, è necessario creare un account di aggiornamento del dispositivo e una risorsa dell'istanza. 

## <a name="device-update-account"></a>Account aggiornamento dispositivo

Un account di aggiornamento del dispositivo è una risorsa creata nella sottoscrizione di Azure. Al livello dell'account di aggiornamento del dispositivo è possibile selezionare l'area in cui verrà creato l'account di aggiornamento del dispositivo. È anche possibile impostare le autorizzazioni per autorizzare gli utenti che avranno accesso all'aggiornamento del dispositivo.


## <a name="device-update-instance"></a>Istanza di aggiornamento del dispositivo
Dopo la creazione di un account, è necessario creare un'istanza di aggiornamento del dispositivo. Un'istanza è un contenitore logico che contiene gli aggiornamenti e le distribuzioni associati a un hub. L'aggiornamento del dispositivo usa l'hub Internet delle cose come una directory del dispositivo e un canale di comunicazione con i dispositivi. 

Durante l'anteprima pubblica, è possibile creare due account di aggiornamento del dispositivo per ogni sottoscrizione. Inoltre, è possibile creare due istanze di aggiornamento del dispositivo per ogni account.

## <a name="configuring-device-update-linked-iot-hub"></a>Configurazione dell'hub Internet degli aggiornamenti del dispositivo 

Per consentire all'aggiornamento del dispositivo di ricevere le notifiche di modifica dall'hub Internet, l'aggiornamento del dispositivo si integra con l'hub eventi "predefinito". Facendo clic sul pulsante "configura Hub Internet" all'interno dell'istanza vengono configurate le route di messaggi e i criteri di accesso necessari per comunicare con i dispositivi Internet. 

Per l'aggiornamento del dispositivo sono configurate le route di messaggi seguenti:

|   Nome di route    | Query di routing  | Descrizione  |
| :--------- | :---- |:---- |
|  DeviceUpdate. DigitalTwinChanges | true |Ascolta gli eventi delle modifiche del dispositivo gemello digitale  |
|  DeviceUpdate. DeviceLifeCycle | opType =' deleteDeviceIdentity '  | Ascolta i dispositivi eliminati |
|  DeviceUpdate. TelemetryModelInformation | iothub-Interface-ID = "urn: azureiot: ModelDiscovery: ModelInformation: 1 | Ascolta i nuovi tipi di dispositivi |
|  DeviceUpdate. DeviceTwinEvents| (opType =' updateTwin'' o opType =' replaceTwin') E IS_DEFINED ($body. Tags. ADUGroup) | Ascolta i nuovi gruppi di aggiornamento del dispositivo |

## <a name="next-steps"></a>Passaggi successivi

[Creare risorse di aggiornamento del dispositivo](./create-device-update-account.md)
