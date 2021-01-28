---
title: Gestione calcolo GPU Pro Azure Stack Edge | Microsoft Docs
description: Viene descritto come gestire le impostazioni di calcolo Edge, ad esempio trigger, moduli, Visualizza configurazione di calcolo, Rimuovi configurazione tramite il portale di Azure sulla GPU Pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/27/2021
ms.author: alkohli
ms.openlocfilehash: 4c4fbef807d31e03a79f80db7fd29580074fb8bd
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955455"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Gestire le risorse di calcolo sulla GPU Pro Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questo articolo descrive come gestire le risorse di calcolo tramite IoT Edge servizio nel dispositivo GPU Pro Azure Stack Edge. È possibile gestire il calcolo tramite il portale di Azure o l'interfaccia utente Web locale. Usare il portale di Azure per gestire moduli, trigger e configurazione IoT Edge e l'interfaccia utente Web locale per gestire le impostazioni di rete di calcolo.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Gestire i trigger
> * Gestire la configurazione di IoT Edge


## <a name="manage-triggers"></a>Gestire i trigger

Gli eventi sono fenomeni che possono verificarsi all'interno dell'ambiente cloud o sul dispositivo su cui si desidera intervenire. Ad esempio, un evento è quando si crea un file in una condivisione. I trigger generano gli eventi. Per il Pro Azure Stack Edge, i trigger possono essere in risposta a eventi di file o a una pianificazione.

- **File**: questi trigger sono in risposta a eventi di file, ad esempio la creazione di un file, la modifica di un file.
- **Pianificata**: questi trigger sono in risposta a una pianificazione che è possibile definire con una data di inizio, un'ora di inizio e l'intervallo di ripetizione.


### <a name="add-a-trigger"></a>Aggiungere un trigger

Eseguire i passaggi seguenti nel portale di Azure per creare un trigger.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **IOT Edge**. Passare a **trigger** e selezionare **+ Aggiungi trigger** sulla barra dei comandi.

    ![Selezionare Aggiungi trigger](media/azure-stack-edge-j-series-manage-compute/add-trigger-1m.png)

2. Nel pannello **Aggiungi trigger** specificare un nome univoco per il trigger.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Selezionare un **Tipo** per il trigger. Scegliere **File** se il trigger è in risposta a un evento del file. Selezionare **Scheduled** quando si desidera avviare il trigger a un'ora definita ed eseguirlo a un intervallo di ripetizione specificato. A seconda della selezione, viene presentato un set diverso di opzioni.

    - **Trigger di file**: scegliere nell'elenco a discesa una condivisione montata. Quando si attiva un evento del file in tale condivisione, il trigger potrebbe richiamare una funzione di Azure.

        ![Aggiunta di una condivisione SMB](media/azure-stack-edge-j-series-manage-compute/add-file-trigger.png)

    - **Trigger pianificato**: specificare la data/ora di inizio e l'intervallo di ripetizione espresso in ore, minuti o secondi. Inoltre, immettere il nome per un argomento. Un argomento fornirà la flessibilità necessaria per indirizzare il trigger a un modulo distribuito nel dispositivo.

        `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` è una stringa di indirizzamento di esempio.

        ![Aggiunta di una condivisione NFS](media/azure-stack-edge-j-series-manage-compute/add-scheduled-trigger.png)

