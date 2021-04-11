---
title: Modalità di orchestrazione per i set di scalabilità di macchine virtuali in Azure
description: Informazioni su come usare le modalità di orchestrazione flessibili e uniformi per i set di scalabilità di macchine virtuali in Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 316f97480dfe7ee6481ef88faf265051a92024da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937261"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Anteprima: modalità di orchestrazione per i set di scalabilità di macchine virtuali in Azure 

I set di scalabilità di macchine virtuali offrono un raggruppamento logico di macchine virtuali gestite dalla piattaforma. Con i set di scalabilità è possibile creare un modello di configurazione della macchina virtuale, aggiungere o rimuovere automaticamente istanze aggiuntive in base al carico della CPU o della memoria e aggiornarsi automaticamente alla versione più recente del sistema operativo. Tradizionalmente, i set di scalabilità consentono di creare macchine virtuali usando un modello di configurazione della macchina virtuale fornito al momento della creazione del set di scalabilità e il set di scalabilità può gestire solo le macchine virtuali create in modo implicito in base al modello di configurazione. 

Le modalità di orchestrazione del set di scalabilità consentono un maggiore controllo sulle modalità di gestione delle istanze di macchine virtuali da parte del set di scalabilità. 

> [!IMPORTANT]
> La modalità di orchestrazione viene definita quando si crea il set di scalabilità e non può essere modificata o aggiornata in un secondo momento.


## <a name="scale-sets-with-uniform-orchestration"></a>Set di scalabilità con orchestrazione uniforme
Ottimizzato per carichi di lavoro senza stato su larga scala con istanze identiche.

I set di scalabilità di macchine virtuali con orchestrazione uniforme usano un profilo o un modello di macchina virtuale per aumentare la capacità desiderata. Sebbene sia possibile gestire o personalizzare le singole istanze di macchine virtuali, l'uniforme USA istanze di VM identiche. Le singole istanze di VM uniformi vengono esposte tramite i comandi API della macchina virtuale del set di scalabilità Le singole istanze non sono compatibili con i comandi dell'API della macchina virtuale IaaS di Azure standard, le funzionalità di gestione di Azure, ad esempio Azure Resource Manager le autorizzazioni RBAC, backup di Azure o Azure Site Recovery. L'orchestrazione uniforme fornisce garanzie di disponibilità elevata del dominio di errore se configurate con meno di 100 istanze. L'orchestrazione uniforme è disponibile a livello generale e supporta una gamma completa di orchestrazioni e gestione dei set di scalabilità, tra cui la scalabilità automatica basata sulle metriche, la protezione dell'istanza e gli aggiornamenti automatici del sistema operativo 


## <a name="scale-sets-with-flexible-orchestration"></a>Set di scalabilità con orchestrazione flessibile 
Ottenere una disponibilità elevata su larga scala con tipi di macchina virtuale identici o multipli.

Grazie all'orchestrazione flessibile, Azure offre un'esperienza unificata nell'ecosistema di macchine virtuali di Azure. L'orchestrazione flessibile offre garanzie di disponibilità elevata (fino a 1000 VM) distribuendo le macchine virtuali tra domini di errore in un'area o in una zona di disponibilità. Questo consente di scalare in orizzontale l'applicazione mantenendo l'isolamento del dominio di errore essenziale per l'esecuzione di carichi di lavoro con stato o basato su quorum, tra cui:
- Carichi di lavoro basati su quorum
- Database Open-Source
- Applicazioni con stato
- Servizi che richiedono disponibilità elevata e scalabilità elevata
- Servizi che vogliono combinare i tipi di macchina virtuale o sfruttare le VM spot e on demand insieme
- Applicazioni del set di disponibilità esistente  

> [!IMPORTANT]
> I set di scalabilità di macchine virtuali in modalità di orchestrazione flessibile sono attualmente in anteprima pubblica. Per usare la funzionalità di anteprima pubblica descritta di seguito, è necessaria una procedura di consenso esplicito.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Cosa è cambiato con la modalità di orchestrazione flessibile?
Uno dei principali vantaggi dell'orchestrazione flessibile è che fornisce funzionalità di orchestrazione sulle VM IaaS di Azure standard, anziché sulle macchine virtuali figlio del set di scalabilità. Ciò significa che è possibile usare tutte le API standard della VM durante la gestione di istanze di orchestrazione flessibili, anziché le API della VM del set di scalabilità di macchine virtuali usate con un'orchestrazione uniforme. Durante il periodo di anteprima, esistono diverse differenze tra la gestione delle istanze in un'orchestrazione flessibile rispetto all'orchestrazione uniforme. In generale, quando possibile, si consiglia di usare le API di VM IaaS di Azure standard. In questa sezione vengono illustrati esempi di procedure consigliate per la gestione delle istanze di macchine virtuali con un'orchestrazione flessibile.  

