---
title: Eseguire la migrazione di VM VMware ad Azure (senza agente) - PowerShell
description: Informazioni su come eseguire una migrazione senza agente di VM VMware con Azure Migrate tramite PowerShell.
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 03/02/2021
ms.openlocfilehash: 24dd33495915a9f4d47a00fbbfe9e894df839d4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715072"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>Eseguire la migrazione di VM VMware ad Azure (senza agente) - PowerShell

Questo articolo illustra come eseguire la migrazione di macchine virtuali VMware individuate con il metodo senza agente usando Azure PowerShell per [Azure Migrate: Migrazione server](migrate-services-overview.md#azure-migrate-server-migration-tool).

Si apprenderà come:

> [!div class="checklist"]
> * Recuperare le VM VMware individuate in un progetto di Azure Migrate.
> * Avviare la replica delle VM.
> * Aggiornare le proprietà per la replica delle VM.
> * Monitorare la replica.
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto.
> * Eseguire una migrazione completa delle VM.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Le esercitazioni usano le opzioni predefinite, laddove possibile, e non visualizzano tutte le impostazioni e i percorsi possibili.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="1-prerequisites"></a>1. Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

1. Completare l' [esercitazione: individuare le macchine virtuali VMware con server Assessment](tutorial-discover-vmware.md) per preparare Azure e VMware per la migrazione.
2. Completare l' [esercitazione: valutare le macchine virtuali VMware per la migrazione alle VM di Azure](./tutorial-assess-vmware-azure-vm.md) prima di eseguirne la migrazione ad Azure.
3. [Installare il modulo AZ PowerShell](/powershell/azure/install-az-ps)

## <a name="2-install-azure-migrate-powershell-module"></a>2. installare Azure Migrate modulo PowerShell

Il modulo Azure Migrate PowerShell è disponibile come parte di Azure PowerShell ( `Az` ). Eseguire il `Get-InstalledModule -Name Az.Migrate` comando per verificare se il modulo Azure migrate PowerShell è installato nel computer.  

## <a name="3-sign-in-to-your-microsoft-azure-subscription"></a>3. accedere alla sottoscrizione di Microsoft Azure

Accedere alla sottoscrizione di Azure con il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-your-azure-subscription"></a>Selezionare la sottoscrizione ad Azure

Usare il cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) per ottenere l'elenco delle sottoscrizioni di Azure a cui si ha accesso. Selezionare la sottoscrizione di Azure con il progetto Azure Migrate da usare con il cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="4-retrieve-the-azure-migrate-project"></a>4. recuperare il progetto di Azure Migrate

Il progetto Azure Migrate viene usato per archiviare i metadati di individuazione, valutazione e migrazione raccolti dall'ambiente da valutare o trasferire.
In un progetto è possibile tenere traccia degli asset individuati, orchestrare le valutazioni ed eseguire le migrazioni.

Come parte dei prerequisiti, sarà già stato creato un progetto Azure Migrate. Usare il cmdlet [Get-AzMigrateProject](/powershell/module/az.migrate/get-azmigrateproject) per recuperare i dettagli di un progetto Azure migrate. È necessario specificare il nome del progetto Azure Migrate (`Name`) e il nome del relativo gruppo di risorse (`ResourceGroupName`).

```azurepowershell-interactive
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name MyResourceGroup

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name MyMigrateProject -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
Write-Output $MigrateProject
```

## <a name="5-retrieve-discovered-vms-in-an-azure-migrate-project"></a>5. recuperare le macchine virtuali individuate in un progetto Azure Migrate

Azure Migrate si basa su un'[appliance leggera di Azure Migrate](migrate-appliance-architecture.md). Come parte dei prerequisiti, l'appliance di Azure Migrate sarà già stata distribuita come VM VMware.

Per recuperare una VM VMware specifica in un progetto Azure Migrate, specificare il nome del progetto Azure Migrate (`ProjectName`), il relativo gruppo di risorse (`ResourceGroupName`) e il nome della macchina virtuale (`DisplayName`).


