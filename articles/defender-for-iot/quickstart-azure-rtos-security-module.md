---
title: 'Guida introduttiva: configurare e abilitare Defender-RTO-micro-Agent per Azure'
description: Informazioni su come eseguire l'onboarding e abilitare il servizio Defender-RTO-micro-Agent per Azure nell'hub Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 3c1af1128b99cbd3263ddffc834eb27ab9dec564
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489846"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos-preview"></a>Guida introduttiva: Defender-RTO-micro-Agent per Azure (anteprima)

Questo articolo fornisce una spiegazione dei prerequisiti prima di iniziare e spiega come abilitare il servizio Defender-RTO-micro-Agent per Azure in un hub Internet. Se non si ha già un hub IoT, vedere [Creare un hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md) per iniziare.

## <a name="prerequisites"></a>Prerequisiti 

### <a name="supported-devices"></a>Dispositivi supportati

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Scaricare, compilare ed eseguire uno dei file con estensione zip per la lavagna e lo strumento specifici (IAR, IDE di semi o PC) di propria scelta dalla [risorsa di GitHub Defender-Internet-micro-Agent per Azure RTO](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Risorse di Azure

La fase successiva per iniziare consiste nel preparare le risorse di Azure. È necessario un hub IoT ed è consigliabile usare un'area di lavoro Log Analytics. Per l'hub IoT, è necessaria la stringa di connessione dell'hub IoT per connettersi al dispositivo. 
  
### <a name="iot-hub-connection"></a>Connessione dell'hub IoT

Per iniziare, è necessaria una connessione dell'hub IoT. 

1. Aprire l'**hub IoT** nel portale di Azure.

1. Passare a tutti i **dispositivi**.

1. Selezionare **Crea**.

1. Copiare la stringa di connessione IoT nel [file di configurazione](how-to-azure-rtos-security-module.md).

Le credenziali per le connessioni vengono ricavate dalla configurazione dell'applicazione utente **HOST_NAME**, **DEVICE_ID** e **DEVICE_SYMMETRIC_KEY**.

Defender-RTO-micro-Agent per Azure usa le connessioni middleware di Azure Internet basato sul protocollo **MQTT** .

## <a name="next-steps"></a>Passaggi successivi

Procedere con l'articolo successivo per completare la configurazione e la personalizzazione della soluzione.

> [!div class="nextstepaction"]
> [Configurare e personalizzare Defender-RTO-micro-Agent per Azure (anteprima)](how-to-azure-rtos-security-module.md)
