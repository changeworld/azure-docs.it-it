---
title: Risolvere i problemi relativi al backup di condivisione file di Azure
description: Questo articolo contiene informazioni per la risoluzione dei problemi che si verificano quando si proteggono le condivisioni file di Azure.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4c934d2295fa702425e8df0a03636b9f9208cfa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515074"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Risolvere i problemi durante il backup di condivisioni file di Azure

Questo articolo fornisce informazioni sulla risoluzione dei problemi che si verificano durante la configurazione del backup o il ripristino di condivisioni file di Azure tramite il Backup di Azure Service.

## <a name="common-configuration-issues"></a>Problemi di configurazione comuni

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Impossibile trovare l'account di archiviazione per configurare il backup per la condivisione file di Azure

- Attendere che termini l'individuazione.
- Controllare se una condivisione file nell'account di archiviazione è già protetta con un altro insieme di credenziali di Servizi di ripristino.

  >[!NOTE]
  >le condivisioni file di un account di archiviazione possono essere protette solo con un unico insieme di credenziali di Servizi di ripristino. È possibile usare questo [script per](scripts/backup-powershell-script-find-recovery-services-vault.md) trovare l'insieme di credenziali di Servizi di ripristino in cui è registrato l'account di archiviazione.

- Assicurarsi che la condivisione file non sia presente in nessuno degli account di archiviazione non supportati. È possibile fare riferimento alla matrice [di supporto per il backup](azure-file-share-support-matrix.md) di condivisione file di Azure per trovare gli account di archiviazione supportati.
- Assicurarsi che la lunghezza combinata del nome dell'account di archiviazione e del nome del gruppo di risorse non superi gli 84 caratteri nel caso di nuovi account di archiviazione e di 77 caratteri nel caso degli account di archiviazione classici.
- Controllare le impostazioni del firewall dell'account di archiviazione per assicurarsi che sia abilitata l'opzione che consente ai servizi Microsoft attendibili di accedere all'account di archiviazione.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Un errore visualizzato nel portale indica che non è possibile individuare gli account di archiviazione

Se si ha una sottoscrizione partner (abilitata per CSP), ignorare l'errore. Se la sottoscrizione non è abilitata per CSP e gli account di archiviazione non possono essere individuati, contattare il supporto tecnico.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Convalida o registrazione dell'account di archiviazione selezionato non riuscita

Ripetere la registrazione. Se il problema persiste, contattare il supporto tecnico.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Impossibile elencare o trovare condivisioni file nell'account di archiviazione selezionato

- Assicurarsi che l'account di archiviazione esista nel gruppo di risorse e che non sia stato eliminato o spostato dopo l'ultima convalida o registrazione nell'insieme di credenziali.
- Assicurarsi che la condivisione file che si vuole proteggere non sia stata eliminata.
- Assicurarsi che l'account di archiviazione sia un account di archiviazione supportato per il backup di condivisione file. È possibile fare riferimento alla matrice [di supporto per il backup di condivisione file di Azure](azure-file-share-support-matrix.md) per trovare gli account di archiviazione supportati.
- Controllare se la condivisione file è già protetta nello stesso insieme di credenziali di Servizi di ripristino.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>La configurazione della condivisione file di backup (o la configurazione dei criteri di protezione) ha esito negativo

- Ripetere la configurazione per verificare se il problema persiste.
- Assicurarsi che la condivisione file da proteggere non sia stata eliminata.
- Se si sta tentando di proteggere più condivisioni file contemporaneamente e alcune condivisioni file hanno esito negativo, provare a configurare di nuovo il backup per le condivisioni file non riuscite.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Impossibile eliminare l'insieme di credenziali di Servizi di ripristino dopo la rimozione della protezione di una condivisione file

Nella finestra di portale di Azure aprire gli account di **archiviazione**  >  **dell'infrastruttura di backup**  >  **dell'insieme di credenziali.** Selezionare **Annulla registrazione** per rimuovere gli account di archiviazione dall'insieme di credenziali di Servizi di ripristino.

>[!NOTE]
>Un insieme di credenziali di Servizi di ripristino può essere eliminato solo dopo l'annullamento della registrazione di tutti gli account di archiviazione registrati con l'insieme di credenziali.

## <a name="common-backup-or-restore-errors"></a>Errori comuni di backup o ripristino

>[!NOTE]
>Fare riferimento [a questo documento per](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) assicurarsi di avere autorizzazioni sufficienti per eseguire operazioni di backup o ripristino.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound- Operazione non riuscita perché la condivisione file non è stata trovata

Codice errore: FileShareNotFound

Messaggio di errore: Operazione non riuscita perché la condivisione file non è stata trovata

Assicurarsi che la condivisione file che si sta tentando di proteggere non sia stata eliminata.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable- Account di archiviazione non trovato o non supportato