4. Selezionare **Aggiungi** per creare il trigger. Una notifica mostra che la creazione del trigger è in corso. Dopo aver creato il trigger, il pannello viene aggiornato per effettuare la reflection del nuovo trigger.
 
    ![Elenco aggiornato di trigger](media/azure-stack-edge-j-series-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Eliminare un trigger

Eseguire i passaggi seguenti nel portale di Azure per eliminare un trigger.

1. Nell'elenco dei trigger selezionare il trigger che si desidera eliminare.

    ![Selezionare il trigger](media/azure-stack-edge-j-series-manage-compute/delete-trigger-1.png)

2. Fare clic con il pulsante destro del mouse e quindi scegliere **Elimina**.

    ![Selezionare Elimina](media/azure-stack-edge-j-series-manage-compute/delete-trigger-2.png)

3. Quando viene richiesta la conferma, fare clic su **Sì**.

    ![Conferma dell'eliminazione](media/azure-stack-edge-j-series-manage-compute/delete-trigger-3.png)

Viene inoltre aggiornato l'elenco dei trigger per riflettere l'eliminazione.

## <a name="manage-iot-edge-configuration"></a>Gestire la configurazione di IoT Edge

Usare il portale di Azure per visualizzare la configurazione di calcolo, rimuovere una configurazione di calcolo esistente o aggiornare la configurazione di calcolo per sincronizzare le chiavi di accesso per il dispositivo e il dispositivo IoT Edge per l'Azure Stack Edge Pro.

### <a name="view-iot-edge-configuration"></a>Visualizza configurazione IoT Edge

Per visualizzare la configurazione IoT Edge per il dispositivo, seguire questa procedura nel portale di Azure.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **IOT Edge**. Dopo l'abilitazione del servizio IoT Edge sul dispositivo, nella pagina panoramica viene indicato che il servizio IoT Edge è in esecuzione correttamente.

    ![Selezionare Visualizza calcolo](media/azure-stack-edge-j-series-manage-compute/view-compute-1.png)

2. Passare a **Proprietà** per visualizzare la configurazione del IOT Edge nel dispositivo. Dopo aver configurato il calcolo, verrà creata una risorsa dell'hub IoT. In tale risorsa dell'hub IoT, sono configurati un dispositivo IoT e un dispositivo IoT Edge. Solo i moduli di Linux sono supportati per l'esecuzione nel dispositivo IoT Edge.

    ![Visualizza configurazione](media/azure-stack-edge-j-series-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>Rimuovi servizio IoT Edge

Per rimuovere la configurazione IoT Edge esistente per il dispositivo, seguire questa procedura nel portale di Azure.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **IOT Edge**. Passare a **Panoramica** e selezionare **Rimuovi** sulla barra dei comandi.

    ![Selezionare Rimuovi calcolo](media/azure-stack-edge-j-series-manage-compute/remove-compute-1.png)

2. Se si rimuove il servizio IoT Edge, l'azione è irreversibile e non può essere annullata. Verranno eliminati anche i moduli e i trigger creati. Sarà necessario riconfigurare il dispositivo nel caso in cui sia necessario usare nuovamente IoT Edge. Quando viene richiesta la conferma, fare clic su **OK**.

    ![Selezionare Rimuovi calcolo 2](media/azure-stack-edge-j-series-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Sincronizzazione delle chiavi di accesso per il dispositivo IoT e il dispositivo IoT Edge

Quando si configura il calcolo nel Azure Stack Edge Pro, vengono creati un dispositivo e un dispositivo IoT Edge. A questi dispositivi vengono assegnate automaticamente delle chiavi di accesso simmetriche. Per una protezione ottimale, queste chiavi vengono ruotate regolarmente tramite il servizio dell'hub IoT.

Per ruotare queste chiavi di accesso, è possibile accedere al servizio dell'hub IoT creato e selezionare il dispositivo IoT o il dispositivo IoT Edge. Ogni dispositivo dispone di una chiave di accesso primaria e di una chiave di accesso secondaria. Assegnare la chiave di accesso primaria alla chiave di accesso secondaria, quindi riscrivere la chiave di accesso primaria.

Se il dispositivo Internet delle cose e IoT Edge chiavi del dispositivo sono stati ruotati, è necessario aggiornare la configurazione in Azure Stack Edge Pro per ottenere le chiavi di accesso più recenti. La sincronizzazione consente al dispositivo di ottenere le chiavi più recenti per il dispositivo IoT e il dispositivo IoT Edge. Azure Stack Edge Pro usa solo le chiavi di accesso primarie.

Eseguire i passaggi seguenti nel portale di Azure per sincronizzare le chiavi di accesso per il proprio dispositivo.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **IOT Edge calcolo**. Passare a **Panoramica** e selezionare **Aggiorna configurazione** sulla barra dei comandi.

    ![Selezionare Aggiorna configurazione](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-1.png)

2. Selezionare **Sì** quando viene richiesta la conferma.

    ![Selezionare Sì quando richiesto](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-2.png)

3. Uscire dalla finestra di dialogo dopo aver completato la sincronizzazione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [risolvere i problemi di Azure stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md).
