---
title: Panoramica delle opzioni dell'SDK per dispositivi Azure
description: Informazioni su come usare l'SDK per dispositivi Azure per l'uso in base al ruolo e alle attività di sviluppo.
author: elhorton
ms.author: elhorton
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: 4d9546c8a912176117e42f2a6bbef3058331383b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702675"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Panoramica degli SDK per dispositivi Azure

Gli SDK per dispositivi Azure per dispositivi sono un set di librerie client per dispositivi, guide per sviluppatori, esempi e documentazione. Gli SDK per dispositivi consentono di connettere i dispositivi a livello di codice ai servizi di Azure.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagramma che mostra vari SDK di Azure.":::

Come illustrato nel diagramma, sono disponibili diversi SDK per dispositivi per adattarsi alle esigenze del linguaggio di programmazione e del dispositivo. Informazioni aggiuntive sulla selezione dell'SDK per dispositivi appropriato sono disponibili nell' [SDK da usare](#which-sdk-should-i-use). Sono disponibili anche gli SDK del servizio Azure per connettere l'applicazione basata sul cloud ai servizi di Azure Internet delle cose nel back-end. Questo articolo è incentrato sugli SDK per dispositivi, ma [qui](#service-sdks)è possibile ottenere altre informazioni sugli SDK dei servizi di Azure.

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Perché usare gli SDK per dispositivi Azure

Per connettere i dispositivi ad Azure, è possibile creare un livello di connessione personalizzato o usare gli SDK per dispositivi Azure. L'uso degli SDK per dispositivi Azure per i dispositivi presenta diversi vantaggi:

| &nbsp; &nbsp; Costo &nbsp; di sviluppo&nbsp; | Livello di connessione personalizzato | SDK per dispositivi Azure Internet |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Supporto | È necessario supportare e documentare il contenuto compilato | Accesso al supporto tecnico Microsoft (GitHub, Microsoft Q&A, Microsoft Docs, team di supporto clienti) |
| Nuove funzioni e caratteristiche | È necessario aggiungere nuove funzionalità di Azure al middleware personalizzato | Può sfruttare immediatamente i vantaggi delle nuove funzionalità che Microsoft aggiunge costantemente agli SDK di Internet delle cose |
| Investimento | Investire centinaia di ore di sviluppo incorporato per progettare, compilare, testare e gestire una versione personalizzata | Può sfruttare i vantaggi di strumenti Open Source gratuiti. L'unico costo associato agli SDK è la curva di apprendimento. |

## <a name="which-sdk-should-i-use"></a>Quale SDK è necessario usare?

Gli SDK per dispositivi Azure Internet sono disponibili nei linguaggi di programmazione più diffusi, tra cui C, C#, Java, Node.js e Python. Quando si sceglie un SDK, è necessario tenere presenti due considerazioni principali: le funzionalità del dispositivo e la familiarità del team con il linguaggio di programmazione.

### <a name="device-capabilities"></a>Funzionalità di dispositivo

Quando si sceglie un SDK, è necessario considerare i limiti dei dispositivi in uso. Un dispositivo vincolato è uno con un solo microcontroller (MCU) e memoria limitata. Se si usa un dispositivo vincolato, è consigliabile usare [Embedded C SDK](#embedded-c-sdk). Questo SDK è progettato per fornire il set minimo di funzionalità per la connessione ad Azure. È anche possibile selezionare i componenti (client MQTT, TLS e librerie socket) più ottimizzati per il dispositivo incorporato. Se il dispositivo vincolato esegue anche Azure RTO, è possibile usare il middleware Azure RTO per connettersi ad Azure. Il middleware Azure RTO esegue il wrapping di Embedded C SDK con funzionalità aggiuntive per semplificare la connessione del dispositivo Azure RTO al cloud.

Un dispositivo non vincolato ha una CPU più affidabile, che è in grado di eseguire un sistema operativo per supportare un runtime del linguaggio, ad esempio .NET o Python. Se si usa un dispositivo non vincolato, la considerazione principale è la conoscenza della lingua.

### <a name="your-teams-familiarity-with-the-programming-language"></a>Familiarità del team con il linguaggio di programmazione

Gli SDK per dispositivi Azure Internet sono implementati in più lingue, in modo da poter scegliere la lingua preferita. Gli SDK per dispositivi si integrano anche con altri strumenti familiari e specifici del linguaggio. La possibilità di lavorare con un linguaggio di sviluppo e strumenti familiari consente al team di ottimizzare il ciclo di sviluppo di ricerca, creazione di prototipi, sviluppo di prodotti e manutenzione continuativa.

Quando possibile, selezionare un SDK familiare al team di sviluppo. Tutti gli SDK Azure è open source e sono disponibili diversi esempi per il team da valutare e testare prima di eseguire il commit in un SDK specifico.

## <a name="how-can-i-get-started"></a>Operazioni iniziali

Il punto di partenza consiste nell'esplorare i repository GitHub degli SDK per dispositivi di Azure. È anche possibile provare una [Guida introduttiva](quickstart-send-telemetry-python.md) che illustra come usare rapidamente un SDK per inviare dati di telemetria ad Azure.

Le opzioni per iniziare dipendono dal tipo di dispositivo in uso:
- Per i dispositivi vincolati, usare [Embedded C SDK](#embedded-c-sdk). 
- Per i dispositivi eseguiti in Azure RTO, è possibile sviluppare con il [middleware RTO di Azure](#azure-rtos-middleware). 
- Per i dispositivi non vincolati, è possibile [scegliere un SDK](#unconstrained-device-sdks) in un linguaggio di propria scelta. 

### <a name="constrained-device-sdks"></a>SDK per dispositivi vincolati
Questi SDK sono specializzati per l'esecuzione su dispositivi con risorse di calcolo o di memoria limitate. Per altre informazioni sui tipi di dispositivo comuni, vedere [Panoramica dei tipi di dispositivi Azure](concepts-iot-device-types.md).

#### <a name="embedded-c-sdk"></a>SDK di C incorporato
* [Repository GitHub](https://github.com/Azure/azure-sdk-for-c/tree/1.0.0/sdk/docs/iot)
* [Esempi](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Documentazione di riferimento](https://azure.github.io/azure-sdk-for-c/)
* [Come creare Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Grafico delle dimensioni per i dispositivi vincolati](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Middleware RTO di Azure

* [Repository GitHub](https://github.com/azure-rtos/threadx)
* [Guide Introduzione](https://github.com/azure-rtos/getting-started) e [altri esempi](https://github.com/azure-rtos/samples)
* [Documentazione di riferimento](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>SDK per dispositivi non vincolati
Questi SDK possono essere eseguiti su qualsiasi dispositivo in grado di supportare un runtime del linguaggio di ordine superiore. Sono inclusi dispositivi quali PC, Raspberry Pis e smartphone. Sono differenziate principalmente per lingua, quindi è possibile scegliere qualsiasi libreria più adatta al proprio team e allo scenario.

#### <a name="c-device-sdk"></a>SDK per dispositivi C
* [Repository GitHub](https://github.com/Azure/azure-iot-sdk-c)
* [Esempi](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Pacchetti](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Documentazione di riferimento](/azure/iot-hub/iot-c-sdk-ref/)
* [Documentazione di riferimento sul modulo perimetrale](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Compilare l'SDK per dispositivi C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Porting dell'SDK per C in altre piattaforme](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentazione](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) per gli sviluppatori per informazioni sulla compilazione incrociata e sull'introduzione a piattaforme diverse
* [Informazioni sull'utilizzo delle risorse dell'hub Azure per Azure](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>SDK per dispositivi C#

* [Repository GitHub](https://github.com/Azure/azure-iot-sdk-csharp)
* [Esempi](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Pacchetto](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Documentazione di riferimento](/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true)
* [Documentazione di riferimento sul modulo perimetrale](/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet&preserve-view=true)

#### <a name="java-device-sdk"></a>SDK per dispositivi Java

* [Repository GitHub](https://github.com/Azure/azure-iot-sdk-java)
* [Esempi](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Pacchetto](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Documentazione di riferimento](/java/api/com.microsoft.azure.sdk.iot.device)
* [Documentazione di riferimento sul modulo perimetrale](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable&preserve-view=true)

#### <a name="nodejs-device-sdk"></a>SDK per dispositivi Node.js

* [Repository GitHub](https://github.com/Azure/azure-iot-sdk-node)
* [Esempi](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Pacchetto](https://www.npmjs.com/package/azure-iot-device)
* [Documentazione di riferimento](/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest&preserve-view=true)
* [Documentazione di riferimento sul modulo perimetrale](/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest&preserve-view=true)

#### <a name="python-device-sdk"></a>SDK per dispositivi Python

* [Repository GitHub](https://github.com/Azure/azure-iot-sdk-python)
* [Esempi](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Pacchetto](https://pypi.org/project/azure-iot-device/)
* [Documentazione di riferimento](/python/api/azure-iot-device)
* [Documentazione di riferimento sul modulo perimetrale](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python&preserve-view=true)

### <a name="service-sdks"></a>SDK per servizi
Azure Internet è dotato anche di SDK di servizi che consentono di creare applicazioni sul lato soluzione per gestire i dispositivi, ottenere informazioni dettagliate, visualizzare i dati e altro ancora. Questi SDK sono specifici per ogni servizio Azure e sono disponibili in C#, Java, JavaScript e Python per semplificare l'esperienza di sviluppo. 

#### <a name="iot-hub"></a>Hub IoT

Gli SDK del servizio Hub di Internet delle cose consentono di creare applicazioni che interagiscono facilmente con l'hub Internet per gestire i dispositivi e la sicurezza. È possibile usare questi SDK per inviare messaggi da cloud a dispositivo, richiamare metodi diretti nei dispositivi, aggiornare le proprietà del dispositivo e altro ancora.

[**Scopri di più sull'hub**](https://azure.microsoft.com/services/iot-hub/)  |  Internet [ **Provare a controllare un dispositivo**](../iot-hub/quickstart-control-device-python.md)

**SDK del servizio Hub** Internet per C#: documentazione di riferimento per [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [esempi](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [](/dotnet/api/microsoft.azure.devices) di pacchetti del repository GitHub

**SDK per servizi dell'hub** Internet di Java: documentazione di [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [riferimento](/java/api/com.microsoft.azure.sdk.iot.service) per esempi di pacchetti del repository GitHub

**SDK del servizio Hub** Internet per JavaScript: documentazione di riferimento per [](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [](https://www.npmjs.com/package/azure-iothub)  |  [esempi](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [](/javascript/api/azure-iothub/?view=azure-iot-typescript-latest&preserve-view=true) di pacchetti del repository GitHub

**SDK del servizio Hub** Internet di Python: documentazione di riferimento per [](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [](https://pypi.python.org/pypi/azure-iot-hub/)  |  [esempi](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [](/python/api/azure-iot-hub) di pacchetti del repository GitHub

#### <a name="azure-digital-twins"></a>Gemelli digitali di Azure

Gemelli digitali di Azure è un'offerta PaaS che consente la creazione di grafici di conoscenze basati su modelli digitali di interi ambienti. Questi ambienti possono essere edifici, fabbriche, fattorie, reti energetiche, ferrovie, stadi e altro ancora, persino intere città. Questi modelli digitali possono essere usati per ottenere informazioni che consentono di migliorare i prodotti, ottimizzare le operazioni, ridurre i costi e rivoluzionare l'esperienza dei clienti. Azure Internet è dotato di SDK per i servizi che semplificano la creazione di applicazioni che usano la potenza dei dispositivi gemelli digitali di Azure.

[**Scopri di più sui dispositivi gemelli**](https://azure.microsoft.com/services/digital-twins/)  |  digitali di Azure [ **Codificare un'applicazione ADT**](../digital-twins/tutorial-code.md)

**C# ADT Service SDK**: [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [documentazione di riferimento](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) per esempi di pacchetti del repository GitHub

**Java ADT Service SDK**: [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [documentazione di riferimento](/java/api/overview/azure/digitaltwins/client?preserve-view=true&view=azure-java-stable) per esempi di pacchetti del repository GitHub

**Node.js ADT Service SDK**: [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [documentazione di riferimento](/javascript/api/@azure/digital-twins-core/?branch=master&view=azure-node-latest&preserve-view=true) per esempi di pacchetti del repository GitHub

**Python ADT Service SDK**: [](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://pypi.org/project/azure-digitaltwins-core/)  |  [](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [documentazione di riferimento](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true) per esempi di pacchetti del repository GitHub

#### <a name="device-provisioning-service"></a>Servizio Device Provisioning

Il servizio Device Provisioning in hub IoT è un servizio helper per Hub IoT che consente di effettuare il provisioning JIT automatizzato nell'hub IoT corretto senza alcun intervento dell'utente. DPS consente il provisioning di milioni di dispositivi in modo sicuro e scalabile. Gli SDK del servizio DPS consentono di creare applicazioni in grado di gestire i dispositivi in modo sicuro creando gruppi di registrazione ed eseguendo operazioni bulk.

[**Altre informazioni sul servizio**](../iot-dps/index.yml)  |  Device provisioning [ **Provare a creare una registrazione di gruppo per i dispositivi X. 509**](../iot-dps/quick-enroll-device-x509-csharp.md)

**SDK del servizio Device provisioning in C#**: documentazione di [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [riferimento](/dotnet/api/microsoft.azure.devices.provisioning.service?view=azure-dotnet&preserve-view=true) per esempi di pacchetti del repository GitHub

**SDK del servizio Device provisioning in Java**: documentazione di [](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [riferimento](/java/api/com.microsoft.azure.sdk.iot.provisioning.service?view=azure-java-stable&preserve-view=true) per esempi di pacchetti del repository GitHub

**Node.js Device Provisioning Service SDK**: [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [documentazione di riferimento](/javascript/api/azure-iot-provisioning-service) per esempi di pacchetti del repository GitHub

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: connettere un dispositivo a IoT Central (Python)](quickstart-send-telemetry-python.md)
* [Guida introduttiva: connettere un dispositivo all'hub Internet (Python)](quickstart-send-telemetry-cli-python.md)
* [Introduzione allo sviluppo incorporato](quickstart-device-development.md)
* Scopri di più sui [vantaggi dello sviluppo usando gli SDK di Azure](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)