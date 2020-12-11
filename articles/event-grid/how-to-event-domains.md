---
title: Pubblicare eventi con domini eventi con griglia di eventi di Azure
description: Mostra come gestire set di argomenti di grandi dimensioni in Griglia di eventi di Azure e pubblicarvi eventi con Domini eventi.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e6861e89def10eec391bf302b1ddc726b38bb98c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109896"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Gestire argomenti e pubblicare eventi con Domini eventi

Questo articolo illustra come:

* Creare un dominio di Griglia di eventi
* Eseguire la sottoscrizione agli argomenti di Griglia di eventi
* List keys
* Pubblicare eventi in un dominio

Per informazioni su Domini eventi, vedere [Informazioni sui domini eventi per la gestione di argomenti di Griglia di eventi](event-domains.md).

## <a name="create-an-event-domain"></a>Creare un dominio eventi

Per gestire set di argomenti di grandi dimensioni, creare un dominio eventi.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)

```azurecli-interactive
az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
New-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

In seguito alla creazione verranno restituiti i valori seguenti:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Prendere nota del valore di `endpoint` e `id` perché verranno richiesti per gestire il dominio e pubblicare eventi.

## <a name="manage-access-to-topics"></a>Gestire l'accesso agli argomenti

Per gestire l'accesso agli argomenti, si usa l'[assegnazione di ruolo](../role-based-access-control/role-assignments-cli.md). L'assegnazione di ruolo usa il controllo degli accessi in base al ruolo di Azure per limitare le operazioni sulle risorse di Azure agli utenti autorizzati in un determinato ambito.

In Griglia di eventi sono disponibili due ruoli predefiniti che è possibile usare per assegnare agli utenti l'accesso ai diversi argomenti in un dominio. Questi ruoli sono `EventGrid EventSubscription Contributor (Preview)`, che consente di creare ed eliminare le sottoscrizioni, e `EventGrid EventSubscription Reader (Preview)`, che consente solo di visualizzare l'elenco delle sottoscrizioni di eventi.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)
Il comando seguente dell'interfaccia della riga di comando di Azure consente all'utente `alice@contoso.com` di creare ed eliminare le sottoscrizioni di eventi solo per l'argomento `demotopic1`:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Il comando PowerShell seguente consente all'utente `alice@contoso.com` di creare ed eliminare le sottoscrizioni di eventi solo per l'argomento `demotopic1`:

```azurepowershell-interactive
New-AzRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Per altre informazioni sulla gestione dell'accesso per le operazioni di Griglia di eventi, vedere [Sicurezza e autenticazione di Griglia di eventi](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Creare argomenti e sottoscrizioni

Il servizio Griglia di eventi crea e gestisce automaticamente l'argomento corrispondente in un dominio in base alla chiamata per creare una sottoscrizione di eventi per un argomento del dominio. Non è previsto alcun passaggio apposito per creare un argomento in un dominio. Analogamente, quando viene eliminata l'ultima sottoscrizione di eventi per un argomento, viene eliminato anche l'argomento.

La procedura di sottoscrizione di un argomento in un dominio è identica a quella per sottoscrivere qualsiasi altra risorsa di Azure. Per l'ID della risorsa di origine specificare l'ID del dominio eventi restituito in precedenza durante la creazione del dominio. Per specificare l'argomento che si vuole sottoscrivere, aggiungere `/topics/<my-topic>` alla fine dell'ID risorsa di origine. Per creare una sottoscrizione di eventi con ambito di dominio che riceva tutti gli eventi nel dominio, specificare l'ID del dominio eventi senza specificare alcun argomento.

In genere, l'utente a cui è stato concesso l'accesso nella sezione precedente creerà la sottoscrizione. Per semplificare questo articolo, creare la sottoscrizione. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Se è necessario un endpoint di test per sottoscrivere gli eventi, è sempre possibile distribuire un'[app Web preesistente](https://github.com/Azure-Samples/azure-event-grid-viewer) che visualizza gli eventi in ingresso. È possibile inviare gli eventi al sito Web di test all'indirizzo `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>

Le autorizzazioni impostate per un argomento vengono archiviate in Azure Active Directory e devono essere eliminate in modo esplicito. L'eliminazione di una sottoscrizione di evento non revoca l'accesso di un utente per creare sottoscrizioni di eventi se hanno accesso in scrittura a un argomento.


## <a name="publish-events-to-an-event-grid-domain"></a>Pubblicare eventi in un dominio di Griglia di eventi

La pubblicazione di eventi in un dominio equivale alla [pubblicazione in un argomento personalizzato](./post-to-custom-topic.md). Tuttavia, tutti gli eventi vengono pubblicati all'endpoint di dominio invece che nell'argomento personalizzato. Nei dati evento JSON, specificare l'argomento a cui si desidera che vengano inviati gli eventi. Con la matrice di eventi seguente l'evento con `"id": "1111"` viene inviato all'argomento `demotopic1`, mentre l'evento con `"id": "2222"` viene inviato all'argomento `demotopic2`:

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)
Per ottenere l'endpoint di dominio con l'interfaccia della riga di comando di Azure, usare

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Per ottenere le chiavi per un dominio usare:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Per ottenere l'endpoint di dominio con PowerShell, usare

```azurepowershell-interactive
Get-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Per ottenere le chiavi per un dominio usare:

```azurepowershell-interactive
Get-AzEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

Usare quindi il metodo preferito per effettuare una richiesta HTTP POST per la pubblicazione di eventi nel dominio di Griglia di eventi.

## <a name="search-lists-of-topics-or-subscriptions"></a>Cerca elenchi di argomenti o sottoscrizioni

Per la ricerca e la gestione di un numero elevato di argomenti o sottoscrizioni, le API di griglia di eventi supportano l'elenco e l'impaginazione.

### <a name="using-cli"></a>Uso dell'interfaccia della riga di comando
Ad esempio, il comando seguente elenca tutti gli argomenti il cui nome contiene `mytopic` . 

```azurecli-interactive
az eventgrid topic list --odata-query "contains(name, 'mytopic')"
```

Per ulteriori informazioni su questo comando, vedere [`az eventgrid topic list`](/cli/azure/eventgrid/topic?#az_eventgrid_topic_list) . 


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui concetti generali relativi ai domini eventi e sulla loro utilità, vedere la [panoramica concettuale dei domini eventi](event-domains.md).
