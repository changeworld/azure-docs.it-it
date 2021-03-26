---
title: Supporto del livello archivio (anteprima)
description: Informazioni sul supporto del livello di archiviazione per backup di Azure
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 322bc9d7e2160cc9156c793859b9fda833b3df09
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563974"
---
# <a name="archive-tier-support-preview"></a>Supporto del livello archivio (anteprima)

I clienti si affidano a backup di Azure per archiviare i dati di backup, inclusi i dati di backup del Long-Term di conservazione (LTR) con esigenze di conservazione definite dalle regole di conformità dell'organizzazione. Nella maggior parte dei casi, i dati di backup meno recenti vengono utilizzati raramente e vengono archiviati solo per esigenze di conformità.

Backup di Azure supporta il backup dei punti di conservazione a lungo termine nel livello archivio, oltre agli snapshot e al livello standard.

## <a name="scope-for-preview"></a>Ambito per l'anteprima

Carichi di lavoro supportati:

- Macchine virtuali di Azure
  - Solo punti di ripristino mensili e annuali. I punti di ripristino giornalieri e settimanali non sono supportati.
  - Age >= 3 mesi nel livello Vault-Standard
  - Conservazione >rimanente = 6 mesi
  - Nessuna dipendenza attiva giornaliera e settimanale
- SQL Server in macchine virtuali di Azure
  - Solo punti di ripristino completi. I log e i differenziali non sono supportati.
  - Age >= 45 giorni nel livello Vault-Standard
  - Conservazione >rimanente = 6 mesi
  - Nessuna dipendenza

Client supportati:

- La funzionalità viene fornita tramite PowerShell

>[!NOTE]
>Il supporto del livello di archiviazione per le macchine virtuali di Azure e SQL Server in macchine virtuali di Azure è in versione di anteprima pubblica limitata con Iscrizioni limitate. Per iscriversi al supporto per l'archivio, usare questo [collegamento](https://aka.ms/ArchivePreviewInterestForm).

## <a name="get-started-with-powershell"></a>Introduzione a PowerShell

1. In PowerShell eseguire questo comando:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. Connettersi ad Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
1. Accedere alla sottoscrizione:

   `Set-AzContext -Subscription "SubscriptionName"`

1. Ottenere l'insieme di credenziali:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. Ottenere l'elenco degli elementi di backup:

    `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM/AzureWorkload" -WorkloadType "AzureVM/MSSQL"`

1. Ottiene l'elemento di backup.

    - Per macchine virtuali di Azure:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Per SQL Server in macchine virtuali di Azure:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>Usare PowerShell

### <a name="check-archivable-recovery-points"></a>Controlla punti di ripristino archiviabili

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

Verranno elencati tutti i punti di ripristino associati a un particolare elemento di backup che è possibile spostare nell'archivio.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Verificare il motivo per cui non è possibile spostare un punto di ripristino nell'archivio

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Dove `$rp[0]` è il punto di ripristino per il quale si desidera verificare il motivo per cui non è archiviabile.

Output di esempio:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Controllare la serie consigliata di punti archiviabili (solo per le macchine virtuali di Azure)

I punti di ripristino associati a una macchina virtuale sono di natura incrementale. Quando un punto di ripristino specifico viene spostato nell'archivio, viene convertito in un backup completo e quindi spostato nell'archivio. Pertanto, il risparmio sui costi associato al passaggio all'archivio dipende dalla varianza dell'origine dati.

Quindi, backup di Azure ha creato un set di punti di ripristino consigliato che può comportare risparmi in termini di costi se spostati insieme.

>[!NOTE]
>Il risparmio sui costi dipende da diversi motivi e potrebbe non essere lo stesso per due istanze.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Sposta in archivio

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Questo comando sposta un punto di ripristino archiviabile in archivio. Restituisce un processo che può essere usato per tenere traccia dell'operazione di spostamento sia dal portale che con PowerShell.

