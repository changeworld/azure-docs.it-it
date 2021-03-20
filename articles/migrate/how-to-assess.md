---
title: Aggiungere strumenti di valutazione in Azure Migrate
description: Informazioni su come aggiungere strumenti di valutazione in Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 9a4ed5542945b8d281ec750d9bbd3a8f444e44d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752261"
---
# <a name="add-assessment-tools"></a>Aggiungere strumenti di valutazione

Questo articolo descrive come aggiungere strumenti di valutazione in [Azure migrate](./migrate-services-overview.md). 

- Se si vuole aggiungere uno strumento di valutazione e non si dispone ancora di un progetto di Azure Migrate, seguire questo [articolo](create-manage-projects.md).
- Se è stato aggiunto uno strumento ISV, o Mover, per la valutazione, [seguire la procedura](prepare-isv-movere.md)per prepararsi a usare lo strumento.

## <a name="select-an-assessment-scenario"></a>Selezionare uno scenario di valutazione

1. Nel progetto di Azure Migrate fare clic su **Panoramica**.
2. Selezionare lo scenario di valutazione:

    - Per individuare e valutare i computer e i carichi di lavoro per la migrazione ad Azure, selezionare **valuta ed Esegui la migrazione dei server**.
    - Per valutare i database SQL Server locali, selezionare **valuta ed Esegui la migrazione dei database**.
    - Per valutare o migrare app Web locali, selezionare **Esplora altre**  >  **app Web**.
    - Per valutare l'infrastruttura di desktop virtuale, selezionare **Esplora altre**  >  **infrastrutture desktop virtuali**.

    ![Opzioni per la selezione di uno scenario di valutazione](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selezionare uno strumento di valutazione del server 


1. Aggiungere uno strumento:

    - Se è stato creato un progetto di Azure Migrate utilizzando l'opzione **valuta e Esegui migrazione server** nel portale, lo strumento Azure migrate server assessment viene aggiunto automaticamente al progetto. Per aggiungere altri strumenti di valutazione, in **Server**, accanto a **strumenti di valutazione**, selezionare **Aggiungi altri strumenti**.
    
         ![Pulsante per aggiungere ulteriori strumenti di valutazione](./media/how-to-assess/add-assessment-tool.png)

    - Se è stato creato un progetto con un'opzione diversa e non sono ancora disponibili strumenti di valutazione, in **Server**  >  **Assessment Tools** selezionare **fare clic qui**.

        ![Pulsante per aggiungere il primo strumento di valutazione](./media/how-to-assess/no-assessment-tool.png)

2. In **Azure migrate**  >  **Aggiungi strumenti** selezionare gli strumenti che si desidera aggiungere. Quindi selezionare **Aggiungi strumento**.

    ![Selezionare gli strumenti di valutazione nell'elenco](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Selezionare uno strumento di valutazione del database

1. Aggiungere uno strumento:

    - Se è stato creato un progetto Azure Migrate usando l'opzione **valuta e migra database** nel portale, lo strumento di valutazione del database viene aggiunto automaticamente al progetto. Per aggiungere ulteriori strumenti di valutazione, in **database**, accanto a **strumenti di valutazione**, selezionare **Aggiungi altri strumenti**.

    - Se è stato creato un progetto con un'opzione diversa e non sono ancora disponibili strumenti di **valutazione del database**, in strumenti di valutazione dei database  >  selezionare **fare clic qui**.

2. In **Azure migrate**  >  **Aggiungi strumenti** selezionare gli strumenti che si desidera aggiungere. Quindi selezionare **Aggiungi strumento**.

    ![Seleziona Strumenti di valutazione del database dall'elenco](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Selezionare uno strumento di valutazione app Web

Se è stato creato un progetto Azure migrate usando l'opzione **Esplora più**  >  **app** Web nel portale, lo strumento di valutazione dell'app Web viene aggiunto automaticamente al progetto. 


1. Se lo strumento di valutazione dell'app Web non è incluso nel progetto, in strumenti di valutazione di app **Web**  >  selezionare **fare clic qui**.
    
    ![Aggiungere strumenti di valutazione app Web](./media/how-to-assess/no-web-app-assessment-tool.png)


2. In **Azure migrate**  >  **Aggiungi strumenti** selezionare lo strumento di valutazione dell'app Web. Quindi selezionare **Aggiungi strumento**.

    ![Selezionare lo strumento di migrazione del database dall'elenco](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Passaggi successivi

Individuare i computer locali per la valutazione usando Azure Migrate server Assessment Tool per macchine virtuali [VMware](./tutorial-discover-vmware.md) , [Hyper-V](./tutorial-discover-hyper-v.md)o [server fisici](./tutorial-discover-physical.md)