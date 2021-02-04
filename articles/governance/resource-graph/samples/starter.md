---
title: Esempi di query di base
description: Usare Azure Resource Graph per eseguire alcune query di base, tra cui conteggio delle risorse, ordinamento delle risorse o ordinamento con un tag specifico.
ms.date: 02/04/2021
ms.topic: sample
ms.openlocfilehash: f3ff78f52f84ad4fac74fa6e7b04de3e645a2fff
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99557978"
---
# <a name="starter-resource-graph-query-samples"></a>Esempi di query di Resource Graph di base

Il primo passaggio per capire le query con Azure Resource Graph è conoscere i fondamenti del [linguaggio di query](../concepts/query-language.md). Se non si ha familiarità con il [linguaggio di query Kusto (KQL)](/azure/kusto/query/index), è consigliabile vedere l'[esercitazione per KQL](/azure/kusto/query/tutorial) per capire come comporre richieste in grado di individuare le risorse desiderate.

Si esamineranno le query di base seguenti:

- [Contare le risorse di Azure](#count-resources)
- [Contare le risorse dell'insieme di credenziali delle chiavi](#count-keyvaults)
- [Elencare le risorse ordinate per nome](#list-resources)
- [Mostrare tutte le macchine virtuali ordinate per nome in ordine decrescente](#show-vms)
- [Mostrare le prime cinque macchine virtuali per nome e tipo di sistema operativo](#show-sorted)
- [Contare le macchine virtuali per tipo di sistema operativo](#count-os)
- [Mostrare le risorse che contengono archivi](#show-storage)
- [Elencare tutti gli indirizzi IP pubblici](#list-publicip)
- [Contare le risorse che hanno indirizzi IP configurati per sottoscrizione](#count-resources-by-ip)
- [Elencare le risorse con un valore di tag specifico](#list-tag)
- [Elencare tutti gli account di archiviazione con un valore di tag specifico](#list-specific-tag)
- [Elencare tutti i tag e i relativi valori](#list-all-tag-values)
- [Visualizzare i gruppi di sicurezza di rete non associati](#unassociated-nsgs)
- [Ottenere un riepilogo dei risparmi sui costi da Azure Advisor](#advisor-savings)
- [Contare i computer nell'ambito dei criteri di Configurazione guest](#count-gcmachines)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="language-support"></a>Lingue supportate

Azure Resource Graph è supportato dall'interfaccia della riga di comando di Azure tramite un'estensione e da Azure PowerShell tramite un modulo. Prima di eseguire le query seguenti, verificare che l'ambiente sia pronto. Per i passaggi necessari per installare e convalidare l'ambiente shell preferito, vedere le informazioni relative all'[interfaccia della riga di comando di Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e ad [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="count-azure-resources"></a><a name="count-resources"></a>Contare le risorse di Azure

Questa query restituisce il numero di risorse di Azure esistenti nelle sottoscrizioni a cui l'utente ha accesso. È anche una query valida da usare per verificare che nella shell preferita siano installati i componenti di Azure Resource Graph appropriati e funzionino correttamente.

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">Portal.Azure.cn</a>

---

## <a name="count-key-vault-resources"></a><a name="count-keyvaults"></a>Contare le risorse dell'insieme di credenziali delle chiavi

Questa query usa `count` invece di `summarize` per contare il numero di record restituiti. Nel conteggio sono inclusi solo gli insiemi di credenziali delle chiavi.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">Portal.Azure.cn</a>

---

## <a name="list-resources-sorted-by-name"></a><a name="list-resources"></a>Elencare le risorse ordinate per nome

Questa query restituisce qualsiasi tipo di risorsa, ma solo le proprietà **name**, **type** e **location**. Usa `order by` per ordinare le proprietà in base alla proprietà **name** in ordine crescente (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">Portal.Azure.cn</a>

---

## <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"></a>Mostrare tutte le macchine virtuali ordinate per nome in ordine decrescente

Per elencare solo le macchine virtuali (che sono di tipo `Microsoft.Compute/virtualMachines`), è possibile cercare le corrispondenze della proprietà **type** nei risultati. Analogamente alla query precedente, `desc` cambia `order by` in decrescente. Nella ricerca di corrispondenze per tipo, `=~` induce Resource Graph a non distinguere fra maiuscole e minuscole.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">Portal.Azure.cn</a>

---

## <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"></a>Mostrare le prime cinque macchine virtuali per nome e tipo di sistema operativo

Questa query userà `top` per recuperare solo cinque record corrispondenti che vengono ordinati in base al nome. Il tipo della risorsa di Azure è `Microsoft.Compute/virtualMachines`. `project` indica ad Azure Resource Graph quali proprietà includere.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">Portal.Azure.cn</a>

---

## <a name="count-virtual-machines-by-os-type"></a><a name="count-os"></a>Contare le macchine virtuali per tipo di sistema operativo

Continuando dalla query precedente, le risorse sono ancora limitate al tipo `Microsoft.Compute/virtualMachines`, ma non è più presente il limite sul numero di record restituiti.
Invece, sono stati usati `summarize` e `count()` per definire come raggruppare e aggregare i valori in base a una proprietà, che in questo esempio è `properties.storageProfile.osDisk.osType`. Per un esempio dell'aspetto di questa stringa nell'oggetto completo, vedere le informazioni sull'[esplorazione delle risorse e l'individuazione delle macchine virtuali](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">Portal.Azure.cn</a>

---

Un modo diverso per scrivere la stessa query consiste nell'applicare `extend` a una proprietà assegnando un nome temporaneo da usare all'interno della query, in questo caso **os**. **os** viene quindi usato da `summarize` e `count()` come nell'esempio precedente.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">Portal.Azure.cn</a>

---

> [!NOTE]
> Tenere presente che, sebbene `=~` consenta la ricerca di corrispondenze senza distinzione fra maiuscole e minuscole, per usare le proprietà, ad esempio **properties.storageProfile.osDisk.osType**, nella query è necessario che le maiuscole/minuscole siano corrette. Se le maiuscole/minuscole sono errate, viene restituito un valore Null o errato e il raggruppamento o il riepilogo non saranno corretti.

## <a name="show-resources-that-contain-storage"></a><a name="show-storage"></a>Mostrare le risorse che contengono archivi

Invece di definire in modo esplicito il tipo da individuare, questa query di esempio troverà tutte le risorse di Azure che contengono (`contains`) la parola **storage**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">Portal.Azure.cn</a>

---

## <a name="list-all-public-ip-addresses"></a><a name="list-publicip"></a>Elencare tutti gli indirizzi IP pubblici

Analogamente alla query precedente, trovare gli elementi in cui il tipo contenga la parola **publicIPAddresses**.
Questa query si basa su tale modello per includere solo i risultati con **properties.ipAddress**
`isnotempty`, in modo da restituire solo **properties.ipAddress** e a `limit` i risultati migliori
100. Potrebbe essere necessario eseguire l'escape delle virgolette a seconda della shell in uso.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">Portal.Azure.cn</a>

---

## <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"></a>Contare le risorse che hanno indirizzi IP configurati per sottoscrizione

Usando la query dell'esempio precedente e aggiungendo `summarize` e `count()`, è possibile ottenere un elenco per sottoscrizione delle risorse con indirizzi IP configurati.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">Portal.Azure.cn</a>

---

## <a name="list-resources-with-a-specific-tag-value"></a><a name="list-tag"></a>Elencare le risorse con un valore di tag specifico

È possibile limitare i risultati mediante proprietà diverse dal tipo di risorsa di Azure, ad esempio un tag. In questo esempio si applica alle risorse di Azure un filtro basato sul nome di tag **Environment** con il valore **Internal**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">Portal.Azure.cn</a>

---

Per fornire anche i tag della risorsa e i relativi valori, aggiungere la proprietà **tags** per alla parola chiave `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">Portal.Azure.cn</a>

---

## <a name="list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"></a>Elencare tutti gli account di archiviazione con un valore di tag specifico

Combinare la funzionalità di filtro dell'esempio precedente e filtrare il tipo di risorsa di Azure per la proprietà **type**. Questa query limita anche la ricerca di tipi specifici di risorse di Azure con un nome di tag e un valore specifici.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">Portal.Azure.cn</a>

---

> [!NOTE]
> Questo esempio usa `==` per la corrispondenza anziché l'operatore condizionale `=~`. `==` rappresenta una corrispondenza con distinzione fra maiuscole e minuscole.

## <a name="list-all-tags-and-their-values"></a><a name="list-all-tag-values"></a>Elencare tutti i tag e i relativi valori

Questa query elenca i tag relativi a gruppi di gestione, sottoscrizioni e risorse insieme ai relativi valori.
La query limita innanzitutto le risorse in cui i tag `isnotempty()` limitano i campi inclusi, includendo solo i _tag_ in `project` , e `mvexpand` e `extend` per ottenere i dati associati dal contenitore delle proprietà. USA quindi `union` per combinare i risultati di _ResourceContainers_ con gli stessi risultati delle _risorse_, fornendo una copertura ampia per i tag da recuperare. Infine, limita i risultati ai `distinct` dati associati ed esclude i tag nascosti dal sistema.

```kusto
ResourceContainers 
| where isnotempty(tags)
| project tags
| mvexpand tags
| extend tagKey = tostring(bag_keys(tags)[0])
| extend tagValue = tostring(tags[tagKey])
| union (
    resources
    | where isnotempty(tags)
    | project tags
    | mvexpand tags
    | extend tagKey = tostring(bag_keys(tags)[0])
    | extend tagValue = tostring(tags[tagKey])
)
| distinct tagKey, tagValue
| where tagKey !startswith "hidden-"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) | union (resources | where notempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) ) | distinct tagKey, tagValue | where tagKey !startswith "hidden-""
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) | union (resources | where notempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) ) | distinct tagKey, tagValue | where tagKey !startswith "hidden-""
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%0A%7C%20where%20isnotempty%28tags%29%0A%7C%20project%20tags%0A%7C%20mvexpand%20tags%0A%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%7C%20union%20%28%0A%20%20%20%20resources%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0A%20%20%20%20%7C%20project%20tags%0A%20%20%20%20%7C%20mvexpand%20tags%0A%20%20%20%20%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%20%20%20%20%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%29%0A%7C%20distinct%20tagKey%2C%20tagValue%0A%7C%20where%20tagKey%20%21startswith%20%22hidden-%22" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%0A%7C%20where%20isnotempty%28tags%29%0A%7C%20project%20tags%0A%7C%20mvexpand%20tags%0A%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%7C%20union%20%28%0A%20%20%20%20resources%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0A%20%20%20%20%7C%20project%20tags%0A%20%20%20%20%7C%20mvexpand%20tags%0A%20%20%20%20%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%20%20%20%20%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%29%0A%7C%20distinct%20tagKey%2C%20tagValue%0A%7C%20where%20tagKey%20%21startswith%20%22hidden-%22" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%0A%7C%20where%20isnotempty%28tags%29%0A%7C%20project%20tags%0A%7C%20mvexpand%20tags%0A%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%7C%20union%20%28%0A%20%20%20%20resources%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0A%20%20%20%20%7C%20project%20tags%0A%20%20%20%20%7C%20mvexpand%20tags%0A%20%20%20%20%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%20%20%20%20%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%29%0A%7C%20distinct%20tagKey%2C%20tagValue%0A%7C%20where%20tagKey%20%21startswith%20%22hidden-%22" target="_blank">Portal.Azure.cn</a>

---

## <a name="show-unassociated-network-security-groups"></a><a name="unassociated-nsgs"></a>Mostrare i gruppi di sicurezza di rete non associati

Questa query restituisce i gruppi di sicurezza di rete non associati a un'interfaccia di rete o a una subnet.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">Portal.Azure.cn</a>

---

## <a name="get-cost-savings-summary-from-azure-advisor"></a><a name="advisor-savings"></a>Ottenere un riepilogo dei risparmi sui costi da Azure Advisor

Questa query riepiloga i risparmi sui costi di ogni raccomandazione di [Azure Advisor](../../../advisor/advisor-overview.md).

```kusto
advisorresources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'Cost'
| extend
    resources = tostring(properties.resourceMetadata.resourceId),
    savings = todouble(properties.extendedProperties.savingsAmount),
    solution = tostring(properties.shortDescription.solution),
    currency = tostring(properties.extendedProperties.savingsCurrency)
| summarize
    dcount(resources), 
    bin(sum(savings), 0.01)
    by solution, currency
| project solution, dcount_resources, sum_savings, currency
| order by sum_savings desc
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.com</a>
- Portale di Azure per enti pubblici: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">Portal.Azure.US</a>
- Portale di Azure Cina 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">Portal.Azure.cn</a>

---

## <a name="count-machines-in-scope-of-guest-configuration-policies"></a><a name="count-gcmachines"></a>Contare i computer nell'ambito dei criteri di Configurazione guest

Visualizza il numero di macchine virtuali di Azure e di server connessi ad Arc inclusi nell'ambito per le assegnazioni di [Configurazione guest di Criteri di Azure](../../policy/concepts/guest-configuration.md).

```kusto
GuestConfigurationResources
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.'
| project machine = tostring(vmid[(-1)]),
    type = tostring(vmid[(-3)])
| distinct machine, type
| summarize count() by type
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]) | distinct machine, type | summarize count() by type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]) | distinct machine, type | summarize count() by type"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Provare questa query in Azure Resource Graph Explorer:

- Portale di Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%20%7C%20extend%20vmid%20%3D%20split(properties.targetResourceId%2C%22%2F%22)%20%7C%20mvexpand%20properties.latestAssignmentReport.resources%20%7C%20where%20properties_latestAssignmentReport_resources.resourceId%20!%3D%20'Invalid%20assignment%20package.'%20%7C%20project%20machine%20%3D%20tostring(vmid%5B(-1)%5D)%2C%20type%20%3D%20tostring(vmid%5B(-3)%5D)%20%7C%20distinct%20machine%2C%20type%20%7C%20summarize%20count()%20by%20type" target="_blank">portal.azure.com</a>

---

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [linguaggio di query](../concepts/query-language.md).
- Altre informazioni su come [esplorare le risorse](../concepts/explore-resources.md).
- Vedere esempi di [query avanzate](advanced.md).