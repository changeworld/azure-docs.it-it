---
title: Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse
description: Inviare Monitoraggio di Azure metriche e i log della piattaforma Monitoraggio di Azure log, archiviazione di Azure o Hub eventi di Azure usando un'impostazione di diagnostica.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 60ac56cfda026871afa1725bbd54625b7ce7585e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789196"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse
[I log della](./platform-logs-overview.md) piattaforma in Azure, inclusi il log attività di Azure e i log delle risorse, forniscono informazioni di diagnostica e controllo dettagliate per le risorse di Azure e la piattaforma Azure da cui dipendono. [Le metriche della](./data-platform-metrics.md) piattaforma vengono raccolte per impostazione predefinita e in genere archiviate nel database Monitoraggio di Azure metriche. Questo articolo fornisce informazioni dettagliate sulla creazione e la configurazione delle impostazioni di diagnostica per inviare metriche della piattaforma e log della piattaforma a destinazioni diverse.

> [!IMPORTANT]
> Prima di creare un'impostazione di diagnostica per il log attività, è necessario disabilitare qualsiasi configurazione legacy. Per [informazioni dettagliate, vedere Metodi](../essentials/activity-log.md#legacy-collection-methods) di raccolta legacy.

Ogni risorsa di Azure richiede una propria impostazione di diagnostica, che definisce i criteri seguenti:

- Categorie di log e dati di metriche inviati alle destinazioni definite nell'impostazione. Le categorie disponibili variano per i diversi tipi di risorsa.
- una o più destinazioni a cui inviare i log. Le destinazioni correnti includono l'area di lavoro di Log Analytics, Hub eventi e Archiviazione di Azure.

Una singola impostazione di diagnostica può definire non più di una delle destinazioni. Se si vogliono inviare dati a più tipi specifici di destinazione (ad esempio, due diverse aree di lavoro di Log Analytics), creare più impostazioni. Ogni risorsa può avere al massimo 5 impostazioni di diagnostica.

