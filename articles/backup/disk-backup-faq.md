---
title: Domande frequenti sul backup su disco di Azure
description: Risposte alle domande frequenti su backup dischi di Azure
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7729bc1120fc0e2f4361739a8e05f3a82ccb4268
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107313"
---
# <a name="frequently-asked-questions-about-azure-disk-backup"></a>Domande frequenti sul backup su disco di Azure

Questo articolo fornisce le risposte alle domande frequenti su backup dischi di Azure. Per ulteriori informazioni sulla disponibilità dell'area di [backup su disco di Azure](disk-backup-overview.md) , sulle limitazioni e sugli scenari supportati, vedere la matrice di [supporto](disk-backup-support-matrix.md).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>È possibile eseguire il backup del disco usando la soluzione di backup su disco di Azure se viene eseguito il backup dello stesso disco con il backup della macchina virtuale di Azure?

Backup di Azure offre il supporto affiancato per il backup del disco gestito tramite il backup su disco e le soluzioni di [backup delle VM di Azure](backup-azure-vms-introduction.md) . Questa operazione è utile quando è necessario un backup coerente dell'applicazione una volta al giorno per le macchine virtuali e anche backup più frequenti del disco del sistema operativo o di un disco dati specifico, che sono coerenti con l'arresto anomalo del sistema senza influito sulle prestazioni dell'applicazione di produzione.

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>Ricerca per categorie trovare il gruppo di risorse snapshot utilizzato per configurare il backup per un disco?

Nella schermata **istanza di backup** è possibile trovare il campo gruppo di risorse snapshot **nella sezione informazioni** di base. È possibile cercare e selezionare l'istanza di backup del disco corrispondente da backup Center o dall'insieme di credenziali per il backup.

