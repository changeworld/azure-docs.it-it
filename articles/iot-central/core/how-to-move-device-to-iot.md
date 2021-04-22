---
title: Come spostare un dispositivo in un Azure IoT Central dall'hub IoT
description: Come spostare il dispositivo in Azure IoT Central dall'hub IoT
author: philmea
ms.author: philmea
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 79aead5b374714e7856897a9b85349198341cb3d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872732"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Come trasferire un dispositivo all'Azure IoT Central dall'hub IoT

*Le informazioni di questo articolo sono destinate a operatori e sviluppatori di dispositivi.*  

Questo articolo descrive come trasferire un dispositivo a un'applicazione Azure IoT Central da un hub IoT. 

Un dispositivo si connette prima a un endpoint DPS per recuperare le informazioni necessarie per la connessione all'applicazione. Internamente, l'applicazione IoT Central usa un hub IoT per gestire la connettività dei dispositivi.  

Un dispositivo può essere connesso a un hub IoT direttamente usando una stringa di connessione o DPS. [hub IoT di Azure servizio Device Provisioning (DPS)](../../iot-dps/about-iot-dps.md) è la route per IoT Central.

## <a name="to-move-the-device-to-azure-iot-central"></a>Per spostare il dispositivo in Azure IoT Central

Per connettere un dispositivo a IoT Central dall'hub IoT, è necessario aggiornare un dispositivo con:

* ID [ambito dell'IoT Central](../../iot-dps/concepts-service.md) applicazione.
* Una chiave derivata dalla chiave di [firma di accesso](concepts-get-connected.md) condiviso del gruppo o dal certificato [X.509](../../iot-hub/iot-hub-x509ca-overview.md)

Per interagire con IoT Central, deve essere presente un modello di dispositivo che modella le proprietà, i dati di telemetria e i comandi implementati dal dispositivo. Per altre informazioni, vedere [Ottenere la connessione a IoT Central](concepts-get-connected.md) e Che cosa sono i modelli di [dispositivo?](concepts-device-templates.md)

## <a name="next-steps"></a>Passaggi successivi

Se si è uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:

- Esaminare il codice di esempio che illustra come usare i token di firma di accesso condiviso in Esercitazione: Creare e connettere un'applicazione [client all'applicazione Azure IoT Central servizio](tutorial-connect-device.md)
- Informazioni su come [connettere i dispositivi con certificati X.509](how-to-connect-devices-x509.md) usando Node.js SDK per dispositivi per IoT Central app IoT Central
- Informazioni su come [monitorare la connettività dei dispositivi con l'interfaccia della riga di comando di Azure](./howto-monitor-devices-azure-cli.md)
- Informazioni su come [definire un nuovo tipo di dispositivo IoT nell'applicazione Azure IoT Central](./howto-set-up-template.md)
- Informazioni sui [dispositivi Azure IoT Edge e su Azure IoT Central](./concepts-iot-edge.md)