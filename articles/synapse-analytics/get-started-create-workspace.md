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
ms.date: 12/31/2020
ms.openlocfilehash: 3a2636ec73d20f3011d8413c794e68ef41b1829c
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209186"
---
# <a name="creating-a-synapse-workspace"></a>Creazione di un'area di lavoro di Synapse

In questa esercitazione si apprenderà a creare un'area di lavoro di Synapse, un pool SQL dedicato e un pool di Apache Spark serverless. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario avere accesso a un gruppo di risorse per il quale si dispone del ruolo di **Proprietario**. Creare l'area di lavoro Synapse in questo gruppo di risorse.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Creare un'area di lavoro Synapse nel portale di Azure

1. Aprire il [portale di Azure](https://portal.azure.com) e nella parte superiore cercare **Synapse**.
1. Nei risultati della ricerca, in **Servizi**, selezionare **Azure Synapse Analytics**.
1. Selezionare **Aggiungi** per creare un'area di lavoro.
1. Nella scheda **nozioni di base** , in dettagli progetto, immettere **la sottoscrizione** preferita, il **gruppo di risorse**, l' **area**, quindi scegliere un nome per l'area di lavoro. In questa esercitazione si userà **myworkspace**.
1. Per **selezionare Data Lake storage generazione 2**, fare clic sul pulsante per **da sottoscrizione**.
1. Per **nome account**, fare clic su **Crea nuovo** e denominare il nuovo account di archiviazione **contosolake** o simile perché questo nome deve essere univoco.
1. Per **nome file System**, fare clic su **Crea nuovo** e denominarlo **utenti**. Verrà creato un contenitore di archiviazione denominato **Users**
1. L'area di lavoro userà questo account di archiviazione come account di archiviazione "primario" per le tabelle Spark e i log delle applicazioni Spark.
1. Selezionare "assegnare a me il ruolo Collaboratore dati BLOB di archiviazione nella casella account Data Lake Storage Gen2". 
1. Selezionare **Rivedi e crea** > **Crea**. L'area di lavoro sarò pronta entro pochi minuti.

> [!NOTE]
> Per abilitare funzionalità dell'area di lavoro da un pool SQL dedicato (in precedenza SQL Data Warehouse), vedere [Come abilitare un'area di lavoro per il pool SQL dedicato (in precedenza SQL Data Warehouse)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Aprire Synapse Studio

Dopo aver creato l'area di lavoro di Azure Synapse, è possibile aprire Synapse Studio in due modi:

* Aprire l'area di lavoro sinapsi nel [portale di Azure](https://portal.azure.com), nella sezione **Panoramica** dell'area di lavoro sinapsi selezionare **Apri** nella casella Apri sinapsi Studio.
* Passare a `https://web.azuresynapse.net` e accedere alla propria area di lavoro.


## <a name="the-built-in-serverless-sql-pool"></a>Pool SQL serverless predefinito

Ogni area di lavoro include un pool SQL serverless predefinito denominato **Predefinito**. Questo pool non può essere eliminato. I pool SQL serverless consentono di usare SQL senza la necessità di riservare capacità con i pool SQL dedicati. Diversamente dai pool SQL dedicati, la fatturazione per un pool SQL serverless è basata sulla quantità di dati analizzati per eseguire la query e non sulla capacità allocata al pool.


## <a name="create-a-dedicated-sql-pool"></a>Creare un pool SQL dedicato

1. Nel riquadro sinistro di Synapse Studio selezionare **Gestisci** > **Pool SQL**.
1. Selezionare **Nuovo**
1. Per **Nome del pool SQL**, selezionare **SQLPOOL1**
1. Per **Livello di prestazioni**, scegliere **DW100C**
1. Selezionare **Rivedi e crea** > **Crea**. Il pool SQL dedicato sarà pronto in pochi minuti. Il pool SQL dedicato è associato a un database del pool SQL dedicato anche questo denominato **SQLPOOL1**.

Un pool SQL dedicato utilizza risorse fatturabili finché è attivo. È possibile sospendere il pool in un secondo momento per ridurre i costi.

> [!NOTE] 
> Quando si crea un nuovo pool SQL dedicato (in precedenza SQL Data Warehouse) nell'area di lavoro, verrà aperta la pagina per effettuarne il provisioning. Il provisioning verrà effettuato nel server SQL logico.


## <a name="create-a-serverless-apache-spark-pool"></a>Creare un pool di Apache Spark serverless

1. Nel riquadro sinistro di Synapse Studio selezionare **Gestisci** > **Pool di Apache Spark**.
1. Selezionare **Nuovo** 
1. Per **Nome del pool di Apache Spark**, immettere **Spark1**.
1. Per **Dimensioni del nodo**, immettere **Piccole**.
1. Per **Numero di nodi**, impostare il valore minimo su 3 e il valore massimo su 3
1. Selezionare **Rivedi e crea** > **Crea**. Il pool di Apache Spark è pronto in pochi secondi.

Il pool di Spark indica ad Azure Synapse il numero di risorse Spark da usare. Verranno addebitati i costi solo per le risorse usate. Quando si interrompe attivamente l'uso del pool, le risorse raggiungono automaticamente il timeout e vengono riciclate.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire analisi con un pool SQL dedicato](get-started-analyze-sql-pool.md)
