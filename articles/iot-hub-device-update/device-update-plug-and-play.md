---
title: Informazioni sul modo in cui l'aggiornamento dei dispositivi per l'hub Internet usa gli Plug and Play | Microsoft Docs
description: Aggiornamento del dispositivo per l'hub Internet usa per individuare e gestire i dispositivi che sono in grado di aggiornare gli aggiornamenti.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0283a84650abaadd454b4f5bca83d1473e443fb8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561815"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Aggiornamento del dispositivo per l'hub e l'Plug and Play Internet

L'aggiornamento del dispositivo per l'hub Internet delle cose USA [plug and Play](../iot-pnp/index.yml) per individuare e gestire i dispositivi che sono in grado di aggiornare gli aggiornamenti. Il servizio di aggiornamento del dispositivo invierà e riceverà le proprietà e i messaggi da e verso i dispositivi usando le interfacce PnP. Per l'aggiornamento del dispositivo per l'hub Internet è necessario che i dispositivi Internet implementino le interfacce e l'ID di modello seguenti, come descritto di seguito.

## <a name="adu-core-interface"></a>Interfaccia di base di ADU

L'interfaccia ' ADUCoreInterface ' viene usata per inviare le azioni di aggiornamento e i metadati ai dispositivi e ricevere lo stato di aggiornamento dai dispositivi. L'interfaccia ' ADU Core ' è divisa in due proprietà dell'oggetto.

Quando si implementa questa interfaccia, il nome del componente previsto nel modello è **"azureDeviceUpdateAgent"** . [Altre informazioni sui componenti PnP di Azure](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Metadati agente

I metadati degli agenti contengono i campi usati dal dispositivo o dall'agente di aggiornamento del dispositivo per inviare informazioni e lo stato ai servizi di aggiornamento del dispositivo.

|Nome|SCHEMA|Direzione|Descrizione|Esempio|
|----|------|---------|-----------|-----------|
|resultCode|numero intero|da dispositivo a cloud|Codice che contiene informazioni sul risultato dell'ultima azione di aggiornamento. Può essere popolato in caso di esito positivo o negativo e deve seguire la [specifica del codice di stato http](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).|500|
|extendedResultCode|numero intero|da dispositivo a cloud|Codice che contiene informazioni aggiuntive sul risultato. Può essere popolato in caso di esito positivo o negativo.|0x80004005|
|state|numero intero|da dispositivo a cloud|Si tratta di un numero intero che indica lo stato corrente dell'agente di aggiornamento del dispositivo. Vedere di seguito per ulteriori dettagli |Idle|
|installedUpdateId|string|da dispositivo a cloud|ID dell'aggiornamento attualmente installato (tramite l'aggiornamento del dispositivo). Questo valore sarà null per un dispositivo che non ha mai effettuato un aggiornamento tramite l'aggiornamento del dispositivo.|Null|
|`deviceProperties`|Mappa|da dispositivo a cloud|Set di proprietà che contengono il produttore e il modello.|Vedere di seguito per ulteriori dettagli

#### <a name="state"></a>State

Si tratta dello stato segnalato dall'agente di aggiornamento del dispositivo dopo la ricezione di un'azione da parte del servizio di aggiornamento del dispositivo. `State` viene segnalato in risposta a un `Action` (vedere di `Actions` seguito) inviato all'agente di aggiornamento del dispositivo dal servizio di aggiornamento del dispositivo. Vedere il [flusso di lavoro Panoramica](understand-device-update.md#device-update-agent) per le richieste che scorrono tra il servizio di aggiornamento del dispositivo e l'agente di aggiornamento del dispositivo.

|Nome|Valore|Descrizione|
|---------|-----|-----------|
|Idle|0|Il dispositivo è pronto per ricevere un'azione dal servizio di aggiornamento del dispositivo. Dopo un aggiornamento riuscito, lo stato viene restituito allo `Idle` stato.|
|DownloadSucceeded|2|Il download è riuscito.|
|InstallSucceeded|4|Installazione completata.|
|Non riuscito|255|Si è verificato un errore durante l'aggiornamento.|

