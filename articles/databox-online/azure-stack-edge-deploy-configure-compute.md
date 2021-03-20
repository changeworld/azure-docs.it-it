---
title: Esercitazione su come filtrare e analizzare i dati con il calcolo in Azure Stack Edge Pro | Microsoft Docs
description: Informazioni su come configurare il ruolo di calcolo in Azure Stack Edge Pro e usarlo per trasformare i dati prima dell'invio ad Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 10741fbf2deb61d63f444ff9e2247bc59f41af38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97968728"
---
# <a name="tutorial-transform-the-data-with-azure-stack-edge-pro"></a>Esercitazione: Trasformare i dati con Azure Stack Edge Pro

Questa esercitazione descrive come configurare un ruolo di calcolo nel dispositivo Azure Stack Edge Pro. Dopo la configurazione del ruolo di calcolo, Azure Stack Edge Pro può trasformare i dati prima che vengano inviati ad Azure.

Il completamento di questa procedura può richiedere circa 10-15 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il calcolo
> * Aggiungere condivisioni
> * Aggiungere un ruolo di calcolo
> * Verificare la trasformazione e il trasferimento dei dati

 
## <a name="prerequisites"></a>Prerequisiti

Prima di configurare un ruolo di calcolo nel dispositivo Azure Stack Edge Pro, assicurarsi di:

