---
title: Replicare le macchine virtuali di Azure in esecuzione nei gruppi di posizionamento di prossimità
description: Informazioni su come replicare le macchine virtuali di Azure in esecuzione nei gruppi di posizionamento di prossimità usando Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: a58ec80c13ee9ae0eceb019ab2fd7909fd6f369b
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889281"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Replicare le macchine virtuali di Azure in esecuzione nei gruppi di posizionamento di prossimità in un'altra area

Questo articolo descrive come eseguire la replica, il failover e il failback di macchine virtuali in esecuzione in un gruppo di posizionamento di prossimità in un'area secondaria.

[Gruppi di posizionamento di prossimità](../virtual-machines/windows/proximity-placement-groups-portal.md) è una funzionalità di raggruppamento logico di macchine virtuali di Azure che è possibile usare per ridurre la latenza di rete tra le macchine virtuali associata alle applicazioni. Quando le macchine virtuali vengono distribuite nello stesso gruppo di posizionamento di prossimità, sono posizionate fisicamente il più vicino possibile l'una all'altra. I gruppi di posizionamento di prossimità sono particolarmente utili per soddisfare i requisiti dei carichi di lavoro sensibili alla latenza.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Ripristino di emergenza con i gruppi di posizionamento di prossimità

In uno scenario tipico, è possibile che le macchine virtuali siano in esecuzione in un gruppo di posizionamento di prossimità per evitare la latenza di rete tra i vari livelli dell'applicazione. Anche se questo fornisce all'applicazione una latenza di rete ottimale, si vogliono proteggere le applicazioni usando Site Recovery per qualsiasi errore a livello di area. Site Recovery replica i dati da un'area di Azure a un'altra e inserisce le macchine nell'area di ripristino di emergenza in caso di failover.

## <a name="considerations"></a>Considerazioni

- L'approccio migliore consiste nell'eseguire il failover/failback delle macchine virtuali in un gruppo di posizionamento di prossimità. Tuttavia, se la macchina virtuale non può essere inserita all'interno del posizionamento di prossimità durante il failover/failback, il failover/failback continuerà a verificarsi e le macchine virtuali verranno create all'esterno di un gruppo di posizionamento di prossimità.
- Se un set di disponibilità viene aggiunto a un gruppo di posizionamento di prossimità e durante il failover/failback le macchine virtuali nel set di disponibilità hanno un vincolo di allocazione, le macchine virtuali verranno create al di fuori del set di disponibilità e del gruppo di posizionamento di prossimità.
- Site Recovery per i gruppi di posizionamento di prossimità non è supportato per i dischi non gestiti.

> [!NOTE]
> Azure Site Recovery non supporta il failback da dischi gestiti per scenari da Hyper-V ad Azure. Di conseguenza, il failback dal gruppo di posizionamento di prossimità in Azure a Hyper-V non è supportato.

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-portal"></a>Configurare il ripristino di emergenza per le macchine virtuali nei gruppi di posizionamento vicini tramite il portale

### <a name="azure-to-azure-via-portal"></a>Da Azure ad Azure tramite il portale

È possibile scegliere di abilitare la replica per una macchina virtuale tramite la pagina di ripristino di emergenza della VM oppure passando a un insieme di credenziali creato in precedenza e passando alla sezione Site Recovery e quindi abilitando la replica. Di seguito viene illustrato come configurare Site Recovery per le macchine virtuali all'interno di un PPG tramite entrambi gli approcci:

