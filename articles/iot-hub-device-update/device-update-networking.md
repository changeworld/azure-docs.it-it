---
title: Aggiornamento del dispositivo per i requisiti di rete dell'hub Internet | Microsoft Docs
description: L'aggiornamento del dispositivo per l'hub Internet usa un'ampia gamma di porte di rete per scopi diversi.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558381"
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

[Altre](../iot-hub/iot-hub-devguide-protocols.md) informazioni sull'elenco corrente dei protocolli supportati.
