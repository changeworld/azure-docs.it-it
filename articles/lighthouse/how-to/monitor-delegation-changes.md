---
title: Monitorare le modifiche della delega nel tenant di gestione
description: Informazioni su come monitorare l'attività di delega dai tenant del cliente al tenant di gestione.
ms.date: 02/18/2021
ms.topic: how-to
ms.openlocfilehash: 8bd9e89039c114f3d1088df44198fe00c69bbf82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199066"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitorare le modifiche della delega nel tenant di gestione

Come provider di servizi, è opportuno tenere presente quando le sottoscrizioni dei clienti o i gruppi di risorse vengono delegati al tenant tramite [Azure Lighthouse](../overview.md)o quando le risorse delegate in precedenza vengono rimosse.

Nel tenant di gestione, il [log attività di Azure](../../azure-monitor/essentials/platform-logs-overview.md) tiene traccia dell'attività di delega a livello di tenant. Questa attività registrata include le deleghe aggiunte o rimosse dai tenant del cliente.

In questo argomento vengono illustrate le autorizzazioni necessarie per monitorare l'attività di delega al tenant (in tutti i clienti). Include anche uno script di esempio che mostra un metodo per l'esecuzione di query e la creazione di report su questi dati.

> [!IMPORTANT]
> Tutti questi passaggi devono essere eseguiti nel tenant di gestione, anziché in tutti i tenant del cliente.
>
> Sebbene si faccia riferimento ai provider di servizi e ai clienti in questo argomento, le [aziende che gestiscono più tenant](../concepts/enterprise.md) possono utilizzare gli stessi processi.

## <a name="enable-access-to-tenant-level-data"></a>Abilitare l'accesso ai dati a livello di tenant

Per accedere ai dati del log attività a livello di tenant, a un account deve essere assegnato il ruolo predefinito [lettore di monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) di Azure nell'ambito radice (/). Questa assegnazione deve essere eseguita da un utente che dispone del ruolo di amministratore globale con accesso con privilegi elevati.

### <a name="elevate-access-for-a-global-administrator-account"></a>Elevare l'accesso per un account amministratore globale

Per assegnare un ruolo all'ambito radice (/), sarà necessario disporre del ruolo di amministratore globale con accesso con privilegi elevati. Questo accesso con privilegi elevati deve essere aggiunto solo quando è necessario eseguire l'assegnazione di ruolo, quindi rimuovere al termine dell'operazione.

