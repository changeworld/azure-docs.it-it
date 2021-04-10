---
title: Distribuire il carico di lavoro IoT Edge usando la condivisione GPU nel dispositivo GPU Pro Azure Stack Edge
description: Viene descritto come distribuire un carico di lavoro condiviso GPU tramite IoT Edge nel dispositivo GPU Pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565148"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Distribuire un carico di lavoro di IoT Edge usando la condivisione GPU in Azure Stack Edge Pro

Questo articolo descrive il modo in cui i carichi di lavoro in contenitori possono condividere le GPU sul dispositivo GPU Azure Stack Edge Pro. L'approccio prevede l'abilitazione del servizio multiprocesso (MP) e la specifica dei carichi di lavoro GPU tramite una distribuzione IoT Edge. 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. È possibile accedere a un dispositivo GPU Pro Azure Stack Edge [attivato](azure-stack-edge-gpu-deploy-activate.md) e configurato per il [calcolo](azure-stack-edge-gpu-deploy-configure-compute.md). L'endpoint dell' [API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) è stato aggiunto e questo endpoint è stato aggiunto al `hosts` file nel client che accederà al dispositivo.

1. È possibile accedere a un sistema client con un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Se si usa un client Windows, il sistema deve eseguire PowerShell 5,0 o versione successiva per accedere al dispositivo.

1. Salvare la distribuzione seguente `json` nel sistema locale. Per eseguire la distribuzione di IoT Edge si useranno le informazioni contenute in questo file. Questa distribuzione è basata su [semplici contenitori CUDA](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) disponibili pubblicamente da NVIDIA. 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>Verificare il driver GPU, versione CUDA

Il primo passaggio consiste nel verificare che il dispositivo esegua il driver GPU e le versioni CUDA necessarie.

