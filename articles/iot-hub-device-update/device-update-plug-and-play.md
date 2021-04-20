---
title: Informazioni su come l'aggiornamento dei dispositivi per l'hub IoT usa IoT Plug and Play | Microsoft Docs
description: L'aggiornamento dei dispositivi per l'hub IoT usa per individuare e gestire i dispositivi che supportano l'aggiornamento over-the-air.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: fbc3502952e11830ef9abb06cb709fcc60288343
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739531"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Aggiornamento del dispositivo per l'hub IoT e IoT Plug and Play

L'aggiornamento dei dispositivi per l'hub IoT [IoT Plug and Play](../iot-pnp/index.yml) per individuare e gestire i dispositivi che supportano l'aggiornamento over-the-air. Il servizio Aggiornamento dispositivo invierà e riceverà proprietà e messaggi da e verso i dispositivi usando le interfacce PnP. L'aggiornamento del dispositivo per l'hub IoT richiede che i dispositivi IoT implementino le interfacce e l'ID modello seguenti, come descritto di seguito.

## <a name="adu-core-interface"></a>Interfaccia ADU Core

L'interfaccia 'ADUCoreInterface' viene usata per inviare azioni di aggiornamento e metadati ai dispositivi e ricevere lo stato di aggiornamento dai dispositivi. L'interfaccia "ADU Core" è suddivisa in due proprietà object.

Il nome del componente previsto nel modello è **"azureDeviceUpdateAgent"** quando si implementa questa interfaccia. [Altre informazioni sui Azure IoT PnP](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Metadati dell'agente

I metadati dell'agente contengono campi che il dispositivo o l'agente di aggiornamento del dispositivo usa per inviare informazioni e stato ai servizi di aggiornamento dispositivi.

|Nome|SCHEMA|Direzione|Descrizione|Esempio|
|----|------|---------|-----------|-----------|
|resultCode|numero intero|da dispositivo a cloud|Codice che contiene informazioni sul risultato dell'ultima azione di aggiornamento. Può essere popolato per esito positivo o negativo e deve seguire [la specifica del codice di stato HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).|500|
|extendedResultCode|numero intero|da dispositivo a cloud|Codice che contiene informazioni aggiuntive sul risultato. Può essere popolato per esito positivo o negativo.|0x80004005|
|state|numero intero|da dispositivo a cloud|È un numero intero che indica lo stato corrente dell'agente di aggiornamento dispositivi. Vedere di seguito per ulteriori dettagli |Idle|
|installedUpdateId|string|da dispositivo a cloud|ID dell'aggiornamento attualmente installato (tramite Aggiornamento dispositivo). Questo valore sarà Null per un dispositivo che non ha mai effettuato un aggiornamento tramite l'aggiornamento del dispositivo.|Null|
|`deviceProperties`|Mappa|da dispositivo a cloud|Set di proprietà che contengono il produttore e il modello.|Vedere di seguito per ulteriori dettagli

#### <a name="state"></a>State

Si tratta dello stato segnalato dall'agente di aggiornamento dispositivi dopo la ricezione di un'azione dal servizio aggiornamento dispositivi. `State` viene segnalato in risposta a `Action` un (vedere di `Actions` seguito) inviato all'agente di aggiornamento dispositivi dal servizio aggiornamento dispositivi. Vedere il [flusso di lavoro di](understand-device-update.md#device-update-agent) panoramica per le richieste che scorrono tra il servizio Aggiornamento dispositivi e l'agente di aggiornamento dispositivi.

|Nome|Valore|Descrizione|
|---------|-----|-----------|
|Idle|0|Il dispositivo è pronto per ricevere un'azione dal servizio aggiornamento dispositivi. Dopo un aggiornamento riuscito, lo stato viene restituito allo `Idle` stato .|
|DownloadSucceeded|2|Download riuscito.|
|InstallSucceeded|4|Installazione completata.|
|Non riuscito|255|Si è verificato un errore durante l'aggiornamento.|

#### <a name="device-properties"></a>Proprietà dispositivo

È il set di proprietà che contengono il produttore e il modello.

|Nome|SCHEMA|Direzione|Descrizione|
|----|------|---------|-----------|
|manufacturer|string|da dispositivo a cloud|Produttore del dispositivo, segnalato tramite `deviceProperties` . Questa proprietà viene letta da una delle due posizioni seguenti: l'interfaccia 'AzureDeviceUpdateCore' tenterà prima di tutto di leggere il valore "aduc_manufacturer" dal file [di configurazione.](device-update-configuration-file.md)  Se il valore non viene popolato nel file di configurazione, per impostazione predefinita verrà segnalata la definizione in fase di compilazione per ADUC_DEVICEPROPERTIES_MANUFACTURER. Questa proprietà verrà segnalata solo in fase di avvio.|
|model|string|da dispositivo a cloud|Modello di dispositivo del dispositivo, segnalato tramite `deviceProperties` . Questa proprietà viene letta da una delle due posizioni seguenti: l'interfaccia AzureDeviceUpdateCore tenterà prima di tutto di leggere il valore "aduc_model" dal [file di configurazione.](device-update-configuration-file.md)  Se il valore non viene popolato nel file di configurazione, per impostazione predefinita verrà segnalata la definizione in fase di compilazione per ADUC_DEVICEPROPERTIES_MODEL. Questa proprietà verrà segnalata solo in fase di avvio.|
|aduVer|string|da dispositivo a cloud|Versione dell'agente di aggiornamento del dispositivo in esecuzione nel dispositivo. Questo valore viene letto dalla compilazione solo se durante la fase di ENABLE_ADU_TELEMETRY_REPORTING è impostato su 1 (true). I clienti possono scegliere di rifiutare esplicitamente la segnalazione delle versioni impostando il valore su 0 (false). [Come personalizzare le proprietà dell'agente di aggiornamento del dispositivo](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).|
|Dover|string|da dispositivo a cloud|Versione dell'Ottimizzazione recapito in esecuzione nel dispositivo. Il valore viene letto dalla compilazione solo se durante la fase di ENABLE_ADU_TELEMETRY_REPORTING è impostato su 1 (true). I clienti possono scegliere di rifiutare esplicitamente la segnalazione della versione impostando il valore su 0 (false). [Come personalizzare le Ottimizzazione recapito dell'agente.](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)|

