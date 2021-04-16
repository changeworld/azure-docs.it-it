---
title: Modalità di orchestrazione per i set di scalabilità di macchine virtuali in Azure
description: Informazioni su come usare le modalità di orchestrazione flessibile e uniforme per i set di scalabilità di macchine virtuali in Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 72e36a942eeaea00699f346db99a7ca3503495da
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481651"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Anteprima: Modalità di orchestrazione per i set di scalabilità di macchine virtuali in Azure 

I set di scalabilità di macchine virtuali forniscono un raggruppamento logico di macchine virtuali gestite dalla piattaforma. Con i set di scalabilità si crea un modello di configurazione della macchina virtuale, si aggiungono o rimuovono automaticamente istanze aggiuntive in base al carico di CPU o memoria e si esegue automaticamente l'aggiornamento alla versione più recente del sistema operativo. In genere, i set di scalabilità consentono di creare macchine virtuali usando un modello di configurazione della macchina virtuale fornito al momento della creazione del set di scalabilità e il set di scalabilità può gestire solo le macchine virtuali create in modo implicito in base al modello di configurazione. 

Le modalità di orchestrazione del set di scalabilità consentono di avere un maggiore controllo sulla modalità di gestione delle istanze di macchina virtuale da parte del set di scalabilità. 

> [!IMPORTANT]
> La modalità di orchestrazione viene definita quando si crea il set di scalabilità e non può essere modificata o aggiornata in un secondo momento.


## <a name="scale-sets-with-uniform-orchestration"></a>Set di scalabilità con orchestrazione uniforme
Ottimizzato per carichi di lavoro senza stato su larga scala con istanze identiche.

I set di scalabilità di macchine virtuali con orchestrazione uniforme usano un profilo o un modello di macchina virtuale per aumentare la capacità desiderata. Sebbene sia possibile gestire o personalizzare singole istanze di macchina virtuale, Uniform usa istanze di macchina virtuale identiche. Le singole istanze di VM Uniform vengono esposte tramite i comandi API vm del set di scalabilità di macchine virtuali. Le singole istanze non sono compatibili con i comandi standard dell'API VM IaaS di Azure, le funzionalità di gestione di Azure, ad esempio le autorizzazioni per il controllo degli accessi in base al ruolo delle risorse Azure Resource Manager, Backup di Azure o Azure Site Recovery. L'orchestrazione uniforme offre garanzie di disponibilità elevata del dominio di errore se configurate con meno di 100 istanze. L'orchestrazione uniforme è disponibile a livello generale e supporta una gamma completa di gestione e orchestrazione dei set di scalabilità, tra cui scalabilità automatica basata su metriche, protezione delle istanze e aggiornamenti automatici del sistema operativo. 


## <a name="scale-sets-with-flexible-orchestration"></a>Set di scalabilità con orchestrazione flessibile 
Ottenere disponibilità elevata su larga scala con tipi di macchine virtuali identici o multipli.

Con l'orchestrazione flessibile, Azure offre un'esperienza unificata nell'ecosistema di macchine virtuali di Azure. L'orchestrazione flessibile offre garanzie di disponibilità elevata (fino a 1000 macchine virtuali) tramite la distribuzione di macchine virtuali tra domini di errore in un'area o all'interno di una zona di disponibilità. Ciò consente di scalare orizzontalmente l'applicazione mantenendo l'isolamento del dominio di errore essenziale per l'esecuzione di carichi di lavoro basati su quorum o con stato, tra cui:
- Carichi di lavoro basati su quorum
- Open-Source database
- Applicazioni con stato
- Servizi che richiedono disponibilità elevata e scalabilità elevata
- Servizi che vogliono combinare tipi di macchine virtuali o sfruttare le macchine virtuali spot e su richiesta insieme
- Applicazioni del set di disponibilità esistenti  

> [!IMPORTANT]
> I set di scalabilità di macchine virtuali in modalità di orchestrazione flessibile sono attualmente in anteprima pubblica. Per usare la funzionalità di anteprima pubblica descritta di seguito, è necessaria una procedura di consenso esplicito.
> Questa versione di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Cosa è cambiato con la modalità di orchestrazione flessibile?
Uno dei principali vantaggi dell'orchestrazione flessibile è che offre funzionalità di orchestrazione rispetto alle macchine virtuali IaaS di Azure standard, anziché alle macchine virtuali figlio del set di scalabilità. Ciò significa che è possibile usare tutte le API vm standard durante la gestione di istanze di orchestrazione flessibili, anziché le API vm del set di scalabilità di macchine virtuali usate con l'orchestrazione uniforme. Durante il periodo di anteprima, esistono diverse differenze tra la gestione delle istanze nell'orchestrazione flessibile e l'orchestrazione uniforme. In generale, è consigliabile usare le API standard delle macchine virtuali IaaS di Azure, quando possibile. In questa sezione vengono evidenziati esempi di procedure consigliate per la gestione delle istanze di macchina virtuale con orchestrazione flessibile.  

