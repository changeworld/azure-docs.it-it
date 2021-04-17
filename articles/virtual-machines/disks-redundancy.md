---
title: Opzioni di ridondanza per i dischi gestiti di Azure
description: Informazioni sull'archiviazione con ridondanza della zona e sull'archiviazione con ridondanza locale per i dischi gestiti di Azure.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0882efeccfc8dc83686d75ab39b8364219c3b5f1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588089"
---
# <a name="redundancy-options-for-managed-disks"></a>Opzioni di ridondanza per i dischi gestiti

I dischi gestiti di Azure offrono due opzioni di ridondanza di archiviazione, l'archiviazione con ridondanza della zona (ZRS) come anteprima e l'archiviazione con ridondanza locale. L'archiviazione con ridondanza locale offre una disponibilità più elevata per i dischi gestiti rispetto all'archiviazione con ridondanza locale. Tuttavia, la latenza di scrittura per i dischi LRS è migliore rispetto ai dischi ZRS perché i dischi LRS scrivono dati in modo sincrono in tre copie in un singolo data center.

## <a name="locally-redundant-storage-for-managed-disks"></a>Archiviazione con ridondanza locale per i dischi gestiti

L'archiviazione con ridondanza locale replica i dati tre volte in un singolo data center nell'area selezionata. L'archiviazione con ridondanza locale protegge i dati dagli errori del rack di server e delle unità. 

Esistono alcuni modi per proteggere l'applicazione usando dischi LRS da un errore di zona intero che può verificarsi a causa di calamità naturali o problemi hardware:
- Usare un'applicazione SQL Server AlwaysOn, in grado di scrivere dati in modo sincrono in due zone e di eseguire automaticamente il failover in un'altra zona durante un'emergenza.
- Eseguire backup frequenti dei dischi LRS con snapshot ZRS.
- Abilitare il ripristino di emergenza tra zone per i dischi LRS [tramite Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Tuttavia, il ripristino di emergenza tra zone non fornisce un obiettivo del punto di ripristino (RPO) pari a zero.

Se il flusso di lavoro non supporta scritture sincrone a livello di applicazione tra zone o l'applicazione deve soddisfare zero RPO, i dischi ZRS sono ideali.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Archiviazione con ridondanza della zona per i dischi gestiti (anteprima)

L'archiviazione con ridondanza della zona replica il disco gestito di Azure in modo sincrono in tre zone di disponibilità di Azure nell'area selezionata. Ogni zona di disponibilità è una posizione fisica separata con alimentazione, raffreddamento e rete indipendenti. 

I dischi ZRS consentono di eseguire il ripristino in caso di errori nelle zone di disponibilità. Se un'intera zona si è insediata, è possibile collegare un disco ZRS a una macchina virtuale in una zona diversa. È anche possibile usare dischi ZRS come disco condiviso per offrire una maggiore disponibilità per le applicazioni in cluster o distribuite, ad esempio SQL FCI, SAP ASCS/SCS o GFS2. È possibile collegare un disco ZRS condiviso alle macchine virtuali primarie e secondarie in zone diverse per sfruttare i vantaggi sia dell'archiviazione con zone di disponibilità [.](../availability-zones/az-overview.md) Se si verifica un errore nella zona primaria, è possibile eseguire rapidamente il failover nella macchina virtuale secondaria usando la [prenotazione persistente SCSI.](disks-shared-enable.md#supported-scsi-pr-commands)

### <a name="limitations"></a>Limitazioni

Durante l'anteprima, l'archiviazione con archiviazione con restrizioni per i dischi gestiti presenta le restrizioni seguenti:

- Supportato solo con unità SSD Premium e SSD Standard.
- Attualmente disponibile solo nell'area EastUS2EUAP.
- I dischi ZRS possono essere creati solo con Azure Resource Manager usando `2020-12-01` l'API.

Iscriversi per l'anteprima [qui.](https://aka.ms/ZRSDisksPreviewSignUp)

### <a name="billing-implications"></a>Implicazioni relative alla fatturazione

Per informazioni dettagliate, vedere la [pagina dei prezzi di Azure.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="comparison-with-other-disk-types"></a>Confronto con altri tipi di disco

Fatta eccezione per una maggiore latenza di scrittura, i dischi che usano LRS sono identici ai dischi che usano l'archiviazione con archiviazione con accesso in lettura. Hanno gli stessi obiettivi di prestazioni. È consigliabile eseguire il [benchmarking](disks-benchmarks.md) dei dischi per simulare il carico di lavoro dell'applicazione per confrontare la latenza tra i dischi di archiviazione con accesso in lettura. 

### <a name="create-zrs-managed-disks"></a>Creare dischi gestiti dell'archiviazione con archiviazione

Usare `2020-12-01` l'API con il modello Azure Resource Manager per creare un disco ZRS.

#### <a name="create-a-vm-with-zrs-disks"></a>Creare una macchina virtuale con dischi ZRS

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Creare macchine virtuali con un disco ZRS condiviso collegato alle macchine virtuali in zone diverse

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Creare un set di scalabilità di macchine virtuali con dischi ZRS

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>Passaggi successivi

- Usare questi modelli Azure Resource Manager [esempio per creare una macchina virtuale con dischi ZRS.](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks)
