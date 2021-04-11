---
title: Ripristinare Managed Disks di Azure tramite Azure PowerShell
description: Informazioni su come ripristinare Azure Managed Disks usando Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0ddf552947c39692ea01d0dea7e67f147d754fcc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630443"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Ripristinare Managed Disks di Azure con Azure PowerShell

Questo articolo illustra come ripristinare [Managed Disks di Azure](../virtual-machines/managed-disks-overview.md) da un punto di ripristino creato da backup di Azure.

Attualmente, l'opzione di ripristino Original-Location (OLR) di ripristino sostituendo il disco di origine esistente da cui sono stati eseguiti i backup non è supportata. È possibile eseguire il ripristino da un punto di ripristino per creare un nuovo disco nello stesso gruppo di risorse del disco di origine da cui sono stati eseguiti i backup o in qualsiasi altro gruppo di risorse. Questa operazione è nota come Alternate-Location Recovery (ALR) e consente di gestire sia il disco di origine sia il disco ripristinato (nuovo).

In questo articolo si apprenderà come:

- Ripristinare per creare un nuovo disco

- Tenere traccia dello stato dell'operazione di ripristino

Si fa riferimento a un insieme di credenziali di backup esistente "TestBkpVault" nel gruppo di risorse "testBkpVaultRG" negli esempi

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Ripristinare per creare un nuovo disco

### <a name="setting-up-permissions"></a>Configurazione delle autorizzazioni

L'insieme di credenziali di backup usa l'identità gestita per accedere ad altre risorse di Azure. Per eseguire il ripristino da un backup, l'identità gestita dell'insieme di credenziali di backup richiede un set di autorizzazioni per il gruppo di risorse in cui deve essere ripristinato il disco.

L'insieme di credenziali per il backup usa un'identità gestita assegnata dal sistema, che è limitata a una per risorsa ed è associata al ciclo di vita di questa risorsa. È possibile concedere le autorizzazioni all'identità gestita usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Identità gestita è un'entità servizio di un tipo speciale che può essere usata solo con le risorse di Azure. Altre informazioni sulle [identità gestite](../active-directory/managed-identities-azure-resources/overview.md).

Assegnare le autorizzazioni pertinenti per l'identità gestita assegnata dal sistema dell'insieme di credenziali nel gruppo di risorse di destinazione in cui i dischi verranno ripristinati o creati come indicato [qui](restore-managed-disks.md#restore-to-create-a-new-disk).

### <a name="fetching-the-relevant-recovery-point"></a>Recupero del punto di ripristino pertinente

Recuperare tutte le istanze usando il comando [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) e identificare l'istanza pertinente.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

È anche possibile usare **AZ. Resourcegraph** e il comando [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) per eseguire ricerche in più istanze in molti insiemi di credenziali e sottoscrizioni.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Una volta identificata l'istanza, recuperare il punto di ripristino pertinente.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Preparazione della richiesta di ripristino

Costruire l'ID ARM del nuovo disco da creare con il gruppo di risorse di destinazione, a cui sono state assegnate le autorizzazioni come descritto in [precedenza](#setting-up-permissions)e il nome del disco richiesto. È ad esempio possibile denominare un disco **PSTestDisk2** in un gruppo di risorse **targetrg** con una sottoscrizione diversa.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Usare il comando [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) per preparare la richiesta di ripristino con tutti i dettagli pertinenti.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Attivare il ripristino

Usare il comando [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) per attivare il ripristino con la richiesta preparata in precedenza.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Rilevamento processo

Tenere traccia di tutti i processi usando il comando [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . È possibile elencare tutti i processi e recuperare i dettagli di un determinato processo.

È anche possibile usare **AZ. ResourceGraph** per tenere traccia di tutti i processi in tutti gli insiemi di credenziali di backup. Usare il comando [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) per ottenere il processo pertinente, che può trovarsi in qualsiasi insieme di credenziali per il backup.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti su backup dischi di Azure](disk-backup-faq.md)