### <a name="assign-fault-domain-during-vm-creation"></a>Assegnare il dominio di errore durante la creazione della macchina virtuale
È possibile scegliere il numero di domini di errore per il set di scalabilità di orchestrazione flessibile. Per impostazione predefinita, quando si aggiunge una macchina virtuale a un set di scalabilità flessibile, Azure riparte uniformemente le istanze tra domini di errore. Anche se è consigliabile consentire ad Azure di assegnare il dominio di errore, per scenari avanzati o di risoluzione dei problemi è possibile eseguire l'override di questo comportamento predefinito e specificare il dominio di errore in cui l'istanza verrà atterrato.

```azurecli-interactive 
az vm create â€“vmss "myVMSS"  â€“-platform_fault_domain 1
```

### <a name="instance-naming"></a>Denominazione delle istanze 
Quando si crea una macchina virtuale e la si aggiunge a un set di scalabilità flessibile, si ha il controllo completo sui nomi delle istanze all'interno delle regole della convenzione di denominazione di Azure. Quando le macchine virtuali vengono aggiunte automaticamente al set di scalabilità tramite la scalabilità automatica, si specifica un prefisso e Azure aggiunge un numero univoco alla fine del nome.

### <a name="query-instances-for-power-state"></a>Eseguire query su istanze per lo stato di alimentazione
Il metodo preferito è usare Azure Resource Graph per eseguire query per tutte le macchine virtuali in un set di scalabilità di macchine virtuali. Azure Resource Graph offre funzionalità di query efficienti per le risorse di Azure su larga scala tra le sottoscrizioni. 

``` 
|â€¯whereâ€¯typeâ€¯=~â€¯'Microsoft.Compute/virtualMachines' 
|â€¯whereâ€¯properties.virtualMachineScaleSetâ€¯containsâ€¯"demo" 
|â€¯extendâ€¯powerStateâ€¯=â€¯properties.extended.instanceView.powerState.code 
|â€¯projectâ€¯name,â€¯resourceGroup,â€¯location,â€¯powerState 
|â€¯orderâ€¯byâ€¯resourceGroupâ€¯desc,â€¯nameâ€¯desc 
```

L'esecuzione di query sulle [Azure Resource Graph](../governance/resource-graph/overview.md) è un modo pratico ed efficiente per eseguire query sulle risorse di Azure e ridurre al minimo le chiamate API al provider di risorse. Azure Resource Graph è una cache coerente alla fine in cui le risorse nuove o aggiornate potrebbero non essere riflesse per un massimo di 60 secondi. È possibile:
- Elencare le macchine virtuali in un gruppo di risorse o in una sottoscrizione.
- Usare l'opzione expand per recuperare la visualizzazione dell'istanza (assegnazione del dominio di errore, stati di alimentazione e provisioning) per tutte le macchine virtuali nella sottoscrizione.
- Usare l'API Get VM e i comandi per ottenere la visualizzazione del modello e dell'istanza per una singola istanza.

