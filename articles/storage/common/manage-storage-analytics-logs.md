---
title: Abilitare e gestire i log di Analisi archiviazione di Azure (versione classica) | Microsoft Docs
description: Informazioni su come monitorare un account di archiviazione in Azure usando Analisi archiviazione di Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: bc6632b55ba8fd90317a8b5046a3e84d863bf0ef
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221715"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Abilitare e gestire i log di Analisi archiviazione di Azure (versione classica)

[Analisi archiviazione di Azure](storage-analytics.md) fornisce log per BLOB, code e tabelle. È possibile usare la [portale di Azure](https://portal.azure.com) per configurare i log registrati per l'account. Questo articolo illustra come abilitare e gestire i log. Per informazioni su come abilitare le metriche, vedere [Enable and manage analisi archiviazione di Azure Metrics (classico)](storage-monitor-storage-account.md).  L'analisi e l'archiviazione dei dati di monitoraggio nel portale di Azure sono i costi associati. Per altre informazioni, vedere [Analisi archiviazione](storage-analytics.md).

> [!NOTE]
> È consigliabile usare i log di archiviazione di Azure in monitoraggio di Azure invece dei log Analisi archiviazione. I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Questa anteprima Abilita i log per i BLOB (che includono Azure Data Lake Storage Gen2), file, code e tabelle. Per altre informazioni, vedere gli articoli seguenti:
>
> - [Monitoraggio dell'archiviazione BLOB di Azure](../blobs/monitor-blob-storage.md)
> - [File di Azure di monitoraggio](../files/storage-files-monitoring.md)
> - [Monitoraggio dell'archiviazione code di Azure](../queues/monitor-queue-storage.md)
> - [Monitoraggio dell'archiviazione tabelle di Azure](../tables/monitor-table-storage.md)

Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Abilitare i log

È possibile impostare Archiviazione di Azure in modo da salvare i log di diagnostica per le richieste di lettura, scrittura ed eliminazione per i servizi BLOB, tabelle e di accodamento. I criteri di conservazione dati impostati si applicano anche a questi log.

> [!NOTE]
> File di Azure supporta attualmente le metriche di Analisi archiviazione, ma non supporta la registrazione del Analisi archiviazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Account di archiviazione** e quindi il nome dell'account di archiviazione per aprire il relativo pannello.

2. Selezionare **impostazioni di diagnostica (versione classica)** nella sezione **monitoraggio (classico)** del pannello del menu.

    ![Voce di menu Diagnostica in Monitoraggio nel portale di Azure.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Verificare che l'opzione **Stato** sia impostata su **Attivato** e selezionare i **servizi** per cui si vuole abilitare la registrazione.

   > [!div class="mx-imgBorder"]
   > ![Configurare la registrazione nel portale di Azure.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. Verificare che la casella di controllo **Elimina dati** sia selezionata.  Impostare quindi il numero di giorni per cui si desidera mantenere i dati di log spostando il dispositivo di scorrimento sotto la casella di controllo o modificando direttamente il valore visualizzato nella casella di testo accanto al controllo dispositivo di scorrimento. L'impostazione predefinita per i nuovi account di archiviazione è sette giorni. Se non si desidera impostare criteri di conservazione, immettere zero. Se non sono presenti criteri di conservazione, è necessario eliminare i dati di log.

   > [!WARNING]
   > I log vengono archiviati come dati nell'account. i dati di log possono accumularsi nell'account nel tempo, che può aumentare il costo di archiviazione. Se sono necessari dati di log solo per un breve periodo di tempo, è possibile ridurre i costi modificando i criteri di conservazione dei dati. I dati di log obsoleti (dati antecedenti ai criteri di conservazione) vengono eliminati dal sistema. Si consiglia di impostare criteri di conservazione in base al periodo di tempo in cui si desidera conservare i dati di log per l'account. Per altre informazioni, vedere [Fatturazione delle metriche di archiviazione](storage-analytics-metrics.md#billing-on-storage-metrics).

4. Fare clic su **Salva**.

   I log di diagnostica sono salvati in un contenitore blob denominato *$logs* nell'account di archiviazione. È possibile visualizzare i dati di log usando uno Storage Explorer come il [Microsoft Azure Storage Explorer](https://storageexplorer.com)o a livello di codice usando la libreria client di archiviazione o PowerShell.

   Per informazioni sull'accesso al contenitore $logs, vedere [Registrazione di Analisi archiviazione](storage-analytics-logging.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Aprire una finestra dei comandi di Windows PowerShell.

2. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

3. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

5. Ottenere il contesto dell’account di archiviazione che definisce l'account di archiviazione che si intende usare.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Sostituire il valore segnaposto `<resource-group-name>` con il nome del proprio gruppo di risorse.

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione. 

6. Usare **set-AzStorageServiceLoggingProperty** per modificare le impostazioni correnti del log. I cmdlet che controllano Registrazione archiviazione usano il parametro **LoggingOperations**, costituito da una stringa contenente un elenco separato da virgole dei tipi di richiesta da registrare. I tre tipi possibili di richiesta sono **read**, **write** e **delete**. Per disattivare la registrazione, usare il valore **none** per il parametro **LoggingOperations**.  

   Il seguente comando attiva la registrazione per le richieste di lettura, scrittura ed eliminazione per il Servizio di accodamento nell'account di archiviazione predefinito, con periodo di conservazione di cinque giorni:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > I log vengono archiviati come dati nell'account. i dati di log possono accumularsi nell'account nel tempo, che può aumentare il costo di archiviazione. Se sono necessari dati di log solo per un breve periodo di tempo, è possibile ridurre i costi modificando i criteri di conservazione dei dati. I dati di log obsoleti (dati antecedenti ai criteri di conservazione) vengono eliminati dal sistema. Si consiglia di impostare criteri di conservazione in base al periodo di tempo in cui si desidera conservare i dati di log per l'account. Per altre informazioni, vedere [Fatturazione delle metriche di archiviazione](storage-analytics-metrics.md#billing-on-storage-metrics).
   
   Il seguente comando disattiva la registrazione per il servizio tabelle nell'account di archiviazione predefinito:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Per informazioni su come configurare i cmdlet di Azure PowerShell per usare la sottoscrizione di Azure e su come selezionare l'account di archiviazione predefinito da utilizzare, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Modificare il periodo di memorizzazione dei dati del log

I dati di log possono accumularsi nell'account nel tempo, che può aumentare il costo di archiviazione. Se sono necessari dati di log solo per un breve periodo di tempo, è possibile ridurre i costi modificando il periodo di conservazione dei dati del log. Se, ad esempio, sono necessari log solo per tre giorni, impostare il periodo di conservazione dei dati di log su un valore di `3` . In questo modo i log verranno eliminati automaticamente dall'account dopo 3 giorni. In questa sezione viene illustrato come visualizzare il periodo di conservazione dei dati di log corrente, quindi aggiornare tale periodo se si desidera eseguire questa operazione.

> [!NOTE]
> Questi passaggi si applicano solo agli account in cui non è abilitata l'impostazione **spazio dei nomi gerarchico** . Se è stata abilitata l'impostazione per l'account, l'impostazione per i giorni di conservazione non è ancora supportata. Sarà invece necessario eliminare i log manualmente usando qualsiasi strumento supportato, ad esempio Azure Storage Explorer, REST o un SDK. Per trovare i log nell'account di archiviazione, vedere [come vengono archiviati i log](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Account di archiviazione** e quindi il nome dell'account di archiviazione per aprire il relativo pannello.
2. Selezionare **impostazioni di diagnostica (versione classica)** nella sezione **monitoraggio (classico)** del pannello del menu.

    ![Voce di menu di diagnostica in monitoraggio nel portale di Azure](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Verificare che la casella di controllo **Elimina dati** sia selezionata.  Impostare quindi il numero di giorni per cui si desidera mantenere i dati di log spostando il dispositivo di scorrimento sotto la casella di controllo o modificando direttamente il valore visualizzato nella casella di testo accanto al controllo dispositivo di scorrimento.

   > [!div class="mx-imgBorder"]
   > ![Modificare il periodo di memorizzazione nella portale di Azure](./media/manage-storage-analytics-logs/modify-retention-period.png)

   Il numero predefinito di giorni per i nuovi account di archiviazione è di sette giorni. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i dati di monitoraggio dovranno essere eliminati manualmente.
   
4. Fare clic su **Salva**.

   I log di diagnostica sono salvati in un contenitore blob denominato *$logs* nell'account di archiviazione. È possibile visualizzare i dati di log usando uno Storage Explorer come il [Microsoft Azure Storage Explorer](https://storageexplorer.com)o a livello di codice usando la libreria client di archiviazione o PowerShell.

   Per informazioni sull'accesso al contenitore $logs, vedere [Registrazione di Analisi archiviazione](storage-analytics-logging.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Aprire una finestra dei comandi di Windows PowerShell.

2. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

3. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

5. Ottenere il contesto dell'account di archiviazione che definisce l'account di archiviazione.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Sostituire il valore segnaposto `<resource-group-name>` con il nome del proprio gruppo di risorse.

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione. 

6. Usare [Get-AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageserviceloggingproperty) per visualizzare i criteri di conservazione dei log correnti. Nell'esempio seguente viene stampato nella console il periodo di conservazione per i servizi di archiviazione BLOB e di Accodamento.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   Nell'output della console, il periodo di memorizzazione viene visualizzato sotto l' `RetentionDays` intestazione di colonna.

   > [!div class="mx-imgBorder"]
   > ![Criteri di conservazione nell'output di PowerShell](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. Usare [set-AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageserviceloggingproperty) per modificare il periodo di memorizzazione. Nell'esempio seguente il periodo di conservazione viene modificato in 4 giorni.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Per informazioni su come configurare i cmdlet di Azure PowerShell per usare la sottoscrizione di Azure e su come selezionare l'account di archiviazione predefinito da utilizzare, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Nell'esempio seguente viene stampato nella console il periodo di conservazione per i servizi di archiviazione BLOB e di Accodamento.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

Nell'esempio seguente il periodo di conservazione viene modificato in 4 giorni. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Nell'esempio seguente viene stampato nella console il periodo di conservazione per i servizi di archiviazione BLOB e di Accodamento.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

Nell'esempio seguente viene modificato il periodo di memorizzazione per i log dei servizi BLOB e di archiviazione di Accodamento in 4 giorni. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>Verificare che i dati di log vengano eliminati

È possibile verificare che i log vengano eliminati visualizzando il contenuto del `$logs` contenitore dell'account di archiviazione. Nell'immagine seguente viene illustrato il contenuto di una cartella nel `$logs` contenitore. La cartella corrisponde al 2021 gennaio e ogni cartella contiene i log per un giorno. Se il giorno attuale è il 29 gennaio 2021 e i criteri di conservazione sono impostati su un solo giorno, la cartella deve contenere i log per un solo giorno.

> [!div class="mx-imgBorder"]
> ![Elenco delle cartelle dei log nel portale di Azure](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Visualizzare i dati del log

 Per visualizzare e analizzare i dati di log, è necessario scaricare su un computer locale i BLOB contenenti i dati di log a cui si è interessati. Molti strumenti di esplorazione delle informazioni archiviate consentono di scaricare i BLOB dall'account di archiviazione. Per scaricare i dati di log è possibile anche usare lo strumento di Azure per la copia [AzCopy](storage-use-azcopy-v10.md) fornito dal team di Archiviazione di Azure.  
 
>[!NOTE]
> Il contenitore `$logs` non è integrato con Griglia di eventi, quindi non si riceveranno notifiche quando vengono scritti i file di log. 

 Per scaricare soltanto i dati di log a cui si è interessati e per evitare di scaricare più volte gli stessi dati, procedere come segue:  

-   Usare la convenzione di denominazione di data e ora per i BLOB contenenti dati di log, in modo da tenere traccia di quelli già scaricati a scopo di analisi ed evitare di ripetere il download.  

-   Usare i metadati dei BLOB contenenti dati di log per identificare il periodo specifico di conservazione dei dati e individuare esattamente il BLOB che è necessario scaricare.  

Per iniziare a usare AzCopy, vedere [Introduzione ad AzCopy](storage-use-azcopy-v10.md) 

Nell'esempio seguente viene illustrato come è possibile scaricare i dati di log per il servizio di accodamento per le ore a partire dalle 09, le 10.00 e le 11.00 del 20 maggio 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Per altre informazioni su come scaricare file specifici, vedere [scaricare i BLOB dall'archiviazione BLOB di Azure con AzCopy V10](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Una volta scaricati i dati di log, è possibile visualizzare le voci di log nei file. Questi file di log utilizzano un formato di testo delimitato che molti strumenti di lettura log sono in grado di analizzare (per ulteriori informazioni, vedere la Guida [monitoraggio, diagnosi e risoluzione dei problemi archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)). Altri strumenti dispongono di funzionalità differenti per la formattazione, il filtro, l'ordinamento e la ricerca nei contenuti dei file di log. Per altre informazioni sul formato dei file di log di Registrazione archiviazione, vedere [Formato del log di Analisi archiviazione](/rest/api/storageservices/storage-analytics-log-format) e [Operazioni e messaggi di stato registrati di Analisi archiviazione](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni su Analisi archiviazione, vedere [analisi archiviazione](storage-analytics.md) per analisi archiviazione.
* [Configurare analisi archiviazione metrica](storage-monitor-storage-account.md).
* Per altre informazioni sull'uso di un linguaggio .NET per configurare Registrazione archiviazione, vedere [Riferimenti alla libreria del client di archiviazione](/previous-versions/azure/dn261237(v=azure.100)). 
* Per informazioni generali sulla configurazione di Registrazione archiviazione mediante l'API REST, vedere [Abilitazione e configurazione di Analisi archiviazione](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).
* Altre informazioni sul formato dei log di Analisi archiviazione. Vedere [analisi archiviazione formato di log](/rest/api/storageservices/storage-analytics-log-format).