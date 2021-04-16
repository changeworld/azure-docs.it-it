---
title: Integrazione e distribuzione continue per il pool SQL dedicato
description: Esperienza DevOps di database di livello aziendale per pool SQL dedicato in Azure Synapse Analytics con supporto predefinito per l'integrazione continua e la distribuzione tramite Azure Pipelines.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 95bf3a8c614b8b7c0269257cb62b3c3a0f60be13
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568285"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integrazione e distribuzione continue per il pool SQL dedicato in Azure Synapse Analytics

Questa semplice esercitazione illustra come integrare il progetto di database di SQL Server Data Tools (SSDT) con Azure DevOps e sfruttare Azure Pipelines per configurare l'integrazione e la distribuzione continue. Questa esercitazione rappresenta il secondo passaggio per la creazione della pipeline di integrazione e distribuzione continua per il data warehousing.

## <a name="before-you-begin"></a>Prima di iniziare

- Completare l'[esercitazione sull'integrazione del controllo del codice sorgente](sql-data-warehouse-source-control-integration.md)

- Configurare Azure DevOps e connettersi

## <a name="continuous-integration-with-visual-studio-build"></a>Integrazione continua con compilazione di Visual Studio

1. Passare ad Azure Pipelines e creare una nuova pipeline di compilazione.

      ![Nuova pipeline](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nuova pipeline")

2. Selezionare il repository del codice sorgente (Git Azure Repos) e selezionare il modello di app desktop .NET.

      ![Configurazione della pipeline](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Configurazione della pipeline")

3. Modificare il file YAML per usare il pool appropriato dell'agente. Il file YAML sarà simile al seguente:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

A questo punto, si dispone di un ambiente semplice in cui qualsiasi archiviazione nel ramo principale del repository del controllo del codice sorgente deve attivare automaticamente una compilazione Visual Studio del progetto di database. Verificare che l'automazione funzioni end-to-end apportando una modifica al progetto di database locale e archiviando tale modifica nel ramo principale.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Distribuzione continua con l'attività Azure Synapse Analytics (o Database)

1. Aggiungere una nuova attività usando l'[attività di distribuzione del database SQL di Azure](/azure/devops/pipelines/targets/azure-sqldb) e compilare i campi obbligatori per connettersi al data warehouse di destinazione. Quando questa attività viene eseguita, il pacchetto di applicazione livello dati generato dal processo di compilazione precedente viene distribuito nel data warehouse di destinazione. È anche possibile usare [l'Azure Synapse Analytics di distribuzione .](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)

      ![Attività di distribuzione](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Attività di distribuzione")

2. Se si usa un agente self-hosted, assicurarsi di impostare la variabile di ambiente in modo da usare il SqlPackage.exe corretto per Azure Synapse Analytics. Il percorso dovrà essere simile a questo:

      ![Variabile di ambiente](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Variabile di ambiente")

   C:\Programmi (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Eseguire e convalidare la pipeline. È possibile apportare modifiche localmente e archiviarle nel controllo del codice sorgente per generare una compilazione e una distribuzione automatiche.

## <a name="next-steps"></a>Passaggi successivi

- Esplorare [l'architettura del pool SQL dedicato (in precedenza SQL DW)](massively-parallel-processing-mpp-architecture.md)
- Creare [rapidamente un pool SQL dedicato (in precedenza SQL DW)](create-data-warehouse-portal.md)
- [Caricare dati di esempio](./load-data-from-azure-blob-storage-using-copy.md)
- Esplorare i [video](sql-data-warehouse-videos.md)