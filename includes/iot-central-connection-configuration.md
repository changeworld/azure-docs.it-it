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
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491161"
---
Quando si eseguirà l'applicazione del dispositivo di esempio più avanti in questa esercitazione, saranno necessari i valori di configurazione seguenti:

* Ambito ID: Nell'applicazione IoT Central passare a **Amministrazione > Connessione dispositivo**. Prendere nota del valore dell'**Ambito ID**.
* Chiave primaria del gruppo: Nell'applicazione IoT Central passare a **Amministrazione > Connessione dispositivo > SAS-IoT-Devices**. Prendere nota del valore **Chiave primaria** della firma di accesso condivisa.

Usare il Cloud Shell per generare una chiave del dispositivo dalla chiave primaria del gruppo recuperata:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

Prendere nota della chiave dispositivo generata. Questo valore verrà usato in un passaggio successivo dell'esercitazione.
