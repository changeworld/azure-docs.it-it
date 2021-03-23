---
title: Usare l'interfaccia della riga di comando per distribuire macchine virtuali Azure spot
description: Informazioni su come usare l'interfaccia della riga di comando per distribuire macchine virtuali Azure spot per ridurre i costi.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 90ad35757834c14abdffb017ff31b3296074ca24
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802438"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Distribuire macchine virtuali Azure spot usando l'interfaccia della riga di comando di Azure

Con le [macchine virtuali di Azure spot](../spot-vms.md) è possibile sfruttare i vantaggi della capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le macchine virtuali di Azure spot. Le macchine virtuali di Azure spot sono quindi ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali Azure spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere i prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

È possibile impostare un prezzo massimo che si è disposti a pagare, per ora, per la macchina virtuale. Il prezzo massimo per una macchina virtuale Azure spot può essere impostato in dollari USA (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il prezzo massimo su `-1`, la macchina virtuale non verrà eliminata in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per la macchina virtuale di Azure spot o al prezzo di una VM standard, che sempre è inferiore, purché siano disponibili capacità e quota. Per altre informazioni sull'impostazione del prezzo massimo, vedere [macchine virtuali di Azure spot-prezzi](../spot-vms.md#pricing).

Il processo di creazione di una macchina virtuale di Azure con l'interfaccia della riga di comando di Azure è identico a quello descritto nell' [articolo introduttivo](./quick-create-cli.md). È sufficiente aggiungere il parametro '--Priority spot ', impostare `--eviction-policy` su deallocate (impostazione predefinita) o `Delete` e specificare un prezzo massimo o `-1` . 


## <a name="install-azure-cli"></a>Installare l'interfaccia da riga di comando di Azure

Per creare macchine virtuali Azure spot, è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.74 o successiva. Eseguire **az --version** per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

Accedere ad Azure tramite [az login](/cli/azure/reference-index#az-login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Creare una macchina virtuale Azure spot

Questo esempio illustra come distribuire una macchina virtuale Linux Azure spot che non verrà rimossa in base al prezzo. I criteri di rimozione sono impostati per deallocare la macchina virtuale, in modo che possa essere riavviata in un secondo momento. Se si vuole eliminare la macchina virtuale e il disco sottostante quando la macchina virtuale viene rimossa, impostare `--eviction-policy` su `Delete` .

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

## <a name="simulate-an-eviction"></a>Simulare un'eliminazione

È possibile simulare un'eliminazione di una macchina virtuale di Azure spot usando REST, PowerShell o l'interfaccia della riga di comando per verificare il grado di risposta dell'applicazione a una rimozione improvvisa.

Nella maggior parte dei casi, è consigliabile usare le macchine virtuali dell'API REST, [simulando la rimozione](/rest/api/compute/virtualmachines/simulateeviction) , per consentire il test automatizzato delle applicazioni. Per REST, un `Response Code: 204` indica che l'eliminazione simulata ha avuto esito positivo. È possibile combinare eliminazioni simulate con il [servizio eventi pianificato](scheduled-events.md), per automatizzare il modo in cui l'app risponderà quando la macchina virtuale verrà eliminata.

Per visualizzare gli eventi pianificati in azione, Guarda [Azure Friday-uso di azure eventi pianificati per preparare la manutenzione della macchina virtuale](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Test rapido

Per un rapido test per illustrare il funzionamento di un'operazione di rimozione simulata, è possibile esaminare il servizio eventi pianificati per verificarne l'aspetto quando si simula una rimozione usando l'interfaccia della riga di comando di Azure.

Il servizio eventi pianificato è abilitato per il servizio la prima volta che si effettua una richiesta per gli eventi. 

Accedere in remoto alla macchina virtuale e quindi aprire un prompt dei comandi. 

Al prompt dei comandi nella macchina virtuale, digitare:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Questa prima risposta potrebbe richiedere fino a 2 minuti. Da questo momento in poi, dovrebbero visualizzare l'output quasi immediatamente.

Da un computer in cui è installata l'interfaccia della riga di comando di Azure, ad esempio il computer locale, simulare un'eliminazione usando [AZ VM simulate-sfratto](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction). Sostituire il nome del gruppo di risorse e il nome della macchina virtuale con quelli personalizzati. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
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

È anche possibile vedere quando la macchina virtuale verrà rimossa controllando `"NotBefore"` che la macchina virtuale non verrà rimossa prima del tempo specificato, in modo che sia la finestra per l'applicazione a chiudersi normalmente.


## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare una macchina virtuale Azure spot usando [Azure PowerShell](../windows/spot-powershell.md), il [portale](../spot-portal.md)o un [modello](spot-template.md).

Eseguire query sulle informazioni sui prezzi correnti usando l' [API prezzi al dettaglio di Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) per informazioni sulla macchina virtuale Azure spot. `meterName`E `skuName` conterranno entrambi `Spot` .

Se si verifica un errore, vedere [codici di errore](../error-codes-spot.md).