### <a name="scale-sets-vm-batch-operations"></a>Operazioni batch per set di scalabilità di macchine virtuali
Usare i comandi della macchina virtuale standard per avviare, arrestare, riavviare ed eliminare istanze anziché le API delle macchine virtuali del set di scalabilità di macchine virtuali. Le operazioni batch delle macchine virtuali del set di scalabilità di macchine virtuali (avvia tutto, arresta tutto, ricrea l'immagine di tutti e così via) non vengono usate con la modalità di orchestrazione flessibile. 

### <a name="monitor-application-health"></a>Monitorare l'integrità delle applicazioni 
Il monitoraggio dell'integrità delle applicazioni consente all'applicazione di fornire ad Azure un heartbeat per determinare se l'applicazione è integra o non integra. Azure può sostituire automaticamente le istanze di macchine virtuali non integre. Per le istanze del set di scalabilità flessibile, è necessario installare e configurare l'estensione integrità applicazione nella macchina virtuale. Per le istanze di set di scalabilità uniformi, è possibile usare l'estensione integrità dell'applicazione o misurare l'integrità con Azure Load Balancer probe di integrità personalizzato. 

### <a name="list-scale-sets-vm-api-changes"></a>Elencare le modifiche all'API vm dei set di scalabilità 
I set di scalabilità di macchine virtuali consentono di elencare le istanze che appartengono al set di scalabilità. Con l'orchestrazione flessibile, il comando elenca le MACCHINE VIRTUALI dei set di scalabilità di macchine virtuali fornisce un elenco di ID macchina virtuale dei set di scalabilità. È quindi possibile chiamare i comandi GET della macchina virtuale per i set di scalabilità di macchine virtuali per ottenere altri dettagli sul funzionamento del set di scalabilità con l'istanza di macchina virtuale. Per ottenere i dettagli completi della macchina virtuale, usare i comandi get vm standard [o Azure Resource Graph](../governance/resource-graph/overview.md). 

### <a name="retrieve-boot-diagnostics-data"></a>Recuperare i dati di diagnostica di avvio 
Usare le API e i comandi standard della macchina virtuale per recuperare i dati e gli screenshot di Diagnostica di avvio dell'istanza. Le API e i comandi di diagnostica di avvio delle macchine virtuali dei set di scalabilità di macchine virtuali non vengono usati con istanze della modalità di orchestrazione flessibile.

### <a name="vm-extensions"></a>Estensioni di VM 
Usare estensioni destinate a macchine virtuali standard, anziché estensioni destinate a istanze uniformi della modalità di orchestrazione.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Confronto tra set di disponibilità flessibili, uniformi e 
La tabella seguente confronta la modalità di orchestrazione flessibile, la modalità di orchestrazione uniforme e i set di disponibilità in base alle relative funzionalità.

| Funzionalità | Supportato dall'orchestrazione flessibile (anteprima) | Supportato dall'orchestrazione uniforme (disponibilità generale) | Supportato da AvSet (disponibilità generale) |
|-|-|-|-|
|         Tipo di macchina virtuale  | Macchina virtuale IaaS standard di Azure (Microsoft.compute /virtualmachines)  | Macchine virtuali specifiche del set di scalabilità (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | Macchina virtuale IaaS standard di Azure (Microsoft.compute /virtualmachines)  |
|         SKU supportati  |            Serie D, serie E, serie F, serie A, serie B, Intel, AMD  |            Tutti gli SKU  |            Tutti gli SKU  |
|         Zone di disponibilità  |            Facoltativamente, specificare tutte le istanze in una singola zona di disponibilità |            Specificare le istanze tra 1, 2 o 3 zone di disponibilità  |            Non supportato  |
|         Controllo completo su VM, schede di interfaccia di rete, dischi  |            Sì  |            Controllo limitato con l'API VM dei set di scalabilità di macchine virtuali  |            Sì  |
|         Ridimensionamento automatico  |            No  |            Sì  |            No  |
|         Assegnare la macchina virtuale a un dominio di errore specifico  |            Sì  |             No   |            No  |
|         Rimuovere schede di interfaccia di rete e dischi durante l'eliminazione di istanze di macchine virtuali  |            No  |            Sì  |            No  |
|         Criteri di aggiornamento (set di scalabilità di macchine virtuali) |            No  |            Automatico, In sequenza, Manuale  |            N/D  |
|         Aggiornamenti automatici del sistema operativo (set di scalabilità di macchine virtuali) |            No  |            Sì  |            N/D  |
|         Applicazione di patch per la sicurezza guest  |            Sì  |            No  |            Sì  |
|         Notifiche di terminazione (set di scalabilità di macchine virtuali) |            No  |            Sì  |            N/D  |
|         Ripristino dell'istanza (set di scalabilità di macchine virtuali) |            No  |            Sì   |            N/D  |
|         Rete accelerata  |            Sì  |            Sì  |            Sì  |
|         Spotâ€ ̄ instances e pricingâ€ ̄  |            Sì, è possibile avere istanze con priorità spot e regolare  |            Sì, le istanze devono essere tutte spot o tutte regolari  |            No, solo istanze con priorità normale  |
|         Combinazione di sistemi operativi  |            Sì, Linux e Windows possono risiedere nello stesso set di scalabilità flessibile |            No, le istanze sono lo stesso sistema operativo  |               Sì, Linux e Windows possono risiedere nello stesso set di scalabilità flessibile |
|         Monitorare l'integrità dell'applicazione  |            Estensione per l'integrità dell'applicazione  |            Estensione per l'integrità dell'applicazione o probe di Azure Load Balancer  |            Estensione per l'integrità dell'applicazione  |
|         UltraSSDâ€ ̄ Disksâ€ ̄  |            Sì  |            Sì, solo per le distribuzioni di zona  |            No  |
|         Infinibandâ€ ̄  |            No  |            Sì, solo gruppo di posizionamento singolo  |            Sì  |
|         Writeâ€ ̄ Acceleratorâ€ ̄  |            No  |            Sì  |            Sì  |
|         Proximityâ€ ̄ Placement Groupsâ€ ̄  |            Sì  |            Sì  |            Sì  |
|         Host dedicati di Azure ̄  |            No  |            Sì  |            Sì  |
|         Basic SLBâ€ ̄  |            No  |            Sì  |            Sì  |
|         Azure Load Balancer Standard SKU |            Sì  |            Sì  |            Sì  |
|         Gateway applicazione  |            No  |            Sì  |            Sì  |
|         Controllo di manutenzione ̄  |            No  |            Sì  |            Sì  |
|         Elencare le macchine virtuali nel set  |            Sì  |            Sì  |            Sì, elencare le macchine virtuali in AvSet  |
|         Avvisi di Azure  |            No  |            Sì  |            Sì  |
|         Informazioni dettagliate macchina virtuale  |            No  |            Sì  |            Sì  |
|         Backup di Azure  |            Sì  |            Sì  |            Sì  |
|         Azure Site Recovery  |     No  |            No  |            Sì  |
|         Aggiungere/rimuovere una macchina virtuale esistente al gruppo  |            No  |            No  |            No  | 


## <a name="register-for-flexible-orchestration-mode"></a>Registrarsi per la modalità di orchestrazione flessibile
Prima di poter distribuire i set di scalabilità di macchine virtuali in modalità orchestrazione flessibile, è necessario registrare la sottoscrizione per la funzionalità di anteprima. Il completamento della registrazione potrebbe richiedere alcuni minuti. È possibile usare i comandi seguenti per Azure PowerShell o l'interfaccia della riga di comando di Azure per la registrazione.

### <a name="azure-portal"></a>Portale di Azure
Passare alla pagina dei dettagli per la sottoscrizione in cui si vuole creare un set di scalabilità in modalità orchestrazione flessibile e selezionare Funzionalità di anteprima dal menu. Selezionare le due funzionalità dell'agente di orchestrazione da abilitare: _VMOrchestratorSingleFD_ e _VMOrchestratorMultiFD_ e fare clic sul pulsante Registra. La registrazione delle funzionalità può richiedere fino a 15 minuti.

![Registrazione della funzionalità.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

Dopo aver registrato le funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo. Passare alla scheda Provider di risorse per la sottoscrizione, selezionare Microsoft.compute e fare clic su Riregistra.

![Eseguire di nuovo la registrazione](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


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

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0 
Usare [az feature register per](/cli/azure/feature#az-feature-register) abilitare l'anteprima per la sottoscrizione. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Introduzione alla modalità di orchestrazione flessibile

Introduzione alla modalità di orchestrazione flessibile per i set di scalabilità tramite l'portale di Azure, l'interfaccia della riga di comando di Azure, Terraform o l'API REST.

### <a name="azure-portal"></a>Portale di Azure

Creare un set di scalabilità di macchine virtuali in modalità orchestrazione flessibile tramite il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra di ricerca cercare e selezionare Set **di scalabilità di macchine virtuali**. 
1. Selezionare **Crea** nella **pagina Set di scalabilità di macchine** virtuali.
1. Nella pagina **Crea un set di scalabilità di macchine** virtuali visualizzare la sezione **Orchestrazione.**
1. Per **Modalità orchestrazione** selezionare **l'opzione Flessibile.**
1. Impostare il **numero di domini di errore**.
1. Completare la creazione del set di scalabilità. Per altre informazioni su come creare un set di [scalabilità, portale di Azure](quick-create-portal.md#create-virtual-machine-scale-set) creare un set di scalabilità.

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Modalità di orchestrazione nel portale durante la creazione di un set di scalabilità":::

Aggiungere quindi una macchina virtuale al set di scalabilità in modalità orchestrazione flessibile.

1. Nella barra di ricerca cercare e selezionare **Macchine virtuali**.
1. Selezionare **Aggiungi** nella **pagina Macchine** virtuali.
1. Nella scheda **Informazioni di base** visualizzare la sezione Dettagli **istanza.**
1. Aggiungere la macchina virtuale al set di scalabilità in modalità orchestrazione flessibile selezionando il set di scalabilità nelle **opzioni di disponibilità**. È possibile aggiungere la macchina virtuale a un set di scalabilità nella stessa area, zona e gruppo di risorse.
1. Completare la creazione della macchina virtuale. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Aggiungere una macchina virtuale al set di scalabilità in modalità orchestrazione flessibile":::


### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Creare un set di scalabilità di macchine virtuali flessibile con l'interfaccia della riga di comando di Azure. L'esempio seguente illustra la creazione di un set di scalabilità flessibile in cui il numero di domini di errore è impostato su 3, viene creata una macchina virtuale e quindi aggiunta al set di scalabilità flessibile. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Creare un set di scalabilità di macchine virtuali flessibile con Terraform. Questo processo richiede **il provider Azurerm Terraform v2.15.0** o versione successiva. Si notino i parametri seguenti:
- Se non viene specificata alcuna zona, `platform_fault_domain_count` può essere 1, 2 o 3 a seconda dell'area.
- Quando viene specificata una zona, `the fault domain count` può essere 1.
- `single_placement_group` il parametro deve essere `false` per set di scalabilità di macchine virtuali flessibili.
- Se si sta eseguendo una distribuzione a livello di regione, non è necessario specificare `zones` .

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
    - API versione 2019-12-01 (o successiva) 
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

2. Aggiungere macchine virtuali al set di scalabilità.
    1. Assegnare `virtualMachineScaleSet` la proprietà al set di scalabilità creato in precedenza. È necessario specificare la proprietà `virtualMachineScaleSet` al momento della creazione della macchina virtuale. 
    1. È possibile usare la funzione Azure Resource Manager modello **copy()** per creare più macchine virtuali contemporaneamente. Vedere [Iterazione delle risorse](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) nei Azure Resource Manager di lavoro. 

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

Per [un esempio completo, vedere](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) Avvio rapido di Azure.


## <a name="frequently-asked-questions"></a>Domande frequenti

**Quale scalabilità supporta l'orchestrazione flessibile?**

È possibile aggiungere fino a 1000 macchine virtuali a un set di scalabilità in modalità orchestrazione flessibile.

**In che modo la disponibilità con l'orchestrazione flessibile viene confrontata con i set di disponibilità o l'orchestrazione uniforme?**

| Attributo di disponibilità  | Orchestrazione flessibile  | Orchestrazione uniforme  | SET DI DISPONIBILITÀ  |
|-|-|-|-|
| Distribuire tra zone di disponibilità  | No  | Sì  | No  |
| Garanzie di disponibilità del dominio di errore all'interno di un'area  | Sì, è possibile distribuire fino a 1000 istanze in un massimo di 3 domini di errore nell'area. Il numero massimo di domini di errore varia in base all'area  | Sì, fino a 100 istanze  | Sì, fino a 200 istanze  |
| Gruppi di posizionamento  | La modalità flessibile usa sempre più gruppi di posizionamento (singlePlacementGroup = false)  | È possibile scegliere Gruppo di posizionamento singolo o Più gruppi di posizionamento | N/D  |
| Domini di aggiornamento  | None, maintenance or host updates are done fault domain by fault domain  | Fino a 5 domini di aggiornamento  | Fino a 20 domini di aggiornamento  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Risolvere i problemi dei set di scalabilità con l'orchestrazione flessibile
Trovare la soluzione ideale per lo scenario di risoluzione dei problemi. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Causa:** La sottoscrizione non è registrata per l'anteprima pubblica della modalità di orchestrazione flessibile. 

**Soluzione:** Seguire le istruzioni precedenti per registrarsi per l'anteprima pubblica della modalità di orchestrazione flessibile. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Causa:** Il `platformFaultDomainCount` parametro non è valido per l'area o la zona selezionata. 

**Soluzione:** È necessario selezionare un valore `platformFaultDomainCount` valido. Per le distribuzioni di zona, il `platformFaultDomainCount` valore massimo è 1. Per le distribuzioni a livello di area in cui non è specificata alcuna zona, il valore `platformFaultDomainCount` massimo varia a seconda dell'area. Vedere [Gestire la disponibilità delle macchine virtuali per gli script per](../virtual-machines/availability.md) determinare il numero massimo di domini di errore per area. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Causa:** Tentativo di eliminare un set di scalabilità in modalità orchestrazione flessibile associata a una o più macchine virtuali. 

**Soluzione:** Eliminare tutte le macchine virtuali associate al set di scalabilità in modalità orchestrazione flessibile, quindi è possibile eliminare il set di scalabilità.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Causa:** La sottoscrizione viene registrata per l'anteprima della modalità orchestrazione flessibile. Tuttavia, il `singlePlacementGroup` parametro è impostato su *True.* 

**Soluzione:** `singlePlacementGroup`L'oggetto deve essere impostato su *False.* 


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Informazioni su come distribuire l'applicazione nel set di scalabilità.](virtual-machine-scale-sets-deploy-app.md)
