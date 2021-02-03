---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500311"
---
Prima di poter distribuire le macchine virtuali nel dispositivo Azure Stack Edge, è necessario configurare il client per la connessione al dispositivo tramite Azure Resource Manager Azure PowerShell. Per i passaggi dettagliati, vedere [connettersi a Azure Resource Manager nel dispositivo Azure stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Assicurarsi che sia possibile usare i passaggi seguenti per accedere al dispositivo dal client. Questa configurazione è stata eseguita durante la connessione a Azure Resource Manager. A questo punto si sta verificando che la configurazione sia stata completata correttamente. 

1. Verificare il funzionamento della comunicazione Azure Resource Manager. Digitare:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Chiamare le API del dispositivo locale per l'autenticazione. Digitare: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Specificare il nome utente- *EdgeARMuser* e la password per la connessione tramite Azure Resource Manager.

1. Se il **calcolo** è stato configurato per Kubernetes, è possibile ignorare questo passaggio. Procedere per assicurarsi di avere abilitato un'interfaccia di rete per il calcolo. Nell'interfaccia utente locale passare a **calcolo** impostazioni. Selezionare l'interfaccia di rete che si desidera utilizzare per creare un commutire virtuale. Le macchine virtuali create verranno collegate a un commutere virtuale collegato a questa porta e alla rete associata. Assicurarsi di scegliere una rete che corrisponda all'indirizzo IP che verrà usato per la macchina virtuale.  

    ![Screenshot che illustra come abilitare le impostazioni di calcolo.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Abilitare il calcolo nell'interfaccia di rete. Azure Stack Edge creerà e gestirà un commutire virtuale corrispondente a tale interfaccia di rete. Non immettere in questo momento indirizzi IP specifici per Kubernetes. L'abilitazione del calcolo può richiedere diversi minuti.

    > [!NOTE]
    > Se si stanno creando macchine virtuali GPU, selezionare un'interfaccia di rete connessa a Internet. In questo modo è possibile installare un'estensione GPU nel dispositivo.