#### <a name="device-properties"></a>Proprietà dispositivo

Si tratta del set di proprietà che contengono il produttore e il modello.

|Nome|SCHEMA|Direzione|Descrizione|
|----|------|---------|-----------|
|manufacturer|string|da dispositivo a cloud|Il produttore del dispositivo, segnalato tramite `deviceProperties` . Questa proprietà viene letta da una delle due posizioni: l'interfaccia ' AzureDeviceUpdateCore ' tenterà prima di leggere il valore ' aduc_manufacturer ' dal file del [file di configurazione](device-update-configuration-file.md) .  Se il valore non viene popolato nel file di configurazione, per impostazione predefinita verrà segnalato la definizione della fase di compilazione per ADUC_DEVICEPROPERTIES_MANUFACTURER. Questa proprietà verrà segnalata solo in fase di avvio.|
|model|string|da dispositivo a cloud|Modello del dispositivo, segnalato tramite `deviceProperties` . Questa proprietà viene letta da una delle due posizioni: l'interfaccia AzureDeviceUpdateCore tenterà prima di leggere il valore ' aduc_model ' dal file del [file di configurazione](device-update-configuration-file.md) .  Se il valore non viene popolato nel file di configurazione, per impostazione predefinita verrà segnalato la definizione della fase di compilazione per ADUC_DEVICEPROPERTIES_MODEL. Questa proprietà verrà segnalata solo in fase di avvio.|
|aduVer|string|da dispositivo a cloud|Versione dell'agente di aggiornamento del dispositivo in esecuzione nel dispositivo. Questo valore viene letto dalla compilazione solo se durante la fase di compilazione ENABLE_ADU_TELEMETRY_REPORTING è impostato su 1 (true). I clienti possono scegliere di rifiutare esplicitamente il reporting della versione impostando il valore su 0 (false). [Come personalizzare le proprietà dell'agente di aggiornamento del dispositivo](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).|
|doVer|string|da dispositivo a cloud|Versione dell'agente di ottimizzazione recapito in esecuzione nel dispositivo. Il valore viene letto dalla compilazione solo se durante la fase di compilazione ENABLE_ADU_TELEMETRY_REPORTING è impostato su 1 (true). I clienti possono scegliere di rifiutare esplicitamente il reporting della versione impostando il valore su 0 (false). [Come personalizzare le proprietà dell'agente di ottimizzazione recapito](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components).|

### <a name="service-metadata"></a>Metadati del servizio

I metadati del servizio contengono i campi usati dai servizi di aggiornamento del dispositivo per comunicare le azioni e i dati all'agente di aggiornamento del dispositivo.