```azurepowershell-interactive
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName MyTestVM | Format-Table DisplayName, Name, Type

# View discovered server details
Write-Output $DiscoveredServer
```

In questa esercitazione verrà eseguita la migrazione di questa VM.

È anche possibile recuperare tutte le macchine virtuali VMware in un progetto Azure Migrate usando i `ProjectName` parametri () e ( `ResourceGroupName` ).

```azurepowershell-interactive
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```

Se si dispone di più appliance in un progetto di Azure migrate, è possibile usare `ProjectName` i parametri (), ( `ResourceGroupName` ) e ( `ApplianceName` ) per recuperare tutte le macchine virtuali individuate con una specifica Azure migrate Appliance.

```azurepowershell-interactive
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName MyMigrateAppliance

```

## <a name="6-initialize-replication-infrastructure"></a>6. inizializzare l'infrastruttura di replica

[Azure Migrate: Migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) sfrutta più risorse di Azure per la migrazione delle macchine virtuali. Migrazione del server effettua il provisioning delle risorse seguenti nello stesso gruppo di risorse del progetto.

- **Bus di servizio**: Migrazione del server usa il bus di servizio per inviare i messaggi di orchestrazione della replica all'appliance.
- **Account di archiviazione del gateway**: lo strumento Migrazione server usa l'account di archiviazione del gateway per archiviare le informazioni sullo stato delle VM da replicare.
- **Account di archiviazione di log**: l'appliance di Azure Migrate carica i log di replica per le VM in un account di archiviazione di log. Azure Migrate applica le informazioni di replica ai dischi gestiti dalla replica.
- **Insieme di credenziali delle chiavi**: l'appliance di Azure Migrate usa l'insieme di credenziali delle chiavi per gestire le stringhe di connessione per il bus di servizio e le chiavi di accesso per gli account di archiviazione usati nella replica.

Prima di replicare la prima VM nel progetto Azure Migrate, eseguire il comando seguente per effettuare il provisioning dell'infrastruttura di replica. Questo comando esegue il provisioning e configura le risorse menzionate in modo da poter iniziare la migrazione delle macchine virtuali VMware.

> [!NOTE]
> Un progetto Azure Migrate supporta le migrazioni solo in un'unica area di Azure. Una volta eseguito questo script, non è possibile cambiare l'area di destinazione della migrazione delle VM VMware.
> È necessario eseguire il `Initialize-AzMigrateReplicationInfrastructure` comando se si configura una nuova appliance nel progetto Azure migrate.

Nell'articolo verrà inizializzata l'infrastruttura di replica in modo che sia possibile eseguire la migrazione delle VM nell'area `Central US`.

```azurepowershell-interactive
# Initialize replication infrastructure for the current Migrate project
Initialize-AzMigrateReplicationInfrastructure -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject. Name -Scenario agentlessVMware -TargetRegion "CentralUS" 

```

## <a name="7-replicate-vms"></a>7. replicare le macchine virtuali

Dopo aver completato l'individuazione e l'inizializzazione dell'infrastruttura di replica, è possibile avviare la replica di macchine virtuali VMware ad Azure. È possibile eseguire contemporaneamente fino a 500 repliche.

È possibile specificare le proprietà di replica come indicato di seguito.

