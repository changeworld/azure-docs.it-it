---
title: Archivi dati supportati in Condivisione dati di Azure
description: Informazioni sugli archivi dati supportati per l'uso in Condivisione dati di Azure.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: def73d137f3cc2c79ae8417995ec6bdf6c519b7d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812625"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Archivi dati supportati in Condivisione dati di Azure

Condivisione dati di Azure offre la condivisione dei dati aperta e flessibile, inclusa la possibilità di condividere da e verso archivi dati diversi. I provider di dati possono condividere dati da un tipo di archivio dati e i consumer di dati possono scegliere un archivio dati per ricevere i dati. 

Questo articolo illustra il set completo di archivi dati di Azure Condivisione dati di Azure supportati. Si apprenderà anche come i provider di dati e i consumer di dati possono combinare archivi dati diversi. 

## <a name="supported-data-stores"></a>Archivi dati supportati 

Nella tabella seguente vengono illustrati gli archivi dati Condivisione dati di Azure supportati. 

| Archivio dati | Condivisione basata su snapshot completi | Condivisione basata su snapshot incrementali | Condivisione sul posto 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Archiviazione BLOB di Azure |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Database SQL di Azure |✓ | | |
| Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse) |✓ | | |
| Azure Synapse Analytics pool SQL dedicato (area di lavoro) |✓ | | |
| Esplora dati di Azure | | |✓ |

## <a name="data-store-support-matrix"></a>Matrice di supporto dell'archivio dati

Condivisione dati di Azure consente ai consumer di dati di scegliere un archivio dati per accettare i dati. Ad esempio, i dati condivisi da database SQL di Azure possono essere ricevuti in Azure Data Lake Storage Gen2, database SQL di Azure o Azure Synapse Analytics. Quando i clienti configurano una condivisione dati ricevente, possono scegliere il formato per ricevere i dati. 

La tabella seguente illustra le combinazioni e le opzioni che i consumer di dati possono scegliere quando accettano e configurano una condivisione dati. Per altre informazioni, vedere Configurare [il mapping di un set di dati.](how-to-configure-mapping.md)

| Archivio dati | Archiviazione BLOB | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Database SQL | Synapse Analytics (in precedenza SQL Data Warehouse) | Synapse Analytics un pool SQL dedicato (area di lavoro) | Esplora dati
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Archiviazione BLOB | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| Database SQL | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (in precedenza SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics un pool SQL dedicato (area di lavoro) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Esplora dati ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Condividere da un account di archiviazione
Condivisione dati di Azure supporta la condivisione di file, cartelle e file system da Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2. Supporta anche la condivisione di BLOB, cartelle e contenitori da Archiviazione BLOB di Azure. È possibile condividere BLOB di blocchi, di accodamento o di pagine che vengono ricevuti come BLOB in blocchi.

Quando file system, contenitori o cartelle vengono condivisi nella condivisione basata su snapshot, i consumer di dati possono scegliere di creare una copia completa dei dati condivisi. Oppure possono usare la funzionalità di snapshot incrementale per copiare solo nuovi file o file aggiornati. 

Uno snapshot incrementale si basa sull'ora dell'ultima modifica dei file. I file esistenti con lo stesso nome dei file nei dati ricevuti vengono sovrascritti in uno snapshot. I file eliminati dall'origine non vengono eliminati nella destinazione. 

Per altre informazioni, vedere [Condividere e ricevere dati da Archiviazione BLOB di Azure e Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Condividere da un'origine basata su SQL
Condivisione dati di Azure supporta la condivisione di tabelle e viste da database SQL di Azure e Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse). Supporta la condivisione di tabelle da un pool SQL Azure Synapse Analytics (area di lavoro) dedicato. La condivisione da Azure Synapse Analytics pool SQL serverless (area di lavoro) non è attualmente supportata. 

I consumer di dati possono scegliere di accettare i dati Azure Data Lake Storage Gen2 o Archiviazione BLOB di Azure come file CSV o file parquet. Possono anche accettare dati come tabelle in database SQL di Azure e Azure Synapse Analytics.

Quando i consumer accettano dati Azure Data Lake Storage Gen2 o Archiviazione BLOB di Azure, gli snapshot completi sovrascrivono il contenuto del file di destinazione se il file esiste già. Quando i dati vengono ricevuti in una tabella e la tabella di destinazione non esiste già, Condivisione dati di Azure crea una tabella SQL usando lo schema di origine. Se una tabella di destinazione esiste già e ha lo stesso nome, viene eliminata e sovrascritta con lo snapshot completo più recente. Gli snapshot incrementali non sono attualmente supportati.

Per altre informazioni, vedere [Condividere e ricevere dati da database SQL di Azure e Azure Synapse Analytics](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Condividere da Esplora dati
Condivisione dati di Azure supporta la possibilità di condividere database sul posto da Esplora dati di Azure cluster. Un provider di dati può condividere a livello del database o del cluster. 

Quando i dati vengono condivisi a livello di database, i consumer di dati possono accedere solo ai database condivisi dal provider di dati. Quando un provider condivide i dati a livello di cluster, i consumer di dati possono accedere a tutti i database dal cluster del provider, inclusi eventuali database futuri creati dal provider di dati.

Per accedere ai database condivisi, i consumer di dati devono avere un proprio cluster Esplora dati di Azure dati. Il cluster deve essere nello stesso data center di Azure del cluster Esplora dati di Azure provider di dati. 

Quando viene stabilita una relazione di condivisione, Condivisione dati di Azure crea un collegamento simbolico tra il cluster del provider e il cluster del consumer. I dati inseriti nel cluster di origine usando la modalità batch vengono visualizzati nel cluster di destinazione entro pochi minuti.

Per altre informazioni, vedere [Condividere e ricevere dati da Esplora dati di Azure](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, continuare con [l'esercitazione Condividere i](share-your-data.md) dati.
