---
title: Aggiornamento del dispositivo per il manifesto di aggiornamento dell'hub Internet | Microsoft Docs
description: Informazioni sul modo in cui le proprietà vengono inviate dal servizio di aggiornamento del dispositivo al dispositivo durante un aggiornamento
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: bbd39f7752a1a482350a7231a0bb5a3710591b1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030682"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>Aggiornamento del dispositivo per il manifesto di aggiornamento dell'hub Internet

## <a name="overview"></a>Panoramica

L'aggiornamento del dispositivo per l'hub Internet usa un _manifesto di aggiornamento_ per comunicare le azioni e anche i metadati che supportano tali azioni tramite le proprietà dell'interfaccia [AzureDeviceUpdateCore. OrchestratorMetadata: 4](./device-update-plug-and-play.md).
Questo documento descrive le nozioni di base sul modo in cui la `updateManifest` proprietà, nell' `AzureDeviceUpdateCore.OrchestratorMetadata:4` interfaccia, viene usata dall'agente di aggiornamento del dispositivo. Le `AzureDeviceUpdateCore.OrchestratorMetadata:4` proprietà dell'interfaccia vengono inviate dall'aggiornamento del dispositivo per il servizio hub Internet all'agente di aggiornamento del dispositivo. `updateManifest`È un oggetto JSON serializzato analizzato dall'agente di aggiornamento del dispositivo.

### <a name="an-example-update-manifest"></a>Un manifesto di aggiornamento di esempio

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

Lo scopo del manifesto di aggiornamento è quello di descrivere il contenuto di un aggiornamento, ovvero l'identità, il tipo, i criteri installati e i metadati dei file di aggiornamento. Il manifesto di aggiornamento, inoltre, è firmato in modo crittografico per consentire all'agente di aggiornamento del dispositivo di verificarne l'autenticità. Per ulteriori informazioni sulla modalità di utilizzo del manifesto di aggiornamento per l'importazione sicura del contenuto, vedere la documentazione sulla [sicurezza degli aggiornamenti dei dispositivi](./device-update-security.md) .

## <a name="import-manifest-vs-update-manifest"></a>Importa manifesto di aggiornamento manifesto di Visual Studio

È importante comprendere le differenze tra il manifesto di importazione e i concetti del manifesto di aggiornamento nell'aggiornamento del dispositivo per l'hub Internet. 
* Il [manifesto di importazione](./import-concepts.md) viene creato da chiunque crei l'aggiornamento corrispondente. Descrive il contenuto dell'aggiornamento che verrà importato nell'aggiornamento del dispositivo per l'hub Internet. 
* Il manifesto di aggiornamento viene generato automaticamente dall'aggiornamento del dispositivo per il servizio hub Internet, usando alcune delle proprietà definite nel manifesto di importazione. Viene usato per comunicare le informazioni rilevanti all'agente di aggiornamento del dispositivo durante il processo di aggiornamento. 

Ogni tipo di manifesto ha una propria versione dello schema e dello schema.

## <a name="update-manifest-properties"></a>Aggiornare le proprietà del manifesto

Le definizioni di alto livello delle proprietà del manifesto di aggiornamento sono disponibili nelle definizioni di interfaccia disponibili [qui](./device-update-plug-and-play.md). Per fornire un contesto più approfondito, verranno esaminate in dettaglio le proprietà e il modo in cui vengono utilizzate nel sistema.

### <a name="updateid"></a>Codice UpdateID

Contiene `provider` , `name` e `version` , che rappresenta l'aggiornamento esatto del dispositivo per l'identità di aggiornamento dell'hub Internet usato per determinare i dispositivi compatibili per l'aggiornamento.

### <a name="updatetype"></a>updateType

Rappresenta il tipo di aggiornamento gestito da un tipo specifico di gestore degli aggiornamenti. Segue il formato `microsoft/swupdate:1` per un aggiornamento basato su immagine e `microsoft/apt:1` per un aggiornamento basato su pacchetti (vedere la `Update Handler Types` sezione seguente).

### <a name="installedcriteria"></a>installedCriteria

Stringa che contiene le informazioni richieste dal gestore di aggiornamento dell'agente di aggiornamento del dispositivo per determinare se l'aggiornamento è installato nel dispositivo. La `Update Handler Types` sezione documenta il formato di `installedCriteria` , per ogni tipo di aggiornamento supportato dall'aggiornamento del dispositivo per l'hub Internet.

### <a name="files"></a>files

Indica all'agente di aggiornamento del dispositivo quali file scaricare e l'hash che verrà usato per verificare che i file siano stati scaricati correttamente.
Ecco un esame più approfondito del `files` contenuto delle proprietà:

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

All'esterno di `updateManifest` è la `fileUrls` matrice di oggetti JSON.

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

Sia `FILE_ID_STRING` , all'interno di `fileUrls` che `files` sono uguali (ad esempio, "0000" in `files` ha l'URL in "0000" all'interno di `fileUrls` ).

### <a name="manifestversion"></a>manifestVersion

Stringa che rappresenta la versione dello schema.

## <a name="update-handler-types"></a>Aggiornare i tipi di gestore

|Metodo Update|Aggiornare il tipo di gestore|Tipo di aggiornamento|Criteri di installazione|File previsti per la pubblicazione|
|-------------|-------------------|----------|-----------------|--------------|
|Basato su immagine|SWUpdate|"Microsoft/swupdate: Version"|L'immagine di riferimento Salva il suggerimento della relativa versione nel file/etc/Adu-Version.  |file con estensione SWU che contiene l'immagine SWUpdate|
|Basato su pacchetti|APT|"Microsoft/APT: Version"|`<name>` + "-" + `<version>` (proprietà definite nel file manifesto apt|`<APT Update Manifest>`. JSON che contiene la configurazione APT e l'elenco di pacchetti|

