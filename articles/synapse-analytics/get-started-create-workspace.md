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
ms.openlocfilehash: f186acbe030dcbb0c2bad22586a8b2a5d1aa520d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259796"
---
# <a name="creating-a-synapse-workspace"></a>Creazione di un'area di lavoro di Synapse

In questa esercitazione si apprenderà a creare un'area di lavoro di Synapse, un pool SQL dedicato e un pool di Apache Spark serverless. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario avere accesso a un gruppo di risorse per il quale si dispone del ruolo di **Proprietario**. Creare l'area di lavoro Synapse in questo gruppo di risorse.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Creare un'area di lavoro Synapse nel portale di Azure

### <a name="start-the-process"></a>Avviare il processo
1. Aprire il [portale di Azure](https://portal.azure.com)nella barra di ricerca immettere **sinapsi** senza premere INVIO.
1. Nei risultati della ricerca, in **Servizi**, selezionare **Azure Synapse Analytics**.
1. Selezionare **Aggiungi** per creare un'area di lavoro.

## <a name="basics-tab--project-details"></a>Scheda nozioni di base > dettagli progetto
Compilare i campi seguenti:

1. **Sottoscrizione** : selezionare una sottoscrizione.
1. **Gruppo di risorse** : usare qualsiasi gruppo di risorse.
1. **Gruppo di risorse gestite** : lasciare vuoto questo campo.

## <a name="basics-tab--workspace-details"></a>Scheda nozioni di base > dettagli dell'area di lavoro
Compilare i campi seguenti:

1. **Nome area di lavoro** : selezionare un nome univoco globale. In questa esercitazione si userà **myworkspace**.
1. **Area** : selezionare qualsiasi area.

In **seleziona Data Lake storage generazione 2**:

1. Per **nome account**, fare clic su **Crea nuovo** e denominare il nuovo account di archiviazione **contosolake** o simile perché questo nome deve essere univoco.
1. Per **nome file System**, fare clic su **Crea nuovo** e denominarlo **utenti**. Verrà creato un contenitore di archiviazione denominato **Users**. L'area di lavoro userà questo account di archiviazione come account di archiviazione "primario" per le tabelle Spark e i log delle applicazioni Spark.
1. Selezionare "assegnare a me il ruolo Collaboratore dati BLOB di archiviazione nella casella account Data Lake Storage Gen2". 

## <a name="completing-the-process"></a>Completamento del processo
Selezionare **Rivedi e crea** > **Crea**. L'area di lavoro sarò pronta entro pochi minuti.

> [!NOTE]
> Per abilitare funzionalità dell'area di lavoro da un pool SQL dedicato (in precedenza SQL Data Warehouse), vedere [Come abilitare un'area di lavoro per il pool SQL dedicato (in precedenza SQL Data Warehouse)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Aprire Synapse Studio

Dopo aver creato l'area di lavoro di Azure Synapse, è possibile aprire Synapse Studio in due modi:

* Aprire l'area di lavoro sinapsi nel [portale di Azure](https://portal.azure.com), nella sezione **Panoramica** dell'area di lavoro sinapsi selezionare **Apri** nella casella Apri sinapsi Studio.
* Passare a `https://web.azuresynapse.net` e accedere alla propria area di lavoro.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Inserire i dati di esempio nell'account di archiviazione primario
Per molti esempi in questa Guida introduttiva verrà usato un set di dati di esempio di una piccola riga di 100.000 righe di dati CAB CAB di NYX. Per iniziare, inserire l'account nell'account di archiviazione primario creato per l'area di lavoro.

* Scaricare questo file nel computer: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* In sinapsi Studio passare all'hub dati. 
* Fare clic su **collegato**.
* Nella categoria **Azure Data Lake storae Gen2** verrà visualizzato un elemento con un nome come area di **lavoro (Primary-contosolake)**
* Fare clic sul contenitore denominato **Users (primario)**
* Fare clic su **carica** e selezionare il `NYCTripSmall.parquet` file scaricato

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizza usando un pool SQL senza server](get-started-analyze-sql-on-demand.md)
