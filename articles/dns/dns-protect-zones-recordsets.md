---
title: Protezione di zone e record DNS - DNS di Azure
description: In questo percorso di apprendimento è possibile iniziare a proteggere le zone DNS e i set di record Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 9d65e024e9efa3ad2bcb1c70d44360c8bd0de384
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785855"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Come proteggere le zone e i record DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Le zone e i record DNS sono risorse critiche. L'eliminazione di una zona DNS o di un singolo record DNS può causare un'interruzione del servizio. È importante che le zone e i record DNS siano protetti da modifiche non autorizzate o accidentali.

Questo articolo illustra come DNS di Azure consente di proteggere le zone e i record DNS privati da tali modifiche.  Vengono applicate due potenti funzionalità di sicurezza fornite da Azure Resource Manager: il controllo degli accessi in base al ruolo [di Azure e](../role-based-access-control/overview.md) i blocchi delle [risorse.](../azure-resource-manager/management/lock-resources.md)

## <a name="azure-role-based-access-control"></a>Controllo degli accessi in base al ruolo di Azure

Il controllo degli accessi in base al ruolo di Azure consente una gestione granulare degli accessi per utenti, gruppi e risorse di Azure. Con il controllo degli accessi in base al ruolo di Azure è possibile concedere il livello di accesso necessario agli utenti. Per altre informazioni sul modo in cui il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso, vedere Informazioni sul controllo degli accessi in base al [ruolo di Azure.](../role-based-access-control/overview.md)

### <a name="the-dns-zone-contributor-role"></a>Ruolo Collaboratore zona DNS

Il ruolo Collaboratore zona DNS è un ruolo predefinito per la gestione delle risorse DNS private. Questo ruolo applicato a un utente o a un gruppo consente di gestire le risorse DNS.

Il gruppo di risorse *myResourceGroup* contiene cinque zone per Contoso Corporation. La concessione dell'autorizzazione Collaboratore zona DNS all'amministratore DNS di questo gruppo di risorse consente il controllo completo su queste zone DNS. Evita di concedere autorizzazioni non necessarie. L'amministratore DNS non può creare o arrestare macchine virtuali.

Il modo più semplice per assegnare le autorizzazioni del controllo degli accessi in base al ruolo di Azure [è tramite portale di Azure](../role-based-access-control/role-assignments-portal.md).  

Aprire **Controllo di accesso (IAM) per** il gruppo di risorse, quindi selezionare **Aggiungi,** quindi selezionare il **ruolo Collaboratore zona DNS.** Selezionare gli utenti o i gruppi necessari per concedere le autorizzazioni.

![Controllo degli accessi in base al ruolo di Azure a livello di gruppo di risorse tramite portale di Azure](./media/dns-protect-zones-recordsets/rbac1.png)

