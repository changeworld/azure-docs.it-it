---
title: Usare PowerShell per distribuire macchine virtuali Azure spot
description: Informazioni su come usare Azure PowerShell per distribuire macchine virtuali Azure spot per risparmiare sui costi.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802744"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Distribuire macchine virtuali Azure spot usando Azure PowerShell


Con le [macchine virtuali di Azure spot](../spot-vms.md) è possibile sfruttare i vantaggi della capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le macchine virtuali di Azure spot. Le macchine virtuali di Azure spot sono quindi ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali Azure spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere i prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Per altre informazioni sull'impostazione del prezzo massimo, vedere [macchine virtuali di Azure spot-prezzi](../spot-vms.md#pricing).

È possibile impostare un prezzo massimo che si è disposti a pagare, per ora, per la macchina virtuale. Il prezzo massimo per una macchina virtuale Azure spot può essere impostato in dollari USA (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il prezzo massimo su `-1`, la macchina virtuale non verrà eliminata in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per spot o al prezzo di una macchina virtuale standard, a seconda di quale sia il più basso, purché siano disponibili capacità e quota.


## <a name="create-the-vm"></a>Creare la macchina virtuale

Creare una spotVM usando [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) per creare la configurazione. Includere `-Priority Spot` e impostare `-MaxPrice` su:
- `-1` quindi, la macchina virtuale non viene rimossa in base al prezzo.
- importo in dollari, fino a 5 cifre. Ad esempio, `-MaxPrice .98765` indica che la macchina virtuale verrà deallocata quando il prezzo per un spotVM va a circa $98765 all'ora.


Questo esempio crea un spotVM che non verrà deallocato in base ai prezzi (solo quando Azure necessita della capacità). I criteri di rimozione sono impostati per deallocare la macchina virtuale, in modo che possa essere riavviata in un secondo momento. Se si vuole eliminare la macchina virtuale e il disco sottostante quando la macchina virtuale viene rimossa, impostare `-EvictionPolicy` su `Delete` in `New-AzVMConfig` .


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Dopo aver creato la macchina virtuale, è possibile eseguire una query per visualizzare il prezzo massimo per tutte le macchine virtuali nel gruppo di risorse.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Simulare un'eliminazione

È possibile simulare un'eliminazione di una macchina virtuale di Azure spot usando REST, PowerShell o l'interfaccia della riga di comando per verificare il grado di risposta dell'applicazione a una rimozione improvvisa.

Nella maggior parte dei casi, è consigliabile usare le macchine virtuali dell'API REST, [simulando la rimozione](/rest/api/compute/virtualmachines/simulateeviction) , per consentire il test automatizzato delle applicazioni. Per REST, un `Response Code: 204` indica che l'eliminazione simulata ha avuto esito positivo. È possibile combinare eliminazioni simulate con il [servizio eventi pianificato](scheduled-events.md), per automatizzare il modo in cui l'app risponderà quando la macchina virtuale verrà eliminata.

Per visualizzare gli eventi pianificati in azione, Guarda [Azure Friday-uso di azure eventi pianificati per preparare la manutenzione della macchina virtuale](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Test rapido

Per un rapido test per illustrare il funzionamento di un'operazione di rimozione simulata, è possibile esaminare il servizio eventi pianificati per verificarne l'aspetto quando si simula una rimozione usando PowerShell.

Il servizio eventi pianificato è abilitato per il servizio la prima volta che si effettua una richiesta per gli eventi. 

Accedere in remoto alla macchina virtuale e quindi aprire un prompt dei comandi. 

Al prompt dei comandi nella macchina virtuale, digitare:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Questa prima risposta potrebbe richiedere fino a 2 minuti. Da questo momento in poi, dovrebbero visualizzare l'output quasi immediatamente.

Da un computer in cui è installato il modulo AZ PowerShell (come il computer locale), simulare un'eliminazione usando [set-AzVM](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). Sostituire il nome del gruppo di risorse e il nome della macchina virtuale con quelli personalizzati. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

L'output della risposta sarà `Status: Succeeded` se la richiesta è stata eseguita correttamente.

Tornare rapidamente alla macchina virtuale spot ed eseguire di nuovo la query sull'endpoint Eventi pianificati. Ripetere il comando seguente fino a ottenere un output che contiene altre informazioni:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Quando il servizio eventi pianificato riceve la notifica di rimozione, verrà visualizzata una risposta simile alla seguente:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Come si può notare `"EventType":"Preempt"` , la risorsa è la risorsa della macchina virtuale `"Resources":["myspotvm"]` . 

È anche possibile vedere quando la macchina virtuale verrà rimossa controllando il `"NotBefore"` valore. La macchina virtuale non verrà eliminata prima del periodo di tempo specificato in `NotBefore` , quindi la finestra dell'applicazione si chiuderà normalmente.


## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare una macchina virtuale Azure spot usando l' [interfaccia](../linux/spot-cli.md)della riga di comando di Azure, il [portale](../spot-portal.md) o un [modello](../linux/spot-template.md).

Eseguire query sulle informazioni sui prezzi correnti usando l' [API prezzi al dettaglio di Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) per informazioni sui prezzi delle macchine virtuali di Azure spot. `meterName`E `skuName` conterranno entrambi `Spot` .

Se si verifica un errore, vedere [codici di errore](../error-codes-spot.md).