Il video seguente illustra i log della piattaforma di routing con le impostazioni di diagnostica.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [Le metriche della](./metrics-supported.md) piattaforma vengono inviate automaticamente [Monitoraggio di Azure metriche .](./data-platform-metrics.md) Le impostazioni di diagnostica possono essere usate per inviare le metriche per determinati servizi di Azure nei log di Monitoraggio di Azure per l'analisi con altri dati di monitoraggio usando query [di log](../logs/log-query-overview.md) con determinate limitazioni. 
>  
>  
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione. *Ad esempio:* la metrica "IOReadBytes" in una blockchain può essere esplorata e grafici a livello di nodo. Tuttavia, quando viene esportata tramite le impostazioni di diagnostica, la metrica esportata rappresenta come tutti i byte letti per tutti i nodi. Inoltre, a causa di limitazioni interne, non tutte le metriche possono essere esportate Monitoraggio di Azure log/Log Analytics. Per altre informazioni, vedere [l'elenco delle metriche esportabili.](./metrics-supported-export-diagnostic-settings.md) 
>  
>  
> Per aggirare queste limitazioni per metriche specifiche, è consigliabile estrarle manualmente usando [l'API REST](/rest/api/monitor/metrics/list) delle metriche e importarle nei log di Monitoraggio di Azure usando l'API dell'agente di raccolta dati [di Monitoraggio di Azure](../logs/data-collector-api.md).  


## <a name="destinations"></a>Destinazioni
I log e le metriche della piattaforma possono essere inviati alle destinazioni nella tabella seguente. 

| Destination | Descrizione |
|:---|:---|
| [area di lavoro Log Analytics](../logs/design-logs-deployment.md) | L'invio di log e metriche a un'area di lavoro Log Analytics consente di analizzarli con altri dati di monitoraggio raccolti da Monitoraggio di Azure usando query di log avanzate e anche di sfruttare altre funzionalità di Monitoraggio di Azure, ad esempio avvisi e visualizzazioni. |
| [Hub eventi](../../event-hubs/index.yml) | L'invio di log e metriche a Hub eventi consente di trasmettere dati a sistemi esterni, ad esempio SIEM di terze parti e altre soluzioni di log analytics.  |
| [Account di archiviazione di Azure](../../storage/blobs/index.yml) | L'archiviazione di log e metriche in un account di archiviazione di Azure è utile per il controllo, l'analisi statica o il backup. Rispetto ai Monitoraggio di Azure log e a un'area di lavoro Log Analytics, l'archiviazione di Azure è meno costosa e i log possono essere conservati all'infinito.  |


### <a name="destination-requirements"></a>Requisiti di destinazione

Prima di creare le impostazioni di diagnostica, è necessario creare tutte le destinazioni per l'impostazione di diagnostica. La destinazione non deve essere nella stessa sottoscrizione della risorsa che invia i log, purché l'utente che configura l'impostazione abbia accesso RBAC di Azure appropriato a entrambe le sottoscrizioni. La tabella seguente fornisce requisiti univoci per ogni destinazione, incluse eventuali restrizioni a livello di regione.

| Destination | Requisiti |
|:---|:---|
| Area di lavoro Log Analytics | L'area di lavoro non deve essere nella stessa area della risorsa monitorata.|
| Hub eventi | I criteri di accesso condiviso per lo spazio dei nomi definiscono le autorizzazioni di cui dispone il meccanismo di streaming. Lo streaming in Hub eventi richiede le autorizzazioni Di gestione, Invio e Ascolto. Per aggiornare l'impostazione di diagnostica in modo da includere lo streaming, è necessario disporre dell'autorizzazione ListKey per la regola di autorizzazione di Hub eventi.<br><br>Lo spazio dei nomi dell'hub eventi deve essere nella stessa area della risorsa monitorata se la risorsa è a livello di area. |
| Account di archiviazione di Azure | Non è consigliabile usare un account di archiviazione esistente in cui sono archiviati altri dati non di monitoraggio, in modo da poter controllare meglio l'accesso ai dati. Se tuttavia si archiviano il log attività e i log delle risorse, è possibile scegliere di usare lo stesso account di archiviazione per mantenere tutti i dati di monitoraggio in una posizione centrale.<br><br>Per inviare i dati all'archiviazione non modificabile, impostare i criteri non modificabili per l'account di archiviazione come descritto in Impostare e gestire i criteri di [immutabilità per l'archiviazione BLOB.](../../storage/blobs/storage-blob-immutability-policies-manage.md) È necessario seguire tutti i passaggi descritti in questo articolo, inclusa l'abilitazione delle scritture di BLOB di accodamento protette.<br><br>L'account di archiviazione deve essere nella stessa area della risorsa monitorata se la risorsa è a livello di area. |

> [!NOTE]
> Gli account di Azure Data Lake Storage Gen2 non sono attualmente supportati come destinazione per le impostazioni di diagnostica anche se possono essere elencati come opzioni valide nel portale di Azure.

> [!NOTE]
> Monitoraggio di Azure (Impostazioni di diagnostica) non può accedere alle risorse di Hub eventi quando sono abilitate le reti virtuali. È necessario abilitare l'impostazione Consenti servizi Microsoft attendibili di ignorare questo firewall nell'hub eventi, in modo che al servizio Monitoraggio di Azure (impostazioni di diagnostica) sia concesso l'accesso alle risorse di Hub eventi. 


## <a name="create-in-azure-portal"></a>Creare nel portale di Azure

È possibile configurare le impostazioni di diagnostica portale di Azure dal menu Monitoraggio di Azure o dal menu della risorsa.

1. La posizione in cui si configurano le impostazioni di diagnostica portale di Azure dipende dalla risorsa.

   - Per una singola risorsa, fare clic su Impostazioni **di** diagnostica in **Monitoraggio** nel menu della risorsa.

        ![Screenshot della sezione Monitoraggio di un menu di risorse in portale di Azure con le impostazioni di diagnostica evidenziate.](media/diagnostic-settings/menu-resource.png)

   - Per una o più risorse, fare clic su **Impostazioni** di diagnostica in **Impostazioni** nel menu Monitoraggio di Azure e quindi fare clic sulla risorsa.

        ![Screenshot della sezione Impostazioni nel menu Monitoraggio di Azure con le impostazioni di diagnostica evidenziate.](media/diagnostic-settings/menu-monitor.png)

   - Per Log attività fare clic **su Log attività** nel menu **Monitoraggio di Azure** e quindi su Impostazioni **di diagnostica.** Assicurarsi di disabilitare qualsiasi configurazione legacy per il log attività. Per informazioni [dettagliate, vedere Disabilitare](./activity-log.md#legacy-collection-methods) le impostazioni esistenti.

        ![Screenshot del menu Monitoraggio di Azure con log attività selezionato e Impostazioni di diagnostica evidenziate nella barra dei menu Monitor-Activity log attività.](media/diagnostic-settings/menu-activity-log.png)

2. Se non esiste un'impostazione sulla risorsa selezionata, viene chiesto di creare un'impostazione. Fare clic su **Aggiungi impostazione di diagnostica**.

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/diagnostic-settings/add-setting.png)

   Se sono presenti impostazioni esistenti nella risorsa, viene visualizzato un elenco di impostazioni già configurate. Fare clic **su Aggiungi impostazione di diagnostica** per aggiungere una nuova impostazione o su Modifica **impostazione** per modificarne una esistente. Ogni impostazione non può avere più di uno dei tipi di destinazione.

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-settings/edit-setting.png)