### <a name="view-archived-recovery-points"></a>Visualizza punti di ripristino archiviati

Questo comando restituisce tutti i punti di ripristino archiviati.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>Ripristino con PowerShell

Per i punti di ripristino nell'archivio, backup di Azure fornisce una metodologia di ripristino integrata.

Il ripristino integrato è un processo in due passaggi. Il primo passaggio prevede la riattivazione dei punti di ripristino archiviati nell'archivio e l'archiviazione temporanea nel livello standard dell'insieme di credenziali per una durata (nota anche come durata della riattivazione) da un periodo compreso tra 10 e 30 giorni. Il valore predefinito è 15 giorni. Esistono due diverse priorità di riattivazione: standard e priorità alta. Altre informazioni sulla [priorità di reidratazione](../storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier).

>[!NOTE]
>
>- La durata della riattivazione dopo la selezione non può essere modificata e i punti di ripristino reidratati rimangono nel livello standard per la durata della riattivazione.
>- Il passaggio aggiuntivo della riattivazione comporta un costo.

Per altre informazioni sui vari metodi di ripristino per le macchine virtuali di Azure, vedere [ripristinare una VM di Azure con PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Per ripristinare SQL Server, attenersi alla [seguente procedura](backup-azure-sql-automation.md#restore-sql-dbs). I parametri aggiuntivi richiesti sono **RehydrationPriority** e **RehydrationDuration**.

### <a name="view-jobs-from-powershell"></a>Visualizzare i processi da PowerShell

Per visualizzare i processi di spostamento e ripristino, usare il cmdlet di PowerShell seguente:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>Usare il portale

### <a name="check-archived-recovery-point"></a>Verifica punto di ripristino archiviato

È ora possibile visualizzare tutti i punti di ripristino spostati nell'archivio.

![Tutti i punti di ripristino.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Ripristino nel portale

Per i punti di ripristino spostati in archivio, per il ripristino è necessario aggiungere i parametri per la durata della reidratazione e la priorità di riattivazione.

![Ripristino nel portale.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Visualizzare i processi nel portale

![Visualizzare i processi nel portale.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Modificare la protezione

Esistono due modi in cui è possibile modificare la protezione per un'origine dati:

- Modificare criteri esistenti
- Protezione dell'origine dati con un nuovo criterio

In entrambi i casi, il nuovo criterio viene applicato a tutti i punti di ripristino precedenti, che si trovano nel livello standard e quelli nel livello archivio. I punti di ripristino meno recenti potrebbero essere eliminati se si verifica una modifica nei criteri.

Quando i punti di ripristino vengono spostati in archivio, vengono sottoposti a un periodo di eliminazione anticipato di 180 giorni. Gli addebiti vengono rivalutati. Se un punto di ripristino che non è rimasto nell'archivio per 180 giorni viene eliminato, avrà un costo equivalente a 180 meno il numero di giorni trascorso nel livello standard.

I punti di ripristino che non sono rimasti nell'archivio per almeno sei mesi comporteranno un costo di eliminazione anticipato durante l'eliminazione.

## <a name="stop-protection-and-delete-data"></a>Arrestare la protezione ed eliminare i dati

Arresta protezione ed Elimina dati Elimina tutti i punti di ripristino. Per i punti di ripristino in archivio che non sono rimasti per una durata di 180 giorni nel livello archivio, l'eliminazione dei punti di ripristino comporterà un costo di eliminazione anticipata.

## <a name="error-codes-and-troubleshooting-steps"></a>Codici di errore e procedure per la risoluzione dei problemi

Sono disponibili diversi codici di errore che si verificano quando non è possibile spostare un punto di ripristino nell'archivio.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Messaggio di errore** -il tipo di Recovery-Point non è idoneo per lo spostamento dell'archivio

**Descrizione** : questo codice di errore viene visualizzato quando il tipo di punto di ripristino selezionato non è idoneo per essere spostato nell'archivio.

**Azione consigliata** : verificare l'idoneità del punto di ripristino [qui](#scope-for-preview)

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Messaggio di errore** : Recovery-Point con dipendenze attive per il ripristino non è idoneo per lo spostamento dell'archivio

**Descrizione:** Il punto di ripristino selezionato presenta dipendenze attive e pertanto non può essere spostato nell'archivio.

**Azione consigliata** : verificare l'idoneità del punto di ripristino [qui](#scope-for-preview)

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Messaggio di errore** -Recovery-Point non è idoneo per lo spostamento dell'archivio perché la durata dell'insieme di credenziali per il livello standard è inferiore al minimo richiesto

**Descrizione** : il punto di ripristino deve rimanere nel livello standard per almeno tre mesi per le macchine virtuali di azure e 45 giorni per SQL Server in macchine virtuali di Azure

**Azione consigliata** : verificare l'idoneità del punto di ripristino [qui](#scope-for-preview)

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Messaggio di errore** -Recovery-Point durata rimanente è inferiore al minimo necessario.

**Descrizione** : la durata minima necessaria per un punto di ripristino per l'idoneità di spostamento dell'archivio è di sei mesi.

**Azione consigliata** : verificare l'idoneità del punto di ripristino [qui](#scope-for-preview)

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Messaggio di errore** -Recovery-Point non è idoneo per lo spostamento dell'archivio perché è già stato spostato nel livello archivio

**Descrizione** : il punto di ripristino selezionato è già presente nell'archivio. Quindi, non è idoneo per essere spostato nell'archivio.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Messaggio di errore** : tipo di origine dati non idoneo per l'API di raccomandazione.

**Descrizione** : l'API di raccomandazione è applicabile solo per le macchine virtuali di Azure. Non è applicabile per il tipo di origine dati selezionato.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Messaggio di errore** : il punto di ripristino è già stato reidratato. La riattivazione non è consentita in questo RP.

**Descrizione** : il punto di ripristino selezionato è già stato reidratato.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Messaggio di errore** -il punto di ripristino non è idoneo per lo spostamento dell'archivio.

**Descrizione** : il punto di ripristino selezionato non è idoneo per lo spostamento dell'archivio.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

 **Messaggio** di errore: il punto di ripristino dell'archivio non è stato reidratato. Ripetere l'operazione di ripristino dopo aver completato la riattivazione nell'archivio RP.

**Descrizione** : il punto di ripristino non viene reidratato. Provare a eseguire il ripristino dopo avere reidratato il punto di ripristino.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

 **Messaggio** di errore-la riattivazione è supportata solo per i punti di ripristino dell'archivio. la riattivazione è supportata solo per i punti di ripristino

**Descrizione** : la riattivazione non è consentita per il punto di ripristino selezionato.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Messaggio di errore** : la reidratazione è già In-Progress per il punto di ripristino dell'archivio.

**Descrizione** : la riattivazione per il punto di ripristino selezionato è già in corso.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Messaggio di errore** -Impossibile spostare il punto di ripristino nel livello archivio a causa di una durata di conservazione insufficiente specificata nei criteri

**Azione consigliata** : aggiornare i criteri nell'elemento protetto con l'impostazione di conservazione appropriata e riprovare.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Messaggio di errore** : stiamo ancora verificando se questo punto di ripristino può essere spostato.

**Descrizione** : è ancora necessario determinare la conformità allo spostamento del punto di ripristino.

**Azione consigliata** : verificare di nuovo dopo aver atteso il tempo.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Cosa accadrà per archiviare i punti di ripristino se si arresta la protezione e si conservano i dati?

Il punto di ripristino rimarrà sempre in archivio. Per ulteriori informazioni, vedere l' [effetto di arrestare la protezione nei punti di ripristino](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

## <a name="next-steps"></a>Passaggi successivi

- [Prezzi di Backup di Azure](azure-backup-pricing.md)