Per istruzioni dettagliate sull'aggiunta e la rimozione dell'elevazione, vedere [elevare l'accesso per gestire tutti i gruppi di gestione e le sottoscrizioni di Azure](../../role-based-access-control/elevate-access-global-admin.md).

Dopo aver innalzato di livello l'accesso, l'account avrà il ruolo di amministratore accesso utenti in Azure nell'ambito radice. Questa assegnazione di ruolo consente di visualizzare tutte le risorse e di assegnare l'accesso in qualsiasi sottoscrizione o gruppo di gestione nella directory, nonché di effettuare assegnazioni di ruolo nell'ambito radice.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>Assegnare il ruolo di lettore di monitoraggio all'ambito radice

Una volta che l'accesso è stato elevato, è possibile assegnare le autorizzazioni appropriate a un account in modo che sia in grado di eseguire query sui dati del log attività a livello di tenant. Questo account deve avere il ruolo predefinito [lettore di monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) di Azure assegnato all'ambito radice del tenant di gestione.

> [!IMPORTANT]
> La concessione di un'assegnazione di ruolo nell'ambito radice significa che le stesse autorizzazioni verranno applicate a tutte le risorse nel tenant. Poiché si tratta di un ampio livello di accesso, è possibile [assegnare questo ruolo a un account dell'entità servizio e utilizzare tale account per eseguire query sui dati](#use-a-service-principal-account-to-query-the-activity-log). È inoltre possibile assegnare il ruolo di lettore di monitoraggio nell'ambito radice a singoli utenti o gruppi di utenti in modo che possano [visualizzare le informazioni di delega direttamente nel portale di Azure](#view-delegation-changes-in-the-azure-portal). In tal caso, tenere presente che si tratta di un ampio livello di accesso, che dovrebbe essere limitato al minor numero possibile di utenti.

Usare uno dei metodi seguenti per effettuare l'assegnazione dell'ambito radice.

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

Dopo aver assegnato il ruolo di lettore di monitoraggio nell'ambito radice all'account desiderato, assicurarsi di [rimuovere l'accesso con privilegi elevati](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) per l'account amministratore globale, in quanto questo livello di accesso non sarà più necessario.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Visualizzare le modifiche di delega nel portale di Azure

Gli utenti a cui è stato assegnato il ruolo di lettore di monitoraggio nell'ambito radice possono visualizzare le modifiche della delega direttamente nel portale di Azure.

1. Passare alla pagina **clienti personali** , quindi selezionare **log attività** dal menu di spostamento a sinistra.
1. Verificare che l' **attività directory** sia selezionata nel filtro vicino alla parte superiore della schermata.

Verrà visualizzato un elenco di modifiche della delega. È possibile selezionare **modifica colonne** per visualizzare o nascondere lo **stato**, **la categoria di eventi**, l' **ora**, il **timestamp**, la **sottoscrizione**, l' **evento avviato da**, il **gruppo di risorse**, il tipo di **risorsa** e i valori **delle risorse** .

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Screenshot delle modifiche della delega nel portale di Azure.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Usare un account dell'entità servizio per eseguire una query sul log attività

Poiché il ruolo di lettore di monitoraggio nell'ambito radice è un livello di accesso elevato, è possibile assegnare il ruolo a un account dell'entità servizio e utilizzare tale account per eseguire query sui dati utilizzando lo script seguente.

> [!IMPORTANT]
> Attualmente, i tenant con una grande quantità di attività di delega possono verificarsi errori durante l'esecuzione di query su questi dati.

Quando si usa un account dell'entità servizio per eseguire una query sul log attività, sono consigliate le procedure consigliate seguenti:

- [Creare un nuovo account dell'entità servizio](../../active-directory/develop/howto-create-service-principal-portal.md) da usare solo per questa funzione, invece di assegnare questo ruolo a un'entità servizio esistente usata per l'automazione.
- Assicurarsi che questa entità servizio non disponga dell'accesso alle risorse dei clienti Delegate.
- [Usare un certificato per autenticare](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) e [archiviarlo in modo sicuro in Azure Key Vault](../../key-vault/general/security-overview.md).
- Limitare gli utenti che dispongono dell'accesso per agire per conto dell'entità servizio.

Dopo aver creato un nuovo account dell'entità servizio con il monitoraggio dell'accesso del lettore all'ambito radice del tenant di gestione, è possibile usarlo per eseguire query e creare report sulle attività di delega nel tenant.

[Questo script di Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) può essere usato per eseguire una query dell'ultimo giorno di attività e per i report sulle deleghe aggiunte o rimosse o sui tentativi non riusciti. Esegue una query sui dati del [log attività del tenant](/rest/api/monitor/TenantActivityLogs/List) , quindi costruisce i valori seguenti per generare report sulle deleghe aggiunte o rimosse:

- **DelegatedResourceId**: ID della sottoscrizione o del gruppo di risorse delegato
- **CustomerTenantId**: ID tenant del cliente
- **CustomerSubscriptionId**: ID sottoscrizione delegato o che contiene il gruppo di risorse delegato
- **CustomerDelegationStatus**: modifica dello stato per la risorsa delegata (SUCCEEDED o Failed)
- **EventTimeStamp**: data e ora in cui è stata registrata la modifica della delega

Quando si eseguono query su questi dati, tenere presente quanto segue:

- Se più gruppi di risorse vengono delegati in una singola distribuzione, verranno restituite voci separate per ogni gruppo di risorse.
- Le modifiche apportate a una delega precedente, ad esempio l'aggiornamento della struttura delle autorizzazioni, verranno registrate come delega aggiuntiva.
- Come indicato in precedenza, per accedere ai dati a livello di tenant, un account deve avere il ruolo predefinito del lettore monitoraggio di Azure nell'ambito radice (/).
- È possibile usare questi dati nei flussi di lavoro e nella creazione di report personalizzati. Ad esempio, è possibile usare l' [API dell'agente di raccolta dati http (anteprima pubblica)](../../azure-monitor/logs/data-collector-api.md) per registrare i dati in monitoraggio di Azure da un client dell'API REST, quindi usare i [gruppi di azioni](../../azure-monitor/alerts/action-groups.md) per creare notifiche o avvisi.

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

- Informazioni su come caricare i clienti nel [Faro di Azure](../concepts/azure-delegated-resource-management.md).
- Informazioni su [monitoraggio di Azure](../../azure-monitor/index.yml) e [log attività di Azure](../../azure-monitor/essentials/platform-logs-overview.md).
- Esaminare la cartella di lavoro [di esempio log attività per dominio](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) per informazioni su come visualizzare i log attività di Azure tra le sottoscrizioni con un'opzione per filtrarli in base al nome di dominio.
