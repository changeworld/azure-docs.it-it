---
title: Usare un modulo di IoT Edge per distribuire il carico di lavoro di calcolo su Azure Stack Edge Pro con GPU | Microsoft Docs
description: Informazioni su come eseguire un carico di lavoro di calcolo usando un modulo IoT Edge creato in precedenza nel dispositivo GPU Pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 73d3b6dc85f94aba63e614c03294c054047ccb89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568580"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Esercitazione: eseguire un carico di lavoro di calcolo con il modulo IoT Edge in Azure Stack GPU Pro Edge

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Questa esercitazione descrive come eseguire un carico di lavoro di calcolo usando un modulo IoT Edge sul dispositivo GPU Pro Azure Stack Edge. Dopo aver configurato il calcolo, il dispositivo trasformerà i dati prima di inviarli ad Azure.

Il completamento di questa procedura può richiedere circa 10-15 minuti.


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il calcolo
> * Aggiungere condivisioni
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

 
## <a name="prerequisites"></a>Prerequisiti

Prima di configurare un ruolo di calcolo sul dispositivo GPU Azure Stack Edge Pro, verificare che:

- Aver attivato il dispositivo Azure Stack Edge Pro come descritto in [Attivare il dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Si dispone di un modulo IoT Edge che è possibile eseguire sui dati. In questa esercitazione è stato usato un `filemove2` modulo che sposta i dati dalla condivisione locale perimetrale sul dispositivo alla condivisione perimetrale da cui vengono inviati i dati all'account di archiviazione di Azure.


## <a name="configure-compute"></a>Configurare il calcolo

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Aggiungere condivisioni

Per la distribuzione semplice di questa esercitazione, saranno necessarie due condivisioni: una condivisione Edge e un'altra condivisione locale Edge.

1. Per aggiungere una condivisione perimetrale sul dispositivo, seguire questa procedura:

    1. Nella risorsa Azure Stack Edge passare a **cloud storage gateway > shares**.
    2. Sulla barra dei comandi selezionare **+ Aggiungi condivisione**.
    3. Nel pannello **Aggiungi condivisione** specificare il nome della condivisione e selezionarne il tipo.
    4. Per montare la condivisione Edge, selezionare la casella di controllo **Usa la condivisione con il calcolo Edge**.
    5. Selezionare **Account di archiviazione**, **Servizio di archiviazione**, un utente esistente e quindi **Crea**.

        ![Aggiungere una condivisione Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > Per montare la condivisione NFS per il calcolo, la rete di calcolo deve essere configurata nella stessa subnet dell'indirizzo IP virtuale NFS. Per informazioni dettagliate su come configurare la rete di calcolo, vedere [Abilitare la rete di calcolo nel dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

    La condivisione Edge verrà creata e si riceverà una notifica di creazione riuscita. L'elenco delle condivisioni può essere aggiornato, ma è necessario attendere il completamento della creazione della condivisione.

2. Per aggiungere una condivisione locale perimetrale sul dispositivo, ripetere tutti i passaggi nel passaggio precedente e selezionare la casella di controllo **configura come condivisione locale Edge**. I dati nella condivisione locale rimangono nel dispositivo.

    ![Aggiungere una condivisione locale Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Se è stata creata una condivisione NFS locale, usare l'opzione di comando di sincronizzazione remota (rsync) seguente per copiare i file nella condivisione:

    `rsync <source file path> < destination file path>`

    Per altre informazioni sul comando `rsync`, vedere la [documentazione di `Rsync`](https://www.computerhope.com/unix/rsync.htm).
 
3. Passare a **cloud storage gateway > shares** per visualizzare l'elenco aggiornato delle condivisioni.

    ![Elenco aggiornato delle condivisioni](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Aggiungere un modulo

È possibile aggiungere un modulo personalizzato o predefinito. Il dispositivo non viene creato con moduli predefiniti o personalizzati. Per informazioni su come creare un modulo personalizzato, vedere [Sviluppare un modulo C# per il dispositivo Azure Stack Edge Pro](./azure-stack-edge-gpu-create-iot-edge-module.md).

In questa sezione si aggiungerà un modulo personalizzato al dispositivo IoT Edge creato in [Sviluppare un modulo C# per il dispositivo Azure Stack Edge Pro](./azure-stack-edge-gpu-create-iot-edge-module.md). Questo modulo personalizzato sposta i file da una condivisione locale Edge nel dispositivo Edge a una condivisione cloud Edge nel dispositivo. La condivisione cloud esegue quindi il push dei file all'account di archiviazione di Azure associato.

Per aggiungere un modulo, seguire questa procedura:

1. Passare a **IoT Edge > Moduli**. Dalla barra dei comandi selezionare **+ Aggiungi modulo**. 

2. Nel pannello **Aggiungi modulo** immettere i valori seguenti:

    
    |Campo  |Valore  |
    |---------|---------|
    |Nome     | Nome univoco per il modulo. Questo modulo è un contenitore Docker che può essere distribuito nel dispositivo IoT Edge associato ad Azure Stack Edge Pro.        |
    |URI immagine     | URI dell'immagine del contenitore corrispondente per il modulo.        |
    |Sono richieste credenziali     | Se questa opzione è selezionata, per recuperare i moduli con un URL corrispondente vengono usati un nome utente e una password.        |
    |Condivisione di input     | Selezionare una condivisione di input. In questo caso, la condivisione di input è la condivisione locale Edge. Il modulo usato in questo esempio sposta i file dalla condivisione locale Edge a una condivisione Edge in cui vengono caricati nel cloud.        |
    |Condivisione di output     | Selezionare una condivisione di output. In questo caso, la condivisione di output è la condivisione Edge.        |
    |Tipo di trigger     | Selezionare **File** o **Pianificazione**. Un trigger di file viene attivato ogni volta che si verifica un evento file, ad esempio la scrittura di un file nella condivisione di input. Un trigger pianificato viene attivato in base a una pianificazione definita dall'utente.         |
    |Nome trigger     | Nome univoco per il trigger.         |
    |Variabili di ambiente| Informazioni facoltative utili per definire l'ambiente in cui il modulo verrà eseguito.   |

    ![Aggiungere e configurare un modulo](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. Selezionare **Aggiungi**. Il modulo verrà aggiunto. La pagina **IoT Edge > Modules** viene aggiornata per indicare che il modulo è stato distribuito. Lo stato di runtime del modulo aggiunto sarà *in esecuzione*.

    ![Modulo distribuito](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificare la trasformazione e il trasferimento dei dati

Il passaggio finale consiste nel garantire che il modulo sia in esecuzione ed elabora i dati come previsto. Lo stato di runtime del modulo deve essere In esecuzione per il dispositivo IoT Edge nella risorsa dell'hub IoT.

Per verificare che il modulo sia in esecuzione ed elabora i dati come previsto, eseguire le operazioni seguenti:


1. In Esplora file connettersi alla condivisione locale Edge e alla condivisione Edge create in precedenza. Vedere i passaggi 

    ![Connettersi alle condivisioni cloud locali e perimetrali perimetrali](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Aggiungere i dati alla condivisione locale.

    ![File copiato nella condivisione locale perimetrale](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   I dati vengono spostati nella condivisione cloud.

    ![File spostato nella condivisione cloud perimetrale](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   Viene quindi eseguito il push dei dati dalla condivisione cloud all'account di archiviazione. Per visualizzare i dati, è possibile usare Storage Explorer o archiviazione di Azure nel portale.

    ![Verificare i dati nell'account di archiviazione](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
Il processo di convalida è stato completato.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il calcolo
> * Aggiungere condivisioni
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

Per informazioni su come amministrare il dispositivo Azure Stack Edge Pro, vedere:

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare un dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)