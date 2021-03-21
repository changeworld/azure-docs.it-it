---
title: Individuare ed esplorare i dati in ADF usando le competenze
description: Informazioni su come individuare, esplorare i dati in Azure Data Factory usando le competenze
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: 031e7ceee739ffd666ad367bd6778865a297a25d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386272"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Individuare ed esplorare i dati in ADF usando le competenze

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questo articolo si registrerà un account Azure di competenza per un Data Factory. Tale connessione consente di individuare le risorse di Azure per la competenza e interagire con esse tramite le funzionalità di ADF. 

In ADF è possibile eseguire le attività seguenti: 
- Usare la casella di ricerca nella parte superiore per trovare asset di Purview in base a parole chiave 
- Comprendere i dati in base a metadati, derivazione, annotazioni 
- Connettere i dati alla data factory con i servizi collegati o i set di dati 

## <a name="prerequisites"></a>Prerequisiti 
- [Account di Azure Purview](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Connettere un account Azure per la competenza a Data Factory](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Uso della competenza Azure in Data Factory 

L'uso della competenza Azure in Data Factory richiede l'accesso a tale account di competenza. Data Factory passa attraverso l'autorizzazione di ambito. Se, ad esempio, si dispone di un ruolo di autorizzazione del curatore, sarà possibile modificare i metadati analizzati da Azure competenza. 

### <a name="data-discovery-search-datasets"></a>Individuazione dati: Cerca set di dati 

Per individuare i dati registrati e analizzati da Azure, è possibile usare la barra di ricerca nella parte superiore centrale del portale di Data Factory. Assicurarsi di selezionare competenza Azure per cercare tutti i dati dell'organizzazione. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Screenshot per l'esecuzione su set di impostazioni.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Azioni che è possibile eseguire su set di impostazioni con risorse Data Factory 
È possibile creare direttamente il servizio collegato, il set di dati o il flusso di dati sui dati cercati da Azure.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Screenshot che illustra come creare direttamente un servizio collegato, un set di dati o un flusso di dati sui dati cercati da Azure.":::

##  <a name="nextsteps"></a>Passaggi successivi 

- [Registra e analizza Azure Data Factory asset in Azure](../purview/register-scan-azure-synapse-analytics.md)
- [Come eseguire ricerche nei dati in Azure Data Catalog](../purview/how-to-search-catalog.md)