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
ms.openlocfilehash: 22631cfd872ed226fc78a97d38c423fbc300f78e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304715"
---
# <a name="integrate-with-pipelines"></a>Integrare con le pipeline

In questa esercitazione si apprenderà come integrare pipeline e attività usando Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Creare una pipeline e aggiungere un'attività del notebook



1. In Synapse Studio assare all'hub **Integrate** (Integrazione).
1. Selezionare **+**  > **Pipeline** per creare una nuova pipeline. Fare clic sul nuovo oggetto pipeline per aprire Progettazione pipeline.
1. In **attività** espandere la cartella **sinapsi** e trascinare un oggetto **notebook** nella finestra di progettazione.
1. Selezionare la scheda **Impostazioni** delle proprietà dell'attività notebook. Usare l'elenco a discesa per selezionare un notebook dall'area di lavoro sinapsi corrente.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Pianificare l'esecuzione della pipeline ogni ora

1. Nella pipeline selezionare **Aggiungi trigger** > **Nuovo/Modifica**.
1. In **Trigger** selezionare **Nuovo** e impostare **Ricorrenza** su "Ogni ora".
1. Selezionare **OK**. 
1. Selezionare **Pubblica tutti**. 


## <a name="monitor-pipeline"></a>Monitorare la pipeline

1. Dopo la pubblicazione della pipeline, per fare in modo che la pipeline venga eseguita immediatamente senza attendere l'ora successiva, selezionare **Aggiungi**  >  **trigger trigger**.
1. In sinapsi Studio passare all'hub **monitoraggio** e selezionare **esecuzioni pipeline** per monitorare lo stato di esecuzione della pipeline.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Visualizzare i dati con Power BI](get-started-visualize-power-bi.md)
