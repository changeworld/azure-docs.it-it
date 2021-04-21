---
title: Monitoraggio File di Azure | Microsoft Docs
description: Informazioni su come monitorare le prestazioni e la disponibilità dei File di Azure. Monitorare File di Azure dati, informazioni sulla configurazione e analizzare i dati delle metriche e dei log.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 620ee3bc5978da4b274aed9a412679ae0835f0b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759828"
---
# <a name="monitoring-azure-files"></a>Monitoraggio File di Azure

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati da File di Azure e come è possibile usare le funzionalità di Monitoraggio di Azure per analizzare gli avvisi su questi dati.

## <a name="monitor-overview"></a>Panoramica di Monitoraggio

La **pagina Panoramica** nel portale di Azure per ogni risorsa File di Azure include una breve visualizzazione dell'utilizzo delle risorse, ad esempio richieste e fatturazione oraria. Queste informazioni sono utili, ma è disponibile solo una piccola quantità di dati di monitoraggio. Alcuni di questi dati vengono raccolti automaticamente ed è disponibile per l'analisi non appena si crea la risorsa. Con alcune configurazioni è possibile abilitare altri tipi di raccolta dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
File di Azure i dati di monitoraggio usando [Monitoraggio di Azure](../../azure-monitor/overview.md), che è un servizio di monitoraggio dello stack completo in Azure. Monitoraggio di Azure offre un set completo di funzionalità per monitorare le risorse e di Azure e le risorse che si trovano in altri cloud e in locale. 

Iniziare con l'articolo Monitoraggio delle risorse [di Azure con Monitoraggio di Azure](../../azure-monitor/essentials/monitor-azure-resource.md), che descrive quanto segue:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Dati di monitoraggio raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti da File di Azure. Gli esempi mostrano come configurare la raccolta dati e analizzare tali dati con gli strumenti di Azure.

## <a name="monitoring-data"></a>Dati di monitoraggio

File di Azure raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure, descritti in [Monitoraggio dei dati dalle risorse di Azure.](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) 

Per [informazioni dettagliate](storage-files-monitoring-reference.md) sulle metriche e sui log creati da File di Azure, vedere Informazioni di riferimento sui dati di monitoraggio file di Azure.

Metriche e log di Monitoraggio di Azure supportano solo gli account di archiviazione di Azure Resource Manager. Monitoraggio di Azure non supporta gli account di archiviazione della versione classica. Se si vogliono usare le metriche o i log con gli account di archiviazione della versione classica, è necessario eseguire la migrazione a un account di archiviazione di Azure Resource Manager. Vedere [Migrate to Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md) (Eseguire la migrazione ad Azure Resource Manager).

## <a name="collection-and-routing"></a>Raccolta e routing

Le metriche della piattaforma e il log attività vengono raccolti automaticamente, ma possono essere indirizzati ad altre posizioni usando un'impostazione di diagnostica. 

Per raccogliere i log delle risorse, è necessario creare un'impostazione di diagnostica. Quando si crea l'impostazione, scegliere **file** come tipo di archiviazione per cui si vogliono abilitare i log. Specificare quindi una delle categorie di operazioni seguenti per cui si desidera raccogliere i log. 

| Categoria | Descrizione |
|:---|:---|
| StorageRead | Operazioni di lettura sugli oggetti. |
| StorageWrite | Operazioni di scrittura su oggetti. |
| StorageDelete | Operazioni di eliminazione sugli oggetti. |

Per ottenere l'elenco delle operazioni SMB e REST registrate, vedere Storage [logged operations and status messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Operazioni registrate nell'archiviazione e messaggi di stato) File di Azure informazioni di riferimento sui dati di [monitoraggio.](storage-files-monitoring-reference.md)

## <a name="creating-a-diagnostic-setting"></a>Creazione di un'impostazione di diagnostica

