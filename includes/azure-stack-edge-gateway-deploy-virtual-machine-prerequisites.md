---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f2443765ecc9116193cefbc729ced25fa5657e59
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763430"
---
Prima di poter distribuire le macchine virtuali nel dispositivo Azure Stack Edge, è necessario configurare il client per la connessione al dispositivo tramite Azure Resource Manager Azure PowerShell. Per i passaggi dettagliati, vedere [connettersi a Azure Resource Manager nel dispositivo Azure stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Assicurarsi che i passaggi seguenti possano essere usati per accedere al dispositivo dal client (questa configurazione è stata eseguita durante la connessione a Azure Resource Manager, ma si sta verificando che la configurazione sia stata completata correttamente): 

1. Verificare il funzionamento della comunicazione Azure Resource Manager. Digitare:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Chiamare le API del dispositivo locale per l'autenticazione. Digitare: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Specificare il nome utente- *EdgeARMuser* e la password per la connessione tramite Azure Resource Manager.

1. Se il **calcolo** è stato configurato per Kubernetes, è possibile ignorare questo passaggio. Procedere per assicurarsi di avere abilitato un'interfaccia di rete per il calcolo. Nell'interfaccia utente locale passare a impostazioni di **calcolo** . Selezionare l'interfaccia di rete che si userà per creare uno switch virtuale. Le macchine virtuali create verranno collegate a un commutere virtuale collegato a questa porta e alla rete associata. Assicurarsi di scegliere una rete che corrisponda all'indirizzo IP che verrà usato per la macchina virtuale.  

    ![Abilita impostazioni di calcolo 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Abilitare il calcolo nell'interfaccia di rete. Azure Stack Edge creerà e gestirà un commutire virtuale corrispondente a tale interfaccia di rete. Non immettere in questo momento indirizzi IP specifici per Kubernetes. L'abilitazione del calcolo può richiedere diversi minuti.

    > [!NOTE]
    > Se si creano VM GPU, selezionare un'interfaccia di rete connessa a Internet. In questo modo è possibile installare l'estensione della GPU nel dispositivo.


1. Abilitare il ruolo VM dal portale di Azure. Questo passaggio crea una sottoscrizione univoca per il dispositivo usato per creare macchine virtuali tramite le API locali del dispositivo. 

    1. Per abilitare il ruolo VM, nella portale di Azure passare alla risorsa Azure Stack Edge per il dispositivo Azure Stack Edge. Passare alle **macchine virtuali > di calcolo Edge**.

        ![Aggiungi immagine VM 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

    1. Selezionare **macchine virtuali** per passare alla pagina **Panoramica** . **Abilitare** la gestione del cloud delle macchine virtuali.
        ![Aggiungi immagine VM 2](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)