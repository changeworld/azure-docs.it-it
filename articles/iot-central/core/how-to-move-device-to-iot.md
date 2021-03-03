---
title: Come spostare un dispositivo in IoT Central di Azure dall'hub Internet
description: Come spostare il dispositivo in IoT Central di Azure dall'hub Internet delle cose
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: f5a7693eeced1028d84598a6db3728776f845f7e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741617"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Come trasferire un dispositivo ad Azure IoT Central dall'hub Internet delle cose

*Le informazioni di questo articolo sono destinate a operatori e sviluppatori di dispositivi.*  

Questo articolo descrive come trasferire un dispositivo a un'applicazione IoT Central di Azure da un hub Internet delle cose. 

Un dispositivo si connette prima a un endpoint DPS per recuperare le informazioni necessarie per la connessione all'applicazione. Internamente, l'applicazione IoT Central usa un hub IoT per gestire la connettività dei dispositivi.  

Un dispositivo può essere connesso direttamente a un hub Internet con una stringa di connessione o usando DPS. Il [servizio Device provisioning (DPS) dell'hub Azure](../../iot-dps/about-iot-dps.md) è la route per IOT Central.

## <a name="to-move-the-device-to-azure-iot-central"></a>Per spostare il dispositivo in Azure IoT Central

Per connettere un dispositivo a IoT Central dall'hub Internet delle cose, è necessario aggiornare un dispositivo con:

* [ID ambito](../../iot-dps/concepts-service.md) dell'applicazione IoT Central.
* Chiave derivata dalla chiave SAS del [gruppo](concepts-get-connected.md) o dal [certificato X. 509](../../iot-hub/iot-hub-x509ca-overview.md)

Per interagire con IoT Central, deve essere presente un modello di dispositivo che modella le proprietà, i dati di telemetria e i comandi implementati dal dispositivo. Per ulteriori informazioni, vedere la pagina relativa [alla connessione a IOT Central](concepts-get-connected.md) e [che cosa sono i modelli di dispositivo?](concepts-device-templates.md)

## <a name="next-steps"></a>Passaggi successivi

Se si è uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:

- Esaminare alcuni esempi di codice che illustrano come usare i token SAS in [esercitazione: creare e connettere un'applicazione client all'applicazione IoT Central di Azure](tutorial-connect-device.md)
- Informazioni su come [connettere i dispositivi con certificati X. 509 usando Node.js SDK per dispositivi per IOT Central applicazione](how-to-connect-devices-x509.md)
- Informazioni su come [monitorare la connettività dei dispositivi con l'interfaccia della riga di comando di Azure](./howto-monitor-devices-azure-cli.md)
- Informazioni su come [definire un nuovo tipo di dispositivo IoT nell'applicazione Azure IoT Central](./howto-set-up-template.md)
- Informazioni sui [dispositivi Azure IoT Edge e su Azure IoT Central](./concepts-iot-edge.md)