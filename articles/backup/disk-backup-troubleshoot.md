---
title: Risoluzione degli errori di backup nel backup su disco di Azure
description: Informazioni su come risolvere gli errori di backup nel backup su disco di Azure
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: f6e9bcbbd618cf820ce91f8a3092d5be4aa045cb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107245"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup"></a>Risoluzione degli errori di backup nel backup su disco di Azure

Questo articolo fornisce informazioni sulla risoluzione dei problemi di backup e ripristino affrontati con dischi di Azure. Per ulteriori informazioni sulla disponibilità dell'area di [backup su disco di Azure](disk-backup-overview.md) , sulle limitazioni e sugli scenari supportati, vedere la matrice di [supporto](disk-backup-support-matrix.md).

## <a name="common-issues-faced-with-azure-disk-backup"></a>Problemi comuni riscontrati con il backup su disco di Azure

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Codice di errore: UserErrorSnapshotRGSubscriptionMismatch

Messaggio di errore: sottoscrizione non valida selezionata per l'archivio dati snapshot

Azione consigliata: i dischi e gli snapshot del disco vengono archiviati nella stessa sottoscrizione. È possibile scegliere un gruppo di risorse per archiviare gli snapshot del disco nella sottoscrizione. Selezionare la stessa sottoscrizione del disco di origine. Per ulteriori informazioni, vedere la [matrice di supporto](disk-backup-support-matrix.md).

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Codice di errore: UserErrorSnapshotRGNotFound

Messaggio di errore: non è stato possibile eseguire l'operazione perché il gruppo di risorse archivio dati snapshot non esiste.

