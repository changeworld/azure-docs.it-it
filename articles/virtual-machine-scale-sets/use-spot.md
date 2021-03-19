---
title: Creare un set di scalabilità che usa macchine virtuali Azure spot
description: Informazioni su come creare set di scalabilità di macchine virtuali di Azure che usano macchine virtuali Azure spot per risparmiare sui costi.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a176a30a1e21ec03c2da329785ab895ec67a4faf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596417"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Macchine virtuali Azure spot per i set di scalabilità di macchine virtuali 

L'uso di macchine virtuali di Azure spot nei set di scalabilità consente di sfruttare i vantaggi della capacità inutilizzata a un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le istanze di macchine virtuali di Azure spot. Le istanze di macchine virtuali di Azure spot sono quindi ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità disponibile dipende dalle dimensioni, dall'area, dal momento della giornata e da altri fattori. Quando si distribuiscono istanze di macchine virtuali di Azure spot nei set di scalabilità, Azure lo alloca solo se è disponibile una capacità, ma non esiste alcun contratto di contratto per queste istanze. Un set di scalabilità di macchine virtuali di Azure spot viene distribuito in un singolo dominio di errore e non offre garanzie di disponibilità elevata.


## <a name="pricing"></a>Prezzi

I prezzi per le istanze di macchine virtuali di Azure spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Con i prezzi variabili è possibile impostare un prezzo massimo, in dollari statunitensi (USD), usando fino a cinque cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il prezzo massimo `-1` , l'istanza non verrà rimossa in base al prezzo. Il prezzo per l'istanza sarà il prezzo corrente per la macchina virtuale di Azure spot o il prezzo di un'istanza standard, che sempre è inferiore, purché siano disponibili capacità e quota.


## <a name="limitations"></a>Limitazioni

Le dimensioni seguenti non sono supportate per le macchine virtuali Azure spot:
 - Serie B
 - Versioni promo di qualsiasi dimensione (ad esempio, dimensioni promo Dv2, NV, NC, H)

La macchina virtuale Azure spot può essere distribuita in qualsiasi area, ad eccezione Microsoft Azure Cina 21Vianet.

<a name="channel"></a>

