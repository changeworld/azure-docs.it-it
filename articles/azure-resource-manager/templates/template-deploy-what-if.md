---
title: Distribuzione modelli di what-if
description: Determinare le modifiche che verranno apportate alle risorse prima di distribuire un Azure Resource Manager predefinito.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tomfitz
ms.openlocfilehash: 7e300f896bb11ed7c77738836f894cff41cc8bf3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781830"
---
# <a name="arm-template-deployment-what-if-operation"></a>Operazione di simulazione della distribuzione del modello di Resource Manager

Prima di distribuire un modello Azure Resource Manager (modello arm), è possibile visualizzare in anteprima le modifiche che verranno apportate. Azure Resource Manager fornisce l'operazione di what-if per vedere come cambieranno le risorse se si distribuisce il modello. L'operazione di simulazione non modifica in alcun modo le risorse esistenti. Prevede invece le modifiche che verranno applicate se il modello specificato viene distribuito.

È possibile usare l'operazione di what-if con Azure PowerShell, l'interfaccia della riga di comando di Azure o le operazioni api REST. L'what-if è supportato per le distribuzioni a livello di gruppo di risorse, sottoscrizione, gruppo di gestione e tenant.

## <a name="install-azure-powershell-module"></a>Installare il modulo Azure PowerShell

Per usare il what-if in PowerShell, è necessario avere la **versione 4.2 o** successiva del modulo Az .

Per installare il modulo, usare:

```powershell
Install-Module -Name Az -Force
```

Per altre informazioni sull'installazione dei moduli, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="install-azure-cli-module"></a>Installare il modulo dell'interfaccia della riga di comando di Azure