### <a name="assign-fault-domain-during-vm-creation"></a>Assegnare un dominio di errore durante la creazione della macchina virtuale
È possibile scegliere il numero di domini di errore per il set di scalabilità dell'orchestrazione flessibile. Per impostazione predefinita, quando si aggiunge una macchina virtuale a un set di scalabilità flessibile, Azure distribuisce uniformemente le istanze tra domini di errore. Sebbene sia consigliabile consentire ad Azure di assegnare il dominio di errore, per scenari avanzati o per la risoluzione dei problemi è possibile eseguire l'override di questo comportamento predefinito e specificare il dominio di errore in cui verrà visualizzata l'istanza.

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Denominazione istanza 
Quando si crea una macchina virtuale e la si aggiunge a un set di scalabilità flessibile, si ha il controllo completo sui nomi delle istanze nelle regole della convenzione di denominazione di Azure. Quando le macchine virtuali vengono aggiunte automaticamente al set di scalabilità tramite la scalabilità automatica, viene fornito un prefisso e Azure aggiunge un numero univoco alla fine del nome.

### <a name="query-instances-for-power-state"></a>Eseguire query sulle istanze per lo stato di alimentazione
Il metodo preferito consiste nell'usare Azure Resource Graph per eseguire una query per tutte le macchine virtuali in un set di scalabilità di macchine virtuali. Azure Resource Graph offre funzionalità di query efficienti per le risorse di Azure su larga scala tra le sottoscrizioni. 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

L'esecuzione di query sulle risorse con [Azure Resource Graph](../governance/resource-graph/overview.md) è un modo pratico ed efficiente per eseguire query sulle risorse di Azure e ridurre al minimo le chiamate API al provider di risorse. Il grafico delle risorse di Azure è una cache coerente in cui le risorse nuove o aggiornate potrebbero non essere riflesse per un massimo di 60 secondi. È possibile:
- Elencare le macchine virtuali in un gruppo di risorse o una sottoscrizione.
- Usare l'opzione Espandi per recuperare la visualizzazione dell'istanza, ovvero l'assegnazione di un dominio di errore, gli Stati di alimentazione e di provisioning, per tutte le macchine virtuali nella sottoscrizione.
- Usare l'API Get VM e i comandi per ottenere la visualizzazione del modello e dell'istanza per una singola istanza.

### <a name="scale-sets-vm-batch-operations"></a>Set di scalabilità di macchine virtuali operazioni batch
Usare i comandi standard della VM per avviare, arrestare, riavviare, eliminare le istanze, anziché le API della VM del set di scalabilità di macchine virtuali. Le operazioni batch della VM del set di scalabilità di macchine virtuali (Start All, stop all, Reimage all e così via) non vengono usate con la modalità di orchestrazione flessibile. 

### <a name="monitor-application-health"></a>Monitorare l'integrità delle applicazioni 
Il monitoraggio dell'integrità dell'applicazione consente all'applicazione di fornire ad Azure un heartbeat per determinare se l'applicazione è integra o non integra. Azure può sostituire automaticamente le istanze di VM non integre. Per le istanze di set di scalabilità flessibili, è necessario installare e configurare l'estensione per l'integrità dell'applicazione nella macchina virtuale. Per le istanze del set di scalabilità uniforme, è possibile usare l'estensione per l'integrità dell'applicazione o misurare lo stato di integrità con un probe di integrità Azure Load Balancer personalizzato. 

### <a name="list-scale-sets-vm-api-changes"></a>Elencare i set di scalabilità delle API delle macchine virtuali 
I set di scalabilità di macchine virtuali consentono di elencare le istanze che appartengono al set di scalabilità. Con un'orchestrazione flessibile, il comando elenca set di scalabilità di macchine virtuali macchina virtuale fornisce un elenco di set di scalabilità di ID VM. È quindi possibile chiamare i comandi per ottenere i set di scalabilità di macchine virtuali per ottenere altri dettagli sull'uso del set di scalabilità con l'istanza di macchina virtuale. Per ottenere i dettagli completi della macchina virtuale, usare i comandi standard GET VM o [Azure Resource Graph](../governance/resource-graph/overview.md). 

### <a name="retrieve-boot-diagnostics-data"></a>Recuperare i dati di diagnostica di avvio 
Usare le API e i comandi standard della VM per recuperare le schermate e i dati di diagnostica di avvio dell'istanza. I comandi e le API di diagnostica di avvio della macchina virtuale per i set di scalabilità di macchine virtuali non vengono usati con istanze in modalità orchestrazione

### <a name="vm-extensions"></a>Estensioni di VM 
Usare le estensioni destinate alle macchine virtuali standard, anziché le estensioni destinate alle istanze in modalità di orchestrazione uniformi.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Confronto tra set flessibili, uniformi e di disponibilità 
Nella tabella seguente vengono confrontati la modalità di orchestrazione flessibile, la modalità di orchestrazione uniforme e i set di disponibilità in base alle relative funzionalità.

| Funzionalità | Supportato da orchestrazione flessibile (anteprima) | Supportato da orchestrazione uniforme (disponibilità generale) | Supportato da AvSets (disponibilità generale) |
|-|-|-|-|
|         Tipo di macchina virtuale  | VM IaaS di Azure standard (Microsoft. Compute/VirtualMachines)  | Macchine virtuali specifiche del set di scalabilità (Microsoft. Compute/virtualmachinescalesets/VirtualMachines)  | VM IaaS di Azure standard (Microsoft. Compute/VirtualMachines)  |
|         SKU supportati  |            Serie D, serie E, serie F, serie, serie B, Intel, AMD  |            Tutti gli SKU  |            Tutti gli SKU  |
|         Zone di disponibilità  |            Facoltativamente, specificare tutte le istanze in una singola zona di disponibilità |            Specificare le istanze in una zona di disponibilità di 1, 2 o 3  |            Non supportato  |
|         Controllo completo su VM, nic e dischi  |            Sì  |            Controllo limitato con l'API VM dei set di scalabilità di macchine virtuali  |            Sì  |
|         Ridimensionamento automatico  |            No  |            Sì  |            No  |
|         Assegnare una macchina virtuale a un dominio di errore specifico  |            Sì  |             No   |            No  |
|         Rimuovere schede di rete e dischi durante l'eliminazione di istanze di VM  |            No  |            Sì  |            No  |
|         Criteri di aggiornamento (set di scalabilità di macchine virtuali) |            No  |            Automatico, in sequenza, manuale  |            N/D  |
|         Aggiornamenti automatici del sistema operativo (set di scalabilità di macchine virtuali) |            No  |            Sì  |            N/D  |
|         Applicazione di patch di sicurezza Guest  |            Sì  |            No  |            Sì  |
|         Notifiche di terminazione (set di scalabilità di VM) |            No  |            Sì  |            N/D  |
|         Ripristino dell'istanza (set di scalabilità di macchine virtuali) |            No  |            Sì   |            N/D  |
|         Rete accelerata  |            Sì  |            Sì  |            Sì  |
|         Individuare le istanze e i prezzi   |            Sì, è possibile avere entrambe le istanze spot e Regular Priority  |            Sì, le istanze devono essere tutte di tipo spot o all Regular  |            No, solo istanze con priorità normale  |
|         Combinare i sistemi operativi  |            Sì, Linux e Windows possono risiedere nello stesso set di scalabilità flessibile |            No, le istanze sono dello stesso sistema operativo  |               Sì, Linux e Windows possono risiedere nello stesso set di scalabilità flessibile |
|         Monitorare l'integrità dell'applicazione  |            Estensione di integrità dell'applicazione  |            Estensione di integrità dell'applicazione o Probe di bilanciamento del carico di Azure  |            Estensione di integrità dell'applicazione  |
|         Dischi UltraSSD   |            Sì  |            Sì, solo per le distribuzioni di zona  |            No  |
|         Infiniband   |            No  |            Sì, solo gruppo di posizionamento singolo  |            Sì  |
|         acceleratore di scrittura   |            No  |            Sì  |            Sì  |
|         Gruppi di posizionamento di prossimità   |            Sì  |            Sì  |            Sì  |
|         Host dedicati di Azure   |            No  |            Sì  |            Sì  |
|         SLB di base   |            No  |            Sì  |            Sì  |
|         SKU standard Azure Load Balancer |            Sì  |            Sì  |            Sì  |
|         Gateway applicazione  |            No  |            Sì  |            Sì  |
|         Controllo manutenzione   |            No  |            Sì  |            Sì  |
|         Elencare le macchine virtuali nel set  |            Sì  |            Sì  |            Sì, elenca le VM in AvSet  |
|         Avvisi di Azure  |            No  |            Sì  |            Sì  |
|         Informazioni dettagliate macchina virtuale  |            No  |            Sì  |            Sì  |
|         Backup di Azure  |            Sì  |            Sì  |            Sì  |
|         Azure Site Recovery  |     No  |            No  |            Sì  |
|         Aggiungi/Rimuovi macchina virtuale esistente al gruppo  |            No  |            No  |            No  | 


## <a name="register-for-flexible-orchestration-mode"></a>Registra per la modalità di orchestrazione flessibile
Prima di poter distribuire set di scalabilità di macchine virtuali in modalità di orchestrazione flessibile, è necessario prima registrare la sottoscrizione per la funzionalità di anteprima. Il completamento della registrazione potrebbe richiedere diversi minuti. Per eseguire la registrazione, è possibile usare i comandi seguenti Azure PowerShell o CLI di Azure.

### <a name="azure-portal"></a>Portale di Azure
Passare alla pagina dei dettagli per la sottoscrizione in cui si vuole creare un set di scalabilità in modalità di orchestrazione flessibile e selezionare funzionalità di anteprima nel menu. Selezionare le due funzioni dell'agente di orchestrazione da abilitare: _VMOrchestratorSingleFD_ e _VMOrchestratorMultiFD_ e quindi fare clic sul pulsante Register (registra). La registrazione delle funzionalità può richiedere fino a 15 minuti.

![Registrazione delle funzionalità.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

Una volta registrate le funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo. Passare alla scheda provider di risorse per la sottoscrizione, selezionare Microsoft. Compute, quindi fare clic su Registra di nuovo.

![Ripetere la registrazione](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell 
Usare il cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) per abilitare l'anteprima per la sottoscrizione. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0 
Usare [AZ feature Register](/cli/azure/feature#az-feature-register) per abilitare l'anteprima per la sottoscrizione. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Introduzione alla modalità di orchestrazione flessibile

Introduzione alla modalità di orchestrazione flessibile per i set di scalabilità tramite il portale di Azure, l'interfaccia della riga di comando di Azure, la bonifica o l'API REST.

### <a name="azure-portal"></a>Portale di Azure

Creare un set di scalabilità di macchine virtuali in modalità di orchestrazione flessibile tramite il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra di ricerca cercare e selezionare set di **scalabilità di macchine virtuali**. 
1. Selezionare **Crea** nella pagina **set di scalabilità di macchine virtuali** .
1. Nella pagina **creare un set di scalabilità di macchine virtuali** , vedere la sezione **orchestrazione** .
1. Per la **modalità di orchestrazione**, selezionare l'opzione **flessibile** .
1. Impostare il **numero di domini di errore**.
1. Terminare la creazione del set di scalabilità. Per altre informazioni su come creare un set di scalabilità, vedere [creare un set di scalabilità nel portale di Azure](quick-create-portal.md#create-virtual-machine-scale-set) .

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Modalità di orchestrazione nel portale durante la creazione di un set di scalabilità":::

Successivamente, aggiungere una macchina virtuale al set di scalabilità in modalità orchestrazione flessibile.

1. Nella barra di ricerca cercare e selezionare **macchine virtuali**.
1. Selezionare **Aggiungi** nella pagina **macchine virtuali** .
1. Nella scheda informazioni di **base** visualizzare la sezione **Dettagli istanza** .
1. Aggiungere la macchina virtuale al set di scalabilità in modalità orchestrazione flessibile selezionando il set di scalabilità nelle **Opzioni di disponibilità**. È possibile aggiungere la macchina virtuale a un set di scalabilità nella stessa area, area e gruppo di risorse.
1. Completare la creazione della macchina virtuale. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Aggiungere una macchina virtuale al set di scalabilità in modalità di orchestrazione flessibile":::


### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Creare un set di scalabilità di macchine virtuali flessibile con la CLI di Azure. Nell'esempio seguente viene illustrata la creazione di un set di scalabilità flessibile in cui il numero di domini di errore è impostato su 3, viene creata una macchina virtuale che viene quindi aggiunta al set di scalabilità flessibile. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Creare un set di scalabilità di macchine virtuali flessibile con bonifica. Questo processo richiede la **bonifica del provider Azurerm v 2.15.0** o versione successiva. Si notino i parametri seguenti:
- Quando non viene specificata alcuna zona, `platform_fault_domain_count` può essere 1, 2 o 3 a seconda dell'area.
- Quando si specifica una zona, `the fault domain count` può essere 1.
- `single_placement_group` il parametro deve essere `false` per i set di scalabilità di macchine virtuali flessibili.
- Se si sta eseguendo una distribuzione a livello di area, non è necessario specificare `zones` .

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>API REST

1. Creare un set di scalabilità vuoto. I parametri seguenti sono obbligatori:
    - API versione 2019-12-01 (o versione successiva) 
    - Il gruppo di posizionamento singolo deve essere `false` quando si crea un set di scalabilità flessibile

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. Aggiungere le macchine virtuali al set di scalabilità.
    1. Assegnare la `virtualMachineScaleSet` proprietà al set di scalabilità creato in precedenza. È necessario specificare la `virtualMachineScaleSet` proprietà al momento della creazione della macchina virtuale. 
    1. È possibile usare la funzione di modello **Copy ()** Azure Resource Manager per creare più macchine virtuali contemporaneamente. Vedere [iterazione delle risorse](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) nei modelli di Azure Resource Manager. 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

Per un esempio completo, vedere [Guida introduttiva di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) .


## <a name="frequently-asked-questions"></a>Domande frequenti

**Qual è la scalabilità supportata da un'orchestrazione flessibile?**

È possibile aggiungere fino a 1000 VM a un set di scalabilità in modalità di orchestrazione flessibile.

**In che modo la disponibilità con un'orchestrazione flessibile si confronta con i set di disponibilità o l'orchestrazione uniforme**

|   | Orchestrazione flessibile  | Orchestrazione uniforme  | SET DI DISPONIBILITÀ  |
|-|-|-|-|
| Distribuisci tra zone di disponibilità  | No  | Sì  | No  |
| Garanzia di disponibilità del dominio di errore all'interno di un'area  | Sì, le istanze fino a 1000 possono essere distribuite in un massimo di 3 domini di errore nell'area. Il numero massimo di domini di errore varia in base all'area  | Sì, fino a 100 istanze  | Sì, fino a 200 istanze  |
| Gruppi di posizionamento  | La modalità flessibile usa sempre più gruppi di posizionamento (singlePlacementGroup = false)  | È possibile scegliere un singolo gruppo di posizionamento o più gruppi di posizionamento | N/D  |
| Domini di aggiornamento  | Nessuno, gli aggiornamenti di manutenzione o host sono stati eseguiti in un dominio di errore per dominio di errore  | Fino a 5 domini di aggiornamento  | Fino a 20 domini di aggiornamento  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Risolvere i problemi dei set di scalabilità con orchestrazione flessibile
Trovare la soluzione corretta per lo scenario di risoluzione dei problemi. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Motivo:** La sottoscrizione non è registrata per l'anteprima pubblica della modalità di orchestrazione flessibile. 

**Soluzione:** Seguire le istruzioni riportate sopra per eseguire la registrazione per l'anteprima pubblica della modalità di orchestrazione flessibile. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Motivo:** Il `platformFaultDomainCount` parametro non è valido per l'area o la zona selezionata. 

**Soluzione:** È necessario selezionare un `platformFaultDomainCount` valore valido. Per le distribuzioni di zona, il `platformFaultDomainCount` valore massimo è 1. Per le distribuzioni a livello di area in cui non è specificata alcuna zona, il valore massimo `platformFaultDomainCount` varia a seconda dell'area. Vedere [gestire la disponibilità delle macchine virtuali per gli script](../virtual-machines/availability.md) per determinare il numero massimo di domini di errore per area. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Motivo:** Tentativo di eliminazione di un set di scalabilità in modalità di orchestrazione flessibile associata a una o più macchine virtuali. 

**Soluzione:** Eliminare tutte le macchine virtuali associate al set di scalabilità in modalità di orchestrazione flessibile, quindi è possibile eliminare il set di scalabilità.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Motivo:** La sottoscrizione è registrata per l'anteprima in modalità orchestrazione flessibile. Tuttavia, il `singlePlacementGroup` parametro è impostato su *true*. 

**Soluzione:** L'oggetto `singlePlacementGroup` deve essere impostato su *false*. 


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Informazioni su come distribuire l'applicazione nel set di scalabilità.](virtual-machine-scale-sets-deploy-app.md)