- **Sottoscrizione e gruppo di risorse di destinazione** : specificare la sottoscrizione e il gruppo di risorse a cui deve essere eseguita la migrazione della macchina virtuale fornendo l'ID del gruppo di risorse usando il `TargetResourceGroupId` parametro ().
- **Rete virtuale di destinazione e subnet** : specificare l'ID della rete virtuale di Azure e il nome della subnet a cui deve essere eseguita la migrazione della macchina virtuale usando `TargetNetworkId` rispettivamente i parametri () e () `TargetSubnetName` .
- **Nome macchina virtuale di destinazione** : specificare il nome della macchina virtuale di Azure da creare usando il `TargetVMName` parametro ().
- **Dimensioni macchina virtuale di destinazione** : specificare le dimensioni della macchina virtuale di Azure da usare per la macchina virtuale di replica tramite il `TargetVMSize` parametro (). Ad esempio, per eseguire la migrazione di una macchina virtuale a D2_v2 macchina virtuale in Azure, specificare il valore per ( `TargetVMSize` ) come "Standard_D2_v2".
- **Licenza** : per usare vantaggio Azure Hybrid per i computer Windows Server coperti con le sottoscrizioni di Software Assurance o Windows Server attive, specificare il valore per il `LicenseType` parametro () come **WindowsServer**. In caso contrario, specificare il valore per il `LicenseType` parametro () come "NoLicenseType".
- **Disco del sistema operativo**: specificare l'identificatore univoco del disco che contiene il bootloader e il programma di installazione del sistema operativo. L'ID disco da usare è la proprietà identificatore univoco (UUID) per il disco recuperato tramite il cmdlet [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) .
- **Tipo di disco** : specificare il valore per il `DiskType` parametro () come indicato di seguito.
    - Per usare i dischi gestiti Premium, specificare "Premium_LRS" come valore per il `DiskType` parametro ().
    - Per usare dischi SSD standard, specificare "StandardSSD_LRS" come valore per il `DiskType` parametro ().
    - Per usare dischi HDD standard, specificare "Standard_LRS" come valore per il `DiskType` parametro ().
- **Ridondanza dell'infrastruttura**: specificare l'opzione di ridondanza dell'infrastruttura come indicato di seguito.
    - Zona di disponibilità per aggiungere la macchina migrata a una zona di disponibilità specifica nell'area. Usare questa opzione per distribuire i server che formano un livello applicazione a più nodi tra zone di disponibilità. Questa opzione è disponibile solo se l'area di destinazione selezionata per la migrazione supporta le zone di disponibilità. Per usare le zone di disponibilità, specificare il valore della zona di disponibilità per il `TargetAvailabilityZone` parametro ().
    - Set di disponibilità per inserire la macchina migrata in un set di disponibilità. Per usare questa opzione, il gruppo di risorse di destinazione selezionato deve avere uno o più set di disponibilità. Per usare il set di disponibilità, specificare l'ID del set di disponibilità per il `TargetAvailabilitySet` parametro ().
 - **Account di archiviazione di diagnostica di avvio** : per usare un account di archiviazione di diagnostica di avvio, specificare l'ID per il `TargetBootDiagnosticStorageAccount` parametro ().
    -  L'account di archiviazione usato per la diagnostica di avvio deve trovarsi nella stessa sottoscrizione in cui si sta eseguendo la migrazione delle macchine virtuali.  
    - Per impostazione predefinita, non viene impostato alcun valore per questo parametro. 

### <a name="replicate-vms-with-all-disks"></a>Replicare le VM con tutti i dischi

In questa esercitazione verranno replicati tutti i dischi della macchina virtuale individuata e verrà specificato un nuovo nome per la macchina virtuale in Azure. Il primo disco del server individuato viene specificato come disco del sistema operativo e viene eseguita la migrazione di tutti i dischi come HDD Standard. È il disco che contiene il bootloader e il programma di installazione del sistema operativo. Il cmdlet restituisce un processo che può essere rilevato per monitorare lo stato dell'operazione.

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType Standard_LRS -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>Replicare VM con dischi selezionati

È anche possibile replicare selettivamente i dischi della macchina virtuale individuata usando il cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) e fornendo tale valore come input al `DiskToInclude` parametro () nel cmdlet [New-AzMigrateServerReplication](/powershell/module/az.migrate/new-azmigrateserverreplication) . È anche possibile usare il cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) per specificare tipi di dischi di destinazione diversi per ogni singolo disco da replicare.

Specificare i valori per i seguenti parametri del cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) .

