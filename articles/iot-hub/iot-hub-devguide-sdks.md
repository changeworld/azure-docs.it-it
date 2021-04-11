---
title: Informazioni sugli SDK dell'hub IoT | Documentazione Microsoft
description: 'Guida per gli sviluppatori: informazioni e collegamenti ai vari Azure IoT SDK per dispositivi e servizi che è possibile usare per compilare app per dispositivo e app back-end.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 1a94bdfd03d4e48495601b5c494204ac1ad50378
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168332"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Comprendere e usare gli SDK per l'hub IoT di Azure

Esistono due categorie di Software Development Kit (SDK) per l'uso con l'hub IoT:

* Gli **SDK per dispositivi dell'hub** Internet che consentono di creare app che vengono eseguite sui dispositivi di Internet delle cose usando il client del dispositivo o il client del modulo. Queste app inviano la telemetria all'hub IoT e, facoltativamente, ricevono messaggi, processi, metodi o aggiornamenti dei dispositivi gemelli dall'hub IoT. È possibile usare questi SDK per creare app per dispositivi che usano le convenzioni e i modelli di [Azure internet plug and Play](../iot-pnp/overview-iot-plug-and-play.md) per annunciare le proprie capacità alle applicazioni che supportano Plug and Play. È anche possibile usare il client per moduli per creare [moduli](../iot-edge/iot-edge-modules.md) per il [runtime di Azure IoT Edge](../iot-edge/about-iot-edge.md).

* Gli **SDK del servizio Hub** Internet di base consentono di creare applicazioni back-end per gestire l'hub Internet e, facoltativamente, inviare messaggi, pianificare processi, richiamare metodi diretti o inviare aggiornamenti delle proprietà desiderati ai dispositivi o ai moduli Internet.

Verrà inoltre fornito un set di SDK per l'uso del [servizio Device Provisioning](../iot-dps/about-iot-dps.md).

* Gli **SDK per i dispositivi di provisioning** consentono di creare app eseguibili nei dispositivi IoT per comunicare con il servizio Device Provisioning.

* Gli **SDK per il servizio di provisioning** consentono di creare applicazioni back-end per gestire le registrazioni nel servizio Device Provisioning.

