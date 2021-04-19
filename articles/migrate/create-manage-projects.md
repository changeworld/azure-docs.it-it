---
title: Creare e gestire progetti
description: Trovare, creare, gestire ed eliminare progetti in Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0c9102f8ca724e431bb478945c5f4ba0369643d6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714840"
---
# <a name="create-and-manage-projects"></a>Creare e gestire progetti

Questo articolo descrive come creare, gestire ed eliminare [progetti](migrate-services-overview.md). 

Il Azure Migrate classico verrà ritirato a febbraio 2024. Dopo febbraio 2024, la versione classica di Azure Migrate non sarà più supportata e i metadati di inventario nel progetto classico verranno eliminati. Se si usano progetti classici, eliminarli e seguire la procedura per creare un nuovo progetto. Non è possibile aggiornare i progetti o i componenti classici al Azure Migrate. Visualizzare [le domande](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) frequenti prima di avviare il processo di creazione.

Il progetto viene usato per archiviare i metadati di individuazione, valutazione e migrazione raccolti dall'ambiente che si sta valutando o migrando. In un progetto è possibile tenere traccia degli asset individuati, creare valutazioni e orchestrare le migrazioni ad Azure.  

## <a name="verify-permissions"></a>Verificare le autorizzazioni

Verificare di avere le autorizzazioni corrette per creare un progetto:

1. Nel portale di Azure aprire la sottoscrizione pertinente e selezionare **Controllo di accesso (IAM).**
2. In **Controlla accesso** trovare l'account pertinente e selezionarlo per visualizzare le autorizzazioni. È necessario avere le autorizzazioni di *Collaboratore* o *Proprietario*. 


## <a name="create-a-project-for-the-first-time"></a>Creare un progetto per la prima volta

Configurare un nuovo progetto in una sottoscrizione di Azure.

1. Nel portale di Azure cercare *Azure Migrate*.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Panoramica** selezionare **Valutare ed eseguire la migrazione dei server** .

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Opzione in Panoramica per valutare ed eseguire la migrazione dei server":::

4. In **Server** selezionare **Crea progetto.**

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="Pulsante per avviare la creazione del progetto":::

5. In **Crea progetto** selezionare la sottoscrizione di Azure e il gruppo di risorse. Creare un gruppo di risorse, se non è presente.
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare.
    - L'area geografica viene usata solo per archiviare i metadati raccolti dai server locali. È possibile selezionare qualsiasi area di destinazione per la migrazione. 
    - Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government). 


    > [!Note]
    > Usare la **sezione Configurazione** avanzata per creare un progetto Azure Migrate con connettività dell'endpoint privato. [Scopri di più](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity) 

7. Selezionare **Crea**.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Pagina per l'input delle impostazioni del progetto":::


Attendere alcuni minuti per la distribuzione del progetto.

## <a name="create-a-project-in-a-specific-region"></a>Creare un progetto in un'area specifica

Nel portale è possibile selezionare l'area geografica in cui si vuole creare il progetto. Se si vuole creare il progetto all'interno di un'area di Azure specifica, usare il comando API seguente per creare il progetto.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Creare progetti aggiuntivi

Se si dispone già di un progetto e si vuole creare un progetto aggiuntivo, eseguire le operazioni seguenti:  

1. Nel portale [pubblico di Azure o](https://portal.azure.com) [Azure per enti pubblici](https://portal.azure.us)cercare **Azure Migrate**.
2. Nel dashboard Azure Migrate server > **selezionare** **Modifica** nell'angolo superiore destro.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Modifica progetto":::

3. Per creare un nuovo progetto, selezionare **fare clic qui.**


## <a name="find-a-project"></a>Trovare un progetto

Trovare un progetto come segue:

1. Nel [portale di Azure](https://portal.azure.com)cercare Azure Migrate *.*
2. Nel dashboard Azure Migrate dashboard > **server** selezionare **Modifica** nell'angolo superiore destro.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Passare a un progetto esistente":::

3. Selezionare la sottoscrizione e il progetto appropriati.


### <a name="find-a-classic-project"></a>Trovare un progetto classico

Se il progetto è stato creato nella [versione precedente](migrate-services-overview.md#azure-migrate-versions) di Azure Migrate, trovarlo come segue:

1. Nel [portale di Azure](https://portal.azure.com)cercare *Azure Migrate*.
2. Nel dashboard Azure Migrate, se è stato creato un progetto nella versione precedente, viene visualizzato un banner che fa riferimento ai progetti meno recenti. Selezionare il banner.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Accedere ai progetti esistenti":::

3. Esaminare l'elenco dei progetti vecchi.


## <a name="delete-a-project"></a>Eliminare un progetto

La procedura di eliminazione è la seguente:

1. Aprire il gruppo di risorse di Azure in cui è stato creato il progetto.
2. Nella pagina del gruppo di risorse selezionare **Mostra tipi nascosti.**
3. Selezionare il progetto da eliminare e le risorse associate.
    - Il tipo di risorsa è **Microsoft.Migrate/migrateprojects.**
    - Se il gruppo di risorse viene usato esclusivamente dal progetto, è possibile eliminare l'intero gruppo di risorse.

Tenere presente quanto segue:

- Quando si elimina , vengono eliminati sia il progetto che i metadati relativi ai server individuati.
- Se si usa la versione precedente di Azure Migrate, aprire il gruppo di risorse di Azure in cui è stato creato il progetto. Selezionare il progetto da eliminare (il tipo di risorsa è **Progetto di migrazione).**
- Se si usa l'analisi delle dipendenze con un'area di lavoro Azure Log Analytics:
    - Se è stata collegata un'area di lavoro Log Analytics a Server Assessment, l'area di lavoro non viene eliminata automaticamente. La stessa area di lavoro Log Analytics può essere usata per più scenari.
    - Se si vuole eliminare l'area di lavoro Log Analytics, eseguire questa operazione manualmente.
- L'eliminazione del progetto è irreversibile. Gli oggetti eliminati non possono essere ripristinati.

### <a name="delete-a-workspace-manually"></a>Eliminare manualmente un'area di lavoro

1. Passare all'area di lavoro Log Analytics collegata al progetto.

    - Se il progetto non è stato eliminato, è possibile trovare il collegamento all'area di lavoro in **Essentials**  >  **Server Assessment**.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="Area di lavoro di Log Analytics":::
       
    - Se il progetto è già stato eliminato, selezionare **Gruppi** di risorse nel riquadro sinistro del portale di Azure e trovare l'area di lavoro.
       
2. [Seguire le istruzioni per](../azure-monitor/logs/delete-workspace.md) eliminare l'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Aggiungere [strumenti di](how-to-assess.md) valutazione [o](how-to-migrate.md) migrazione ai progetti.