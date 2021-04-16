---
title: "Avvio rapido: Introduzione alla creazione di un'area di lavoro di Synapse"
description: In questa esercitazione si apprenderà a creare un'area di lavoro di Synapse, un pool SQL dedicato e un pool di Apache Spark serverless.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 61fcbfa9d40ebb26485ce8160fa3b011935ab4d6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536311"
---
# <a name="creating-a-synapse-workspace"></a>Creazione di un'area di lavoro di Synapse

In questa esercitazione si apprenderà a creare un'area di lavoro di Synapse, un pool SQL dedicato e un pool di Apache Spark serverless. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario avere accesso a un gruppo di risorse per il quale si dispone del ruolo di **Proprietario**. Creare l'area di lavoro Synapse in questo gruppo di risorse.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Creare un'area di lavoro Synapse nel portale di Azure

### <a name="start-the-process"></a>Avviare il processo
1. Aprire il [portale di Azure](https://portal.azure.com), nella barra di ricerca immettere **Synapse** senza premere INVIO.
1. Nei risultati della ricerca, in **Servizi**, selezionare **Azure Synapse Analytics**.
1. Selezionare **Aggiungi** per creare un'area di lavoro.

## <a name="basics-tab--project-details"></a>Scheda Informazioni di base > dettagli del progetto
Compilare i campi seguenti:

1. **Sottoscrizione:** selezionare qualsiasi sottoscrizione.
1. **Gruppo di risorse:** usare qualsiasi gruppo di risorse.
1. **Gruppo di risorse gestite:** lasciare vuoto questo campo.

## <a name="basics-tab--workspace-details"></a>Scheda Informazioni di base > dettagli dell'area di lavoro
Compilare i campi seguenti:

1. **Nome area di** lavoro: selezionare qualsiasi nome univoco globale. In questa esercitazione si userà **myworkspace**.
1. **Area:** selezionare l'area in cui sono state posizionate le applicazioni/servizi client (ad esempio, macchina virtuale di Azure, Power BI, Azure Analysis Service) e le risorse di archiviazione che contengono dati (ad esempio Archiviazione Di Azure Data Lake, Azure Cosmos DB archiviazione analitica).

> [!NOTE]
> Un'area di lavoro non condivisa con le applicazioni client o l'archiviazione può essere la causa principale di molti problemi di prestazioni. Se i dati o i client sono posizionati in più aree, è possibile creare aree di lavoro separate in aree diverse colocate con i dati e i client.

In **Selezionare Data Lake Storage Generazione 2**:

1. In **Nome account** selezionare Crea nuovo e assegnare al nuovo account di archiviazione il nome  **contosolake** o simile al nome che deve essere univoco.
1. In **Nome file system** selezionare Crea **nuovo** e assegnare un nome **agli utenti.** Verrà creato un contenitore di archiviazione denominato **users**. L'area di lavoro userà questo account di archiviazione come account di archiviazione "primario" per le tabelle Spark e i log delle applicazioni Spark.
1. Selezionare la casella "Assegna a se stesso il ruolo Collaboratore dati BLOB di archiviazione nell'account Data Lake Storage Gen2". 

## <a name="completing-the-process"></a>Completamento del processo
Selezionare **Rivedi e crea** > **Crea**. L'area di lavoro sarò pronta entro pochi minuti.

> [!NOTE]
> Per abilitare funzionalità dell'area di lavoro da un pool SQL dedicato (in precedenza SQL Data Warehouse), vedere [Come abilitare un'area di lavoro per il pool SQL dedicato (in precedenza SQL Data Warehouse)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Aprire Synapse Studio

Dopo aver creato l'area di lavoro di Azure Synapse, è possibile aprire Synapse Studio in due modi:

* Aprire l'area di lavoro Synapse nel [portale di Azure](https://portal.azure.com), nella sezione **Panoramica**  dell'area di lavoro Synapse selezionare Apri nella casella Apri Synapse Studio.
* Passare a `https://web.azuresynapse.net` e accedere alla propria area di lavoro.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Inserire i dati di esempio nell'account di archiviazione primario
In questa guida introduttiva verrà utilizzato un piccolo set di dati di esempio di 100.000 righe di dati NYX Taxi Cab. Per iniziare, posizionarlo nell'account di archiviazione primario creato per l'area di lavoro.

* Scaricare questo file nel computer: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* In Synapse Studio passare all'hub dati. 
* Selezionare **Collegato.**
* Sotto la categoria **Azure Data Lake Storage Gen2** verrà visualizzato un elemento con un nome come **myworkspace ( Primary - contosolake )**.
* Selezionare il contenitore **denominato users (Primary)**.
* Selezionare **Carica** e selezionare il `NYCTripSmall.parquet` file scaricato.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare usando un pool SQL serverless](get-started-analyze-sql-on-demand.md)
