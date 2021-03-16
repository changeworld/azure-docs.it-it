---
title: Condivisione GPU sul dispositivo GPU Azure Stack Edge Pro
description: Vengono descritti gli approcci alla condivisione delle GPU sul dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 6683e39cfa3601b1ae1fbbe02e69e4dc0a54e8e7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565020"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Condivisione GPU sul dispositivo GPU Azure Stack Edge Pro

GPU (Graphics Processing Unit) è un processore specializzato progettato per accelerare il rendering della grafica. Le GPU possono elaborare più dati contemporaneamente, rendendoli utili per l'apprendimento automatico, la modifica di video e le applicazioni di gioco. Oltre alla CPU per il calcolo per utilizzo generico, i dispositivi GPU di Azure Stack Edge Pro possono contenere una o due GPU NVIDIA Tesla T4 per carichi di lavoro a elevato utilizzo di calcolo, ad esempio l'inferenza accelerata dell'hardware. Per ulteriori informazioni, vedere la pagina relativa alla [GPU Tesla T4 di NVIDIA](https://www.nvidia.com/data-center/tesla-t4/).


## <a name="about-gpu-sharing"></a>Informazioni sulla condivisione GPU

Molti carichi di lavoro di calcolo o di altro sistema potrebbero non richiedere una GPU dedicata. Le GPU possono essere condivise e la condivisione di GPU tra i carichi di lavoro in contenitori o le macchine virtuali consente di aumentare l'utilizzo della GPU senza influire significativamente sui vantaggi di prestazioni della GPU.  

## <a name="using-gpu-with-vms"></a>Uso della GPU con macchine virtuali

Nel dispositivo Azure Stack Edge Pro non è possibile condividere una GPU quando si distribuiscono carichi di lavoro della macchina virtuale. È possibile eseguire il mapping di una GPU solo a una macchina virtuale. Questo implica che è possibile avere una sola VM GPU in un dispositivo con una GPU e due macchine virtuali in un dispositivo dotato di due GPU. Quando si usano macchine virtuali GPU in un dispositivo con Kubernetes configurato per i carichi di lavoro in contenitori, è necessario considerare anche altri fattori. Per altre informazioni, vedere [VM GPU e Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes).


## <a name="using-gpu-with-containers"></a>Uso della GPU con i contenitori

Se si distribuiscono carichi di lavoro in contenitori, una GPU può essere condivisa in più modi a livello hardware e software. Con la GPU di Tesla T4 sul dispositivo Azure Stack Edge Pro, si è limitati alla condivisione del software. Nel dispositivo vengono usati i due approcci seguenti per la condivisione software della GPU: 

- Il primo approccio consiste nell'usare le variabili di ambiente per specificare il numero di GPU che possono essere condivise in tempo. Quando si usa questo approccio, tenere presenti le avvertenze seguenti:

    - Con questo metodo è possibile specificare una o entrambe o nessuna GPU. Non è possibile specificare l'utilizzo frazionario.
    - È possibile eseguire il mapping di più moduli a una GPU, ma lo stesso modulo non può essere mappato a più di una GPU.
    - Con l'output di NVIDIA SMI, è possibile visualizzare l'utilizzo complessivo della GPU, incluso l'utilizzo della memoria.
    
    Per altre informazioni, vedere come [distribuire un modulo di IOT Edge che usa GPU](azure-stack-edge-gpu-configure-gpu-modules.md) nel dispositivo.

- Il secondo approccio richiede l'abilitazione del servizio multiprocesso nelle GPU NVIDIA. MP è un servizio di runtime che consente l'esecuzione simultanea di più processi con CUDA su una singola GPU condivisa. MP consente la sovrapposizione di operazioni kernel e memcopy da processi diversi sulla GPU per ottenere un utilizzo massimo. Per altre informazioni, vedere [servizio](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)a più processi.

    Quando si usa questo approccio, tenere presenti le avvertenze seguenti:
    
    - MP consente di specificare più flag nella distribuzione GPU.
    - È possibile specificare l'utilizzo frazionario tramite MP, limitando così l'utilizzo di ogni applicazione distribuita nel dispositivo. È possibile specificare la percentuale di GPU da usare per ogni app nella `env` sezione del aggiungendo `deployment.yaml` il parametro seguente: 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>Utilizzo GPU
 
Quando si condivide la GPU nei carichi di lavoro in contenitori distribuiti nel dispositivo, è possibile usare NVIDIA System Management Interface (NVIDIA-SMI). NVIDIA-SMI è un'utilità da riga di comando che consente di gestire e monitorare i dispositivi GPU NVIDIA. Per ulteriori informazioni, vedere [NVIDIA System Management Interface](https://developer.nvidia.com/nvidia-system-management-interface).

Per visualizzare l'utilizzo della GPU, connettersi prima all'interfaccia di PowerShell del dispositivo. Eseguire il `Get-HcsNvidiaSmi` comando e visualizzare l'output di NVIDIA SMI. È anche possibile visualizzare il modo in cui l'utilizzo della GPU viene modificato abilitando MP e quindi distribuendo più carichi di lavoro nel dispositivo. Per altre informazioni, vedere [abilitare il servizio a più processi](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps).


## <a name="next-steps"></a>Passaggi successivi

- [Condivisione GPU per le distribuzioni di Kubernetes in Azure stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [Condivisione GPU per le distribuzioni di Internet delle cose in Azure stack Edge Pro](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
