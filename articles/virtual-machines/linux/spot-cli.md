---
title: Usare l'interfaccia della riga di comando per distribuire macchine virtuali Azure spot
description: Informazioni su come usare l'interfaccia della riga di comando per distribuire macchine virtuali Azure spot per ridurre i costi.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0a7be682f921efdfae486e8f6545758964a941ae
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098860"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Distribuire macchine virtuali Azure spot usando l'interfaccia della riga di comando di Azure

Con le [macchine virtuali di Azure spot](../spot-vms.md) è possibile sfruttare i vantaggi della capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le macchine virtuali di Azure spot. Le macchine virtuali di Azure spot sono quindi ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali Azure spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere i prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

È possibile impostare un prezzo massimo che si è disposti a pagare, per ora, per la macchina virtuale. Il prezzo massimo per una macchina virtuale Azure spot può essere impostato in dollari USA (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il prezzo massimo su `-1`, la macchina virtuale non verrà eliminata in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per la macchina virtuale di Azure spot o al prezzo di una VM standard, che sempre è inferiore, purché siano disponibili capacità e quota. Per altre informazioni sull'impostazione del prezzo massimo, vedere [macchine virtuali di Azure spot-prezzi](../spot-vms.md#pricing).

Il processo di creazione di una macchina virtuale di Azure con l'interfaccia della riga di comando di Azure è identico a quello descritto nell' [articolo introduttivo](./quick-create-cli.md). È sufficiente aggiungere il parametro '--Priority spot ', impostare `--eviction-policy` su deallocate (impostazione predefinita) o `Delete` e specificare un prezzo massimo o `-1` . 


## <a name="install-azure-cli"></a>Installare l'interfaccia da riga di comando di Azure

Per creare macchine virtuali Azure spot, è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.74 o successiva. Eseguire **az --version** per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

Accedere ad Azure tramite [az login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Creare una macchina virtuale Azure spot

Questo esempio illustra come distribuire una macchina virtuale Linux Azure spot che non verrà rimossa in base al prezzo. I criteri di rimozione sono impostati per deallocare la macchina virtuale, in modo che possa essere riavviata in un secondo momento. Se si vuole eliminare la macchina virtuale e il disco sottostante quando la macchina virtuale viene rimossa, impostare `--eviction-policy` su `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Dopo aver creato la macchina virtuale, è possibile eseguire una query per visualizzare il prezzo di fatturazione massimo per tutte le macchine virtuali nel gruppo di risorse.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simulare un'eliminazione

È possibile [simulare un'eliminazione](/rest/api/compute/virtualmachines/simulateeviction) di una macchina virtuale di Azure spot, per testare il modo in cui l'applicazione effettuerà il ristagno a una rimozione improvvisa. 

Sostituire quanto segue con le informazioni: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```
`Response Code: 204` indica che l'eliminazione simulata è stata completata. 

**Passaggi successivi**

È anche possibile creare una macchina virtuale Azure spot usando [Azure PowerShell](../windows/spot-powershell.md), il [portale](../spot-portal.md)o un [modello](spot-template.md).

Eseguire query sulle informazioni sui prezzi correnti usando l' [API prezzi al dettaglio di Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) per informazioni sulla macchina virtuale Azure spot. `meterName`E `skuName` conterranno entrambi `Spot` .

Se si verifica un errore, vedere [codici di errore](../error-codes-spot.md).
