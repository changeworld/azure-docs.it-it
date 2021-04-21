---
title: Creare un set di scalabilità che usa macchine virtuali spot di Azure
description: Informazioni su come creare set di scalabilità di macchine virtuali di Azure che usano macchine virtuali spot di Azure per risparmiare sui costi.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 61bb87d84b96f988ae065a70b85d445fc8b96ccf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762948"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Macchine virtuali spot di Azure per set di scalabilità di macchine virtuali 

L'uso di macchine virtuali Spot di Azure nei set di scalabilità consente di sfruttare la capacità inutilizzata a un risparmio significativo sui costi. In qualsiasi momento in cui Azure necessita della capacità, l'infrastruttura di Azure eviterà le istanze di Macchine virtuali spot di Azure. Pertanto, le istanze di Macchine virtuali spot di Azure sono ottime per i carichi di lavoro in grado di gestire interruzioni come processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità disponibile dipende dalle dimensioni, dall'area, dal momento della giornata e da altri fattori. Quando si distribuiscono istanze di macchine virtuali spot di Azure nei set di scalabilità, Azure alloca l'istanza solo se è disponibile capacità, ma non esiste alcun contratto di servizio per queste istanze. Un set di scalabilità di macchine virtuali Spot di Azure viene distribuito in un singolo dominio di errore e non offre garanzie di disponibilità elevata.


## <a name="pricing"></a>Prezzi

I prezzi per le istanze di macchine virtuali spot di Azure sono variabili, in base all'area e alla SKU. Per altre informazioni, vedere prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows.](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) 


Con i prezzi variabili, è possibile impostare un prezzo massimo, in dollari USA (USD), usando fino a cinque cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il prezzo massimo su , l'istanza non verrà `-1` sfrattata in base al prezzo. Il prezzo per l'istanza sarà il prezzo corrente per la macchina virtuale spot di Azure o il prezzo per un'istanza standard, che è sempre inferiore, purché siano disponibili capacità e quota.


## <a name="limitations"></a>Limitazioni

Le dimensioni seguenti non sono supportate per le macchine virtuali spot di Azure:
 - Serie B
 - Versioni promo di qualsiasi dimensione (ad esempio, dimensioni promo Dv2, NV, NC, H)

La macchina virtuale spot di Azure può essere distribuita in qualsiasi area, ad Microsoft Azure Cina 21Vianet.

<a name="channel"></a>

