---
title: Individuare, connettere ed esplorare i dati in sinapsi usando Azure
description: Guida su come individuare i dati, connetterli ed esplorarli in sinapsi
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: 79090bfbf08cde3f18b1ca734b8af22d16e7e0ea
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628663"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Individuare, connettere ed esplorare i dati in sinapsi usando Azure 

In questo documento si apprenderà il tipo di interazioni che è possibile eseguire durante la registrazione di un account Azure di competenza in sinapsi. 

## <a name="prerequisites"></a>Prerequisiti 

- [Account di Azure Purview](../../purview/create-catalog-portal.md) 
- [Area di lavoro di Synapse](../quickstart-create-workspace.md) 
- [Connettere un account Azure per la competenza a sinapsi](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Uso della competenza Azure in sinapsi 

Per usare la competenza Azure in sinapsi è necessario avere accesso a tale account di competenza. Sinapsi passa attraverso l'autorizzazione di competenza. Se, ad esempio, si dispone di un ruolo di autorizzazione del curatore, sarà possibile modificare i metadati analizzati da Azure competenza. 

### <a name="data-discovery-search-datasets"></a>Individuazione dati: Cerca set di dati 

Per individuare i dati registrati e analizzati da Azure, è possibile usare la barra di ricerca in alto al centro dell'area di lavoro sinapsi. Assicurarsi di selezionare competenza Azure per cercare tutti i dati dell'organizzazione. 

[![Cercare asset di Azure per competenza](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azioni di competenza di Azure 

Di seguito è riportato un elenco delle funzionalità di competenza di Azure disponibili in sinapsi: 
- **Panoramica** dei metadati 
- Visualizzare e modificare lo **schema** dei metadati con classificazioni, termini di glossario, tipi di dati e descrizioni 
- Visualizzare **la** derivazione per comprendere le dipendenze ed eseguire l'analisi dell'effetto. Per ulteriori informazioni su [, vedere](../../purview/catalog-lineage-user-guide.md) derivazione
- Visualizzare e modificare i **contatti** per conoscere chi è un proprietario o un esperto su un set di dati 
- **Correlate** per comprendere le dipendenze gerarchiche di un set di dati specifico. Questa esperienza è utile per esplorare la gerarchia dei dati.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Azioni che è possibile eseguire su set di impostazioni con risorse sinapsi 

### <a name="connect-data-to-synapse"></a>Connettere i dati alla sinapsi 

- È possibile creare un **nuovo servizio collegato** per la sinapsi. Tale azione sarà necessaria per copiare i dati in sinapsi o includerli nell'hub dati (per le origini dati supportate, ad esempio ADLSg2) 
- Per oggetti quali file, cartelle o tabelle, è possibile creare direttamente un **nuovo set di dati di integrazione** e utilizzare un servizio collegato esistente, se già creato 

Non è ancora possibile dedurre se è presente un servizio collegato esistente o un set di dati di integrazione. 

###  <a name="develop-in-synapse"></a>Sviluppare in sinapsi 

Sono disponibili tre azioni che è possibile eseguire: **nuovo script SQL**, **nuovo notebook** e **nuovo flusso di dati**. 

Con il **nuovo script SQL**, a seconda del tipo di supporto, è possibile: 
- Visualizzare le prime 100 righe per comprendere la forma dei dati. 
- Creare una tabella esterna dal database SQL sinapsi 
- Caricare i dati in un database SQL sinapsi 
 
Con il **nuovo notebook** è possibile: 
- Caricare i dati in un frame di dati Spark 
- Creare una tabella Spark (se si esegue questa operazione su un formato parquet, viene creata anche una tabella del pool SQL senza server). 
 
Con il **nuovo flusso di dati**, è possibile creare un set di dati di integrazione che può essere utilizzato come origine in una pipeline del flusso di dati. Il flusso di dati è una funzionalità di sviluppo senza codice per eseguire la trasformazione dei dati. Per altre informazioni sull' [uso del flusso di dati in sinapsi](../quickstart-data-flow.md).

##  <a name="nextsteps"></a>Passaggi successivi 

- [Registrare e analizzare gli asset di Azure Synapse in Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)
- [Come eseguire ricerche nei dati in Azure Data Catalog](../../purview/how-to-search-catalog.md)
