---
title: "Avvio rapido: Creare un'istanza con il portale di Azure"
titleSuffix: Azure Database Migration Service
description: Usare il portale di Azure per creare un'istanza del Servizio Migrazione del database di Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/29/2021
ms.openlocfilehash: 8a500104a0273b9e131815c4dc832bd33729cd51
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566592"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Avvio rapido: Creare un'istanza del Servizio Migrazione del database di Azure usando il portale di Azure

In questa Guida introduttiva si usa il portale di Azure per creare un'istanza del servizio migrazione del database di Azure. Dopo aver creato l'istanza, è possibile usarla per eseguire la migrazione di dati da più origini di database alle piattaforme dati di Azure, ad esempio da SQL Server al database SQL di Azure o da SQL Server a un Istanza gestita SQL di Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Nel Web browser passare al [portale di Microsoft Azure](https://portal.azure.com/) e quindi immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

> [!NOTE]
> È possibile creare fino a 10 istanze di Servizio Migrazione del database per area. Se è necessario un numero maggiore di istanze, creare un ticket di supporto.

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

Prima di creare la prima istanza del servizio Migrazione del database, registrare il provider di risorse Microsoft.DataMigration.

1. Nel portale di Azure cercare e selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/quickstart-create-data-migration-service-portal/portal-select-subscription.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/quickstart-create-data-migration-service-portal/portal-select-resource-provider.png)

3. Cercare migration e quindi selezionare **Registra** per **Microsoft.DataMigration**.

    ![Registrare il provider di risorse](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Creare un'istanza del servizio

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**. Cercare e selezionare **Servizio Migrazione del database di Azure**.

    ![Azure Marketplace](media/quickstart-create-data-migration-service-portal/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/quickstart-create-data-migration-service-portal/dms-create.png)

3. Nella schermata Informazioni di base di **Crea servizio Migrazione**:

     - Selezionare la sottoscrizione.
     - Creare un nuovo gruppo di risorse o sceglierne uno esistente.
     - Specificare un nome per l'istanza del Servizio Migrazione del database di Azure.
     - Selezionare la località in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure.
     - Scegliere **Azure** come modalità del servizio.
     - Selezione di un piano tariffario. Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).
     
    ![Configurare le impostazioni di base dell'istanza del Servizio Migrazione del database di Azure](media/quickstart-create-data-migration-service-portal/dms-create-basics.png)

     - Al termine, selezionare Avanti: Rete.

4. Nella schermata Rete di **Crea servizio Migrazione**:

    - Selezionare una rete virtuale esistente o crearne una nuova. La rete virtuale consente al Servizio Migrazione del database di Azure di accedere al database di origine e all'ambiente di destinazione. Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale con il portale di Azure](../virtual-network/quick-create-portal.md).

    ![Configurare le impostazioni di rete dell'istanza del Servizio Migrazione del database di Azure](media/quickstart-create-data-migration-service-portal/dms-network-settings.png)

    - Selezionare **Rivedi e crea** per creare il servizio. 
    
    - Dopo alcuni istanti, l'istanza del servizio migrazione del database di Azure viene creata e pronta per l'uso:

    ![Creare il servizio migrazione](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile pulire le risorse create in questa Guida introduttiva eliminando il [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md). Per eliminare il gruppo di risorse, passare all'istanza del servizio Migrazione del database di Azure creata. Selezionare il nome **Gruppo di risorse** e quindi selezionare **Elimina gruppo di risorse**. Questa azione elimina tutti gli asset nel gruppo di risorse, nonché il gruppo stesso.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire la migrazione offline di SQL Server a Database SQL di Azure](tutorial-sql-server-to-azure-sql.md)
* [Eseguire la migrazione online di SQL Server a Database SQL di Azure](./tutorial-sql-server-to-azure-sql.md)
* [Eseguire la migrazione di SQL Server a un Istanza gestita SQL di Azure offline](tutorial-sql-server-to-managed-instance.md)
* [Eseguire la migrazione di SQL Server a una Istanza gestita online di Azure SQL](tutorial-sql-server-managed-instance-online.md)