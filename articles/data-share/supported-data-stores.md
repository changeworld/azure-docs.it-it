---
title: Archivi dati supportati in Condivisione dati di Azure
description: Informazioni sugli archivi dati supportati per l'uso nella condivisione dati di Azure.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 852c44f5edc5c0b0f5f655f63ab040927bd9bc7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963680"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Archivi dati supportati in Condivisione dati di Azure

La condivisione di dati di Azure offre una condivisione dei dati aperta e flessibile, inclusa la possibilità di condividere da e in archivi dati diversi. I provider di dati possono condividere dati da un tipo di archivio dati e i consumer di dati possono scegliere un archivio dati per ricevere i dati. 

Questo articolo illustra il set completo di archivi dati di Azure supportati dalla condivisione dati di Azure. Verranno inoltre fornite informazioni sul modo in cui i provider di dati e i consumer di dati possono combinare archivi dati diversi. 

## <a name="supported-data-stores"></a>Archivi dati supportati 

La tabella seguente illustra gli archivi dati supportati da Azure Data Share. 

| Archivio dati | Condivisione basata su snapshot completi | Condivisione basata su snapshot incrementali | Condivisione sul posto 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Archiviazione BLOB di Azure |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Database SQL di Azure |✓ | | |
| Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse) |✓ | | |
| Azure sinapsi Analytics (area di lavoro) pool SQL dedicato |✓ | | |
| Esplora dati di Azure | | |✓ |

## <a name="data-store-support-matrix"></a>Matrice di supporto dell'archivio dati

La condivisione di dati di Azure consente ai consumer di dati di scegliere un archivio dati per accettare i dati. Ad esempio, i dati condivisi dal database SQL di Azure possono essere ricevuti in Azure Data Lake Storage Gen2, nel database SQL di Azure o in Azure sinapsi Analytics. Quando i clienti configurano una condivisione dati ricevente, possono scegliere il formato per la ricezione dei dati. 

Nella tabella seguente vengono illustrate le combinazioni e le opzioni che i consumer di dati possono scegliere quando accettano e configurano una condivisione di dati. Per altre informazioni, vedere [configurare il mapping di un set di dati](how-to-configure-mapping.md).

| Archivio dati | Archiviazione BLOB | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Database SQL | Sinapsi Analytics (in precedenza SQL Data Warehouse) | Pool SQL dedicato dell'analisi sinapsi (area di lavoro) | Esplora dati
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Archiviazione BLOB | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| Database SQL | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Sinapsi Analytics (in precedenza SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Pool SQL dedicato dell'analisi sinapsi (area di lavoro) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Esplora dati ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Condividere da un account di archiviazione
La condivisione di dati di Azure supporta la condivisione di file, cartelle e file System da Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2. Supporta anche la condivisione di BLOB, cartelle e contenitori dall'archiviazione BLOB di Azure. Attualmente sono supportati solo i BLOB in blocchi. 

Quando i file System, i contenitori o le cartelle sono condivisi nella condivisione basata su snapshot, i consumer di dati possono scegliere di eseguire una copia completa dei dati condivisi. In alternativa, è possibile usare la funzionalità di snapshot incrementale per copiare solo i nuovi file o i file aggiornati. 

Uno snapshot incrementale si basa sull'ora dell'Ultima modifica dei file. I file esistenti con lo stesso nome dei file nei dati ricevuti vengono sovrascritti in uno snapshot. I file eliminati dall'origine non vengono eliminati nella destinazione. 

Per altre informazioni, vedere [condividere e ricevere dati da un archivio BLOB di Azure e Azure Data Lake storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Condividere da un'origine basata su SQL
La condivisione di dati di Azure supporta la condivisione di tabelle e viste dal database SQL di Azure e da Azure sinapsi Analytics (in precedenza Azure SQL Data Warehouse). Supporta la condivisione di tabelle dal pool SQL dedicato di analisi delle sinapsi di Azure (area di lavoro). La condivisione da Azure sinapsi Analytics (area di lavoro) pool SQL senza server non è attualmente supportata. 

I consumer di dati possono scegliere di accettare i dati in Azure Data Lake Storage Gen2 o nell'archiviazione BLOB di Azure come file di un file con estensione CSV o parquet. Possono anche accettare dati come tabelle nel database SQL di Azure e in Azure sinapsi Analytics.

Quando i consumer accettano i dati in Azure Data Lake Storage Gen2 o nell'archiviazione BLOB di Azure, gli snapshot completi sovrascrivono il contenuto del file di destinazione se il file esiste già. Quando i dati vengono ricevuti in una tabella e la tabella di destinazione non esiste già, la condivisione di dati di Azure crea una tabella SQL usando lo schema di origine. Se una tabella di destinazione esiste già e ha lo stesso nome, viene eliminata e sovrascritta con lo snapshot completo più recente. Gli snapshot incrementali non sono attualmente supportati.

Per altre informazioni, vedere [condividere e ricevere dati da un database SQL di Azure e da Azure sinapsi Analytics](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Condividi da Esplora dati
La condivisione di dati di Azure supporta la possibilità di condividere i database sul posto da Azure Esplora dati cluster. Un provider di dati può condividere a livello del database o del cluster. 

Quando i dati vengono condivisi a livello di database, i consumer di dati possono accedere solo ai database condivisi dal provider di dati. Quando un provider condivide i dati a livello di cluster, i consumer di dati possono accedere a tutti i database dal cluster del provider, inclusi eventuali database futuri creati dal provider di dati.

Per accedere ai database condivisi, i consumer di dati necessitano del proprio cluster di Esplora dati di Azure. Il cluster deve trovarsi nello stesso data center di Azure del cluster di Azure Esplora dati del provider di dati. 

Quando viene stabilita una relazione di condivisione, condivisione dati di Azure crea un collegamento simbolico tra il cluster del provider e il cluster del consumer. I dati inseriti nel cluster di origine con la modalità batch vengono visualizzati nel cluster di destinazione entro pochi minuti.

Per altre informazioni, vedere [condividere e ricevere dati da Azure Esplora dati](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, continuare con l'esercitazione [condividere i dati](share-your-data.md) .