|Nome|SCHEMA|Direzione|Descrizione|
|----|------|---------|-----------|
|azione|numero intero|da cloud a dispositivo|Si tratta di un numero intero che corrisponde a un'azione che deve essere eseguita dall'agente. Valori elencati di seguito.|
|updateManifest|string|da cloud a dispositivo|Utilizzato per descrivere il contenuto di un aggiornamento. Generato dal [manifesto di importazione](import-update.md#create-device-update-import-manifest)|
|updateManifestSignature|Oggetto JSON|da cloud a dispositivo|Firma Web JSON (JWS) con chiavi Web JSON usate per la verifica dell'origine.|
|URL|Mappa|da cloud a dispositivo|Mappa di `FileHash` a `DownloadUri` . Indica all'agente quali file scaricare e hash da usare per verificare che i file siano stati scaricati correttamente.|

#### <a name="action"></a>Azione

`Actions` di seguito sono rappresentate le azioni eseguite dall'agente di aggiornamento del dispositivo come indicato dal servizio di aggiornamento del dispositivo. L'agente di aggiornamento del dispositivo segnalerà un `State` (vedere `State` la sezione precedente) che elabora l'oggetto `Action` ricevuto. Vedere il [flusso di lavoro Panoramica](understand-device-update.md#device-update-agent) per le richieste che scorrono tra il servizio di aggiornamento del dispositivo e l'agente di aggiornamento del dispositivo.

|Nome|Valore|Descrizione|
|---------|-----|-----------|
|Scarica|0|Scaricare contenuto pubblicato o aggiornare e qualsiasi altro contenuto necessario|
|Installazione|1|Installare il contenuto o aggiornare. In genere, ciò significa chiamare il programma di installazione per il contenuto o l'aggiornamento.|
|Applica|2|Finalizzare l'aggiornamento. Segnala il riavvio del sistema, se necessario.|
|Annulla|255|Arrestare l'elaborazione dell'azione corrente e tornare a `Idle` . Verrà utilizzato anche per indicare all'agente nello `Failed` stato di tornare a `Idle` .|

## <a name="device-information-interface"></a>Interfaccia informazioni sul dispositivo

L'interfaccia informazioni sul dispositivo è un concetto usato all'interno dell' [architettura plug and Play](../iot-pnp/overview-iot-plug-and-play.md). Contiene le proprietà da dispositivo a cloud che forniscono informazioni sull'hardware e sul sistema operativo del dispositivo. L'aggiornamento del dispositivo per l'hub Internet usa le proprietà DeviceInformation. Manufacturer e DeviceInformation. Model per la telemetria e la diagnostica. Per ulteriori informazioni sull'interfaccia informazioni sul dispositivo, vedere questo [esempio](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json).

Il nome del componente previsto nel modello è **DeviceInformation** quando si implementa questa interfaccia. [Informazioni sui componenti PnP di Azure](../iot-pnp/concepts-components.md)

|Nome|Tipo|SCHEMA|Direzione|Descrizione|Esempio|
|----|----|------|---------|-----------|-----------|
|manufacturer|Proprietà|string|da dispositivo a cloud|Nome della società del produttore del dispositivo. Potrebbe corrispondere al nome dell'OEM (Original Equipment Manufacturer).|Contoso|
|model|Proprietà|string|da dispositivo a cloud|Nome o ID del modello di dispositivo.|Dispositivo IoT Edge|
|swVersion|Proprietà|string|da dispositivo a cloud|Versione del software nel dispositivo. swVersion potrebbe essere la versione del firmware.|4.15.0-122|
|osName|Proprietà|string|da dispositivo a cloud|Nome del sistema operativo del dispositivo.|Ubuntu Server 18.04|
|processorArchitecture|Proprietà|string|da dispositivo a cloud|Architettura del processore nel dispositivo.|ARM64|
|processorManufacturer|Proprietà|string|da dispositivo a cloud|Nome del produttore del processore nel dispositivo.|Microsoft|
|totalStorage|Proprietà|string|da dispositivo a cloud|Totale dello spazio di archiviazione disponibile nel dispositivo, espressa in kilobyte.|2048|
|totalMemory|Proprietà|string|da dispositivo a cloud|Memoria totale disponibile del dispositivo, espressa in kilobyte.|256|

## <a name="model-id"></a>ID modello 

ID modello è il modo in cui i dispositivi intelligenti annunciano le proprie funzionalità alle applicazioni Azure Internet con i plug-in e Play.To altre informazioni su come creare Smart Device per annunciare le proprie capacità alle Plug and Play applicazioni Azure Internet per gli [sviluppatori](../iot-pnp/concepts-developer-guide-device.md)di dispositivi.

Per l'aggiornamento del dispositivo per l'hub Internet è necessario che il numero di Plug and Play sia Smart Device annunciare un ID modello con un valore **"dtmi: AzureDeviceUpdate; 1"** come parte della connessione del dispositivo. [Informazioni su come annunciare un ID modello](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement).