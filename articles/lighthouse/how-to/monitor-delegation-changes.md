---
title: Monitorare le modifiche di delega nel tenant di gestione
description: Informazioni su come monitorare l'attività di delega dai tenant dei clienti al tenant di gestione.
ms.date: 02/18/2021
ms.topic: how-to
ms.openlocfilehash: 1a12b916fae9794d6d695191a81ec076917bda31
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814893"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitorare le modifiche di delega nel tenant di gestione

Un provider di servizi può essere utile sapere quando le sottoscrizioni dei clienti o i gruppi di risorse vengono delegati al tenant [tramite Azure Lighthouse](../overview.md)o quando le risorse delegate in precedenza vengono rimosse.

Nel tenant di gestione, il [log attività di Azure tiene traccia](../../azure-monitor/essentials/platform-logs-overview.md) dell'attività di delega a livello di tenant. Questa attività registrata include eventuali deleghe aggiunte o rimosse dai tenant dei clienti.

Questo argomento illustra le autorizzazioni necessarie per monitorare l'attività di delega al tenant (in tutti i clienti). Include anche uno script di esempio che illustra un metodo per l'esecuzione di query e la creazione di report su questi dati.

> [!IMPORTANT]
> Tutti questi passaggi devono essere eseguiti nel tenant di gestione, anziché nei tenant dei clienti.
>
> Anche se in questo argomento si fa riferimento ai provider di servizi e ai clienti, le aziende [che gestiscono](../concepts/enterprise.md) più tenant possono usare gli stessi processi.

## <a name="enable-access-to-tenant-level-data"></a>Abilitare l'accesso ai dati a livello di tenant