Sono attualmente supportati i [tipi di offerta](https://azure.microsoft.com/support/legal/offer-details/) seguenti:

-   Enterprise Agreement
-   Codice dell'offerta con pagamento in base al consumo 003P
-   Sponsorizzato
- Per il provider di servizi cloud (CSP), vedere il centro per i [partner](/partner-center/azure-plan-get-started) oppure contattare direttamente il partner.

## <a name="eviction-policy"></a>Criteri di rimozione

Quando si crea un set di scalabilità con macchine virtuali di Azure spot, è possibile impostare i criteri di rimozione per *deallocare* (impostazione predefinita) o *eliminare*. 

Il criterio *deallocate* sposta le istanze eliminate nello stato arrestato-deallocato, consentendo di ridistribuire le istanze eliminate. Tuttavia, non è garantito che l'allocazione avrà esito positivo. Le macchine virtuali deallocate verranno incluse nel conteggio per la quota delle istanze del set di scalabilità e verranno addebitati i costi dei dischi sottostanti. 

Se si desidera che le istanze vengano eliminate al momento della rimozione, è possibile impostare i criteri di rimozione da *eliminare*. Con i criteri di rimozione impostati per l'eliminazione è quindi possibile creare nuove macchine virtuali aumentando il valore della proprietà del numero di istanze di set di scalabilità. Le macchine virtuali rimosse vengono eliminate insieme ai relativi dischi sottostanti, quindi non verrà addebitato alcun costo per l'archiviazione. Si può anche usare la funzionalità di ridimensionamento automatico dei set di scalabilità per cercare di compensare automaticamente le macchine virtuali rimosse, tuttavia non esiste alcuna garanzia di successo dell'allocazione. Si consiglia di usare la funzionalità di scalabilità automatica nei set di scalabilità di macchine virtuali Azure spot quando si impostano i criteri di rimozione da eliminare per evitare il costo dei dischi e raggiungere i limiti di quota. 

Gli utenti possono acconsentire esplicitamente a ricevere notifiche in-VM tramite [Azure eventi pianificati](../virtual-machines/linux/scheduled-events.md). In questo modo riceveranno una notifica se le macchine virtuali vengono eliminate e avranno a disposizione 30 secondi per completare i processi ed eseguire le attività di arresto prima dell'eliminazione. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Prova & ripristino (anteprima)

Questa nuova funzionalità a livello di piattaforma userà l'intelligenza artificiale per provare automaticamente a ripristinare le istanze di macchine virtuali di Azure spot eliminate all'interno di un set di scalabilità per gestire il numero di istanze di destinazione. 

> [!IMPORTANT]
> Prova & il ripristino è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Prova & ripristinare i vantaggi:
- Tenta di ripristinare le macchine virtuali di Azure spot eliminate a causa della capacità.
- Le macchine virtuali di Azure spot ripristinate dovrebbero essere eseguite per un periodo di tempo più lungo con una minore probabilità di una capacità attivata per la rimozione.
- Migliora la durata di una macchina virtuale Azure spot, quindi i carichi di lavoro vengono eseguiti per un periodo di tempo più lungo.
- Consente ai set di scalabilità di macchine virtuali di gestire il numero di destinazioni per le macchine virtuali Azure spot, in modo analogo alla funzionalità di conteggio destinazione già esistente per le VM con pagamento in base al consumo.

Provare & il ripristino è disabilitato nei set di scalabilità che usano la [scalabilità](virtual-machine-scale-sets-autoscale-overview.md)automatica. Il numero di macchine virtuali nel set di scalabilità è determinato dalle regole di scalabilità automatica.

### <a name="register-for-try--restore"></a>Eseguire la registrazione per try & Restore

Prima di poter usare la funzionalità Try & Restore, è necessario registrare la sottoscrizione per l'anteprima. Il completamento della registrazione potrebbe richiedere diversi minuti. Per completare la registrazione delle funzionalità, è possibile usare l'interfaccia della riga di comando di Azure o PowerShell.


**Usare l'interfaccia della riga di comando**

Usare [AZ feature Register](/cli/azure/feature#az-feature-register) per abilitare l'anteprima per la sottoscrizione. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**Usare PowerShell** 

Usare il cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) per abilitare l'anteprima per la sottoscrizione. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Gruppi di posizionamento

Il gruppo di posizionamento è un costrutto simile a un set di disponibilità di Azure, con domini di errore e domini di aggiornamento propri. Per impostazione predefinita, un set di scalabilità è costituito da un singolo gruppo di posizionamento con una dimensione massima di 100 VM. Se la proprietà del set di scalabilità denominata `singlePlacementGroup` è impostata su *false*, il set di scalabilità può essere composto da più gruppi di posizionamento e presenta un intervallo di 0-1000 VM. 

> [!IMPORTANT]
> A meno che non si usi InfiniBand con HPC, si consiglia vivamente di impostare la proprietà del set `singlePlacementGroup` di scalabilità su *false* per abilitare più gruppi di posizionamento per una migliore scalabilità nell'area o nella zona. 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Distribuzione di macchine virtuali Azure spot nei set di scalabilità

Per distribuire le macchine virtuali Azure spot nei set di scalabilità, è possibile impostare il nuovo flag di *priorità* su *spot*. Tutte le macchine virtuali nel set di scalabilità verranno impostate su spot. Per creare un set di scalabilità con macchine virtuali di Azure spot, usare uno dei metodi seguenti:
- [Azure portal](#portal)
- [Interfaccia della riga di comando di Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modelli di Gestione risorse di Azure](#resource-manager-templates)

## <a name="portal"></a>Portale

Il processo di creazione di un set di scalabilità che usa macchine virtuali Azure spot è identico a quello descritto nell' [articolo introduttivo](quick-create-portal.md). Quando si distribuisce un set di scalabilità, è possibile scegliere di impostare il flag spot e i criteri di rimozione: ![ creare un set di scalabilità con macchine virtuali di Azure spot](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il processo per creare un set di scalabilità con macchine virtuali di Azure spot è identico a quello descritto nell' [articolo introduttivo](quick-create-cli.md). È sufficiente aggiungere "--Priority spot" e aggiungere `--max-price` . In questo esempio viene usato `-1` per, `--max-price` quindi l'istanza non verrà rimossa in base al prezzo.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Il processo per creare un set di scalabilità con macchine virtuali di Azure spot è identico a quello descritto nell' [articolo introduttivo](quick-create-powershell.md).
È sufficiente aggiungere "-Priority spot" e fornire un oggetto `-max-price` a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Il processo per creare un set di scalabilità che usa macchine virtuali di Azure è identico a quello descritto nell'articolo introduttivo per [Linux](quick-create-template-linux.md) o [Windows](quick-create-template-windows.md). 

Per le distribuzioni di modelli di macchina virtuale Azure spot, usare `"apiVersion": "2019-03-01"` o versione successiva. 

Aggiungere le `priority` `evictionPolicy` proprietà, e `billingProfile` alla sezione `"virtualMachineProfile":` e la `"singlePlacementGroup": false,` proprietà alla `"Microsoft.Compute/virtualMachineScaleSets"` sezione nel modello:

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

Per eliminare l'istanza dopo che è stata rimossa, impostare il `evictionPolicy` parametro su `Delete` .


## <a name="simulate-an-eviction"></a>Simulare un'eliminazione

È possibile [simulare un'eliminazione](/rest/api/compute/virtualmachines/simulateeviction) di una macchina virtuale di Azure spot per verificare il grado di risposta dell'applicazione a una rimozione improvvisa. 

Sostituire quanto segue con le informazioni: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` indica che l'eliminazione simulata è stata completata. 

## <a name="faq"></a>Domande frequenti

**D:** Una volta creata, è un'istanza di macchina virtuale di Azure spot uguale all'istanza standard?

**R:** Sì, ad eccezione del fatto che non esiste alcun contratto di contratto per le macchine virtuali di Azure spot ed è possibile rimuoverli in qualsiasi momento.


**D:** Cosa si può fare se la VM viene eliminata, ma è ancora necessaria capacità?

**R:** È consigliabile usare VM standard anziché macchine virtuali Azure spot se è necessaria immediatamente la capacità.


**D:** Come viene gestita la quota per la macchina virtuale Azure spot?

**R:** Le istanze di macchine virtuali e le istanze standard di Azure spot avranno pool di quote separati. La quota della macchina virtuale Azure spot verrà condivisa tra le macchine virtuali e le istanze del set di scalabilità. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).


**D:** È possibile richiedere una quota aggiuntiva per la macchina virtuale Azure spot?

**R:** Sì, sarà possibile inviare la richiesta per aumentare la quota per le macchine virtuali Azure spot tramite il [processo standard di richiesta di quota](../azure-portal/supportability/per-vm-quota-requests.md).


**D:** È possibile convertire I set di scalabilità esistenti nei set di scalabilità di macchine virtuali di Azure spot?

**R:** No, l'impostazione del `Spot` flag è supportata solo in fase di creazione.


**D:** Se si usa `low` per i set di scalabilità con priorità bassa, è necessario iniziare a usare `Spot` invece?

**R:** Per il momento, sia che `low` `Spot` funzioneranno, ma è necessario iniziare a eseguire la transizione a usando `Spot` .


**D:** È possibile creare un set di scalabilità con macchine virtuali normali e macchine virtuali Azure spot?

**R:** No, un set di scalabilità non può supportare più di un tipo di priorità.


**D:**  È possibile usare la scalabilità automatica con i set di scalabilità di macchine virtuali Azure spot?

**R:** Sì, è possibile impostare le regole di scalabilità automatica nel set di scalabilità di macchine virtuali Azure spot. Se le macchine virtuali vengono eliminate, la scalabilità automatica può tentare di creare nuove macchine virtuali di Azure spot. Tuttavia, tenere presente che questa capacità potrebbe non funzionare. 


**D:**  La scalabilità automatica funziona con entrambi i criteri di rimozione (deallocazione ed eliminazione)?

**R:** Sì, tuttavia è consigliabile impostare i criteri di rimozione per l'eliminazione quando si usa la scalabilità automatica. Questo avviene perché le istanze appena deallocate sono incluse nel calcolo delle capacità nel set di scalabilità. Quando si usa la scalabilità automatica, probabilmente verrà raggiunto rapidamente il numero di istanze di destinazione a causa delle istanze deallocate e rimosse. Inoltre, le operazioni di ridimensionamento possono essere influenzate da eliminazioni di spot. Ad esempio, le istanze del set di scalabilità di macchine virtuali possono scendere al di sotto del conteggio dei set min a causa di più eliminazioni spot durante le operazioni di ridimensionamento 


**D:** Dove è possibile pubblicare le domande?

**R:** È possibile pubblicare e contrassegnare la domanda con `azure-spot` in [Domande e risposte](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui prezzi, vedere la [pagina dei prezzi dei set di scalabilità di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/).