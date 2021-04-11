---
title: Risoluzione dei problemi dell'hub Azure Internet Error 409001 DeviceAlreadyExists
description: Informazioni su come correggere l'errore 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b075519fff2c7c328778d770ef68e9751922807b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061129"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Questo articolo descrive le cause e le soluzioni per gli errori di **409001 DeviceAlreadyExists** .

## <a name="symptoms"></a>Sintomi

Quando si tenta di registrare un dispositivo nell'hub Internet, la richiesta ha esito negativo con l'errore **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Causa

Nell'hub Internet è già presente un dispositivo con lo stesso ID dispositivo. 

## <a name="solution"></a>Soluzione

Usare un ID dispositivo diverso e riprovare.