Per accedere ai dati del log attività a livello di tenant, a un account deve essere assegnato il ruolo predefinito Di [Azure](../../role-based-access-control/built-in-roles.md#monitoring-reader) lettore di monitoraggio nell'ambito radice (/). Questa assegnazione deve essere eseguita da un utente con il ruolo amministratore globale con accesso con privilegi elevati aggiuntivi.

### <a name="elevate-access-for-a-global-administrator-account"></a>Elevare l'accesso per un account amministratore globale

Per assegnare un ruolo nell'ambito radice (/), è necessario disporre del ruolo Amministratore globale con accesso con privilegi elevati. Questo accesso con privilegi elevati deve essere aggiunto solo quando è necessario eseguire l'assegnazione di ruolo e quindi rimosso al termine.

Per istruzioni dettagliate sull'aggiunta e la rimozione dell'elevazione dei privilegi, vedere Elevare l'accesso [per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure.](../../role-based-access-control/elevate-access-global-admin.md)

Dopo aver elevato l'accesso, l'account avrà il ruolo Amministratore accesso utente in Azure nell'ambito radice. Questa assegnazione di ruolo consente di visualizzare tutte le risorse e assegnare l'accesso in qualsiasi sottoscrizione o gruppo di gestione nella directory, nonché di eseguire assegnazioni di ruolo nell'ambito radice.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>Assegnare il ruolo lettore di monitoraggio nell'ambito radice

Dopo aver elevato l'accesso, è possibile assegnare le autorizzazioni appropriate a un account in modo che possa eseguire query sui dati del log attività a livello di tenant. A questo account dovrà essere [assegnato](../../role-based-access-control/built-in-roles.md#monitoring-reader) il ruolo predefinito di Azure Lettore di monitoraggio nell'ambito radice del tenant di gestione.

> [!IMPORTANT]
> La concessione di un'assegnazione di ruolo nell'ambito radice significa che le stesse autorizzazioni verranno applicate a ogni risorsa nel tenant. Poiché si tratta di un ampio livello di accesso, è possibile assegnare questo ruolo a un account dell'entità servizio e usare tale account per [eseguire query sui dati](#use-a-service-principal-account-to-query-the-activity-log). È anche possibile assegnare il ruolo lettore di monitoraggio [nell'ambito radice](#view-delegation-changes-in-the-azure-portal)a singoli utenti o gruppi di utenti in modo che possano visualizzare le informazioni di delega direttamente nel portale di Azure . In questo caso, tenere presente che si tratta di un livello di accesso ampio che deve essere limitato al numero più basso possibile di utenti.

Usare uno dei metodi seguenti per eseguire l'assegnazione dell'ambito radice.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ObjectId <objectId> 
```

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Rimuovere l'accesso con privilegi elevati per l'account amministratore globale

Dopo aver assegnato il ruolo di lettore di monitoraggio nell'ambito radice all'account desiderato, assicurarsi di rimuovere l'accesso con privilegi elevati per l'account amministratore globale, perché questo livello di accesso non sarà più necessario. [](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access)

## <a name="view-delegation-changes-in-the-azure-portal"></a>Visualizzare le modifiche della delega nel portale di Azure

Gli utenti a cui è stato assegnato il ruolo di lettore di monitoraggio nell'ambito radice possono visualizzare le modifiche della delega direttamente nel portale di Azure.

1. Passare alla pagina **Clienti** e quindi selezionare **Log attività** dal menu di spostamento a sinistra.
1. Assicurarsi che **l'attività** directory sia selezionata nel filtro nella parte superiore della schermata.

Verrà visualizzato un elenco di modifiche di delega. È possibile **selezionare** Modifica colonne per visualizzare o nascondere i valori **Stato** **,** Categoria evento **,** Ora **,** Timestamp **,** Sottoscrizione , **Evento** avviato da **,** Gruppo di risorse **,** Tipo di **risorsa** e Risorsa.

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Screenshot delle modifiche della delega nel portale di Azure.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Usare un account dell'entità servizio per eseguire query sul log attività

Poiché il ruolo Lettore di monitoraggio nell'ambito radice è un livello di accesso così ampio, è possibile assegnare il ruolo a un account dell'entità servizio e usare tale account per eseguire query sui dati usando lo script seguente.

> [!IMPORTANT]
> Attualmente, i tenant con una grande quantità di attività di delega possono verificarsi errori durante l'esecuzione di query su questi dati.

Quando si usa un account dell'entità servizio per eseguire query sul log attività, è consigliabile seguire queste procedure consigliate:

- [Creare un nuovo account dell'entità](../../active-directory/develop/howto-create-service-principal-portal.md) servizio da usare solo per questa funzione, anziché assegnare questo ruolo a un'entità servizio esistente usata per altre attività di automazione.
- Assicurarsi che questa entità servizio non abbia accesso ad alcuna risorsa del cliente delegata.
- [Usare un certificato per autenticarlo](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) e [archiviarlo in](../../key-vault/general/security-features.md)modo sicuro in Azure Key Vault .
- Limitare gli utenti che hanno accesso ad agire per conto dell'entità servizio.

Dopo aver creato un nuovo account entità servizio con l'accesso lettore di monitoraggio all'ambito radice del tenant di gestione, è possibile usarlo per eseguire query e creare report sull'attività di delega nel tenant.

[Questo Azure PowerShell script](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) può essere usato per eseguire query sugli ultimi 1 giorno di attività e sui report su eventuali deleghe aggiunte o rimosse (o tentativi non riusciti). Esegue una query [sui dati del log](/rest/api/monitor/TenantActivityLogs/List) attività del tenant e quindi costruisce i valori seguenti per segnalare le deleghe aggiunte o rimosse:

- **DelegatedResourceId:** ID della sottoscrizione o del gruppo di risorse delegato
- **CustomerTenantId:** ID tenant del cliente
- **CustomerSubscriptionId:** ID sottoscrizione delegato o contenente il gruppo di risorse delegato
- **CustomerDelegationStatus:** modifica dello stato per la risorsa delegata (riuscita o non riuscita)
- **EventTimeStamp:** data e ora in cui è stata registrata la modifica della delega

Durante l'esecuzione di query su questi dati, tenere presente:

- Se più gruppi di risorse vengono delegati in una singola distribuzione, verranno restituite voci separate per ogni gruppo di risorse.
- Le modifiche apportate a una delega precedente, ad esempio l'aggiornamento della struttura delle autorizzazioni, verranno registrate come delega aggiunta.
- Come indicato in precedenza, per accedere a questi dati a livello di tenant, un account deve avere il ruolo predefinito di Azure lettore di monitoraggio nell'ambito radice (/).
- È possibile usare questi dati nei propri flussi di lavoro e report. Ad esempio, è possibile usare l'API dell'agente di raccolta dati [HTTP (anteprima pubblica)](../../azure-monitor/logs/data-collector-api.md) per registrare i dati Monitoraggio di Azure da un client API REST, quindi usare i gruppi di azioni per creare notifiche o avvisi. [](../../azure-monitor/alerts/action-groups.md)

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire l'onboard dei [clienti Azure Lighthouse](../concepts/azure-delegated-resource-management.md).
- Informazioni su [Monitoraggio di Azure](../../azure-monitor/index.yml) log attività [di Azure.](../../azure-monitor/essentials/platform-logs-overview.md)
- Esaminare la [cartella di lavoro di esempio](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) Log attività per dominio per informazioni su come visualizzare i log attività di Azure tra sottoscrizioni con un'opzione per filtrarli in base al nome di dominio.
