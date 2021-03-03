---
title: Opzioni di ridondanza per Azure Managed Disks
description: Informazioni sull'archiviazione con ridondanza della zona e l'archiviazione con ridondanza locale per Azure Managed Disks.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6cafbff86a55ad0bed7da17fcef1aea2b0a53d1b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679711"
---
# <a name="redundancy-options-for-managed-disks"></a>Opzioni di ridondanza per Managed Disks

Azure Managed Disks offre due opzioni di ridondanza di archiviazione, archiviazione con ridondanza della zona (ZRS) come anteprima e archiviazione con ridondanza locale. ZRS offre una disponibilità più elevata per i dischi gestiti rispetto all'archiviazione con ridondanza locale (con ridondanza locale). Tuttavia, la latenza di scrittura per i dischi con ridondanza locale è migliore rispetto ai dischi ZRS, perché i dischi con ridondanza locale scrivono i dati in modo sincrono in tre copie in un'unica data center.

## <a name="locally-redundant-storage-for-managed-disks"></a>Archiviazione con ridondanza locale per Managed Disks

Archiviazione con ridondanza locale (con ridondanza locale) replica i dati tre volte all'interno di una singola data center nell'area selezionata. L'archiviazione con ridondanza locale protegge i dati dagli errori del rack di server e delle unità. 

Esistono diversi modi per proteggere l'applicazione con i dischi con ridondanza locale da un errore dell'intera zona che può verificarsi a causa di calamità naturali o problemi hardware:
- Usare un'applicazione come SQL Server AlwaysOn, in grado di scrivere i dati in modo sincrono in due zone e di eseguire automaticamente il failover in un'altra area durante un'emergenza.
- Eseguire backup frequenti dei dischi con ridondanza locale con snapshot ZRS.
- Abilitare il ripristino di emergenza tra zone per i dischi con ridondanza locale tramite [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Tuttavia, il ripristino di emergenza tra zone non fornisce l'obiettivo del punto di ripristino (RPO) zero.

Se il flusso di lavoro non supporta le scritture sincrone a livello di applicazione tra le zone o se l'applicazione deve soddisfare zero RPO, i dischi ZRS sarebbero ideali.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Archiviazione con ridondanza della zona per Managed Disks (anteprima)

Archiviazione con ridondanza della zona (ZRS) replica in modo sincrono il disco gestito di Azure in tre zone di disponibilità di Azure nell'area selezionata. Ogni zona di disponibilità è una posizione fisica separata con alimentazione, raffreddamento e rete indipendenti. 

I dischi ZRS consentono di eseguire il ripristino dagli errori nelle zone di disponibilità. Se un'intera zona è inattiva, un disco ZRS può essere collegato a una macchina virtuale in un'area diversa. È anche possibile usare i dischi ZRS in combinazione con i dischi condivisi per offrire una maggiore disponibilità per applicazioni cluster o distribuite come FCI SQL, SAP ASC/SCS o GFS2. È possibile aggiungere un disco ZRS condiviso alle macchine virtuali primarie e secondarie in zone diverse per sfruttare i vantaggi di ZRS e [zone di disponibilità](../availability-zones/az-overview.md). Se la zona primaria ha esito negativo, è possibile eseguire rapidamente il failover alla macchina virtuale secondaria usando la [prenotazione permanente SCSI](disks-shared-enable.md#supported-scsi-pr-commands).

### <a name="limitations"></a>Limitazioni

Durante l'anteprima, ZRS per Managed disks presenta le restrizioni seguenti:

- Supportato solo con unità SSD (Solid-State Drive) Premium e SSD standard.
- Attualmente disponibile solo nell'area EastUS2EUAP.
- I dischi ZRS possono essere creati solo con Azure Resource Manager modelli usando l' `2020-12-01` API.

Iscriversi per l'anteprima [qui](https://aka.ms/ZRSDisksPreviewSignUp).

### <a name="billing-implications"></a>Implicazioni relative alla fatturazione

Per informazioni dettagliate, vedere la [pagina dei prezzi di Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Confronto con altri tipi di dischi

Ad eccezione della latenza di scrittura, i dischi che usano ZRS sono identici ai dischi che usano con ridondanza locale. Hanno gli stessi obiettivi di prestazioni.

### <a name="create-zrs-managed-disks"></a>Creare dischi gestiti ZRS

`2020-12-01`Per creare un disco ZRS, è necessario usare l'API con il modello di Azure Resource Manager.

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