### <a name="service-metadata"></a>Metadati del servizio

I metadati del servizio contengono i campi utilizzati dal servizio Aggiornamento dispositivi per comunicare azioni e dati all'agente di aggiornamento dispositivi.

|Nome|SCHEMA|Direzione|Descrizione|
|----|------|---------|-----------|
|azione|numero intero|da cloud a dispositivo|È un numero intero che corrisponde a un'azione che l'agente deve eseguire. Valori elencati di seguito.|
|updateManifest|string|da cloud a dispositivo|Usato per descrivere il contenuto di un aggiornamento. Generato dal manifesto [di importazione](import-update.md#create-a-device-update-import-manifest)|
|updateManifestSignature|Oggetto JSON|da cloud a dispositivo|Firma JSON Web (JWS) con chiavi Web JSON usate per la verifica dell'origine.|
|fileUrls|Mappa|da cloud a dispositivo|Mapping di `FileHash` a `DownloadUri` . Indica all'agente quali file scaricare e l'hash da usare per verificare che i file siano stati scaricati correttamente.|

#### <a name="action"></a>Azione

`Actions` di seguito rappresenta le azioni eseguite dall'agente di aggiornamento dispositivi come indicato dal servizio aggiornamento dispositivi. L'agente di aggiornamento del dispositivo segnala `State` un (vedere `State` la sezione precedente) che elabora l'oggetto `Action` ricevuto. Vedere il [flusso di lavoro di](understand-device-update.md#device-update-agent) panoramica per le richieste che scorrono tra il servizio Aggiornamento dispositivi e l'agente di aggiornamento dispositivi.

|Nome|Valore|Descrizione|
|---------|-----|-----------|
|Scarica|0|Scaricare il contenuto pubblicato o l'aggiornamento e qualsiasi altro contenuto necessario|
|Installazione|1|Installare il contenuto o l'aggiornamento. In genere questo significa chiamare il programma di installazione per il contenuto o l'aggiornamento.|
|Applica|2|Finalizzare l'aggiornamento. Segnala al sistema di riavviare se necessario.|
|Annulla|255|Arrestare l'elaborazione dell'azione corrente e tornare a `Idle` . Verrà usato anche per indicare all'agente nello `Failed` stato di tornare a `Idle` .|

## <a name="device-information-interface"></a>Interfaccia delle informazioni sul dispositivo

L'interfaccia di informazioni sul dispositivo è un concetto usato [all'interno IoT Plug and Play architettura](../iot-pnp/overview-iot-plug-and-play.md). Contiene le proprietà da dispositivo a cloud che forniscono informazioni sull'hardware e sul sistema operativo del dispositivo. L'aggiornamento del dispositivo per l'hub IoT usa le proprietà DeviceInformation.manufacturer e DeviceInformation.model per la telemetria e la diagnostica. Per altre informazioni sull'interfaccia device information, vedere questo [esempio.](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)

Il nome del componente previsto nel modello è **deviceInformation** quando si implementa questa interfaccia. [Informazioni sui Azure IoT PnP](../iot-pnp/concepts-components.md)

|Nome|Type|SCHEMA|Direzione|Descrizione|Esempio|
|----|----|------|---------|-----------|-----------|
|manufacturer|Proprietà|string|da dispositivo a cloud|Nome della società del produttore del dispositivo. Può essere uguale al nome del produttore OEM (Original Equipment Manufacturer).|Contoso|
|model|Proprietà|string|da dispositivo a cloud|ID o nome del modello di dispositivo.|IoT Edge dispositivo|
|swVersion|Proprietà|string|da dispositivo a cloud|Versione del software nel dispositivo. swVersion potrebbe essere la versione del firmware.|4.15.0-122|
|osName|Proprietà|string|da dispositivo a cloud|Nome del sistema operativo nel dispositivo.|Ubuntu Server 18.04|
|processorArchitecture|Proprietà|string|da dispositivo a cloud|Architettura del processore nel dispositivo.|ARM64|
|processorManufacturer|Proprietà|string|da dispositivo a cloud|Nome del produttore del processore nel dispositivo.|Microsoft|
|totalStorage|Proprietà|string|da dispositivo a cloud|Spazio di archiviazione totale disponibile nel dispositivo in kilobyte.|2048|
|totalMemory|Proprietà|string|da dispositivo a cloud|Memoria totale disponibile nel dispositivo in kilobyte.|256|

## <a name="model-id"></a>ID modello 

L'ID modello è il modo in cui i dispositivi intelligenti annunciano le proprie funzionalità alle applicazioni Azure IoT con Plug and Play.To IoT. Per altre informazioni su come creare dispositivi intelligenti per annunciare le proprie funzionalità alle applicazioni Azure IoT, vedere la guida per gli sviluppatori di dispositivi IoT Plug and Play [.](../iot-pnp/concepts-developer-guide-device.md)

L'aggiornamento del dispositivo per l'hub IoT richiede che lo smart device IoT Plug and Play annunci un ID modello con il valore **"dtmi:AzureDeviceUpdate;1"** come parte della connessione del dispositivo. [Informazioni su come annunciare un ID modello.](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement)