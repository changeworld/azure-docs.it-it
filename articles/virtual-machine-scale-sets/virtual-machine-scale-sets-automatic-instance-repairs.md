---
title: Riparazione automatica delle istanze con i set di scalabilità di macchine virtuali di Azure
description: Informazioni su come configurare i criteri di ripristino automatici per le istanze di macchina virtuale in un set di scalabilità
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 733f4602e43511924783f6bc8cb1bad29edb5ea0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762912"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Ripristini automatici delle istanze per i set di scalabilità di macchine virtuali di Azure

L'abilitazione della riparazione automatica delle istanze per i set di scalabilità di macchine virtuali di Azure consente di ottenere una disponibilità elevata per le applicazioni mantenendo un set di istanze integre. Se un'istanza nel set di scalabilità non [](./virtual-machine-scale-sets-health-extension.md) è integra come segnalato dall'estensione integrità dell'applicazione o dai [probe](../load-balancer/load-balancer-custom-probe-overview.md)di integrità del servizio di bilanciamento del carico, questa funzionalità esegue automaticamente il ripristino dell'istanza eliminando l'istanza non integra e creandone una nuova per sostituirla.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisiti per l'uso del ripristino automatico delle istanze

**Abilitare il monitoraggio dell'integrità delle applicazioni per il set di scalabilità**

Per il set di scalabilità deve essere abilitato il monitoraggio dell'integrità delle applicazioni per le istanze. Questa operazione può essere eseguita usando l'estensione [Integrità applicazione o](./virtual-machine-scale-sets-health-extension.md) i probe di integrità del servizio di [bilanciamento del carico.](../load-balancer/load-balancer-custom-probe-overview.md) È possibile attivarne solo uno alla volta. L'estensione per l'integrità dell'applicazione o i probe del servizio di bilanciamento del carico effettuano il ping dell'endpoint dell'applicazione configurato nelle istanze di macchina virtuale per determinare lo stato di integrità dell'applicazione. Questo stato di integrità viene usato dall'agente di orchestrazione del set di scalabilità per monitorare l'integrità dell'istanza ed eseguire le operazioni di ripristino quando necessario.

**Configurare l'endpoint per fornire lo stato di integrità**

Prima di abilitare i criteri di ripristino automatico delle istanze, assicurarsi che nelle istanze del set di scalabilità sia configurato un endpoint dell'applicazione per generare lo stato di integrità dell'applicazione. Quando un'istanza restituisce lo stato 200 (OK) nell'endpoint dell'applicazione, l'istanza viene contrassegnata come "Integra". In tutti gli altri casi, l'istanza è contrassegnata come "Non integra", inclusi gli scenari seguenti:

- Quando non è presente alcun endpoint dell'applicazione configurato all'interno delle istanze della macchina virtuale per fornire lo stato di integrità dell'applicazione
- Quando l'endpoint dell'applicazione non è configurato correttamente
- Quando l'endpoint dell'applicazione non è raggiungibile

Per le istanze contrassegnate come "Non integre", le riparazione automatiche vengono attivate dal set di scalabilità. Assicurarsi che l'endpoint applicazione sia configurato correttamente prima di abilitare i criteri di ripristino automatici per evitare riparazioni impreviste dell'istanza, mentre l'endpoint viene configurato.

**Numero massimo di istanze nel set di scalabilità**

Questa funzionalità è attualmente disponibile solo per i set di scalabilità con un massimo di 200 istanze. Il set di scalabilità può essere distribuito come gruppo di posizionamento singolo o multi-posizionamento, tuttavia il numero di istanze non può essere superiore a 200 se per il set di scalabilità è abilitato il ripristino automatico delle istanze.

**Versione dell'API**

I criteri di ripristino automatici sono supportati per l'API di calcolo versione 2018-10-01 o successiva.

**Restrizioni sugli spostamenti di risorse o sottoscrizioni**

Gli spostamenti di risorse o sottoscrizioni non sono attualmente supportati per i set di scalabilità quando è abilitata la funzionalità di ripristino automatico.

