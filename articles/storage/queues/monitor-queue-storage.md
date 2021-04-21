---
title: Monitoraggio archiviazione code di Azure
description: Informazioni su come monitorare le prestazioni e la disponibilità archiviazione code di Azure. Monitorare archiviazione code di Azure dati, ottenere informazioni sulla configurazione e analizzare i dati delle metriche e dei log.
author: normesta
services: storage
ms.author: normesta
ms.reviewer: fryu
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b65aff45cc304f59e45fc3bed925b93ee6c622fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788404"
---
# <a name="monitoring-azure-queue-storage"></a>Monitoraggio archiviazione code di Azure

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati da archiviazione code di Azure e come è possibile usare le funzionalità di Monitoraggio di Azure per analizzare gli avvisi su questi dati.

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Questa anteprima abilita i log per BLOB (inclusi Azure Data Lake Storage Gen2), file, code e tabelle. Questa funzionalità è disponibile per tutti gli account di archiviazione creati con il modello Azure Resource Manager distribuzione. Vedere Panoramica [dell'account di archiviazione.](../common/storage-account-overview.md)

## <a name="monitor-overview"></a>Panoramica di Monitoraggio

La **pagina Panoramica** nel portale di Azure per ogni risorsa di archiviazione code include una breve visualizzazione dell'utilizzo delle risorse, ad esempio le richieste e la fatturazione oraria. Queste informazioni sono utili, ma è disponibile solo una piccola quantità di dati di monitoraggio. Alcuni di questi dati vengono raccolti automaticamente ed è disponibile per l'analisi non appena si crea la risorsa. Con alcune configurazioni è possibile abilitare altri tipi di raccolta dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure

archiviazione code di Azure i dati di monitoraggio [usando Monitoraggio di Azure](../../azure-monitor/overview.md), un servizio di monitoraggio dello stack completo in Azure. Monitoraggio di Azure offre un set completo di funzionalità per monitorare le risorse di Azure e le risorse in altri cloud e in locale.

Iniziare a [monitorare le risorse di Azure con Monitoraggio di Azure](../../azure-monitor/essentials/monitor-azure-resource.md) che descrive quanto segue:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Dati di monitoraggio raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti da Archiviazione di Azure. Gli esempi mostrano come configurare la raccolta dati e analizzare tali dati con gli strumenti di Azure.

## <a name="monitoring-data"></a>Dati di monitoraggio

archiviazione code di Azure raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure, descritti in Monitoraggio [dei dati dalle risorse di Azure.](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)

Vedere [archiviazione code di Azure informazioni di riferimento sui dati](monitor-queue-storage-reference.md) di monitoraggio per informazioni dettagliate sulle metriche e le metriche dei log create da archiviazione code di Azure.

Metriche e log di Monitoraggio di Azure supportano solo gli account di archiviazione di Azure Resource Manager. Monitoraggio di Azure non supporta gli account di archiviazione della versione classica. Se si vogliono usare le metriche o i log con gli account di archiviazione della versione classica, è necessario eseguire la migrazione a un account di archiviazione di Azure Resource Manager. Vedere [Migrate to Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md) (Eseguire la migrazione ad Azure Resource Manager).

Se si vuole, è possibile continuare a usare le metriche e i log della versione classica. In realtà, le metriche e i log della versione classica sono disponibili in parallelo con le metriche e i log di Monitoraggio di Azure. Il supporto rimane invariato fino al termine del servizio relativo alle metriche e ai log legacy da parte di Archiviazione di Azure.

## <a name="collection-and-routing"></a>Raccolta e routing

Le metriche della piattaforma e il log attività vengono raccolti automaticamente, ma possono essere indirizzati ad altre posizioni usando un'impostazione di diagnostica.

Per raccogliere i log delle risorse, è necessario creare un'impostazione di diagnostica. Quando si crea l'impostazione, scegliere **queue** come tipo di archiviazione per cui si vogliono abilitare i log. Specificare quindi una delle categorie di operazioni seguenti per cui si desidera raccogliere i log.

| Categoria | Descrizione |
|:---|:---|
| **StorageRead** | Operazioni di lettura sugli oggetti. |
| **StorageWrite** | Operazioni di scrittura su oggetti. |
| **StorageDelete** | Operazioni di eliminazione sugli oggetti. |

## <a name="creating-a-diagnostic-setting"></a>Creazione di un'impostazione di diagnostica

È possibile creare un'impostazione di diagnostica usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o un modello Azure Resource Manager diagnostica.

Per indicazioni generali, vedere [Creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure.](../../azure-monitor/essentials/diagnostic-settings.md)

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Questa anteprima abilita i log per BLOB (che includono Azure Data Lake Storage Gen2), file, code e tabelle. Questa funzionalità è disponibile per tutti gli account di archiviazione creati con il modello Azure Resource Manager distribuzione. Vedere [Panoramica dell'account di archiviazione.](../common/storage-account-overview.md)

### <a name="azure-portal"></a>[Portale di Azure](#tab/azure-portal)

1. Accedere al portale di Azure.

2. Passare all'account di archiviazione.

3. Nella sezione **Monitoraggio** fare clic su **Impostazioni di diagnostica (anteprima)**.

   > [!div class="mx-imgBorder"]
   > ![portale - Log di diagnostica](media/monitor-queue-storage/diagnostic-logs-settings-pane.png)

4. Scegliere **coda** come tipo di archiviazione per cui si vogliono abilitare i log.

5. Fare clic su **Aggiungi impostazione di diagnostica**.

   > [!div class="mx-imgBorder"]
   > ![portale - Log risorse - Aggiungere l'impostazione di diagnostica](media/monitor-queue-storage/diagnostic-logs-settings-pane-2.png)

   Verrà **visualizzata la pagina Impostazioni** di diagnostica.

   > [!div class="mx-imgBorder"]
   > ![Pagina Log risorse](media/monitor-queue-storage/diagnostic-logs-page.png)

6. Nel campo **Nome** della pagina immettere un nome per l'impostazione del log delle risorse. Selezionare quindi le operazioni che si desidera siano registrate (operazioni di lettura, scrittura ed eliminazione) e la posizione in cui inviare i log.

#### <a name="archive-logs-to-a-storage-account"></a>Archiviare i log in un account di archiviazione

Se si sceglie di archiviare i log in un account di archiviazione, si paga il volume dei log inviati all'account di archiviazione. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Selezionare la **casella di controllo Archivia** in un account di archiviazione e quindi selezionare il **pulsante** Configura.

   > [!div class="mx-imgBorder"]
   > ![Archiviazione archivio pagina impostazioni di diagnostica](media/monitor-queue-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. **Nell'elenco a discesa Account** di archiviazione selezionare l'account di archiviazione in cui archiviare i log, fare clic sul pulsante **OK** e quindi selezionare **il pulsante** Salva.
 
   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Prima di scegliere un account di archiviazione come destinazione di esportazione, vedere [Archiviare](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) i log delle risorse di Azure per comprendere i prerequisiti per l'account di archiviazione.

#### <a name="stream-logs-to-azure-event-hubs"></a>Trasmettere i log a Hub eventi di Azure

Se si sceglie di trasmettere i log a un hub eventi, si paga per il volume di log inviati all'hub eventi. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Selezionare la **casella di controllo Stream to an event hub** (Trasmettere a un hub eventi) e quindi selezionare il **pulsante** Configura.

2. Nel riquadro **Selezionare un hub eventi** scegliere lo spazio dei nomi, il nome e il nome dei criteri dell'hub eventi a cui si vuole trasmettere i log.

   > [!div class="mx-imgBorder"]
   > ![Hub eventi della pagina impostazioni di diagnostica](media/monitor-queue-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Fare clic sul pulsante **OK** e quindi selezionare **il pulsante** Salva.

#### <a name="send-logs-to-azure-log-analytics"></a>Inviare log ad Azure Log Analytics

1. Selezionare la casella di controllo Invia a **Log Analytics,** selezionare un'area di lavoro Log Analytics e quindi selezionare il **pulsante** Salva.

   > [!div class="mx-imgBorder"]
   > ![Pagina Impostazioni di diagnostica - Log Analytics](media/monitor-queue-storage/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Aprire una Windows PowerShell comando e accedere alla sottoscrizione di Azure usando il `Connect-AzAccount` comando . Seguire quindi le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

2. Impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione per cui si vuole abilitare la registrazione.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Archiviare i log in un account di archiviazione

Se si sceglie di archiviare i log in un account di archiviazione, si paga per il volume dei log inviati all'account di archiviazione. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Abilitare i log usando il cmdlet di PowerShell [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) insieme al `StorageAccountId` parametro .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log>
```

Sostituire il `<storage-service-resource--id>` segnaposto in questo frammento con l'ID risorsa della coda. L'ID della risorsa si trova nel portale di Azure, nella **pagina delle proprietà** del proprio account di archiviazione.

È possibile usare `StorageRead` , e per il valore del parametro `StorageWrite` `StorageDelete` **Category.**

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Ecco un esempio:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Per una descrizione di ogni parametro, vedere [Archiviare i log delle risorse di Azure tramite Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Trasmettere i log a un hub eventi

Se si sceglie di trasmettere i log a un hub eventi, si paga per il volume di log inviati all'hub eventi. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Abilitare i log usando il cmdlet di PowerShell [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) con il `EventHubAuthorizationRuleId` parametro .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Ecco un esempio:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Per una descrizione di ogni parametro, vedere Trasmettere [dati a Hub eventi tramite i cmdlet di PowerShell.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Inviare log a Log Analytics

Abilitare i log usando il cmdlet di PowerShell [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) con il `WorkspaceId` parametro .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Ecco un esempio:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Per altre informazioni, vedere Trasmettere [i log delle risorse di Azure all'area di lavoro Log Analytics in Monitoraggio di Azure](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Aprire prima di tutto il [Azure Cloud Shell](../../cloud-shell/overview.md)oppure, se è stata installata l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) in locale, aprire un'applicazione console dei comandi, ad esempio PowerShell.

2. Se l'identità è associata a più sottoscrizioni, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione per cui si vogliono abilitare i log.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

#### <a name="archive-logs-to-a-storage-account"></a>Archiviare i log in un account di archiviazione

Se si sceglie di archiviare i log in un account di archiviazione, si paga per il volume dei log inviati all'account di archiviazione. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Abilitare i log usando il [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) comando .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

Sostituire il `<storage-service-resource--id>` segnaposto in questo frammento con l'ID risorsa della coda. L'ID della risorsa si trova nel portale di Azure, nella **pagina delle proprietà** del proprio account di archiviazione.

È possibile usare `StorageRead` , e per il valore del parametro `StorageWrite` `StorageDelete` `category` .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Ecco un esempio:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

Per una descrizione di ogni parametro, vedere Archiviare i log delle risorse [tramite l'interfaccia](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)della riga di comando di Azure.

#### <a name="stream-logs-to-an-event-hub"></a>Trasmettere i log a un hub eventi

Se si sceglie di trasmettere i log a un hub eventi, si paga per il volume di log inviati all'hub eventi. Per prezzi specifici, vedere la **sezione Log piattaforma** della pagina Monitoraggio di Azure [prezzi.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Abilitare i log usando il [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) comando .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Ecco un esempio:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Per una descrizione di ogni parametro, vedere Trasmettere dati a Hub eventi tramite l'interfaccia della [riga di comando di Azure.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Inviare log a Log Analytics

Abilitare i log usando il [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) comando .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Ecco un esempio:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Per altre informazioni, vedere Trasmettere [i log delle risorse di Azure all'area di lavoro Log Analytics in Monitoraggio di Azure](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

# <a name="template"></a>[Modello](#tab/template)

Per visualizzare un modello Azure Resource Manager che crea un'impostazione di diagnostica, vedere [Impostazione di diagnostica per Archiviazione di Azure](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analisi delle metriche

È possibile analizzare le metriche per Archiviazione di Azure con le metriche di altri servizi di Azure usando Azure Esplora metriche. Aprire Esplora metriche selezionando **Metrica** dal menu di **Monitoraggio di Azure**. Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](../../azure-monitor/essentials/metrics-getting-started.md).

L'esempio seguente mostra come visualizzare le **transazioni** a livello di account.

![Screenshot dell'accesso alle metriche nel portale di Azure](./media/monitor-queue-storage/access-metrics-portal.png)

Per le metriche che supportano le dimensioni, è possibile applicare un filtro specificando il valore di dimensione desiderato. L'esempio seguente mostra come visualizzare le **transazioni** a livello di account su un'operazione specifica selezionando valori per la dimensione **API Name**.

![Screenshot dell'accesso alle metriche con dimensioni nel portale di Azure](./media/monitor-queue-storage/access-metrics-portal-with-dimension.png)

Per un elenco completo delle dimensioni supportate da Archiviazione di Azure, vedere [Dimensioni delle metriche](monitor-queue-storage-reference.md#metrics-dimensions).

Le metriche per archiviazione code di Azure sono in questi spazi dei nomi:

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/queueServices

Per un elenco di tutte le Monitoraggio di Azure supportate, che include archiviazione code di Azure, Monitoraggio di Azure [metriche supportate.](../../azure-monitor/essentials/metrics-supported.md)

### <a name="accessing-metrics"></a>Accesso alle metriche

> [!TIP]
> Per visualizzare esempi dell'interfaccia della riga di comando di Azure o di .NET, scegliere le schede corrispondenti di seguito.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Elenco della definizione di metrica

È possibile elencare la definizione della metrica dell'account di archiviazione o del servizio di archiviazione code. Usare il cmdlet [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition).

In questo esempio sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o l'ID risorsa della coda. Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Lettura dei valori delle metriche

È possibile leggere i valori delle metriche a livello di account dell'account di archiviazione o del servizio di archiviazione code. Usare il cmdlet [Get-AzMetric](/powershell/module/az.monitor/get-azmetric).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Elenco della definizione di metrica a livello di account

È possibile elencare la definizione della metrica dell'account di archiviazione o del servizio di archiviazione code. Usare il [`az monitor metrics list-definitions`](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) comando .

In questo esempio sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o l'ID risorsa della coda. Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Lettura dei valori di metrica a livello di account

È possibile leggere i valori delle metriche dell'account di archiviazione o del servizio di archiviazione code. Usare il [`az monitor metrics list`](/cli/azure/monitor/metrics#az_monitor_metrics_list) comando .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Monitoraggio di Azure fornisce l'[SDK di .NET](https://www.nuget.org/packages/microsoft.azure.management.monitor/) per consentire la lettura di definizioni e valori della metrica. Il [codice di esempio](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) illustra come usare l'SDK con parametri diversi. Per le metriche di archiviazione, usare `0.18.0-preview` o una versione successiva.

In questi esempi sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o della coda. Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

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
        // Resource ID for queue storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default";
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

### <a name="template"></a>[Modello](#tab/template)

N/D.

---

## <a name="analyzing-logs"></a>Analisi dei log

È possibile accedere ai log delle risorse come coda in un account di archiviazione, come dati di evento o tramite query di Log Analytics.

Per informazioni di riferimento dettagliate sui campi visualizzati in questi log, vedere archiviazione code di Azure [di riferimento ai dati di monitoraggio.](monitor-queue-storage-reference.md)

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Questa anteprima abilita i log per BLOB (che includono Azure Data Lake Storage Gen2), file, code, tabelle, account di archiviazione Premium negli account di archiviazione per utilizzo generico v1 e utilizzo generico v2. Gli account di archiviazione della versione classica non sono supportati.

Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Ad esempio, se un account di archiviazione ha attività nell'endpoint della coda, ma non negli endpoint tabella o BLOB, vengono creati solo i log relativi all'archiviazione code. I log di Archiviazione di Azure contengono informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo.

### <a name="log-authenticated-requests"></a>Richieste di registrazione autenticate

Vengono registrati i seguenti tipi di richieste autenticate:

- Richieste riuscite
- Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo
- Richieste che usano una firma di accesso condiviso o OAuth, incluse le richieste riuscite e non riuscite
- Richieste ai dati di analisi (dati di log classici nel contenitore **$logs** e dati di metrica della classe nelle tabelle **$metric**)

Le richieste effettuate dal servizio di archiviazione code stesso, ad esempio la creazione o l'eliminazione del log, non vengono registrate. Per un elenco completo dei dati registrati vedere [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato del log di Analisi archiviazione](monitor-queue-storage-reference.md).

### <a name="log-anonymous-requests"></a>Richieste di registrazione anonime

Vengono registrati i seguenti tipi di richieste anonime:

- Richieste riuscite
- Errori server
- Errori di timeout per client e server
- Richieste `GET` non riuscite con codice di errore 304 ( `Not Modified` )

Tutte le altre richieste anonime non riuscite non vengono registrate. Per un elenco completo dei dati registrati vedere [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato del log di Analisi archiviazione](monitor-queue-storage-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Accesso ai log in un account di archiviazione

I log vengono visualizzati come BLOB archiviati in un contenitore nell'account di archiviazione di destinazione. I dati vengono raccolti e archiviati all'interno di un singolo BLOB come payload JSON delimitato da righe. Il nome del BLOB si basa sulla convenzione di denominazione seguente:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/queueServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Ad esempio:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Accesso ai log in un hub eventi

I log inviati a un hub eventi non vengono archiviati come file, ma è possibile verificare che l'hub eventi abbia ricevuto le informazioni del log. Nel portale di Azure passare all'hub eventi e verificare che il `incoming requests` conteggio sia maggiore di zero.

![Log di controllo](media/monitor-queue-storage/event-hub-log.png)

È possibile accedere e leggere i dati di log inviati all'hub eventi usando le informazioni di sicurezza e gli strumenti di monitoraggio e gestione degli eventi. Per altre informazioni, vedere [Quali operazioni si possono eseguire con i dati di monitoraggio inviati all'hub eventi?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Accesso ai log in un'area di lavoro Log Analytics

È possibile accedere ai log inviati a un'area di lavoro Log Analytics usando le query di log di Monitoraggio di Azure.

Per altre informazioni, vedere [Introduzione a Log Analytics in Monitoraggio di Azure](../../azure-monitor/logs/log-analytics-tutorial.md).

I dati vengono archiviati nella `StorageQueueLogs` tabella.

#### <a name="sample-kusto-queries"></a>Query Kusto di esempio

Ecco alcune query che è possibile immettere nella barra **di** ricerca log per monitorare le code. Queste query usano il [nuovo linguaggio](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> Quando si seleziona **Log dal** menu del gruppo di risorse dell'account di archiviazione, Log Analytics viene aperto con l'ambito della query impostato sul gruppo di risorse corrente. Ciò significa che le query di log includeranno solo i dati di tale gruppo di risorse. Se si vuole eseguire una query che include dati di altre  risorse o dati di altri servizi di Azure, scegliere Log dal **menu** Monitoraggio di Azure dati. Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](../../azure-monitor/logs/scope.md).

Usare queste query per agevolare il monitoraggio degli account di Archiviazione di Azure:

- Per elencare i 10 errori più comuni negli ultimi tre giorni.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```

- Per elencare le prime 10 operazioni che hanno causato la maggior parte degli errori negli ultimi tre giorni.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```

- Per elencare le prime 10 operazioni con la latenza end-to-end più lunga negli ultimi tre giorni.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```

- Per elencare tutte le operazioni che hanno causato errori di limitazione lato server negli ultimi tre giorni.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```

- Per elencare tutte le richieste con accesso anonimo negli ultimi tre giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```

- Per creare un grafico a torta delle operazioni utilizzate negli ultimi tre giorni.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc
    | render piechart
    ```

## <a name="faq"></a>Domande frequenti

**Le Archiviazione di Azure supportano le metriche per i dischi gestiti o non gestiti?**

No. Le istanze di calcolo supportano le metriche sui dischi. Per altre informazioni, vedere Metriche per disco per dischi gestiti [e non gestiti.](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sui log e sulle metriche creati da archiviazione code di Azure, vedere archiviazione code di Azure [informazioni di riferimento sui dati di monitoraggio.](monitor-queue-storage-reference.md)
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con monitoraggio di Azure](../../azure-monitor/essentials/monitor-azure-resource.md).
- Per altre informazioni sulla migrazione delle metriche, vedere [Migrazione delle metriche di Archiviazione di Azure](../common/storage-metrics-migration.md).