- **DiskId**: specificare l'identificatore univoco del disco di cui eseguire la migrazione. L'ID disco da usare è la proprietà identificatore univoco (UUID) per il disco recuperato tramite il cmdlet [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) .
- **IsOSDisk**: specificare "true" se il disco di cui eseguire la migrazione è il disco del sistema operativo della VM; in caso contrario, "false".
- **DiskType**: specificare il tipo di disco da usare in Azure.

Nell'esempio seguente verranno replicati solo due dischi della macchina virtuale individuata. Si specificherà il disco del sistema operativo e si utilizzeranno tipi di dischi diversi per ogni disco da replicare. Il cmdlet restituisce un processo di cui è possibile tenere traccia per monitorare lo stato dell'operazione.

```azurepowershell-interactive
# View disk details of the discovered server
Write-Output $DiscoveredServer.Disk

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType StandardSSD_LRS -IsOSDisk true
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType Premium_LRS -IsOSDisk false

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="8-monitor-replication"></a>8. monitorare la replica

La replica avviene nel modo indicato di seguito:

- Al termine del processo, viene avviata la replica iniziale delle macchine virtuali in Azure.
- Durante la replica iniziale viene creato uno snapshot della VM. I dati dei dischi dello snapshot vengono replicati nei dischi gestiti di replica in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi locali vengono replicate periodicamente nei dischi di replica in Azure.

Tenere traccia dello stato della replica utilizzando il cmdlet [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) .



```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM

# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

È possibile tenere traccia delle proprietà della **Descrizione** dello **stato** della migrazione e della migrazione nell'output.

- Per la replica iniziale, i valori per le proprietà **stato migrazione** e **Descrizione stato migrazione** saranno `InitialSeedingInProgress` `Initial replication` rispettivamente e.
- Durante delta replication, i valori per le proprietà della **Descrizione** **dello stato della** migrazione e dello stato della migrazione saranno `Replicating` `Ready to migrate` rispettivamente e.
- Al termine della migrazione, i **valori per le** proprietà della descrizione dello stato della migrazione e dello stato della **migrazione** saranno `Migration succeeded` `Migrated` rispettivamente e.

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

Per informazioni dettagliate sullo stato di avanzamento della replica, eseguire il cmdlet seguente.

```azurepowershell-interactive
Write-Output $replicatingserver.ProviderSpecificDetail
```

È possibile tenere traccia dello stato di avanzamento della replica iniziale usando le proprietà della **percentuale di avanzamento del seeding iniziale** nell'output.

```Output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

La replica avviene nel modo indicato di seguito:

- Al termine del processo, viene avviata la replica iniziale delle macchine virtuali in Azure.
- Durante la replica iniziale viene creato uno snapshot della VM. I dati dei dischi dello snapshot vengono replicati nei dischi gestiti di replica in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi locali vengono replicate periodicamente nei dischi di replica in Azure.

## <a name="9-retrieve-the-status-of-a-job"></a>9. recuperare lo stato di un processo

È possibile monitorare lo stato di un processo usando il cmdlet [Get-AzMigrateJob](/powershell/module/az.migrate/get-azmigratejob) .

```azurepowershell-interactive
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="10-update-properties-of-a-replicating-vm"></a>10. aggiornare le proprietà di una macchina virtuale di replica

[Azure Migrate: Migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) consente di cambiare le proprietà di destinazione, ad esempio nome, dimensioni, gruppo di risorse, configurazione della scheda di interfaccia di retee così via, per una macchina virtuale di replica.

Per una VM è possibile aggiornare le proprietà seguenti.

