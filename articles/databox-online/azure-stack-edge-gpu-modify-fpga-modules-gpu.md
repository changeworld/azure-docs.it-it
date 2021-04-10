---
title: Modificare IoT Edge moduli nel dispositivo FPGA per l'esecuzione nel dispositivo GPU Pro Azure Stack Edge
description: Descrive le modifiche necessarie per l'esecuzione dei moduli di IoT Edge esistenti nei dispositivi FPGA esistenti sul dispositivo GPU Pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 1276a242efb1917a0c4a24aa73c3e0d11f81e158
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559163"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Eseguire i moduli Azure Stack di IoT Edge esistenti dai dispositivi Microsoft Edge Pro FPGA nel dispositivo GPU Pro Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Questo articolo illustra in dettaglio le modifiche necessarie per un modulo di IoT Edge basato su Docker in esecuzione su Azure Stack Edge Pro FPGA, in modo che possa essere eseguito su una piattaforma di IoT Edge basata su Kubernetes sul dispositivo GPU Azure Stack Edge Pro. 

## <a name="about-iot-edge-implementation"></a>Informazioni sull'implementazione di IoT Edge 

L'implementazione del IoT Edge è diversa Azure Stack nei dispositivi Microsoft Edge Pro FPGA e nei dispositivi GPU Pro Azure Stack Edge. Per i dispositivi GPU, Kubernetes viene usato come piattaforma di hosting per IoT Edge. Il IoT Edge nei dispositivi FPGA usa una piattaforma basata su Docker. Il modello di applicazione basato su Docker IoT Edge viene automaticamente convertito nel modello di applicazione nativo Kubernetes. Tuttavia, alcune modifiche potrebbero ancora essere necessarie poiché è supportato solo un piccolo subset del modello di applicazione Kubernetes.

Se si esegue la migrazione dei carichi di lavoro da un dispositivo FPGA a un dispositivo GPU, sarà necessario apportare modifiche ai moduli di IoT Edge esistenti per poter essere eseguiti correttamente nella piattaforma Kubernetes. Potrebbe essere necessario specificare i requisiti di archiviazione, rete, utilizzo delle risorse e proxy Web in modo diverso. 

## <a name="storage"></a>Archiviazione

Quando si specifica l'archiviazione per i moduli IoT Edge, tenere presenti le informazioni seguenti.

- Lo spazio di archiviazione per i contenitori in Kubernetes viene specificato usando i montaggi del volume.
- La distribuzione in Kubernetes non può avere binding per l'associazione di percorsi di archiviazione o host permanenti.
    - Per l'archiviazione persistente, usare `Mounts` con il tipo `volume` .
    - Per i percorsi host, usare `Mounts` con il tipo `bind` .
- Per IoT Edge su Kubernetes, l'associazione tramite `Mounts` funziona solo per la directory e non per il file.

#### <a name="example---storage-via-volume-mounts"></a>Esempio-archiviazione tramite montaggi del volume 

Per IoT Edge in Docker, le associazioni percorso host vengono usate per eseguire il mapping delle condivisioni sul dispositivo ai percorsi all'interno del contenitore. Di seguito sono riportate le opzioni di creazione del contenitore usate nei dispositivi FPGA:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
Per i percorsi host per IoT Edge in Kubernetes, viene illustrato un esempio di utilizzo di `Mounts` with Type `bind` :
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Per i dispositivi GPU che eseguono IoT Edge in Kubernetes, vengono usati i montaggi del volume per specificare lo spazio di archiviazione. Per eseguire il provisioning dell'archiviazione tramite le condivisioni, il valore di `Mounts.Source` corrisponde al nome della condivisione SMB o NFS di cui è stato effettuato il provisioning sul dispositivo GPU. `/home/input`È il percorso in cui il volume è accessibile all'interno del contenitore. Di seguito sono riportate le opzioni di creazione del contenitore usate nei dispositivi GPU:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Rete

Quando si specifica la rete per i moduli di IoT Edge, tenere presenti le informazioni seguenti. 

- `HostPort` la specifica è necessaria per esporre un servizio all'interno e all'esterno del cluster.
    - Opzioni di K8sExperimental per limitare l'esposizione del servizio al solo cluster.
