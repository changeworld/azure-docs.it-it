---
title: Informazioni su Azure IoT Central | Microsoft Docs
description: Azure IoT Central è una piattaforma applicativa che semplifica la creazione di soluzioni IoT e contribuisce a ridurre il carico di lavoro e i costi delle operazioni di gestione e dello sviluppo IoT. Questo articolo offre una panoramica delle funzionalità di Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: abf5f601bb98ab7e03b25f6ab9bd06cb051a1112
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589066"
---
# <a name="what-is-azure-iot-central"></a>Informazioni su Azure IoT Central

IoT Central è una piattaforma applicativa di Internet delle cose che riduce il carico di attività e i costi di sviluppo, gestione e manutenzione di soluzioni IoT di livello aziendale. La scelta di compilare con IoT Central consente di concentrare tempo, denaro ed energie per trasformare il business con dati IoT, anziché semplicemente gestire e aggiornare un'infrastruttura IoT complessa e in continua evoluzione.

L'interfaccia utente Web consente di connettere rapidamente i dispositivi, monitorare le condizioni dei dispositivi, creare regole e gestire milioni di dispositivi e i relativi dati per tutto il ciclo di vita. Consente anche di agire sulle informazioni dettagliate sui dispositivi estendendo l'intelligence IoT alle applicazioni line-of-business.

Per IoT Central, questo articolo descrive:

- Ruoli utente tipici associati a un progetto.
- Procedura per la creazione dell'applicazione.
- Procedura per la connessione di dispositivi all'applicazione.
- Procedura per la gestione dell'applicazione.
- Funzionalità Azure IoT Edge in IoT Central.
- Come connettere i dispositivi con runtime Azure IoT Edge alla propria applicazione.

## <a name="user-roles"></a>Ruoli utente

La IoT Central fa riferimento a quattro ruoli utente che interagiscono con un'IoT Central applicazione:

- Uno _sviluppatore di soluzioni_ è responsabile della [creazione di un'applicazione](quick-deploy-iot-central.md), della [configurazione di regole e azioni](quick-configure-rules.md), della [definizione di integrazioni con altri servizi](howto-export-data.md) e dell'ulteriore personalizzazione dell'applicazione per operatori e sviluppatori di dispositivi.
- Un _operatore_ [gestisce i dispositivi](howto-manage-devices.md) connessi all'applicazione.
- Un _amministratore_ è responsabile di attività amministrative, ad esempio la gestione di [ruoli e autorizzazioni degli utenti](howto-administer.md) nell'applicazione.
- Uno _sviluppatori di dispositivi_ [crea il codice che viene eseguito in un dispositivo](concepts-telemetry-properties-commands.md) o in un [modulo IoT Edge](concepts-iot-edge.md) connesso all'applicazione.

## <a name="create-your-iot-central-application"></a>Creare l'applicazione IoT Central

È possibile distribuire rapidamente una nuova IoT Central e quindi personalizzarla in base a requisiti specifici. Iniziare con un modello di _applicazione generico o_ con uno dei modelli di applicazione incentrati sul settore per retail, [energy,](../energy/overview-iot-central-energy.md) [government](../government/overview-iot-central-government.md)o [healthcare.](../healthcare/overview-iot-central-healthcare.md) [](../retail/overview-iot-central-retail.md)

Vedere [l'avvio rapido Creare una nuova](quick-deploy-iot-central.md) applicazione per una procedura di creazione della prima applicazione.

## <a name="connect-devices"></a>Connettere dispositivi

Dopo aver creato l'applicazione, il primo passaggio consiste nel creare un dispositivo di connessione. Ogni dispositivo connesso a IoT Central usa un _modello di dispositivo_. Un modello di dispositivo è il progetto che definisce le caratteristiche e il comportamento di un tipo di dispositivo, ad esempio:

- I dati di telemetrica che invia. Gli esempi includono i dati di temperatura e umidità. La telemetria invia i dati in streaming.
- Le proprietà di business modificabili da un operatore. Gli esempi includono un indirizzo del cliente e la data dell'ultima manutenzione.
- Le proprietà del dispositivo impostate da un dispositivo che sono di sola lettura nell'applicazione. Ad esempio, lo stato di una valvola come aperto o chiuso.
- Le proprietà, impostate da un operatore, che determinano il comportamento del dispositivo. Ad esempio, un obiettivo di temperatura per il dispositivo.
- Comandi, che possono essere chiamati da un operatore, che vengono eseguiti in un dispositivo. Ad esempio, un comando per riavviare un dispositivo in modalità remota.

Ogni [modello di dispositivo](howto-set-up-template.md) include:

- Un _modello di dispositivo_ che descrive le funzionalità che un dispositivo deve implementare. Le funzionalità del dispositivo includono:

  - I dati di telemetria inviati in streaming a IoT Central.
  - Le proprietà di sola lettura usate per segnalare lo stato IoT Central.
  - Le proprietà scrivibili ricevute da IoT Central per impostare lo stato del dispositivo.
  - I comandi chiamati da IoT Central.

- Proprietà cloud non archiviate nel dispositivo.
- Personalizzazioni, dashboard e moduli che fanno parte dell'applicazione IoT Central.

Sono disponibili diverse opzioni per la creazione di modelli di dispositivo:

- Progettare il modello di dispositivo in IoT Central e quindi implementarne la rappresentazione nel codice.
- Creare un modello di dispositivo con Visual Studio Code e pubblicarlo in un repository. Implementare il codice del dispositivo dal modello e connettere il dispositivo all'applicazione IoT Central. IoT Central trova il modello di dispositivo nel repository e crea automaticamente un semplice modello di dispositivo.
- Creare un modello di dispositivo con Visual Studio Code. Implementare il codice del dispositivo dal modello. Importare manualmente il modello di dispositivo nell'applicazione IoT Central e quindi aggiungere eventuali personalizzazioni, dashboard e proprietà cloud necessari per l'applicazione IoT Central.

Vedere la [guida introduttiva Aggiungere un](quick-create-simulated-device.md) dispositivo simulato per una procedura di creazione e connessione del primo dispositivo.

### <a name="customize-the-ui"></a>Personalizzare l'interfaccia utente

È anche possibile personalizzare l'IoT Central'interfaccia utente dell'applicazione per gli operatori responsabili dell'uso quotidiano dell'applicazione. Le personalizzazioni che è possibile apportare includono:

- Configurazione di dashboard personalizzati per consentire agli operatori di individuare informazioni dettagliate e risolvere i problemi più velocemente.
- Configurazione di analisi personalizzate per esplorare i dati della serie temporale dai dispositivi connessi.
- Definizione del layout di proprietà e impostazioni in un modello di dispositivo.

## <a name="manage-your-devices"></a>Gestire i dispositivi

Gli operatori usano l'applicazione IoT Central per [gestire i dispositivi](howto-manage-devices.md) nella soluzione IoT Central. Gli operatori eseguono attività quali:

- Eseguire il monitoraggio dei dispositivi connessi all'applicazione.
- Risolvere e correggere i problemi relativi ai dispositivi.
- Eseguire il provisioning di nuovi dispositivi.

È possibile [definire regole e azioni personalizzate che](howto-configure-rules.md) operano sul flusso di dati dai dispositivi connessi. Un operatore può abilitare o disabilitare queste regole a livello di dispositivo per controllare e automatizzare le attività all'interno dell'applicazione.

Con qualsiasi soluzione IoT progettata per operare su larga scala, è importante adottare un approccio strutturato alla gestione dei dispositivi. Non è sufficiente connettere i dispositivi al cloud, ma è necessario che rimangano connessi e integri. Usare le seguenti funzionalità IoT Central per gestire i dispositivi durante l'intero ciclo di vita dell'applicazione:

### <a name="dashboards"></a>Dashboard

I [dashboard](./howto-set-up-template.md#generate-default-views) predefiniti forniscono un'interfaccia utente personalizzabile per monitorare l'integrità e i dati di telemetria dei dispositivi. È possibile iniziare con un dashboard predefinito in un [modello di applicazione](howto-use-app-templates.md) o creare dashboard personalizzati in base alle esigenze degli operatori. È possibile condividere i dashboard con tutti gli utenti dell'applicazione o mantenerli privati.

### <a name="rules-and-actions"></a>Regole e azioni

Creare [regole personalizzate](tutorial-create-telemetry-rules.md) in base allo stato e ai dati di telemetria dei dispositivi per identificare quelli che richiedono attenzione. Configurare azioni per avvisare le persone appropriate e assicurarsi che vengano tempestivamente intraprese misure correttive.

### <a name="jobs"></a>Processi

I [processi](howto-run-a-job.md) consentono di applicare aggiornamenti singoli o in blocco ai dispositivi impostando proprietà o chiamando comandi.

## <a name="integrate-with-other-services"></a>Integrazione con altri servizi

Essendo una piattaforma applicativa, IoT Central consente di trasformare i dati IoT in informazioni aziendali dettagliate che generano risultati azionabili. Le [regole](./tutorial-create-telemetry-rules.md), l'[esportazione di dati](./howto-export-data.md) e l'[API REST pubblica](/learn/modules/manage-iot-central-apps-with-rest-api/) sono esempi delle possibilità offerte per integrare IoT Central con le applicazioni line-of-business:

![Come trasformare i dati IoT con IoT Central](media/overview-iot-central/transform.png)

È possibile generare informazioni aziendali dettagliate, ad esempio per determinare le tendenze di efficienza dei macchinari o prevedere il consumo energetico futuro in una fabbrica, creando pipeline di analisi personalizzate per elaborare i dati di telemetria provenienti dai dispositivi e archiviare i risultati. Configurare le esportazioni dei dati nell'applicazione IoT Central per esportare dati di telemetria, oltre a modifiche delle proprietà e dei modelli dei dispositivi, in altri servizi in cui è possibile analizzarli, archiviarli e visualizzarli con gli strumenti preferiti.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Creare soluzioni IoT e integrazioni personalizzate con le API REST

Creare soluzioni IoT come:

- App specifiche per dispositivi mobili in grado di configurare e controllare i dispositivi in remoto.
- Integrazioni personalizzate che consentono l'interazione delle attuali applicazioni line-of-business con i dispositivi e i dati IoT.
- Applicazioni di gestione per la modellazione, l'onboarding e l'accesso ai dati dei dispositivi.

## <a name="administer-your-application"></a>Amministrare l'applicazione

Le applicazioni IoT Central sono completamente ospitate in Microsoft e ciò consente di ridurre il carico amministrativo correlato alla gestione delle applicazioni. Gli amministratori gestiscono l'accesso all'applicazione con [regole e autorizzazioni utente](howto-administer.md).

## <a name="pricing"></a>Prezzi

È possibile creare l'applicazione IoT Central con una versione di valutazione gratuita valida 7 giorni oppure usare un piano tariffario standard.

- Le applicazioni con il piano *gratuito* sono gratuite per sette giorni e supportano fino a cinque dispositivi. È possibile convertirle per l'uso di un piano tariffario standard in qualsiasi momento prima della scadenza.
- Le applicazioni create usando il piano *Standard* vengono fatturate in base ai singoli dispositivi; è possibile scegliere il piano tariffario **Standard 0**, **Standard 1** o **Standard 2** con i primi due dispositivi gratuiti. Altre informazioni sui [prezzi di IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="quotas"></a>Quote

Le quote predefinite di ogni sottoscrizione di Azure possono influire sull'ambito della soluzione IoT. Attualmente, IoT Central limita il numero di applicazioni distribuibili in una sottoscrizione a 10. Se è necessario aumentare questo limite, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

## <a name="known-issues"></a>Problemi noti

- L'esportazione dati continua non supporta il formato Avro (incompatibilità).
- GeoJSON non è attualmente supportato.
- La tessera mappa non è attualmente supportato.
- I tipi di schema della matrice non sono supportati.
- Sono supportati solo l'SDK per dispositivi C e gli SDK per dispositivi e servizi Node.js.
- IoT Central è attualmente disponibile nelle località seguenti: Stati Uniti, Europa, Asia Pacifico, Australia, Regno Unito e Giappone.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver ottenuto una panoramica di IoT Central, vengono consigliati alcuni passaggi successivi:

- Per gli sviluppatori di dispositivi che desiderano approfondire il codice, il passaggio successivo suggerito consiste nel [Creare e connettere un'applicazione client all'applicazione Azure IoT Central](./tutorial-connect-device.md).
- Acquisire familiarità con l'[interfaccia utente di Azure IoT Central](overview-iot-central-tour.md).
- Iniziare a [creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).
