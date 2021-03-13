---
title: Registrazione di Analisi archiviazione di Azure
description: Usare Analisi archiviazione per registrare i dettagli sulle richieste di archiviazione di Azure. Vedere quali richieste vengono registrate, come vengono archiviati i log, come abilitare la registrazione dell'archiviazione e altro ancora.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: a5d1d6af68fcbd6a5822b2652ee79c464d02241f
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200773"
---
# <a name="azure-storage-analytics-logging"></a>Registrazione di Analisi archiviazione di Azure

Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo. Ciò significa che la maggior parte delle richieste comporterà un record di log, ma la completezza e la tempestività dei log di Analisi archiviazione non sono garantite. 

> [!NOTE]
> È consigliabile usare i log di archiviazione di Azure in monitoraggio di Azure invece dei log Analisi archiviazione. I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Questa anteprima Abilita i log per i BLOB (che includono Azure Data Lake Storage Gen2), file, code e tabelle. Per altre informazioni, vedere gli articoli seguenti:
>
> - [Monitoraggio dell'archiviazione BLOB di Azure](../blobs/monitor-blob-storage.md)
> - [File di Azure di monitoraggio](../files/storage-files-monitoring.md)
> - [Monitoraggio dell'archiviazione code di Azure](../queues/monitor-queue-storage.md)
> - [Monitoraggio dell'archiviazione tabelle di Azure](../tables/monitor-table-storage.md)

 La registrazione di Analisi archiviazione non è abilitata per impostazione predefinita per l'account di archiviazione. È possibile abilitarlo nella [portale di Azure](https://portal.azure.com/) o tramite PowerShell o l'interfaccia della riga di comando di Azure. Per istruzioni dettagliate, vedere [Enable and manage analisi archiviazione di Azure logs (classico)](manage-storage-analytics-logs.md). 

È anche possibile abilitare i log di Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Usare le operazioni [Recupera proprietà del servizio BLOB](/rest/api/storageservices/Blob-Service-REST-API), [Recupera proprietà del servizio di accodamento](/rest/api/storageservices/Get-Queue-Service-Properties), [Recupera proprietà del servizio tabelle](/rest/api/storageservices/Get-Table-Service-Properties) per abilitare Analisi archiviazione per ciascun servizio. Per un esempio in cui si abilitano i log di Analisi archiviazione con .NET, vedere [abilitare i log](manage-storage-analytics-logs.md)

 Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Se, ad esempio, un account di archiviazione presenta un'attività nell'endpoint BLOB ma non negli endpoint tabella o coda, saranno creati solo log relativi al servizio BLOB.

> [!NOTE]
>  La registrazione dell'analisi archiviazione è attualmente disponibile solo per servizi BLOB, code e tabelle. La registrazione Analisi archiviazione è disponibile anche per gli account [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) ad alte prestazioni. Tuttavia, non è disponibile per gli account per utilizzo generico v2 ad alte prestazioni.

## <a name="requests-logged-in-logging"></a>Registrazione di richieste registrate
### <a name="logging-authenticated-requests"></a>Registrazione delle richieste autenticate

 Vengono registrati i seguenti tipi di richieste autenticate:

- Richieste riuscite
- Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo
- Richieste che usano una firma di accesso condiviso o OAuth, incluse le richieste riuscite e non riuscite
- Richieste ai dati di analisi

  Le richieste eseguite dalla stessa Analisi archiviazione, ad esempio, la creazione oppure l'eliminazione di log, non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate in Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato log Analisi archiviazione](/rest/api/storageservices/storage-analytics-log-format).

### <a name="logging-anonymous-requests"></a>Registrazione di richieste anonime

 Vengono registrati i seguenti tipi di richieste anonime:

- Richieste riuscite
- Errori server
- Errori di timeout per client e server
- Richieste GET non riuscite con codice di errore 304 (non modificate)

  Tutte le altre richieste anonime non riuscite non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate in Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato log Analisi archiviazione](/rest/api/storageservices/storage-analytics-log-format).

## <a name="how-logs-are-stored"></a>Come vengono archiviati i log

Tutti i log vengono archiviati in Blob in blocchi in un contenitore denominato `$logs`, che viene creato automaticamente quando si abilita Analisi archiviazione per un account di archiviazione. Il contenitore `$logs` si trova nello spazio dei nomi Blob dell'account di archiviazione, ad esempio: `http://<accountname>.blob.core.windows.net/$logs`. Questo contenitore non può essere eliminato una volta abilitata Analisi di archiviazione, sebbene sia possibile eliminarne il contenuto. Se si usa lo strumento di esplorazione dell'archiviazione per passare direttamente al contenitore, vengono visualizzati tutti i BLOB contenenti i dati di registrazione.

> [!NOTE]
>  Il contenitore `$logs` non viene visualizzato quando viene eseguita un'operazione di inclusione nell'elenco del contenitore, ad esempio, l'operazione ListContainers. È necessario effettuare l'accesso diretto. Ad esempio, è possibile usare l'operazione ListBlobs per accedere ai BLOB nel contenitore `$logs`.

Nel momento in cui vengono registrate le richieste, Analisi archiviazione carica i risultati intermedi come blocchi. Analisi archiviazione invierà periodicamente questi blocchi e li renderà disponibili come BLOB. A causa della frequenza con cui il servizio di archiviazione scarica i writer dei log, la visualizzazione dei dati dei BLOB presenti nel contenitore **$logs** può richiedere fino a un'ora. Per i log creati nella stessa ora possono esistere record duplicati. È possibile determinare se un record è un duplicato controllandone il numero **RequestId** e **Operation**.

Se ogni ora vengono registrati ingenti volumi di dati memorizzati in diversi file, è possibile usare i metadati dei BLOB per determinare i dati contenuti nel log esaminando i campi dei metadati. Questa procedura può rivelarsi utile perché in alcune occasioni i dati vengono scritti nei file di log con un ritardo: i metadati dei BLOB forniscono un'indicazione più accurata del contenuto del BLOB rispetto al nome di quest'ultimo.

La maggior parte degli strumenti di esplorazione delle informazioni archiviate consente di visualizzare i metadati dei BLOB. È possibile anche leggere queste informazioni a livello di codice o usando PowerShell. Il seguente frammento di codice PowerShell è un esempio di filtro dell'elenco di BLOB di log in base al nome per specificare un'ora e in base ai metadati per identificare soltanto i log contenenti operazioni di **scrittura**.  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'blob/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Per informazioni sulla creazione di elenchi di BLOB a livello di codice, vedere [Enumerazione di risorse BLOB](/rest/api/storageservices/Enumerating-Blob-Resources) e [Impostazione e recupero di proprietà e metadati per le risorse BLOB](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources).  

### <a name="log-naming-conventions"></a>Convenzioni di denominazione dei log

 Ciascun log verrà scritto nel seguente formato:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 Nella tabella seguente vengono descritti i singoli attributi del nome del log:

|Attributo|Descrizione|
|---------------|-----------------|
|`<service-name>`|Nome del servizio di archiviazione. Ad esempio: `blob`, `table` o `queue`|
|`YYYY`|Anno a quattro cifre per il log. Ad esempio: `2011`|
|`MM`|Mese a due cifre per il log. Ad esempio: `07`|
|`DD`|Giorno a due cifre per il log. Ad esempio: `31`|
|`hh`|Ora a due cifre che indica l'ora di inizio per i log, nel formato UTC 24 ore. Ad esempio: `18`|
|`mm`|Numero a due cifre che indica il minuto di inizio per i log. **Nota:**  Questo valore non è supportato nella versione corrente di Analisi archiviazione, e il relativo valore sarà sempre `00`.|
|`<counter>`|Contatore base zero con sei cifre che indica il numero di BLOB di log generati per il servizio di archiviazione in un'ora. Questo contatore parte da `000000`. Ad esempio: `000001`|

 Di seguito è riportato un nome di log completo di esempio che combina gli esempi precedenti:

 `blob/2011/07/31/1800/000001.log`

 Di seguito è riportato un URI di esempio che può essere utilizzato per accedere al log precedente:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando viene registrata una richiesta di archiviazione, il nome log risultante è correlato all'ora in cui è stata completata l'operazione richiesta. Ad esempio, se una richiesta GetBlob è stata completata alle 6:30 PM del 7/31/2011, il log viene scritto con il prefisso seguente: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadati dei log

 Tutti i BLOB dei log vengono archiviati con i metadati che possono essere utilizzati per identificare i dati di registrazione contenuti nei BLOB. Nella tabella seguente sono descritti i singoli attributi dei metadati:

|Attributo|Descrizione|
|---------------|-----------------|
|`LogType`|Descrive se il log contiene informazioni relative alle operazioni di lettura, scrittura o eliminazione. Questo valore può includere un solo tipo o una combinazione di tutti e tre, separati da virgola.<br /><br /> Esempio 1: `write`<br /><br /> Esempio 2: `read,write`<br /><br /> Esempio 3: `read,write,delete`|
|`StartTime`|L'ora meno recente di una voce del log, sotto forma di `YYYY-MM-DDThh:mm:ssZ`. Ad esempio: `2011-07-31T18:21:46Z`|
|`EndTime`|L'ora più recente di una voce del log, sotto forma di `YYYY-MM-DDThh:mm:ssZ`. Ad esempio: `2011-07-31T18:22:09Z`|
|`LogVersion`|Versione del formato del log.|

 Nell'elenco seguente vengono visualizzati metadati di esempio completi utilizzando i precedenti esempi:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`


## <a name="next-steps"></a>Passaggi successivi

* [Abilitare e gestire i log di Analisi archiviazione di Azure (versione classica)](manage-storage-analytics-logs.md)
* [Formato log Analisi archiviazione](/rest/api/storageservices/storage-analytics-log-format)
* [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Metriche di Analisi archiviazione (versione classica)](storage-analytics-metrics.md)
