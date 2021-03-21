---
title: Procedure consigliate per lo sviluppo di dispositivi in Azure IoT Central | Microsoft Docs
description: Questo articolo illustra le procedure consigliate per la connettività dei dispositivi in Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054708"
---
# <a name="best-practices-for-device-development"></a>Procedure consigliate per lo sviluppo di dispositivi

*Questo articolo si applica agli sviluppatori di dispositivi.*

Questi consigli illustrano come implementare i dispositivi per sfruttare i vantaggi del ripristino di emergenza incorporato e della scalabilità automatica in IoT Central.

L'elenco seguente illustra il flusso di alto livello quando un dispositivo si connette a IoT Central:

1. Usare DPS per eseguire il provisioning del dispositivo e ottenere una stringa di connessione del dispositivo.

1. Usare la stringa di connessione per connettere l'endpoint dell'hub degli elementi interni di IoT Central. Inviare dati e ricevere dati dall'applicazione IoT Central.

1. Se il dispositivo ottiene errori di connessione, a seconda del tipo di errore, ritentare la connessione o effettuare nuovamente il provisioning del dispositivo.

## <a name="use-dps-to-provision-the-device"></a>Usare DPS per eseguire il provisioning del dispositivo

Per eseguire il provisioning di un dispositivo con DPS, usare l'ID ambito, le credenziali e l'ID dispositivo dell'applicazione IoT Central. Per altre informazioni sui tipi di credenziali, vedere registrazione del [gruppo X. 509](concepts-get-connected.md#x509-group-enrollment) e [registrazione del gruppo SAS](concepts-get-connected.md#sas-group-enrollment). Per altre informazioni sugli ID dispositivo, vedere [registrazione del dispositivo](concepts-get-connected.md#device-registration).

In seguito all'esito positivo, DPS restituisce una stringa di connessione che il dispositivo può usare per connettersi all'applicazione IoT Central. Per risolvere gli errori di provisioning, vedere [controllare lo stato del provisioning del dispositivo](troubleshoot-connection.md#check-the-provisioning-status-of-your-device).

Il dispositivo può memorizzare nella cache la stringa di connessione da usare per le connessioni successive. Tuttavia, il dispositivo deve essere pronto a [gestire gli errori di connessione](#handle-connection-failures).

## <a name="connect-to-iot-central"></a>Connetti a IoT Central

Usare la stringa di connessione per connettere l'endpoint dell'hub degli elementi interni di IoT Central. La connessione consente di inviare dati di telemetria all'applicazione IoT Central, sincronizzare i valori delle proprietà con l'applicazione IoT Central e rispondere ai comandi inviati dall'applicazione IoT Central.

## <a name="handle-connection-failures"></a>Gestione degli errori di connessione

Ai fini della scalabilità o del ripristino di emergenza, IoT Central possibile aggiornare l'hub Internet delle cose sottostante. Per mantenere la connettività, il codice del dispositivo deve gestire errori di connessione specifici stabilendo una connessione al nuovo endpoint dell'hub.

Se il dispositivo riceve uno degli errori seguenti quando si connette, deve ripetere il passaggio di provisioning con DPS per ottenere una nuova stringa di connessione. Questi errori indicano che la stringa di connessione usata dal dispositivo non è più valida:

- Endpoint dell'hub di Internet delle cose irraggiungibile.
- Token di sicurezza scaduto.
- Dispositivo disabilitato nell'hub Internet.

Se il dispositivo riceve uno degli errori seguenti quando si connette, deve usare una strategia di back-off per ritentare la connessione. Questi errori indicano che la stringa di connessione usata dal dispositivo è ancora valida, ma le condizioni transitorie arrestano la connessione del dispositivo:

- Dispositivo bloccato dall'operatore.
- Errore interno 500 dal servizio.

Per altre informazioni sui codici di errore del dispositivo, vedere [risoluzione dei problemi relativi alle connessioni del dispositivo](troubleshoot-connection.md).

## <a name="next-steps"></a>Passaggi successivi

Se si è uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:

- Esaminare alcuni esempi di codice che illustrano come usare i token SAS in [esercitazione: creare e connettere un'applicazione client all'applicazione IoT Central di Azure](tutorial-connect-device.md)
- Informazioni su come [connettere i dispositivi con certificati X. 509 usando Node.js SDK per dispositivi per IOT Central applicazione](how-to-connect-devices-x509.md)
- Informazioni su come [monitorare la connettività dei dispositivi con l'interfaccia della riga di comando di Azure](./howto-monitor-devices-azure-cli.md)
- Informazioni su come [definire un nuovo tipo di dispositivo IoT nell'applicazione Azure IoT Central](./howto-set-up-template.md)
- Informazioni sui [dispositivi Azure IoT Edge e su Azure IoT Central](./concepts-iot-edge.md)