Per usare il what-if nell'interfaccia della riga di comando di Azure, è necessario avere l'interfaccia della riga di comando di Azure 2.14.0 o versione successiva. Se necessario, [installare la versione più recente dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="see-results"></a>See results (Visualizza risultati)

Quando si usa what-if in PowerShell o nell'interfaccia della riga di comando di Azure, l'output include risultati codificati a colori che consentono di visualizzare i diversi tipi di modifiche.

![Resource Manager'operazione di what-if di distribuzione del modello fullresourcepayload e modifica dei tipi](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

L'output di testo è:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> L'operazione di what-if non può risolvere la [funzione di riferimento](template-functions-resource.md#reference). Ogni volta che si imposta una proprietà su un'espressione modello che include la funzione di riferimento, i report di what-if che la proprietà cambieranno. Questo comportamento si verifica perché what-if confronta il valore corrente della proprietà (ad esempio o per un valore booleano) con l'espressione modello `true` `false` non risolta. Ovviamente, questi valori non corrispondono. Quando si distribuisce il modello, la proprietà cambierà solo quando l'espressione del modello viene risolta in un valore diverso.

## <a name="what-if-commands"></a>Comandi di what-if

### <a name="azure-powershell"></a>Azure PowerShell

Per visualizzare in anteprima le modifiche prima di distribuire un modello, usare [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) [o New-AzSubscriptionDeployment.](/powershell/module/az.resources/new-azdeployment) Aggiungere il `-Whatif` parametro switch al comando di distribuzione.

* `New-AzResourceGroupDeployment -Whatif` per le distribuzioni di gruppi di risorse
* `New-AzSubscriptionDeployment -Whatif` e `New-AzDeployment -Whatif` per le distribuzioni a livello di sottoscrizione

È possibile usare il `-Confirm` parametro switch per visualizzare in anteprima le modifiche e ricevere la richiesta di continuare con la distribuzione.

* `New-AzResourceGroupDeployment -Confirm` per le distribuzioni di gruppi di risorse
* `New-AzSubscriptionDeployment -Confirm` e `New-AzDeployment -Confirm` per le distribuzioni a livello di sottoscrizione

I comandi precedenti restituiscono un riepilogo di testo che è possibile esaminare manualmente. Per ottenere un oggetto che è possibile controllare a livello di codice per le modifiche, usare [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) o [Get-AzSubscriptionDeploymentWhatIfResult.](/powershell/module/az.resources/get-azdeploymentwhatifresult)

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` per le distribuzioni di gruppi di risorse
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` o `$results = Get-AzDeploymentWhatIfResult` per le distribuzioni a livello di sottoscrizione

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per visualizzare in anteprima le modifiche prima di distribuire un modello, usare:

* [az deployment group what-if for](/cli/azure/deployment/group#az_deployment_group_what_if) resource group deployments
* [az deployment sub what-if per](/cli/azure/deployment/sub#az_deployment_sub_what_if) le distribuzioni a livello di sottoscrizione
* [az deployment mg what-if for](/cli/azure/deployment/mg#az_deployment_mg_what_if) management group deployments
* [az deployment tenant what-if for](/cli/azure/deployment/tenant#az_deployment_tenant_what_if) tenant deployments

È possibile usare l'opzione (o la relativa forma breve) per visualizzare in anteprima le modifiche e ricevere la richiesta `--confirm-with-what-if` di continuare con la `-c` distribuzione. Aggiungere questa opzione a:

* [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)
* [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create).
* [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)

Ad esempio, usare `az deployment group create --confirm-with-what-if` o `-c` per le distribuzioni di gruppi di risorse.

I comandi precedenti restituiscono un riepilogo di testo che è possibile esaminare manualmente. Per ottenere un oggetto JSON che è possibile controllare a livello di codice per le modifiche, usare `--no-pretty-print` l'opzione . Ad esempio, usare `az deployment group what-if --no-pretty-print` per le distribuzioni di gruppi di risorse.

Per restituire i risultati senza colori, aprire il file di configurazione dell'interfaccia della riga di comando [di Azure.](/cli/azure/azure-cli-configuration) Impostare **no_color** su **sì.**

### <a name="azure-rest-api"></a>API REST di Azure

Per l'API REST, usare:

* [Distribuzioni: What If](/rest/api/resources/deployments/whatif) per le distribuzioni di gruppi di risorse
* [Distribuzioni : What If nell'ambito della sottoscrizione per](/rest/api/resources/deployments/whatifatsubscriptionscope) le distribuzioni di sottoscrizioni
* [Distribuzioni - What If ambito del](/rest/api/resources/deployments/whatifatmanagementgroupscope) gruppo di gestione per le distribuzioni dei gruppi di gestione
* [Distribuzioni: What If nell'ambito tenant per](/rest/api/resources/deployments/whatifattenantscope) le distribuzioni tenant.

## <a name="change-types"></a>Tipi di modifiche

L'operazione di what-if elenca sei diversi tipi di modifiche:

- **Crea:** la risorsa non esiste attualmente, ma è definita nel modello. La risorsa verrà creata.

- **Elimina:** questo tipo di modifica si applica solo quando si usa [la modalità completa per](deployment-modes.md) la distribuzione. La risorsa esiste, ma non è definita nel modello. Con la modalità completa, la risorsa verrà eliminata. In questo tipo di [modifica sono](complete-mode-deletion.md) incluse solo le risorse che supportano l'eliminazione in modalità completa.

- **Ignora:** la risorsa esiste, ma non è definita nel modello. La risorsa non verrà distribuita o modificata.

- **NoChange:** la risorsa esiste ed è definita nel modello. La risorsa verrà ridistribuita, ma le sue proprietà non verranno modificate. Questo tipo di modifica viene [restituito quando ResultFormat](#result-format) è impostato su `FullResourcePayloads` , che è il valore predefinito.

- **Modify:** la risorsa esiste ed è definita nel modello. La risorsa verrà ridistribuita e le sue proprietà verranno modificate. Questo tipo di modifica viene [restituito quando ResultFormat](#result-format) è impostato su `FullResourcePayloads` , che è il valore predefinito.

- **Distribuisci:** la risorsa esiste ed è definita nel modello. La risorsa verrà ridistribuita. Le proprietà della risorsa possono essere modificate o meno. L'operazione restituisce questo tipo di modifica quando non ha informazioni sufficienti per determinare se una o più proprietà verranno modificate. Questa condizione viene visualizzata solo quando [ResultFormat](#result-format) è impostato su `ResourceIdOnly` .

## <a name="result-format"></a>Formato del risultato

È possibile controllare il livello di dettaglio restituito sulle modifiche previste. Sono disponibili due opzioni:

* **FullResourcePayloads:** restituisce un elenco di risorse che verranno modificate e informazioni dettagliate sulle proprietà che verranno modificate
* **ResourceIdOnly:** restituisce un elenco di risorse che verranno modificate

Il valore predefinito è **FullResourcePayloads.**

Per i comandi di distribuzione di PowerShell, usare il `-WhatIfResultFormat` parametro . Nei comandi dell'oggetto a livello di codice usare il `ResultFormat` parametro .

Per l'interfaccia della riga di comando di Azure, usare il parametro `--result-format`.
 
I risultati seguenti mostrano i due diversi formati di output:

- Payload di risorse completi

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Solo ID risorsa

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Eseguire un'operazione di what-if

### <a name="set-up-environment"></a>Set up environment (Configurare l'ambiente)

Per vedere come funziona l'analisi di what-if, è possibile eseguire alcuni test. Distribuire prima di tutto un [modello che crea una rete virtuale.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) Questa rete virtuale verrà utilizzata per testare il modo in cui le modifiche vengono segnalate da what-if.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Modifica dei test

Al termine della distribuzione, si è pronti per testare l'operazione di what-if. Questa volta si distribuisce un [modello che modifica la rete virtuale](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Manca uno dei tag originali, una subnet è stata rimossa e il prefisso dell'indirizzo è stato modificato.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

L'output della simulazione è simile a:

![Resource Manager output dell'operazione di what-if per la distribuzione di modelli](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

L'output di testo è:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Si noti nella parte superiore dell'output che i colori sono definiti per indicare il tipo di modifiche.

Nella parte inferiore dell'output viene visualizzato il tag Proprietario eliminato. Il prefisso dell'indirizzo è stato modificato da 10.0.0.0/16 a 10.0.0.0/15. La subnet denominata subnet001 è stata eliminata. Tenere presente che queste modifiche non sono state distribuite. Verrà visualizzata un'anteprima delle modifiche che verranno apportate se si distribuisce il modello.

Alcune delle proprietà elencate come eliminate non verranno effettivamente modificate. Le proprietà possono essere erroneamente segnalate come eliminate quando non sono nel modello, ma vengono impostate automaticamente durante la distribuzione come valori predefiniti. Questo risultato viene considerato "rumore" nella risposta di what-if. La risorsa distribuita finale avrà i valori impostati per le proprietà. Con la maturazione dell'operazione di what-if, queste proprietà verranno filtrate in base al risultato.

## <a name="programmatically-evaluate-what-if-results"></a>Valutare a livello di codice i risultati di valutazione

A questo punto, è possibile valutare a livello di codice i risultati di what-if impostando il comando su una variabile.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

È possibile visualizzare un riepilogo di ogni modifica.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Confermare l'eliminazione

L'operazione di what-if supporta l'uso della [modalità di distribuzione](deployment-modes.md). Quando è impostata sulla modalità di completamento, le risorse non presenti nel modello vengono eliminate. Nell'esempio seguente viene distribuito [un modello senza risorse definite](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) in modalità completa.

Per visualizzare in anteprima le modifiche prima di distribuire un modello, usare il parametro confirm switch con il comando di distribuzione. Se le modifiche sono come previsto, rispondere che si vuole completare la distribuzione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Poiché nel modello non sono definite risorse e la modalità di distribuzione è impostata per il completamento, la rete virtuale verrà eliminata.

![Resource Manager modalità di distribuzione dell'output dell'operazione di what-if della distribuzione del modello completata](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

L'output di testo è:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001&quot;
      location:        &quot;centralus&quot;
      name:            &quot;vnet-001&quot;
      tags.CostCenter: &quot;12345&quot;
      tags.Owner:      &quot;Team A&quot;
      type:            &quot;Microsoft.Network/virtualNetworks&quot;

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;Y"):
```

Vengono visualizzati le modifiche previste ed è possibile confermare che si vuole eseguire la distribuzione.

## <a name="sdks"></a>SDK

È possibile usare l'operazione di what-if tramite azure SDK.

* Per Python, usare [what-if](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-).

* Per Java, usare [la classe DeploymentWhatIf.](/java/api/com.microsoft.azure.management.resources.deploymentwhatif)

* Per .NET, usare [la classe DeploymentWhatIf.](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif)

## <a name="next-steps"></a>Passaggi successivi

- Per usare l'operazione di what-if in una pipeline, vedere Testare i [modelli arm con What-If in una pipeline.](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)
- Se si notano risultati non corretti dall'operazione di analisi di what if, segnalare i problemi all'indirizzo [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Per un Microsoft Learn che illustra l'uso di what if, vedere Visualizzare in anteprima le modifiche e convalidare le risorse di Azure usando [what-if](/learn/modules/arm-template-test/)e il toolkit di test del modello arm.
