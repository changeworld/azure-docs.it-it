---
title: includere file
description: includere file
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a3c85a7c76115ff72e5042788ad83fd31f601c9c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96122684"
---
Impostare le variabili di ambiente per configurare l'esempio. Il frammento di codice seguente mostra come impostare le variabili di ambiente al prompt dei comandi di Windows. Se si usa una shell **bash**, sostituire i comandi `set` con i comandi `export`:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<The ID scope you made a note of previously>
set IOTHUB_DEVICE_DPS_DEVICE_ID=sample-device-01
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<The generated device key you made a note of previously>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
```