1. [Connettersi all'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Eseguire il comando seguente:

    `Get-HcsGpuNvidiaSmi`

1. Nell'output di NVIDIA SMI prendere nota della versione GPU e della versione CUDA del dispositivo. Se si esegue Azure Stack software perimetrale 2102, questa versione corrisponderebbe alle versioni seguenti del driver:

    - Versione driver GPU: 460.32.03
    - Versione CUDA: 11,2
    
    Di seguito è riportato un esempio di output:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>  
    ```

1. Lasciare aperta la sessione perché verrà utilizzata per visualizzare l'output di NVIDIA SMI nell'articolo.


## <a name="deploy-without-context-sharing"></a>Distribuisci senza condivisione del contesto

È ora possibile distribuire un'applicazione nel dispositivo quando il servizio multiprocesso non è in esecuzione e non è presente alcuna condivisione del contesto. La distribuzione avviene tramite il portale di Azure nello `iotedge` spazio dei nomi esistente nel dispositivo.

### <a name="create-user-in-iot-edge-namespace"></a>Crea utente nello spazio dei nomi IoT Edge

Prima di tutto verrà creato un utente che si connetterà allo `iotedge` spazio dei nomi. I moduli IoT Edge vengono distribuiti nello spazio dei nomi iotedge. Per altre informazioni, vedere [spazi dei nomi Kubernetes nel dispositivo](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types).

Seguire questa procedura per creare un utente e concedere all'utente l'accesso allo `iotedge` spazio dei nomi. 

1. [Connettersi all'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Creare un nuovo utente nello `iotedge` spazio dei nomi. Eseguire il comando seguente:

    `New-HcsKubernetesUser -UserName <user name>`

    Di seguito è riportato un esempio di output:

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. Copiare l'output visualizzato in testo normale. Salvare l'output come file di *configurazione* (senza estensione) nella `.kube` cartella del profilo utente nel computer locale, ad esempio `C:\Users\<username>\.kube` . 

1. Concedere all'utente creato l'accesso allo `iotedge` spazio dei nomi. Eseguire il comando seguente:

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    Di seguito è riportato un esempio di output:

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
Per istruzioni dettagliate, vedere [connettersi a un cluster Kubernetes e gestirlo tramite kubectl sul dispositivo GPU Azure stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac).

### <a name="deploy-modules-via-portal"></a>Distribuire moduli tramite il portale

Distribuire i moduli IoT Edge tramite il portale di Azure. Verranno distribuiti i moduli di esempio NVIDIA CUDA disponibili pubblicamente che eseguono la simulazione n-body. Per ulteriori informazioni, vedere [simulazione del corpo N](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).

1. Verificare che il servizio IoT Edge sia in esecuzione nel dispositivo.

    ![Servizio IoT Edge in esecuzione.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. Selezionare il riquadro IoT Edge nel riquadro di destra. Passare a **IOT Edge proprietà >**. Nel riquadro di destra selezionare la risorsa dell'hub Internet delle cose associata al dispositivo.

    ![Visualizzare proprietà.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. Nella risorsa hub Internet, passare a **gestione automatica dispositivi > IOT Edge**. Nel riquadro di destra selezionare il dispositivo IoT Edge associato al dispositivo.

    ![Passare a IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. Selezionare **Imposta moduli**.

    ![Passare a imposta moduli.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. Selezionare **+ aggiungi > + modulo di IOT Edge**.

    ![Aggiungi modulo IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. Nella scheda **Impostazioni modulo** specificare il nome e l' **URI dell'immagine** del **Modulo IOT Edge** . Impostare i **criteri di pull dell'immagine** **su on create**.

    ![Impostazioni del modulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. Nella scheda **variabili di ambiente** specificare **NVIDIA_VISIBLE_DEVICES** come **0**.

    ![Variabili di ambiente.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. Nella scheda **contenitore crea opzioni** specificare le opzioni seguenti:

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    Le opzioni vengono visualizzate nel modo seguente:

    ![Opzioni di creazione del contenitore.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    Selezionare **Aggiungi**.

1. Il modulo aggiunto dovrebbe essere visualizzato come **in esecuzione**. 

    ![Esaminare e creare la distribuzione.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. Ripetere tutti i passaggi per aggiungere un modulo che è stato seguito durante l'aggiunta del primo modulo. In questo esempio, specificare il nome del modulo come `cuda-sample2` . 

    ![Impostazioni del modulo per il secondo modulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    Utilizzare la stessa variabile di ambiente in modo che entrambi i moduli condividono la stessa GPU.

    ![Variabile di ambiente per il secondo modulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    Usare le stesse opzioni di creazione di contenitori fornite per il primo modulo e selezionare **Aggiungi**.

    ![Opzioni di creazione di contenitori per i 2 moduli.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. Nella pagina **imposta moduli** selezionare **Verifica + crea** e quindi selezionare **Crea**. 

    ![Esaminare e creare la seconda distribuzione.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. Lo **stato di runtime** di entrambi i moduli verrà ora visualizzato come **in esecuzione**.  

    ![secondo stato della distribuzione.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>Monitorare la distribuzione del carico di lavoro

1. Aprire una nuova sessione di PowerShell.

1. Elenca i pod in esecuzione nello `iotedge` spazio dei nomi. Eseguire il comando seguente:

    `kubectl get pods -n iotedge`   

    Di seguito è riportato un esempio di output:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    Sono presenti due pod `cuda-sample1-97c494d7f-lnmns` e in `cuda-sample2-d9f6c4688-2rld9` esecuzione nel dispositivo.

1. Mentre entrambi i contenitori eseguono la simulazione n-body, visualizzare l'utilizzo della GPU dall'output di NVIDIA SMI. Passare all'interfaccia di PowerShell del dispositivo ed eseguire `Get-HcsGpuNvidiaSmi` .

    Di seguito è riportato un esempio di output quando entrambi i contenitori eseguono la simulazione n-body:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    Come si può notare, sono presenti due contenitori in esecuzione con simulazione a n corpo sulla GPU 0. È anche possibile visualizzare l'utilizzo della memoria corrispondente.

1. Al termine della simulazione, l'output di NVIDIA SMI indicherà che non sono in esecuzione processi nel dispositivo.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
 
1. Al termine della simulazione del corpo n, visualizzare i log per comprendere i dettagli della distribuzione e il tempo necessario per il completamento della simulazione. 

    Di seguito è riportato un esempio di output del primo contenitore:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    Di seguito è riportato un esempio di output dal secondo contenitore:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. Arrestare la distribuzione del modulo. Nella risorsa hub Internet per il dispositivo:
    1. Passare a **distribuzione automatica dispositivo > IOT Edge**. Selezionare il dispositivo IoT Edge corrispondente al dispositivo.

    1. Passare a **impostare i moduli** e selezionare un modulo. 

        ![Selezionare set Module.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. Nella scheda **moduli** selezionare un modulo.
    
        ![Selezionare un modulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  Nella scheda **Impostazioni modulo** impostare **stato desiderato** su arrestato. Selezionare **Aggiorna**.

        ![Modificare le impostazioni del modulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. Ripetere i passaggi per arrestare il secondo modulo distribuito nel dispositivo. Selezionare **Rivedi e crea** e quindi **Crea**. Questa operazione dovrebbe aggiornare la distribuzione.

        ![Esaminare e creare una distribuzione aggiornata.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. Aggiornare la pagina **moduli set** più volte. fino a quando lo **stato del runtime** del modulo non viene visualizzato come **arrestato**.

        ![Verificare lo stato della distribuzione.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>Distribuire con condivisione del contesto

È ora possibile distribuire la simulazione n-body in due contenitori CUDA quando MP è in esecuzione nel dispositivo. In primo luogo, verranno abilitati i MPS sul dispositivo.


1. [Connettersi all'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Per abilitare i Management Pack nel dispositivo, eseguire il `Start-HcsGpuMPS` comando.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Ottenere l'output di NVIDIA SMI dall'interfaccia di PowerShell del dispositivo. È possibile visualizzare il `nvidia-cuda-mps-server` processo oppure il servizio MPS è in esecuzione nel dispositivo.

    Di seguito è riportato un esempio di output:

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. Distribuire i moduli arrestati in precedenza. Impostare lo **stato desiderato** su in esecuzione tramite **set Modules**.

    Ecco l'output di esempio:

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    È possibile osservare che i moduli vengono distribuiti e in esecuzione nel dispositivo.

1. Quando si distribuiscono i moduli, viene avviata anche la simulazione n-body in entrambi i contenitori. Ecco l'output di esempio quando la simulazione è stata completata nel primo contenitore:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    Ecco l'output di esempio quando la simulazione è stata completata nel secondo contenitore:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. Ottenere l'output di NVIDIA SMI dall'interfaccia di PowerShell del dispositivo quando entrambi i contenitori eseguono la simulazione del corpo n. Di seguito è riportato un esempio di output. Esistono tre processi: il `nvidia-cuda-mps-server` processo (tipo C) corrisponde al servizio MPS e i `/tmp/nbody` processi (tipo M + C) corrispondono ai carichi di lavoro n-body distribuiti dai moduli. 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>Passaggi successivi

- [Distribuire un carico di lavoro KUBERNETES GPU condiviso in Azure stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