Queste autorizzazioni possono essere concesse anche [tramite Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-azure-rbac"></a>Controllo degli accessi in base al ruolo di Azure a livello di zona

Le regole del Controllo degli accessi in base al ruolo di Azure possono essere applicate a una sottoscrizione, a un gruppo di risorse o a una singola risorsa. Tale risorsa può essere una singola zona DNS o un singolo set di record.

Ad esempio, il gruppo di risorse *myResourceGroup contiene* la *zona* contoso.com e una sottozone *customers.contoso.com*. I record CNAME vengono creati per ogni account cliente. All'account amministratore usato per gestire i record CNAME vengono assegnate le autorizzazioni per creare record *customers.contoso.com* zona. L'account può gestire *solo customers.contoso.com.*

Le autorizzazioni di Controllo degli accessi in base al ruolo di Azure a livello di zona possono essere concesse tramite il portale di Azure.  Aprire **Controllo di accesso (IAM)** per la zona, selezionare Aggiungi, quindi selezionare il ruolo Collaboratore zona **DNS** e selezionare gli utenti o i gruppi necessari per concedere le autorizzazioni. 

![Controllo degli accessi in base al ruolo di Azure a livello di zona DNS tramite il portale di Azure](./media/dns-protect-zones-recordsets/rbac2.png)

Queste autorizzazioni possono essere concesse anche [tramite Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Controllo degli accessi in base al ruolo di Azure a livello di set di record

Le autorizzazioni vengono applicate a livello di set di record.  All'utente viene concesso il controllo alle voci necessarie e non è in grado di apportare altre modifiche.

Le autorizzazioni di Controllo degli accessi in base al ruolo di Azure a livello di set di record possono essere configurate tramite il portale di Azure, usando il pulsante Controllo di accesso **(IAM)** nella pagina del set di record:

![Controllo degli accessi in base al ruolo di Azure a livello di set di record tramite il portale di Azure](./media/dns-protect-zones-recordsets/rbac3.png)

Le autorizzazioni di Controllo degli accessi in base al ruolo di Azure a livello di set di record possono essere [concesse anche Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Ruoli personalizzati

Il ruolo Collaboratore zona DNS predefinito consente il controllo completo su una risorsa DNS. È possibile creare ruoli di Azure personalizzati per fornire un controllo più granulare.

All'account usato per gestire I record CNAME viene concessa l'autorizzazione per gestire solo i record CNAME. L'account non è in grado di modificare i record di altri tipi. L'account non è in grado di eseguire operazioni a livello di zona, ad esempio l'eliminazione della zona.

L'esempio seguente illustra la definizione di un ruolo personalizzato per gestire solo record CNAME:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

La proprietà Actions definisce le autorizzazioni specifiche di DNS seguenti:

* `Microsoft.Network/dnsZones/CNAME/*` concede il controllo completo sui record CNAME
* `Microsoft.Network/dnsZones/read` concede l'autorizzazione per leggere le zone DNS, ma non per modificarle, consentendo di visualizzare l'area in cui viene creato il record CNAME.

Le azioni rimanenti vengono copiate dal [ruolo di collaboratore zona DNS predefinito](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> L'uso di un ruolo personalizzato di Azure per impedire l'eliminazione di set di record consentendo al tempo stesso di aggiornarli non è un controllo efficace. Impedisce l'eliminazione di set di record, ma non la relativa modifica.  Le modifiche consentite includono l'aggiunta e la rimozione di record dal set di record, inclusa la rimozione di tutti i record per ottenere un set di record vuoto. Questo è lo stesso effetto ottenuto eliminando il set di record dal punto di vista della risoluzione DNS.

Le definizioni di ruolo personalizzate non possono attualmente essere definite tramite il portale di Azure. È possibile creare un ruolo personalizzato basato su questa definizione di ruolo tramite Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Può anche essere creato tramite l'interfaccia della riga di comando di Azure:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

Il ruolo può quindi essere assegnato come avviene per i ruoli predefiniti, come descritto in precedenza in questo articolo.

Per altre informazioni su come creare, gestire e assegnare ruoli personalizzati, vedere Ruoli [personalizzati di Azure.](../role-based-access-control/custom-roles.md)

## <a name="resource-locks"></a>Blocchi risorse

Azure Resource Manager supporta un altro tipo di controllo di sicurezza, la possibilità di bloccare le risorse. I blocchi delle risorse vengono applicati alla risorsa e sono efficaci per tutti gli utenti e i ruoli. Per altre informazioni, vedere [Bloccare le risorse con Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Esistono due tipi di blocco delle risorse: **CanNotDelete** **e ReadOnly.** Questi tipi di blocco possono essere applicati a una DNS privato o a un singolo set di record. Le sezioni seguenti descrivono diversi scenari comuni e come supportarli usando i blocchi risorse.

### <a name="protecting-against-all-changes"></a>Protezione da tutte le modifiche

Per impedire che vengano apportate modifiche, applicare un blocco ReadOnly alla zona. Questo blocco impedisce la creazione di nuovi set di record e la modifica o l'eliminazione di set di record esistenti.

I blocchi risorse a livello di zona possono essere creati tramite il portale di Azure.  Nella pagina della zona DNS, selezionare **Blocchi**, quindi selezionare **+Aggiungi**:

![Blocchi risorse a livello di zona tramite il portale di Azure](./media/dns-protect-zones-recordsets/locks1.png)

I blocchi delle risorse a livello di zona possono essere creati anche [tramite Azure PowerShell](/powershell/module/az.resources/new-azresourcelock):

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](/cli/azure/lock#az_lock_create):

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Protezione di singoli record

Per evitare che venga modificato un set di record DNS esistente, impostare il blocco ReadOnly al set di record.

> [!NOTE]
> L'applicazione di un blocco CanNotDelete a un set di record non è un controllo efficace. Impedisce l'eliminazione del set di record, ma non impedisce che venga modificato.  Le modifiche consentite includono l'aggiunta e la rimozione di record dal set di record, inclusa la rimozione di tutti i record per ottenere un set di record vuoto. Questo è lo stesso effetto ottenuto eliminando il set di record dal punto di vista della risoluzione DNS.

I blocchi risorse a livello di set di record possono attualmente essere configurati solo tramite Azure PowerShell.  Non sono supportate nell'interfaccia della riga di comando portale di Azure o di Azure.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>Protezione dall'eliminazione di zone

Quando una zona viene eliminata in DNS di Azure, vengono eliminati tutti i set di record nella zona.  Questa operazione non può essere annullata. L'eliminazione accidentale di una zona critica può avere un impatto notevole.  È importante proteggersi dall'eliminazione accidentale della zona.

L'applicazione di un blocco CanNotDelete a una zona impedisce l'eliminazione della zona. I blocchi vengono ereditati dalle risorse figlio. Un blocco impedisce l'eliminazione di qualsiasi set di record nella zona. Come descritto nella nota precedente, non è efficace perché i record possono comunque essere rimossi dai set di record esistenti.

In alternativa, applicare un blocco CanNotDelete a un set di record nella zona, ad esempio il set di record SOA. La zona non viene eliminata senza eliminare anche i set di record. Questo blocco protegge dall'eliminazione della zona, consentendo comunque di modificare liberamente i set di record all'interno della zona. Se si tenta di eliminare la zona, Azure Resource Manager rileva questa rimozione. La rimozione eliminerebbe anche il set di record SOA, Azure Resource Manager blocca la chiamata perché il soa è bloccato.  Nessun set di record viene eliminato.

Il comando PowerShell seguente crea un blocco CanNotDelete sul record SOA della zona specificata:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Un'altra opzione per impedire l'eliminazione accidentale della zona è l'uso di un ruolo personalizzato. Questo ruolo garantisce che gli account usati per gestire le zone non dispongono delle autorizzazioni di eliminazione della zona. 

Quando è necessario eliminare una zona, è possibile applicare un'eliminazione in due passaggi:

 - Concedere prima di tutto le autorizzazioni di eliminazione della zona
 - Concedere quindi le autorizzazioni per eliminare la zona.

Il ruolo personalizzato funziona per tutte le zone accessibili da tali account. Gli account con autorizzazioni di eliminazione della zona, ad esempio il proprietario della sottoscrizione, possono comunque eliminare accidentalmente una zona.

È possibile usare entrambi gli approcci, blocchi delle risorse e ruoli personalizzati, allo stesso tempo, come approccio di difesa avanzata alla protezione della zona DNS.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'uso del controllo degli accessi in base al ruolo di Azure, vedere Informazioni sul controllo degli accessi in base al ruolo di [Azure.](../role-based-access-control/overview.md)
* Per altre informazioni sull'uso dei blocchi risorse, vedere [Bloccare le risorse con Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
