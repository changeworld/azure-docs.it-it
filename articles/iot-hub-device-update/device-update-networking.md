---
title: Aggiornamento del dispositivo per i requisiti di rete dell'hub Internet | Microsoft Docs
description: L'aggiornamento del dispositivo per l'hub Internet usa un'ampia gamma di porte di rete per scopi diversi.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679584"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Porte usate con l'aggiornamento del dispositivo per l'hub Internet
ADU usa un'ampia gamma di porte di rete per scopi diversi.

## <a name="default-ports"></a>Porte predefinite

Scopo|Numero porta |
---|---
Scaricare da Networks/CDNs  | 80 (protocollo HTTP)
Scarica da MCC/CDNs | 80 (protocollo HTTP)
Connessione dell'agente ADU all'hub di Azure  | 8883 (protocollo MQTT)

## <a name="use-azure-iot-hub-supported-protocols"></a>Usare i protocolli supportati dell'hub Azure.
L'agente ADU può essere modificato in modo da usare uno dei protocolli supportati dell'hub Azure.

[Altre](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) informazioni sull'elenco corrente dei protocolli supportati.
