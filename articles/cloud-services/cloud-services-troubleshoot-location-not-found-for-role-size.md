---
title: Risolvere i problemi di LocationNotFoundForRoleSize durante la distribuzione di un servizio cloud (versione classica) in Azure | Microsoft Docs
description: Questo articolo illustra come risolvere un'eccezione LocationNotFoundForRoleSize quando si distribuisce un servizio cloud (classico) in Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: b11aedb52be3c263c781c2ac68d1d5197ba4def2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745718"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Risolvere i problemi di LocationNotFoundForRoleSize durante la distribuzione di un servizio cloud (versione classica) in Azure

Questo articolo illustra come risolvere gli errori di allocazione in cui le dimensioni di una macchina virtuale (VM) non sono disponibili quando si distribuisce un servizio cloud di Azure (versione classica).

Quando si distribuiscono istanze in un servizio cloud (versione classica) o si aggiungono nuove istanze del ruolo Web o di lavoro, Microsoft Azure alloca le risorse di calcolo.

Occasionalmente è possibile che si verifichino errori durante queste operazioni anche prima di raggiungere il limite della sottoscrizione di Azure.

> [!TIP]
> Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi.

## <a name="symptom"></a>Sintomo

In portale di Azure passare al servizio cloud (versione classica) e nella barra laterale selezionare *log operazioni (versione classica)* per visualizzare i log.

![Image Mostra il pannello del log delle operazioni (classico).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Quando si esaminano i log del servizio cloud (versione classica), verrà visualizzata l'eccezione seguente:

|Tipo di eccezione  |Messaggio di errore  |
|---------|---------|
|LocationNotFoundForRoleSize     |L'operazione ' `{Operation ID}` ' non è riuscita:' il livello VM richiesto non è attualmente disponibile nell'area ( `{Region ID}` ) per questa sottoscrizione. Provare un altro livello o eseguire la distribuzione in un percorso diverso .'.|

## <a name="cause"></a>Causa

Si è verificato un problema di capacità con l'area o il cluster in cui si esegue la distribuzione. L'eccezione *LocationNotFoundForRoleSize* si verifica quando lo SKU di risorsa selezionato (dimensione VM) non è disponibile per l'area specificata.

## <a name="solution"></a>Soluzione

In questo scenario, è necessario selezionare un'area o uno SKU diverso per distribuire il servizio cloud (versione classica) a. Prima di distribuire o aggiornare il servizio cloud (versione classica), è possibile determinare quali SKU sono disponibili in un'area o in una zona di disponibilità. Seguire i processi dell'interfaccia della riga di comando di [Azure](#list-skus-in-region-using-azure-cli), [PowerShell](#list-skus-in-region-using-powershell)o [API REST](#list-skus-in-region-using-rest-api) .

### <a name="list-skus-in-region-using-azure-cli"></a>Elencare SKU nell'area usando l'interfaccia della riga di comando

È possibile usare il comando [AZ VM list-SKUS](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) .

- Usare il `--location` parametro per filtrare l'output in base alla posizione in uso.
- Usare il parametro `--size` per eseguire la ricerca in base a un nome parziale delle dimensioni.
- Per ulteriori informazioni, vedere la Guida [risoluzione dell'errore per SKU non disponibile](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Ad esempio:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Risultati di esempio:** ![ Output dell'interfaccia della riga di comando di Azure dell'esecuzione del comando ' AZ VM list-SKUS--location southcentralus--size Standard_F--output Table ', che Mostra gli SKU disponibili.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Elencare gli SKU nell'area usando PowerShell

È possibile usare il comando [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) .

- Filtrare i risultati in base all'area.
- Per questo comando, è necessaria la versione più recente di PowerShell.
- Per ulteriori informazioni, vedere la Guida [risoluzione dell'errore per SKU non disponibile](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) .

**Ad esempio:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Altri comandi utili:**

Filtrare le posizioni che contengono le dimensioni (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Filtrare tutti i percorsi che contengono le dimensioni (v3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Elencare gli SKU nell'area usando l'API REST

È possibile usare l'operazione [SKU di risorsa-elenco](https://docs.microsoft.com/rest/api/compute/resourceskus/list) . Le aree e gli SKU disponibili vengono restituiti nel formato seguente:

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

Per ulteriori soluzioni di errore di allocazione e per comprendere meglio il modo in cui vengono generate:

> [!div class="nextstepaction"]
> [Errori di allocazione-servizio cloud (versione classica)](cloud-services-allocation-failures.md)

Se il problema di Azure non viene risolto in questo articolo, visitare i forum di Azure su [MSDN e stack overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in [@AzureSupport su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure. Per inviare una richiesta di supporto tecnico, nella pagina [Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) selezionare *Supporto*.
