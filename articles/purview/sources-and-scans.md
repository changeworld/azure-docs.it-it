---
title: Origini dati e tipi di file supportati
description: Questo articolo fornisce informazioni concettuali sulle origini dati e sui tipi di file supportati in ambito.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3b19fab33d0c8f53025605fd14fe65f08e660392
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677922"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Origini dati e tipi di file supportati in Azure

Questo articolo illustra le origini dati supportate, i tipi di file e i concetti di analisi in ambito.

## <a name="supported-data-sources"></a>Origini dati supportate

Azure competenza supporta le origini seguenti:

| Tipo di archivio | Tipo di autenticazione supportato | Configurare le analisi tramite UX/PowerShell |
| ---------- | ------------------- | ------------------------------ |
| Server SQL locale                   | Autenticazione SQL                        | UX                                |
| Azure Synapse Analytics (in precedenza SQL Data Warehouse)            | Autenticazione SQL, entità servizio, MSI               | UX                             |
| Database SQL di Azure (DB)                  | Autenticazione SQL, entità servizio, MSI               | UX |
| Istanza gestita di database SQL di Azure      | Autenticazione SQL, entità servizio, MSI               | UX    |
| Archiviazione BLOB di Azure                       | Chiave dell'account, entità servizio, MSI | UX            |
| Esplora dati di Azure                      | Entità servizio                              | UX            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | Entità servizio, MSI                              | UX            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Chiave dell'account, entità servizio, MSI            | UX            |
| Azure Cosmos DB                          | Chiave account                                    | UX            |


> [!Note]
> Azure Data Lake Storage Gen2 è ora disponibile a livello generale. È consigliabile iniziare a usarlo oggi stesso. Per altre informazioni, vedere la [pagina del prodotto](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-supported-for-scanning"></a>Tipi di file supportati per l'analisi

I tipi di file seguenti sono supportati per l'analisi, per l'estrazione e la classificazione dello schema, ove applicabile:

- Formati di file strutturati supportati dall'estensione: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
- Formati di file di documenti supportati dall'estensione: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- La competenza supporta inoltre estensioni di file personalizzate e parser personalizzati.
 
> [!Note]
> Ogni file gzip deve essere mappato a un singolo file CSV all'interno di. I file gzip sono soggetti a regole di classificazione personalizzate e di sistema. Attualmente non è supportata la scansione di un file gzip mappato a più file all'interno di o di qualsiasi tipo di file diverso da CSV. 

## <a name="sampling-within-a-file"></a>Campionamento all'interno di un file

Nella terminologia di competenza,
- L1 Scan: estrae le informazioni di base e i metadati come nome file, dimensioni e nome completo
- Analisi L2: estrae lo schema per i tipi di file strutturati e le tabelle di database
- L3 Scan: estrae lo schema laddove applicabile e sottopone il file campionato al sistema e alle regole di classificazione personalizzate

Per tutti i formati di file strutturati, i file di esempio dello scanner di competenza sono i seguenti:

- Per i tipi di file strutturati, vengono campionate 128 righe in ogni colonna o 1 MB, a seconda del valore minore.
- Per i formati di file di documento, vengono campionati 20 MB di ogni file.
    - Se un file di documento è più grande di 20 MB, non è soggetto a un'analisi approfondita (soggetto alla classificazione). In tal caso, l'ambito acquisisce solo i metadati di base, come il nome file e il nome completo.

## <a name="resource-set-file-sampling"></a>Campionamento file set di risorse

Una cartella o un gruppo di file di partizione viene rilevato come un *set di risorse* in ambito, se corrisponde a un criterio del set di risorse di sistema o a un criterio del set di risorse definito dal cliente. Se viene rilevato un set di risorse, viene campionata ogni cartella che contiene. Altre informazioni sui set di risorse sono disponibili [qui](concept-resource-sets.md).

Campionamento di file per i set di risorse in base ai tipi di file:

- **File delimitati (CSV, PSV, SSV, TSV)** -1 nei file 100 sono campionati (analisi L3) in una cartella o un gruppo di file di partizione considerati come ' set di risorse '
- **Data Lake tipi di file (parquet, avro, ORC)** -1 nei file 18.446.744.073.709.551.615 (Long max) vengono campionati (analisi L3) in una cartella o un gruppo di file di partizione considerati un *set di risorse*
- **Altri tipi di file strutturati (JSON, XML, txt)** -1 nei file 100 sono campionati (analisi L3) in una cartella o un gruppo di file di partizione considerati come ' set di risorse '
- **Oggetti SQL e entità CosmosDB** : ogni file è stato analizzato con L3.
- **Tipi di file di documento** . ogni file è stato analizzato con L3. I modelli di set di risorse non si applicano a questi tipi di file.

## <a name="scan-regions"></a>Aree di analisi
Di seguito è riportato un elenco di tutte le aree dell'origine dati di Azure (data center) in cui viene eseguito lo scanner di competenza. Se l'origine dati di Azure si trova in un'area esterna a questo elenco, lo scanner verrà eseguito nell'area dell'istanza di ambito.
 
### <a name="purview-scanner-regions"></a>Aree dello scanner di competenza

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- SoutheastAsia
- Europa occidentale
- NorthEurope
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral

## <a name="classification"></a>Classificazione

Tutte le regole di classificazione del sistema 105 sono valide per i formati di file strutturati. Solo le regole di classificazione di MCE sono valide per i tipi di file di documento (non i modelli Regex nativi di analisi dei dati, il rilevamento basato sul filtro Bloom). Per ulteriori informazioni sulle classificazioni supportate, vedere la pagina relativa alle [classificazioni supportate in Azure](supported-classifications.md).

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: eseguire il starter kit e analizzare i dati](tutorial-scan-data.md)
- [Gestire le origini dati in Azure (anteprima)](manage-data-sources.md)