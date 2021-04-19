---
title: Panoramica dell'account di archiviazione
titleSuffix: Azure Storage
description: Informazioni sui diversi tipi di account di archiviazione in Archiviazione di Azure. Esaminare la denominazione degli account, i livelli di prestazioni, i livelli di accesso, la ridondanza, la crittografia, gli endpoint e altro ancora.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d4874ad6688fa85f0c511632498938817bb218f7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714201"
---
# <a name="storage-account-overview"></a>Panoramica dell'account di archiviazione

Un account di archiviazione di Azure contiene tutti gli oggetti dati di Archiviazione di Azure: BLOB, file, code, tabelle e dischi. L'account di archiviazione fornisce uno spazio dei nomi univoco per Archiviazione di Azure dati accessibili da qualsiasi parte del mondo tramite HTTP o HTTPS. I dati nell'account di archiviazione di Azure sono durevoli e a disponibilità elevata, sicuri e altamente scalabili.

Per informazioni su come creare un account di archiviazione di Azure, vedere [Creare un account di archiviazione](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

Archiviazione di Azure offre diversi tipi di account di archiviazione. Ogni tipo supporta caratteristiche diverse e ha uno specifico modello di prezzi. È importante tenere in considerazione tali differenze prima di creare un account di archiviazione per determinare il tipo di account ottimale per le proprie applicazioni.

La tabella seguente descrive i tipi di account di archiviazione consigliati da Microsoft per la maggior parte degli scenari:

| Tipo di account di archiviazione | Servizi supportati | Opzioni di ridondanza | Modello di distribuzione | Utilizzo |
|--|--|--|--|--|
| Standard per utilizzo generico v2 | BLOB, file, coda, tabella e Data Lake Storage<sup>1</sup> | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Resource Manager<sup>3</sup> | tipo di account di archiviazione Basic per BLOB, file, code e tabelle. Consigliato per la maggior parte degli scenari che usano Archiviazione di Azure. |
| BLOB in blocchi Premium<sup>4</sup> | Solo BLOB in blocchi | LRS<br /><br />ZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Account di archiviazione con caratteristiche di prestazioni Premium per BLOB in blocchi e BLOB di accodamento. Consigliato per scenari con percentuali di transazioni elevate o scenari che usano oggetti di dimensioni inferiori o che richiedono una latenza di archiviazione costantemente bassa.<br />[Ulteriori informazioni...](../blobs/storage-blob-performance-tiers.md) |
| Condivisioni file Premium<sup>4</sup> | Solo condivisioni file | LRS<br /><br />ZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Account di archiviazione solo file con caratteristiche di prestazioni Premium. Consigliato per applicazioni di livello aziendale o a prestazioni elevate.<br />[Ulteriori informazioni...](../files/storage-files-planning.md#management-concepts) |
| BLOB di pagine Premium<sup>4</sup> | Solo BLOB di pagine | LRS | Resource Manager<sup>3</sup> | Tipo di account di archiviazione Premium solo per i BLOB di pagine.<br />[Ulteriori informazioni...](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Azure Data Lake Storage è un set di funzionalità dedicate all'analisi dei Big Data, create in Archiviazione BLOB di Azure. Data Lake Storage è supportato solo per gli account di archiviazione per utilizzo generico V2 con uno spazio dei nomi gerarchico abilitato. Per altre informazioni sui Data Lake Storage Gen2, vedere [Introduction to Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>2</sup> L'archiviazione con ridondanza della zona (ZRS) e l'archiviazione con ridondanza della zona geografica (GZRS/RA-GZRS) sono disponibili solo per account standard per utilizzo generico v2, BLOB in blocchi Premium e account di condivisione file Premium in determinate aree. Per altre informazioni sulle opzioni di ridondanza di Archiviazione di Azure, vedere [Ridondanza dell'archiviazione](storage-redundancy.md).

<sup>3</sup> Azure Resource Manager è il modello di distribuzione consigliato per le risorse di Azure, inclusi gli account di archiviazione. Per altre informazioni, vedere Resource Manager [panoramica di](../../azure-resource-manager/management/overview.md).

<sup>4</sup> Gli account di archiviazione in un livello di prestazioni Premium usano dischi a stato solido (SSD) per bassa latenza e velocità effettiva elevata.

Sono supportati anche gli account di archiviazione legacy. Per altre informazioni, vedere [Tipi di account di archiviazione legacy.](#legacy-storage-account-types)

## <a name="storage-account-endpoints"></a>Endpoint dell'account di archiviazione

Un account di archiviazione offre uno spazio dei nomi univoco in Azure per i dati. Tutti gli oggetti archiviati in Archiviazione di Azure hanno un indirizzo che include il nome univoco dell'account. La combinazione del nome dell'account e dell'endpoint di servizio di Archiviazione di Azure costituisce gli endpoint per l'account di archiviazione.

Quando si assegna un nome all'account di archiviazione, tenere presenti queste regole:

- I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.
- Nome dell'account di archiviazione deve essere univoco all'interno di Azure. Due account di archiviazione non possono avere lo stesso nome.

La tabella seguente elenca il formato dell'endpoint per ognuno dei Archiviazione di Azure servizi.

| Servizio di archiviazione | Endpoint |
|--|--|
| Archiviazione BLOB | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| File di Azure | `https://<storage-account>.file.core.windows.net` |
| Archiviazione code | `https://<storage-account>.queue.core.windows.net` |
| Archiviazione tabelle | `https://<storage-account>.table.core.windows.net` |

Costruire l'URL per accedere a un oggetto in un account di archiviazione aggiungendo la posizione dell'oggetto nell'account di archiviazione all'endpoint. Ad esempio, l'URL per un BLOB sarà simile al seguente:

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

È anche possibile configurare l'account di archiviazione per l'uso di un dominio personalizzato per i BLOB. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato per l'account di Archiviazione di Azure](../blobs/storage-custom-domain-name.md).  

## <a name="migrating-a-storage-account"></a>Migrazione di un account di archiviazione

La tabella seguente riepiloga e fornisce indicazioni per lo spostamento, l'aggiornamento o la migrazione di un account di archiviazione:

| Scenario di migrazione | Dettagli |
|--|--|
| Spostare un account di archiviazione in una sottoscrizione diversa | Azure Resource Manager offre opzioni per lo spostamento di una risorsa in una sottoscrizione diversa. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Spostare un account di archiviazione in un gruppo di risorse diverso | Azure Resource Manager offre opzioni per lo spostamento di una risorsa in un gruppo di risorse diverso. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Spostare un account di archiviazione in un'area diversa | Per spostare un account di archiviazione, creare una copia dell'account di archiviazione in un'altra area. Spostare quindi i dati in tale account usando AzCopy o un altro strumento di propria scelta. Per altre informazioni, vedere [Spostare un account Archiviazione di Azure in un'altra area.](storage-account-move.md) |
| Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico v2 | È possibile aggiornare un account di archiviazione per utilizzo generico v1 o un account di archiviazione BLOB a un account per utilizzo generico v2. Questa azione non può essere annullata. Per altre informazioni, [Eseguire l'aggiornamento alla versione 2 di un account di archiviazione per uso generico](storage-account-upgrade.md). |
| Eseguire la migrazione di un account di archiviazione classico Azure Resource Manager | Il Azure Resource Manager di distribuzione è superiore al modello di distribuzione classica in termini di funzionalità, scalabilità e sicurezza. Per altre informazioni sulla migrazione di un account di archiviazione classico a Azure Resource Manager, vedere Migrazione di account di archiviazione [in](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) Migrazione supportata dalla piattaforma di risorse **IaaS** dal modello di archiviazione classica a Azure Resource Manager . |

## <a name="transferring-data-into-a-storage-account"></a>Trasferimento dei dati in un account di archiviazione

Microsoft offre servizi e utilità per l'importazione dei dati da dispositivi di archiviazione locali o provider di archiviazione cloud di terze parti. La soluzione da usare dipende dalla quantità di dati che si sta trasferendo. Per altre informazioni, vedere panoramica [Archiviazione di Azure migrazione.](storage-migration-overview.md)

## <a name="storage-account-encryption"></a>Crittografia degli account di archiviazione

Tutti i dati nell'account di archiviazione vengono crittografati automaticamente sul lato servizio. Per altre informazioni sulla crittografia e sulla gestione delle chiavi, vedere [Crittografia Archiviazione di Azure per i dati in stato di inalter.](storage-service-encryption.md)

## <a name="storage-account-billing"></a>Fatturazione dell'account di archiviazione

Archiviazione di Azure le fatture in base all'utilizzo dell'account di archiviazione. Tutti gli oggetti in un account di archiviazione vengono fatturati insieme come gruppo. I costi di archiviazione vengono calcolati in base ai fattori seguenti:

- L'**area** si riferisce all'area geografica in cui si trova l'account.
- Il **tipo di account** si riferisce al tipo di account di archiviazione in uso.
- Il **livello di accesso** si riferisce al criterio di utilizzo dei dati specificato per l'account di archiviazione BLOB o v2 per utilizzo generico.
- **La** capacità si riferisce alla quantità di risorse dell'account di archiviazione che si sta usando per archiviare i dati.
- **La ridondanza** determina quante copie dei dati vengono mantenute contemporaneamente e in quali posizioni.
- Le **transazioni** si riferiscono a tutte le operazioni di lettura e scrittura in Archiviazione di Azure.
- I **dati in uscita** si riferiscono ai dati trasferiti all'esterno di un'area di Azure. Quando un'applicazione che non è in esecuzione nella stessa area geografica accede ai dati dell'account di archiviazione, viene addebitata un importo per i dati in uscita. Per informazioni sull'uso dei gruppi di risorse per raggruppare i dati e i servizi nella stessa area per ridurre gli addebiti per il traffico in uscita, vedere [Informazioni sul gruppo di risorse di Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

La [Archiviazione di Azure prezzi fornisce](https://azure.microsoft.com/pricing/details/storage/) informazioni dettagliate sui prezzi in base al tipo di account, alla capacità di archiviazione, alla replica e alle transazioni. I [dettagli sui prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/) forniscono informazioni dettagliate sui prezzi per l'uscita dei dati. È possibile usare il calcolatore [Archiviazione di Azure prezzi per](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) stimare i costi.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>Tipi di account di archiviazione legacy

La tabella seguente descrive i tipi di account di archiviazione legacy. Questi tipi di account non sono consigliati da Microsoft, ma possono essere usati in determinati scenari:

| Tipo di account di archiviazione legacy | Servizi supportati | Opzioni di ridondanza | Modello di distribuzione | Utilizzo |
|--|--|--|--|--|
| Standard per utilizzo generico v1 | BLOB, file, coda, tabella e Data Lake Storage | LRS/GRS/RA-GRS | Resource Manager, classica | Gli account per utilizzo generico v1 potrebbero non avere le funzionalità più recenti o i prezzi più bassi per gigabyte. Prendere in considerazione l'uso per questi scenari:<br /><ul><li>Le applicazioni richiedono il modello di distribuzione classica di Azure.</li><li>Le applicazioni sono a elevato utilizzo di transazioni o usano una larghezza di banda significativa per la replica geografica, ma non richiedono una capacità elevata. In questo caso, un account per utilizzo generico v1 può essere la scelta più economica.</li><li>Si usa una versione dell'API REST di Archiviazione di Azure precedente alla versione 2014-02-14 o una libreria client con una versione inferiore a 4.x e non è possibile aggiornare l'applicazione.</li></ul> |
| Archiviazione BLOB standard | BLOB (solo BLOB in blocchi e BLOB di accodamento) | LRS/GRS/RA-GRS | Gestione risorse | Quando possibile, Microsoft consiglia di usare account standard per utilizzo generico v2. |

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account di archiviazione](storage-account-create.md)
- [Upgrade to a general-purpose v2 storage account](storage-account-upgrade.md) (Eseguire l'aggiornamento alla versione 2 di un account di archiviazione per utilizzo generico)
- [consente di ripristinare un account di archiviazione eliminato](storage-account-recover.md)