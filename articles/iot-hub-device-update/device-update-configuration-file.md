---
title: Informazioni sull'aggiornamento del dispositivo per il file di configurazione dell'hub Azure Microsoft Docs
description: Informazioni sull'aggiornamento del dispositivo per il file di configurazione dell'hub Azure.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101662944"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>Aggiornamento del dispositivo per il file di configurazione dell'hub Internet

"adu-conf.txt" è un file facoltativo che è possibile creare per gestire le configurazioni seguenti.

* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["produttore"]
* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["modello"]
* DeviceInformation. produttore
* DeviceInformation. Model
* Stringa di connessione del dispositivo (se non è nota dall'agente di aggiornamento del dispositivo).

## <a name="purpose"></a>Scopo
L'agente di aggiornamento dispositivi tenterà prima di tutto di ottenere i `manufacturer` `model` valori e dal dispositivo da usare per il [livello di interfaccia](device-update-agent-overview.md#the-interface-layer). Se l'operazione ha esito negativo, l'agente di aggiornamento del dispositivo cercherà il file "adu-conf.txt" e utilizzerà i valori da questa posizione. Se entrambi i tentativi hanno esito negativo, l'agente di aggiornamento del dispositivo utilizzerà i valori [predefiniti](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) .

Altre informazioni sull'interfaccia di [base di adu](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) e [sull'interfaccia informazioni sul dispositivo](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

## <a name="file-location"></a>Percorso del file

Nel sistema Linux, nella partizione o nel disco denominato `adu` , creare un file di testo denominato "adu-conf.txt" con i campi seguenti.

## <a name="list-of-fields"></a>Elenco di campi

|Nome|Descrizione|
|-----------|--------------------|
|connection_string|Stringa di connessione di cui è stato eseguito il pre-provisioning. il dispositivo può usare per connettersi all'hub Internet. Nota: non è necessario se si esegue il provisioning dell'agente di aggiornamento dispositivi tramite il [servizio di identità Azure](https://azure.github.io/iot-identity-service/) .|
|aduc_manufacturer|Segnalato dall' `AzureDeviceUpdateCore:4.ClientMetadata:4` interfaccia per classificare il dispositivo per la distribuzione degli aggiornamenti.|
|aduc_model|Segnalato dall' `AzureDeviceUpdateCore:4.ClientMetadata:4` interfaccia per classificare il dispositivo per la distribuzione degli aggiornamenti.|
|manufacturer|Segnalato dall'agente di aggiornamento del dispositivo come parte dell' `DeviceInformation` interfaccia.|
|model|Segnalato dall'agente di aggiornamento del dispositivo come parte dell' `DeviceInformation` interfaccia.|

## <a name="example-adu-conftxt-file-contents"></a>Contenuto del file "adu-conf.txt" di esempio

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