Altre informazioni sui [ vantaggi offerti dallo sviluppo con gli SDK di Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="os-platform-and-hardware-compatibility"></a>Compatibilità delle piattaforme del sistema operativo e hardware

È possibile trovare le piattaforme supportate per gli SDK in [Supporto della piattaforma Azure IoT SDK](iot-hub-device-sdk-platform-support.md).

Per altre informazioni sulla compatibilità SDK con i dispositivi hardware specifici, vedere il [catalogo di dispositivi Azure Certified per IoT](https://devicecatalog.azure.com/) o lo specifico repository.

## <a name="azure-iot-hub-device-sdks"></a>SDK per dispositivi hub IoT di Azure

Microsoft Azure IoT SDK per dispositivi contiene codice che facilita la compilazione delle applicazioni che si connettono e sono gestite dai servizi hub IoT di Azure.

Azure IoT Hub SDK per dispositivi per .NET: 

* Scaricare da [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Lo spazio dei nomi è Microsoft.Azure.Devices.Clients, che contiene i client per dispositivi hub IoT (DeviceClient, ModuleClient).
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-csharp)
* [Informazioni di riferimento sulle API](/dotnet/api/microsoft.azure.devices)
* [Riferimento al modulo](/dotnet/api/microsoft.azure.devices.client.moduleclient)


SDK per dispositivi hub Azure Internet per Embedded C (ANSI C-C99):
* [Compilare l'SDK di C incorporato](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Codice sorgente](https://github.com/Azure/azure-sdk-for-c)
* [Grafico delle dimensioni](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart) per i dispositivi vincolati.
* [Informazioni di riferimento sulle API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Identity/1.0.0/api/index.html)


Azure IoT Hub SDK per dispositivi per C (ANSI C - C99):

* Installare da [apt-get, MBED, IDE Arduino o iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-c)
* [Compilare l'SDK per dispositivi C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Informazioni di riferimento sulle API](/azure/iot-hub/iot-c-sdk-ref/)
* [Riferimento al modulo](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Porting dell'SDK per C in altre piattaforme](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentazione per sviluppatori](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) per informazioni su compilazione incrociata, introduzione alle diverse piattaforme e così via.
* [Informazioni sull'utilizzo delle risorse dell'hub Azure per Azure](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Azure IoT Hub SDK per dispositivi per Java:

* Aggiungere al progetto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-java)
* [Informazioni di riferimento sulle API](/java/api/com.microsoft.azure.sdk.iot.device)
* [Riferimento al modulo](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

Azure IoT Hub SDK per dispositivi per Node.js:

* Installare da [npm](https://www.npmjs.com/package/azure-iot-device)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-node)
* [Informazioni di riferimento sulle API](/javascript/api/azure-iot-device/)
* [Riferimento al modulo](/javascript/api/azure-iot-device/moduleclient)

Azure IoT Hub SDK per dispositivi per Python:

* Installare da [pip](https://pypi.org/project/azure-iot-device/)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-python)
* [Informazioni di riferimento sulle API](/python/api/azure-iot-device)

Azure IoT Hub SDK per dispositivi per iOS:

* Installare da [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Esempi](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Riferimento all'API: vedere [riferimento all'API C](/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>SDK per il servizio hub IoT di Azure

I componenti Azure Iot SDK per servizi contengono codice che facilita la compilazione di applicazioni che interagiscono direttamente con l'hub IoT per gestire dispositivi e sicurezza.

Azure IoT Hub SDK per servizi per .NET:

* Scaricare da [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Lo spazio dei nomi è Microsoft.Azure.Devices, che contiene i client per il servizio hub IoT (RegistryManager, ServiceClients).
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-csharp)
* [Informazioni di riferimento sulle API](/dotnet/api/microsoft.azure.devices)

Azure IoT Hub SDK per servizi per Java:

* Aggiungere al progetto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-java)
* [Informazioni di riferimento sulle API](/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub SDK per servizi per Node.js:

* Scaricare da [npm](https://www.npmjs.com/package/azure-iothub)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-node)
* [Informazioni di riferimento sulle API](/javascript/api/azure-iothub/)

Azure IoT Hub SDK per servizi per Python:

* Scaricare da [pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-python/tree/master)
* [Informazioni di riferimento sulle API](/python/api/azure-iot-hub)

Azure IoT Hub SDK per servizi per C:

L'SDK del servizio Azure per la distribuzione di Azure per C non è più in fase di sviluppo attivo.
Si continuerà a correggere i bug critici, ad esempio arresti anomali, danneggiamento dei dati e vulnerabilità della sicurezza. Tuttavia, non verrà aggiunta alcuna nuova funzionalità o verranno risolti i bug non critici.

Il supporto dell'SDK del servizio Azure è disponibile in linguaggi di livello superiore ([C#](https://github.com/Azure/azure-iot-sdk-csharp), [Java](https://github.com/Azure/azure-iot-sdk-java), [node](https://github.com/Azure/azure-iot-sdk-node), [Python](https://github.com/Azure/azure-iot-sdk-python)).

* Download da [apt-get, mbed, IDE Arduino o NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub SDK per servizi per iOS:

* Installare da [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Esempi](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Vedere i file Leggimi nei repository GitHub per informazioni sull'uso di gestori di pacchetti specifici per piattaforma e linguaggio e installare file binari e dipendenze nel computer di sviluppo.

## <a name="microsoft-azure-provisioning-sdks"></a>SDK di provisioning di Microsoft Azure

**Gli SDK del provisioning di Microsoft Azure** consentono di eseguire il provisioning dei dispositivi all'hub IoT usando il [servizio di provisioning di dispositivi](../iot-dps/about-iot-dps.md).

SDK dei dispositivi di provisioning e di servizio di Azure per C#:

* Scaricare l'[SDK per dispositivi](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) e l'[SDK per il servizio](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) da NuGet.
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Informazioni di riferimento sulle API](/dotnet/api/microsoft.azure.devices.provisioning.client)

SDK dei dispositivi di provisioning e di servizio di Azure per C:

* Installare da [apt-get, MBED, IDE Arduino o iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Informazioni di riferimento sulle API](/azure/iot-hub/iot-c-sdk-ref/)

SDK dei dispositivi di provisioning e di servizio di Azure per Java:

* Aggiungere al progetto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Informazioni di riferimento sulle API](/java/api/com.microsoft.azure.sdk.iot.provisioning.device)

SDK dei dispositivi di provisioning e di servizio di Azure per Node.js:

* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Informazioni di riferimento sulle API](/javascript/api/overview/azure/iothubdeviceprovisioning)
* Scaricare l'[SDK per dispositivi](https://badge.fury.io/js/azure-iot-provisioning-device) e l'[SDK per il servizio](https://badge.fury.io/js/azure-iot-provisioning-service) da npm

SDK dei dispositivi di provisioning e di servizio di Azure per Python:

* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-python)
* Scaricare l'[SDK per dispositivi](https://pypi.org/project/azure-iot-device/) e l'[SDK per il servizio](https://pypi.org/project/azure-iothub-provisioningserviceclient/) da pip

## <a name="next-steps"></a>Passaggi successivi

La piattaforma di Azure IoT SDK fornisce anche un set di strumenti per facilitare lo sviluppo:

* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): uno strumento multipiattaforma da riga di comando che consente di diagnosticare i problemi relativi alla connessione all'hub IoT.
* [Azure-Internet Explorer](https://github.com/Azure/azure-iot-explorer): un'applicazione desktop multipiattaforma per connettersi all'hub Internet e aggiungere/gestire/comunicare con i dispositivi Internet.

Documentazione pertinente relativa allo sviluppo tramite gli SDK IoT di Azure:

* Informazioni su [come gestire le funzionalità di connettività e messaggistica affidabile](iot-hub-reliability-features-in-sdks.md) con gli SDK per l'hub IoT.
* Informazioni su come [sviluppare per piattaforme per dispositivi mobili](iot-hub-how-to-develop-for-mobile-devices.md) come iOS e Android.
* [Supporto della piattaforma di Azure IoT SDK](iot-hub-device-sdk-platform-support.md)

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint hub IoT](iot-hub-devguide-endpoints.md)
* [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi](iot-hub-devguide-query-language.md)
* [Quote e limitazioni](iot-hub-devguide-quotas-throttling.md)
* [Supporto di MQTT nell'hub IoT](iot-hub-mqtt-support.md)
* [Riferimento all'API REST dell'hub Internet](/rest/api/iothub/)
