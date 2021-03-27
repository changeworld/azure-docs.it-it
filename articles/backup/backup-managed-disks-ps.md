---
title: Eseguire il backup di Managed Disks di Azure con Azure PowerShell
description: Informazioni su come eseguire il backup di Azure Managed Disks usando Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630493"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Eseguire il backup di Managed Disks di Azure con Azure PowerShell

Questo articolo illustra come eseguire il backup di un [disco gestito di Azure](../virtual-machines/managed-disks-overview.md) con Azure PowerShell.

In questo articolo si apprenderà come:

- Creare un insieme di credenziali per il backup

- Creare un criterio di backup

- Configurare un backup di un disco di Azure

- Eseguire un processo di backup su richiesta

Per informazioni sulla disponibilità dell'area di backup su disco di Azure, sulle limitazioni e sugli scenari supportati, vedere la [matrice di supporto](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Creare un insieme di credenziali per il backup

Un insieme di credenziali per il backup è un'entità di archiviazione di Azure che include i dati di backup per i vari carichi di lavoro più recenti supportati da backup di Azure, ad esempio database di Azure per i server PostgreSQL e dischi di Azure. Gli insiemi di credenziali di backup consentono di organizzare facilmente i dati di backup, riducendo al minimo il sovraccarico di gestione. Gli insiemi di credenziali di backup si basano sul modello di Azure Resource Manager di Azure, che offre funzionalità avanzate per proteggere i dati di backup.

