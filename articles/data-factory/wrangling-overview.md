---
title: Dati in Azure Data Factory
description: Panoramica dei dati in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f922e7a2755a6e26a0d9f93f2668753e2f4dad5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98738170"
---
# <a name="what-is-data-wrangling"></a>Che cos'è data wrangling?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Le organizzazioni hanno la possibilità di esplorare i dati aziendali critici per la preparazione e la gestione dei dati allo scopo di fornire un'analisi accurata dei dati complessi che continuano a crescere ogni giorno. La preparazione dei dati è necessaria in modo che le organizzazioni possano usare i dati in vari processi aziendali e ridurre il tempo di valore.

Data Factory ti permette di preparare i dati senza codice su scala cloud in modo iterativo usando Power Query. Data Factory si integra con [Power query online](/power-query/) e rende disponibili le funzioni Power query M come attività della pipeline.

Data Factory converte i M generati dall'editor di mashup online Power Query in codice Spark per l'esecuzione di scalabilità cloud traducendo M in flussi di dati Azure Data Factory. La ricerca di dati con Power Query e flussi di dati è particolarmente utile per gli ingegneri di dati o per gli integratori di dati Citizen.

> [!NOTE]
> L'attività Power Query in Azure Data Factory è attualmente disponibile in anteprima pubblica

## <a name="use-cases"></a>Casi d'uso

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>Esplorazione e preparazione rapida di dati interattivi

Più data Engineers e gli integratori di dati Citizen possono esplorare e preparare in modo interattivo i set di dati a livello di cloud. Con l'aumentare del volume, della varietà e della velocità dei dati nei data Lake, gli utenti devono disporre di un modo efficace per esplorare e preparare i set di dati. Potrebbe essere necessario, ad esempio, creare un set di dati che "disponga di tutte le informazioni demografiche sui clienti per i nuovi clienti dal 2017". Non è possibile eseguire il mapping a una destinazione nota. Si stanno esplorando, litigando e preparando i set di impostazioni per soddisfare un requisito prima di pubblicarlo nel Lake. La disputa è spesso usata per scenari di analisi meno formali. I set di impostazioni possono essere usati per eseguire trasformazioni e operazioni di machine learning a valle.

### <a name="code-free-agile-data-preparation"></a>Preparazione dei dati agile senza codice

Gli integratori di dati Citizen dedicano più del 60% del tempo alla ricerca e alla preparazione dei dati. Si sta cercando di eseguire questa operazione in un codice libero per migliorare la produttività operativa. Consentire agli integratori di dati dei cittadini di arricchire, modellare e pubblicare i dati usando strumenti noti come Power Query online in modo scalabile, migliora notevolmente la produttività. Il Azure Data Factory consente all'editor di mashup familiare Power Query online di consentire agli integratori di dati Citizen di correggere rapidamente gli errori, standardizzare i dati e produrre dati di alta qualità per supportare le decisioni aziendali.

### <a name="data-validation-and-exploration"></a>Convalida ed esplorazione dei dati

Analizza visivamente i dati in modo senza codice per rimuovere eventuali outlier, anomalie e conformi a una forma per l'analisi veloce.

## <a name="supported-sources"></a>Origini supportate

| Connettore | Formato dati | Tipo di autenticazione |
| -- | -- | --|
| [Archiviazione BLOB di Azure](connector-azure-blob-storage.md) | CSV, parquet | Chiave account |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Entità servizio |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, parquet | Chiave dell'account, entità servizio |
| [Database SQL di Azure](connector-azure-sql-database.md) | - | Autenticazione SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Autenticazione SQL |

## <a name="the-mashup-editor"></a>Editor mashup

Quando si crea un'attività di Power Query, tutti i set di dati di origine diventano query del set di dati e vengono inseriti nella cartella **ADFResource** . Per impostazione predefinita, UserQuery punterà alla prima query del set di dati. Tutte le trasformazioni devono essere eseguite in UserQuery in quanto le modifiche alle query del set di dati non sono supportate né verranno rese permanente. La ridenominazione, l'aggiunta e l'eliminazione di query non è attualmente supportata.

![Dispute](media/wrangling-data-flow/editor.png)

Attualmente non tutte le funzioni Power Query M sono supportate per data wrangling nonostante siano disponibili durante la creazione. Quando si compilano le attività Power Query, viene visualizzato il messaggio di errore seguente se una funzione non è supportata:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Per ulteriori informazioni sulle trasformazioni supportate, vedere [Data wrangling Functions](wrangling-functions.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un data wrangling Power query mash-up](wrangling-tutorial.md).
