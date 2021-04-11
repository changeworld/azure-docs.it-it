---
title: Risoluzione dei problemi dell'hub Azure Internet Error 409002 LinkCreationConflict
description: Informazioni su come correggere l'errore 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: c354eed6e7bf9f2147f8b81a82a06e80a9337c84
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061044"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

Questo articolo descrive le cause e le soluzioni per gli errori di **409002 LinkCreationConflict** .

## <a name="symptoms"></a>Sintomi

Viene visualizzato l'errore **409002 LinkCreationConflict** nei log insieme alla disconnessione del dispositivo o a un errore del messaggio da cloud a dispositivo.

<!-- When using AMQP? -->

## <a name="cause"></a>Causa

In genere, questo errore si verifica quando l'hub Internet rileva che un client ha più di una connessione. Infatti, quando arriva una nuova richiesta di connessione per un dispositivo con una connessione esistente, l'hub Internet chiude la connessione esistente con questo errore.

### <a name="cause-1"></a>Causa 1

Nel caso più comune, un problema separato, ad esempio [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md), causa la disconnessione del dispositivo. Il dispositivo tenta di ristabilire immediatamente la connessione, ma l'hub Internet considera ancora il dispositivo connesso. L'hub IoT chiude la connessione precedente e registra questo errore.

### <a name="cause-2"></a>Causa 2

La logica del lato dispositivo non funzionante causa la connessione del dispositivo quando ne è già aperta una.

## <a name="solution"></a>Soluzione

In genere, questo errore si verifica come effetto collaterale di un altro problema temporaneo. Per risolvere il problema, cercare l'errore nei log. In alternativa, assicurarsi di generare una nuova richiesta di connessione solo se la connessione viene interrotta.
