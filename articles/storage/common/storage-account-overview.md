---
title: Panoramica dell'account di archiviazione
titleSuffix: Azure Storage
description: Informazioni sui diversi tipi di account di archiviazione in archiviazione di Azure. Verificare la denominazione degli account, i livelli di prestazioni, i livelli di accesso, la ridondanza, la crittografia, gli endpoint e altro ancora.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 24d955b0d1c53f57f5927f9e893b6ecd75fb3ca8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561892"
---
# <a name="storage-account-overview"></a>Panoramica dell'account di archiviazione

Un account di archiviazione di Azure contiene tutti gli oggetti dati di Archiviazione di Azure: BLOB, file, code, tabelle e dischi. L'account di archiviazione fornisce uno spazio dei nomi univoco per i dati di archiviazione di Azure accessibili da qualsiasi parte del mondo tramite HTTP o HTTPS. I dati nell'account di archiviazione di Azure sono durevoli e a disponibilità elevata, protetti e altamente scalabili.

Per informazioni su come creare un account di archiviazione di Azure, vedere [Creare un account di archiviazione](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

Archiviazione di Azure offre diversi tipi di account di archiviazione. Ogni tipo supporta caratteristiche diverse e ha uno specifico modello di prezzi. È importante tenere in considerazione tali differenze prima di creare un account di archiviazione per determinare il tipo di account ottimale per le proprie applicazioni. I tipi di account di archiviazione sono i seguenti:

- **Account per utilizzo generico v2**: tipo di account di archiviazione Basic per BLOB, file, code e tabelle. Consigliato per la maggior parte degli scenari che usano Archiviazione di Azure.
- **Account per utilizzo generico v1**: tipo di account legacy per BLOB, file, code e tabelle. Laddove è possibile, preferire account per utilizzo generico v2.
- **Account BlockBlobStorage**: account di archiviazione con caratteristiche di prestazioni Premium per i BLOB in blocchi e i BLOB di aggiunta. Consigliato per scenari con percentuali di transazioni elevate o scenari che usano oggetti di dimensioni inferiori o che richiedono una latenza di archiviazione costantemente bassa.
- **Account FileStorage**: account di archiviazione solo file con caratteristiche di prestazioni Premium. Consigliato per applicazioni di livello aziendale o a prestazioni elevate.
- **Account BlobStorage**: account di archiviazione solo BLOB legacy. Laddove è possibile, preferire account per utilizzo generico v2.

La tabella seguente descrive i tipi di account di archiviazione, i servizi supportati e i modelli di distribuzione supportati per ogni tipo di conto:

| Tipo di account di archiviazione | Servizi supportati | Opzioni di ridondanza | Modello di distribuzione<sup>1</sup> |
|--|--|--|--|
| Utilizzo generico v2 | BLOB, file, code, tabelle, dischi e Data Lake Gen2<sup>2</sup> | CON ridondanza locale, GRS, RA-GRS, ZRS, GZRS, RA-GZRS<sup>3</sup> | Gestione risorse |
| Utilizzo generico v1 | BLOB, file, coda, tabella e disco | LRS, GRS, RA-GRS | Resource Manager, classica |
| BlockBlobStorage | BLOB (solo BLOB in blocchi e BLOB di accodamento) | CON ridondanza locale, ZRS<sup>3</sup> | Gestione risorse |
| FileStorage | Solo file | CON ridondanza locale, ZRS<sup>3</sup> | Gestione risorse |
| BlobStorage | BLOB (solo BLOB in blocchi e BLOB di accodamento) | LRS, GRS, RA-GRS | Gestione risorse |

<sup>1</sup>È consigliabile usare il modello di distribuzione Azure Resource Manager. Gli account di archiviazione che usano il modello di distribuzione classico possono comunque essere creati in alcune posizioni e gli account classici esistenti continuano a essere supportati. Per altre informazioni, vedere [Confronto tra distribuzione di Azure Resource Manager e classica: comprensione dei modelli di implementazione e dello stato delle risorse](../../azure-resource-manager/management/deployment-models.md).

<sup>2</sup> Azure Data Lake Storage Gen2 è un set di funzionalità dedicate a Big Data Analytics, basate sull'archiviazione BLOB di Azure. Data Lake Storage Gen2 è supportato solo per gli account di archiviazione per utilizzo generico V2 con lo spazio dei nomi gerarchico abilitato. Per altre informazioni su Data Lake Storage Gen2, vedere [Introduzione ad Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>3</sup> L'archiviazione con ridondanza della zona (ZRS) e l'archiviazione con ridondanza della zona geografica (GZRS/RA-GZRS) sono disponibili solo per gli account per utilizzo generico standard v2, BlockBlobStorage e filestorage in determinate aree. Per altre informazioni sulle opzioni di ridondanza di Archiviazione di Azure, vedere [Ridondanza dell'archiviazione](storage-redundancy.md).

