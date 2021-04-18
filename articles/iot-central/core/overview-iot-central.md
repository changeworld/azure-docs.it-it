---
title: Informazioni su Azure IoT Central | Microsoft Docs
description: IoT Central è una piattaforma di app IoT ospitata sicura, scalabile con l'azienda man mano che l'azienda cresce e si integra con le app aziendali esistenti. Questo articolo offre una panoramica delle funzionalità di Azure IoT Central.
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 1ff8243f9f4d070592ae6dc99e7c33472a6a90de
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600443"
---
# <a name="what-is-azure-iot-central"></a>Informazioni su Azure IoT Central

IoT Central è una piattaforma di app IoT ospitata sicura, scalabile con l'azienda man mano che l'azienda cresce e si integra con le app aziendali esistenti. La scelta di compilare con IoT Central consente di concentrare tempo, denaro ed energie per trasformare il business con dati IoT, anziché semplicemente gestire e aggiornare un'infrastruttura IoT complessa e in continua evoluzione.

IoT Central consente di connettere rapidamente i dispositivi, monitorare le condizioni dei dispositivi, creare regole e gestire milioni di dispositivi e i relativi dati per tutto il ciclo di vita. Consente anche di agire sulle informazioni dettagliate sui dispositivi estendendo l'intelligence IoT alle applicazioni line-of-business.

## <a name="create-your-iot-central-application"></a>Creare l'applicazione IoT Central

È possibile creare rapidamente una nuova applicazione IoT Central e quindi personalizzarla in base alle esigenze specifiche. È possibile iniziare con un modello di applicazione _generico_ o con uno dei modelli di applicazione incentrati sul settore per [retail,](../retail/overview-iot-central-retail.md) [energy,](../energy/overview-iot-central-energy.md) [government](../government/overview-iot-central-government.md)o [healthcare.](../healthcare/overview-iot-central-healthcare.md)

Per una [procedura di creazione della prima applicazione,](quick-deploy-iot-central.md) vedere l'avvio rapido Creare una nuova applicazione.

## <a name="connect-your-devices"></a>Connettere i dati dell'utente
Dopo aver creato l'applicazione, il primo passaggio consiste nel connettere i dispositivi. Per [un'introduzione alla connessione](./overview-iot-central-developer.md) dei dispositivi all'applicazione IoT Central dispositivi, vedere Panoramica dello sviluppo di dispositivi.

### <a name="device-templates"></a>Modelli di dispositivo

I dispositivi IoT Central sono associati a un modello _di dispositivo_. Un modello di dispositivo è simile a un progetto: definisce le caratteristiche e i comportamenti dei dispositivi, ad esempio:

- Telemetrie, che rappresentano le misurazioni dei sensori, ad esempio temperatura o umidità.
- Proprietà, che rappresentano lo stato durevole di un dispositivo. Ad esempio, lo stato di una pompa refrigeratore o la temperatura di destinazione per un dispositivo. È possibile dichiarare le proprietà in sola lettura o in scrittura. Solo i dispositivi possono aggiornare il valore di una proprietà di sola lettura. Un operatore può impostare il valore di una proprietà scrivibile da inviare a un dispositivo.
- Comandi, operazioni che possono essere attivate in un dispositivo, ad esempio un comando per riavviare in remoto un dispositivo.
- Proprietà cloud, ovvero i metadati del dispositivo da archiviare nell'applicazione IoT Central, ad esempio l'indirizzo del cliente o la data dell'ultima assistenza.

Per altre [informazioni, vedere](howto-set-up-template.md) l'articolo Creare un modello di dispositivo.

### <a name="customize-the-ui"></a>Personalizzare l'interfaccia utente

È possibile personalizzare l'IoT Central per gli operatori responsabili dell'uso quotidiano dell'applicazione, ad esempio:

- Configurazione di dashboard personalizzati per consentire agli operatori di individuare informazioni dettagliate e risolvere i problemi più velocemente.


## <a name="manage-your-devices"></a>Gestire i dispositivi


Con qualsiasi soluzione IoT progettata per operare su larga scala, è importante adottare un approccio strutturato alla gestione dei dispositivi. Non è sufficiente connettere i dispositivi al cloud, ma è necessario che rimangano connessi e integri.

È possibile [gestire i dispositivi usando](howto-manage-devices.md) l'applicazione IoT Central per eseguire attività quali:

- Monitoraggio dei dispositivi.
- Risolvere e correggere i problemi relativi ai dispositivi.
- Eseguire aggiornamenti in blocco nei dispositivi.

### <a name="dashboards"></a>Dashboard

I [dashboard](./howto-set-up-template.md#generate-default-views) predefiniti forniscono un'interfaccia utente personalizzabile per monitorare l'integrità e i dati di telemetria dei dispositivi. È possibile iniziare con un dashboard predefinito in un [modello di applicazione](howto-use-app-templates.md) o creare dashboard personalizzati in base alle esigenze degli operatori. È possibile condividere i dashboard con tutti gli utenti dell'applicazione o mantenerli privati.

### <a name="rules-and-actions"></a>Regole e azioni

Creare [regole personalizzate](tutorial-create-telemetry-rules.md) in base allo stato e ai dati di telemetria dei dispositivi per identificare quelli che richiedono attenzione. Configurare azioni per avvisare le persone appropriate e assicurarsi che vengano tempestivamente intraprese misure correttive.

### <a name="jobs"></a>Processi

I [processi](howto-run-a-job.md) consentono di applicare aggiornamenti singoli o in blocco ai dispositivi impostando proprietà o chiamando comandi.

### <a name="analytics"></a>Analytics
[Analytics](howto-create-analytics.md) espone funzionalità avanzate per analizzare le tendenze cronologiche e correlare varie telemetrie dai dispositivi.

## <a name="integrate-with-other-services"></a>Integrazione con altri servizi

Essendo una piattaforma applicativa, IoT Central consente di trasformare i dati IoT in informazioni aziendali dettagliate che generano risultati azionabili. Le [regole](./tutorial-create-telemetry-rules.md), l'[esportazione di dati](./howto-export-data.md) e l'[API REST pubblica](/learn/modules/manage-iot-central-apps-with-rest-api/) sono esempi delle possibilità offerte per integrare IoT Central con le applicazioni line-of-business:

![Come trasformare i dati IoT con IoT Central](media/overview-iot-central/transform.png)

È possibile generare informazioni aziendali dettagliate, ad esempio per determinare le tendenze di efficienza dei macchinari o prevedere il consumo energetico futuro in una fabbrica, creando pipeline di analisi personalizzate per elaborare i dati di telemetria provenienti dai dispositivi e archiviare i risultati. Configurare le esportazioni di dati nell'applicazione IoT Central per esportare i dati in altri servizi in cui è possibile analizzare, archiviare e visualizzare i dati con gli strumenti preferiti.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Creare soluzioni IoT e integrazioni personalizzate con le API REST

Creare soluzioni IoT come:

- App specifiche per dispositivi mobili in grado di configurare e controllare i dispositivi in remoto.
- Integrazioni personalizzate che consentono l'interazione delle attuali applicazioni line-of-business con i dispositivi e i dati IoT.
- Applicazioni di gestione per la modellazione, l'onboarding e l'accesso ai dati dei dispositivi.

## <a name="administer-your-application"></a>Amministrare l'applicazione

Le applicazioni IoT Central sono completamente ospitate in Microsoft e ciò consente di ridurre il carico amministrativo correlato alla gestione delle applicazioni. Gli amministratori gestiscono l'accesso all'applicazione con [regole e autorizzazioni utente](howto-administer.md).

## <a name="pricing"></a>Prezzi

È possibile creare un'IoT Central usando una versione di valutazione gratuita di 7 giorni o un piano tariffario standard.

- Le applicazioni con il piano *gratuito* sono gratuite per sette giorni e supportano fino a cinque dispositivi. È possibile convertirle per l'uso di un piano tariffario standard in qualsiasi momento prima della scadenza.
- Le applicazioni create usando il *piano standard* vengono fatturate in base al dispositivo. È possibile scegliere il piano tariffario **Standard 0,** **Standard 1** o **Standard 2** con i primi due dispositivi gratuiti. Altre informazioni sui [prezzi di IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver ottenuto una panoramica di IoT Central, vengono consigliati alcuni passaggi successivi:

- Iniziare a [creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).
- Acquisire familiarità con l'[interfaccia utente di Azure IoT Central](overview-iot-central-tour.md).
- Se si è uno sviluppatore di dispositivi e si vuole approfondire il codice, creare e connettere un'applicazione [client all'Azure IoT Central.](./tutorial-connect-device.md)