Codice errore: UserErrorFileShareEndpointUnreachable

Messaggio di errore: Account di archiviazione non trovato o non supportato

- Assicurarsi che l'account di archiviazione esista nel gruppo di risorse e che non sia stato eliminato o rimosso dal gruppo di risorse dopo l'ultima convalida.

- Assicurarsi che l'account di archiviazione sia un account di archiviazione supportato per il backup della condivisione file.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached: è stato raggiunto il limite massimo di snapshot per questa condivisione file. sarà possibile richiedere più tempo dopo la scadenza di quelle meno recenti

Codice errore: AFSMaxSnapshotReached

Messaggio di errore: è stato raggiunto il limite massimo di snapshot per questa condivisione file. sarà possibile richiedere più tempo alla scadenza di quelle meno recenti.

- Questo errore può verificarsi quando si creano più backup su richiesta per una condivisione file.
- È previsto un limite di 200 snapshot per condivisione file, inclusi quelli di Backup di Azure. I backup (o gli snapshot) pianificati meno recenti vengono cancellati automaticamente. I backup (o gli snapshot) su richiesta devono essere eliminati se viene raggiunto il limite massimo.

Eliminare i backup su richiesta (snapshot di condivisione file di Azure) dal portale di File di Azure.

>[!NOTE]
> eliminando gli snapshot creati da Azure Backup si perderanno i punti di ripristino.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound: operazione non riuscita perché l'account di archiviazione specificato non esiste più

Codice errore: UserErrorStorageAccountNotFound

Messaggio di errore: Operazione non riuscita perché l'account di archiviazione specificato non esiste più.

Assicurarsi che l'account di archiviazione esista ancora e non sia eliminato.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound: i dettagli dell'account di archiviazione specificati non sono corretti

Codice errore: UserErrorDTSStorageAccountNotFound

Messaggio di errore: i dettagli dell'account di archiviazione specificati non sono corretti.

Assicurarsi che l'account di archiviazione esista ancora e non sia eliminato.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound: il gruppo di risorse non esiste

Codice errore: UserErrorResourceGroupNotFound

Messaggio di errore: Il gruppo di risorse non esiste

Selezionare un gruppo di risorse esistente o crearne uno nuovo.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest: è già in corso un processo di backup per questa condivisione file

Codice errore: ParallelSnapshotRequest

Messaggio di errore: È già in corso un processo di backup per questa condivisione file.

- Il backup della condivisione file non supporta le richieste di snapshot parallele nella stessa condivisione file.

- Attendere il completamento del processo di backup esistente e riprovare. Se non è possibile trovare un processo di backup nell'insieme di credenziali di Servizi di ripristino, controllare gli altri insiemi di credenziali di Servizi di ripristino nella stessa sottoscrizione.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling- Il backup o il ripristino della condivisione file non è riuscito a causa della limitazione del servizio di archiviazione. Il problema potrebbe essere dovuto al fatto che il servizio di archiviazione è occupato nell'elaborazione di altre richieste per l'account di archiviazione specificato

Codice errore: FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling

Messaggio di errore: Backup o ripristino della condivisione file non riuscito a causa della limitazione del servizio di archiviazione. Questo problema potrebbe dipendere dal fatto che il servizio di archiviazione sta già elaborando altre richieste per l'account di archiviazione specificato.

Provare l'operazione di backup/ripristino in un secondo momento.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound- Condivisione file di destinazione non trovata

Codice errore: TargetFileShareNotFound

Messaggio di errore: Condivisione file di destinazione non trovata.

- Assicurarsi che l'account di archiviazione selezionato esista e che la condivisione file di destinazione non sia stata eliminata.

- Assicurarsi che l'account di archiviazione sia un account di archiviazione supportato per il backup della condivisione file.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked- I processi di backup o ripristino non sono riusciti perché l'account di archiviazione è in stato bloccato

Codice errore: UserErrorStorageAccountIsLocked

Messaggio di errore: I processi di backup o ripristino non sono riusciti a causa del blocco dell'account di archiviazione.

Rimuovere il blocco sull'account di archiviazione o usare **il blocco di** eliminazione anziché il blocco di **lettura** e ripetere l'operazione di backup o ripristino.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached: il ripristino non è riuscito perché il numero di file non riusciti supera la soglia

Codice errore: DataTransferServiceCoFLimitReached

Messaggio di errore: Recupero non riuscito perché il numero di file non riusciti supera la soglia.

- I motivi dell'errore di ripristino sono elencati in un file (percorso specificato nei dettagli del processo). Risolvere gli errori e ripetere l'operazione di ripristino solo per i file non riusciti.

- Motivi comuni per gli errori di ripristino dei file:

  - I file non riusciti sono attualmente in uso
  - nella directory padre esiste una directory con lo stesso nome del file non riuscito.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover: ripristino non riuscito perché non è stato possibile recuperare alcun file

