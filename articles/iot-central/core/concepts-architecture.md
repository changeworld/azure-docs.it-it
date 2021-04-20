---
title: Concetti dell'architettura di Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi all'architettura di Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: bcda4ca252101ed1505f71a1b5f9fe9a0d8d16b9
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728393"
---
# <a name="azure-iot-central-architecture"></a>Architettura di Azure IoT Central

Questo articolo offre una panoramica dei concetti chiave dell'architettura Azure IoT Central sicurezza.

## <a name="devices"></a>Dispositivi

I dispositivi scambiano dati con l'applicazione Azure IoT Central. Un dispositivo può:

- Inviare misure, ad esempio dati di telemetria.
- Sincronizzare le impostazioni con l'applicazione.

In Azure IoT Central, i dati che un dispositivo può scambiare con l'applicazione sono specificati in un modello di dispositivo. Per altre informazioni sui modelli di dispositivo, vedere [Modelli di dispositivo.](concepts-device-templates.md)

Per altre informazioni sul modo in cui i dispositivi si connettono all'applicazione Azure IoT Central, vedere [Connettività dei dispositivi](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Dispositivi Azure IoT Edge

Oltre ai dispositivi creati tramite [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks), è anche possibile connettere [dispositivi Azure IoT Edge](../../iot-edge/about-iot-edge.md) a un'applicazione IoT Central. IoT Edge consente di eseguire l'intelligence per il cloud e la logica personalizzata direttamente nei dispositivi IoT gestiti da IoT Central. Il runtime di IoT Edge consente di:

- Installare e aggiornare i carichi di lavoro nel dispositivo.
- Mantenere IoT Edge standard di sicurezza nel dispositivo.
- Assicurare che i moduli di IoT Edge siano sempre in esecuzione.
- Segnalare l'integrità dei moduli al cloud per il monitoraggio remoto.
- Gestire la comunicazione tra i dispositivi foglia downstream e un dispositivo IoT Edge, tra i moduli in un dispositivo IoT Edge e tra un dispositivo IoT Edge e il cloud.