- **Nome macchina virtuale** : specificare il nome della macchina virtuale di Azure da creare usando il `TargetVMName` parametro [].
- **Dimensioni macchina virtuale** : specificare le dimensioni della macchina virtuale di Azure da usare per la macchina virtuale di replica tramite il `TargetVMSize` parametro []. Ad esempio, per eseguire la migrazione di una macchina virtuale a D2_v2 macchina virtuale in Azure, specificare il valore per [ `TargetVMSize` ] come `Standard_D2_v2` .
- **Rete virtuale** : specificare l'ID della rete virtuale di Azure a cui deve essere eseguita la migrazione della macchina virtuale usando il `TargetNetworkId` parametro [].
- **Gruppo di risorse** : specificare l'ID del gruppo di risorse a cui deve essere eseguita la migrazione della macchina virtuale, fornendo l'ID del gruppo di risorse usando il `TargetResourceGroupId` parametro [].
- **Interfaccia di rete** : la configurazione NIC può essere specificata usando il cmdlet [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping) . All'oggetto viene quindi passato un input al parametro [ `NicToUpdate` ] nel cmdlet [set-AzMigrateServerReplication](/powershell/module/az.migrate/set-azmigrateserverreplication) .

    - **Modificare l'allocazione IP** : per specificare un indirizzo IP statico per una scheda di interfaccia di rete, fornire l'indirizzo IPv4 da usare come IP statico per la macchina virtuale usando il `TargetNicIP` parametro []. Per assegnare dinamicamente un indirizzo IP per una scheda di interfaccia di rete, fornire `auto` come valore per il parametro **TargetNicIP** .
    - Usare Values `Primary` `Secondary` oppure `DoNotCreate` per il `TargetNicSelectionType` parametro [] per specificare se la scheda di interfaccia di rete deve essere primaria, secondaria o non deve essere creata nella macchina virtuale migrata. Per la VM è possibile specificare un'unica scheda di interfaccia di rete come primaria.
    - Per impostare la scheda di interfaccia di rete come primaria, è anche necessario specificare altre schede che dovranno essere impostate come secondarie o che non dovranno essere create nella VM di cui eseguire la migrazione.
    - Per modificare la subnet per la scheda di interfaccia di rete, specificare il nome della subnet utilizzando il `TargetNicSubnet` parametro [].

 - **Zona di disponibilità** : per usare le zone di disponibilità, specificare il valore della zona di disponibilità per il `TargetAvailabilityZone` parametro [].
 - **Set di disponibilità** : per usare il set di disponibilità, specificare l'ID del set di disponibilità per il `TargetAvailabilitySet` parametro [].

Il cmdlet [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) restituisce un processo che può essere rilevato per monitorare lo stato dell'operazione.

```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM

# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```

Nell'esempio seguente la configurazione della scheda di interfaccia di rete verrà aggiornata impostando la prima come primaria e assegnandole un indirizzo IP statico. La seconda scheda di interfaccia di rete verrà rimossa per la migrazione e verranno aggiornati il nome e le dimensioni della VM.

```azurepowershell-interactive
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP ###.###.###.### -TargetNicSelectionType Primary
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType DoNotCreate

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize Standard_DS13_v2 -TargetVMName MyMigratedVM -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq 'InProgress') -or ($UpdateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $UpdateJob.State
```



## <a name="11-run-a-test-migration"></a>11. eseguire una migrazione di test

All'avvio della replica differenziale, è possibile eseguire una migrazione di test per le VM prima di eseguire una migrazione completa ad Azure. È consigliabile farlo almeno una volta per ogni macchina virtuale, prima di eseguirne la migrazione. Il cmdlet restituisce un processo di cui è possibile tenere traccia per monitorare lo stato dell'operazione.

- L'esecuzione di una migrazione di test verifica che tutto funzioni come previsto. La migrazione di test non ha alcun effetto sul computer locale, che rimane operativo e continua a eseguire la replica.
- Il test simula la migrazione creando una VM di Azure con dati replicati, in genere eseguendo la migrazione a un rete virtuale non di produzione nella sottoscrizione di Azure.
- È possibile usare la VM di Azure di test replicata per convalidare la migrazione, eseguire test delle app e risolvere eventuali problemi prima della migrazione completa.

Selezionare la rete virtuale di Azure da usare per il test specificando l'ID della rete virtuale usando il `TestNetworkID` parametro [].

