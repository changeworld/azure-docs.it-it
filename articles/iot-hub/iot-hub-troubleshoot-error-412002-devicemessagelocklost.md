---
title: Risoluzione dei problemi dell'hub Azure Internet Error 412002 DeviceMessageLockLost
description: Informazioni su come correggere l'errore 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 53364009f9b9c041c39728e438c3e24eacfd1665
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435478"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Questo articolo descrive le cause e le soluzioni per gli errori di **412002 DeviceMessageLockLost** .

## <a name="symptoms"></a>Sintomi

Quando si tenta di inviare un messaggio da cloud a dispositivo, la richiesta ha esito negativo con l'errore **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Causa

Quando un dispositivo riceve un messaggio da cloud a dispositivo dalla coda (ad esempio, usando), [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync) il messaggio viene bloccato dall'hub Internet per una durata di timeout del blocco di un minuto. Se il dispositivo tenta di completare il messaggio dopo la scadenza del timeout di blocco, l'hub Internet genera questa eccezione.

## <a name="solution"></a>Soluzione

Se l'hub delle cose non riceve la notifica entro la durata del timeout di blocco di un minuto, il messaggio viene reimpostato sullo stato *accodato* . Il dispositivo può tentare nuovamente di ricevere il messaggio. Per evitare che l'errore si verifichi in futuro, implementare la logica lato dispositivo per completare il messaggio entro un minuto dalla ricezione del messaggio. Non è possibile modificare questo timeout di un minuto.