---
title: Ripristinare azure Managed Disks tramite Azure PowerShell
description: Informazioni su come ripristinare azure Managed Disks usando Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520038"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Ripristinare azure Managed Disks usando Azure PowerShell

Questo articolo illustra come ripristinare azure [Managed Disks](../virtual-machines/managed-disks-overview.md) da un punto di ripristino creato da Backup di Azure.

Attualmente, l'opzione Original-Location recovery (OLR) di ripristino sostituendo il disco di origine esistente da cui sono stati evasi i backup non è supportata. È possibile eseguire il ripristino da un punto di ripristino per creare un nuovo disco nello stesso gruppo di risorse del disco di origine da cui sono stati evasi i backup o in qualsiasi altro gruppo di risorse. Questa operazione è nota come Alternate-Location ripristino automatico (ALR) e consente di mantenere sia il disco di origine che il disco ripristinato (nuovo).

In questo articolo si apprenderà come:

- Eseguire il ripristino per creare un nuovo disco

- Tenere traccia dello stato dell'operazione di ripristino

Negli esempi si farà riferimento a un insieme di credenziali di backup esistente "TestBkpVault" nel gruppo di risorse "testBkpVaultRG"

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Eseguire il ripristino per creare un nuovo disco

### <a name="setting-up-permissions"></a>Configurazione delle autorizzazioni

Backup Vault usa l'identità gestita per accedere ad altre risorse di Azure. Per eseguire il ripristino dal backup, l'identità gestita dell'insieme di credenziali di backup richiede un set di autorizzazioni per il gruppo di risorse in cui deve essere ripristinato il disco.

L'insieme di credenziali di backup usa un'identità gestita assegnata dal sistema, limitata a una per ogni risorsa ed è associata al ciclo di vita di questa risorsa. È possibile concedere autorizzazioni all'identità gestita usando il controllo degli accessi in base al ruolo di Azure. L'identità gestita è un'entità servizio di un tipo speciale che può essere usata solo con le risorse di Azure. Altre informazioni sulle [identità gestite.](../active-directory/managed-identities-azure-resources/overview.md)

Assegnare le autorizzazioni rilevanti per l'identità gestita assegnata dal sistema dell'insieme di credenziali nel gruppo di risorse di destinazione in cui verranno ripristinati/creati i dischi, come indicato [qui.](restore-managed-disks.md#restore-to-create-a-new-disk)

### <a name="fetching-the-relevant-recovery-point"></a>Recupero del punto di ripristino pertinente

Recuperare tutte le istanze [usando il comando Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) e identificare l'istanza pertinente.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

È anche possibile usare **Az.Resourcegraph** e il comando [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) per eseguire ricerche tra istanze in molti insiemi di credenziali e sottoscrizioni.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Dopo aver identificato l'istanza, recuperare il punto di ripristino pertinente.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Preparazione della richiesta di ripristino

Costruire l'ID ARM del nuovo disco da creare con il gruppo di [](#setting-up-permissions)risorse di destinazione, a cui sono state assegnate le autorizzazioni come descritto in dettaglio in precedenza, e il nome del disco richiesto. Ad esempio, un disco può essere denominato **PSTestDisk2** in un gruppo di risorse **targetrg** con una sottoscrizione diversa.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Usare il [comando Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) per preparare la richiesta di ripristino con tutti i dettagli pertinenti.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Attivare il ripristino

Usare il [comando Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) per attivare il ripristino con la richiesta preparata in precedenza.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Processo di rilevamento

Tenere traccia di tutti i processi [usando il comando Get-AzDataProtectionJob.](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) È possibile elencare tutti i processi e recuperare i dettagli di un processo specifico.

È anche possibile usare **Az.ResourceGraph** per tenere traccia di tutti i processi in tutti gli insiemi di credenziali di backup. Usare il [comando Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) per ottenere il processo pertinente, che può essere in qualsiasi insieme di credenziali di backup.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti su Backup su disco di Azure](disk-backup-faq.yml)