3. Assegnare un nome all'impostazione se non ne ha già uno.

    ![Aggiungi impostazione di diagnostica](media/diagnostic-settings/setting-new-blank.png)

4. **Dettagli categoria (cosa instradare):** selezionare la casella per ogni categoria di dati da inviare alle destinazioni specificate in un secondo momento. L'elenco delle categorie varia per ogni servizio di Azure.

     - **AllMetrics indirizza** le metriche della piattaforma di una risorsa nell'archivio log di Azure, ma in formato di log. Queste metriche vengono in genere inviate solo al database Monitoraggio di Azure di serie tempose delle metriche. L'invio all'archivio Monitoraggio di Azure log ( disponibile per la ricerca tramite Log Analytics) consente di integrarli in query che esere ricerche in altri log. Questa opzione potrebbe non essere disponibile per tutti i tipi di risorse. Quando è supportata, Monitoraggio di Azure [metriche supportate](./metrics-supported.md) elenca le metriche raccolte per i tipi di risorse.

       > [!NOTE]
       > Vedere la limitazione per il routing delle metriche Monitoraggio di Azure log precedenti in questo articolo.  


     - **I** log elencano le diverse categorie disponibili a seconda del tipo di risorsa. Controllare le categorie che si desidera indirizzare a una destinazione.