Sono attualmente [supportati i tipi di](https://azure.microsoft.com/support/legal/offer-details/) offerta seguenti:

-   Enterprise Agreement
-   Codice dell'offerta con pagamento in base al go (003P)
-   Sponsor (0036P e 0136P)
- Per cloud service provider (CSP), vedere il Partner Center [o](/partner-center/azure-plan-get-started) contattare direttamente il partner.

## <a name="eviction-policy"></a>Criteri di rimozione

Quando si crea un set di scalabilità usando macchine virtuali spot di Azure, è possibile impostare i criteri di eliminazione su *Dealloca* (impostazione predefinita) o *Elimina*. 

Il *criterio Dealloca* sposta le istanze che sono state deallocate allo stato arrestato e deallocato, consentendo di ridistribuire le istanze che sono state annullate. Tuttavia, non è garantito che l'allocazione avrà esito positivo. Le macchine virtuali deallocate verranno incluse nel conteggio per la quota delle istanze del set di scalabilità e verranno addebitati i costi dei dischi sottostanti. 

Se si desidera eliminare le istanze quando vengono eliminate, è possibile impostare i criteri di eliminazione per *eliminare*. Con i criteri di rimozione impostati per l'eliminazione è quindi possibile creare nuove macchine virtuali aumentando il valore della proprietà del numero di istanze di set di scalabilità. Le macchine virtuali rimosse vengono eliminate insieme ai relativi dischi sottostanti, quindi non verrà addebitato alcun costo per l'archiviazione. Si può anche usare la funzionalità di ridimensionamento automatico dei set di scalabilità per cercare di compensare automaticamente le macchine virtuali rimosse, tuttavia non esiste alcuna garanzia di successo dell'allocazione. È consigliabile usare la funzionalità di scalabilità automatica solo nei set di scalabilità di macchine virtuali spot di Azure quando si impostano i criteri di eliminazione da eliminare per evitare il costo dei dischi e raggiungere i limiti di quota. 

Gli utenti possono acconsentire esplicitamente alla ricezione di notifiche in-VM [tramite Azure Eventi pianificati](../virtual-machines/linux/scheduled-events.md). In questo modo riceveranno una notifica se le macchine virtuali vengono eliminate e avranno a disposizione 30 secondi per completare i processi ed eseguire le attività di arresto prima dell'eliminazione. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Provare & ripristino (anteprima)

Questa nuova funzionalità a livello di piattaforma userà l'intelligenza artificiale per provare automaticamente a ripristinare le istanze di macchine virtuali spot di Azure all'interno di un set di scalabilità per mantenere il numero di istanze di destinazione. 

> [!IMPORTANT]
> Provare & il ripristino è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Provare & vantaggi del ripristino:
- Tentativi di ripristino di macchine virtuali spot di Azure che sono stati sgomberati a causa della capacità.
- Le macchine virtuali spot di Azure ripristinate verranno eseguite per un periodo di tempo più lungo con una probabilità inferiore di un'azione di sfratto attivata dalla capacità.
- Migliora la durata di una macchina virtuale spot di Azure, in modo che i carichi di lavoro vengono eseguiti per una durata più lunga.
- Consente ai set di scalabilità di macchine virtuali di mantenere il numero di destinazione per le macchine virtuali spot di Azure, in modo analogo alla funzionalità di conteggio delle destinazione già esistente per le macchine virtuali con pagamento in base al numero.

Provare & il ripristino è disabilitato nei set di scalabilità che usano [la scalabilità automatica.](virtual-machine-scale-sets-autoscale-overview.md) Il numero di macchine virtuali nel set di scalabilità è basato sulle regole di scalabilità automatica.

### <a name="register-for-try--restore"></a>Registrarsi per provare & ripristino

Prima di poter usare la funzionalità & ripristino, è necessario registrare la sottoscrizione per l'anteprima. Il completamento della registrazione può richiedere alcuni minuti. È possibile usare l'interfaccia della riga di comando di Azure o PowerShell per completare la registrazione della funzionalità.


**Usare l'interfaccia della riga di comando**

Usare [az feature register per](/cli/azure/feature#az_feature_register) abilitare l'anteprima per la sottoscrizione. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo. 

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

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Gruppi di posizionamento

Il gruppo di posizionamento è un costrutto simile a un set di disponibilità di Azure, con i propri domini di errore e domini di aggiornamento. Per impostazione predefinita, un set di scalabilità è costituito da un singolo gruppo di posizionamento con una dimensione massima di 100 VM. Se la proprietà del set di scalabilità denominata è impostata su false , il set di scalabilità può essere costituito da più gruppi di posizionamento e ha un intervallo di `singlePlacementGroup` 0-1.000 macchine virtuali.  

> [!IMPORTANT]
> A meno che non si utilizzi Infiniband con HPC, è consigliabile impostare la proprietà del set di scalabilità su false per abilitare più gruppi di posizionamento per una migliore scalabilità nell'area o nella `singlePlacementGroup` zona.  

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Distribuzione di macchine virtuali spot di Azure nei set di scalabilità

Per distribuire macchine virtuali spot di Azure nei set di scalabilità, è possibile impostare il nuovo flag *Di* priorità su *Spot*. Tutte le macchine virtuali nel set di scalabilità verranno impostate su Spot. Per creare un set di scalabilità con macchine virtuali spot di Azure, usare uno dei metodi seguenti:
- [Azure portal](#portal)
- [Interfaccia della riga di comando di Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modelli di Gestione risorse di Azure](#resource-manager-templates)

## <a name="portal"></a>Portale

Il processo di creazione di un set di scalabilità che usa macchine virtuali spot di Azure è lo stesso descritto in dettaglio [nell'articolo introduttivo](quick-create-portal.md). Quando si distribuisce un set di scalabilità, è possibile scegliere di impostare il flag Spot e i criteri di rimozione: Creare un set di scalabilità con macchine virtuali ![ spot di Azure](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il processo di creazione di un set di scalabilità con macchine virtuali spot di Azure è lo stesso descritto in dettaglio [nell'articolo introduttivo](quick-create-cli.md). È sufficiente aggiungere '--Priority Spot' e `--max-price` aggiungere . In questo esempio si usa per in modo che l'istanza non verrà `-1` `--max-price` sfrattata in base al prezzo.

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

Il processo di creazione di un set di scalabilità con macchine virtuali spot di Azure è lo stesso descritto in dettaglio [nell'articolo introduttivo](quick-create-powershell.md).
È sufficiente aggiungere "-Priority Spot" e fornire a `-max-price` [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Il processo di creazione di un set di scalabilità che usa macchine virtuali spot di Azure è identico a quello descritto in dettaglio nell'articolo introduttivo per [Linux](quick-create-template-linux.md) o [Windows.](quick-create-template-windows.md) 

Per le distribuzioni del modello di macchina virtuale spot di Azure, usare `"apiVersion": "2019-03-01"` o versione successiva. 

Aggiungere `priority` le `evictionPolicy` proprietà , e alla sezione e la proprietà alla sezione nel `billingProfile` `"virtualMachineProfile":` `"singlePlacementGroup": false,` `"Microsoft.Compute/virtualMachineScaleSets"` modello:

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

Per eliminare l'istanza dopo che è stata rimossa, modificare il `evictionPolicy` parametro in `Delete` .


## <a name="simulate-an-eviction"></a>Simulare un'evizione

È possibile [simulare l'evizione](/rest/api/compute/virtualmachines/simulateeviction) di una macchina virtuale spot di Azure per testare l'applicazione che risponderà a un'improvviso sfratto. 

Sostituire quanto segue con le proprie informazioni: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` indica che l'evizione simulata è riuscita. 

## <a name="faq"></a>Domande frequenti

**D:** Una volta creata, un'istanza di macchina virtuale spot di Azure corrisponde a un'istanza standard?

**A:** Sì, ad eccezione del fatto che non esiste alcun contratto di servizio per le macchine virtuali spot di Azure e possono essere espresse in qualsiasi momento.


**D:** Cosa si può fare se la VM viene eliminata, ma è ancora necessaria capacità?

**A:** Se è necessaria immediatamente la capacità, è consigliabile usare macchine virtuali standard anziché macchine virtuali spot di Azure.


**D:** Come viene gestita la quota per la macchina virtuale spot di Azure?

**A:** Le istanze di macchine virtuali spot di Azure e le istanze standard avranno pool di quota separati. La quota delle macchine virtuali spot di Azure verrà condivisa tra le macchine virtuali e le istanze del set di scalabilità. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).


**D:** È possibile richiedere una quota aggiuntiva per la macchina virtuale spot di Azure?

**A:** Sì, sarà possibile inviare la richiesta per aumentare la quota per le macchine virtuali spot di Azure tramite il [processo di richiesta di quota standard](../azure-portal/supportability/per-vm-quota-requests.md).


**D:** È possibile convertire i set di scalabilità esistenti in set di scalabilità di macchine virtuali spot di Azure?

**A:** No, l'impostazione `Spot` del flag è supportata solo al momento della creazione.


**D:** Se si usa per i set di scalabilità con priorità `low` bassa, è necessario iniziare a usare `Spot` ?

**A:** Per il momento, entrambi `low` e `Spot` funzioneranno, ma è consigliabile iniziare la transizione a usando `Spot` .


**D:** È possibile creare un set di scalabilità con macchine virtuali normali e macchine virtuali spot di Azure?

**A:** No, un set di scalabilità non può supportare più di un tipo di priorità.


**D:**  È possibile usare la scalabilità automatica con i set di scalabilità di macchine virtuali spot di Azure?

**A:** Sì, è possibile impostare regole di scalabilità automatica nel set di scalabilità di macchine virtuali spot di Azure. Se le macchine virtuali vengono sfrattate, la scalabilità automatica può provare a creare nuove macchine virtuali spot di Azure. Tuttavia, tenere presente che questa capacità potrebbe non funzionare. 


**D:**  La scalabilità automatica funziona con entrambi i criteri di eliminazione (deallocazione ed eliminazione)?

**A:** Sì, tuttavia è consigliabile impostare i criteri di eliminazione da eliminare quando si usa la scalabilità automatica. Questo avviene perché le istanze appena deallocate sono incluse nel calcolo delle capacità nel set di scalabilità. Quando si usa la scalabilità automatica, probabilmente verrà raggiunto rapidamente il numero di istanze di destinazione a causa delle istanze deallocate e rimosse. Inoltre, le operazioni di ridimensionamento potrebbero essere influenzate da sfratti spot. Ad esempio, le istanze del set di scalabilità di macchine virtuali potrebbero scendere al di sotto del numero minimo di set a causa di più sfratti spot durante le operazioni di ridimensionamento. 


**D:** Dove è possibile pubblicare le domande?

**R:** È possibile pubblicare e contrassegnare la domanda con `azure-spot` in [Domande e risposte](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui prezzi, vedere la [pagina dei prezzi dei set di scalabilità di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/).