- Come selezionare PPG nell'area di ripristino di emergenza abilitando la replica tramite il pannello di ripristino di emergenza della macchina virtuale IaaS:
  1. Passare alla macchina virtuale. Nel pannello laterale sinistro, in "Operations", selezionare "ripristino di emergenza"
  2. Nella scheda ' nozioni di base ' scegliere l'area di ripristino di emergenza in cui si vuole replicare la macchina virtuale. Vai a' impostazioni avanzate '
  3. Qui è possibile visualizzare il gruppo di posizionamento vicino della macchina virtuale e l'opzione per selezionare un PPG nell'area di ripristino di emergenza. Site Recovery offre inoltre la possibilità di usare un nuovo gruppo di posizionamento di prossimità creato automaticamente se si sceglie di usare questa opzione predefinita. È possibile scegliere il gruppo di posizionamento vicino desiderato, quindi passare a "verifica + Avvia replica" e infine abilitare la replica.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="Abilitare la replica":::.

- Come selezionare PPG nell'area di ripristino di emergenza durante l'abilitazione della replica tramite il pannello dell'insieme di credenziali:
  1. Passare all'insieme di credenziali dei servizi di ripristino e passare alla scheda Site Recovery
  2. Fare clic su "+ Abilita Site Recovery" e quindi selezionare "1: Abilita replica" in macchine virtuali di Azure (come si vuole replicare una VM di Azure)
  3. Compilare i campi obbligatori nella scheda ' origine ' e fare clic su' avanti '
  4. Selezionare l'elenco di macchine virtuali per cui si vuole abilitare la replica nella scheda ' macchine virtuali ' e fare clic su' avanti '
  5. Qui è possibile visualizzare l'opzione per selezionare un PPG nell'area di ripristino di emergenza. Site Recovery offre inoltre la possibilità di usare un nuovo PPG creato automaticamente se si sceglie di usare questa opzione predefinita. È possibile scegliere il PPG desiderato e quindi procedere con l'abilitazione della replica.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="Abilitare la replica tramite l'insieme di credenziali.":::

Si noti che è possibile aggiornare facilmente la selezione del PPG nell'area di ripristino di emergenza dopo che la replica è stata abilitata per la macchina virtuale.

1. Passare alla macchina virtuale e nel pannello sul lato sinistro, in "Operations", selezionare "ripristino di emergenza".
2. Passare al pannello "calcolo e rete" e fare clic su "modifica" nella parte superiore della pagina
3. È possibile visualizzare le opzioni per modificare più impostazioni di destinazione, incluso il PPG di destinazione. Scegliere il PPG in cui si desidera eseguire il failover della macchina virtuale e fare clic su' Salva '.

### <a name="vmware-to-azure-via-portal"></a>Da VMware ad Azure tramite il portale

Dopo aver abilitato la replica per la macchina virtuale, è possibile configurare il gruppo di posizionamento vicino alla macchina virtuale di destinazione. Assicurarsi di creare separatamente il PPG nell'area di destinazione in base alle esigenze. Successivamente, è possibile aggiornare facilmente la selezione del PPG nell'area di ripristino di emergenza dopo che la replica è stata abilitata per la macchina virtuale.

1. Selezionare la macchina virtuale dall'insieme di credenziali e nel pannello sul lato sinistro, in "Operations", selezionare "ripristino di emergenza".
2. Passare al pannello "calcolo e rete" e fare clic su "modifica" nella parte superiore della pagina
3. È possibile visualizzare le opzioni per modificare più impostazioni di destinazione, incluso il PPG di destinazione. Scegliere il PPG in cui si desidera eseguire il failover della macchina virtuale e fare clic su' Salva '.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="Aggiornamento V2A PPG":::

### <a name="hyper-v-to-azure-via-portal"></a>Da Hyper-V ad Azure tramite il portale

Dopo aver abilitato la replica per la macchina virtuale, è possibile configurare il gruppo di posizionamento vicino alla macchina virtuale di destinazione. Assicurarsi di creare separatamente il PPG nell'area di destinazione in base alle esigenze. Successivamente, è possibile aggiornare facilmente la selezione del PPG nell'area di ripristino di emergenza dopo che la replica è stata abilitata per la macchina virtuale.