Prima di creare un insieme di credenziali per il backup, scegliere la ridondanza di archiviazione dei dati all'interno dell'insieme di credenziali. Quindi, procedere con la creazione dell'insieme di credenziali di backup con la ridondanza di archiviazione e il percorso. In questo articolo verrà creato un insieme di credenziali di backup "TestBkpVault" nell'area "westus" nel gruppo di risorse "testBkpVaultRG". Usare il comando [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) per creare un insieme di credenziali per il backup. Altre informazioni sulla creazione di un insieme di credenziali per [il backup](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Dopo la creazione dell'insieme di credenziali, creare un criterio di backup per proteggere i dischi di Azure.

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

Per comprendere i componenti interni di un criterio di backup per il backup su disco di Azure, recuperare il modello di criteri usando il comando [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). Questo comando restituisce un modello di criteri predefinito per un tipo di origine dati specificato. Utilizzare questo modello di criteri per creare un nuovo criterio.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Il modello di criteri è costituito da un trigger (che decide che cosa attiva il backup) e da un ciclo di vita (che decide quando eliminare/copiare/spostare il backup). In backup dischi di Azure il valore predefinito per trigger è un trigger orario pianificato ogni 4 ore (PT4H) e per conservare ogni backup per 7 giorni.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Il backup su disco di Azure offre più backup al giorno. Se sono necessari backup più frequenti, scegliere la frequenza di backup **oraria** con la possibilità di eseguire i backup con intervalli di ogni 4, 6, 8 o 12 ore. I backup sono pianificati in base all'intervallo di **tempo** selezionato. Se, ad esempio, si seleziona **ogni 4 ore**, i backup vengono eseguiti approssimativamente nell'intervallo di ogni 4 ore, in modo che i backup vengano distribuiti equamente durante il giorno. Se è sufficiente un backup di una volta al giorno, scegliere la frequenza di backup **giornaliera** . Nella frequenza di backup giornaliera è possibile specificare l'ora del giorno in cui vengono eseguiti i backup. È importante notare che l'ora del giorno indica l'ora di inizio del backup e non l'ora di completamento del backup. Il tempo necessario per il completamento dell'operazione di backup dipende da diversi fattori, tra cui le dimensioni del disco e la velocità di varianza tra backup consecutivi. Tuttavia, backup su disco di Azure è un backup senza agente che usa [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md), che non influisca sulle prestazioni dell'applicazione di produzione.

   >[!NOTE]
   > Anche se l'insieme di credenziali selezionato può avere l'impostazione di ridondanza globale, attualmente backup dischi di Azure supporta solo archivio dati snapshot. Tutti i backup vengono archiviati in un gruppo di risorse nella sottoscrizione e non vengono copiati nell'archivio dell'insieme di credenziali di backup.

Per altre informazioni sulla creazione dei criteri, vedere il documento relativo ai [criteri di backup su disco di Azure](backup-managed-disks.md#create-backup-policy) .

Se si desidera modificare la frequenza oraria o il periodo di conservazione, utilizzare i comandi [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) e/o [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) . Una volta che l'oggetto criterio ha tutti i valori desiderati, procedere con la creazione di un nuovo criterio dall'oggetto criterio usando [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Configurare il backup

Dopo aver creato l'insieme di credenziali e i criteri, è necessario prendere in considerazione 3 punti critici per proteggere un disco di Azure.

### <a name="key-entities-involved"></a>Entità chiave interattive

#### <a name="disk-to-be-protected"></a>Disco da proteggere

Recuperare l'ID ARM del disco da proteggere. Questa funzione fungerà da identificatore del disco. Si userà un esempio di disco denominato "PSTestDisk" in un gruppo di risorse "diskrg" in una sottoscrizione diversa.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Gruppo di risorse snapshot

Gli snapshot del disco vengono archiviati in un gruppo di risorse all'interno della sottoscrizione. Come linea guida, è consigliabile creare un gruppo di risorse dedicato come archivio dati snapshot da usare con il servizio backup di Azure. Un gruppo di risorse dedicato consente di limitare le autorizzazioni di accesso al gruppo di risorse, garantendo la sicurezza e la facilità di gestione dei dati di backup. Prendere nota dell'ID ARM per il gruppo di risorse in cui si vogliono inserire gli snapshot del disco

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Insieme di credenziali per il backup

Gli insiemi di credenziali per il backup richiedono le autorizzazioni su disco e il gruppo di risorse snapshot per poter attivare gli snapshot e gestire il ciclo di vita. L'identità gestita assegnata dal sistema dell'insieme di credenziali viene usata per assegnare tali autorizzazioni. Usare il comando [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) per abilitare l'identità gestita assegnata dal sistema per l'insieme di credenziali di servizi di ripristino.

### <a name="assign-permissions"></a>Assegnare le autorizzazioni

L'utente deve assegnare alcune autorizzazioni tramite il controllo degli accessi in base al ruolo (rappresentato da Vault MSI) e il disco pertinente e/o il disco RG. Questi possono essere eseguiti tramite il portale o PowerShell. Tutte le autorizzazioni correlate sono descritte in dettaglio in punti 1, 2, 3 in [questa sezione](backup-managed-disks.md#configure-backup).

### <a name="prepare-the-request"></a>Preparare la richiesta

Una volta impostate tutte le autorizzazioni rilevanti, la configurazione del backup viene eseguita in due passaggi. In primo luogo, si prepara la richiesta pertinente usando il gruppo di risorse dell'insieme di credenziali, dei criteri, del disco e dello snapshot pertinente usando il comando [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) . Quindi, si invia la richiesta di protezione del disco usando il comando [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

Recuperare l'istanza di backup pertinente per cui l'utente desidera attivare un backup usando il comando [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

È possibile specificare una regola di conservazione durante l'attivazione del backup. Per visualizzare le regole di conservazione nel criterio, spostarsi nell'oggetto criteri per le regole di conservazione. Nell'esempio seguente viene visualizzata la regola denominata ' default ', che verrà usata per il backup su richiesta

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Attivare un backup su richiesta usando il comando [backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Rilevamento dei processi

Tenere traccia di tutti i processi usando il comando [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . È possibile elencare tutti i processi e recuperare i dettagli di un determinato processo.

È anche possibile usare AZ. ResourceGraph per tenere traccia di tutti i processi in tutti gli insiemi di credenziali di backup. Usare il comando [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) per ottenere il processo pertinente che può trovarsi in qualsiasi insieme di credenziali per il backup.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare Managed Disks di Azure con Azure PowerShell](restore-managed-disks-ps.md)