![Azure IoT Central con Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central abilita le funzionalità seguenti a per IoT Edge dispositivi:

- Modelli di dispositivo per descrivere le funzionalità di un IoT Edge dispositivo, ad esempio:
  - Funzionalità di caricamento del manifesto della distribuzione, che consente di gestire un manifesto per una flotta di dispositivi.
  - Moduli eseguiti nel IoT Edge dispositivo.
  - Dati di telemetria inviati da ogni modulo.
  - Proprietà segnalate da ogni modulo.
  - Comandi a cui risponde ogni modulo.
  - Relazioni tra un dispositivo gateway IoT Edge dispositivo gateway e un dispositivo downstream.
  - Proprietà cloud che non vengono archiviate nel IoT Edge dispositivo.
  - Personalizzazioni, dashboard e moduli che fanno parte dell'applicazione IoT Central.

  Per altre informazioni, vedere [l'articolo Connettere Azure IoT Edge dispositivi a un Azure IoT Central app applicazione.](./concepts-iot-edge.md)

- Possibilità di effettuare il provisioning IoT Edge dispositivi su larga scala usando Azure IoT provisioning di dispositivi
- Regole e azioni.
- Dashboard e analisi personalizzati.
- Esportazione continua dei dati di telemetria IoT Edge dispositivi.

### <a name="iot-edge-device-types"></a>IoT Edge tipi di dispositivi

IoT Central classifica IoT Edge tipi di dispositivo come segue:

- Dispositivi foglia. Un IoT Edge può avere dispositivi foglia downstream, ma non ne viene eseguito il provisioning in IoT Central.
- Dispositivi gateway con dispositivi downstream. Il provisioning del dispositivo gateway e dei dispositivi downstream viene effettuato in IoT Central

![IoT Central con IoT Edge panoramica](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge modelli

IoT Central supporta i modelli di IoT Edge seguenti:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge come dispositivo foglia

![IoT Edge come dispositivo foglia](./media/concepts-architecture/edgeasleafdevice.png)

Il IoT Edge viene effettuato il provisioning in IoT Central e tutti i dispositivi downstream e i relativi dati di telemetria vengono rappresentati come provenienti dal IoT Edge dispositivo. Non viene effettuato il provisioning dei dispositivi downstream IoT Edge dispositivo IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge dispositivo gateway connesso a dispositivi downstream con identità

![IoT Edge con l'identità del dispositivo downstream](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Il IoT Edge viene effettuato il provisioning in IoT Central insieme ai dispositivi downstream connessi al dispositivo IoT Edge dispositivo. Il supporto di runtime per il provisioning di dispositivi downstream tramite il gateway non è attualmente supportato.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge dispositivo gateway connesso ai dispositivi downstream con identità fornita dal IoT Edge gateway

![IoT Edge con dispositivo downstream senza identità](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Il IoT Edge viene effettuato il provisioning in IoT Central insieme ai dispositivi downstream connessi al dispositivo IoT Edge dispositivo. Il supporto di runtime del gateway che fornisce identità ai dispositivi downstream e il provisioning dei dispositivi downstream non è attualmente supportato. Se si porta il proprio modulo di conversione delle identità, IoT Central può supportare questo modello.

## <a name="cloud-gateway"></a>Gateway cloud

Azure IoT Central usa l'hub IoT come gateway del cloud che permette la connettività dei dispositivi. L'hub IoT consente:

- Inserimento dati su larga scala nel cloud.
- Gestione di dispositivi.
- Connettività dei dispositivi sicura.

Per altre informazioni sull'hub IoT, vedere [Documentazione sull'hub IoT](../../iot-hub/index.yml).

Per altre informazioni sulla connettività dei dispositivi in Azure IoT Central, vedere [Connettività dei dispositivi](concepts-get-connected.md).

## <a name="data-stores"></a>Archivi dati

Azure IoT Central archivia i dati dell'applicazione nel cloud. I dati archiviati dell'applicazione comprendono:

- Modelli di dispositivo.
- Identità dei dispositivi.
- Metadati dei dispositivi.
- Dati di utenti e ruoli.

Azure IoT Central usa un archivio di serie temporali per i dati di misura inviati dai dispositivi. Dati di serie temporali dei dispositivi utilizzati dal servizio di analisi.

## <a name="data-export"></a>Esportazione dati

In un Azure IoT Central appaltatore è possibile [esportare](howto-export-data.md) continuamente i dati nelle proprie istanze Hub eventi di Azure e bus di servizio di Azure dati. È anche possibile esportare periodicamente i dati nell'account di archiviazione BLOB di Azure. IoT Central possibile esportare misure, dispositivi e modelli di dispositivo.

## <a name="batch-device-updates"></a>Aggiornamenti dei dispositivi batch

In un'Azure IoT Central è possibile creare ed [eseguire processi per](howto-run-a-job.md) gestire i dispositivi connessi. Questi processi consentono di eseguire aggiornamenti in blocco alle proprietà o alle impostazioni del dispositivo o di eseguire comandi. Ad esempio, è possibile creare un processo per aumentare la velocità della ventola per più distributori automatici refrigerati.

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo

Ogni IoT Central applicazione dispone di un proprio sistema di controllo degli accessi in base al ruolo incorporato. Un [amministratore può definire regole di accesso](howto-manage-users-roles.md) per un Azure IoT Central app applicazione usando uno dei ruoli predefiniti o creando un ruolo personalizzato. I ruoli determinano le aree dell'applicazione a cui un utente può accedere e le azioni che può eseguire.

## <a name="security"></a>Security

Le funzionalità di sicurezza all'interno di Azure IoT Central includono:

- I dati sono crittografati quando sono in transito o inattivi.
- L'autenticazione viene fornita tramite Azure Active Directory o l'account Microsoft. È supportata l'autenticazione a due fattori.
- Isolamento del tenant completo.
- Sicurezza a livello di dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le informazioni sull'architettura di Azure IoT Central, il passaggio successivo consigliato consiste nell'apprendere la connettività dei dispositivi [in](concepts-get-connected.md) Azure IoT Central.