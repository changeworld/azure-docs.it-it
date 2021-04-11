---
title: Risoluzione dei problemi dell'hub Azure Internet Error 404001 DeviceNotFound
description: Informazioni su come correggere l'errore 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 09f3c00dadc6e95aae01fb8082fb746e155d4688
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061282"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Questo articolo descrive le cause e le soluzioni per gli errori di **404001 DeviceNotFound** .

## <a name="symptoms"></a>Sintomi

Durante una comunicazione da cloud a dispositivo (C2D), ad esempio il messaggio C2D, l'aggiornamento del dispositivo gemello o il metodo diretto, l'operazione ha esito negativo e restituisce l'errore **404001 DeviceNotFound**.

## <a name="cause"></a>Causa

L'operazione non è riuscita perché il dispositivo non è stato trovato dall'hub. Il dispositivo non è registrato o è disabilitato.

## <a name="solution"></a>Soluzione

Registrare l'ID del dispositivo usato, quindi riprovare.