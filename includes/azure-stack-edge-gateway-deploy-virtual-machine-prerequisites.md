---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 71d5a910e36762d096763c4f45a13cbdad47414d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730626"
---
Prima di poter distribuire le macchine virtuali nel dispositivo Azure Stack Edge, è necessario configurare il client per la connessione al dispositivo tramite Azure Resource Manager Azure PowerShell. Per istruzioni dettagliate, vedere [connettersi a Azure Resource Manager sul dispositivo Azure stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Assicurarsi di poter usare la procedura seguente per accedere al dispositivo dal client. Questa configurazione è già stata eseguita quando si è connessi a Azure Resource Manager e ora si sta verificando che la configurazione sia stata completata correttamente. 

1. Verificare che Azure Resource Manager la comunicazione stia funzionando eseguendo il comando seguente:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Per chiamare le API del dispositivo locale per l'autenticazione, immettere: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Per connettersi tramite Azure Resource Manager, specificare il nome utente *EdgeARMuser* e la password.

1. Se il calcolo è stato configurato per Kubernetes, è possibile ignorare questo passaggio. In caso contrario, assicurarsi che sia stata abilitata un'interfaccia di rete per il calcolo eseguendo le operazioni seguenti: 

   a. Nell'interfaccia utente locale passare a **calcolo** impostazioni.  
   b. Selezionare l'interfaccia di rete che si desidera utilizzare per creare un commutire virtuale. Le macchine virtuali create verranno associate a un Commuter virtuale collegato a questa porta e alla rete associata. Assicurarsi di scegliere una rete corrispondente all'indirizzo IP da usare per la macchina virtuale.  

    ![Screenshot del riquadro impostazioni di rete per la configurazione di calcolo.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. In **Abilita per calcolo** sull'interfaccia di rete selezionare **Sì**. Azure Stack Edge creerà e gestirà un commutire virtuale che corrisponde a tale interfaccia di rete. Non immettere in questo momento indirizzi IP specifici per Kubernetes. L'abilitazione del calcolo può richiedere diversi minuti.

    > [!NOTE]
    > Se si stanno creando macchine virtuali GPU, selezionare un'interfaccia di rete connessa a Internet. In questo modo è possibile installare un'estensione GPU nel dispositivo.


