---
title: Esercitazione su come attivare il dispositivo Azure Stack Edge Pro R nel portale di Azure
description: Esercitazione per la distribuzione di Azure Stack Edge Pro R che illustra come attivare il dispositivo fisico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro R device so I can use it to transfer data to Azure.
ms.openlocfilehash: bae12b5ad7ce6c079161f42e1d6fe3e6c08e3f4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727533"
---
# <a name="tutorial-activate-azure-stack-edge-pro-r-device"></a>Esercitazione: Attivare il dispositivo Azure Stack Edge Pro R

Questa esercitazione descrive come attivare il dispositivo Azure Stack Edge Pro R usando l'interfaccia utente Web locale.

Per completare il processo di attivazione sono necessari circa 5 minuti.  

In questa esercitazione sono stati trattati gli argomenti seguenti:

> [!div class="checklist"]
> * Prerequisiti
> * Attivare il dispositivo fisico

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Azure Stack Edge Pro R, assicurarsi di:

* Per il dispositivo fisico: 
    
    - Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md).
    - Aver configurato le impostazioni di rete e di rete di calcolo come descritto in [Configurare la rete, la rete di calcolo e il proxy Web](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
    - Avere caricato i certificati del dispositivo personali o generati nel dispositivo se è stato modificato il nome del dispositivo o il dominio DNS tramite la pagina **Dispositivo**. Se questo passaggio non è ancora stato eseguito, verrà visualizzato un errore durante l'attivazione del dispositivo e l'attivazione verrà bloccata. Per altre informazioni, vedere [Configurazione dei certificati](azure-stack-edge-placeholder.md).
    
* Disporre della chiave di attivazione del servizio Azure Stack Edge creata per gestire il dispositivo Azure Stack Edge Pro R. Per altre informazioni, vedere [Preparare la distribuzione di Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md).


## <a name="activate-the-device"></a>Attivare il dispositivo

1. Nell'interfaccia utente Web locale del dispositivo passare alla pagina **Attività iniziali**.
2. Nel riquadro **Attivazione** selezionare **Attiva**. 

    ![Pagina "Dettagli cloud" dell'interfaccia utente Web locale - 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. Nel riquadro **Attiva**:
    1. Immettere la **chiave di attivazione** ottenuta in [Ottenere la chiave di attivazione per Azure Stack Edge Pro](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. È possibile abilitare la raccolta proattiva dei log per consentire a Microsoft di raccogliere i log in base allo stato di integrità del dispositivo. I log raccolti in questo modo vengono caricati in un account di Archiviazione di Azure.
    
    1. Selezionare **Applica**.

    ![Interfaccia utente Web locale: "Dettagli cloud" - Pagina 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)


5. Il dispositivo viene prima di tutto attivato. Viene quindi richiesto di scaricare il file di chiave.
    
    ![Interfaccia utente Web locale: "Dettagli cloud" - Pagina 3](./media/azure-stack-edge-pro-r-deploy-activate/activate-3.png)
    
    Selezionare **Download and continue** (Scarica e continua) e salvare il file *device-serial-no.json* in un percorso sicuro all'esterno del dispositivo. **Questo file di chiave contiene le chiavi di ripristino per il disco del sistema operativo e i dischi dati nel dispositivo**. Queste chiavi potrebbero essere necessarie per facilitare un ripristino di sistema futuro.

    Ecco il contenuto del file *json*:

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    La tabella seguente illustra le varie chiavi:
    
    |Campo  |Descrizione  |
    |---------|---------|
    |`Id`    | Questo è l'ID del dispositivo.        |
    |`DataVolumeBitLockerExternalKeys`|Queste sono le chiavi di BitLocker per i dischi dati e vengono usate per ripristinare i dati locali nel dispositivo.|
    |`SystemVolumeBitLockerRecoveryKey`| Questa è la chiave di BitLocker per il volume di sistema. Questa chiave consente di ripristinare la configurazione del sistema e i dati di sistema per il dispositivo. |
    |`ServiceEncryptionKey`| Questa chiave protegge i dati nel percorso verso il servizio di Azure. Questa chiave garantisce che, nel caso in cui venga compromesso il servizio di Azure, non vengano compromesse anche le informazioni archiviate. |

6. Passare alla pagina **Panoramica**. Lo stato del dispositivo dovrebbe essere **Attivato**.

    ![Interfaccia utente Web locale: "Dettagli cloud" - Pagina 4](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
L'attivazione del dispositivo è stata completata. È ora possibile aggiungere condivisioni al dispositivo.

In caso di problemi durante l'attivazione, vedere [Risolvere i problemi di attivazione e di Azure Key Vault](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati trattati gli argomenti seguenti:

> [!div class="checklist"]
> * Prerequisiti
> * Attivare il dispositivo fisico

Per informazioni su come trasferire dati con il dispositivo Azure Stack Edge Pro R, vedere:

> [!div class="nextstepaction"]
> [Trasferire i dati con Azure Stack Edge Pro R](./azure-stack-edge-j-series-deploy-add-shares.md)
