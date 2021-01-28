---
title: Eseguire il push dei dati di derivazione di Data Factory in Azure Purview
description: Informazioni su come effettuare il push dei dati di derivazione di Data Factory in Azure
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: e87a9d677fee94d410099db1da80a56b5539048c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935468"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Eseguire il push dei dati di derivazione Data Factory in Azure (anteprima)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa esercitazione si userà l'interfaccia utente Data Factory per creare una pipeline che esegue le attività e segnala i dati di derivazione all'account Azure. Sarà quindi possibile visualizzare tutte le informazioni di derivazione nell'account Azure.

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Azure Data Factory**. Se non si dispone di un Azure Data Factory, vedere [creare una Azure Data Factory](./quickstart-create-data-factory-portal.md).
* **Account Azure**. L'account di competenza acquisisce tutti i dati di derivazione generati da data factory. Se non si dispone di un account Azure, vedere [creare un ambito di competenza Azure](../purview/create-catalog-portal.md).


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Eseguire attività Data Factory e inviare dati di derivazione ad Azure.
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>Passaggio 1: connettere Data Factory all'account di competenza
Accedere al proprio account di competenza nel portale di competenza, andare al **centro di gestione**. Scegliere **Data Factory** in **connessioni esterne** e fare clic sul pulsante **nuovo** per creare una connessione a una nuova data factory. 
[![Screenshot per la creazione di una connessione tra Data Factory e l'account ](./media/data-factory-purview/connect-adf-to-purview.png) di competenza ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

Nella pagina popup è possibile scegliere la Data Factory che si vuole connettere a questo account di competenza. 
![Screenshot per una nuova connessione](./media/data-factory-purview/new-adf-purview-connection.png)

È possibile controllare lo stato dopo la creazione della connessione. **Connected** significa che la connessione tra data factory e questa competenza è connessa correttamente. 
> [!NOTE]
> È necessario assegnare uno dei ruoli seguenti nell'account di competenza e Data Factory ruolo **collaboratore** per creare la connessione tra data factory e Azure.
> - Proprietario
> - Amministratore accessi utente

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>Passaggio 2: eseguire le attività di copia e flusso di attività in Data Factory
È possibile creare pipeline, attività di copia e attività del flusso di attività in Data Factory. Non è necessaria alcuna configurazione aggiuntiva per l'acquisizione dei dati di derivazione. I dati di derivazione verranno acquisiti automaticamente durante l'esecuzione delle attività.
![Screenshot dell'attività di copia e flusso di dati ](./media/data-factory-purview/adf-activities-for-lineage.png) se non si sa come creare attività di copia e flusso di dati, vedere [copiare dati da un archivio BLOB di Azure a un database nel database SQL di azure usando Azure Data Factory](./tutorial-copy-data-portal.md) e [trasformare i dati usando il mapping dei flussi di dati](./tutorial-data-flow.md).

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>Passaggio 3: eseguire le attività di esecuzione del pacchetto SSIS in Data Factory
È possibile creare pipeline ed eseguire attività del pacchetto SSIS in Data Factory. Non è necessaria alcuna configurazione aggiuntiva per l'acquisizione dei dati di derivazione. I dati di derivazione verranno acquisiti automaticamente durante l'esecuzione delle attività.
![Screenshot dell'attività Esegui pacchetto SSIS](./media/data-factory-purview/ssis-activities-for-lineage.png)

Se non si sa come creare attività Esegui pacchetto SSIS, vedere [eseguire pacchetti SSIS in Azure](./tutorial-deploy-ssis-packages-azure.md).

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Passaggio 4: visualizzare le informazioni di derivazione nell'account di competenza
Tornare all'account di competenza. Nella home page selezionare **Sfoglia asset**. Scegliere l'asset desiderato, quindi fare clic sulla scheda derivazione. Vengono visualizzate tutte le informazioni di derivazione.
[![Screenshot dell'account ](./media/data-factory-purview/view-dataset.png) di competenza ](./media/data-factory-purview/view-dataset.png#lightbox)

È possibile visualizzare i dati di derivazione per l'attività di copia.
[![Screenshot della derivazione della copia ](./media/data-factory-purview/copy-lineage.png)](./media/data-factory-purview/copy-lineage.png#lightbox)

È anche possibile visualizzare i dati di derivazione per l'attività flusso di dati.
[![Screenshot della derivazione del flusso di flussi ](./media/data-factory-purview/dataflow-lineage.png)](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> Per la derivazione dell'attività del flusso di dati, è supportata solo l'origine e il sink. La derivazione per la trasformazione del flusso di tempo non è ancora supportata.

È anche possibile visualizzare i dati di derivazione per l'attività Esegui pacchetto SSIS.
[![Screenshot della derivazione SSIS ](./media/data-factory-purview/ssis-lineage.png)](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> Per la derivazione dell'attività Esegui pacchetto SSIS, è supportata solo l'origine e la destinazione. La derivazione per la trasformazione non è ancora supportata.

## <a name="next-steps"></a>Passaggi successivi
[Guida dell'utente di derivazione del catalogo](../purview/catalog-lineage-user-guide.md)

[Connetti Data Factory ad Azure competenza](connect-data-factory-to-azure-purview.md)