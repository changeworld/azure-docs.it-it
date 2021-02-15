---
title: Aggiornamento automatico delle estensioni per le macchine virtuali e i set di scalabilità in Azure
description: Informazioni su come abilitare l'aggiornamento automatico delle estensioni per le macchine virtuali e i set di scalabilità di macchine virtuali in Azure.
author: mayanknayar
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: acc014785105d14c3109cfa420f0e9402ca3f534
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417558"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Anteprima: aggiornamento automatico delle estensioni per le macchine virtuali e i set di scalabilità in Azure

L'aggiornamento automatico dell'estensione è disponibile in anteprima per le VM di Azure e i set di scalabilità di macchine virtuali di Azure. Quando l'aggiornamento automatico dell'estensione è abilitato in una macchina virtuale o in un set di scalabilità, l'estensione viene aggiornata automaticamente ogni volta che il server di pubblicazione dell'estensione rilascia una nuova versione per l'estensione.

 L'aggiornamento automatico dell'estensione presenta le funzionalità seguenti:
- Supportato per le VM di Azure e i set di scalabilità di macchine virtuali di Azure. Service Fabric set di scalabilità di macchine virtuali non sono attualmente supportati.
- Gli aggiornamenti vengono applicati in un modello di distribuzione First di disponibilità (descritto di seguito).
- Quando viene applicato a un set di scalabilità di macchine virtuali, non più del 20% delle macchine virtuali dei set di scalabilità di macchine virtuali verrà aggiornato in un singolo batch (soggetto a almeno una macchina virtuale per batch).
- Funziona per tutte le dimensioni delle macchine virtuali e per le estensioni Windows e Linux.
- È possibile rifiutare esplicitamente l'aggiornamento automatico in qualsiasi momento.
- L'aggiornamento automatico dell'estensione può essere abilitato in un set di scalabilità di macchine virtuali di qualsiasi dimensione.
- Ogni estensione supportata è registrata singolarmente ed è possibile scegliere le estensioni da aggiornare automaticamente.
- Supportato in tutte le aree del cloud pubblico.


> [!IMPORTANT]
> L'aggiornamento automatico dell'estensione è attualmente disponibile in anteprima pubblica. Per usare la funzionalità di anteprima pubblica descritta di seguito, è necessaria una procedura di consenso esplicito.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Funzionamento dell'aggiornamento automatico dell'estensione
Il processo di aggiornamento dell'estensione funziona sostituendo la versione dell'estensione esistente in una macchina virtuale con la nuova versione dell'estensione pubblicata dal server di pubblicazione dell'estensione. L'integrità della macchina virtuale viene monitorata dopo l'installazione della nuova estensione. Se la macchina virtuale non si trova in uno stato integro entro 5 minuti dal completamento dell'aggiornamento, viene eseguito il rollback della nuova versione dell'estensione alla versione precedente.

Un aggiornamento dell'estensione non riuscito viene ritentato automaticamente. Un nuovo tentativo viene eseguito ogni pochi giorni automaticamente senza l'intervento dell'utente.


## <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Processo di aggiornamento per i set di scalabilità di macchine virtuali
1. Prima di iniziare il processo di aggiornamento, l'agente di orchestrazione garantisce che non più del 20% delle VM nell'intero set di scalabilità sia non integro (per qualsiasi motivo).

2. L'agente di orchestrazione dell'aggiornamento identifica il batch di istanze di macchine virtuali da aggiornare, con qualsiasi batch con un massimo del 20% del numero totale di VM, soggetto a una dimensione minima del batch di una macchina virtuale.

3. Per i set di scalabilità con probe di integrità dell'applicazione configurati o estensione per l'integrità dell'applicazione, l'aggiornamento attende fino a 5 minuti (o la configurazione del probe di integrità definito) affinché la macchina virtuale diventi integra, prima di procedere con l'aggiornamento del batch successivo. Se una macchina virtuale non ripristina l'integrità dopo un aggiornamento, per impostazione predefinita viene reinstallata la versione precedente dell'estensione per la VM.

4. L'agente di orchestrazione dell'aggiornamento tiene traccia anche della percentuale di macchine virtuali che diventano non integre dopo un aggiornamento. L'aggiornamento verrà interrotto se più del 20% delle istanze aggiornate diventa non integro durante il processo di aggiornamento.

Questo processo continua fino a completare l'aggiornamento di tutte le istanze nel set di scalabilità.

L'agente di orchestrazione aggiornamento del set di scalabilità controlla l'integrità complessiva del set di scalabilità prima di aggiornare ogni batch. Durante l'aggiornamento di un batch, potrebbero essere presenti altre attività di manutenzione pianificate o non pianificate che potrebbero influisca sull'integrità delle macchine virtuali del set di scalabilità. In questi casi, se più del 20% delle istanze del set di scalabilità diventa non integro, l'aggiornamento del set di scalabilità si interrompe alla fine del batch corrente.


### <a name="availability-first-updates"></a>Disponibilità-aggiornamenti prima
Il modello di disponibilità-primo per gli aggiornamenti orchestrati della piattaforma garantirà che le configurazioni di disponibilità in Azure siano rispettate in più livelli di disponibilità.

Per un gruppo di macchine virtuali in fase di aggiornamento, la piattaforma Azure eseguirà l'orchestrazione degli aggiornamenti:

**Tra le aree:**
- Un aggiornamento verrà spostato in Azure a livello globale in modo graduale per evitare errori di distribuzione a livello di Azure.
- Una ' fase ' può costituire una o più aree e un aggiornamento si sposta tra le fasi solo se le macchine virtuali idonee in una fase vengono aggiornate correttamente.
- Le aree geografiche abbinate non verranno aggiornate simultaneamente e non possono trovarsi nella stessa fase di area.
- Il completamento di un aggiornamento viene misurato tenendo traccia dello stato di una macchina virtuale dopo l'aggiornamento. L'integrità della macchina virtuale viene rilevata tramite indicatori di integrità della piattaforma per la macchina virtuale. Nel caso dei set di scalabilità di macchine virtuali, l'integrità della VM viene rilevata tramite probe di integrità dell'applicazione o l'estensione dell'integrità dell'applicazione, se applicata al set di scalabilità.

**All'interno di un'area:**
- Le macchine virtuali in zone di disponibilità diverse non vengono aggiornate simultaneamente.
- Le singole macchine virtuali che non fanno parte di un set di disponibilità vengono eseguite in batch in base al massimo sforzo per evitare aggiornamenti simultanei per tutte le macchine virtuali in una sottoscrizione.  

**All'interno di un'set ':**
- Tutte le macchine virtuali in un set di disponibilità o in un set di scalabilità comuni non vengono aggiornate simultaneamente.  
- Le macchine virtuali in un set di disponibilità comune vengono aggiornate all'interno dei limiti del dominio di aggiornamento e le macchine virtuali in più domini di aggiornamento non vengono aggiornate simultaneamente.  
- Le macchine virtuali in un set di scalabilità di macchine virtuali comune vengono raggruppate in batch e aggiornate entro i limiti del dominio di aggiornamento.


## <a name="supported-extensions"></a>Estensioni supportate
L'anteprima dell'aggiornamento automatico dell'estensione supporta le estensioni seguenti (e altre vengono aggiunte periodicamente):
- Dependency Agent: [Windows](./extensions/agent-dependency-windows.md) e [Linux](./extensions/agent-dependency-linux.md)
- [Estensione per l'integrità dell'applicazione](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) : Windows e Linux


## <a name="enabling-preview-access"></a>Abilitazione dell'accesso in anteprima
Per abilitare la funzionalità di anteprima, è necessario un unico consenso esplicito per la funzionalità **AutomaticExtensionUpgradePreview** per sottoscrizione, come descritto di seguito.

### <a name="rest-api"></a>API REST
Nell'esempio seguente viene descritto come abilitare l'anteprima per la sottoscrizione:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) per abilitare l'anteprima per la sottoscrizione.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Usare [AZ feature Register](/cli/azure/feature#az-feature-register) per abilitare l'anteprima per la sottoscrizione.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Abilitazione dell'aggiornamento automatico dell'estensione
Per abilitare l'aggiornamento automatico dell'estensione per un'estensione, è necessario assicurarsi che la proprietà *enableAutomaticUpgrade* sia impostata su *true* e aggiunta a ogni definizione di estensione singolarmente.


### <a name="rest-api-for-virtual-machines"></a>API REST per le macchine virtuali
Per abilitare l'aggiornamento automatico dell'estensione per un'estensione (in questo esempio l'estensione Dependency Agent) in una macchina virtuale di Azure, usare il comando seguente:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>API REST per i set di scalabilità di macchine virtuali
Usare il comando seguente per aggiungere l'estensione al modello del set di scalabilità:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell per le macchine virtuali
Usare il cmdlet [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) :

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell per i set di scalabilità di macchine virtuali
Usare il cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) per aggiungere l'estensione al modello del set di scalabilità:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Aggiornare il set di scalabilità usando [Update-AzVmss](/powershell/module/az.compute/update-azvmss) dopo l'aggiunta dell'estensione.


### <a name="azure-cli-for-virtual-machines"></a>Interfaccia della riga di comando di Azure per le macchine virtuali
Usare il cmdlet [AZ VM Extension set](/cli/azure/vm/extension#az_vm_extension_set) :

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>CLI di Azure per i set di scalabilità di macchine virtuali
Usare il cmdlet [AZ vmss Extension set](/cli/azure/vmss/extension#az_vmss_extension_set) per aggiungere l'estensione al modello del set di scalabilità:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Aggiornamenti di estensione con più estensioni

Per una VM o un set di scalabilità di macchine virtuali possono essere abilitate più estensioni con aggiornamento automatico delle estensioni, oltre ad altre estensioni senza aggiornamenti automatici delle estensioni.  

Se sono disponibili più aggiornamenti dell'estensione per una macchina virtuale, gli aggiornamenti possono essere raggruppati in batch. Ogni aggiornamento dell'estensione viene tuttavia applicato singolarmente in una macchina virtuale. Un errore in un'estensione non influisca sulle altre estensioni che potrebbero essere in corso di aggiornamento. Se, ad esempio, sono pianificate due estensioni per un aggiornamento e il primo aggiornamento dell'estensione ha esito negativo, la seconda estensione verrà ancora aggiornata.

Gli aggiornamenti automatici delle estensioni possono essere applicati anche quando una VM o un set di scalabilità di macchine virtuali ha più estensioni configurate con la [sequenziazione](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)delle estensioni. La sequenziazione delle estensioni è applicabile per la prima distribuzione della macchina virtuale e gli eventuali aggiornamenti di estensione successivi in un'estensione vengono applicati in modo indipendente.


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Informazioni sull'estensione per l'integrità dell'applicazione](./windows/automatic-vm-guest-patching.md)
