---
title: Panoramica delle opzioni Azure IoT SDK per dispositivi
description: Informazioni su Azure IoT SDK per dispositivi da usare in base al ruolo e alle attività di sviluppo.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c35a9045bf809c03630fbb7c57f9d31e7b143422
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876458"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Panoramica degli SDK Azure IoT dispositivo

Gli AZURE IOT sdk per dispositivi sono un set di librerie client di dispositivi, guide per sviluppatori, esempi e documentazione. Gli SDK per dispositivi consentono di connettere i dispositivi a livello di codice Azure IoT servizi.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagramma che mostra vari AZURE IOT SDK":::

Come illustrato nel diagramma, sono disponibili diversi SDK per dispositivi in base alle esigenze del dispositivo e del linguaggio di programmazione. Le indicazioni per la selezione dell'SDK del dispositivo appropriato sono disponibili in [Quale SDK è consigliabile usare](#which-sdk-should-i-use). Sono disponibili anche Azure IoT SDK del servizio per connettere l'applicazione basata sul cloud Azure IoT servizi nel back-end. Questo articolo è in particolare sugli SDK per dispositivi, ma è possibile ottenere altre informazioni sugli SDK del servizio di Azure [qui.](#service-sdks)

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Perché è consigliabile usare gli SDK Azure IoT dispositivo?

Per connettere i dispositivi Azure IoT, è possibile creare un livello di connessione personalizzato o usare Azure IoT SDK per dispositivi. L'uso degli SDK Azure IoT dispositivo offre diversi vantaggi:

| Costo di sviluppo &nbsp; &nbsp; &nbsp;&nbsp; | Livello di connessione personalizzato | Azure IoT SDK del dispositivo |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Supporto | È necessario supportare e documentare tutto ciò che si compila | Avere accesso al supporto Microsoft (GitHub, Microsoft Q&A, Microsoft Docs, team di supporto clienti) |
| Nuove funzioni e caratteristiche | È necessario aggiungere nuove funzionalità di Azure al middleware personalizzato | Può sfruttare immediatamente le nuove funzionalità che Microsoft aggiunge costantemente agli SDK IoT |
| Investimento | Investire centinaia di ore di sviluppo incorporato per progettare, compilare, testare e gestire una versione personalizzata | Può sfruttare i vantaggi di strumenti open source gratuiti. L'unico costo associato agli SDK è la curva di apprendimento. |

## <a name="which-sdk-should-i-use"></a>Quale SDK è consigliabile usare?

Azure IoT SDK per dispositivi sono disponibili nei linguaggi di programmazione più diffusi, tra cui C, C#, Java, Node.js e Python. Quando si sceglie un SDK, è necessario tenere presenti due considerazioni principali: le funzionalità del dispositivo e la familiarità del team con il linguaggio di programmazione.

### <a name="device-capabilities"></a>Funzionalità di dispositivo

Quando si sceglie un SDK, è necessario considerare i limiti dei dispositivi in uso. Un dispositivo vincolato è un dispositivo con un singolo micro controller (MCU) e memoria limitata. Se si usa un dispositivo vincolato, è consigliabile usare [Embedded C SDK.](#embedded-c-sdk) Questo SDK è progettato per offrire il set minimo di funzionalità per la connessione Azure IoT. È anche possibile selezionare i componenti (librerie client MQTT, TLS e socket) più ottimizzati per il dispositivo incorporato. Se il dispositivo vincolato esegue Azure RTOS, è possibile usare il middleware Azure RTOS per connettersi al Azure IoT. Il middleware Azure RTOS esegue il wrapping di Embedded C SDK con funzionalità aggiuntive per semplificare la connessione Azure RTOS dispositivo al cloud.

Un dispositivo senza vincoli ha una CPU più affidabile, in grado di eseguire un sistema operativo per supportare un runtime del linguaggio, ad esempio .NET o Python. Se si usa un dispositivo senza vincoli, la considerazione principale è la conoscenza del linguaggio.

### <a name="your-teams-familiarity-with-the-programming-language"></a>Familiarità del team con il linguaggio di programmazione

Azure IoT SDK per dispositivi vengono implementati in più lingue, quindi è possibile scegliere il linguaggio preferito. Gli SDK per dispositivi si integrano anche con altri strumenti familiari specifici del linguaggio. La possibilità di usare un linguaggio di sviluppo e strumenti familiari consente al team di ottimizzare il ciclo di sviluppo di ricerca, prototipazione, sviluppo di prodotti e manutenzione continua.

Quando possibile, selezionare un SDK familiare al team di sviluppo. Tutti Azure IoT SDK sono open source e hanno diversi esempi che il team può valutare e testare prima di eseguire il commit in un SDK specifico.

## <a name="how-can-i-get-started"></a>Operazioni iniziali

Il punto di partenza è esplorare i repository GitHub degli SDK per dispositivi di Azure. È anche possibile provare una [guida introduttiva che](quickstart-send-telemetry-python.md) illustra come usare rapidamente un SDK per inviare dati di telemetria Azure IoT.

Le opzioni per iniziare dipendono dal tipo di dispositivo in uso:
- Per i dispositivi vincolati, usare [Embedded C SDK.](#embedded-c-sdk) 
- Per i dispositivi eseguiti Azure RTOS, è possibile sviluppare con il [middleware Azure RTOS .](#azure-rtos-middleware) 
- Per i dispositivi senza vincoli, è possibile [scegliere un SDK](#unconstrained-device-sdks) in un linguaggio di propria scelta. 

### <a name="constrained-device-sdks"></a>SDK per dispositivi vincolati
Questi SDK sono specializzati per l'esecuzione in dispositivi con risorse di calcolo o memoria limitate. Per altre informazioni sui tipi di dispositivo comuni, vedere [Panoramica Azure IoT tipi di dispositivo.](concepts-iot-device-types.md)

#### <a name="embedded-c-sdk"></a>SDK C incorporato
* [GitHub Repository](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Esempi](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Documentazione di riferimento](https://azure.github.io/azure-sdk-for-c/)
* [Come compilare Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Grafico delle dimensioni per i dispositivi vincolati](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS Middleware

* [GitHub Repository](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Attività iniziali guide e](https://github.com/azure-rtos/getting-started) [altri esempi](https://github.com/azure-rtos/samples)
* [Documentazione di riferimento](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>SDK per dispositivi non vincolati
Questi SDK possono essere eseguiti in qualsiasi dispositivo in grado di supportare un runtime di linguaggio di ordine superiore. Sono inclusi dispositivi come PC, Raspberry Pis e smartphone. Si differenziano principalmente in base al linguaggio, quindi è possibile scegliere la libreria più adatta al team e allo scenario.

#### <a name="c-device-sdk"></a>SDK per dispositivi C
* [GitHub Repository](https://github.com/Azure/azure-iot-sdk-c)
* [Esempi](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Pacchetti](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Documentazione di riferimento](/azure/iot-hub/iot-c-sdk-ref/)
* [Documentazione di riferimento del modulo Edge](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Compilare l'SDK per dispositivi C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Porting dell'SDK per C in altre piattaforme](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentazione per](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) sviluppatori per informazioni sulla compilazione incrociata e introduzione su piattaforme diverse
* [hub IoT di Azure sul consumo di risorse dell'SDK C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>SDK per dispositivi C#

* [GitHub Repository](https://github.com/Azure/azure-iot-sdk-csharp)
* [Esempi](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Pacchetto](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Documentazione di riferimento](/dotnet/api/microsoft.azure.devices)
* [Documentazione di riferimento del modulo Edge](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java Device SDK

* [GitHub Repository](https://github.com/Azure/azure-iot-sdk-java)
* [Esempi](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Pacchetto](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Documentazione di riferimento](/java/api/com.microsoft.azure.sdk.iot.device)
* [Documentazione di riferimento del modulo Edge](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js SDK per dispositivi

* [GitHub Repository](https://github.com/Azure/azure-iot-sdk-node)
* [Esempi](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Pacchetto](https://www.npmjs.com/package/azure-iot-device)
* [Documentazione di riferimento](/javascript/api/azure-iot-device/)
* [Documentazione di riferimento del modulo Edge](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Python Device SDK

* [GitHub Repository](https://github.com/Azure/azure-iot-sdk-python)
* [Esempi](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Pacchetto](https://pypi.org/project/azure-iot-device/)
* [Documentazione di riferimento](/python/api/azure-iot-device)
* [Documentazione di riferimento del modulo Edge](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>SDK per servizi
Azure IoT offre anche SDK di servizio che consentono di compilare applicazioni lato soluzione per gestire i dispositivi, ottenere informazioni dettagliate, visualizzare i dati e altro ancora. Questi SDK sono specifici per ogni servizio Azure IoT e sono disponibili in C#, Java, JavaScript e Python per semplificare l'esperienza di sviluppo. 

#### <a name="iot-hub"></a>Hub IoT

Gli SDK del servizio Hub IoT consentono di creare applicazioni che interagiscono facilmente con l'hub IoT per gestire i dispositivi e la sicurezza. È possibile usare questi SDK per inviare messaggi da cloud a dispositivo, richiamare metodi diretti nei dispositivi, aggiornare le proprietà del dispositivo e altro ancora.

[**Altre informazioni sull'hub**](https://azure.microsoft.com/services/iot-hub/)  |  IoT [ **Provare a controllare un dispositivo**](../iot-hub/quickstart-control-device-python.md)

**C# IoT Hub Service SDK**: Documentazione di riferimento degli esempi di pacchetti [del repository GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [](/dotnet/api/microsoft.azure.devices)

**Java IoT Hub Service SDK**: [Documentazione di riferimento degli esempi di pacchetti del repository GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [](/java/api/com.microsoft.azure.sdk.iot.service)

**JavaScript IoT Hub Service SDK**: Documentazione di riferimento degli esempi di pacchetti [del repository GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [](https://www.npmjs.com/package/azure-iothub)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [](/javascript/api/azure-iothub/)

**Python IoT Hub Service SDK**: Documentazione di riferimento degli esempi di pacchetti [del repository GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [](https://pypi.python.org/pypi/azure-iot-hub/)  |  [](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Gemelli digitali di Azure

Gemelli digitali di Azure è un'offerta PaaS che consente la creazione di grafici di conoscenze basati su modelli digitali di interi ambienti. Questi ambienti possono essere edifici, fabbriche, fattorie, reti energetiche, ferrovie, stadi e altro ancora, persino intere città. Questi modelli digitali possono essere usati per ottenere informazioni che consentono di migliorare i prodotti, ottimizzare le operazioni, ridurre i costi e rivoluzionare l'esperienza dei clienti. Azure IoT offre SDK di servizio per semplificare la creazione di applicazioni che usano la potenza Gemelli digitali di Azure.

[**Altre informazioni sulle Gemelli digitali di Azure**](https://azure.microsoft.com/services/digital-twins/)  |  [ **Codificare un'applicazione ADT**](../digital-twins/tutorial-code.md)

**C# ADT Service SDK**: Documentazione di riferimento [sugli esempi di pacchetti del repository GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [](/dotnet/api/overview/azure/digitaltwins/client)

**Java ADT Service SDK**: Documentazione di riferimento sugli esempi di pacchetti [del repository GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [](/java/api/overview/azure/digitaltwins/client)

**Node.js SDK del servizio ADT:** Documentazione di riferimento sugli esempi di pacchetti [del repository GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [](/javascript/api/@azure/digital-twins-core/)

**Python ADT Service SDK**: Documentazione di riferimento sugli esempi di pacchetti [del repository GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://pypi.org/project/azure-digitaltwins-core/)  |  [](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Servizio Device Provisioning

Il servizio Device Provisioning in hub IoT è un servizio helper per Hub IoT che consente di effettuare il provisioning JIT automatizzato nell'hub IoT corretto senza alcun intervento dell'utente. DPS consente il provisioning di milioni di dispositivi in modo sicuro e scalabile. Gli SDK del servizio DPS consentono di creare applicazioni in grado di gestire in modo sicuro i dispositivi creando gruppi di registrazione ed eseguendo operazioni bulk.

[**Altre informazioni sul servizio Device Provisioning**](../iot-dps/index.yml)  |  [ **Provare a creare una registrazione di gruppo per i dispositivi X.509**](../iot-dps/quick-enroll-device-x509-csharp.md)

**SDK del servizio Device Provisioning in C#:** [documentazione di riferimento degli esempi di pacchetti del repository GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [](/dotnet/api/microsoft.azure.devices.provisioning.service)

**Java Device Provisioning Service SDK:** [documentazione di riferimento degli esempi di pacchetti](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [del](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [](/java/api/com.microsoft.azure.sdk.iot.provisioning.service) repository GitHub

**Node.js Sdk del servizio Device Provisioning:** documentazione [di riferimento degli esempi di pacchetti del repository GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Connettere un dispositivo a IoT Central (Python)](quickstart-send-telemetry-python.md)
* [Guida introduttiva: Connettere un dispositivo all'hub IoT (Python)](quickstart-send-telemetry-cli-python.md)
* [Introduzione allo sviluppo incorporato](quickstart-device-development.md)
* Altre informazioni sui [vantaggi dello sviluppo con Azure IoT SDK](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)