```azurepowershell-interactive
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq 'InProgress') -or ($TestMigrationJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $TestMigrationJob.State
```

Al termine del test, pulire la migrazione di test usando il cmdlet [Start-AzMigrateTestMigrationCleanup](/powershell/module/az.migrate/start-azmigratetestmigrationcleanup) . Il cmdlet restituisce un processo che può essere rilevato per monitorare lo stato dell'operazione.

```azurepowershell-interactive
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $CleanupTestMigrationJob.State
```

## <a name="12-migrate-vms"></a>12. eseguire la migrazione di macchine virtuali

Dopo aver verificato che la migrazione di test funzioni nel modo previsto, è possibile procedere con la migrazione del server di replica usando il cmdlet seguente. Il cmdlet restituisce un processo che può essere rilevato per monitorare lo stato dell'operazione.

Se non si vuole disattivare il server di origine, non usare il `TurnOffSourceServer` parametro [].

```azurepowershell-interactive
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="13-complete-the-migration"></a>13. completare la migrazione

1. Al termine della migrazione, arrestare la replica per il computer locale e pulire le informazioni sullo stato di replica per la macchina virtuale usando il cmdlet seguente. Il cmdlet restituisce un processo che può essere rilevato per monitorare lo stato dell'operazione.

   ```azurepowershell-interactive
   # Stop replication for a migrated server
   $StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer

   # Track job status to check for completion
   while (($StopReplicationJob.State -eq 'InProgress') -or ($StopReplicationJob.State -eq 'NotStarted')){
           #If the job hasn't completed, sleep for 10 seconds before checking the job status again
           sleep 10;
           $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
   }
   # Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
   Write-Output $StopReplicationJob.State
   ```

1. Se nel computer è presente il sistema operativo Linux, installare l'agente [Linux](../virtual-machines/extensions/agent-linux.md) nelle macchine virtuali di cui è stata eseguita la migrazione. Per le macchine virtuali Windows l'agente di macchine virtuali viene installato automaticamente durante la migrazione.
1. Apportare nell'app le eventuali modifiche post-migrazione necessarie, come l'aggiornamento delle stringhe di connessione del database e delle configurazioni dei server Web.
1. Eseguire i test di accettazione della migrazione e dell'applicazione finale sull'applicazione migrata ora in esecuzione in Azure.
1. Trasferire il traffico all'istanza della VM di Azure di cui è stata eseguita la migrazione.
1. Rimuovere le macchine virtuali locali dall'inventario delle macchine virtuali locale.
1. Rimuovere le macchine virtuali locali dai processi di backup locali.
1. Aggiornare la documentazione interna con la nuova posizione e il nuovo indirizzo IP delle macchine virtuali di Azure.

## <a name="14-post-migration-best-practices"></a>14. procedure consigliate di post-migrazione

- Per una maggiore resilienza:
    - Proteggere i dati eseguendo il backup delle macchine virtuali di Azure con il servizio Backup di Azure. [Altre informazioni](../backup/quick-backup-vm-portal.md)
    - Mantenere i carichi di lavoro in esecuzione e sempre disponibili eseguendo la replica delle macchine virtuali di Azure in un'area secondaria con Site Recovery. [Altre informazioni](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Per una maggiore sicurezza:
    - Bloccare e limitare l'accesso del traffico in ingresso con la funzionalità di [amministrazione JIT del Centro sicurezza di Azure](../security-center/security-center-just-in-time.md).
    - Limitare il traffico di rete verso gli endpoint di gestione con la funzionalità [Gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md).
    - Distribuire [Crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md) per garantire la sicurezza dei dischi e proteggere i dati da furti e accessi non autorizzati.
    - Per altre informazioni sulla [protezione delle risorse IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), visitare il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/).
- Per il monitoraggio e la gestione:
-  È consigliabile distribuire [Gestione costi di Azure](../cost-management-billing/cloudyn/overview.md) per monitorare l'utilizzo delle risorse e le spese.
