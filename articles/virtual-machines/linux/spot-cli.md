---
title: Usare l'interfaccia della riga di comando per distribuire macchine virtuali spot di Azure
description: Informazioni su come usare l'interfaccia della riga di comando per distribuire macchine virtuali spot di Azure per ridurre i costi.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8e8bdaa7a812d8c7accfea59b58b75a58d50e21e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789610"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Distribuire macchine virtuali spot di Azure con l'interfaccia della riga di comando di Azure

[L'uso di Macchine virtuali spot](../spot-vms.md) di Azure consente di sfruttare la capacità inutilizzata a un notevole risparmio sui costi. In qualsiasi momento in cui Azure necessita della capacità, l'infrastruttura di Azure eviterà macchine virtuali spot di Azure. Le macchine virtuali spot di Azure sono quindi un'ottima opzione per carichi di lavoro in grado di gestire interruzioni come processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali spot di Azure sono variabili, in base all'area e alla SKU. Per altre informazioni, vedere i prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

È possibile impostare un prezzo massimo che si è disposti a pagare, all'ora, per la macchina virtuale. Il prezzo massimo per una macchina virtuale spot di Azure può essere impostato in dollari STATUNITENSI (USD), usando fino a 5 cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il prezzo massimo su `-1`, la macchina virtuale non verrà eliminata in base al prezzo. Il prezzo per la macchina virtuale sarà il prezzo corrente per la macchina virtuale spot di Azure o il prezzo per una macchina virtuale standard, che è sempre inferiore, purché siano disponibili capacità e quota. Per altre informazioni sull'impostazione del prezzo massimo, vedere [Macchine virtuali spot di Azure - Prezzi.](../spot-vms.md#pricing)

Il processo per creare una macchina virtuale spot di Azure con l'interfaccia della riga di comando di Azure è identico a quello descritto [nell'articolo di avvio rapido](./quick-create-cli.md). È sufficiente aggiungere il parametro '--priority Spot', impostare su `--eviction-policy` Deallocate (impostazione predefinita) o `Delete` e specificare un prezzo massimo o `-1` . 


## <a name="install-azure-cli"></a>Installare l'interfaccia da riga di comando di Azure

Per creare macchine virtuali spot di Azure, è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.74 o successiva. Eseguire **az --version** per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

Accedere ad Azure tramite [az login](/cli/azure/reference-index#az_login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Creare una macchina virtuale spot di Azure

Questo esempio illustra come distribuire una macchina virtuale Linux spot di Azure che non verrà esata in base al prezzo. I criteri di evizione sono impostati per deallocare la macchina virtuale, in modo che possa essere riavviata in un secondo momento. Se si vuole eliminare la macchina virtuale e il disco sottostante quando la macchina virtuale viene cancellata, impostare `--eviction-policy` su `Delete` .

```azurecli-interactive
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

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simulare un'operazione di evizione

È possibile simulare un'operazione di sfratto di una macchina virtuale spot di Azure usando REST, PowerShell o l'interfaccia della riga di comando per verificare il livello di risposta dell'applicazione a un'improvviso sfratto.

Nella maggior parte dei casi, è necessario usare l'API REST [Virtual Machines - Simulate Eviction](/rest/api/compute/virtualmachines/simulateeviction) per facilitare il test automatizzato delle applicazioni. Per REST, a `Response Code: 204` indica che l'operazione di sfratto simulato è riuscita. È possibile combinare gli sfratti simulati con il servizio eventi [pianificati](scheduled-events.md)per automatizzare la risposta dell'app quando la macchina virtuale viene sfrattata.

Per visualizzare gli eventi pianificati in azione, vedere [Azure Friday - Using Azure Eventi pianificati to prepare for VM maintenance (Azure Friday - Uso di Azure Eventi pianificati per preparare la manutenzione delle macchine virtuali).](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)


### <a name="quick-test"></a>Test rapido

Per un rapido test per illustrare il funzionamento di un'eliminazione simulata, si esaminerà l'esecuzione di query sul servizio eventi pianificato per vedere come appare quando si simula un'eliminazione tramite l'interfaccia della riga di comando di Azure.

Il servizio Eventi pianificati è abilitato per il servizio la prima volta che si effettua una richiesta di eventi. 

Eseguire l'accesso remoto nella macchina virtuale e quindi aprire un prompt dei comandi. 

Dal prompt dei comandi nella macchina virtuale digitare:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Questa prima risposta potrebbe richiedere fino a 2 minuti. D'ora in poi, dovrebbero visualizzare l'output quasi immediatamente.

Da un computer in cui è installata l'interfaccia della riga di comando di Azure (ad esempio il computer locale), simulare un'operazione di sfratto usando [az vm simulate-eviction](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction). Sostituire il nome del gruppo di risorse e il nome della macchina virtuale con il proprio. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

L'output della risposta avrà `Status: Succeeded` se la richiesta è stata eseguita correttamente.

Tornare rapidamente alla connessione remota alla macchina virtuale Spot ed eseguire di nuovo una query sull Eventi pianificati endpoint. Ripetere il comando seguente fino a ottenere un output contenente altre informazioni:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Quando il servizio eventi pianificato ottiene la notifica di sfratto, si otterrà una risposta simile alla seguente:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

È possibile vedere che `"EventType":"Preempt"` , e la risorsa è la risorsa macchina virtuale `"Resources":["myspotvm"]` . 

È anche possibile vedere quando la macchina virtuale verrà sfrattata controllando : la macchina virtuale non verrà sfrattata prima del tempo specificato, in modo che l'applicazione si chiuda `"NotBefore"` normalmente.


## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare una macchina virtuale spot di Azure [usando Azure PowerShell](../windows/spot-powershell.md), [il portale](../spot-portal.md)o un [modello](spot-template.md).

Eseguire query sulle informazioni sui prezzi correnti usando [l'API](/rest/api/cost-management/retail-prices/azure-retail-prices) dei prezzi al dettaglio di Azure per informazioni sulla macchina virtuale spot di Azure. e `meterName` `skuName` conterranno entrambi `Spot` .

Se si verifica un errore, vedere [Codici di errore](../error-codes-spot.md).
