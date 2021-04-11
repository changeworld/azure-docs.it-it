---
title: Risoluzione dei problemi dell'hub Azure Internet Error 404103 DeviceNotOnline
description: Informazioni su come correggere l'errore 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: ed4341c1c8df588bf735bdc9c531c2165514d610
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061265"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Questo articolo descrive le cause e le soluzioni per gli errori di **404103 DeviceNotOnline** .

## <a name="symptoms"></a>Sintomi

Un metodo diretto a un dispositivo ha esito negativo con l'errore **404103 DeviceNotOnline** anche se il dispositivo è online. 

## <a name="cause"></a>Causa

Se si è certi che il dispositivo è online e si è ancora ricevuto l'errore, è probabile che il callback del metodo diretto non sia registrato nel dispositivo.

## <a name="solution"></a>Soluzione

Per configurare correttamente il dispositivo per i callback di metodi diretti, vedere [gestire un metodo diretto in un dispositivo](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).