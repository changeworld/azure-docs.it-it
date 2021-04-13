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
ms.openlocfilehash: b22954edf4f3a5a935c470326aa43bd24ee2d708
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366063"
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

## <a name="basics-tab--workspace-details"></a>Scheda Informazioni di base >'area di lavoro
Compilare i campi seguenti:

1. **Nome area di** lavoro: selezionare un nome univoco globale. In questa esercitazione si userà **myworkspace**.
1. **Area:** selezionare qualsiasi area.

In **Selezionare Data Lake Storage Gen 2:**

1. In **Nome account** selezionare Crea nuovo e assegnare al nuovo account di archiviazione il nome  **contosolake** o un nome simile a , in quanto il nome deve essere univoco.
1. Per **Nome file system** selezionare Crea nuovo **e** assegnare agli utenti il nome **.** Verrà creato un contenitore di archiviazione denominato **users**. L'area di lavoro userà questo account di archiviazione come account di archiviazione "primario" per le tabelle Spark e i log delle applicazioni Spark.
1. Selezionare la casella "Assign me the Storage Blob Data Contributor role on the Data Lake Storage Gen2 account" (Assegna a se stesso il ruolo Collaboratore ai dati dei BLOB di archiviazione nell'account di archiviazione). 

## <a name="completing-the-process"></a>Completamento del processo
Selezionare **Rivedi e crea** > **Crea**. L'area di lavoro sarò pronta entro pochi minuti.

> [!NOTE]
> Per abilitare funzionalità dell'area di lavoro da un pool SQL dedicato (in precedenza SQL Data Warehouse), vedere [Come abilitare un'area di lavoro per il pool SQL dedicato (in precedenza SQL Data Warehouse)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Aprire Synapse Studio

Dopo aver creato l'area di lavoro di Azure Synapse, è possibile aprire Synapse Studio in due modi:

* Aprire l'area di lavoro di Synapse  [nel](https://portal.azure.com)portale di Azure , nella sezione  Panoramica dell'area di lavoro Synapse selezionare Apri nella casella Synapse Studio Apri.
* Passare a `https://web.azuresynapse.net` e accedere alla propria area di lavoro.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Inserire i dati di esempio nell'account di archiviazione primario
In questa guida introduttiva verrà utilizzato un piccolo set di dati di esempio di 100.000 righe di dati NYX Taxi Cab. Per iniziare, posizionarlo nell'account di archiviazione primario creato per l'area di lavoro.

* Scaricare questo file nel computer: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* In Synapse Studio passare all'hub dati. 
* Selezionare **Collegato.**
* Nella categoria **Azure Data Lake Storae Gen2** verrà visualizzato un elemento con un nome come **myworkspace ( Primary - contosolake ).**
* Selezionare il contenitore denominato **users (Primary)**.
* Selezionare **Carica** e selezionare il `NYCTripSmall.parquet` file scaricato.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare usando un pool SQL serverless](get-started-analyze-sql-on-demand.md)
