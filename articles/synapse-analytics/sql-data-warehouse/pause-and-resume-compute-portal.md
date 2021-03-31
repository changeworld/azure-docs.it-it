---
title: "Avvio rapido: Sospendere e riprendere l'esecuzione delle risorse di calcolo in un pool SQL dedicato con il portale di Azure"
description: Usare il portale di Azure per sospendere l'esecuzione delle risorse di calcolo per un pool SQL dedicato per risparmiare sui costi. Riprendere il calcolo quando si è pronti a usare il data warehouse.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f9cc5083c4b515454b9d5cbc40ed3b48cba80211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602163"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>Avvio rapido: Sospendere e riprendere l'esecuzione delle risorse di calcolo in un pool SQL dedicato con il portale di Azure

È possibile usare il portale di Azure per sospendere e riprendere l'esecuzione delle risorse di calcolo del pool SQL dedicato. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Prima di iniziare

Vedere [Creare e connettere - Portale](../quickstart-create-sql-pool-portal.md) per creare un pool SQL dedicato denominato **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Sospendere le risorse di calcolo

Per ridurre i costi, è possibile sospendere e riprendere le risorse di calcolo su richiesta. Ad esempio, se non si usa il database durante la notte e nei fine settimana, è possibile sospenderlo in questi intervalli di tempo e riprenderne l'esecuzione durante il giorno.
 
>[!NOTE]
>Mentre il database è sospeso, non verranno addebitati costi per le risorse di calcolo. Continueranno tuttavia a essere applicati addebiti per l'archiviazione. 

Seguire questa procedura per sospendere un pool SQL dedicato:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare alla pagina **Pool SQL dedicato** per aprire il pool SQL. 
3. Si noti che **Stato** è impostato su **Online**.

    ![Calcolo delle risorse online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Per sospendere il pool SQL dedicato, fare clic sul pulsante **Sospendi**. 
5. Viene visualizzato un messaggio in cui viene chiesto se si vuole continuare. Fare clic su **Sì**.
6. Attendere qualche minuto e quindi verificare che lo **Stato** sia **Sospensione**.

    ![Screenshot che mostra il portale di Azure per un data warehouse di esempio con stato Ripresa.](./media/pause-and-resume-compute-portal/pausing.png)

7. Al termine dell'operazione di sospensione, lo stato diventa **Sospeso** e il pulsante di opzione è **Riprendi**.
8. Le risorse di calcolo per il pool SQL dedicato sono ora offline. Non verranno addebitate le risorse di calcolo fino al ripristino del servizio.

    ![Calcolo delle risorse offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Riavviare le risorse di calcolo

Seguire questa procedura per riprendere un pool SQL dedicato.

1. Passare alla pagina **Pool SQL dedicato** per aprire il pool SQL.
3. Nella pagina **mySampleDataWarehouse** verificare che **Stato** sia **Sospeso**.

    ![Calcolo delle risorse offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Per riprendere il pool SQL, fare clic su **Riprendi**. 
1. Viene visualizzato un messaggio in cui viene chiesto se si vuole avviare. Fare clic su **Sì**.
1. Si noti che lo **Stato** è **Ripresa**.

    ![Screenshot che mostra il portale di Azure per un data warehouse di esempio con il pulsante di avvio selezionato e uno stato di ripresa.](./media/pause-and-resume-compute-portal/resuming.png)

1. Quando il pool SQL è di nuovo online, lo stato diventa **Online** e il pulsante di opzione è **Sospendi**.
1. Le risorse di calcolo per il pool SQL sono ora online ed è possibile usare il servizio. Verranno ripresi gli addebiti per il calcolo.

    ![Calcolo delle risorse online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Verranno addebitati i costi per le unità Data Warehouse e per i dati archiviati nel pool SQL dedicato. Le risorse di calcolo e archiviazione vengono fatturate separatamente. 

- Se si vogliono mantenere i dati nelle risorse di archiviazione, sospendere il calcolo.
- Per evitare di ricevere addebiti in futuro, è possibile eliminare il pool SQL dedicato. 

Seguire questa procedura per pulire le risorse nel modo desiderato.

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare il pool SQL dedicato.

    ![Pulire le risorse](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Per sospendere il calcolo, fare clic sul pulsante **Pausa**. 

1. Per rimuovere il pool SQL dedicato in modo da non ricevere addebiti per risorse di calcolo o di archiviazione, fare clic su **Elimina**.



## <a name="next-steps"></a>Passaggi successivi

Le risorse di calcolo del pool SQL dedicato sono state sospese e riprese. Per altre informazioni, vedere l'articolo successivo, [Caricare i dati in un pool SQL dedicato](./load-data-from-azure-blob-storage-using-copy.md). Per altre informazioni sulla gestione delle funzionalità di calcolo, vedere l'articolo [Panoramica sulla gestione delle risorse di calcolo](sql-data-warehouse-manage-compute-overview.md).