5. **Dettagli destinazione:** selezionare la casella per ogni destinazione. Quando si seleziona ogni casella, vengono visualizzate opzioni che consentono di aggiungere altre informazioni.

      ![Inviare a Log Analytics o a Hub eventi](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics: immettere** la sottoscrizione e l'area di lavoro.  Se non si ha un'area di lavoro, è necessario [crearne una prima di procedere.](../logs/quick-create-workspace.md)

    1. **Hub eventi:** specificare i criteri seguenti:
       - Sottoscrizione di cui fa parte l'hub eventi
       - Spazio dei nomi dell'hub eventi: se non ne è ancora disponibile uno, è necessario [crearne uno](../../event-hubs/event-hubs-create.md)
       - Nome dell'hub eventi (facoltativo) a cui inviare tutti i dati. Se non si specifica un nome, viene creato un hub eventi per ogni categoria di log. Se si inviano più categorie, è possibile specificare un nome per limitare il numero di hub eventi creati. Per [informazioni dettagliate, vedere Hub eventi di Azure quote e limiti.](../../event-hubs/event-hubs-quotas.md)
       - Un criterio dell'hub eventi (facoltativo) Un criterio definisce le autorizzazioni di cui dispone il meccanismo di streaming. Per altre informazioni, vedere [Event-hubs-features.](../../event-hubs/event-hubs-features.md#publisher-policy)

    1. **Archiviazione:** scegliere la sottoscrizione, l'account di archiviazione e i criteri di conservazione.

        ![Invia all'archiviazione](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > È consigliabile impostare i criteri di conservazione su 0 ed eliminare manualmente i dati dall'archiviazione usando un processo pianificato per evitare possibili confusione in futuro.
        >
        > In primo luogo, se si usa l'archiviazione per l'archiviazione, in genere si vogliono archiviare i dati per più di 365 giorni. In secondo momento, se si sceglie un criterio di conservazione maggiore di 0, la data di scadenza viene allegata ai log al momento dell'archiviazione. Non è possibile modificare la data per tali log dopo l'archiviazione.
        >
        > Ad esempio, se si impostano i criteri di conservazione per *WorkflowRuntime* su 180 giorni e quindi 24 ore dopo impostarlo su 365 giorni, i log archiviati durante le prime 24 ore verranno eliminati automaticamente dopo 180 giorni, mentre tutti i log successivi di quel tipo verranno eliminati automaticamente dopo 365 giorni. La modifica dei criteri di conservazione in un secondo momento non fa in modo che le prime 24 ore di log rimangano per 365 giorni.

6. Fare clic su **Salva**.

Dopo alcuni istanti, la nuova impostazione viene visualizzata nell'elenco di impostazioni per questa risorsa e i log vengono trasmessi alle destinazioni specificate quando vengono generati nuovi dati dell'evento. Possono essere necessario fino a 15 minuti tra il momento in cui viene generato un evento e il momento in cui viene [visualizzato in un'area di lavoro Log Analytics.](../logs/data-ingestion-time.md)

## <a name="create-using-powershell"></a>Creare usando PowerShell

Usare il cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) per creare un'impostazione di diagnostica con [Azure PowerShell](../powershell-samples.md). Per le descrizioni dei relativi parametri, vedere la documentazione relativa a questo cmdlet.

> [!IMPORTANT]
> Non è possibile usare questo metodo per il log attività di Azure. Usare invece [Create diagnostic setting in Monitoraggio di Azure using a Resource Manager template](./resource-manager-diagnostic-settings.md) per creare un modello Resource Manager e distribuirlo con PowerShell.

Di seguito è riportato un cmdlet di PowerShell di esempio per creare un'impostazione di diagnostica usando tutte e tre le destinazioni.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Creare con l'interfaccia della riga di comando di Azure

Usare il [comando az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) per creare un'impostazione di diagnostica con l'interfaccia della riga [di comando di Azure.](/cli/azure/monitor) Per le descrizioni dei relativi parametri, vedere la documentazione di questo comando.

> [!IMPORTANT]
> Non è possibile usare questo metodo per il log attività di Azure. Usare invece Create [diagnostic setting in Monitoraggio di Azure using a Resource Manager template](./resource-manager-diagnostic-settings.md) per creare un modello Resource Manager e distribuirlo con l'interfaccia della riga di comando.

Di seguito è riportato un comando dell'interfaccia della riga di comando di esempio per creare un'impostazione di diagnostica usando tutte e tre le destinazioni. La sintassi è leggermente diversa a seconda del client.

# <a name="cmd"></a>[CMD](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>Creare usando Resource Manager modello
Vedere [Resource Manager esempi di modello per le impostazioni di diagnostica in](./resource-manager-diagnostic-settings.md) Monitoraggio di Azure per creare o aggiornare le impostazioni di diagnostica con un Resource Manager modello.

## <a name="create-using-rest-api"></a>Creare usando l'API REST
Vedere [Impostazioni di diagnostica](/rest/api/monitor/diagnosticsettings) per creare o aggiornare le impostazioni di diagnostica usando l'API REST [Monitoraggio di Azure.](/rest/api/monitor/)

## <a name="create-using-azure-policy"></a>Creare usando Criteri di Azure
Poiché è necessario creare un'impostazione di diagnostica per ogni risorsa di Azure, Criteri di Azure possibile usare per creare automaticamente un'impostazione di diagnostica quando viene creata ogni risorsa. Per [informazioni dettagliate, vedere](../deploy-scale.md) Distribuire Monitoraggio di Azure su larga scala usando Criteri di Azure.

## <a name="metric-category-is-not-supported-error"></a>Errore della categoria metrica non supportato
Quando si distribuisce un'impostazione di diagnostica, viene visualizzato il messaggio di errore seguente:

   "La categoria di metriche '*xxxx*' non è supportata"

Ad esempio: 

   "La categoria di metriche 'ActionsFailed' non è supportata"

in cui in precedenza la distribuzione ha avuto esito positivo. 

Il problema si verifica quando si usa un modello di Resource Manager, l'API REST delle impostazioni di diagnostica, l'interfaccia della riga di comando di Azure o Azure PowerShell. Le impostazioni di diagnostica create portale di Azure non sono interessate dal fatto che vengono presentati solo i nomi di categoria supportati.

Il problema è causato da una modifica recente nell'API sottostante. Le categorie di metriche diverse da "AllMetrics" non sono supportate e non sono mai state tranne alcuni servizi di Azure molto specifici. In passato, altri nomi di categoria venivano ignorati durante la distribuzione di un'impostazione di diagnostica. Il Monitoraggio di Azure back-end ha semplicemente reindirizzato queste categorie a 'AllMetrics'.  A partire da febbraio 2021, il back-end è stato aggiornato per confermare in modo specifico che la categoria di metriche specificata sia accurata. Questa modifica ha causato l'esito negativo di alcune distribuzioni.

Se viene visualizzato questo errore, aggiornare le distribuzioni in modo da sostituire i nomi delle categorie delle metriche con "AllMetrics" per risolvere il problema. Se in precedenza la distribuzione aggiungeva più categorie, deve essere mantenuta solo una con il riferimento 'AllMetrics'. Se il problema persiste, contattare il supporto tecnico di Azure tramite il portale di Azure. 



## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui log della piattaforma Azure](./platform-logs-overview.md)