- La comunicazione tra i moduli richiede `HostPort` la specifica e la connessione tramite la porta mappata (e non l'uso della porta esposta al contenitore).
- La rete host funziona con `dnsPolicy = ClusterFirstWithHostNet` , in quanto tutti i contenitori (soprattutto `edgeHub` ) non devono essere presenti anche nella rete host. <!--Need further clarifications on this one-->
- L'aggiunta di mapping delle porte per TCP, UDP nella stessa richiesta non funziona.

#### <a name="example---external-access-to-modules"></a>Esempio-accesso esterno ai moduli 

Per tutti i moduli IoT Edge che specificano i binding di porta, un indirizzo IP viene assegnato usando l'intervallo IP del servizio esterno Kubernetes specificato nell'interfaccia utente locale del dispositivo. Non sono state apportate modifiche alle opzioni di creazione del contenitore tra IoT Edge in Docker rispetto a IoT Edge in Kubernetes, come illustrato nell'esempio seguente.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

Tuttavia, per eseguire una query sull'indirizzo IP assegnato al modulo, è possibile usare il dashboard di Kubernetes come descritto in [ottenere un indirizzo IP per i servizi o i moduli](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules). 

In alternativa, è possibile [connettersi all'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) e usare il `iotedge` comando list per elencare tutti i moduli in esecuzione nel dispositivo. L' [output del comando](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) indicherà anche gli IP esterni associati al modulo.


## <a name="resource-usage"></a>Utilizzo delle risorse 

Con le configurazioni IoT Edge basate su Kubernetes nei dispositivi GPU, le risorse quali l'accelerazione hardware, la memoria e i requisiti della CPU vengono specificati in modo diverso rispetto ai dispositivi FPGA. 

#### <a name="compute-acceleration-usage"></a>Utilizzo accelerazione calcolo

Per distribuire moduli in FPGA, usare le opzioni di creazione del contenitore <!--with Device Bindings--> come illustrato nella configurazione seguente: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
Per la GPU, usare le specifiche delle richieste di risorse anziché le associazioni di dispositivi, come illustrato nella configurazione minima seguente. Si richiedono risorse NVIDIA anziché catapulta e non è necessario specificare `wireserver` . 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Utilizzo della CPU e della memoria
 
Per impostare l'utilizzo della memoria e della CPU, usare i limiti del processore per i moduli nella `k8s-experimental` sezione. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
Le specifiche di memoria e CPU non sono necessarie, ma in genere è consigliabile. Se `requests` non è specificato, i valori impostati nei limiti vengono usati come minimo necessario. 

L'uso della memoria condivisa per i moduli richiede anche un modo diverso. Ad esempio, è possibile usare la modalità IPC dell'host per l'accesso alla memoria condivisa tra le soluzioni di analisi video in tempo reale e le soluzioni di inferenza come descritto in [distribuire analisi video in tempo reale su Azure stack Edge](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal).


## <a name="web-proxy"></a>Proxy Web 

Quando si configura il proxy Web, tenere presenti le seguenti informazioni:

Se nella rete è configurato un proxy Web, configurare le variabili di ambiente seguenti per la `edgeHub` distribuzione nell'installazione di IOT Edge basata su Docker nei dispositivi FPGA:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (a meno che il proxy Web non consenta il `Amqp` traffico)

Per le configurazioni di IoT Edge basate su Kubernetes nei dispositivi GPU, è necessario configurare questa variabile aggiuntiva durante la distribuzione:

- `no_proxy`: localhost

- IoT Edge proxy nella piattaforma Kubernetes usa la porta 35000 e 35001. Verificare che il modulo non venga eseguito in queste porte o che causi conflitti di porte. 

## <a name="other-differences"></a>Altre differenze

- **Strategia di distribuzione**: potrebbe essere necessario modificare il comportamento di distribuzione per tutti gli aggiornamenti del modulo. Il comportamento predefinito per i moduli IoT Edge è l'aggiornamento in sequenza. Questo comportamento impedisce il riavvio del modulo aggiornato se il modulo usa risorse quali l'accelerazione hardware o le porte di rete. Questo comportamento può avere effetti imprevisti, specialmente quando si gestiscono volumi permanenti sulla piattaforma Kubernetes per i dispositivi GPU. Per eseguire l'override di questo comportamento predefinito, è possibile specificare un `Recreate` nella `k8s-experimental` sezione del modulo.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **Nomi dei moduli**: i nomi dei moduli devono seguire le convenzioni di denominazione Kubernetes. Quando si spostano i moduli in IoT Edge con Kubernetes, potrebbe essere necessario rinominare i moduli in esecuzione in IoT Edge con Docker. Per ulteriori informazioni sulla denominazione, vedere [convenzioni di denominazione Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Altre opzioni**: 
    - Alcune opzioni di creazione Docker che funzionavano sui dispositivi FPGA non funzioneranno nell'ambiente Kubernetes nei dispositivi GPU. Ad esempio:, like – EntryPoint.<!--can we confirm what exactly is required here-->
    - Le variabili di ambiente, ad esempio, `:` devono essere sostituite da `__` .
    - Lo stato di **creazione del contenitore** per un pod Kubernetes comporta lo stato **backoff** per un modulo nella risorsa hub Internet. Sebbene esistano diversi motivi per cui il Pod è in questo stato, un motivo comune è quando viene effettuato il pull di un'immagine del contenitore di grandi dimensioni su una connessione a larghezza di banda bassa. Quando il Pod si trova in questo stato, lo stato del modulo viene visualizzato come **backoff** nell'hub tutto, anche se il modulo è appena in fase di avvio.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [configurare GPU per l'uso di un modulo](./azure-stack-edge-gpu-configure-gpu-modules.md).