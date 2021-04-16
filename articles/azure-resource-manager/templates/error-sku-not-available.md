---
title: Errori SKU non disponibili
description: Descrive come risolvere l'errore di SKU non disponibile durante la distribuzione di risorse con Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.openlocfilehash: 3baedf6a5c9f2dbfd3ddf666b458fac649fce2ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503899"
---
# <a name="resolve-errors-for-sku-not-available"></a>Risolvere gli errori dovuti all'indisponibilità di SKU

Questo articolo descrive come risolvere l'errore **SkuNotAvailable**. Se non si riesce a trovare uno SKU appropriato in tale area/zona o in un'area/zona alternativa che soddisfi le esigenze aziendali, inviare una richiesta [di SKU](/troubleshoot/azure/general/region-access-request-process) a supporto di Azure.

## <a name="symptom"></a>Sintomo

Quando si distribuisce una risorsa, in genere una macchina virtuale, vengono visualizzati il codice di errore e il messaggio di errore seguenti:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Questo errore viene visualizzato quando lo SKU della risorsa selezionato, ad esempio le dimensioni della macchina virtuale, non è disponibile per il percorso selezionato.

Se si distribuisce una macchina virtuale spot di Azure o un'istanza del set di scalabilità Spot, in questa posizione non è disponibile alcuna capacità per Azure Spot. Per altre informazioni, vedere [Messaggi di errore spot.](../../virtual-machines/error-codes-spot.md)

## <a name="solution-1---powershell"></a>Soluzione 1: PowerShell

Per determinare quali SKU sono disponibili in un'area/zona, usare il [comando Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku) Filtrare i risultati in base all'area. Per questo comando, è necessaria la versione più recente di PowerShell.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

I risultati includono un elenco di SKU per la località e le eventuali limitazioni per tale SKU. Si noti che lo SKU potrebbe essere elencato come `NotAvailableForSubscription`.

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Per filtrare in base a località e SKU, usare:

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

Il comando restituisce risultati come:

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>Soluzione 2: interfaccia della riga di comando di Azure

Per determinare quali SKU sono disponibili in un'area, usare il [comando az vm list-skus.](/cli/azure/vm#az_vm_list_skus) Usare il parametro `--location` per filtrare l'output in base alla posizione. Usare il parametro `--size` per eseguire la ricerca in base a un nome parziale delle dimensioni. Usare il `--all` parametro per visualizzare tutte le informazioni, incluse le dimensioni non disponibili per la sottoscrizione corrente.

È necessario disporre dell'interfaccia della riga di comando di Azure versione 2.15.0 o successiva. Per controllare la versione, usare `az --version` . Se necessario, [aggiornare l'installazione di](/cli/azure/update-azure-cli).

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

Il comando restituisce risultati come:

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
...
```

## <a name="solution-3---azure-portal"></a>Soluzione 3: portale di Azure

Per determinare quali SKU sono disponibili in un'area, usare il [portale](https://portal.azure.com). Accedere al portale e aggiungere una risorsa tramite l'interfaccia. Quando si impostano i valori, vengono visualizzati gli SKU disponibili per tale risorsa. Non è necessario completare la distribuzione.

Ad esempio, avviare il processo di creazione di una macchina virtuale. Per visualizzare altre dimensioni disponibili, selezionare **Modifica dimensioni**.

![Creare una macchina virtuale](./media/error-sku-not-available/create-vm.png)

È possibile filtrare e scorrere tra le dimensioni disponibili.

![SKU disponibili](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Soluzione 4: REST

Per determinare quali SKU sono disponibili in un'area, usare l'operazione [SKU di risorse - Elenco](/rest/api/compute/resourceskus/list).

Le aree e gli SKU disponibili vengono restituiti nel formato seguente:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```