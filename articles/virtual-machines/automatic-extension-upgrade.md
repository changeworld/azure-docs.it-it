---
title: Aggiornamento automatico delle estensioni per macchine virtuali e set di scalabilità in Azure
description: Informazioni su come abilitare l'aggiornamento automatico delle estensioni per le macchine virtuali e i set di scalabilità di macchine virtuali in Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: bf9e802e2485e84211044ce650c7748e789e752e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762606"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Anteprima: Aggiornamento automatico delle estensioni per macchine virtuali e set di scalabilità in Azure

L'aggiornamento automatico delle estensioni è disponibile in anteprima per le macchine virtuali di Azure e i set di scalabilità di macchine virtuali di Azure. Quando l'aggiornamento automatico delle estensioni è abilitato in una macchina virtuale o in un set di scalabilità, l'estensione viene aggiornata automaticamente ogni volta che il server di pubblicazione dell'estensione rilascia una nuova versione per tale estensione.

 L'aggiornamento automatico delle estensioni include le funzionalità seguenti:
- Supportato per le macchine virtuali di Azure e i set di scalabilità di macchine virtuali di Azure. Service Fabric set di scalabilità di macchine virtuali non sono attualmente supportati.
- Gli aggiornamenti vengono applicati in un modello di distribuzione availability-first (descritto di seguito).
- Per un set di scalabilità di macchine virtuali, non più del 20% delle macchine virtuali del set di scalabilità verrà aggiornato in un singolo batch. La dimensione minima del batch è una macchina virtuale.
- Funziona per tutte le dimensioni delle macchine virtuali e per le estensioni Windows e Linux.
- È possibile rifiutare esplicitamente gli aggiornamenti automatici in qualsiasi momento.
- L'aggiornamento automatico delle estensioni può essere abilitato in set di scalabilità di macchine virtuali di qualsiasi dimensione.
- Ogni estensione supportata viene registrato singolarmente ed è possibile scegliere quali estensioni aggiornare automaticamente.
- Supportato in tutte le aree del cloud pubblico.


> [!IMPORTANT]
> L'aggiornamento automatico delle estensioni è attualmente disponibile in anteprima pubblica. Per usare la funzionalità di anteprima pubblica descritta di seguito, è necessaria una procedura di consenso esplicito.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Come funziona l'aggiornamento automatico delle estensioni?
Il processo di aggiornamento dell'estensione sostituisce la versione dell'estensione esistente in una macchina virtuale con una nuova versione della stessa estensione quando viene pubblicata dall'autore dell'estensione. L'integrità della macchina virtuale viene monitorata dopo l'installazione della nuova estensione. Se la macchina virtuale non è in uno stato integro entro 5 minuti dal completamento dell'aggiornamento, viene eseguito il rollback della versione dell'estensione alla versione precedente.

Un aggiornamento dell'estensione non riuscito viene ritentato automaticamente. Viene eseguito un nuovo tentativo ogni pochi giorni automaticamente senza l'intervento dell'utente.

### <a name="availability-first-updates"></a>Aggiornamenti per la disponibilità
Il modello availability-first per gli aggiornamenti orchestrati dalla piattaforma garantisce che le configurazioni di disponibilità in Azure siano rispettate in più livelli di disponibilità.

Per un gruppo di macchine virtuali in fase di aggiornamento, la piattaforma Azure orchestra gli aggiornamenti:

**Tra aree:**
- Un aggiornamento verrà spostato in Azure a livello globale in modo graduale per evitare errori di distribuzione a livello di Azure.
- Una "fase" può avere una o più aree e un aggiornamento viene spostato tra più fasi solo se le macchine virtuali idonee nella fase precedente vengono aggiornate correttamente.
- Le aree con associazione geografica non verranno aggiornate contemporaneamente e non potranno essere nella stessa fase a livello di area.
- L'esito positivo di un aggiornamento viene misurato tramite il rilevamento dell'integrità di una macchina virtuale dopo l'aggiornamento. L'integrità della macchina virtuale viene monitorata tramite gli indicatori di integrità della piattaforma per la macchina virtuale. Per i set di scalabilità di macchine virtuali, l'integrità della macchina virtuale viene monitorata tramite probe di integrità dell'applicazione o l'estensione Integrità applicazione, se applicata al set di scalabilità.

**All'interno di un'area:**
- Le macchine virtuali in zone di disponibilità non vengono aggiornate contemporaneamente.
- Le singole macchine virtuali che non fanno parte di un set di disponibilità vengono eseguite in batch in batch per evitare aggiornamenti simultanei per tutte le macchine virtuali in una sottoscrizione.  

**All'interno di un 'set':**
- Tutte le macchine virtuali in un set di disponibilità o un set di scalabilità comune non vengono aggiornate contemporaneamente.  
- Le macchine virtuali in un set di disponibilità comune vengono aggiornate entro i limiti del dominio di aggiornamento e le macchine virtuali in più domini di aggiornamento non vengono aggiornate contemporaneamente.  
- Le macchine virtuali in un set di scalabilità di macchine virtuali comune vengono raggruppate in batch e aggiornate entro i limiti del dominio di aggiornamento.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Processo di aggiornamento per i set di scalabilità di macchine virtuali
1. Prima di iniziare il processo di aggiornamento, l'agente di orchestrazione garantisce che non più del 20% delle macchine virtuali nell'intero set di scalabilità non sia integro (per qualsiasi motivo).

