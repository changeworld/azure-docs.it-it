---
title: 'Avvio rapido: Configurare e abilitare il modulo di sicurezza per Azure RTOS'
description: Informazioni su come eseguire l'onboarding e abilitare il servizio Modulo di sicurezza per Azure RTOS nell'hub IoT di Azure.
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
ms.openlocfilehash: a11a8ec2d8eb82950cf0ab55eb6ca1913b41c84a
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522938"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Avvio rapido: Modulo di sicurezza per Azure RTOS (anteprima)

Questo articolo illustra i prerequisiti da soddisfare prima di abilitare il servizio Modulo di sicurezza per Azure RTOS in un hub IoT. Se non si ha già un hub IoT, vedere [Creare un hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md) per iniziare.

## <a name="prerequisites"></a>Prerequisiti 

### <a name="supported-devices"></a>Dispositivi supportati

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Scaricare, compilare ed eseguire uno dei file con estensione zip per la scheda e lo strumento specifici (IAR, semi IDE o PC) a scelta dalla [risorsa GitHub del modulo di sicurezza per Azure RTOS](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Risorse di Azure

La fase successiva per iniziare consiste nel preparare le risorse di Azure. È necessario un hub IoT ed è consigliabile usare un'area di lavoro Log Analytics. Per l'hub IoT, è necessaria la stringa di connessione dell'hub IoT per connettersi al dispositivo. 
  
### <a name="iot-hub-connection"></a>Connessione dell'hub IoT

Per iniziare, è necessaria una connessione dell'hub IoT. 

1. Aprire l'**hub IoT** nel portale di Azure.

1. Passare a tutti i **dispositivi**.

1. Selezionare **Crea**.

1. Copiare la stringa di connessione IoT nel [file di configurazione](how-to-azure-rtos-security-module.md).

Le credenziali per le connessioni vengono ricavate dalla configurazione dell'applicazione utente **HOST_NAME**, **DEVICE_ID** e **DEVICE_SYMMETRIC_KEY**.

Il modulo di sicurezza per Azure RTOS usa le connessioni middleware di Azure IoT basate sul protocollo **MQTT**.

## <a name="next-steps"></a>Passaggi successivi

Procedere con l'articolo successivo per completare la configurazione e la personalizzazione della soluzione.

> [!div class="nextstepaction"]
> [Configurare il modulo di sicurezza per Azure RTOS](how-to-azure-rtos-security-module.md)
