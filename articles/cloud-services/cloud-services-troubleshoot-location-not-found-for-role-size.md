---
title: Risolvere i problemi relativi a LocationNotFoundForRoleSize durante la distribuzione di un servizio cloud (versione classica) in Azure | Microsoft Docs
description: Questo articolo illustra come risolvere un'eccezione LocationNotFoundForRoleSize quando si distribuisce un servizio cloud (versione classica) in Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 54af2387ec0ff6c8f86f96821baad17736e8d85b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877967"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Risolvere i problemi relativi a LocationNotFoundForRoleSize durante la distribuzione di un servizio cloud (versione classica) in Azure

In questo articolo si risolveranno gli errori di allocazione in cui le dimensioni di una macchina virtuale non sono disponibili quando si distribuisce un servizio cloud di Azure (versione classica).

Quando si distribuiscono istanze in un servizio cloud (versione classica) o si aggiungono nuove istanze del ruolo Web o di lavoro, Microsoft Azure alloca le risorse di calcolo.

In alcuni casi possono verificarsi errori durante queste operazioni, anche prima di raggiungere il limite della sottoscrizione di Azure.

> [!TIP]
> Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi.

## <a name="symptom"></a>Sintomo

Nella portale di Azure passare al servizio cloud (versione classica) e nella barra laterale selezionare Log *operazioni (versione classica)* per visualizzare i log.

![L'immagine mostra il pannello Log operazioni (versione classica).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Quando si esaminano i log del servizio cloud (versione classica), viene visualizzata l'eccezione seguente:

|Tipo di eccezione  |Messaggio di errore  |
|---------|---------|
|LocationNotFoundForRoleSize     |L'operazione ' ' non è riuscita: 'Il livello macchina virtuale richiesto `{Operation ID}` non è attualmente disponibile nell'area ( ) per questa `{Region ID}` sottoscrizione. Provare un altro livello o eseguire la distribuzione in un percorso diverso."|

## <a name="cause"></a>Causa

Si è verificato un problema di capacità con l'area o il cluster in cui si esegue la distribuzione. *L'eccezione LocationNotFoundForRoleSize* si verifica quando lo SKU della risorsa selezionato (dimensioni vm) non è disponibile per l'area specificata.

## <a name="solution"></a>Soluzione

In questo scenario è necessario selezionare un'area o uno SKU diverso in cui distribuire il servizio cloud (versione classica). Prima di distribuire o aggiornare il servizio cloud (versione classica), è possibile determinare quali SKU sono disponibili in un'area o in una zona di disponibilità. Seguire i [processi seguenti dell'interfaccia](#list-skus-in-region-using-azure-cli)della riga di comando di Azure, di [PowerShell](#list-skus-in-region-using-powershell)o [dell'API REST.](#list-skus-in-region-using-rest-api)

### <a name="list-skus-in-region-using-azure-cli"></a>Elencare gli SKU nell'area usando l'interfaccia della riga di comando di Azure

È possibile usare [az vm list-skus](/cli/azure/vm?view=azure-cli-latest
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus) .

- Usare il `--location` parametro per filtrare l'output in base alla posizione in uso.
- Usare il parametro `--size` per eseguire la ricerca in base a un nome parziale delle dimensioni.
- Per altre informazioni, vedere la [guida Risolvere l'errore per SKU non](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) disponibile.

    **Ad esempio:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Risultati di esempio:** ![ Output dell'interfaccia della riga di comando di Azure dell'esecuzione del comando 'az vm list-skus --location southcentralus --size Standard_F --output table', che mostra gli SKU disponibili.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Elencare gli SKU nell'area usando PowerShell

È possibile usare [il comando Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku)

- Filtrare i risultati in base all'area.
- Per questo comando, è necessaria la versione più recente di PowerShell.
- Per altre informazioni, vedere la [guida Risolvere l'errore per lo SKU non](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) disponibile.

**Ad esempio:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Altri comandi utili:**

Filtrare le posizioni che contengono dimensioni (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Filtrare tutte le posizioni che contengono dimensioni (V3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Elencare gli SKU nell'area usando l'API REST

È possibile usare [l'operazione Sku risorse -](/rest/api/compute/resourceskus/list) Elenco. Le aree e gli SKU disponibili vengono restituiti nel formato seguente:

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
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Passaggi successivi

Per altre soluzioni di errore di allocazione e per comprendere meglio come vengono generati:

> [!div class="nextstepaction"]
> [Errori di allocazione - Servizio cloud (versione classica)](cloud-services-allocation-failures.md)

Se il problema di Azure non viene risolto in questo articolo, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in [@AzureSupport su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure. Per inviare una richiesta di supporto tecnico, nella pagina [Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) selezionare *Supporto*.