È possibile creare un'impostazione di diagnostica usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o Azure Resource Manager modello.

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Questa anteprima abilita i log per BLOB (inclusi Azure Data Lake Storage Gen2), file, code e tabelle. Questa funzionalità è disponibile per tutti gli account di archiviazione creati con il modello Azure Resource Manager distribuzione. Vedere Panoramica [dell'account di archiviazione.](../common/storage-account-overview.md)

Per indicazioni generali, vedere [Creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure.](../../azure-monitor/essentials/diagnostic-settings.md)

### <a name="azure-portal"></a>[Portale di Azure](#tab/azure-portal)

1. Accedere al portale di Azure.

2. Passare all'account di archiviazione.

3. Nella sezione **Monitoraggio** fare clic su **Impostazioni di diagnostica (anteprima).**

   > [!div class="mx-imgBorder"]
   > ![portale - Log di diagnostica](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. Scegliere **file** come tipo di archiviazione per cui si vogliono abilitare i log.

5. Fare clic su **Aggiungi impostazione di diagnostica**.

   > [!div class="mx-imgBorder"]
   > ![portale - Log delle risorse - Aggiungere un'impostazione di diagnostica](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   Verrà **visualizzata la pagina Impostazioni** di diagnostica.

   > [!div class="mx-imgBorder"]
   > ![Pagina Dei log delle risorse](media/storage-files-monitoring/diagnostic-logs-page.png)

6. Nel campo **Nome** della pagina immettere un nome per l'impostazione Log risorse. Selezionare quindi le operazioni che si desidera siano registrate (operazioni di lettura, scrittura ed eliminazione) e la posizione in cui inviare i log.

#### <a name="archive-logs-to-a-storage-account"></a>Archiviare i log in un account di archiviazione

Se si sceglie di archiviare i log in un account di archiviazione, si paga per il volume dei log inviati all'account di archiviazione. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Selezionare la **casella di controllo Archivia in un account di** archiviazione e quindi fare clic sul **pulsante** Configura.

   > [!div class="mx-imgBorder"]   
   > ![Archiviazione archivio pagina impostazioni di diagnostica](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. **Nell'elenco a discesa Account** di archiviazione selezionare l'account di archiviazione in cui archiviare i log, fare clic sul pulsante **OK** e quindi sul **pulsante** Salva.

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Prima di scegliere un account di archiviazione come destinazione di esportazione, vedere [Archiviare](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) i log delle risorse di Azure per comprendere i prerequisiti nell'account di archiviazione.

#### <a name="stream-logs-to-azure-event-hubs"></a>Trasmettere i log Hub eventi di Azure

Se si sceglie di trasmettere i log a un hub eventi, si paga il volume dei log inviati all'hub eventi. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Selezionare la **casella di controllo Da flusso a hub eventi** e quindi fare clic sul **pulsante** Configura.

2. Nel riquadro **Selezionare un hub eventi** scegliere lo spazio dei nomi, il nome e il nome dei criteri dell'hub eventi a cui trasmettere i log. 

   > [!div class="mx-imgBorder"]
   > ![Hub eventi della pagina delle impostazioni di diagnostica](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. Fare clic **sul pulsante OK** e quindi sul **pulsante** Salva.

#### <a name="send-logs-to-azure-log-analytics"></a>Inviare log ad Azure Log Analytics

1. Selezionare la casella **di controllo Invia a Log Analytics,** selezionare un'area di lavoro Log Analytics e quindi fare clic sul pulsante **Salva.**

   > [!div class="mx-imgBorder"]   
   > ![Pagina Impostazioni di diagnostica - Log Analytics](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Aprire una Windows PowerShell comando e accedere alla sottoscrizione di Azure usando il `Connect-AzAccount` comando . Seguire quindi le istruzioni visualizzate sullo schermo.

   ```powershell
   Connect-AzAccount
   ```

2. Impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione per cui si vuole abilitare la registrazione.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Archiviare i log in un account di archiviazione

Se si sceglie di archiviare i log in un account di archiviazione, si paga il volume dei log inviati all'account di archiviazione. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Abilitare i log usando il cmdlet Di PowerShell [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) insieme al `StorageAccountId` parametro .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

Sostituire il `<storage-service-resource--id>` segnaposto in questo frammento di codice con l'ID risorsa del servizio File di Azure. L'ID della risorsa si trova nel portale di Azure, nella **pagina delle proprietà** del proprio account di archiviazione.

È possibile usare `StorageRead` , e per il valore del parametro `StorageWrite` `StorageDelete` **Category.**

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Ecco un esempio:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Per una descrizione di ogni parametro, vedere [Archiviare i log delle risorse di Azure tramite Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Trasmettere i log a un hub eventi

Se si sceglie di trasmettere i log a un hub eventi, si paga per il volume di log inviati all'hub eventi. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Abilitare i log usando il cmdlet di PowerShell [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) con il `EventHubAuthorizationRuleId` parametro .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Ecco un esempio:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Per una descrizione di ogni parametro, vedere Trasmettere [dati a Hub eventi tramite i cmdlet di PowerShell.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Inviare log a Log Analytics

Abilitare i log usando il cmdlet di PowerShell [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) con il `WorkspaceId` parametro .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Ecco un esempio:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Per altre informazioni, vedere Trasmettere [i log delle risorse di Azure all'area di lavoro Log Analytics in Monitoraggio di Azure](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Prima di tutto aprire [Azure Cloud Shell](../../cloud-shell/overview.md) oppure un'applicazione console dei comandi come Windows PowerShell, se si dispone dell’interfaccia della riga di comando di Azure [installata](/cli/azure/install-azure-cli) in locale.

2. Se l'identità è associata a più sottoscrizioni, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione per cui si vogliono abilitare i log.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

#### <a name="archive-logs-to-a-storage-account"></a>Archiviare i log in un account di archiviazione

Se si sceglie di archiviare i log in un account di archiviazione, si paga per il volume dei log inviati all'account di archiviazione. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Abilitare i log usando il [comando az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

Sostituire il `<storage-service-resource--id>` segnaposto in questo frammento di codice con il servizio di archiviazione BLOB con ID risorsa. L'ID della risorsa si trova nel portale di Azure, nella **pagina delle proprietà** del proprio account di archiviazione.

È possibile usare `StorageRead` , e per il valore del parametro `StorageWrite` `StorageDelete` **category.**

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Ecco un esempio:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

Per una descrizione di ogni parametro, vedere Archiviare i log delle risorse [tramite l'interfaccia](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)della riga di comando di Azure.

#### <a name="stream-logs-to-an-event-hub"></a>Trasmettere i log a un hub eventi

Se si sceglie di trasmettere i log a un hub eventi, si paga per il volume di log inviati all'hub eventi. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Abilitare i log usando il [comando az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Ecco un esempio:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Per una descrizione di ogni parametro, vedere Trasmettere dati a Hub eventi tramite l'interfaccia della [riga di comando di Azure.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Inviare log a Log Analytics

Abilitare i log usando il [comando az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Ecco un esempio:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Per altre informazioni, vedere Eseguire lo [streaming dei log delle risorse di Azure nell'area di lavoro Log Analytics in Monitoraggio di Azure](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Modello](#tab/template)

Per visualizzare un modello Azure Resource Manager che crea un'impostazione di diagnostica, vedere [Impostazione diagnostica per Archiviazione di Azure](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analisi delle metriche

È possibile analizzare le metriche di Archiviazione di Azure con metriche di altri servizi di Azure usando Esplora metriche. Aprire Esplora metriche selezionando **Metrica** dal menu di **Monitoraggio di Azure**. Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](../../azure-monitor/essentials/metrics-getting-started.md). 

Per le metriche che supportano le dimensioni, è possibile applicare un filtro specificando il valore di dimensione desiderato.  Per un elenco completo delle dimensioni supportate da Archiviazione di Azure, vedere [Dimensioni delle metriche](storage-files-monitoring-reference.md#metrics-dimensions). Le metriche per File di Azure sono in questi spazi dei nomi: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

Per un elenco di tutte le Monitoraggio di Azure di supporto, che include File di Azure, vedere Monitoraggio di Azure [metriche supportate](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Accesso alle metriche

> [!TIP]
> Per visualizzare esempi dell'interfaccia della riga di comando di Azure o di .NET, scegliere le schede corrispondenti di seguito.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Elenco della definizione di metrica

È possibile elencare la definizione della metrica dell'account di archiviazione o del File di Azure servizio. Usare il cmdlet [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition).

In questo esempio sostituire il segnaposto con l'ID risorsa dell'intero account di archiviazione o `<resource-ID>` l'ID risorsa del File di Azure servizio.  Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Lettura dei valori delle metriche

È possibile leggere i valori delle metriche a livello di account dell'account di archiviazione o del File di Azure servizio. Usare il cmdlet [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Elenco della definizione di metrica a livello di account

È possibile elencare la definizione della metrica dell'account di archiviazione o del File di Azure servizio. Usare il comando [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions).
 
In questo esempio sostituire il segnaposto con l'ID risorsa dell'intero account di archiviazione o `<resource-ID>` l'ID risorsa del File di Azure servizio. Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Lettura dei valori di metrica a livello di account

È possibile leggere i valori delle metriche dell'account di archiviazione o del File di Azure servizio. Usare il comando [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Monitoraggio di Azure fornisce l'[SDK di .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) per consentire la lettura di definizioni e valori della metrica. Il [codice di esempio](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) illustra come usare l'SDK con parametri diversi. Per le metriche di archiviazione, usare `0.18.0-preview` o una versione successiva.
 
In questi esempi sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o del File di Azure servizio. Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

Sostituire la variabile `<subscription-ID>` con l'ID della propria sottoscrizione. Per istruzioni su come ottenere i valori per `<tenant-ID>`, `<application-ID>` e `<AccessKey>`, vedere [Usare il portale per creare un'applicazione Azure Active Directory (Azure AD) e un'entità servizio che possano accedere alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md). 

#### <a name="list-the-account-level-metric-definition"></a>Elenco della definizione di metrica a livello di account

L'esempio seguente mostra come elencare le definizioni delle metriche a livello di account:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>Lettura dei valori delle metriche a livello di account

L'esempio seguente mostra come leggere i dati di `UsedCapacity` a livello di account:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>Lettura dei valori delle metriche multidimensionali

Per leggere i dati di metriche multidimensionali in valori di dimensioni specifici è necessario definire i filtri dei metadati.

L'esempio seguente mostra come leggere i dati di metrica per le metriche che supportano più dimensioni:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

# <a name="template"></a>[Modello](#tab/template)

N/D.

---

## <a name="analyzing-logs"></a>Analisi dei log

È possibile accedere ai log delle risorse come BLOB in un account di archiviazione, come dati degli eventi o tramite query di Log Analytics.

Per ottenere l'elenco delle operazioni SMB e REST registrate, vedere Storage [logged operations and status messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Operazioni registrate di archiviazione e messaggi di stato) File di Azure informazioni di riferimento sui dati di [monitoraggio](storage-files-monitoring-reference.md).

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Questa anteprima abilita i log per BLOB (che includono Azure Data Lake Storage Gen2), file, code, tabelle, account di archiviazione Premium negli account di archiviazione per utilizzo generico v1 e utilizzo generico v2. Gli account di archiviazione della versione classica non sono supportati.

Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Ad esempio, se un account di archiviazione ha attività nell'endpoint file, ma non negli endpoint di tabella o coda, vengono creati solo i log relativi al servizio File di Azure. I log di Archiviazione di Azure contengono informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo.

### <a name="log-authenticated-requests"></a>Richieste di registrazione autenticate

 Vengono registrati i seguenti tipi di richieste autenticate:

- Richieste riuscite
- Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo
- Richieste che usano Kerberos, NTLM o firma di accesso condiviso, incluse le richieste non riuscite e riuscite
- Richieste ai dati di analisi (dati di log classici nel **contenitore $logs** e dati delle metriche classiche nelle tabelle $metric **classiche)**

Le richieste effettuate dal File di Azure stesso, ad esempio la creazione o l'eliminazione di log, non vengono registrate. Per un elenco completo delle richieste SMB e REST registrate, vedere Storage [logged operations and status messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Operazioni registrate nell'archiviazione e messaggi di stato) File di Azure informazioni di riferimento sui dati di [monitoraggio.](storage-files-monitoring-reference.md)

### <a name="accessing-logs-in-a-storage-account"></a>Accesso ai log in un account di archiviazione

I log vengono visualizzati come BLOB archiviati in un contenitore nell'account di archiviazione di destinazione. I dati vengono raccolti e archiviati all'interno di un singolo BLOB come payload JSON delimitato da righe. Il nome del BLOB si basa sulla convenzione di denominazione seguente:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Ad esempio:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Accesso ai log in un hub eventi

I log inviati a un hub eventi non vengono archiviati come file, ma è possibile verificare che l'hub eventi abbia ricevuto le informazioni del log. Passare all'hub eventi nel portale di Azure e verificare che il numero di **Messaggi in arrivo** sia maggiore di zero. 

![Log di controllo](media/storage-files-monitoring/event-hub-log.png)

È possibile accedere e leggere i dati di log inviati all'hub eventi usando le informazioni di sicurezza e gli strumenti di monitoraggio e gestione degli eventi. Per altre informazioni, vedere [Quali operazioni si possono eseguire con i dati di monitoraggio inviati all'hub eventi?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Accesso ai log in un'area di lavoro Log Analytics

È possibile accedere ai log inviati a un'area di lavoro Log Analytics usando le query di log di Monitoraggio di Azure. I dati vengono archiviati **nella tabella StorageFileLogs.** 

Per altre informazioni, vedere Esercitazione [su Log Analytics.](../../azure-monitor/logs/log-analytics-tutorial.md)

#### <a name="sample-kusto-queries"></a>Query Kusto di esempio

Ecco alcune query che è possibile immettere nella **barra** di ricerca log per monitorare l'File di Azure. Queste query usano il [nuovo linguaggio](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> Quando si seleziona **Log dal** menu del gruppo di risorse dell'account di archiviazione, Log Analytics viene aperto con l'ambito della query impostato sul gruppo di risorse corrente. Ciò significa che le query di log includeranno solo i dati di tale gruppo di risorse. Se si vuole eseguire una query che include dati di altre  risorse o dati di altri servizi di Azure, scegliere Log dal **menu** Monitoraggio di Azure dati. Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](../../azure-monitor/logs/scope.md).

Usare queste query per monitorare le condivisioni file di Azure:

- Visualizzare gli errori SMB nell'ultima settimana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Creare un grafico a torta delle operazioni SMB nell'ultima settimana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Visualizzare gli errori REST nell'ultima settimana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Creare un grafico a torta delle operazioni REST nell'ultima settimana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Per visualizzare l'elenco di nomi di colonna e descrizioni per File di Azure, vedere [StorageFileLogs.](/azure/azure-monitor/reference/tables/storagefilelogs)

Per altre informazioni su come scrivere query, vedere Esercitazione [su Log Analytics.](../../azure-monitor/logs/log-analytics-tutorial.md)

## <a name="alerts"></a>Avvisi

Monitoraggio di Azure avvisi notificano in modo proattivo quando vengono trovate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi nel sistema prima che i clienti li notino. È possibile impostare avvisi sulle [metriche](../../azure-monitor/alerts/alerts-metric-overview.md), [sui log](../../azure-monitor/alerts/alerts-unified-log.md)e sul [log attività](../../azure-monitor/alerts/activity-log-alerts.md). 

La tabella seguente elenca alcuni scenari di esempio da monitorare e la metrica appropriata da usare per l'avviso:

| Scenario | Metrica da usare per l'avviso |
|-|-|
| La condivisione file è limitate. | Metrica: Transazioni<br>Nome dimensione: Tipo di risposta <br>Nome dimensione: FileShare (solo condivisione file Premium) |
| Le dimensioni della condivisione file sono dell'80% della capacità. | Metrica: Capacità file<br>Nome dimensione: FileShare (solo condivisione file Premium) |
| L'uscita della condivisione file ha superato i 500 GiB in un giorno. | Metrica: Uscita<br>Nome dimensione: FileShare (solo condivisione file Premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Come creare avvisi per File di Azure

1. Passare **all'account di archiviazione** nella **portale di Azure**. 

2. Fare **clic su Avvisi** e quindi su + Nuova regola di **avviso.**

3. Fare **clic su Modifica risorsa,** selezionare il tipo di risorsa **File** e quindi fare clic su **Fine.** 

4. Fare **clic su Aggiungi** condizione e specificare le informazioni seguenti per l'avviso: 

    - **Metrica**
    - **Nome dimensione**
    - **Logica avvisi**

5. Fare **clic su Aggiungi** gruppi di azioni e aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.

6. Compilare i **dettagli dell'avviso,** **ad esempio Nome regola di** avviso, **Descrizione** e **Gravità.**

7. Fare **clic su Crea regola di** avviso per creare l'avviso.

> [!NOTE]  
> Se si crea un avviso ed è troppo rumoroso, modificare il valore di soglia e la logica di avviso.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Come creare un avviso se una condivisione file è con limitazione

1. Passare **all'account di archiviazione** nella **portale di Azure**.
2. Nella sezione **Monitoraggio fare** clic su **Avvisi** e quindi su + Nuova regola **di avviso.**
3. Fare **clic su Modifica risorsa,** selezionare il tipo di risorsa File **per** l'account di archiviazione e quindi fare clic su **Fine.** Ad esempio, se il nome dell'account di archiviazione `contoso` è , selezionare la `contoso/file` risorsa.
4. Fare **clic su Aggiungi** condizione per aggiungere una condizione.
5. Verrà visualizzato un elenco dei segnali supportati per l'account di archiviazione e selezionare la **metrica** Transazioni.
6. Nel pannello **Configura logica dei segnali** fare clic sull'elenco a discesa **Nome** dimensione e selezionare Tipo **di risposta.**
7. Fare clic **sull'elenco** a discesa Valori dimensione e selezionare i tipi di risposta appropriati per la condivisione file.

    Per le condivisioni file standard, selezionare i tipi di risposta seguenti:

    - SuccessWithShareIopsThrottling
    - SuccessWithThrottling
    - ClientShareIopsThrottlingError

    Per le condivisioni file Premium, selezionare i tipi di risposta seguenti:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Se i tipi di risposta non sono elencati nell'elenco a discesa **Valori** dimensione, significa che la risorsa non è stata limitate. Per aggiungere i valori di  dimensione, accanto all'elenco a discesa Valori dimensione selezionare Aggiungi valore **personalizzato,** immettere il tipo di respone ,ad esempio **SuccessWithThrottling,** selezionare **OK** e quindi ripetere questi passaggi per aggiungere tutti i tipi di risposta applicabili per la condivisione file.

8. Per **le condivisioni file Premium,** fare clic **sull'elenco** a discesa Nome dimensione e selezionare **Condivisione file**. Per **le condivisioni file standard,** andare al **passaggio #10**.

   > [!NOTE]
   > Se la condivisione file è una  condivisione file standard, la dimensione Condivisione file non elenca le condivisioni file perché le metriche per condivisione non sono disponibili per le condivisioni file standard. Gli avvisi di limitazione per le condivisioni file standard verranno attivati se una condivisione file all'interno dell'account di archiviazione è stata limitata e l'avviso non identificherà quale condivisione file è stata limitazione. Poiché le metriche per condivisione non sono disponibili per le condivisioni file standard, è consigliabile avere una condivisione file per ogni account di archiviazione.

9. Fare clic **sull'elenco** a discesa Valori dimensione e selezionare le condivisie file per cui si vuole creare un avviso.
10. Definire i **parametri di avviso** (valore soglia, operatore, granularità delle aggregazioni e frequenza di valutazione) e fare clic su **Fine**.

    > [!TIP]
    > Se si usa una soglia statica, il grafico delle metriche consente di determinare un valore soglia ragionevole se la condivisione file è attualmente in fase di limitazione. Se si usa una soglia dinamica, nel grafico delle metriche verranno visualizzate le soglie calcolate in base ai dati recenti.

11. Fare **clic su Aggiungi gruppi** di azioni per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni. 
12. Compilare i **dettagli dell'avviso,** ad esempio **Nome regola di** avviso, **Descrizione** e **Gravità.**
13. Fare **clic su Crea regola di** avviso per creare l'avviso.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Come creare un avviso se le dimensioni della condivisione file di Azure sono dell'80% della capacità

1. Passare **all'account di archiviazione** nella **portale di Azure**.
2. Nella sezione **Monitoraggio fare** clic su **Avvisi e** quindi su + Nuova regola **di avviso.**
3. Fare **clic su Modifica risorsa,** selezionare il tipo di risorsa File **per** l'account di archiviazione e quindi fare clic su **Fine.** Ad esempio, se il nome dell'account di archiviazione `contoso` è , selezionare la risorsa `contoso/file` .
4. Fare **clic su Aggiungi** condizione per aggiungere una condizione.
5. Verrà visualizzato un elenco di segnali supportati per l'account di archiviazione e selezionare la metrica **Capacità** file.
6. Per **le condivisioni file Premium**, fare clic **sull'elenco a** discesa Nome dimensione e selezionare Condivisione **file**. Per **le condivisioni file standard,** andare **al passaggio #8**.

   > [!NOTE]
   > Se la condivisione file è una  condivisione file standard, la dimensione Condivisione file non elenca le condivisioni file perché le metriche per condivisione non sono disponibili per le condivisioni file standard. Gli avvisi per le condivisioni file standard sono basati su tutte le condivisioni file nell'account di archiviazione. Poiché le metriche per condivisione non sono disponibili per le condivisioni file standard, è consigliabile avere una condivisione file per ogni account di archiviazione.

7. Fare clic **sull'elenco** a discesa Valori dimensione e selezionare le condivisie file per le quale si vuole creare un avviso.
8. Immettere il **valore soglia** in byte. Ad esempio, se le dimensioni della condivisione file sono pari a 100 TiB e si vuole ricevere un avviso quando le dimensioni della condivisione file sono pari all'80% della capacità, il valore soglia in byte è 87960930222080.
9. Definire il resto dei parametri di **avviso** (granularità dell'aggregazione e frequenza di valutazione) e fare clic su **Fine.**
10. Fare **clic su Aggiungi gruppi** di azioni per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni. 
11. Compilare i **dettagli dell'avviso,** **ad esempio Nome regola di** avviso, **Descrizione** e **Gravità.**
12. Fare **clic su Crea regola di** avviso per creare l'avviso.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Come creare un avviso se l'uscita della condivisione file di Azure ha superato 500 GiB in un giorno

1. Passare **all'account di archiviazione** nella **portale di Azure**.
2. Nella sezione Monitoraggio fare clic su **Avvisi** e quindi su **+ Nuova regola di avviso.**
3. Fare **clic su Modifica risorsa,** selezionare il tipo di risorsa **File** per l'account di archiviazione e quindi fare clic su **Fine.** Ad esempio, se il nome dell'account di archiviazione è contoso, selezionare la risorsa contoso/file.
4. Fare **clic su Aggiungi** condizione per aggiungere una condizione.
5. Verrà visualizzato un elenco di segnali supportati per l'account di archiviazione e selezionare la **metrica In** uscita.
6. Per **le condivisioni file Premium,** fare clic **sull'elenco** a discesa Nome dimensione e selezionare **Condivisione file**. Per **le condivisioni file standard,** andare al **passaggio #8**.

   > [!NOTE]
   > Se la condivisione file è una  condivisione file standard, la dimensione Condivisione file non elenca le condivisioni file perché le metriche per condivisione non sono disponibili per le condivisioni file standard. Gli avvisi per le condivisioni file standard si basano su tutte le condivisioni file nell'account di archiviazione. Poiché le metriche per condivisione non sono disponibili per le condivisioni file standard, è consigliabile avere una condivisione file per ogni account di archiviazione.

7. Fare clic **sull'elenco** a discesa Valori dimensione e selezionare le condivisie file per cui si vuole creare un avviso.
8. Immettere **536870912000 byte** per Valore soglia. 
9. Fare clic **sull'elenco** a discesa Granularità aggregazione e **selezionare 24 ore.**
10. Selezionare **frequenza di valutazione e** fare **clic su Fine.**
11. Fare **clic su Aggiungi gruppi** di azioni per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni. 
12. Compilare i **dettagli dell'avviso,** ad esempio **Nome regola di** avviso, **Descrizione** e **Gravità.**
13. Fare **clic su Crea regola di avviso** per creare l'avviso.

## <a name="next-steps"></a>Passaggi successivi

- [File di Azure informazioni di riferimento sui dati di monitoraggio](storage-files-monitoring-reference.md)
- [Monitorare le risorse di Azure con Monitoraggio di Azure](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Archiviazione di Azure migrazione delle metriche](../common/storage-metrics-migration.md)
- [Pianificazione per la distribuzione di File di Azure](./storage-files-planning.md)
- [Come distribuire File di Azure](./storage-how-to-create-file-share.md)
- [Risolvere i problemi di File di Azure in Windows](./storage-troubleshoot-windows-file-connection-problems.md)
- [Risolvere i problemi di File di Azure in Linux](./storage-troubleshoot-linux-file-connection-problems.md)