**Restrizione per i set di scalabilità di Service Fabric**

Questa funzionalità non è attualmente supportata per i set di scalabilità di Service Fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Come funzionano le riparazioni automatiche delle istanze?

La funzionalità di ripristino automatico delle istanze si basa sul monitoraggio dell'integrità delle singole istanze in un set di scalabilità. Le istanze di vm in un set di scalabilità possono essere configurate per generare lo stato di integrità dell'applicazione usando [l'estensione](./virtual-machine-scale-sets-health-extension.md) Integrità applicazione o i probe di [integrità del servizio di bilanciamento del carico.](../load-balancer/load-balancer-custom-probe-overview.md) Se viene rilevato che un'istanza non è integra, il set di scalabilità esegue un'azione di ripristino eliminando l'istanza non integra e creando una nuova istanza per sostituirla. Per creare la nuova istanza viene usato il modello di set di scalabilità di macchine virtuali più recente. Questa funzionalità può essere abilitata nel modello del set di scalabilità di macchine virtuali usando *l'oggetto automaticRepairsPolicy.*

### <a name="batching"></a>Creazione di batch

Le operazioni di ripristino automatico dell'istanza vengono eseguite in batch. In qualsiasi momento, non più del 5% delle istanze nel set di scalabilità viene ripristinato tramite i criteri di ripristino automatici. In questo modo è possibile evitare l'eliminazione simultanea e la ri-creazione di un numero elevato di istanze se non vengono trovate integre contemporaneamente.

### <a name="grace-period"></a>Periodo di tolleranza

Quando un'istanza passa attraverso un'operazione di modifica dello stato a causa di un'azione PUT, PATCH o POST eseguita sul set di scalabilità (ad esempio, ricreare l'immagine, ridistribuire, aggiornare e così via), qualsiasi azione di ripristino su tale istanza viene eseguita solo dopo l'attesa del periodo di tolleranza. Il periodo di tolleranza è la quantità di tempo per consentire all'istanza di tornare allo stato integro. Il periodo di tolleranza inizia dopo il completamento della modifica dello stato. Ciò consente di evitare operazioni di ripristino prematura o accidentale. Il periodo di tolleranza viene rispettato per qualsiasi istanza appena creata nel set di scalabilità(inclusa quella creata come risultato dell'operazione di ripristino). Il periodo di tolleranza è specificato in minuti nel formato ISO 8601 e può essere impostato usando la proprietà *automaticRepairsPolicy.gracePeriod*. Il periodo di tolleranza può essere compreso tra 30 minuti e 90 minuti e ha un valore predefinito di 30 minuti.

### <a name="suspension-of-repairs"></a>Sospensione delle riparazione 

I set di scalabilità di macchine virtuali consentono di sospendere temporaneamente le riparazione automatiche delle istanze, se necessario. ServiceState *per* le riparazione automatiche nella proprietà *orchestrationServices* nella visualizzazione istanza del set di scalabilità di macchine virtuali mostra lo stato corrente delle riparazione automatiche. Quando un set di scalabilità è acconsentito esplicitamente alle riparazione automatiche, il valore del parametro *serviceState* è impostato su *Running*. Quando le riparazione automatiche vengono sospese per un set di scalabilità, il parametro *serviceState* viene impostato su *Suspended.* Se *automaticRepairsPolicy* è definito in un set di scalabilità ma la funzionalità di ripristino automatico non è abilitata, il parametro *serviceState* è impostato su *Non in esecuzione.*

Se le istanze appena create per sostituire quelle non integre in un set di scalabilità continuano a rimanere non integre anche dopo aver eseguito ripetutamente operazioni di ripristino, come misura di sicurezza la piattaforma aggiorna *serviceState* per le ripusizioni automatiche su *Suspended.* È possibile riprendere le riprendimenti automatici impostando il valore di *serviceState* per le riposizioni automatiche su *In esecuzione.* Nella sezione relativa alla visualizzazione [](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) e all'aggiornamento dello stato del servizio dei criteri di ripristino automatici per il set di scalabilità sono disponibili istruzioni dettagliate. 

Il processo di ripristino automatico dell'istanza funziona come segue:

1. [Estensione integrità applicazione o](./virtual-machine-scale-sets-health-extension.md) probe di [integrità del](../load-balancer/load-balancer-custom-probe-overview.md) servizio di bilanciamento del carico effettuano il ping dell'endpoint dell'applicazione all'interno di ogni macchina virtuale nel set di scalabilità per ottenere lo stato di integrità dell'applicazione per ogni istanza.
2. Se l'endpoint risponde con stato 200 (OK), l'istanza viene contrassegnata come "Integro". In tutti gli altri casi (incluso se l'endpoint non è raggiungibile), l'istanza è contrassegnata come "Non integro".
3. Quando viene rilevato che un'istanza non è integra, il set di scalabilità attiva un'azione di ripristino eliminando l'istanza non integra e creandone una nuova per sostituirla.
4. Le riparazioni dell'istanza vengono eseguite in batch. In qualsiasi momento, non più del 5% delle istanze totali nel set di scalabilità viene ripristinato. Se un set di scalabilità ha meno di 20 istanze, le riparazioni vengono eseguite per un'istanza non integra alla volta.
5. Il processo precedente continua fino a quando non vengono ripristinate tutte le istanze non integre nel set di scalabilità.

## <a name="instance-protection-and-automatic-repairs"></a>Protezione dell'istanza e riparazioni automatiche

Se un'istanza di un set di scalabilità è protetta applicando uno dei criteri di [protezione,](./virtual-machine-scale-sets-instance-protection.md)le riparazioni automatiche non vengono eseguite in tale istanza. Questo vale per entrambi i criteri di protezione: Proteggi da *scale-in* e *Proteggi da azioni del set di* scalabilità. 

## <a name="terminatenotificationandautomaticrepairs"></a>Terminare la notifica e le riparazioni automatiche

Se la [funzionalità di notifica di](./virtual-machine-scale-sets-terminate-notification.md) terminazione è abilitata in un set di scalabilità, durante l'operazione di ripristino automatico, l'eliminazione di un'istanza non integra segue la configurazione di notifica di terminazione. Una notifica di terminazione viene inviata tramite il servizio metadati di Azure , eventi pianificati, e l'eliminazione dell'istanza viene ritardata per la durata del timeout di ritardo configurato. Tuttavia, la creazione di una nuova istanza per sostituire quella non integra non attende il completamento del timeout di ritardo.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Abilitazione dei criteri di ripristino automatici durante la creazione di un nuovo set di scalabilità

Per abilitare i criteri di ripristino automatici durante la [](#requirements-for-using-automatic-instance-repairs) creazione di un nuovo set di scalabilità, assicurarsi che tutti i requisiti per acconsentire esplicitamente a questa funzionalità siano soddisfatti. L'endpoint dell'applicazione deve essere configurato correttamente per le istanze del set di scalabilità per evitare l'attivazione di riparazione impreviste durante la configurazione dell'endpoint. Per i set di scalabilità appena creati, le operazioni di ripristino delle istanze vengono eseguite solo dopo l'attesa per la durata del periodo di tolleranza. Per abilitare il ripristino automatico dell'istanza in un set di scalabilità, *usare l'oggetto automaticRepairsPolicy* nel modello del set di scalabilità di macchine virtuali.

È anche possibile [](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) usare questo modello di avvio rapido per distribuire un set di scalabilità di macchine virtuali con probe di integrità del servizio di bilanciamento del carico e riparazione automatica delle istanze abilitata con un periodo di tolleranza di 30 minuti.

### <a name="azure-portal"></a>Portale di Azure
 
La procedura seguente abilita i criteri di ripristino automatici quando si crea un nuovo set di scalabilità.
 
1. Passare a **Set di scalabilità di macchine virtuali.**
1. Selezionare **+ Aggiungi per** creare un nuovo set di scalabilità.
1. Passare alla **scheda** Integrità. 
1. Individuare la **sezione** Integrità.
1. Abilitare **l'opzione Monitorare l'integrità dell'applicazione.**
1. Individuare la **sezione Criteri di ripristino** automatici.
1. Attivare **l'opzione** **Riparazione** automatica.
1. In **Periodo di tolleranza (min)** specificare il periodo di tolleranza in minuti. I valori consentiti sono compresi tra 30 e 90 minuti. 
1. Al termine della creazione del nuovo set di scalabilità, selezionare **il pulsante Rivedi e** crea.

### <a name="rest-api"></a>API REST

L'esempio seguente illustra come abilitare il ripristino automatico delle istanze in un modello di set di scalabilità. Usare l'API versione 2018-10-01 o successiva.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

La funzionalità di ripristino automatico dell'istanza può essere abilitata durante la creazione di un nuovo set di scalabilità usando il cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Questo script di esempio illustra la creazione di un set di scalabilità e delle risorse associate usando il file di configurazione: [Creare un set di scalabilità di macchine virtuali completo.](./scripts/powershell-sample-create-complete-scale-set.md) È possibile configurare i criteri di ripristino automatico delle istanze aggiungendo i parametri *EnableAutomaticRepair* e *AutomaticRepairGracePeriod* all'oggetto di configurazione per la creazione del set di scalabilità. L'esempio seguente abilita la funzionalità con un periodo di tolleranza di 30 minuti.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

L'esempio seguente abilita i criteri di ripristino automatici durante la creazione di un nuovo set di scalabilità usando *[az vmss create](/cli/azure/vmss#az_vmss_create)*. Creare prima un gruppo di risorse, quindi creare un nuovo set di scalabilità con periodo di tolleranza dei criteri di ripristino automatico impostato su 30 minuti.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

L'esempio precedente usa un servizio di bilanciamento del carico esistente e un probe di integrità per il monitoraggio dello stato di integrità delle istanze dell'applicazione. Se invece si preferisce usare un'estensione per l'integrità dell'applicazione per il monitoraggio, è possibile creare un set di scalabilità, configurare l'estensione per l'integrità dell'applicazione e quindi abilitare i criteri di ripristino automatico dell'istanza usando *az vmss update*, come illustrato nella sezione successiva.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Abilitazione dei criteri di ripristino automatici durante l'aggiornamento di un set di scalabilità esistente

Prima di abilitare i criteri di ripristino automatici [](#requirements-for-using-automatic-instance-repairs) in un set di scalabilità esistente, assicurarsi che siano soddisfatti tutti i requisiti per acconsentire esplicitamente a questa funzionalità. L'endpoint applicazione deve essere configurato correttamente per le istanze del set di scalabilità per evitare di attivare riparazioni impreviste mentre l'endpoint viene configurato. Per abilitare il ripristino automatico dell'istanza in un set di scalabilità, *usare l'oggetto automaticRepairsPolicy* nel modello del set di scalabilità di macchine virtuali.

Dopo aver aggiornato il modello di un set di scalabilità esistente, assicurarsi che il modello più recente sia applicato a tutte le istanze della scalabilità. Vedere le istruzioni su come portare le macchine virtuali aggiornate con il [modello di set di scalabilità più recente.](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-portal"></a>Portale di Azure

È possibile modificare i criteri di ripristino automatici di un set di scalabilità esistente tramite il portale di Azure. 
 
1. Passare a un set di scalabilità di macchine virtuali esistente.
1. In **Impostazioni** nel menu a sinistra selezionare **Integrità e ripristino.**
1. Abilitare **l'opzione Monitorare l'integrità dell'applicazione.**
1. Individuare la **sezione Criteri di ripristino** automatico.
1. Attivare **l'opzione** **Riparazioni** automatiche.
1. In **Periodo di tolleranza (min)** specificare il periodo di tolleranza in minuti. I valori consentiti sono compresi tra 30 e 90 minuti. 
1. Al termine, selezionare **Salva**. 

### <a name="rest-api"></a>API REST

L'esempio seguente abilita i criteri con periodo di tolleranza di 40 minuti. Usare l'API versione 2018-10-01 o successiva.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Usare il cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) per modificare la configurazione della funzionalità di ripristino automatico dell'istanza in un set di scalabilità esistente. Nell'esempio seguente il periodo di tolleranza viene aggiornato a 40 minuti.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Di seguito è riportato un esempio per aggiornare i criteri di ripristino automatico delle istanze di un set di scalabilità esistente usando *[az vmss update](/cli/azure/vmss#az_vmss_update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Visualizzazione e aggiornamento dello stato del servizio dei criteri di riparazione automatica delle istanze

### <a name="rest-api"></a>API REST 

Usare [Get Instance View](/rest/api/compute/virtualmachinescalesets/getinstanceview) con API versione 2019-12-01 o successiva per il set di scalabilità di macchine virtuali per visualizzare *serviceState* per le riparazione automatiche nella proprietà *orchestrationServices*. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Usare *l'API setOrchestrationServiceState* con l'API versione 2019-12-01 o successiva in un set di scalabilità di macchine virtuali per impostare lo stato delle riparazione automatiche. Una volta che il set di scalabilità è stato acconsentito esplicitamente alla funzionalità di ripristino automatico, è possibile usare questa API per sospendere o riprendere le riparazione automatiche per il set di scalabilità. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure 

Usare il cmdlet [get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view) per visualizzare *serviceState per* il ripristino automatico dell'istanza. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Usare il cmdlet [set-orchestration-service-state](/cli/azure/vmss#az_vmss_set_orchestration_service_state) per aggiornare *serviceState per* il ripristino automatico dell'istanza. Dopo che il set di scalabilità ha acconsentito esplicitamente alla funzionalità di ripristino automatico, è possibile usare questo cmdlet per sospendere o riprendere le riposizioni automatiche per il set di scalabilità. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Usare il cmdlet [Get-AzVmss con](/powershell/module/az.compute/get-azvmss) il parametro *InstanceView* per visualizzare *ServiceState per* il ripristino automatico dell'istanza.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Usare Set-AzVmssOrchestrationServiceState cmdlet per aggiornare *serviceState per* il ripristino automatico dell'istanza. Dopo che il set di scalabilità ha acconsentito esplicitamente alla funzionalità di ripristino automatico, è possibile usare questo cmdlet per sospendere o riprendere le riprendimenti automatici per il set di scalabilità.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Risolvere problemi

**Non è stato possibile abilitare i criteri di ripristino automatici**

Se viene visualizzato un errore "BadRequest" con il messaggio "Impossibile trovare il membro 'automaticRepairsPolicy' nell'oggetto di tipo 'properties'", controllare la versione dell'API usata per il set di scalabilità di macchine virtuali. Per questa funzionalità è necessaria l'API versione 2018-10-01 o successiva.

**L'istanza non viene ripristinata anche quando i criteri sono abilitati**

L'istanza potrebbe essere nel periodo di tolleranza. Si tratta della quantità di tempo di attesa dopo qualsiasi modifica dello stato nell'istanza prima di eseguire le riparazioni. Ciò consente di evitare riparazioni prematura o accidentali. L'azione di ripristino deve essere eseguita dopo il completamento del periodo di tolleranza per l'istanza.

**Visualizzazione dello stato di integrità dell'applicazione per le istanze del set di scalabilità**

È possibile usare [l'API Get Instance View](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) per le istanze in un set di scalabilità di macchine virtuali per visualizzare lo stato di integrità dell'applicazione. Con Azure PowerShell, è possibile usare il cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) con il flag *-InstanceView.* Lo stato di integrità dell'applicazione viene fornito nella proprietà *vmHealth*.

Nel portale di Azure è possibile visualizzare anche lo stato di integrità. Passare a un set  di scalabilità esistente, selezionare Istanze  dal menu a sinistra e cercare nella colonna Stato integrità lo stato di integrità di ogni istanza del set di scalabilità. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare [l'estensione Integrità applicazioni o](./virtual-machine-scale-sets-health-extension.md) i probe di integrità del servizio di [bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md) per i set di scalabilità.