Codice errore: DataTransferServiceAllFilesFailedToRecover

Messaggio di errore: Ripristino non riuscito perché non è stato possibile recuperare alcun file.

- I motivi dell'errore di ripristino sono elencati in un file (percorso specificato nei dettagli del processo). Risolvere gli errori e ripetere le operazioni di ripristino solo per i file con errori.

- Motivi comuni per gli errori di ripristino dei file:

  - I file non riusciti sono attualmente in uso
  - nella directory padre esiste una directory con lo stesso nome del file non riuscito.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid: il ripristino non riesce perché uno dei file nell'origine non esiste

Codice errore: DataTransferServiceSourceUriNotValid

Messaggio di errore: Il ripristino non riesce perché uno dei file nell'origine non esiste.

- Gli elementi selezionati non sono presenti nei dati del punto di ripristino. Per ripristinare i file, fornire l'elenco di file corretto.
- Lo snapshot di condivisione file che corrisponde al punto di ripristino è stato eliminato manualmente. Selezionare un altro punto di ripristino e ripetere l'operazione di ripristino.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked: è in corso un processo di ripristino nella stessa destinazione

Codice errore: UserErrorDTSDestLocked

Messaggio di errore: è in corso un processo di ripristino nella stessa destinazione.

- Il backup della condivisione file non supporta il ripristino parallelo nella stessa condivisione file di destinazione.

- Attendere che termini il ripristino in corso e quindi riprovare. Se non è possibile trovare un processo di ripristino nell'insieme di credenziali di Servizi di ripristino, controllare altri insiemi di credenziali di Servizi di ripristino nella stessa sottoscrizione.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull: operazione di ripristino non riuscita perché la condivisione file di destinazione è piena

Codice errore: UserErrorTargetFileShareFull

Messaggio di errore: Operazione di ripristino non riuscita perché la condivisione file di destinazione è piena.

Aumentare la quota delle dimensioni della condivisione file di destinazione per contenere i dati di ripristino e ripetere l'operazione di ripristino.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient: la condivisione file di destinazione non ha una quota di dimensioni di archiviazione sufficiente per il ripristino

Codice errore: UserErrorTargetFileShareQuotaNotSufficient

Messaggio di errore: La condivisione file di destinazione non ha una quota di dimensioni di archiviazione sufficiente per il ripristino

Aumentare la quota delle dimensioni della condivisione file di destinazione per contenere i dati di ripristino e ripetere l'operazione

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>Sincronizzazione file'operazione di ripristino PreRestoreFailed non è riuscita perché si è verificato un errore durante l'esecuzione di operazioni di pre-ripristino nelle risorse del servizio Sincronizzazione file associate alla condivisione file di destinazione

Codice errore: Sincronizzazione file PreRestoreFailed

Messaggio di errore: Operazione di ripristino non riuscita perché si è verificato un errore durante l'esecuzione di operazioni di pre-ripristino Sincronizzazione file risorse del servizio associate alla condivisione file di destinazione.

Provare a ripristinare i dati in un secondo momento. Se il problema persiste, contattare il supporto tecnico Microsoft.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress: Sincronizzazione file di Azure rilevamento delle modifiche del servizio è in corso per la condivisione file di destinazione. Il rilevamento delle modifiche è stato attivato da un ripristino precedente nella condivisione file di destinazione

Codice errore: AzureFileSyncChangeDetectionInProgress

Messaggio di errore: Sincronizzazione file di Azure rilevamento delle modifiche del servizio è in corso per la condivisione file di destinazione. Il rilevamento delle modifiche è stato attivato da un ripristino precedente nella condivisione file di destinazione.

Usare una condivisione file di destinazione diversa. In alternativa, è possibile attendere il completamento Sincronizzazione file di Azure rilevamento delle modifiche del servizio per la condivisione file di destinazione prima di ritentare il ripristino.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored: non è stato possibile recuperare correttamente uno o più file. Per altre informazioni, controllare l'elenco dei file non riusciti nel percorso indicato in precedenza

Codice errore: UserErrorAFSRecoverySomeFilesNotRestored

Messaggio di errore: Impossibile recuperare correttamente uno o più file. Per altre informazioni, vedere l'elenco di file con errori nel percorso sopra specificato.

- I motivi dell'errore di ripristino sono elencati nel file (percorso specificato nei dettagli del processo). Risolvere i motivi e ripetere l'operazione di ripristino solo per i file non riusciti.
- Motivi comuni per gli errori di ripristino dei file:

  - I file non riusciti sono attualmente in uso
  - nella directory padre esiste una directory con lo stesso nome del file non riuscito.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound: impossibile trovare lo snapshot della condivisione file di Azure corrispondente al punto di ripristino