1. Selezionare la macchina virtuale dall'insieme di credenziali e nel pannello sul lato sinistro, in "Operations", selezionare "ripristino di emergenza".
2. Passare al pannello "calcolo e rete" e fare clic su "modifica" nella parte superiore della pagina
3. È possibile visualizzare le opzioni per modificare più impostazioni di destinazione, incluso il PPG di destinazione. Scegliere il PPG in cui si desidera eseguire il failover della macchina virtuale e fare clic su' Salva '.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="Aggiornamento H2A PPG":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>Configurare il ripristino di emergenza per le macchine virtuali nei gruppi di posizionamento vicini tramite PowerShell

### <a name="prerequisites"></a>Prerequisiti 

1. Verificare che sia installato il modulo Az di Azure PowerShell. Se è necessario installare o aggiornare Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).
2. Il valore minimo Azure PowerShell AZ Version dovrebbe essere 4.1.0. Per controllare la versione corrente, usare il comando seguente:

    ```
    Get-InstalledModule -Name Az
    ```

### <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Configurare Site Recovery per le macchine virtuali nel gruppo di posizionamento di prossimità

> [!NOTE]
> Assicurarsi di avere a portata di mano l'ID univoco del gruppo di posizionamento di prossimità di destinazione. Se si sta creando un nuovo gruppo di posizionamento di prossimità, controllare il comando [qui](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) e, se si usa un gruppo di posizionamento di prossimità esistente, usare il comando [qui](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups).

### <a name="azure-to-azure"></a>Da Azure ad Azure

