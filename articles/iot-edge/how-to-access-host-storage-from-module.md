---
title: Usare IoT Edge di archiviazione locale del dispositivo da un modulo - Azure IoT Edge | Microsoft Docs
description: Usare le variabili di ambiente e le opzioni di creazione per abilitare l'accesso del modulo IoT Edge'archiviazione locale del dispositivo.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 78752d4da42fe07461ae0e82b10343dc7219ad91
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482059"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Consentire ai moduli di accedere all'archiviazione locale di un dispositivo

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Oltre ad archiviare i dati usando i servizi di archiviazione di Azure o nell'archiviazione contenitore del dispositivo, è anche possibile dedicare spazio di archiviazione nel dispositivo host IoT Edge per migliorare l'affidabilità, soprattutto quando si opera offline.

## <a name="link-module-storage-to-device-storage"></a>Collegare l'archiviazione moduli all'archiviazione del dispositivo

Per abilitare un collegamento dall'archiviazione moduli all'archiviazione nel sistema host, creare una variabile di ambiente per il modulo che punti a una cartella di archiviazione nel contenitore. Usare quindi le opzioni di creazione per associare tale cartella di archiviazione a una cartella nel computer host.

Ad esempio, se si vuole abilitare l'hub di IoT Edge per archiviare i messaggi nella memoria locale del dispositivo e recuperarli in un secondo momento, è possibile configurare le variabili di ambiente e le opzioni di creazione nel portale di Azure nella sezione Impostazioni **di** runtime.

1. Sia per IoT Edge hub che per IoT Edge, aggiungere una variabile di ambiente denominata **storageFolder** che punta a una directory nel modulo.
1. Sia per IoT Edge hub che per IoT Edge, aggiungere binding per connettere una directory locale nel computer host a una directory nel modulo. Ad esempio:

   ![Aggiungere le opzioni di creazione e le variabili di ambiente per l'archiviazione locale](./media/how-to-access-host-storage-from-module/offline-storage.png)

In caso contrario, è possibile configurare l'archiviazione locale direttamente nel manifesto della distribuzione. Ad esempio:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Sostituire `<HostStoragePath>` e con il percorso di archiviazione `<ModuleStoragePath>` dell'host e del modulo. Entrambi i valori devono essere un percorso assoluto.

Ad esempio, in un sistema Linux significa che la `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` directory **/etc/iotedge/storage** nel sistema host è mappata alla directory **/iotedge/storage/** nel contenitore. In un sistema Windows, come altro esempio, significa che la directory C: temp nel sistema host è mappata alla `"Binds":["C:\\temp:C:\\contemp"]` directory **C: \\ contemp** nel contenitore. **\\**

Inoltre, nei dispositivi Linux verificare che il profilo utente per il modulo abbia le autorizzazioni di lettura, scrittura ed esecuzione necessarie per la directory del sistema host. Tornando all'esempio precedente di abilitazione IoT Edge hub per archiviare i messaggi nell'archiviazione locale del dispositivo, è necessario concedere le autorizzazioni al relativo profilo utente, UID 1000. L'IoT Edge funziona come radice, quindi non sono necessarie autorizzazioni aggiuntive. Esistono diversi modi per gestire le autorizzazioni di directory nei sistemi Linux, tra cui l'uso di per modificare il proprietario della directory e quindi per modificare le `chown` `chmod` autorizzazioni, ad esempio:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Per altre informazioni sulle opzioni di creazione, vedere [docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="encrypted-data-in-module-storage"></a>Dati crittografati nell'archiviazione moduli

Quando i moduli richiamano IoT Edge'API del carico di lavoro del daemon per crittografare i dati, la chiave di crittografia viene derivata usando l'ID modulo e l'ID di generazione del modulo. Un ID generazione viene usato per proteggere i segreti se un modulo viene rimosso dalla distribuzione e un altro modulo con lo stesso ID modulo viene successivamente distribuito nello stesso dispositivo. È possibile visualizzare l'ID generazione di un modulo usando il comando dell'interfaccia della riga di comando di Azure [az iot hub module-identity show](/cli/azure/iot/hub/module-identity).

Se si desidera condividere i file tra moduli tra generazioni, non devono contenere segreti o non verranno decrittografati.

## <a name="next-steps"></a>Passaggi successivi

Per un altro esempio di accesso all'archiviazione host da un modulo, vedere Archiviare i dati sul perimetro con Archiviazione BLOB di Azure [in IoT Edge](how-to-store-data-blob.md).
