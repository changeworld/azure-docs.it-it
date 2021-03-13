---
title: Glossario dei termini di Azure. Microsoft Docs
description: 'Guida per gli sviluppatori: un glossario che illustra alcuni dei termini comuni usati negli articoli relativi ad Azure.'
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 3e8a2ac93e9fea6ad045030759be894617557658
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103022088"
---
# <a name="glossary-of-iot-terms"></a>Glossario dei termini delle cose

Questo articolo elenca alcuni dei termini comuni usati negli articoli relativi alle cose.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Advanced Message Queueing Protocol

[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) è uno dei protocolli di messaggistica supportati da [Hub IoT](#iot-hub) per la comunicazione con i dispositivi. Per altre informazioni sui protocolli di messaggistica supportati dall'hub IoT, vedere [Inviare e ricevere messaggi con l'hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="automatic-device-management"></a>Gestione automatica dei dispositivi

La gestione automatica dei dispositivi nell'hub IoT di Azure consente di automatizzare molte attività ripetitive e complesse di gestione di un numero elevato di dispositivi nel loro intero ciclo di vita. La gestione automatica dei dispositivi consente di gestire un insieme di dispositivi in base alle proprietà, definire la configurazione desiderata e permette all'hub IoT di aggiornare i dispositivi quando rientrano nell'ambito.  Si compone delle [configurazioni automatiche del dispositivo](../iot-hub/iot-hub-automatic-device-management.md) e delle [distribuzioni automatiche di IoT Edge](../iot-edge/how-to-deploy-at-scale.md).

### <a name="automatic-device-configuration"></a>Configurazione automatica dei dispositivi

Il back-end della soluzione può usare le [configurazioni automatiche dei dispositivi](../iot-hub/iot-hub-automatic-device-management.md) per assegnare le proprietà desiderate a un set di [dispositivi gemelli](#device-twin) e segnalare lo stato usando metriche di sistema e metriche personalizzate. 

### <a name="azure-iot-device-sdks"></a>SDK dispositivo IoT Azure

Sono disponibili _SDK per dispositivi_ per più linguaggi che consentono di creare [app per dispositivo](#device-app) che interagiscono con un hub IoT. Le esercitazioni su Hub IoT illustrano come usare questi SDK per dispositivi. Il codice sorgente e altre informazioni sugli SDK per dispositivi sono disponibili in questo [repository](https://github.com/Azure/azure-iot-sdks) GitHub.

### <a name="azure-iot-explorer"></a>Esplora risorse di Azure

[Azure Internet Explorer](https://github.com/Azure/azure-iot-explorer) viene usato per visualizzare i dati di telemetria inviati dal dispositivo, usare le proprietà del dispositivo e chiamare i comandi. È anche possibile usare Esplora risorse per interagire con i dispositivi e testarli e per gestire i [dispositivi Plug and Play](#iot-plug-and-play-device).

### <a name="azure-iot-service-sdks"></a>Azure IoT SDK per servizi

Sono disponibili _SDK per servizi_ per più linguaggi che consentono di creare [app back-end](#back-end-app) che interagiscono con un hub IoT. Le esercitazioni su Hub IoT illustrano come usare questi SDK per servizi. Il codice sorgente e altre informazioni sugli SDK per servizi sono disponibili in questo [repository](https://github.com/Azure/azure-iot-sdks) GitHub.

### <a name="azure-iot-tools"></a>Azure IoT Tools

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) è un'utile estensione open source multipiattaforma di Visual Studio Code che consente di gestire i dispositivi e l'hub IoT di Azure in Visual Studio Code. Con Azure IoT Tools, gli sviluppatori IoT possono sviluppare con facilità un progetto IoT in Visual Studio Code.

## <a name="b"></a>B

### <a name="back-end-app"></a>App back-end

Nell'ambito di [Hub IoT](#iot-hub), un'app back-end è un'app che connette a uno degli endpoint per il servizio in un hub IoT. Un'app back-end, ad esempio, può recuperare messaggi [da dispositivo a cloud](#device-to-cloud) o gestire il [Registro delle identità](#identity-registry). Un'app back-end in genere viene eseguita nel cloud, ma in molte esercitazioni le app back-end sono app console in esecuzione nel computer di sviluppo locale.

### <a name="built-in-endpoints"></a>Endpoint predefiniti

Ogni hub IoT include un [endpoint](../iot-hub/iot-hub-devguide-endpoints.md) predefinito compatibile con l'Hub eventi. Per leggere i messaggi da dispositivo a cloud da questo endpoint, è possibile usare qualsiasi meccanismo che funzioni con l'Hub eventi.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Gateway cloud

Un gateway cloud abilita la connettività per i dispositivi che non possono connettersi direttamente a [Hub IoT](#iot-hub). Un gateway cloud, a differenza di un [gateway sul campo](#field-gateway) che viene eseguito in locale nei dispositivi, è ospitato nel cloud. Un caso d'uso tipico per il gateway cloud è l'implementazione della conversione dei protocolli per i dispositivi.

### <a name="cloud-to-device"></a>Da cloud a dispositivo

Si riferisce ai messaggi inviati da un hub IoT a un dispositivo connesso. Spesso, questi messaggi sono comandi che indicano al dispositivo di eseguire un'azione. Per altre informazioni, vedere [Inviare e ricevere messaggi con l'hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Comandi

In Plug and Play di tutto, i comandi definiti in un' [interfaccia](#interface) rappresentano i metodi che possono essere eseguiti nel dispositivo [gemello digitale](#digital-twin). Ad esempio, un comando per riavviare un dispositivo.

### <a name="component"></a>Componente

In Plug and Play, i componenti consentono di creare un' [interfaccia](#interface) del modello come assembly di altre interfacce. Un [modello di dispositivo](#device-model) può combinare più interfacce come componenti. Un modello, ad esempio, può includere un componente switch e un componente termostato. Più componenti in un modello possono usare anche lo stesso tipo di interfaccia. Un modello, ad esempio, può includere due componenti termostato.

### <a name="configuration"></a>Configurazione

Nell'ambito della [configurazione automatica dei dispositivi](../iot-hub/iot-hub-automatic-device-management.md), una configurazione nell'hub IoT definisce la configurazione desiderata per un set di dispositivi gemelli e fornisce un set di metriche per segnalare lo stato e l'avanzamento.

### <a name="connection-string"></a>Stringa di connessione

Le stringhe di connessione vengono usate nel codice dell'app per incapsulare le informazioni necessarie per connettersi a un endpoint. Una stringa di connessione contiene in genere l'indirizzo dell'endpoint e informazioni di sicurezza, ma il formato della stringa può variare nei diversi servizi. Esistono due tipi di stringa di connessione associato al servizio hub IoT:

- *Stringhe di connessione dispositivo* che consentono ai dispositivi di connettersi agli endpoint che usano dispositivi in un hub IoT.

- *Stringhe di connessione hub IoT* che consentono alle app back-end di connettersi agli endpoint che usano i servizi in un hub IoT.

### <a name="custom-endpoints"></a>Endpoint personalizzati

È possibile creare [endpoint](../iot-hub/iot-hub-devguide-endpoints.md) personalizzati su un hub IoT per recapitare i messaggi inviati da una [regola di routing](#routing-rules). Gli endpoint personalizzati connettono direttamente a un Hub eventi, a una coda del Bus di servizio o a un argomento del Bus di servizio.

### <a name="custom-gateway"></a>Gateway personalizzato

Un gateway abilita la connettività per i dispositivi che non possono connettersi direttamente a [Hub IoT](#iot-hub). È possibile usare Azure IoT Edge per compilare gateway personalizzati che implementano la logica personalizzata per gestire i messaggi, le conversioni dei protocolli personalizzati e altre attività di elaborazione nei dispositivi perimetrali.

## <a name="d"></a>D

### <a name="data-point-message"></a>Messaggio di punto dati

Un messaggio di punto dati è un messaggio da [dispositivo a cloud](#device-to-cloud) contenente dati di [telemetria](#telemetry) come temperatura o velocità del vento.

### <a name="default-component"></a>Componente predefinito

Nel Plug and Play di tutto, tutti i [modelli di dispositivo](#device-model) dispongono di un componente predefinito. Un modello di dispositivo semplice dispone solo di un componente predefinito. tale modello è anche noto come dispositivo senza componenti. Un modello più complesso ha più componenti annidati sotto il componente predefinito.

### <a name="device-certification"></a>Certificato del dispositivo

Il programma di certificazione dei dispositivi Plug and Play IoT verifica che un dispositivo soddisfi i requisiti di certificazione Plug and Play IoT. È possibile aggiungere un dispositivo certificato al [catalogo di dispositivi Certified for Azure IoT](https://aka.ms/devicecatalog) pubblico.

### <a name="device-model"></a>Modello del dispositivo

Un modello di dispositivo usa il [linguaggio di definizione delle gemelle digitali](#digital-twins-definition-language) per descrivere le funzionalità di un dispositivo Plug and Play. Un modello di dispositivo semplice usa una singola interfaccia per descrivere le funzionalità del dispositivo. Un modello di dispositivo più complesso include più componenti, ognuno dei quali descrive un set di funzionalità. Per altre informazioni, vedere la pagina relativa ai [componenti plug and Play nei modelli](../iot-pnp/concepts-components.md).

### <a name="device-builder"></a>Generatore di dispositivi

Un generatore di dispositivi usa un [modello di dispositivo](#device-model) e le [interfacce](#interface) quando si implementa il codice per l'esecuzione su un [dispositivo Plug and Play](#iot-plug-and-play-device). I generatori di dispositivi usano in genere uno degli [SDK per dispositivi Azure](#azure-iot-device-sdks) per implementare il client del dispositivo.

### <a name="device-modeling"></a>Modelli dispositivo

Un generatore di [dispositivi](#device-builder) o un [Generatore di moduli](#module-builder)usa il linguaggio di [definizione delle gemelle digitali](#digital-twins-definition-language) per modellare le funzionalità di un [plug and Play dispositivo](#iot-plug-and-play-device). Un [Generatore di soluzioni](#solution-builder) può configurare una soluzione Internet delle cose dal modello.

### <a name="desired-configuration"></a>Configurazione desiderata

Nell'ambito di un [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md), il termine configurazione desiderata indica il set completo di proprietà e metadati nel dispositivo gemello che deve essere sincronizzato con il dispositivo.

### <a name="desired-properties"></a>Proprietà desiderate

Nell'ambito di un [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md), le proprietà desiderate sono un sottoparagrafo del dispositivo gemello usato con le [proprietà segnalate](#reported-properties) per sincronizzare la condizione o la configurazione del dispositivo. Le proprietà desiderate possono essere impostate solo da un'[app back-end](#back-end-app) e possono essere lette dall'[app per dispositivi](#device-app).

### <a name="device-to-cloud"></a>Da dispositivo a cloud

Si riferisce ai messaggi inviati da un dispositivo connesso a [Hub IoT](#iot-hub). Questi messaggi potrebbero essere di [punto dati](#data-point-message) o [interattivi](#interactive-message). Per altre informazioni, vedere [Inviare e ricevere messaggi con l'hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="device"></a>Dispositivo

Nel contesto di IoT, un dispositivo è in genere un dispositivo di elaborazione autonomo e su scala ridotta, che potrebbe raccogliere dati o controllare altri dispositivi. Ad esempio, un dispositivo potrebbe essere un sistema di monitoraggio ambientale o un controller per i sistemi di irrigazione e ventilazione in una serra. Il [catalogo dei dispositivi](https://catalog.azureiotsolutions.com/) contiene un elenco di dispositivi hardware certificati da usare con [hub IoT](#iot-hub).

### <a name="device-app"></a>App per dispositivo

Un'app per dispositivi viene eseguita nel [dispositivo](#device) e gestisce la comunicazione con l'[hub IoT](#iot-hub). In genere si usa uno degli [Azure IoT SDK per dispositivi](#azure-iot-device-sdks) quando si implementa un'app per dispositivi. In diverse esercitazioni su IoT si usa un [dispositivo simulato](#simulated-device) per praticità.

### <a name="device-condition"></a>Condizione del dispositivo

Si riferisce alle informazioni sullo stato del dispositivo, ad esempio il metodo di connettività attualmente in uso, come segnalato da un' [app per dispositivi](#device-app). Le [app per dispositivi](#device-app) possono anche segnalare le loro stesse funzionalità. È possibile cercare le informazioni sulla condizione e sulle funzionalità usando i dispositivi gemelli.

### <a name="device-data"></a>Dati del dispositivo

Per dati del dispositivo si intendono i dati di ogni dispositivo archiviati nel [registro delle identità](#identity-registry) di Hub IoT. È possibile importare ed esportare questi dati.

### <a name="device-identity"></a>Identità del dispositivo

L'identità del dispositivo è l'identificatore univoco assegnato a ogni dispositivo registrato nel [registro delle identità](#identity-registry).

### <a name="device-management"></a>Gestione dei dispositivi

La gestione dei dispositivi comprende il ciclo di vita completo associato alla gestione dei dispositivi nella soluzione IoT, inclusi pianificazione, provisioning, configurazione, monitoraggio e ritiro.

### <a name="device-management-patterns"></a>Modelli di gestione dei dispositivi

L'[hub IoT](#iot-hub) abilita modelli di gestione dei dispositivi comuni, inclusi riavvio, esecuzione di ripristini delle impostazioni predefinite ed esecuzione di aggiornamenti del firmware nei dispositivi.

### <a name="device-rest-api"></a>API REST dei dispositivi

È possibile usare l'[API REST dei dispositivi](/rest/api/iothub/device) da un dispositivo per l'invio da dispositivo a cloud di messaggi a un hub IoT e per la ricezione da [cloud a dispositivo](#cloud-to-device) di messaggi da un hub IoT. In genere è consigliabile usare uno degli [SDK per dispositivi](#azure-iot-device-sdks) di livello superiore, come illustrato nelle esercitazioni su Hub IoT.

### <a name="device-provisioning"></a>Provisioning di dispositivi

Il provisioning dei dispositivi è il processo di aggiunta dei dati iniziali dei [dispositivi](#device-data) agli archivi nella soluzione. Per abilitare un nuovo dispositivo per la connessione all'hub, è necessario aggiungere un ID dispositivo e le chiavi al registro delle [identità](#identity-registry)dell'hub Internet. Come parte del processo di provisioning, può essere necessario inizializzare i dati specifici del dispositivo in altri archivi di soluzioni.

### <a name="device-twin"></a>Dispositivo gemello

Un dispositivo gemello è un documento JSON nel quali vengono archiviate informazioni sullo stato dei dispositivi, ad esempio metadati, configurazioni e condizioni. Hub IoT rende permanente un dispositivo gemello per ogni dispositivo di cui viene effettuato il provisioning nell'hub IoT. I dispositivi gemelli consentono di sincronizzare le configurazioni e le condizioni del dispositivo tra il dispositivo e il back-end della soluzione. È possibile eseguire una query sui dispositivi gemelli per individuare dispositivi specifici e per lo stato delle operazioni a esecuzione prolungata.

### <a name="direct-method"></a>Metodo diretto

Un [metodo diretto](../iot-hub/iot-hub-devguide-direct-methods.md) è un modo per attivare un metodo da eseguire su un dispositivo richiamando un'API nell'hub IoT.

### <a name="digital-twin"></a>Gemelli digitali

Un dispositivo gemello digitale è una raccolta di dati digitali che rappresenta un oggetto fisico. Le modifiche apportate all'oggetto fisico vengono riflesse nel dispositivo gemello digitale. In alcuni scenari, è possibile usare il dispositivo gemello digitale per modificare l'oggetto fisico. Il [servizio Azure Digital Twins](../digital-twins/index.yml) usa i modelli espressi nel [linguaggio di definizione delle gemelle digitali](#digital-twins-definition-language) per abilitare un'ampia gamma di soluzioni basate sul cloud che usano i dispositivi gemelli digitali. Un dispositivo [plug and Play](../iot-pnp/index.yml) dispone di un dispositivo gemello digitale, descritto da un [modello di dispositivo](#device-model)DTDL.

### <a name="digital-twin-change-events"></a>Eventi di modifica del gemello digitale

Quando un [dispositivo Plug and Play](#iot-plug-and-play-device) è connesso a un hub Internet delle cose, l'hub può usare la funzionalità di routing per inviare notifiche delle modifiche del dispositivo gemello digitale. Ad esempio, ogni volta che un valore della [Proprietà](#properties) viene modificato in un dispositivo, l'hub Internet può inviare una notifica a un endpoint, ad esempio un hub eventi.

### <a name="digital-twins-definition-language"></a>Linguaggio di definizione gemelli digitali

Un linguaggio per la descrizione dei modelli e delle interfacce per [dispositivi Plug and Play IoT](#iot-plug-and-play-device). Usare la [versione 2 del linguaggio Digital Twins Definition](https://github.com/Azure/opendigitaltwins-dtdl) per descrivere le funzionalità di un dispositivo [gemello digitale](#digital-twin) e abilitare la piattaforma Internet delle cose e le soluzioni Internet per l'uso della semantica dell'entità.

### <a name="digital-twin-route"></a>Route gemelli digitali

Una route configurata in un hub Internet per la distribuzione di eventi di modifica di dispositivi [gemelli digitali](#digital-twin-change-events) a un endpoint, ad esempio un hub eventi.

## <a name="e"></a>E

### <a name="endpoint"></a>Endpoint

Un hub IoT espone più [endpoint](../iot-hub/iot-hub-devguide-endpoints.md) che consentono alle app di connettersi all'hub IoT. Esistono endpoint per il dispositivo che consentono ai dispositivi di eseguire operazioni come l'invio di messaggi [da dispositivo a cloud](#device-to-cloud) e la ricezione di messaggi [da cloud a dispositivo](#cloud-to-device). Esistono endpoint di gestione del servizio che consentono alle [app back-end](#back-end-app) di eseguire operazioni come la gestione delle [identità del dispositivo](#device-identity) e la gestione del dispositivo gemello. Esistono [endpoint incorporati](#built-in-endpoints) del servizio per leggere i messaggi da dispositivo a cloud. È possibile creare [endpoint personalizzati](#custom-endpoints) per ricevere i messaggi da dispositivo a cloud inviati da una [regola di routing](#routing-rules).

### <a name="event-hub-compatible-endpoint"></a>Endpoint compatibile con Hub eventi

Per leggere i messaggi da [dispositivo a cloud](#device-to-cloud) inviati all'hub Internet, è possibile connettersi a un endpoint nell'hub e usare qualsiasi metodo compatibile con hub eventi per leggere tali messaggi. I metodi compatibili con hub eventi includono l'uso degli [SDK di hub eventi](../event-hubs/event-hubs-programming-guide.md) e di [analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md).

## <a name="f"></a>F

### <a name="field-gateway"></a>Gateway sul campo

Un gateway sul campo Abilita la connettività per i dispositivi che non possono connettersi direttamente all' [Hub](#iot-hub) Internet e che in genere viene distribuito localmente con i dispositivi. Per altre informazioni, vedere [Informazioni sull'hub IoT di Azure](../iot-hub/about-iot-hub.md).

## <a name="g"></a>G

### <a name="gateway"></a>Gateway

Un gateway abilita la connettività per i dispositivi che non possono connettersi direttamente a [Hub IoT](#iot-hub). Vedere anche [Gateway sul campo](#field-gateway), [Gateway cloud](#cloud-gateway) e [Gateway personalizzato](#custom-gateway).

## <a name="i"></a>I

### <a name="identity-registry"></a>Registro delle identità

Il [registro delle identità](../iot-hub/iot-hub-devguide-identity-registry.md) è il componente integrato di un hub IoT che archivia le informazioni sui singoli dispositivi a cui è consentito connettersi a un hub IoT.

### <a name="interactive-message"></a>Messaggio interattivo

Un messaggio interattivo è un messaggio [da cloud a dispositivo](#cloud-to-device) che attiva un'azione immediata nel back-end della soluzione. Ad esempio, un dispositivo potrebbe inviare un allarme relativo a un errore che deve essere registrato automaticamente in un sistema CRM.

### <a name="interface"></a>Interfaccia

In Plug and Play, un'interfaccia descrive le funzionalità correlate che vengono implementate da un [dispositivo Plug and Play](#iot-plug-and-play-device) o un dispositivo [gemello digitale](#digital-twin). È possibile riutilizzare le interfacce in diversi [modelli di dispositivo](#device-model). Quando un'interfaccia viene usata in un modello di dispositivo, definisce un [componente](#component) del dispositivo. Un dispositivo semplice contiene solo un'interfaccia predefinita.

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge consente la distribuzione basata su cloud di codice specifico per soluzioni o servizi di Azure nei dispositivi locali. I [dispositivi IOT Edge](#iot-edge-device) possono aggregare i dati di altri dispositivi per eseguire operazioni di elaborazione e analisi prima di inviare i dati al cloud. Per ulteriori informazioni, vedere [Azure IOT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>Agente di IoT Edge

La parte del runtime di IoT Edge responsabile della distribuzione e del monitoraggio dei moduli.

### <a name="iot-edge-device"></a>Dispositivo IoT Edge

Un dispositivo IoT Edge usa i [moduli IOT Edge](#iot-edge-module) in contenitori per eseguire servizi di Azure, servizi di terze parti o codice personalizzato. Sul dispositivo IoT Edge, il [runtime di IOT Edge](#iot-edge-runtime) gestisce i moduli. È possibile monitorare e gestire in remoto un dispositivo IoT Edge dal cloud.

### <a name="iot-edge-automatic-deployment"></a>Distribuzione automatica di IoT Edge

Una distribuzione automatica di IoT Edge configura un set di destinazione di dispositivi IoT Edge per eseguire un set di moduli IoT Edge. Ogni distribuzione garantisce continuamente che tutti i dispositivi conformi alla condizione di destinazione eseguano il set di moduli specificato, anche quando vengono creati nuovi dispositivi o vengono modificati in modo che corrispondano alla condizione di destinazione. Ogni dispositivo IoT Edge riceve solo la distribuzione con priorità più alta tra quelle di cui soddisfa la condizione di destinazione. Altre informazioni sulla [distribuzione automatica di IoT Edge](../iot-edge/module-deployment-monitoring.md).

### <a name="iot-edge-deployment-manifest"></a>Manifesto della distribuzione di IoT Edge

Documento JSON contenente le informazioni da copiare in uno o più moduli gemello dei dispositivi IoT Edge per distribuire un set di moduli, route e le proprietà desiderate del modulo associato.

### <a name="iot-edge-gateway-device"></a>Dispositivo gateway IoT Edge

Un dispositivo IoT Edge con dispositivo downstream, che può essere un dispositivo IoT Edge o non IoT Edge.

### <a name="iot-edge-hub"></a>Hub di IoT Edge

La parte del runtime di IoT Edge responsabile delle comunicazioni da modulo a modulo e delle comunicazioni upstream (verso l'hub IoT) e downstream (dall'hub IoT).

### <a name="iot-edge-leaf-device"></a>Dispositivo foglia IoT Edge

Un dispositivo IoT Edge senza dispositivo downstream.

### <a name="iot-edge-module"></a>Modulo IoT Edge

Un modulo IoT Edge è un contenitore Docker che è possibile distribuire su dispositivi IoT Edge. Esegue un'attività specifica, come inserire un messaggio da un dispositivo, trasformare un messaggio o inviare un messaggio a un hub IoT. Comunica inoltre con altri moduli e invia dati al runtime di IoT Edge. [Informazioni sui requisiti e gli strumenti per lo sviluppo di moduli IOT Edge](../iot-edge/module-development.md).

### <a name="iot-edge-module-identity"></a>Identità del modulo IoT Edge

Un record nel registro delle identità del modulo dell'hub IoT con informazioni dettagliate sull'esistenza e le credenziali di sicurezza che devono essere usate da un modulo per eseguire l'autenticazione con un hub Edge o l'hub IoT.

### <a name="iot-edge-module-image"></a>Immagine del modulo IoT Edge

L'immagine Docker usata dal runtime di IoT Edge per creare istanze del modulo.

### <a name="iot-edge-module-twin"></a>Modulo IoT Edge gemello

Un documento Json contenuto nell'hub IoT in cui vengono archiviate le informazioni di stato relative a un'istanza del modulo.

### <a name="iot-edge-priority"></a>Priorità di IoT Edge

Quando due distribuzioni di IoT Edge hanno come destinazione lo stesso dispositivo, viene applicata la distribuzione con priorità più elevata. Se due distribuzioni hanno la stessa priorità, viene applicata la distribuzione con la data di creazione successiva. Per altre informazioni, vedere [Priorità](../iot-edge/module-deployment-monitoring.md#priority).

### <a name="iot-edge-runtime"></a>Runtime di IoT Edge

Il runtime di IoT Edge include tutti i componenti distribuiti da Microsoft per essere installati in un dispositivo IoT Edge. Include l'agente Edge, l'hub di Edge e il daemon di sicurezza di IoT Edge.

### <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge imposta i moduli in un unico dispositivo

Operazione che copia il contenuto di un manifesto IoT Edge in un dispositivo gemello del modulo. L'API sottostante è una configurazione di applicazione generica che accetta semplicemente un IoT Edge manifesto come input.

### <a name="iot-edge-target-condition"></a>Condizione di destinazione di IoT Edge

In una distribuzione IoT Edge la condizione di destinazione è qualsiasi condizione booleana nei tag dei dispositivi gemelli per selezionare i dispositivi di destinazione della distribuzione, ad esempio **tag. Environment = prod**. La condizione di destinazione viene costantemente valutata in modo da includere tutti i nuovi dispositivi che soddisfano i requisiti o rimuovere i dispositivi che non sono più disponibili. Per altre informazioni, vedere [Condizione di destinazione](../iot-edge/module-deployment-monitoring.md#target-condition)

### <a name="iot-hub"></a>Hub IoT

Hub IoT è un servizio completamente gestito di Azure che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. Per altre informazioni, vedere [Informazioni sull'hub IoT di Azure](../iot-hub/about-iot-hub.md). Usando la sottoscrizione di Azure, è possibile creare hub IoT per gestire i carichi di lavoro di messaggistica IoT.

### <a name="iot-hub-metrics"></a>Metriche di Hub IoT

Le metriche di Hub IoT forniscono dati sullo stato degli hub IoT nella sottoscrizione di Azure. Le metriche dell'hub IoT consentono di valutare l'integrità complessiva del servizio e dei dispositivi connessi. Le metriche consentono di visualizzare le attività in corso nell'hub IoT e di esaminare la causa radice dei problemi senza la necessità di contattare il supporto di Azure. Per altre informazioni, vedere [Monitoraggio dell'hub IoT di Azure](../iot-hub/monitor-iot-hub.md).

### <a name="iot-hub-query-language"></a>Linguaggio di query di Hub IoT

Il [linguaggio di query dell'hub](../iot-hub/iot-hub-devguide-query-language.md) Internet è un linguaggio simile a SQL che consente di eseguire query su un [processo](#job), i dispositivi gemelli digitali e i dispositivi gemelli.

### <a name="iot-hub-resource-rest-api"></a>API REST delle risorse dell'hub IoT

È possibile usare l'[API REST delle risorse dell'hub IoT](/rest/api/iothub/iothubresource) per gestire gli hub IoT nella sottoscrizione di Azure che esegue operazioni come creazione, aggiornamento ed eliminazione di hub.

### <a name="iot-solution-accelerators"></a>Acceleratori di soluzioni IoT

Gli acceleratori di soluzioni Azure IoT raggruppano più servizi di Azure in soluzioni. Tali soluzioni consentono di iniziare a usare rapidamente implementazioni end-to-end di scenari IoT comuni. Per altre informazioni, vedere [Panoramica degli acceleratori di soluzioni di Azure IoT](../iot-accelerators/about-iot-accelerators.md).

### <a name="the-iot-extension-for-azure-cli"></a>L'estensione IoT per l'interfaccia della riga di comando di Azure 

[L'estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension) è uno strumento multipiattaforma da riga di comando. Lo strumento consente di gestire i dispositivi nel [registro delle identità](#identity-registry), inviare e ricevere messaggi e file dai dispositivi e monitorare le operazioni dell'hub IoT.

### <a name="iot-plug-and-play-bridge"></a>Bridge Plug and Play IoT

Internet delle cose Plug and Play Bridge è un'applicazione open source che consente ai sensori e alle periferiche esistenti collegati ai gateway Windows o Linux di connettersi come [dispositivi Plug and Play](#iot-plug-and-play-device).

### <a name="iot-plug-and-play-device"></a>Dispositivo Plug and Play IoT

Un dispositivo Plug and Play è in genere un dispositivo di elaborazione autonomo di piccole dimensioni che raccoglie dati o controlla altri dispositivi e che esegue software o firmware che implementa un [modello di dispositivo](#device-model).  Ad esempio, un dispositivo Plug and Play IoT potrebbe essere un dispositivo di monitoraggio ambientale o un controller per un sistema di irrigazione di un sistema agricolo intelligente. Un dispositivo Plug and Play potrebbe essere implementato direttamente o come modulo di IoT Edge. È possibile scrivere una soluzione IoT ospitata nel cloud per comandare, controllare e ricevere i dati da dispositivi Plug and Play IoT.

### <a name="iot-plug-and-play-conventions"></a>Convenzioni di Plug and Play IoT

I [dispositivi](#iot-plug-and-play-device) plug and Play dovrebbero seguire un set di convenzioni quando scambiano dati con una soluzione.

## <a name="j"></a>J

### <a name="job"></a>Processo

Il back-end della soluzione può usare i [processi](../iot-hub/iot-hub-devguide-jobs.md) per pianificare e tenere traccia delle attività in un set di dispositivi registrati con l'hub Internet. Le attività includono l'aggiornamento delle [proprietà desiderate](#desired-properties)del dispositivo gemello, l'aggiornamento dei [tag](#tags)del dispositivo gemello e la chiamata di [metodi diretti](#direct-method) [Hub IoT](#iot-hub) usa anche per eseguire [importazioni ed esportazioni](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) nel [registro delle identità](#identity-registry).

## <a name="m"></a>M

### <a name="model-id"></a>ID modello

Quando un Plug and Play dispositivo si connette a un hub Internet delle cose, invia l' **ID modello** del modello [DTDL](#digital-twins-definition-language) che implementa. Questo ID consente alla soluzione di trovare il modello di dispositivo.

### <a name="model-repository"></a>Repository modelli

Un repository di modelli archivia i modelli e le [interfacce](#interface)del [dispositivo](#device-model) .

### <a name="model-repository-rest-api"></a>REST API del repository modelli

API per la gestione e l'interazione con il repository del modello. Ad esempio, è possibile usare l'API per aggiungere e cercare i [modelli di dispositivo](#device-model).

### <a name="module-builder"></a>Generatore di moduli

Un generatore di moduli usa un [modello di dispositivo](#device-model) e le [interfacce](#interface) quando si implementa il codice per l'esecuzione su un [dispositivo Plug and Play](#iot-plug-and-play-device). I generatori di moduli implementano il codice come modulo o un modulo IoT Edge per eseguire la distribuzione nel runtime di IoT Edge in un dispositivo.

### <a name="modules"></a>Moduli

Sul lato dispositivo, gli SDK per dispositivi dell'hub Internet delle cose consentono di creare [moduli](../iot-hub/iot-hub-devguide-module-twins.md) in cui ognuno apre una connessione indipendente all'hub. Questa funzionalità permette di usare spazi dei nomi distinti per i diversi componenti nel dispositivo.

L'identità del modulo e il modulo gemello forniscono le stesse funzionalità dell' [identità del dispositivo](#device-identity) e del [dispositivo gemello](#device-twin) , ma con una granularità più fine. Questa granularità superiore consente a determinati dispositivi, ad esempio i dispositivi basati su sistema operativo o i dispositivi firmware che gestiscono più componenti, di isolare la configurazione e le condizioni di ognuno di questi componenti.

### <a name="module-identity"></a>Identità del modulo

L'identità del modulo è l'identificatore univoco assegnato a ogni modulo che appartiene a un dispositivo. L'identità del modulo viene anche inserita nel [registro delle identità](#identity-registry).

### <a name="module-twin"></a>Modulo gemello

Simile a un dispositivo gemello, un modulo gemello è un documento JSON nel quale vengono archiviate informazioni sullo stato dei moduli, ad esempio metadati, configurazioni e condizioni. L'hub IoT rende persistente un modulo gemello per ogni identità di modulo di cui si esegue il provisioning in un'identità di dispositivo nell'hub IoT. I moduli gemelli consentono di sincronizzare le configurazioni e le condizioni del modulo tra il modulo stesso e il back-end della soluzione. È possibile effettuare una query sui moduli gemelli per individuare moduli specifici ed effettuare query sullo stato delle operazioni a esecuzione prolungata.

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) è uno dei protocolli di messaggistica supportati dall' [Hub](#iot-hub) Internet per la comunicazione con i dispositivi. Per altre informazioni sui protocolli di messaggistica supportati dall'hub IoT, vedere [Inviare e ricevere messaggi con l'hub IoT](../iot-hub/iot-hub-devguide-messaging.md).

## <a name="o"></a>O

### <a name="operations-monitoring"></a>Monitoraggio delle operazioni

Il [monitoraggio delle operazioni](../iot-hub/iot-hub-operations-monitoring.md) dell'hub Internet in tempo reale consente di monitorare lo stato delle operazioni nell'hub Internet delle cose. [Hub](#iot-hub) Internet Tracks tiene traccia degli eventi nelle diverse categorie di operazioni. È possibile scegliere di impostare l'invio di eventi da una o più categorie a un endpoint di Hub IoT per l'elaborazione. È possibile monitorare i dati per individuare gli errori o configurare un'elaborazione più complessa in base ai modelli di dati.

## <a name="p"></a>P

### <a name="physical-device"></a>Dispositivo fisico

Un dispositivo fisico è un vero e proprio dispositivo, ad esempio un Raspberry Pi, che si connette a un hub IoT. Per motivi di praticità, diverse esercitazioni su Hub IoT usano [dispositivi simulati](#simulated-device) per consentire di eseguire esempi nel computer locale.

### <a name="primary-and-secondary-keys"></a>Chiavi primarie e secondarie

Quando ci si connette a un endpoint per il dispositivo o per il servizio in un hub IoT, la [stringa di connessione](#connection-string) include la chiave che concede l'accesso. Quando si aggiunge un dispositivo al [registro delle identità](#identity-registry) o si aggiungono [criteri di accesso condiviso](#shared-access-policy) all'hub, il servizio genera una chiave primaria e una secondaria. Avere due chiavi consente di eseguire il rollover da una chiave all'altra quando si aggiorna una chiave, senza perdere l'accesso all'hub IoT.

### <a name="properties"></a>Proprietà

Le proprietà sono campi dati definiti in un'[interfaccia](#interface) che rappresentano uno stato di un gemello digitale. È possibile dichiarare le proprietà in sola lettura o in scrittura. Le proprietà di sola lettura, ad esempio il numero di serie, vengono impostate dal codice in esecuzione nel [dispositivo Plug and Play IoT](#iot-plug-and-play-device) stesso.  Le proprietà scrivibili, ad esempio una soglia di allarme, vengono in genere impostate dalla soluzione IoT basata sul cloud.

### <a name="protocol-gateway"></a>Gateway di protocollo

Un gateway del protocollo viene in genere distribuito nel cloud e fornisce servizi di conversione del protocollo per i dispositivi che si connettono all' [Hub](#iot-hub)Internet. Per altre informazioni, vedere [Informazioni sull'hub IoT di Azure](../iot-hub/about-iot-hub.md).

## <a name="r"></a>R

### <a name="reported-configuration"></a>Configurazione segnalata

Nell'ambito di un [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md), il termine configurazione segnalata indica il set completo di proprietà e metadati nel dispositivo gemello che deve essere segnalato al back-end della soluzione.

### <a name="reported-properties"></a>Proprietà segnalate

Nell'ambito di un [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md), le proprietà segnalate sono un sottoparagrafo del dispositivo gemello usato con le [proprietà desiderate](#desired-properties) per sincronizzare la condizione o la configurazione del dispositivo. Le proprietà segnalate possono essere impostate solo dall'[app per dispositivo](#device-app) e possono essere lette e sottoposte a query da un'[app back-end](#back-end-app).

### <a name="retry-policy"></a>Criterio di ripetizione dei tentativi

I criteri di ripetizione si usano per gestire gli [errori temporanei](/azure/architecture/best-practices/transient-faults) quando ci si connette a un servizio cloud.

### <a name="routing-rules"></a>Regole di gestione

Configurare le [regole di routing](../iot-hub/iot-hub-devguide-messages-read-custom.md) nell'hub IoT per indirizzare i messaggi da dispositivo a cloud su un [endpoint predefinito](#built-in-endpoints) o su un [endpoint personalizzato](#custom-endpoints) per l'elaborazione da parte della soluzione di back-end in uso.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN è un protocollo usato dal protocollo AMQP per trasferire i token di sicurezza.

### <a name="service-rest-api"></a>API REST del servizio

È possibile usare l'[API REST del servizio](/rest/api/iothub/service/configuration) dalla soluzione di back-end per gestire i dispositivi gemelli. L'API consente di recuperare e aggiornare le proprietà del [dispositivo gemello](#device-twin), di invocare [metodi diretti](#direct-method) e pianificare i [processi](#job). In genere è consigliabile usare uno degli [SDK per servizi](#azure-iot-service-sdks) di livello superiore, come illustrato nelle esercitazioni su Hub IoT.

### <a name="shared-access-signature"></a>Firma di accesso condiviso

Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. L'autenticazione della firma di accesso condiviso presenta due componenti: i _criteri di accesso condiviso_ e una _firma di accesso condiviso_, che viene spesso chiamata token. Un dispositivo usa la firma di accesso condiviso per l'autenticazione con un hub IoT. Anche le [app back-end](#back-end-app) usano la firma di accesso condiviso per l'autenticazione con gli endpoint per il servizio in un hub IoT. In genere si include il token di firma di accesso condiviso nella [stringa di connessione](#connection-string) usata da un'app per stabilire una connessione a un hub IoT.

### <a name="shared-access-policy"></a>Criteri di accesso condiviso

Un criterio di accesso condiviso definisce le autorizzazioni concesse a chiunque abbia una [chiave primaria o secondaria](#primary-and-secondary-keys) valida associata a tale criterio. È possibile gestire i criteri di accesso condiviso e le chiavi per l'hub nel portale.

### <a name="simulated-device"></a>Dispositivo simulato

Per motivi di praticità, diverse esercitazioni su Hub IoT usano dispositivi simulati per consentire di eseguire esempi nel computer locale. Un [dispositivo fisico](#physical-device) è invece un vero e proprio dispositivo, ad esempio un Raspberry Pi, che si connette a un hub IoT.

### <a name="solution"></a>Soluzione

Una _soluzione_ può essere una soluzione di Visual Studio che include uno o più progetti, ma una _soluzione_ può anche essere una soluzione IoT che include elementi come dispositivi, [app per dispositivo](#device-app), un hub IoT, altri servizi di Azure e [app back-end](#back-end-app).

### <a name="solution-builder"></a>Generatore di soluzioni

Un generatore di soluzioni crea il back-end della soluzione. Un generatore di soluzioni funziona in genere con le risorse di Azure, ad esempio i repository dell'hub e del [modello](#model-repository).

### <a name="system-properties"></a>Proprietà di sistema

Nell'ambito di un [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md), le proprietà di sistema sono di sola lettura e includono informazioni riguardanti l'uso dei dispositivi, ad esempio lo stato della connessione e l'ora dell'ultima attività.

## <a name="t"></a>T

### <a name="tags"></a>Tag

Nell'ambito di un [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md), i tag sono metadati del dispositivo archiviati e recuperati dal back-end della soluzione in forma di documento JSON. I tag non sono visibili alle app in un dispositivo.

### <a name="telemetry"></a>Telemetria

I dispositivi raccolgono dati di telemetria, ad esempio la velocità del vento o la temperatura, e usano i messaggi dei punti dati per inviare i dati di telemetria a un hub IoT.

In Plug and Play, i campi di telemetria definiti in un' [interfaccia](#interface) rappresentano le misurazioni. Queste misurazioni sono in genere valori come le letture dei sensori che vengono inviate dal [dispositivo Plug and Play IoT](#iot-plug-and-play-device) come flusso di dati.

### <a name="token-service"></a>Servizio token

È possibile usare un servizio token per implementare un meccanismo di autenticazione per i dispositivi. Usa i [criteri di accesso condiviso](#shared-access-policy) dell'hub IoT con autorizzazioni **DeviceConnect** per creare token *basati sul dispositivo*. Questi token abilitano la connessione di un dispositivo all'hub IoT. Un dispositivo usa un meccanismo di autenticazione personalizzato per l'autenticazione con il servizio token. SE l'autenticazione del dispositivo riesce, il servizio token rilascia un token di firma di accesso condiviso per consentire al dispositivo da usare di accedere all'hub IoT.

### <a name="twin-queries"></a>Query dei dispositivi gemelli

Le [query su dispositivi e moduli gemelli](../iot-hub/iot-hub-devguide-query-language.md) usano il linguaggio di query simile a SQL dell'hub IoT per recuperare informazioni da dispositivi o moduli gemelli. È possibile usare lo stesso linguaggio di query dell'hub Internet per recuperare le informazioni su un [processo](#job) in esecuzione nell'hub Internet.

### <a name="twin-synchronization"></a>Sincronizzazione dei dispositivi o dei moduli gemelli

La sincronizzazione usa le [proprietà desiderate](#desired-properties) dei dispositivi gemelli o dei moduli gemelli per configurare i dispositivi o i moduli e recuperare le [proprietà segnalate](#reported-properties) da tali elementi per l'archiviazione nel dispositivo o modulo gemello.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>Certificato client X.509

Un dispositivo può usare un certificato X.509 per autenticarsi con [Hub IoT](#iot-hub). Usare un certificato X.509 è un'alternativa all'uso di un [token di firma di accesso condiviso](#shared-access-signature).