1. [Accedere](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription) al proprio account e impostare la sottoscrizione.
2. Ottenere i dettagli della macchina virtuale che si intende replicare come indicato [qui](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated).
3. [Creare](./azure-to-azure-powershell.md#create-a-recovery-services-vault) un insieme di credenziali di Servizi di ripristino e [impostare](./azure-to-azure-powershell.md#set-the-vault-context) il contesto dell'insieme di credenziali.
4. Preparare l'insieme di credenziali per avviare la replica della macchina virtuale. Ciò comporta la creazione di un [oggetto Service Fabric](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) sia per l'area primaria che per l'area di ripristino.
5. [Creare](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) un contenitore di protezione di Site Recovery sia per l'infrastruttura primaria che per l'infrastruttura di ripristino.
6. [Creare](./azure-to-azure-powershell.md#create-a-replication-policy) i criteri di replica.
7. Creare un mapping del contenitore di protezione tra il contenitore di protezione primario e quello di ripristino usando [questi](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) passaggi e un mapping del contenitore di protezione per il failback come indicato [qui](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. Creare un account di archiviazione della cache seguendo [questi](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account) passaggi.
9. Creare i mapping di rete necessari come indicato [qui](./azure-to-azure-powershell.md#create-network-mappings).
10. Per replicare la macchina virtuale di Azure con Managed disks, usare il cmdlet di PowerShell seguente-

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Make sure to replace the variables $datadiskName with data disk name.

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Quando si Abilita la replica per più dischi dati, usare il cmdlet di PowerShell seguente-

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig

#Data disk

# Add data disks
Foreach( $disk in $VM.StorageProfile.DataDisks)
{
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $dataDiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
    $diskconfigs += $DataDisk1ReplicationConfig
}

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Quando si Abilita la replica da zona a zona con PPG, il comando per avviare la replica verrà scambiato con il cmdlet di PowerShell-

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

Quando l'operazione di avvio della replica ha esito positivo, i dati della macchina virtuale vengono replicati nell'area di ripristino.

Il processo di replica inizia eseguendo il seeding di una copia dei dischi replicati della macchina virtuale nell'area di ripristino. Questa fase viene denominata fase di replica iniziale.

Al termine della replica iniziale, la replica passa alla fase di sincronizzazione differenziale. A questo punto la macchina virtuale è protetta ed è possibile eseguire un'operazione di failover di test. Al termine della replica iniziale, lo stato della replica dell'elemento replicato che rappresenta la macchina virtuale passa allo stato Protetto.

Per monitorare lo stato e l'integrità della replica della macchina virtuale, ottenere i dettagli dell'elemento della macchina virtuale protetto dalla replica.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Per eseguire un failover di test, convalidarlo ed eliminarlo, seguire [questi](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover) passaggi.
12. Per eseguire il failover, seguire i passaggi indicati [qui](./azure-to-azure-powershell.md#fail-over-to-azure).
13. Per eseguire la riprotezione e il failback nell'area di origine, usare il cmdlet di PowerShell seguente:

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. Per disabilitare la replica, seguire i passaggi indicati [qui](./azure-to-azure-powershell.md#disable-replication).

### <a name="vmware-to-azure-via-powershell"></a>Da VMware ad Azure tramite PowerShell

1. Assicurarsi di [preparare i server VMware locali](./vmware-azure-tutorial-prepare-on-premises.md) per il ripristino di emergenza in Azure.
2. Accedere al proprio account e impostare la sottoscrizione come specificato [qui](./vmware-azure-disaster-recovery-powershell.md#log-into-azure).
3. [Configurare](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault) un insieme di credenziali di Servizi di ripristino e [impostare il contesto dell'insieme di credenziali](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context).
4. [Convalidare](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration) la registrazione dell'insieme di credenziali.
5. [Creare](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy) i criteri di replica.
6. [Aggiungere](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) un server vCenter e individuare le macchine virtuali e [creare](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication) gli account di archiviazione per la replica.
7. Per replicare le macchine virtuali VMware, vedere qui i dettagli e usare il cmdlet di PowerShell seguente:

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. Per controllare lo stato di replica e l'integrità della replica della macchina virtuale, è possibile usare il cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. Configurare le impostazioni di failover seguendo i passaggi indicati [qui](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings).
10. [Eseguire](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover) un failover di test.
11. Eseguire il failover in Azure usando [questi](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure) passaggi.

### <a name="hyper-v-to-azure-via-powershell"></a>Da Hyper-V ad Azure tramite PowerShell

1. Assicurarsi di [preparare i server Hyper-V locali](./hyper-v-prepare-on-premises-tutorial.md) per il ripristino di emergenza in Azure.
2. [Accedere](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account) ad Azure.
3. [Configurare](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault) un insieme di credenziali e [impostare](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context) il contesto dell'insieme di credenziali di Servizi di ripristino.
4. [Creare](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) un sito Hyper-V.
5. [Installare](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent) il provider e l'agente.
6. [Creare](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy) i criteri di replica.
7. Abilitare la replica usando i passaggi seguenti: 
    
    a. Recuperare l'elemento di protezione corrispondente alla macchina virtuale da proteggere, come segue:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Proteggere la macchina virtuale. Se alla VM protetta è collegato più di un disco, specificare il disco del sistema operativo usando il parametro OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. Attendere che le macchine virtuali raggiungano uno stato protetto dopo la replica iniziale. L’operazione può richiedere un certo tempo a seconda di fattori quali la quantità di dati da replicare e la larghezza di banda upstream disponibile in Azure. Quando viene raggiunto lo stato protetto, i valori State e StateDescription del processo vengono aggiornati come segue: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Aggiornare le proprietà di ripristino, ad esempio le dimensioni del ruolo macchina virtuale, e la rete di Azure a cui associare la scheda di interfaccia di rete della macchina virtuale dopo il failover.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Eseguire un [failover](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover) di test.


## <a name="next-steps"></a>Passaggi successivi

Per eseguire la riprotezione e il failback per VMware in Azure, seguire i passaggi descritti [qui](./vmware-azure-prepare-failback.md).

Per eseguire il failover per Hyper-V in Azure, seguire i passaggi descritti [qui](./site-recovery-failover.md) e per eseguire il failback, seguire i passaggi illustrati [qui](./hyper-v-azure-failback.md).

Per altre informazioni, vedere [Failover in Site Recovery](site-recovery-failover.md).
