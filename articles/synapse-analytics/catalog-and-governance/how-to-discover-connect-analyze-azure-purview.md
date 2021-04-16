---
title: Individuare, connettere ed esplorare i dati in Synapse con Azure Purview
description: Guida su come individuare i dati, connetterli ed esplorarli in Synapse
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: f98507fa72f4503700bf39393063dd1ecc650e91
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567894"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Individuare, connettere ed esplorare i dati in Synapse con Azure Purview 

In questo documento si apprenderà il tipo di interazioni che è possibile eseguire durante la registrazione di un account Azure Purview in Synapse. 

## <a name="prerequisites"></a>Prerequisiti 

- [Account di Azure Purview](../../purview/create-catalog-portal.md) 
- [Area di lavoro di Synapse](../quickstart-create-workspace.md) 
- [Connettere un account Azure Purview a Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Uso di Azure Purview in Synapse 

L'uso di Azure Purview in Synapse richiede l'accesso a tale account Purview. Synapse passa attraverso l'autorizzazione Purview. Ad esempio, se si dispone di un ruolo di autorizzazione non autorizzato, sarà possibile modificare i metadati analizzati da Azure Purview. 

### <a name="data-discovery-search-datasets"></a>Individuazione dei dati: set di dati di ricerca 

Per individuare i dati registrati e analizzati da Azure Purview, è possibile usare la barra di ricerca nella parte superiore centrale dell'area di lavoro synapse. Assicurarsi di selezionare Azure Purview per cercare tutti i dati dell'organizzazione. 

[![Cercare asset di Azure Purview](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azioni di Azure Purview 

Ecco un elenco delle funzionalità di Azure Purview disponibili in Synapse: 
- **Panoramica** dei metadati 
- Visualizzare e modificare **lo schema** dei metadati con classificazioni, termini di glossario, tipi di dati e descrizioni 
- Visualizzare **la derivazione** per comprendere le dipendenze e analizzare l'impatto. Per altre informazioni, vedere [derivazione](../../purview/catalog-lineage-user-guide.md)
- Visualizzare e modificare **i contatti** per sapere chi è un proprietario o un esperto su un set di dati 
- **Correlati** alla comprensione delle dipendenze gerarchiche di un set di dati specifico. Questa esperienza è utile per esplorare la gerarchia dei dati.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Azioni che è possibile eseguire sui set di dati con le risorse synapse 

### <a name="connect-data-to-synapse"></a>Connettere i dati a Synapse 

- È possibile creare un **nuovo servizio collegato** a Synapse. Tale azione sarà necessaria per copiare i dati in Synapse o averli nell'hub dati (per le origini dati supportate come ADLSg2) 
- Per oggetti come file, cartelle o tabelle,  è possibile creare direttamente un nuovo set di dati di integrazione e sfruttare un servizio collegato esistente, se già creato 

Non è ancora possibile dedurre se è presente un servizio collegato o un set di dati di integrazione esistente. 

###  <a name="develop-in-synapse"></a>Sviluppare in Synapse 

È possibile eseguire tre azioni: **Nuovo script SQL,** **Nuovo notebook** e Nuovo **Flusso di dati**. 

Con **Nuovo script SQL**, a seconda del tipo di supporto, è possibile: 
- Visualizzare le prime 100 righe per comprendere la forma dei dati. 
- Creare una tabella esterna da Synapse SQL database 
- Caricare i dati in un database Synapse SQL dati 
 
Con **Nuovo notebook** è possibile: 
- Caricare dati in un dataframe Spark 
- Creare una tabella Spark (se si fa questo in formato Parquet, viene creata anche una tabella del pool SQL serverless). 
 
Con **Nuovo flusso di dati** è possibile creare un set di dati di integrazione che può essere usato come origine in una pipeline del flusso di dati. Il flusso di dati è una funzionalità di sviluppo senza codice per eseguire la trasformazione dei dati. Per altre informazioni [sull'uso del flusso di dati in Synapse](../quickstart-data-flow.md).

##  <a name="nextsteps"></a>Passaggi successivi 

- [Registrare e analizzare gli asset di Azure Synapse in Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)
- [Come cercare dati in Azure Purview Data Catalog](../../purview/how-to-search-catalog.md)
