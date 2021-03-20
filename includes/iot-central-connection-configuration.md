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
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017501"
---
Quando si eseguirà l'applicazione del dispositivo di esempio più avanti in questa esercitazione, saranno necessari i valori di configurazione seguenti:

* Ambito ID: Nell'applicazione IoT Central passare a **Amministrazione > Connessione dispositivo**. Prendere nota del valore dell'**Ambito ID**.
* Chiave primaria del gruppo: Nell'applicazione IoT Central passare a **Amministrazione > Connessione dispositivo > SAS-IoT-Devices**. Prendere nota del valore **Chiave primaria** della firma di accesso condivisa.

Usare Cloud Shell per generare una chiave del dispositivo dalla chiave SAS del gruppo appena recuperata:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

Prendere nota della chiave dispositivo generata. Questo valore verrà usato in un passaggio successivo dell'esercitazione.