### <a name="storage-account-redundancy"></a>Ridondanza dell'account di archiviazione

Le opzioni di ridondanza per un account di archiviazione includono:

- **Archiviazione con ridondanza locale (con ridondanza locale)**: strategia di ridondanza semplice e a basso costo. I dati vengono copiati in modo sincrono per tre volte all'interno di un'unica posizione fisica nell'area primaria.
- **Archiviazione con ridondanza della zona (ZRS)**: ridondanza per gli scenari che richiedono una disponibilità elevata. I dati vengono copiati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria.
- **Archiviazione con ridondanza geografica**: ridondanza tra più aree per la protezione da interruzioni a livello di area. I dati vengono copiati in modo sincrono per tre volte all'interno dell'area primaria e quindi copiati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).
- **Archiviazione con ridondanza della zona geografica (GZRS)**: ridondanza per gli scenari che richiedono disponibilità elevata e durabilità massima. I dati vengono copiati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria e quindi copiati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica della zona e accesso in lettura (RA-GZRS).

Per altre informazioni sulle opzioni di ridondanza in Archiviazione di Azure, vedere [Ridondanza di Archiviazione di Azure](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>Account per utilizzo generico v2

Gli account di archiviazione per utilizzo generico v2 supportano le funzionalità di archiviazione di Azure più recenti e incorporano tutte le funzionalità degli account di archiviazione BLOB e per utilizzo generico v1. Gli account per utilizzo generico v2 offrono i prezzi per gigabyte più bassi per Archiviazione di Azure, oltre a prezzi per transazione competitivi a livello di settore. Gli account di archiviazione per utilizzo generico v2 supportano i servizi di archiviazione di Azure seguenti:

- BLOB (tutti i tipi: blocchi, accodamento, pagina)
- Data Lake Gen2
- File
- Dischi
- Code
- Tabelle

> [!NOTE]
> Per la maggior parte degli scenari è consigliabile usare un account di archiviazione per utilizzo generico v2. È possibile eseguire facilmente l'aggiornamento di un account di archiviazione per utilizzo generico v1 o un account di archiviazione BLOB a un account per utilizzo generico v2, senza tempi di inattività e senza la necessità copiare i dati. Tuttavia, l'aggiornamento non può essere annullato.
>
> Per altre informazioni sull'aggiornamento a un account per utilizzo generico v2, vedere [Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico v2](storage-account-upgrade.md).

Gli account di archiviazione per utilizzo generico v2 offrono più livelli di accesso per l'archiviazione dei dati in base ai modelli di utilizzo. Per altre informazioni, vedere [Livelli di accesso per i dati BLOB in blocchi](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Account per utilizzo generico v1

Gli account di archiviazione per utilizzo generico V1 forniscono l'accesso a tutti i servizi di archiviazione di Azure, ma potrebbero non avere le funzionalità più recenti o i prezzi più bassi per Gigabyte. Gli account di archiviazione per utilizzo generico v1 supportano i servizi di archiviazione di Azure seguenti:

- BLOB (tutti i tipi)
- File
- Dischi
- Code
- Tabelle

Per questi scenari, è possibile usare account generali V1:

- Le applicazioni richiedono il modello di distribuzione classica di Azure. Gli account per utilizzo generico v2 e gli account di archiviazione BLOB supportano solo il modello di distribuzione Azure Resource Manager.

- Le applicazioni sono a elevato utilizzo di transazioni o usano una larghezza di banda di replica geografica significativa, ma non richiedono una capacità elevata. In questo caso, un account per utilizzo generico v1 può essere la scelta più economica.

- Si usa una versione dell' [API REST dei servizi di archiviazione](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) precedente alla 2014-02-14 o una libreria client con una versione precedente alla 4. x. Non è possibile aggiornare l'applicazione.

> [!NOTE]
> Sebbene Microsoft consigli gli account per utilizzo generico V2 per la maggior parte degli scenari, Microsoft continuerà a supportare gli account per utilizzo generico V1 per i clienti nuovi ed esistenti. È possibile creare account di archiviazione per utilizzo generico V1 in nuove aree ogni volta che archiviazione di Azure è disponibile in tali aree. Microsoft non ha attualmente un piano per deprecare il supporto per gli account per utilizzo generico V1 e fornirà almeno un preavviso di un anno prima di deprecare le funzionalità di archiviazione di Azure. Microsoft continuerà a fornire aggiornamenti della sicurezza per gli account per utilizzo generico V1, ma per questo tipo di account non è previsto alcun nuovo sviluppo di funzionalità.
>
> A partire dal 1 ° ottobre 2020, i prezzi per gli account per utilizzo generico V1 nelle nuove aree di archiviazione di Azure equivalgono ai prezzi per gli account per utilizzo generico V2 in tali aree. I prezzi nelle aree di archiviazione di Azure esistenti non sono stati modificati. Per informazioni dettagliate sui prezzi per gli account per utilizzo generico V1 in un'area specifica, vedere la pagina dei prezzi di archiviazione di Azure. Scegliere l'area geografica e quindi selezionare **altro** accanto a offerte per i **prezzi**.

### <a name="blockblobstorage-accounts"></a>Account BlockBlobStorage

Un account BlockBlobStorage è un account di archiviazione specializzato nel livello di prestazioni Premium per l'archiviazione di dati di oggetti non strutturati come BLOB in blocchi o BLOB di Accodamento. Rispetto agli account per utilizzo generico V2 e BlobStorage, gli account BlockBlobStorage offrono latenza bassa, coerente e frequenze di transazione più elevate.

Gli account BlockBlobStorage non supportano attualmente la suddivisione in livelli per i livelli di accesso frequente, sporadico o archivio. Questo tipo di account di archiviazione non supporta BLOB di pagine, tabelle o code.

### <a name="filestorage-accounts"></a>Account filestorage

Un account filestorage è un account di archiviazione specializzato usato per archiviare e creare condivisioni file Premium. Questo tipo di account di archiviazione supporta i file ma non i BLOB in blocchi, i BLOB di Accodamento, i BLOB di pagine, le tabelle o le code.

Gli account filestorage offrono caratteristiche esclusive dedicate alle prestazioni, ad esempio il picchi di IOPS. Per ulteriori informazioni su queste caratteristiche, vedere la sezione [livelli di archiviazione di condivisione file](../files/storage-files-planning.md#storage-tiers) della Guida alla pianificazione dei file.

## <a name="naming-storage-accounts"></a>Denominazione degli account di archiviazione

Quando si assegna un nome all'account di archiviazione, tenere presenti queste regole:

- I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.
- Nome dell'account di archiviazione deve essere univoco all'interno di Azure. Due account di archiviazione non possono avere lo stesso nome.

## <a name="performance-tiers"></a>Livelli di prestazioni

A seconda del tipo di account di archiviazione creato, è possibile scegliere tra i livelli di prestazioni standard e Premium. La tabella seguente riepiloga i livelli di prestazioni disponibili per il tipo di account di archiviazione.

| Tipo di account di archiviazione | Livelli di prestazioni supportati |
|--|--|
| Utilizzo generico v2 | Standard, Premium<sup>1</sup> |
| Utilizzo generico v1 | Standard, Premium<sup>1</sup> |
| BlockBlobStorage | Premium |
| FileStorage | Premium |
| BlobStorage | Standard |

<sup>1</sup> Le prestazioni Premium per gli account per utilizzo generico V2 e per utilizzo generico V1 sono disponibili solo per i BLOB di pagine e dischi. Le prestazioni Premium per i BLOB in blocchi o di aggiunta sono disponibili solo per gli account BlockBlobStorage. Le prestazioni Premium per i file sono disponibili solo per gli account FileStorage.

### <a name="general-purpose-storage-accounts"></a>Account di archiviazione per utilizzo generico

Gli account di archiviazione per utilizzo generico possono essere configurati per uno dei livelli di prestazioni seguenti:

- Un livello di prestazioni standard per l'archiviazione di BLOB, file, tabelle, code e dischi delle macchine virtuali di Azure. Per altre informazioni sugli obiettivi di scalabilità per gli account di archiviazione standard, vedere [obiettivi di scalabilità per gli account di archiviazione standard](scalability-targets-standard-account.md).
- Livello di prestazioni Premium per l'archiviazione di dischi di macchine virtuali non gestiti. Microsoft consiglia di usare Managed disks con macchine virtuali di Azure anziché con dischi non gestiti. Per altre informazioni sugli obiettivi di scalabilità per il livello di prestazioni Premium, vedere [obiettivi di scalabilità per gli account di archiviazione BLOB di pagine Premium](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Account di archiviazione BlockBlobStorage

Gli account di archiviazione BlockBlobStorage forniscono un livello di prestazioni Premium per l'archiviazione di BLOB in blocchi e BLOB di Accodamento. Per ulteriori informazioni, vedere [obiettivi di scalabilità per gli account di archiviazione BLOB in blocchi Premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Account di archiviazione filestorage

Gli account di archiviazione filestorage forniscono un livello di prestazioni Premium per le condivisioni file di Azure. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per File di Azure](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Livelli di accesso per i dati BLOB in blocchi

Archiviazione di Azure offre diverse opzioni per l'accesso ai dati BLOB in blocchi in base ai modelli di utilizzo. Ogni livello di accesso in Archiviazione di Azure è ottimizzato per un determinato modello di utilizzo dei dati. Selezionando il livello di accesso corretto per le proprie esigenze, è possibile archiviare i dati BLOB in blocchi nel modo economicamente più conveniente.

I livelli di accesso disponibili sono i seguenti:

- Livello **di accesso** frequente. Questo livello è ottimizzato per l'accesso frequente agli oggetti nell'account di archiviazione. L'accesso ai dati nel livello critico è più conveniente, mentre i costi di archiviazione sono maggiori. I nuovi account di archiviazione vengono creati nel livello ad accesso frequente per impostazione predefinita.
- Livello **di accesso** sporadico. Questo livello è ottimizzato per l'archiviazione di grandi quantità di dati a cui si accede raramente e che vengono archiviati per almeno 30 giorni. L'archiviazione dei dati nel livello ad accesso sporadico è più conveniente, ma l'accesso a tali dati può risultare più costoso rispetto all'accesso ai dati nel livello critico.
- Il livello di **accesso archivio**. Questo livello è disponibile solo per i singoli BLOB in blocchi. Il livello archivio è ottimizzato per i dati che possono tollerare diverse ore di latenza di recupero e che rimarranno nel livello di archiviazione per almeno 180 giorni. Il livello archivio è l'opzione più conveniente per l'archiviazione dei dati. Tuttavia, l'accesso a tali dati è più costoso rispetto all'accesso ai dati nei livelli ad accesso frequente o sporadico.

Se viene apportata una modifica al modello di utilizzo dei dati, è possibile passare da un livello di accesso all'altro in qualsiasi momento. Per altre informazioni sui livelli di accesso, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](../blobs/storage-blob-storage-tiers.md).

La tabella seguente illustra i livelli di accesso disponibili per i BLOB in ogni tipo di account di archiviazione.

| Tipo di account di archiviazione | Livelli di accesso supportati |
|--|--|
| Utilizzo generico v2 | Frequente, ad accesso sporadico, archivio<sup>1</sup> |
| Utilizzo generico v1 | N/D |
| BlockBlobStorage | N/D |
| FileStorage | N/D |
| BlobStorage | Frequente, ad accesso sporadico, archivio<sup>1</sup> |

<sup>1</sup> la suddivisione in livelli di archiviazione e a livello di BLOB supporta solo BLOB in blocchi. Il livello di archiviazione archivio è disponibile solo a livello di singolo BLOB, non a livello di account di archiviazione. Per altre informazioni, vedere [livelli di accesso per archiviazione BLOB di Azure-accesso frequente,](../blobs/storage-blob-storage-tiers.md)accesso sporadico e archivio.

> [!IMPORTANT]
> La modifica del livello di accesso per un account di archiviazione o un BLOB esistente può comportare costi aggiuntivi. Per ulteriori informazioni, vedere la pagina relativa alla [fatturazione dell'account di archiviazione](#storage-account-billing).

## <a name="encryption"></a>Crittografia

Tutti i dati nell'account di archiviazione vengono crittografati sul lato del servizio. Per altre informazioni sulla crittografia, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Endpoint dell'account di archiviazione

Un account di archiviazione offre uno spazio dei nomi univoco in Azure per i dati. Tutti gli oggetti archiviati in Archiviazione di Azure hanno un indirizzo che include il nome univoco dell'account. La combinazione del nome dell'account e dell'endpoint di servizio di Archiviazione di Azure costituisce gli endpoint per l'account di archiviazione.

La tabella seguente elenca gli endpoint per ognuno dei servizi di archiviazione di Azure.

| Servizio di archiviazione | Endpoint |
|--|--|
| Archiviazione BLOB | `https://<storage-account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| File di Azure | `https://<storage-account>.file.core.windows.net` |
| Archiviazione code | `https://<storage-account>.queue.core.windows.net` |
| Archiviazione tabelle | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> Gli account di archiviazione BLOB e BLOB in blocchi espongono solo l'endpoint del servizio BLOB.

Costruire l'URL per accedere a un oggetto in un account di archiviazione accodando la posizione dell'oggetto nell'account di archiviazione all'endpoint. Ad esempio, il formato di un indirizzo è simile al seguente: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

È anche possibile configurare l'account di archiviazione per l'uso di un dominio personalizzato per i BLOB. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato per l'account di Archiviazione di Azure](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Controllare l'accesso ai dati dell'account

Per impostazione predefinita, i dati nel proprio account sono accessibili solo all'utente, ovvero al proprietario dell'account. È possibile controllare chi può accedere ai dati e quali autorizzazioni assegnare.

Ogni richiesta effettuata all'account di archiviazione deve essere autorizzata. Al livello del servizio, la richiesta deve includere un'intestazione di *autorizzazione* valida. In particolare, questa intestazione include tutte le informazioni necessarie per la convalida della richiesta da parte del servizio prima dell'esecuzione.

È possibile concedere l'accesso ai dati nell'account di archiviazione usando uno degli approcci seguenti:

- **Azure Active Directory:** Usare le credenziali di Azure Active Directory (Azure AD) per autenticare un utente, un gruppo o un'altra identità per l'accesso ai dati BLOB e di Accodamento. Se l'autenticazione di un'identità ha esito positivo, Azure AD restituisce un token da usare per l'autorizzazione della richiesta con l'archiviazione BLOB di Azure o con l'archiviazione code. Per altre informazioni, vedere [Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory](storage-auth-aad.md).
- **Autorizzazione con chiave condivisa:** usare la chiave di accesso dell'account di archiviazione per creare una stringa di connessione usata dall'applicazione in fase di esecuzione per accedere ad Archiviazione di Azure. I valori nella stringa di connessione vengono usati per creare l'intestazione *Autorizzazione* che viene passata ad Archiviazione di Azure. Per altre informazioni, vedere [Configurare le stringhe di connessione di Archiviazione di Azure](storage-configure-connection-string.md).
- **Firma di accesso condiviso:** Una firma di accesso condiviso è un token che consente l'accesso delegato alle risorse nell'account di archiviazione. Il token SAS incapsula tutte le informazioni necessarie per autorizzare una richiesta ad archiviazione di Azure nell'URL. Quando si crea una firma di accesso condiviso, è possibile specificare le autorizzazioni concesse dalla firma di accesso condiviso a una risorsa e l'intervallo in base al quale sono valide le autorizzazioni. Un token di firma di accesso condiviso può essere firmato con Azure AD credenziali o con chiave condivisa. Per altre informazioni, vedere [concedere l'accesso limitato alle risorse di archiviazione di Azure usando le firme di accesso condiviso (SAS)](storage-sas-overview.md).

> [!NOTE]
> L'autenticazione degli utenti o delle applicazioni tramite le credenziali di Azure AD offre un livello superiore di sicurezza e facilità d'uso rispetto ad altri metodi di autorizzazione. Mentre con le applicazioni è possibile continuare a usare l'autorizzazione con chiave condivisa, l'uso di Azure AD consente di evitare la necessità di archiviare la chiave di accesso dell'account con il codice. È anche possibile continuare a usare le firme di accesso condiviso per concedere accesso specifico alle risorse dell'account di archiviazione, ma Azure AD offre funzionalità simili senza la necessità di gestire i token di firma di accesso condiviso o di occuparsi della revoca di una di firma di accesso condiviso compromessa.
>
> Microsoft consiglia di usare Azure AD autorizzazione per le applicazioni di Accodamento e BLOB di archiviazione di Azure, quando possibile.

## <a name="copying-data-into-a-storage-account"></a>Copia dei dati in un account di archiviazione

Microsoft fornisce utilità e librerie per l'importazione dei dati da dispositivi di archiviazione locali o provider di archiviazione cloud di terze parti. La soluzione da usare dipende dalla quantità di dati che si sta trasferendo.

Quando effettua l'aggiornamento a un account per utilizzo generico v2 da un account per utilizzo generico v1 o un account di archiviazione BLOB, la migrazione dei dati viene eseguita automaticamente. Microsoft consiglia questo percorso per l'aggiornamento dell'account. Tuttavia, se si decide di spostare i dati da un account per utilizzo generico V1 a un account di archiviazione BLOB, i dati verranno migrati manualmente, usando gli strumenti e le librerie descritti di seguito.

### <a name="azcopy"></a>AzCopy

AzCopy è un'utilità da riga di comando Windows progettata per offrire prestazioni elevate di copia dei dati da e verso Archiviazione di Azure. È possibile usare AzCopy per copiare i dati in un account di archiviazione BLOB da un account di archiviazione per utilizzo generico esistente o per caricare i dati da dispositivi di archiviazione locali. Per altre informazioni, vedere [trasferire dati con l'utilità AzCopy Command-Line](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Libreria di spostamento dei dati

La libreria di spostamento dei dati di Archiviazione di Azure per .NET si basa sul framework di spostamento dei dati principali alla base di AzCopy. La libreria è progettata per operazioni di trasferimento dei dati affidabili, semplici e a prestazioni elevate simili a quelle di AzCopy. È possibile usare la libreria per lo spostamento dei dati per sfruttare le funzionalità di AzCopy in modo nativo. Per altre informazioni, vedere [libreria di spostamento dei dati di archiviazione di Azure per .NET](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST o libreria client

È possibile creare un'applicazione personalizzata per eseguire la migrazione dei dati da un account di archiviazione per utilizzo generico V1 a un account di archiviazione BLOB. Usare una delle librerie client di Azure o l'API REST dei servizi di archiviazione di Azure. Archiviazione di Azure fornisce librerie client avanzate per più linguaggi e piattaforme, ad esempio .NET, Java, C++, Node.js, PHP, Ruby e Python. Le librerie client offrono funzionalità avanzate, ad esempio la logica di ripetizione dei tentativi, la registrazione e i caricamenti paralleli. È possibile sviluppare usando direttamente l'API REST, che può essere chiamata da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS.

Per altre informazioni sull'API REST di Archiviazione di Azure, vedere [Informazioni di riferimento sulle API REST dei servizi di archiviazione di Azure](/rest/api/storageservices/).

> [!IMPORTANT]
> I BLOB crittografati con la crittografia lato client archiviano i metadati correlati alla crittografia con il BLOB. Se si copia un BLOB crittografato con la crittografia lato client, verificare che l'operazione di copia mantenga i metadati del BLOB e soprattutto quelli correlati alla crittografia. Se si copia un BLOB senza i metadati di crittografia, il contenuto del BLOB non sarà più recuperabile. Per altre informazioni sui metadati correlati alla crittografia, vedere [Crittografia lato client e Insieme di credenziali chiave Azure per Archiviazione di Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Fatturazione dell'account di archiviazione

Fatturazione di archiviazione di Azure in base all'utilizzo dell'account di archiviazione. Tutti gli oggetti in un account di archiviazione vengono fatturati insieme come gruppo. I costi di archiviazione vengono calcolati in base ai fattori seguenti:

- L'**area** si riferisce all'area geografica in cui si trova l'account.
- Il **tipo di account** si riferisce al tipo di account di archiviazione in uso.
- Il **livello di accesso** si riferisce al criterio di utilizzo dei dati specificato per l'account di archiviazione BLOB o v2 per utilizzo generico.
- La **capacità** si riferisce alla quantità di allocazione dell'account di archiviazione usata per archiviare i dati.
- La **replica** determina il numero di copie dei dati conservate contemporaneamente e le posizioni di archiviazione.
- Le **transazioni** si riferiscono a tutte le operazioni di lettura e scrittura in Archiviazione di Azure.
- I **dati in uscita** si riferiscono ai dati trasferiti all'esterno di un'area di Azure. Quando un'applicazione che non è in esecuzione nella stessa area geografica accede ai dati dell'account di archiviazione, viene addebitata un importo per i dati in uscita. Per informazioni sull'uso dei gruppi di risorse per raggruppare i dati e i servizi nella stessa area per ridurre gli addebiti per il traffico in uscita, vedere [Informazioni sul gruppo di risorse di Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

La pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) fornisce informazioni dettagliate sui prezzi in base a tipo di account, capacità di archiviazione, replica e transazioni. Nella pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/) vengono fornite informazioni dettagliate sui prezzi per il trasferimento dei dati in uscita. È possibile usare la pagina per il [calcolo dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) per stimare i costi.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account di archiviazione](storage-account-create.md)
- [Creare un account di archiviazione BLOB in blocchi](../blobs/storage-blob-create-account-block-blob.md)
- [Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico v2](storage-account-upgrade.md)
- [consente di ripristinare un account di archiviazione eliminato](storage-account-recover.md)