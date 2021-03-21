---
title: Creare e gestire i progetto di Azure Migrate
description: Trovare, creare, gestire ed eliminare progetti in Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0656a9eabb692453331c5c4b229ce26f41ecfdf3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730202"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Creare e gestire i progetto di Azure Migrate

Questo articolo descrive come creare, gestire ed eliminare progetti [Azure migrate](migrate-services-overview.md) . 

Il Azure Migrate classico verrà ritirato a febbraio 2024. Dopo il 2024 febbraio, la versione classica di Azure Migrate non sarà più supportata e i metadati di inventario nel progetto classico verranno eliminati. Se si usano progetti di Azure Migrate classici, eliminare tali progetti e seguire i passaggi per creare un nuovo progetto Azure Migrate. Non è possibile aggiornare i componenti o i progetti di Azure Migrate classici al Azure Migrate. Visualizzare le [domande frequenti](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) prima di avviare il processo di creazione.

Il progetto Azure Migrate viene usato per archiviare i metadati di individuazione, valutazione e migrazione raccolti dall'ambiente da valutare o trasferire. In un progetto è possibile tenere traccia delle risorse individuate, creare valutazioni e orchestrare le migrazioni in Azure.  

## <a name="verify-permissions"></a>Verificare le autorizzazioni

Verificare di disporre delle autorizzazioni corrette per creare un progetto Azure Migrate:

1. Nella portale di Azure aprire la sottoscrizione pertinente e quindi selezionare **controllo di accesso (IAM)**.
2. In **Check Access** trovare l'account pertinente e selezionare it View Permissions. È necessario avere le autorizzazioni di *Collaboratore* o *Proprietario*. 


## <a name="create-a-project-for-the-first-time"></a>Creare un progetto per la prima volta

Configurare un nuovo progetto di Azure Migrate in una sottoscrizione di Azure.

1. Nella portale di Azure cercare *Azure migrate*.
2. In **Servizi** selezionare **Azure migrate**.
3. In **Panoramica** selezionare **Valutare ed eseguire la migrazione dei server** .

    ![Opzione in panoramica per la valutazione e la migrazione dei server](./media/create-manage-projects/assess-migrate-servers.png)

4. In **Server** selezionare **Crea progetto**.

    ![Pulsante per avviare la creazione del progetto](./media/create-manage-projects/create-project.png)

5. In **Crea progetto** selezionare la sottoscrizione e il gruppo di risorse di Azure. Creare un gruppo di risorse, se non è presente.
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare.
    - La geografia viene usata solo per archiviare i metadati raccolti da computer locali. È possibile selezionare qualsiasi area di destinazione per la migrazione. 
    - Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

8. Selezionare **Crea**.

   ![Pagina per immettere le impostazioni del progetto](./media/create-manage-projects/project-details.png)


Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate.

## <a name="create-a-project-in-a-specific-region"></a>Creare un progetto in un'area specifica

Nel portale è possibile selezionare l'area geografica in cui si vuole creare il progetto. Se si vuole creare il progetto all'interno di un'area di Azure specifica, usare il comando API seguente per creare il progetto.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Creazione di progetti aggiuntivi

Se si dispone già di un progetto Azure Migrate e si desidera creare un progetto aggiuntivo, procedere come segue:  

1. Nel [portale pubblico di Azure](https://portal.azure.com) o in [Azure per enti](https://portal.azure.us)pubblici cercare **Azure migrate**.
2. Nel dashboard Azure Migrate > **Server** selezionare **Cambia** nell'angolo superiore destro.

   ![Modificare il progetto Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Per creare un nuovo progetto, selezionare **fare clic qui**.


## <a name="find-a-project"></a>Trovare un progetto

Trovare un progetto come indicato di seguito:

1. Nella [portale di Azure](https://portal.azure.com)cercare *Azure migrate*.
2. Nel dashboard Azure Migrate > **Server** selezionare **Cambia** nell'angolo superiore destro.

    ![Passa a un progetto di Azure Migrate esistente](./media/create-manage-projects/switch-project.png)

3. Selezionare la sottoscrizione appropriata e il progetto Azure Migrate.


### <a name="find-a-classic-azure-migrate-project"></a>Trovare un progetto di Azure Migrate classico

Se il progetto è stato creato nella [versione precedente](migrate-services-overview.md#azure-migrate-versions) di Azure migrate, trovarlo come segue:

1. Nella [portale di Azure](https://portal.azure.com)cercare *Azure migrate*.
2. Nel dashboard Azure Migrate, se è stato creato un progetto nella versione precedente, viene visualizzato un banner che fa riferimento a progetti precedenti. Selezionare il banner.

    ![Accedi a progetti esistenti](./media/create-manage-projects/access-existing-projects.png)

3. Esaminare l'elenco dei progetti precedenti.


## <a name="delete-a-project"></a>Eliminare un progetto

La procedura di eliminazione è la seguente:

1. Aprire il gruppo di risorse di Azure in cui è stato creato il progetto.
2. Nella pagina gruppo di risorse selezionare **Mostra tipi nascosti**.
3. Selezionare il progetto di migrazione che si desidera eliminare e le risorse associate.
    - Il tipo di risorsa è **Microsoft. migrate/migrateprojects**.
    - Se il gruppo di risorse viene usato esclusivamente dal progetto di Azure Migrate, è possibile eliminare l'intero gruppo di risorse.

Tenere presente quanto segue:

- Quando si elimina, vengono eliminati sia il progetto che i metadati relativi alle macchine virtuali individuate.
- Se si usa la versione precedente di Azure Migrate, aprire il gruppo di risorse di Azure in cui è stato creato il progetto. Selezionare il progetto di migrazione che si vuole eliminare (il tipo di risorsa è **progetto di migrazione**).
- Se si usa l'analisi delle dipendenze con un'area di lavoro di Azure Log Analytics:
    - Se è stata collegata un'area di lavoro Log Analytics allo strumento Server Assessment, l'area di lavoro non viene eliminata automaticamente. È possibile utilizzare la stessa area di lavoro Log Analytics per più scenari.
    - Se si desidera eliminare l'area di lavoro Log Analytics, procedere manualmente.
- L'eliminazione del progetto è irreversibile. Impossibile recuperare gli oggetti eliminati.

### <a name="delete-a-workspace-manually"></a>Eliminare un'area di lavoro manualmente

1. Passare all'area di lavoro Log Analytics collegata al progetto.

    - Se il progetto Azure migrate non è stato eliminato, è possibile trovare il collegamento all'area di lavoro in **Essentials**  >  **Server Assessment**.
       ![Area di lavoro ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Se il progetto Azure Migrate è già stato eliminato, selezionare **gruppi di risorse** nel riquadro sinistro della portale di Azure e individuare l'area di lavoro.
       
2. [Seguire le istruzioni](../azure-monitor/logs/delete-workspace.md) per eliminare l'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Aggiungere strumenti di [valutazione](how-to-assess.md) o [migrazione](how-to-migrate.md) ai progetti Azure migrate.