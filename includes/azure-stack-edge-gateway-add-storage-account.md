---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: cf67ea58c4c13756fdc6e437883f12124ca47eb9
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104859"
---
1. Nel [portale di Azure](https://portal.azure.com/) selezionare la risorsa Azure Stack Edge e quindi passare a **Panoramica**. Il dispositivo dovrebbe essere online. Passare a **Gateway di archiviazione nel cloud > Account di archiviazione**.

2. Selezionare **+ Aggiungi account di archiviazione** sulla barra dei comandi del dispositivo. 

   ![Aggiungere un account di archiviazione](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. Nel riquadro **Aggiungi account di archiviazione Edge** specificare le impostazioni seguenti:

    1. Consente di specificare un nome univoco per l'account di archiviazione perimetrale nel dispositivo. I nomi degli account di archiviazione possono contenere solo numeri e lettere minuscole. I caratteri speciali non sono consentiti. Il nome dell'account di archiviazione deve essere univoco nel singolo dispositivo, non in tutti i dispositivi.

    2. Consente di specificare una descrizione facoltativa per le informazioni sui dati contenuti nell'account di archiviazione.  
    
    3. Per impostazione predefinita, l'account di archiviazione perimetrale viene mappato a un account di archiviazione di Azure nel cloud e i dati dell'account di archiviazione vengono automaticamente inseriti nel cloud. Specificare l'account di archiviazione di Azure a cui è associato l'account di archiviazione Edge.

    4. Creare un nuovo contenitore o selezionarlo da un contenitore esistente nell'account di archiviazione di Azure. Tutti i dati del dispositivo scritti nell'account di archiviazione Edge vengono caricati automaticamente nel contenitore di archiviazione selezionato nell'account di archiviazione di Azure associato.

    5. Dopo aver specificato tutte le opzioni dell'account di archiviazione, selezionare **Aggiungi** per creare l'account di archiviazione Edge. Si riceve una notifica quando l'account di archiviazione perimetrale viene creato correttamente. Il nuovo account di archiviazione Edge viene quindi visualizzato nell'elenco degli account di archiviazione nel portale di Azure.

    <!--[Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->
    
4. Se si seleziona questo nuovo account di archiviazione e si passa a **Chiavi di accesso**, è possibile trovare l'endpoint servizio BLOB e il nome dell'account di archiviazione corrispondente. Copiare queste informazioni perché questi valori, unitamente alle chiavi di accesso, consentiranno di connettersi all'account di archiviazione Edge.

    ![Aggiungere un account di archiviazione 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    Per ottenere le chiavi di accesso, è necessario [connettersi alle API locali del dispositivo con Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md).