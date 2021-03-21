---
title: 'Avvio rapido: Ridimensionare le risorse di calcolo per il pool SQL di Synapse (portale di Azure)'
description: È possibile ridimensionare le risorse di calcolo per il pool SQL di Synapse (data warehouse) usando il portale di Azure.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/28/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 26a8a865a787a9c9b17031f94456272c93380704
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117045"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Avvio rapido: Ridimensionare le risorse di calcolo per il pool SQL di Synapse con il portale di Azure

È possibile ridimensionare le risorse di calcolo per il pool SQL di Synapse (data warehouse) usando il portale di Azure. [aumentandone il numero](sql-data-warehouse-manage-compute-overview.md) per ottenere prestazioni migliori o riducendolo per diminuire i costi. 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Prima di iniziare

È possibile ridimensionare un pool SQL già disponibile oppure fare riferimento a [Avvio rapido: Creare e connettere - portale](create-data-warehouse-portal.md) per creare un pool SQL denominato **mySampleDataWarehouse**. In questa Guida introduttiva viene ridimensionato **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Il pool SQL da ridimensionare deve essere online. 

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo

È possibile ridimensionare le risorse di calcolo del pool SQL aumentando o diminuendo le unità di data warehouse. Nella guida [Avvio rapido: Creare e connettere - Portale](create-data-warehouse-portal.md) è stato creato il data warehouse **mySampleDataWarehouse** ed è stato inizializzato con 400 DWU. La procedura seguente modifica le unità Data Warehouse per **mySampleDataWarehouse**.

Per modificare le unità Data Warehouse:

1. Fare clic su **Azure Synapse Analytics (in precedenza SQL DW)** nella pagina sinistra del portale di Azure.
2. Selezionare **mySampleDataWarehouse** nella pagina **Azure Synapse Analytics (in precedenza SQL DW)** . Verrà aperto il pool SQL.
3. Fare clic su **Scale**.

    ![Fare clici su Ridimensiona](./media/quickstart-scale-compute-portal/click-scale.png)

2. Nel pannello Ridimensiona spostare il dispositivo di scorrimento a sinistra o a destra per modificare l'impostazione delle unità Data Warehouse. Quindi selezionare Ridimensiona.

    ![Spostare il dispositivo di scorrimento](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul pool SQL, continuare con l'esercitazione [Caricare i dati in un pool SQL](./load-data-from-azure-blob-storage-using-copy.md).