2. L'agente di orchestrazione dell'aggiornamento identifica il batch di istanze di macchine virtuali da aggiornare. Un batch di aggiornamento può avere un massimo del 20% del numero totale di macchine virtuali, soggetto a una dimensione batch minima di una macchina virtuale.

3. Per i set di scalabilità con probe di integrità dell'applicazione configurati o l'estensione integrità applicazione, l'aggiornamento attende fino a 5 minuti (o la configurazione del probe di integrità definita) per l'integrità della macchina virtuale prima di aggiornare il batch successivo. Se una macchina virtuale non ripristina l'integrità dopo un aggiornamento, per impostazione predefinita viene reinstallata la versione dell'estensione precedente nella macchina virtuale.

4. L'agente di orchestrazione dell'aggiornamento tiene traccia anche della percentuale di macchine virtuali che diventano non integre dopo un aggiornamento. L'aggiornamento verrà interrotto se più del 20% delle istanze aggiornate diventa non integro durante il processo di aggiornamento.

Questo processo continua fino a completare l'aggiornamento di tutte le istanze nel set di scalabilità.

L'agente di orchestrazione dell'aggiornamento del set di scalabilità verifica l'integrità complessiva del set di scalabilità prima di aggiornare ogni batch. Durante l'aggiornamento di un batch, potrebbero esserci altre attività di manutenzione pianificate o non pianificate simultanee che potrebbero influire sull'integrità delle macchine virtuali del set di scalabilità. In questi casi, se più del 20% delle istanze del set di scalabilità diventa non integro, l'aggiornamento del set di scalabilità si interrompe alla fine del batch corrente.

## <a name="supported-extensions"></a>Estensioni supportate
L'anteprima dell'aggiornamento automatico delle estensioni supporta le estensioni seguenti (e ne vengono aggiunte periodicamente altre):
- Dependency Agent: [Windows](./extensions/agent-dependency-windows.md) e [Linux](./extensions/agent-dependency-linux.md)
- [Estensione integrità applicazione](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) - Windows e Linux


## <a name="enabling-preview-access"></a>Abilitazione dell'accesso in anteprima
L'abilitazione della funzionalità di anteprima richiede il consenso esplicito una sola volta per la funzionalità **AutomaticExtensionUpgradePreview** per ogni sottoscrizione, come descritto di seguito.

### <a name="rest-api"></a>API REST
L'esempio seguente descrive come abilitare l'anteprima per la sottoscrizione:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

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

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Usare [az feature register per](/cli/azure/feature#az_feature_register) abilitare l'anteprima per la sottoscrizione.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Abilitazione dell'aggiornamento automatico delle estensioni
Per abilitare l'aggiornamento automatico delle estensioni per un'estensione, è necessario assicurarsi che la proprietà *enableAutomaticUpgrade* sia impostata su *true* e aggiunta a ogni definizione di estensione singolarmente.


### <a name="rest-api-for-virtual-machines"></a>API REST per macchine virtuali
Per abilitare l'aggiornamento automatico dell'estensione per un'estensione (in questo esempio l'estensione Dependency Agent) in una macchina virtuale di Azure, usare quanto segue:

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

### <a name="rest-api-for-virtual-machine-scale-sets"></a>API REST per set di scalabilità di macchine virtuali
Usare il codice seguente per aggiungere l'estensione al modello del set di scalabilità:

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
Usare il cmdlet [Set-AzVMExtension:](/powershell/module/az.compute/set-azvmextension)

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


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell set di scalabilità di macchine virtuali
Usare il cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) per aggiungere l'estensione al modello del set di scalabilità:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Aggiornare il set di scalabilità [usando Update-AzVmss dopo](/powershell/module/az.compute/update-azvmss) aver aggiunto l'estensione.


### <a name="azure-cli-for-virtual-machines"></a>Interfaccia della riga di comando di Azure per le macchine virtuali
Usare il cmdlet [az vm extension set:](/cli/azure/vm/extension#az_vm_extension_set)

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Interfaccia della riga di comando di Azure per set di scalabilità di macchine virtuali
Usare il cmdlet [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) per aggiungere l'estensione al modello del set di scalabilità:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Aggiornamenti delle estensioni con più estensioni

Una macchina virtuale o un set di scalabilità di macchine virtuali può avere più estensioni con l'aggiornamento automatico delle estensioni abilitato. Lo stesso set di scalabilità o macchina virtuale può avere anche altre estensioni senza l'aggiornamento automatico delle estensioni abilitato.  

Se sono disponibili più aggiornamenti di estensione per una macchina virtuale, gli aggiornamenti possono essere uniti in batch, ma ogni aggiornamento dell'estensione viene applicato singolarmente in una macchina virtuale. Un errore in un'estensione non influisce sulle altre estensioni che potrebbero essere in corso di aggiornamento. Ad esempio, se due estensioni sono pianificate per un aggiornamento e il primo aggiornamento dell'estensione non riesce, la seconda estensione verrà comunque aggiornata.

Gli aggiornamenti automatici delle estensioni possono essere applicati anche quando una macchina virtuale o un set di scalabilità di macchine virtuali ha più estensioni configurate con la [sequenziazione dell'estensione](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). La sequenziazione delle estensioni è applicabile per la prima distribuzione della macchina virtuale ed eventuali aggiornamenti futuri dell'estensione in un'estensione vengono applicati in modo indipendente.


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Informazioni sull'estensione per l'integrità dell'applicazione](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
