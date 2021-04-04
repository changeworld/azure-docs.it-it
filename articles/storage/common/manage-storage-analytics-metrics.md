---
title: Abilitare e gestire le metriche di Analisi archiviazione di Azure (versione classica) | Microsoft Docs
description: Informazioni su come abilitare, modificare e visualizzare Analisi archiviazione di Azure metrica.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221575"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Abilitare e gestire le metriche di Analisi archiviazione di Azure (versione classica)

[Analisi archiviazione di Azure](storage-analytics.md) fornisce le metriche per tutti i servizi di archiviazione per BLOB, code e tabelle. È possibile usare la [portale di Azure](https://portal.azure.com) per configurare le metriche registrate per l'account e configurare i grafici che forniscono rappresentazioni visive dei dati di metrica. Questo articolo illustra come abilitare e gestire le metriche. Per informazioni su come abilitare i log, vedere [Enable and manage analisi archiviazione di Azure logs (classico)](manage-storage-analytics-logs.md).

È consigliabile consultare [Monitoraggio di Azure per Archiviazione](../../azure-monitor/insights/storage-insights-overview.md) (anteprima). Si tratta di una funzionalità di Monitoraggio di Azure che offre un monitoraggio completo degli account di Archiviazione di Azure offrendo una visualizzazione unificata delle prestazioni, della capacità e della disponibilità dei servizi di Archiviazione di Azure. Non è necessario abilitare o configurare elementi ed è possibile visualizzare immediatamente queste metriche dai grafici interattivi predefiniti e da altre visualizzazioni incluse.

> [!NOTE]
> All'esame dei dati di monitoraggio nel portale di Azure sono associati costi. Per altre informazioni, vedere [Analisi archiviazione](storage-analytics.md).
>
> Gli account di archiviazione BLOB in blocchi di prestazioni Premium non supportano la metrica Analisi archiviazione. Se si vogliono visualizzare le metriche con gli account di archiviazione BLOB in blocchi di prestazioni Premium, provare a usare le [metriche di archiviazione di Azure in monitoraggio di Azure](../blobs/monitor-blob-storage.md).
>
> Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Abilitazione di metriche

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Account di archiviazione** e quindi il nome dell'account di archiviazione per aprire il dashboard dell'account.

2. Selezionare **impostazioni di diagnostica (versione classica)** nella sezione **monitoraggio (classico)** del pannello del menu.
   
   ![Screenshot che evidenzia l'opzione impostazioni di diagnostica (versione classica) nella sezione monitoraggio (classica).](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. Selezionare il **tipo** di dati di metrica per ogni **servizio** che si vuole monitorare e i **criteri di conservazione** per i dati. È anche possibile disabilitare il monitoraggio impostando **Stato** su **Disattivato**.

   > [!div class="mx-imgBorder"]
   > ![Configurare la registrazione nel portale di Azure.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Per impostare i criteri di conservazione dei dati, spostare il dispositivo di scorrimento **Conservazione (in giorni)** oppure immettere il numero desiderato di giorni, da 1 a 365. L'impostazione predefinita per i nuovi account di archiviazione è sette giorni. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i dati di monitoraggio dovranno essere eliminati manualmente.

   > [!WARNING]
   > Metriche vengono archiviati come dati nell'account. I dati delle metriche possono accumularsi nell'account nel tempo, che può aumentare il costo di archiviazione. Se sono necessari dati sulle metriche solo per un breve periodo di tempo, è possibile ridurre i costi modificando i criteri di conservazione dei dati. I dati di metrica obsoleti (dati antecedenti ai criteri di conservazione) vengono eliminati dal sistema. Si consiglia di impostare criteri di conservazione in base al periodo di tempo in cui si vogliono conservare i dati di metrica per l'account. Per altre informazioni, vedere [Fatturazione delle metriche di archiviazione](storage-analytics-metrics.md#billing-on-storage-metrics).
   >

4. Al termine della configurazione del monitoraggio, selezionare **Salva**.

Un set predefinito di metriche viene visualizzato nei grafici nel pannello **Panoramica** , oltre che nel pannello **metriche (classico)** . Dopo aver abilitato le metriche per un servizio, potrebbe trascorrere fino a un'ora prima che i dati vengano visualizzati nei grafici. È possibile selezionare **Modifica** in qualsiasi grafico delle metriche per configurare le metriche visualizzate nel grafico.

È possibile disabilitare la raccolta e la registrazione delle metriche impostando **Stato** su **Disattivato**.

> [!NOTE]
> Archiviazione di Azure usa l'[archivio tabelle](storage-introduction.md#table-storage) per archiviare le metriche per l'account di archiviazione e archivia le metriche in tabelle dell'account. Per altre informazioni, vedere [Come vengono archiviate le metriche](storage-analytics-metrics.md#how-metrics-are-stored).

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

6. È possibile usare PowerShell nel computer locale per configurare le metriche di archiviazione nell'account di archiviazione. Usare il cmdlet Azure PowerShell **set-AzStorageServiceMetricsProperty** per modificare le impostazioni correnti. 

   Il comando seguente attiva le metriche al minuto per il servizio BLOB nell'account di archiviazione con il periodo di memorizzazione impostato su cinque giorni.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   Questo cmdlet usa i parametri seguenti:  

   - **ServiceType**: i valori possibili sono **Blob**, **Queue**, **Table** e **File**.
   - **MetricsType**: i valori possibili sono **Hour** e **Minute**.  
   - **MetricsLevel**: I valori possibili sono:
      - **Nessuna**: disattiva il monitoraggio.
      - **Servizio**: raccoglie metriche come ingresso e uscita, disponibilità, latenza e percentuali di successo aggregate per i servizi BLOB, Coda, Tabella e File.
      - **ServiceAndApi**: oltre alle metriche di servizio, raccoglie lo stesso set di metriche per ogni operazione di archiviazione eseguita nell'API del servizio Archiviazione di Azure.

   Il seguente comando recupera il livello delle metriche orarie corrente e i giorni di memorizzazione per il servizio BLOB nell'account di archiviazione predefinito:  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Per informazioni su come configurare i cmdlet di Azure PowerShell per usare la sottoscrizione di Azure e su come selezionare l'account di archiviazione predefinito da utilizzare, vedere [Installare e configurare Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

Per altre informazioni sull'uso di un linguaggio .NET per configurare le metriche di archiviazione, vedere [API di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage).  

Per informazioni generali sulla configurazione delle metriche di archiviazione mediante l'API REST, vedere [Abilitazione e configurazione di Analisi archiviazione](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Per altre informazioni sull'uso di un linguaggio .NET per configurare le metriche di archiviazione, vedere [API di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage).  

Per informazioni generali sulla configurazione delle metriche di archiviazione mediante l'API REST, vedere [Abilitazione e configurazione di Analisi archiviazione](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Visualizzare le metriche in un grafico

Una volta configurate le metriche dell’analisi di archiviazione per monitorare l'account di archiviazione, l’analisi di archiviazione registra le metriche in un set di tabelle note nell'account di archiviazione. È possibile configurare i grafici per visualizzare le metriche orarie nell' [portale di Azure](https://portal.azure.com).

Per scegliere le metriche di archiviazione da visualizzare in un grafico, seguire questa procedura.

1. Per iniziare, visualizzare un grafico delle metriche di archiviazione nel portale di Azure. È possibile trovare i grafici nel pannello dell' **account di archiviazione** e nel pannello **metriche (classico)** .

   In questo esempio viene usato il seguente grafico che compare nel **pannello dell'account di archiviazione**:

   ![Selezione del grafico nel portale di Azure](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. Fare clic in qualsiasi punto del grafico per modificarlo.

3. Selezionare l'**Intervallo di tempo** delle metriche da visualizzare nel grafico e il **servizio** (blob, coda, tabella, file) di cui si desidera visualizzare le metriche. In questo caso sono selezionate le metriche della scorsa settimana da visualizzare per il servizio blob:

   ![Selezione dell'intervallo di tempo e del servizio nel pannello Modifica grafico](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Selezionare le singole **metriche** da visualizzare nel grafico e quindi fare clic su **OK**.

   ![Selezione delle singole metriche nel pannello Modifica grafico](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

Le impostazioni del grafico non incidono su raccolta, aggregazione o archiviazione dei dati di monitoraggio nell'account di archiviazione.

#### <a name="metrics-availability-in-charts"></a>Metriche disponibili nei grafici

L'elenco delle metriche disponibili varia in base al servizio selezionato nell'elenco a discesa e al tipo di unità del grafico che viene modificato. Ad esempio, è possibile selezionare metriche relative a percentuali come *PercentNetworkError* e *PercentThrottlingError* solo se si sta modificando un grafico che visualizza unità in percentuale:

![Grafico della percentuale di errori nelle richieste nel portale di Azure](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Risoluzione delle metriche

Le metriche selezionate in **Diagnostica** determinano la risoluzione delle metriche disponibili per l'account:

* Il monitoraggio **aggregato** offre metriche come ingresso/uscita, disponibilità, latenza e percentuale di operazioni riuscite, che vengono aggregate dai servizi BLOB, tabelle, file e di accodamento.
* **Per API** offre una risoluzione più dettagliata, con disponibilità di metriche per le singole operazioni di archiviazione in aggiunta alle aggregazioni a livello di servizio.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Scaricare le metriche per l'archiviazione o l'analisi in locale

Se si desidera scaricare le metriche per l'archiviazione a lungo termine o per analizzarle in locale, è necessario usare uno strumento o scrivere il codice per leggere le tabelle. Se si elencano tutte le tabelle nell'account di archiviazione, le tabelle non vengono visualizzate, ma è possibile accedervi direttamente mediante il nome. Molti strumenti di esplorazione dell'archivio sono compatibili con queste tabelle e consentono di visualizzarle direttamente. Vedere [Strumenti client di Archiviazione di Azure](./storage-explorers.md) per un elenco di strumenti disponibili.

|Metriche|Nomi tabella|Note| 
|-|-|-|  
|Metriche orarie|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Nelle versioni precedenti al 15 agosto 2013, queste tabelle sono note come:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Le metriche per il servizio File sono disponibili a partire dalla versione del 5 aprile 2015.|  
|Metriche al minuto|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Può essere abilitata solo a livello di codice o usando PowerShell.<br /><br /> Le metriche per il servizio File sono disponibili a partire dalla versione del 5 aprile 2015.|  
|Capacità|$MetricsCapacityBlob|Solo servizio BLOB.|  

I dettagli completi sugli schemi di queste tabelle sono disponibili in [Schema di tabella della metrica di Analisi archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema). Le righe di esempio seguenti mostrano solo un subset delle colonne disponibili, ma illustrano alcune importanti funzionalità relative al modo in cui le metriche di archiviazione salvano queste metriche:  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Disponibilità|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

Nei dati delle metriche al minuto di questo esempio, la chiave di partizione usa la risoluzione ora al minuto. La chiave di riga identifica il tipo di informazioni archiviate nella riga. È composta dal tipo di accesso e dal tipo di richiesta:  

-   Il tipo di accesso è **user** o **system**, laddove **user** si riferisce a tutte le richieste utente al servizio di archiviazione e **system** si riferisce alle richieste effettuate da Analisi archiviazione.  
-   Il tipo di richiesta può essere **all**, e in questo caso si tratta di una riga di riepilogo, o identificare l'API specifica, ad esempio **QueryEntity** o **UpdateEntity**.  

Questi dati di esempio mostrano tutti i record per un solo minuto (a partire dalle 11.00). La somma del numero di richieste **QueryEntities**, del numero di richieste **QueryEntity** e del numero di richieste **UpdateEntity** è sette. Il totale è visualizzato nella riga **user:All**. Analogamente, è possibile ricavare la latenza end-to-end media 104,4286 nella riga **user:All** calcolando ((143,8 * 5) + 3 + 9) / 7.  

## <a name="view-metrics-data-programmatically"></a>Visualizzare i dati delle metriche a livello di codice

Nell'elenco riportato di seguito viene illustrato il codice C# di esempio che consente l'accesso alle metriche al minuto per un intervallo di minuti. I risultati vengono visualizzati in una finestra della console. Il codice di esempio usa la libreria client di Archiviazione di Azure versione 4.x o successiva che include la classe **CloudAnalyticsClient**, in grado di semplificare l'accesso alle tabelle di metrica nell'archiviazione. 

> [!NOTE]
> La classe **CloudAnalyticsClient** non è inclusa nella libreria client di archiviazione BLOB di Azure V12 per .NET. Il **31 agosto 2023** analisi archiviazione metriche, dette anche *metriche classiche* , verranno ritirate. Per altre informazioni, consultare l'[annuncio ufficiale](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Se si usano metriche classiche, è consigliabile passare alle metriche in monitoraggio di Azure prima di tale data. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Aggiungere i grafici delle metriche al dashboard del portale

È possibile aggiungere i grafici delle metriche di Archiviazione di Azure per qualsiasi account di archiviazione al dashboard del portale.

1. Fare clic su **Modifica dashboard** mentre si visualizza il dashboard nel [portale di Azure](https://portal.azure.com).
1. In **Raccolta riquadri** selezionare **Trova riquadri per** > **Tipo**.
1. Selezionare **Tipo** > **Account di archiviazione**.
1. In **Risorse** selezionare l'account di archiviazione di cui si vogliono aggiungere le metriche al dashboard.
1. Selezionare **Categorie** > **Monitoraggio**.
1. Trascinare il riquadro del grafico per la metrica da visualizzare sul dashboard. Ripetere l'operazione per tutte le metriche che si vogliono visualizzare nel dashboard. Nell'immagine seguente è evidenziato il grafico "BLOB - Richieste totali" come esempio, ma tutti i grafici sono posizionabili nel dashboard.

   ![Raccolta riquadri nel portale di Azure](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Dopo aver completato l'aggiunta dei grafici, selezionare **Fine personalizzazione** nella parte superiore del dashboard.

Dopo che sono stati aggiunti al dashboard, i grafici possono essere ulteriormente personalizzati come descritto in Personalizzare i grafici delle metriche.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni su Analisi archiviazione, vedere [analisi archiviazione](storage-analytics.md) per analisi archiviazione.
* [Configurare i log analisi archiviazione](manage-storage-analytics-logs.md).
* Altre informazioni sullo schema delle metriche. Vedere [analisi archiviazione schema della tabella metrica](/rest/api/storageservices/storage-analytics-metrics-table-schema).