Azione consigliata: creare il gruppo di risorse e fornire le autorizzazioni necessarie. Per ulteriori informazioni, vedere [Configure backup](backup-managed-disks.md#configure-backup).

### <a name="error-code-usererrormanageddisknotfound"></a>Codice di errore: UserErrorManagedDiskNotFound

Messaggio di errore: non è stato possibile eseguire l'operazione perché il disco gestito non esiste più.

Azione consigliata: i backup continueranno ad avere esito negativo perché il disco di origine può essere eliminato o spostato in un percorso diverso. Usare il punto di ripristino esistente per ripristinare il disco se è stato eliminato per errore. Se il disco viene spostato in un percorso diverso, configurare il backup per il disco.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Codice di errore: UserErrorNotEnoughPermissionOnDisk

Messaggio di errore: il servizio backup di Azure richiede autorizzazioni aggiuntive sul disco per eseguire questa operazione.

Azione consigliata: concedere all'identità gestita dell'insieme di credenziali di backup le autorizzazioni appropriate per il disco. Per informazioni sulle autorizzazioni richieste dall'identità gestita dell'insieme di credenziali per il backup e su come fornirle, vedere [la documentazione](backup-managed-disks.md) .

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Codice di errore: UserErrorNotEnoughPermissionOnSnapshotRG

Messaggio di errore: per eseguire questa operazione, il servizio backup di Azure richiede autorizzazioni aggiuntive per il gruppo di risorse archivio dati snapshot.

Azione consigliata: concedere all'identità gestita dell'insieme di credenziali di backup le autorizzazioni appropriate per il gruppo di risorse archivio dati snapshot. Il gruppo di risorse archivio dati snapshot è il percorso in cui sono archiviati gli snapshot del disco. Vedere [la documentazione](backup-managed-disks.md) per conoscere il gruppo di risorse, le autorizzazioni necessarie per l'identità gestita dell'insieme di credenziali di backup e come fornirle.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Codice di errore: UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

Messaggio di errore: il disco non valido o il servizio backup di Azure richiede autorizzazioni aggiuntive sul disco per eseguire questa operazione

Azione consigliata: i backup continueranno ad avere esito negativo perché il disco di origine può essere eliminato o spostato in un percorso diverso. Usare il punto di ripristino esistente per ripristinare il disco se è stato eliminato per errore. Se il disco viene spostato in un percorso diverso, configurare il backup per il disco. Se il disco non viene eliminato o spostato, concedere all'identità gestita dell'insieme di credenziali di backup le autorizzazioni appropriate per il disco. Per informazioni sulle autorizzazioni richieste dall'identità gestita dell'insieme di credenziali di backup e su come fornirle, vedere [la documentazione](backup-managed-disks.md) .

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Codice di errore: UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

Messaggio di errore: non è stato possibile eseguire l'operazione perché il gruppo di risorse archivio dati snapshot non esiste più. Per eseguire questa operazione, il servizio backup di Azure richiede autorizzazioni aggiuntive per il gruppo di risorse archivio dati snapshot.

Azione consigliata: creare un gruppo di risorse e concedere all'identità gestita dell'insieme di credenziali di backup le autorizzazioni appropriate per il gruppo di risorse archivio dati snapshot. Il gruppo di risorse archivio dati snapshot è il percorso in cui sono archiviati gli snapshot del disco. Vedere [la documentazione](backup-managed-disks.md) per informazioni sul gruppo di risorse, sulle autorizzazioni richieste dall'identità gestita dell'insieme di credenziali di backup e su come fornirle.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Codice di errore: UserErrorDiskBackupAuthorizationFailed

Messaggio di errore: nell'identità gestita dell'insieme di credenziali per il backup mancano le autorizzazioni necessarie per eseguire questa operazione.

Azione consigliata: concedere all'identità gestita dell'insieme di credenziali di backup le autorizzazioni appropriate per il disco di cui eseguire il backup e nel gruppo di risorse archivio dati snapshot in cui sono archiviati gli snapshot. Per informazioni sulle autorizzazioni richieste dall'identità gestita dell'insieme di credenziali di backup e su come fornirle, vedere [la documentazione](backup-managed-disks.md) .

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Codice di errore: UserErrorSnapshotRGOrMSIPermissionsNotPresent

Messaggio di errore: non è stato possibile eseguire l'operazione perché il gruppo di risorse archivio dati snapshot non esiste più. In alternativa, il servizio backup di Azure richiede autorizzazioni aggiuntive per il gruppo di risorse archivio dati snapshot per eseguire questa operazione.

Azione consigliata: creare il gruppo di risorse e concedere all'identità gestita dell'insieme di credenziali di backup le autorizzazioni appropriate per il gruppo di risorse archivio dati snapshot. Il gruppo di risorse archivio dati snapshot è il percorso in cui sono archiviati gli snapshot. Vedere [la documentazione](backup-managed-disks.md) per informazioni sul gruppo di risorse, sulle autorizzazioni richieste dall'identità gestita dell'insieme di credenziali di backup e su come fornirle.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Codice di errore: UserErrorOperationalStoreParametersNotProvided

Messaggio di errore: non è stato possibile eseguire l'operazione perché il parametro del gruppo di risorse archivio dati snapshot non è stato specificato

Azione consigliata: specificare il parametro del gruppo di risorse archivio dati snapshot. Il gruppo di risorse archivio dati snapshot è il percorso in cui sono archiviati gli snapshot del disco. Per ulteriori informazioni, vedere [la documentazione](backup-managed-disks.md)di.

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Codice di errore: UserErrorInvalidOperationalStoreResourceGroup

Messaggio di errore: il gruppo di risorse archivio dati snapshot specificato non è valido

Azione consigliata: fornire un gruppo di risorse valido per l'archivio dati snapshot. Il gruppo di risorse archivio dati snapshot è il percorso in cui sono archiviati gli snapshot del disco. Per ulteriori informazioni, vedere [la documentazione](backup-managed-disks.md)di.

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Codice di errore: UserErrorDiskBackupDiskTypeNotSupported

Messaggio di errore: tipo di disco non supportato

Azione consigliata: fare riferimento alla [matrice di supporto](disk-backup-support-matrix.md) in scenari e limitazioni non supportati.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Codice di errore: UserErrorSameNameDiskAlreadyExists

Messaggio di errore: non è stato possibile eseguire il ripristino perché un disco con lo stesso nome esiste già nel gruppo di risorse di destinazione selezionato

Azione consigliata: specificare un nome di disco diverso per il ripristino. Per altre informazioni, vedere [Restore Azure Managed disks](restore-managed-disks.md).

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Codice di errore: UserErrorRestoreTargetRGNotFound

Messaggio di errore: l'operazione non è riuscita perché il gruppo di risorse di destinazione non esiste.

Azione consigliata: fornire un gruppo di risorse valido da ripristinare. Per altre informazioni, vedere [Restore Azure Managed disks](restore-managed-disks.md).

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Codice di errore: UserErrorNotEnoughPermissionOnTargetRG

Messaggio di errore: per eseguire questa operazione, il servizio backup di Azure richiede autorizzazioni aggiuntive per il gruppo di risorse di destinazione.

Azione consigliata: concedere all'identità gestita dell'insieme di credenziali di backup le autorizzazioni appropriate per il gruppo di risorse di destinazione. Il gruppo di risorse di destinazione è il percorso selezionato in cui deve essere ripristinato il disco. Per informazioni sulle autorizzazioni richieste dall'identità gestita dell'insieme di credenziali di backup e su come fornirle, vedere la [documentazione di ripristino](restore-managed-disks.md) .

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Codice di errore: UserErrorSubscriptionDiskQuotaLimitReached

Messaggio di errore: l'operazione non è riuscita perché è stato raggiunto il limite massimo di quota del disco per la sottoscrizione.

Azione consigliata: fare riferimento alla [sottoscrizione di Azure e ai limiti del servizio e alla documentazione relativa alle quote](../azure-resource-manager/management/azure-subscription-service-limits.md) oppure contattare supporto tecnico Microsoft per ulteriori istruzioni.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Codice di errore: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

Messaggio di errore: l'operazione non è riuscita perché il gruppo di risorse di destinazione non esiste. Per eseguire questa operazione, il servizio backup di Azure richiede autorizzazioni aggiuntive per il gruppo di risorse di destinazione.

Azione consigliata: fornire un gruppo di risorse valido da ripristinare e concedere all'identità gestita dell'insieme di credenziali di backup le autorizzazioni appropriate per il gruppo di risorse di destinazione. Il gruppo di risorse di destinazione è il percorso selezionato in cui deve essere ripristinato il disco. Per informazioni sulle autorizzazioni richieste dall'identità gestita dell'insieme di credenziali di backup e su come fornirle, vedere la [documentazione di ripristino](restore-managed-disks.md) .

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Codice di errore: UserErrorDESKeyVaultKeyDisabled

Messaggio di errore: la chiave dell'insieme di credenziali delle chiavi usata per la crittografia del disco non è abilitata.

Azione consigliata: abilitare la chiave dell'insieme di credenziali delle chiavi usata per la crittografia del disco. Vedere le limitazioni nella matrice di [supporto](disk-backup-support-matrix.md).

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Codice di errore: UserErrorMSIPermissionsNotPresentOnDES

Messaggio di errore: il servizio backup di Azure richiede l'autorizzazione per accedere al set di crittografia del disco usato con il disco.

Azione consigliata: fornire l'accesso in lettura all'identità gestita dell'insieme di credenziali di backup al set di crittografia del disco (DES).

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Codice di errore: UserErrorDESKeyVaultKeyNotAvailable

Messaggio di errore: la chiave dell'insieme di credenziali delle chiavi usata per la crittografia del disco non è disponibile.

Azione consigliata: assicurarsi che la chiave dell'insieme di credenziali delle chiavi usata per la crittografia del disco non sia disabilitata o eliminata.

### <a name="error-code-usererrordisksnapshotnotfound"></a>Codice di errore: UserErrorDiskSnapshotNotFound

Messaggio di errore: lo snapshot del disco per questo punto di ripristino è stato eliminato.

Azione consigliata: gli snapshot vengono archiviati nel gruppo di risorse archivio dati snapshot nella sottoscrizione. È possibile che lo snapshot correlato al punto di ripristino selezionato sia stato eliminato o spostato da questo gruppo di risorse. Prendere in considerazione l'uso di un altro punto di ripristino da ripristinare. Seguire anche le linee guida consigliate per la scelta del gruppo di risorse snapshot indicato nella [documentazione relativa al ripristino](restore-managed-disks.md).

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Codice di errore: UserErrorSnapshotMetadataNotFound

Messaggio di errore: i metadati dello snapshot del disco per questo punto di ripristino sono stati eliminati

Azione consigliata: prendere in considerazione l'uso di un altro punto di ripristino da ripristinare. Per ulteriori informazioni, vedere la [documentazione di ripristino](restore-managed-disks.md).

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>Codice di errore: BackupAgentPluginHostValidateProtectionError

Messaggio di errore: il backup del disco non è ancora disponibile nell'area dell'insieme di credenziali di backup in cui è in corso il tentativo di configurare la protezione.

Azione consigliata: l'insieme di credenziali per il backup deve trovarsi in un'area supportata. Per la disponibilità dell'area, vedere la [matrice di supporto](disk-backup-support-matrix.md).

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>Codice di errore: UserErrorDppDatasourceAlreadyHasBackupInstance

Messaggio di errore: il disco che si sta tentando di configurare il backup è già protetto. Il disco è già associato a un'istanza di backup in un insieme di credenziali di backup.

Azione consigliata: questo disco è già associato a un'istanza di backup in un insieme di credenziali di backup. Se si vuole proteggere nuovamente questo disco, eliminare l'istanza di backup dall'insieme di credenziali per il backup in cui è attualmente protetta e riproteggere il disco in qualsiasi altro insieme di credenziali.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>Codice di errore: UserErrorDppDatasourceAlreadyProtected

Messaggio di errore: il disco che si sta tentando di configurare il backup è già protetto. Il disco è già associato a un'istanza di backup in un insieme di credenziali di backup.

Azione consigliata: questo disco è già associato a un'istanza di backup in un insieme di credenziali di backup. Se si vuole proteggere nuovamente questo disco, eliminare l'istanza di backup dall'insieme di credenziali per il backup in cui è attualmente protetta e riproteggere il disco in qualsiasi altro insieme di credenziali.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Codice di errore: UserErrorMaxConcurrentOperationLimitReached

Messaggio di errore: Impossibile avviare l'operazione perché è stato raggiunto il numero massimo di backup simultanei consentiti.

Azione consigliata: attendere il completamento del backup in esecuzione precedente.

## <a name="next-steps"></a>Passaggi successivi

- [Matrice di supporto per il backup di dischi di Azure](disk-backup-support-matrix.md)