![Campo gruppo di risorse snapshot](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>Che cos'è un gruppo di risorse snapshot?

Il backup su disco di Azure offre un backup del livello operativo per il disco gestito. Ovvero gli snapshot creati durante le operazioni di backup pianificati e su richiesta vengono archiviati in un gruppo di risorse all'interno della sottoscrizione. Backup di Azure offre il ripristino immediato perché gli snapshot incrementali vengono archiviati all'interno della sottoscrizione. Questo gruppo di risorse è noto come gruppo di risorse snapshot. Per ulteriori informazioni, vedere [Configure backup](backup-managed-disks.md#configure-backup).

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>Perché il gruppo di risorse snapshot deve trovarsi nella stessa sottoscrizione di quella del disco di cui viene eseguito il backup?

Non è possibile creare uno snapshot incrementale per un disco specifico al di fuori della sottoscrizione del disco. Quindi, scegliere il gruppo di risorse nella stessa sottoscrizione del disco di cui eseguire il backup. Altre informazioni sullo [snapshot incrementale](../virtual-machines/disks-incremental-snapshots.md#restrictions) per Managed Disks.

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>Perché è necessario fornire le assegnazioni di ruolo per poter configurare i backup, eseguire backup pianificati e su richiesta e operazioni di ripristino?

Il backup su disco di Azure usa l'approccio con privilegi minimi per individuare, proteggere e ripristinare i dischi gestiti nelle sottoscrizioni. Per ottenere questo risultato, backup di Azure usa l'identità gestita dell'insieme di credenziali per il [backup](backup-vault-overview.md) per accedere ad altre risorse di Azure. Un'identità gestita assegnata dal sistema è limitata a una per risorsa ed è associata al ciclo di vita di questa risorsa. È possibile concedere le autorizzazioni all'identità gestita usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Identità gestita è un'entità servizio di un tipo speciale che può essere usata solo con le risorse di Azure. Vedere altre informazioni sulle [identità gestite](../active-directory/managed-identities-azure-resources/overview.md). Per impostazione predefinita, l'insieme di credenziali per il backup non è autorizzato ad accedere al disco di cui eseguire il backup, creare snapshot periodici, eliminare snapshot dopo il periodo di conservazione e ripristinare un disco da un backup. Concedendo in modo esplicito le assegnazioni di ruolo all'identità gestita dell'insieme di credenziali di backup, è possibile controllare la gestione delle autorizzazioni per le risorse nelle sottoscrizioni.

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>Perché i criteri di backup limitano la durata del periodo di conservazione?

Il backup su disco di Azure usa snapshot incrementali, che sono limitati a 200 snapshot per disco. Per consentire l'esecuzione di backup su richiesta a parte i backup pianificati, i criteri di backup limitano i backup totali a 180. Altre informazioni sugli [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md#restrictions) per Managed Disks.

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>Come funziona la frequenza di backup oraria e giornaliera nei criteri di backup?

Il backup su disco di Azure offre più backup al giorno. Se sono necessari backup più frequenti, scegliere la frequenza di backup **ogni ora** . I backup sono pianificati in base all'intervallo di **tempo** selezionato. Se ad esempio si seleziona **ogni 4 ore**, i backup vengono eseguiti approssimativamente ogni 4 ore in modo che i backup vengano distribuiti equamente nel giorno. Se una volta al giorno il backup è sufficiente, scegliere la frequenza di backup **giornaliera** . Nella frequenza di backup giornaliera è possibile specificare l'ora del giorno in cui verranno eseguiti i backup. È importante notare che l'ora del giorno indica l'ora di inizio del backup e non l'ora di completamento del backup. Il tempo necessario per completare l'operazione di backup dipende da diversi fattori, tra cui la percentuale di varianza tra backup consecutivi. Tuttavia, backup su disco di Azure è un backup senza agente che usa [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md) che non influiscano sulle prestazioni dell'applicazione di produzione.

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>Perché l'impostazione di ridondanza dell'insieme di credenziali per il backup non si applica ai backup archiviati nel livello operativo (gruppo di risorse snapshot)?

Backup di Azure usa [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md#restrictions) dei dischi gestiti che archiviano solo le modifiche delta sui dischi dopo l'ultimo snapshot in HDD standard archiviazione, indipendentemente dal tipo di archiviazione del disco padre. Per una maggiore affidabilità, per impostazione predefinita gli snapshot incrementali vengono archiviati nell'archiviazione con ridondanza della zona (ZRS) nelle aree che supportano ZRS. Il backup su disco di Azure supporta attualmente backup operativi di dischi gestiti che non copiano i backup nell'archivio dell'insieme di credenziali di backup. Quindi, l'impostazione di ridondanza dell'archiviazione di backup dell'insieme di credenziali per il backup non si applica ai punti di ripristino.

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>È possibile usare il centro di backup per configurare I backup e gestire le istanze di backup per i dischi di Azure?

Sì, il backup dei dischi di Azure è integrato nel [centro di backup](backup-center-overview.md), che fornisce una **singola esperienza di gestione unificata** in Azure per le aziende per la gestione, il monitoraggio, il funzionamento e l'analisi dei backup su larga scala. È anche possibile usare l'insieme di credenziali di backup per eseguire il backup, il ripristino e la gestione delle istanze di backup protette nell'insieme di credenziali.

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>Perché è necessario creare un insieme di credenziali per il backup e non usare un insieme di credenziali di servizi di ripristino?

Un insieme di credenziali di backup è un'entità di archiviazione di Azure che ospita i dati di backup per determinati carichi di lavoro più recenti supportati da backup di Azure. È possibile usare insiemi di credenziali di backup per conservare i dati di backup per vari servizi di Azure, ad esempio i server di database di Azure per PostgreSQL, i dischi di Azure e i carichi di lavoro più recenti supportati da backup di Azure. Gli insiemi di credenziali di backup consentono di organizzare facilmente i dati di backup, riducendo al minimo il sovraccarico di gestione. Per ulteriori informazioni, fare riferimento agli insiemi di credenziali di [backup](./backup-vault-overview.md) .

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>È possibile eseguire il backup del disco e l'insieme di credenziali di backup si trova in sottoscrizioni diverse?

Sì, il disco gestito di origine di cui eseguire il backup e l'insieme di credenziali per il backup possono trovarsi in sottoscrizioni diverse.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>È possibile eseguire il backup del disco e l'insieme di credenziali di backup si trova in aree diverse?

No, attualmente il disco gestito di origine di cui eseguire il backup e l'insieme di credenziali per il backup devono trovarsi nella stessa area.

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>È possibile ripristinare un disco in una sottoscrizione diversa?

Sì, è possibile ripristinare il disco in una sottoscrizione diversa da quella del disco gestito dal codice sorgente da cui viene effettuato il backup.

### <a name="can-i-back-up-multiple-disks-together"></a>È possibile eseguire il backup di più dischi insieme?

No, gli snapshot temporizzati di più dischi collegati a una macchina virtuale non sono supportati. Per ulteriori informazioni, vedere [configurare il backup](backup-managed-disks.md#configure-backup) e per altre informazioni sulle limitazioni, fare riferimento alla [matrice di supporto](disk-backup-support-matrix.md).

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>Quali sono le opzioni disponibili per eseguire il backup dei dischi tra più sottoscrizioni?

Attualmente, l'uso del portale di Azure per configurare il backup dei dischi è limitato a un massimo di 20 dischi dalla stessa sottoscrizione.

### <a name="what-is-a-target-resource-group"></a>Che cos'è un gruppo di risorse di destinazione?

Durante un'operazione di ripristino, è possibile scegliere la sottoscrizione e un gruppo di risorse in cui si desidera ripristinare il disco. Backup di Azure creerà nuovi dischi dal punto di ripristino nel gruppo di risorse selezionato. Questa operazione viene definita gruppo di risorse di destinazione. Si noti che l'identità gestita dell'insieme di credenziali di backup richiede che l'assegnazione di ruolo nel gruppo di risorse di destinazione sia in grado di eseguire correttamente l'operazione di ripristino. Per ulteriori informazioni, vedere la [documentazione di ripristino](restore-managed-disks.md).

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>Quali sono le autorizzazioni usate da backup di Azure durante l'operazione di backup e ripristino?

Di seguito sono riportate le azioni utilizzate nel ruolo **lettore di backup su disco** assegnato sul **disco** di cui eseguire il backup:

"Microsoft. Compute/disks/Read"

"Microsoft. Compute/disks/beginGetAccess/Action"

Di seguito sono riportate le azioni utilizzate nel ruolo **collaboratore snapshot disco** assegnato nel **gruppo di risorse snapshot**:

"Microsoft. Compute/Snapshots/Delete"

"Microsoft. Compute/Snapshots/Write"

"Microsoft. Compute/Snapshots/Read"

"Microsoft. storage/storageAccounts/Write"

"Microsoft. storage/storageAccounts/Read"

"Microsoft. storage/storageAccounts/Delete"

"Microsoft. resources/subscriptions/resourceGroups/Read"

"Microsoft. storage/storageAccounts/listkeys/Action"

"Microsoft. Compute/Snapshots/beginGetAccess/Action"

"Microsoft. Compute/Snapshots/endGetAccess/Action"

"Microsoft. Compute/disks/beginGetAccess/Action"

Di seguito sono riportate le azioni usate nel ruolo **operatore ripristino disco** assegnato nel **gruppo di risorse di destinazione**:

"Microsoft. Compute/disks/Write"

"Microsoft. Compute/disks/Read"

"Microsoft. resources/subscriptions/resourceGroups/Read"

>[!NOTE]
>Le autorizzazioni per questi ruoli possono cambiare in futuro, in base alle funzionalità aggiunte dal servizio backup di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Matrice di supporto per il backup di dischi di Azure](disk-backup-support-matrix.md)