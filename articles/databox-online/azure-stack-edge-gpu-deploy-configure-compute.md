---
title: Esercitazione su come filtrare e analizzare i dati con il calcolo in Azure Stack Edge Pro con GPU | Microsoft Docs
description: Informazioni su come configurare il ruolo di calcolo in Azure Stack Edge Pro con GPU e usarlo per trasformare i dati prima dell'invio ad Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 750b7a8367d46434f48626268a0eb37c9edddfb1
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633538"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Esercitazione: Configurare il calcolo nel dispositivo Azure Stack Edge Pro con GPU

<!--ALPA WILL VERIFY - [!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questa esercitazione illustra come configurare un ruolo di calcolo e creare un cluster Kubernetes nel dispositivo Azure Stack Edge Pro. 

Il completamento di questa procedura può richiedere circa 20-30 minuti.


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il calcolo
> * Ottenere gli endpoint Kubernetes

 
## <a name="prerequisites"></a>Prerequisiti

Prima di configurare un ruolo di calcolo nel dispositivo Azure Stack Edge Pro, assicurarsi di:

- Aver attivato il dispositivo Azure Stack Edge Pro come descritto in [Attivare il dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Assicurarsi di aver seguito le istruzioni riportate in [Abilitare la rete di calcolo](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) e di:
    - Avere abilitato un'interfaccia di rete per il calcolo.
    - Avere assegnato gli indirizzi IP del nodo Kubernetes e gli indirizzi IP del servizio esterno Kubernetes.

## <a name="configure-compute"></a>Configurare il calcolo

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Ottenere gli endpoint Kubernetes

Per configurare un client per l'accesso al cluster Kubernetes, sarà necessario l'endpoint Kubernetes. Seguire questa procedura per ottenere l'endpoint dell'API Kubernetes dall'interfaccia utente locale del dispositivo Azure Stack Edge Pro.

1. Nell'interfaccia utente Web locale del dispositivo passare alla pagina **Dispositivi**.
2. In **Device endpoints** (Endpoint dispositivo) copiare l'endpoint **Kubernetes API service** (Servizio API Kubernetes). Questo endpoint è una stringa nel formato seguente: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`. 

    ![Pagina del dispositivo nell'interfaccia utente locale](./media/azure-stack-edge-gpu-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Salvare la stringa dell'endpoint. Verrà usata in un secondo momento durante la configurazione di un client per accedere al cluster Kubernetes tramite kubectl.

4. Mentre ci si trova ancora nell'interfaccia utente Web locale, è possibile:

    - Passare all'API Kubernetes, selezionare le **impostazioni avanzate** e scaricare un file di configurazione avanzata per Kubernetes. 

        ![Pagina del dispositivo nell'interfaccia utente locale 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Se è stato fornito un codice da Microsoft (alcuni utenti selezionati possono averlo), si può usare questo file di configurazione.

        ![Pagina del dispositivo nell'interfaccia utente locale 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - È anche possibile passare all'endpoint **dashboard Kubernetes** e scaricare un file di configurazione `aseuser`. 
    
        ![Pagina del dispositivo nell'interfaccia utente locale 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        È possibile usare questo file di configurazione per accedere al dashboard di Kubernetes o eseguire il debug di eventuali problemi nel cluster Kubernetes. Per altre informazioni, vedere [Accedere al dashboard di Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il calcolo
> * Ottenere gli endpoint Kubernetes


Per informazioni su come amministrare il dispositivo Azure Stack Edge Pro, vedere:

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare un dispositivo Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
