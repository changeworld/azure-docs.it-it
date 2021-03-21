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
ms.openlocfilehash: ee8a192bcfe20c77e47ee9c89dfc3286a979e358
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722294"
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
1. Scheda **nozioni di base** , in **Dettagli progetto**, compilare i campi seguenti:
      1. **Sottoscrizione** : selezionare una sottoscrizione.
      2. **Gruppo di risorse** : usare qualsiasi gruppo di risorse.
      3. **Gruppo di risorse** : lasciare vuoto questo campo.


## <a name="basics-tab--workspace-details"></a>Scheda nozioni di base > dettagli dell'area di lavoro
1. Nella scheda informazioni di **base** , in **Dettagli area di lavoro**, compilare i campi seguenti:
      1. **Nome area di lavoro** : selezionare un nome univoco globale. In questa esercitazione si userà **myworkspace**.
      1. **Area** : selezionare qualsiasi area.
      1. **Selezionare Data Lake Storage generazione 2**
        1. Fare clic sul pulsante per **da sottoscrizione**.
        1. Per **nome account**, fare clic su **Crea nuovo** e denominare il nuovo account di archiviazione **contosolake** o simile perché questo nome deve essere univoco.
        1. Per **nome file System**, fare clic su **Crea nuovo** e denominarlo **utenti**. Verrà creato un contenitore di archiviazione denominato **Users**. L'area di lavoro userà questo account di archiviazione come account di archiviazione "primario" per le tabelle Spark e i log delle applicazioni Spark.
        1. Selezionare "assegnare a me il ruolo Collaboratore dati BLOB di archiviazione nella casella account Data Lake Storage Gen2". 

### <a name="completing-the-process"></a>Completamento del processo
5. Selezionare **Rivedi e crea** > **Crea**. L'area di lavoro sarò pronta entro pochi minuti.

> [!NOTE]
> Per abilitare funzionalità dell'area di lavoro da un pool SQL dedicato (in precedenza SQL Data Warehouse), vedere [Come abilitare un'area di lavoro per il pool SQL dedicato (in precedenza SQL Data Warehouse)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Aprire Synapse Studio

Dopo aver creato l'area di lavoro di Azure Synapse, è possibile aprire Synapse Studio in due modi:

* Aprire l'area di lavoro sinapsi nel [portale di Azure](https://portal.azure.com), nella sezione **Panoramica** dell'area di lavoro sinapsi selezionare **Apri** nella casella Apri sinapsi Studio.
* Passare a `https://web.azuresynapse.net` e accedere alla propria area di lavoro.











## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizza usando un pool SQL senza server](get-started-analyze-sql-on-demand.md)
