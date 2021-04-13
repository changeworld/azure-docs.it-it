---
title: "Esercitazione: Introduzione all'integrazione con le pipeline"
description: In questa esercitazione si apprenderà come integrare pipeline e attività usando Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 05c33db130bfa3fcc1a4f5d75935294fcc0ba1d7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365468"
---
# <a name="integrate-with-pipelines"></a>Integrare con le pipeline

In questa esercitazione si apprenderà come integrare pipeline e attività usando Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Creare una pipeline e aggiungere un'attività notebook



1. In Synapse Studio assare all'hub **Integrate** (Integrazione).
1. Selezionare **+**  > **Pipeline** per creare una nuova pipeline. Fare clic sul nuovo oggetto pipeline per aprire Progettazione pipeline.
1. In **Attività** espandere la **cartella Synapse** e trascinare un oggetto **Notebook** nella finestra di progettazione.
1. Selezionare la **scheda Impostazioni** delle proprietà dell'attività Notebook. Usare l'elenco a discesa per selezionare qualsiasi notebook dall'area di lavoro synapse corrente.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Pianificare l'esecuzione della pipeline ogni ora

1. Nella pipeline selezionare **Aggiungi trigger** > **Nuovo/Modifica**.
1. In **Trigger** selezionare **Nuovo** e impostare **Ricorrenza** su "Ogni ora".
1. Selezionare **OK**. 
1. Selezionare **Pubblica tutti**. 


## <a name="monitor-pipeline-execution"></a>Monitorare l'esecuzione della pipeline

1. Dopo aver pubblicato la pipeline, per eseguire la pipeline immediatamente, senza attendere l'ora successiva, selezionare **Aggiungi trigger**  >  **trigger ora**.
1. In Synapse Studio passare all'hub **Monitoraggio.**
1. Selezionare **Esecuzioni pipeline per** monitorare lo stato di avanzamento dell'esecuzione della pipeline.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Visualizzare i dati con Power BI](get-started-visualize-power-bi.md)