Codice errore: UserErrorAFSSourceSnapshotNotFound

Messaggio di errore: Impossibile trovare lo snapshot della condivisione file di Azure corrispondente al punto di ripristino

- Verificare che lo snapshot della condivisione file, corrispondente al punto di ripristino che si sta tentando di usare per il ripristino, esista ancora.

  >[!NOTE]
  >Se si elimina uno snapshot di condivisione file creato da Backup di Azure, i punti di ripristino corrispondenti diventano inutilizzabili. È consigliabile non eliminare gli snapshot per garantire il ripristino garantito.

- Provare a selezionare un altro punto di ripristino per ripristinare i dati.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget: è in corso un altro processo di ripristino nella stessa condivisione file di destinazione

Codice errore: UserErrorAnotherRestoreInProgressOnSameTarget

Messaggio di errore: È in corso un altro processo di ripristino nella stessa condivisione file di destinazione

Usare una condivisione file di destinazione diversa. In alternativa, è possibile annullare o attendere il completamento dell'altro ripristino.

## <a name="common-modify-policy-errors"></a>Errori comuni dei criteri di modifica

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation: è in corso un'altra operazione di configurazione della protezione per questo elemento

Codice errore: BMSUserErrorConflictingProtectionOperation

Messaggio di errore: È in corso un'altra operazione di configurazione della protezione per questo elemento.

Attendere il completamento dell'operazione precedente di modifica dei criteri e riprovare in un secondo momento.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked- È in corso un'altra operazione sull'elemento selezionato

Codice errore: BMSUserErrorObjectLocked

Messaggio di errore: È in corso un'altra operazione sull'elemento selezionato.

Attendere il completamento dell'altra operazione in corso e riprovare in un secondo momento.

## <a name="common-soft-delete-related-errors"></a>Errori comuni correlati all'eliminazione soft

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState: questo punto di ripristino non è disponibile perché lo snapshot associato a questo punto si trova in una condivisione file in stato di eliminazione soft

Codice errore: UserErrorRestoreAFSInSoftDeleteState

Messaggio di errore: Questo punto di ripristino non è disponibile perché lo snapshot associato a questo punto si trova in una condivisione file in stato di eliminazione soft.

Non è possibile eseguire un'operazione di ripristino quando la condivisione file è in stato di eliminazione soft. Annullare l'eliminazione della condivisione file dal portale file o usando lo script Annulla [eliminazione](scripts/backup-powershell-script-undelete-file-share.md) e quindi provare a eseguire il ripristino.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState: i punti di ripristino elencati non sono disponibili perché la condivisione file associata contenente gli snapshot del punto di ripristino è stata eliminata definitivamente

Codice errore: UserErrorRestoreAFSInDeleteState

Messaggio di errore: I punti di ripristino elencati non sono disponibili perché la condivisione file associata contenente gli snapshot del punto di ripristino è stata eliminata definitivamente.

Controllare se la condivisione file di cui è stato eseguito il backup è stata eliminata. Se era in stato di eliminazione soft, controllare se il periodo di conservazione dell'eliminazione soft è stato finito e non è stato ripristinato. In uno di questi casi, si perderanno tutti gli snapshot in modo permanente e non sarà possibile ripristinare i dati.

>[!NOTE]
> È consigliabile non eliminare la condivisione file di cui è stato eseguito il backup o, se è in stato di eliminazione soft, annullare l'eliminazione prima della fine del periodo di conservazione dell'eliminazione soft, per evitare di perdere tutti i punti di ripristino.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState: il backup non è riuscito perché la condivisione file di Azure è in stato di eliminazione soft

Codice errore: UserErrorBackupAFSInSoftDeleteState

Messaggio di errore: Il backup non è riuscito perché la condivisione file di Azure è in stato eliminato definitivamente

Annullare l'eliminazione della condivisione file dal portale **file** o usando lo [script Annulla](scripts/backup-powershell-script-undelete-file-share.md) eliminazione per continuare il backup e impedire l'eliminazione permanente dei dati.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState: backup non riuscito perché la condivisione file di Azure associata viene eliminata definitivamente

Codice errore: UserErrorBackupAFSInDeleteState

Messaggio di errore: Backup non riuscito perché la condivisione file di Azure associata viene eliminata definitivamente

Controllare se la condivisione file di cui è stato eseguito il backup viene eliminata definitivamente. In caso affermativa, arrestare il backup per la condivisione file per evitare errori di backup ripetuti. Per informazioni su come arrestare la protezione, vedere [Arrestare la protezione per la condivisione file di Azure](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul backup delle condivisioni file di Azure, vedere:

- [Eseguire il backup di condivisioni file di Azure](backup-afs.md)
- [Domande frequenti sul backup di condivisioni file di Azure](backup-azure-files-faq.yml)
