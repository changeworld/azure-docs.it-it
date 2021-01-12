---
title: Esercitazione su come filtrare e analizzare i dati con il calcolo in Azure Stack Edge Pro con GPU | Microsoft Docs
description: Informazioni su come configurare il ruolo di calcolo in Azure Stack Edge Pro con GPU e usarlo per trasformare i dati prima dell'invio ad Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c884ad6850b8f94baa7c658d685651c3241be33f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935650"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Esercitazione: Configurare il calcolo nel dispositivo Azure Stack Edge Pro con GPU

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

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

Per configurare il calcolo in Azure Stack Edge Pro, si creerà una risorsa hub IoT tramite il portale di Azure.

1. Nel portale di Azure della risorsa Azure Stack Edge passare a **Panoramica** e selezionare **IoT Edge**.

   ![Introduzione al calcolo](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-1.png)

2. In **Abilita servizio IoT Edge** selezionare **Aggiungi**.

   ![Configurare il calcolo](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-2.png)

3. Nel pannello **Configura calcolo Edge** immettere le informazioni seguenti:
   
   |Campo  |valore  |
   |---------|---------|
   |Hub IoT     | Scegliere **Nuovo** o **Esistente**. <br> Per impostazione predefinita, per creare una risorsa IoT viene usato un livello Standard (S1). Per usare una risorsa IoT di un livello gratuito, crearne uno e quindi selezionare la risorsa esistente. <br> In ogni caso, la risorsa hub IoT usa la stessa sottoscrizione e lo stesso gruppo di risorse usati dalla risorsa Azure Stack Edge.     |
   |Nome     |Immettere un nome per la risorsa hub IoT.         |

   ![Introduzione al calcolo 2](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-3.png)

4. Dopo aver completato le impostazioni, selezionare **Rivedi e crea**. Verificare le impostazioni della risorsa hub IoT e selezionare **Crea**.

   La creazione di una risorsa hub IoT richiede alcuni minuti. Una volta creata la risorsa, la pagina **Panoramica** indica che il servizio IoT Edge è ora in esecuzione.

   ![Introduzione al calcolo 3](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-4.png)

5. Per verificare che il ruolo di calcolo Edge sia stato configurato, selezionare **Proprietà**.

   ![Introduzione al calcolo 4](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-5.png)

   Quando il ruolo di calcolo Edge è configurato nel dispositivo Edge, crea due dispositivi: un dispositivo IoT e un dispositivo IoT Edge. Entrambi i dispositivi possono essere visualizzati nella risorsa dell'hub IoT. Nel dispositivo IoT Edge viene eseguito anche un runtime IoT Edge. Attualmente per il dispositivo IoT Edge è disponibile solo la piattaforma Linux.

Per configurare il calcolo possono essere necessari 20-30 minuti, dato che in background vengono creati un cluster Kubernetes e le macchine virtuali.

Dopo aver configurato correttamente il calcolo nel portale di Azure, sono disponibili un cluster Kubernetes e un utente predefinito associato allo spazio dei nomi IoT (spazio dei nomi di sistema controllato da Azure Stack Edge Pro).

## <a name="get-kubernetes-endpoints"></a>Ottenere gli endpoint Kubernetes

Per configurare un client per l'accesso al cluster Kubernetes, sarà necessario l'endpoint Kubernetes. Seguire questa procedura per ottenere l'endpoint dell'API Kubernetes dall'interfaccia utente locale del dispositivo Azure Stack Edge Pro.

1. Nell'interfaccia utente Web locale del dispositivo passare alla pagina **Dispositivi**.
2. In **Device endpoints** (Endpoint dispositivo) copiare l'endpoint **Kubernetes API service** (Servizio API Kubernetes). Questo endpoint è una stringa nel formato seguente: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`. 

    ![Pagina del dispositivo nell'interfaccia utente locale](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

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
