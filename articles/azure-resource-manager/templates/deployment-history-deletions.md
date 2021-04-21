---
title: Eliminazioni della cronologia di distribuzione
description: Descrive come Azure Resource Manager automaticamente le distribuzioni dalla cronologia di distribuzione. Le distribuzioni vengono eliminate quando la cronologia sta per superare il limite di 800.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: b55c022c35c43be6818bb3c551d5db85b1927ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781848"
---
# <a name="automatic-deletions-from-deployment-history"></a>Eliminazioni automatiche dalla cronologia di distribuzione

Ogni volta che si distribuisce un modello, le informazioni sulla distribuzione vengono scritte nella cronologia di distribuzione. Ogni gruppo di risorse è limitato a 800 distribuzioni nella cronologia di distribuzione.

Azure Resource Manager elimina automaticamente le distribuzioni dalla cronologia non appena si avvicina il limite. L'eliminazione automatica è una modifica rispetto al comportamento passato. In precedenza era necessario eliminare manualmente le distribuzioni dalla cronologia di distribuzione per evitare di ricevere un errore. Questa modifica è stata implementata il 6 agosto 2020.

**Le eliminazioni automatiche sono supportate per le distribuzioni di gruppi di risorse. Attualmente, le distribuzioni nella cronologia per le [distribuzioni di](deploy-to-subscription.md)sottoscrizione, [gruppo](deploy-to-management-group.md)di gestione e [tenant](deploy-to-tenant.md) non vengono eliminate automaticamente.**

> [!NOTE]
> L'eliminazione di una distribuzione dalla cronologia non influisce sulle risorse distribuite.

## <a name="when-deployments-are-deleted"></a>Quando vengono eliminate le distribuzioni

Le distribuzioni vengono eliminate dalla cronologia quando si superano 775 distribuzioni. Azure Resource Manager elimina le distribuzioni fino a quando la cronologia non scende a 750. Le distribuzioni meno recenti vengono sempre eliminate per prime.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Eliminazioni dalla cronologia di distribuzione":::

> [!NOTE]
> Il numero iniziale (775) e il numero finale (750) sono soggetti a modifiche.
>
> Se il gruppo di risorse è già al limite di 800, la distribuzione successiva ha esito negativo e viene visualizzato un errore. Il processo di eliminazione automatica viene avviato immediatamente. È possibile provare di nuovo la distribuzione dopo una breve attesa.

Oltre alle distribuzioni, le eliminazioni vengono attivate anche quando si esegue l'operazione di [what-if](template-deploy-what-if.md) o si convalida una distribuzione.

Quando si assegna a una distribuzione lo stesso nome di una distribuzione nella cronologia, viene reimpostata la relativa posizione nella cronologia. La distribuzione passa alla posizione più recente nella cronologia. È anche possibile reimpostare la posizione di una distribuzione quando si [esegue il rollback a tale distribuzione](rollback-on-error.md) dopo un errore.

## <a name="remove-locks-that-block-deletions"></a>Rimuovere blocchi che bloccano le eliminazioni

Se si dispone di [un blocco CanNotDelete](../management/lock-resources.md) per un gruppo di risorse, le distribuzioni per tale gruppo di risorse non possono essere eliminate. È necessario rimuovere il blocco per sfruttare le eliminazioni automatiche nella cronologia di distribuzione.

Per usare PowerShell per eliminare un blocco, eseguire i comandi seguenti:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Per usare l'interfaccia della riga di comando di Azure per eliminare un blocco, eseguire i comandi seguenti:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>Autorizzazioni necessarie

Le eliminazioni vengono richieste con l'identità dell'utente che ha distribuito il modello. Per eliminare le distribuzioni, l'utente deve avere accesso **all'azione Microsoft.Resources/deployments/delete.** Se l'utente non dispone delle autorizzazioni necessarie, le distribuzioni non vengono eliminate dalla cronologia.

Se l'utente corrente non dispone delle autorizzazioni necessarie, l'eliminazione automatica viene tentata di nuovo durante la distribuzione successiva.

## <a name="opt-out-of-automatic-deletions"></a>Rifiutare esplicitamente le eliminazioni automatiche

È possibile rifiutare esplicitamente le eliminazioni automatiche dalla cronologia. **Usare questa opzione solo quando si vuole gestire manualmente la cronologia di distribuzione.** Il limite di 800 distribuzioni nella cronologia è ancora applicato. Se si superano 800 distribuzioni, si riceverà un errore e la distribuzione avrà esito negativo.

Per disabilitare le eliminazioni automatiche, registrare il `Microsoft.Resources/DisableDeploymentGrooming` flag di funzionalità. Quando si registra il flag di funzionalità, si rifiutano esplicitamente le eliminazioni automatiche per l'intera sottoscrizione di Azure. Non è possibile rifiutare esplicitamente solo un gruppo di risorse specifico. Per riabilita le eliminazioni automatiche, annullare la registrazione del flag di funzionalità.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per PowerShell, usare [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Per visualizzare lo stato corrente della sottoscrizione, usare:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Per riabilitare le eliminazioni automatiche, usare l'API REST di Azure o l'interfaccia della riga di comando di Azure.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per l'interfaccia della riga di comando di Azure, [usare az feature register](/cli/azure/feature#az_feature_register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Per visualizzare lo stato corrente della sottoscrizione, usare:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Per riabilita le eliminazioni automatiche, usare [az feature unregister](/cli/azure/feature#az_feature_unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Per l'API REST, [usare Funzionalità - Registrare](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Per visualizzare lo stato corrente della sottoscrizione, usare:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Per riabilita le eliminazioni automatiche, usare [Funzionalità - Annulla registrazione](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla visualizzazione della cronologia di distribuzione, vedere [Visualizzare la cronologia delle distribuzioni con Azure Resource Manager](deployment-history.md).
