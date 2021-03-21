---
title: Connettere una data factory ad Azure Purview
description: Informazioni su come connettere una Data Factory ad Azure
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 44f093f96d0f4653a6fcca94aaa97264c93e3c7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727941"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Connetti Data Factory ad Azure (anteprima)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra come connettere Data Factory ad Azure e come segnalare la derivazione dei dati di Azure Data Factory attività copia dati, flusso di dati ed esecuzione pacchetto SSIS.


## <a name="connect-data-factory-to-azure-purview"></a>Connetti Data Factory ad Azure competenza
Azure competenza è un nuovo servizio cloud per l'uso da parte degli utenti di dati per la gestione centralizzata della governance dei dati nei rispettivi ambienti cloud e locali. È possibile connettere la Data Factory ad Azure e la connessione consente di usare la competenza di Azure per l'acquisizione dei dati di derivazione di copia, flusso di dati ed esecuzione di un pacchetto SSIS. Sono disponibili due modi per connettersi data factory ad Azure competenza:
### <a name="register-azure-purview-account-to-data-factory"></a>Registrare l'account Azure per la competenza Data Factory
1. Nel portale di Azure Data Factory, passare a **Gestisci**  ->  **competenza Azure**. Selezionare **Connect to a Purview account** (Connetti a un account Purview). 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Screenshot per la registrazione di un account di competenza.":::
2. È possibile scegliere **Dalla sottoscrizione di Azure** o **Immetti manualmente**. Con l'opzione **Dalla sottoscrizione di Azure** è possibile selezionare l'account a cui si ha accesso. 
3. Una volta stabilita la connessione, dovrebbe essere possibile visualizzare il nome dell'account di competenza nell' **account di competenza** della scheda. 
4. È possibile usare la barra di ricerca in alto al centro del portale di Azure Data Factory per cercare i dati. 

Se viene visualizzato un messaggio di avviso nel portale di Azure Data Factory dopo aver registrato un account Azure per la Data Factory, attenersi alla procedura seguente per risolvere il problema:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Screenshot per l'avviso relativo alla registrazione di un account di competenza.":::

1. Passare a portale di Azure e trovare il data factory. Scegliere la sezione "Tags" e verificare se è presente un tag denominato **catalogUri**. In caso contrario, disconnettere e riconnettere l'account Azure per le competenze nel portale di ADF.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Screenshot per i tag di registrazione di un account di competenza.":::

2. Controllare se l'autorizzazione viene concessa per la registrazione di un account di Azure per la Data Factory. Scopri [come connettere Azure Data Factory e Azure](../purview/how-to-link-azure-data-factory.md#create-new-data-factory-connection) .

### <a name="register-data-factory-in-azure-purview"></a>Registra Data Factory in Azure
Per informazioni su come registrare Data Factory in Azure, vedere [come connettersi Azure Data Factory e Azure](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Segnala i dati di derivazione ad Azure competenza
Quando i clienti eseguono la copia, il flusso di dati o l'attività Esegui pacchetto SSIS in Azure Data Factory, i clienti possono ottenere la relazione di dipendenza e ottenere una panoramica di alto livello dell'intero processo del flusso di lavoro tra origini dati e destinazione.
Per informazioni su come raccogliere la derivazione da Azure Data Factory, vedere [Data Factory](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)derivazione.

## <a name="next-steps"></a>Passaggi successivi
[Guida dell'utente di derivazione del catalogo](../purview/catalog-lineage-user-guide.md)

[Esercitazione: effettuare il push dei dati di derivazione Data Factory in Azure](turorial-push-lineage-to-purview.md)