- Aver attivato il dispositivo Azure Stack Edge Pro come descritto in [Connettere, configurare e attivare Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurare il calcolo

Per configurare il calcolo in Azure Stack Edge Pro, creare una risorsa hub IoT.

1. Nel portale di Azure della risorsa Azure Stack Edge passare a **Panoramica**. Nel riquadro destro selezionare **IoT Edge**.

    ![Introduzione al calcolo](./media/azure-stack-edge-deploy-configure-compute/configure-compute-1.png)

1. Nel riquadro **Abilita servizio IoT Edge** selezionare **Aggiungi**. Viene abilitato il servizio IoT Edge, che consente di distribuire i moduli IoT Edge in locale nel dispositivo.

    ![Introduzione al calcolo 2](./media/azure-stack-edge-deploy-configure-compute/configure-compute-2.png)

1. Nel pannello **Crea servizio IoT Edge** immettere quanto segue:

   
    |Campo  |Valore  |
    |---------|---------|
    |Subscription     |Selezionare una sottoscrizione per la risorsa hub IoT. È possibile usare la stessa sottoscrizione usata dalla risorsa Azure Stack Edge.         |
    |Gruppo di risorse     |Selezionare un gruppo di risorse per la risorsa hub IoT. È possibile usare lo stesso gruppo di risorse usato dalla risorsa Azure Stack Edge.         |
    |Hub IoT     | Scegliere **Nuovo** o **Esistente**. <br> Per impostazione predefinita, per creare una risorsa IoT viene usato un livello Standard (S1). Per usare una risorsa IoT di un livello gratuito, crearne uno e quindi selezionare la risorsa esistente. <br> In ogni caso, la risorsa hub IoT usa la stessa sottoscrizione e lo stesso gruppo di risorse usati dalla risorsa Azure Stack Edge.     |
    |Nome     |Immettere un nome per la risorsa hub IoT.         |

    ![Introduzione al calcolo 3](./media/azure-stack-edge-deploy-configure-compute/configure-compute-3.png)

4. Selezionare **Rivedi e crea**. La creazione della risorsa hub IoT richiede qualche minuto. Una volta creata la risorsa hub IoT, la pagina **Panoramica** viene aggiornata per indicare che il servizio IoT Edge è in esecuzione. 

    ![Introduzione al calcolo 4](./media/azure-stack-edge-deploy-configure-compute/configure-compute-4.png)    
    
    Quando il servizio IoT Edge è configurato nel dispositivo Edge, crea due dispositivi: un dispositivo IoT e un dispositivo IoT Edge. Entrambi i dispositivi possono essere visualizzati nella risorsa dell'hub IoT. Nel dispositivo IoT Edge viene eseguito anche un runtime IoT Edge. Attualmente per il dispositivo IoT Edge è disponibile solo la piattaforma Linux.

    Per verificare che il ruolo di calcolo Edge sia stato configurato, selezionare **Servizio IoT Edge > Proprietà** e visualizzare il dispositivo IoT e il dispositivo IoT Edge.

    ![Introduzione al calcolo 5](./media/azure-stack-edge-deploy-configure-compute/configure-compute-5.png) 

## <a name="add-shares"></a>Aggiungere condivisioni

Per la distribuzione semplice di questa esercitazione, saranno necessarie due condivisioni: una condivisione Edge e un'altra condivisione locale Edge.

1. Aggiungere una condivisione Edge nel dispositivo seguendo questa procedura:

    1. Nella risorsa Azure Stack Edge passare a **IoT Edge > Condivisioni**.
    2. Sulla barra dei comandi selezionare **+ Aggiungi condivisione**.
    3. Nel pannello **Aggiungi condivisione** specificare il nome della condivisione e selezionarne il tipo.
    4. Per montare la condivisione Edge, selezionare la casella di controllo **Usa la condivisione con il calcolo Edge**.
    5. Selezionare **Account di archiviazione**, **Servizio di archiviazione**, un utente esistente e quindi **Crea**.

        ![Aggiungere una condivisione Edge](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-1.png) 

    Se è stata creata una condivisione NFS locale, usare l'opzione di comando di sincronizzazione remota (rsync) seguente per copiare i file nella condivisione:

    `rsync <source file path> < destination file path>`

    Per altre informazioni sul comando rsync, vedere la [documentazione di rsync](https://www.computerhope.com/unix/rsync.htm).

    La condivisione Edge verrà creata e si riceverà una notifica di creazione riuscita. L'elenco delle condivisioni può essere aggiornato, ma è necessario attendere il completamento della creazione della condivisione.

2. Aggiungere una condivisione locale Edge nel dispositivo Edge ripetendo tutti i passaggi della procedura precedente e selezionando la casella di controllo **Configura come condivisione locale Edge**. I dati nella condivisione locale rimangono nel dispositivo.

    ![Aggiungere una condivisione locale Edge](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Passare a **IoT Edge > Condivisioni** per visualizzare l'elenco aggiornato di condivisioni.

    ![Elenco aggiornato delle condivisioni](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Aggiungere un modulo

È possibile aggiungere un modulo personalizzato o predefinito. In questo dispositivo Edge non sono presenti moduli personalizzati. Per informazioni su come creare un modulo personalizzato, vedere [Sviluppare un modulo C# per il dispositivo Azure Stack Edge Pro](azure-stack-edge-create-iot-edge-module.md).

In questa sezione si aggiungerà un modulo personalizzato al dispositivo IoT Edge creato in [Sviluppare un modulo C# per il dispositivo Azure Stack Edge Pro](azure-stack-edge-create-iot-edge-module.md). Questo modulo personalizzato sposta i file da una condivisione locale Edge nel dispositivo Edge a una condivisione cloud Edge nel dispositivo. La condivisione cloud esegue quindi il push dei file all'account di archiviazione di Azure associato.

1. Passare a **IoT Edge > Moduli**. Sulla barra dei comandi del dispositivo selezionare **+ Aggiungi modulo**.
2. Nel pannello **Configura e aggiungi modulo** immettere i valori seguenti:

    
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

    ![Aggiungere e configurare un modulo](./media/azure-stack-edge-deploy-configure-compute/add-module-1.png)

3. Selezionare **Aggiungi**. Il modulo verrà aggiunto. La pagina **IoT Edge > Panoramica** viene aggiornata per indicare che il modulo è stato distribuito. 

    ![Modulo distribuito](./media/azure-stack-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verificare la trasformazione e il trasferimento dei dati

Il passaggio finale consiste nell'assicurarsi che il modulo sia connesso e in esecuzione come previsto. Lo stato di runtime del modulo deve essere In esecuzione per il dispositivo IoT Edge nella risorsa dell'hub IoT.

Per verificare che il modulo sia in esecuzione, eseguire queste operazioni:

1. Selezionare il riquadro **Aggiungi modulo**. Verrà visualizzato il pannello **Moduli**. Nell'elenco dei moduli identificare il modulo che è stato distribuito. Lo stato di runtime del modulo aggiunto sarà *in esecuzione*.

    ![Verificare la trasformazione dei dati](./media/azure-stack-edge-deploy-configure-compute/verify-data-1.png)
 
1. In Esplora file connettersi alla condivisione locale Edge e alla condivisione Edge create in precedenza.

    ![Verificare la trasformazione dei dati 2](./media/azure-stack-edge-deploy-configure-compute/verify-data-2.png) 
 
1. Aggiungere i dati alla condivisione locale.

    ![Verificare la trasformazione dei dati 3](./media/azure-stack-edge-deploy-configure-compute/verify-data-3.png) 
 
    I dati vengono spostati nella condivisione cloud.

    ![Verificare la trasformazione dei dati 4](./media/azure-stack-edge-deploy-configure-compute/verify-data-4.png)  

    Viene quindi eseguito il push dei dati dalla condivisione cloud all'account di archiviazione. Per visualizzare i dati, passare a Storage Explorer.

    ![Verificare la trasformazione dei dati 5](./media/azure-stack-edge-deploy-configure-compute/verify-data-5.png) 
 
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
> [Usare l'interfaccia utente Web locale per amministrare un dispositivo Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
