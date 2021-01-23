---
title: Eseguire il backup e il ripristino Active Directory
description: Informazioni su come eseguire il backup e il ripristino di Active Directory controller di dominio.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733554"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Eseguire il backup e il ripristino di Active Directory controller di dominio

Il backup di Active Directory e la garanzia di ripristini riusciti in caso di danneggiamento, compromissione o emergenza costituiscono una parte essenziale del Active Directory manutenzione.

Questo articolo descrive le procedure appropriate per eseguire il backup e il ripristino di Active Directory controller di dominio con backup di Azure, sia che si tratti di macchine virtuali di Azure o server locali. Viene illustrato uno scenario in cui è necessario ripristinare lo stato di un intero controller di dominio al momento del backup. Per visualizzare lo scenario di ripristino appropriato, vedere [questo articolo](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover).  

>[!NOTE]
> Questo articolo non illustra il ripristino di elementi dal [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Per informazioni sul ripristino di Azure Active Directory utenti, vedere [questo articolo](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="best-practices"></a>Procedure consigliate

- Verificare che sia stato eseguito il backup di almeno un controller di dominio. Se si esegue il backup di più controller di dominio, assicurarsi che venga eseguito il backup di tutti i [ruoli FSMO (Flexible Single Master Operation)](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) .

- Eseguire il backup Active Directory di frequente. Il backup non deve mai superare la durata di rimozione definitiva (per impostazione predefinita, 60 giorni), perché gli oggetti anteriori alla durata di rimozione definitiva saranno "contrassegnati per la rimozione definitiva" e non saranno più considerati validi.

- Disporre di un piano di ripristino di emergenza chiaro che include istruzioni su come ripristinare i controller di dominio. Per preparare il ripristino di una foresta di Active Directory, leggere la [Guida al ripristino della foresta Active Directory](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide).

- Se è necessario ripristinare un controller di dominio e un controller di dominio funzionante rimanente nel dominio, è possibile creare un nuovo server anziché eseguire il ripristino da un backup. Aggiungere il ruolo del server **Active Directory Domain Services** al nuovo server per impostarlo come controller di dominio nel dominio esistente. Il Active Directory dati verrà replicato nel nuovo server. Per rimuovere il controller di dominio precedente da Active Directory, seguire la procedura descritta [in questo articolo](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) per eseguire la pulizia dei metadati.

>[!NOTE]
>Backup di Azure non include il ripristino a livello di elemento per Active Directory. Se si desidera ripristinare oggetti eliminati ed è possibile accedere a un controller di dominio, utilizzare il [cestino Active Directory](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt). Se il metodo non è disponibile, è possibile utilizzare il backup del controller di dominio per ripristinare gli oggetti eliminati con lo strumento **ntdsutil.exe** come illustrato [qui](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).
>
>Per informazioni sull'esecuzione di un ripristino autorevole di SYSVOL, vedere [questo articolo](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol).

## <a name="backing-up-azure-vm-domain-controllers"></a>Backup di controller di dominio di macchine virtuali di Azure

Se il controller di dominio è una macchina virtuale di Azure, è possibile eseguire il backup del server usando il [backup delle VM di Azure](backup-azure-vms-introduction.md).

Leggere le [considerazioni operative per i controller di dominio virtualizzati](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) per garantire il completamento dei backup (e i ripristini futuri) dei controller di dominio della VM di Azure.

## <a name="backing-up-on-premises-domain-controllers"></a>Esecuzione del backup dei controller di dominio locali

Per eseguire il backup di un controller di dominio locale, è necessario eseguire il backup dei dati dello stato del sistema del server.

- Se si usa MARS, seguire [queste istruzioni](backup-azure-system-state.md).
- Se si usa MAB (server di Backup di Azure), seguire [queste istruzioni](backup-mabs-system-state-and-bmr.md).

>[!NOTE]
> Il ripristino dei controller di dominio locali (dallo stato del sistema o dalle macchine virtuali) al cloud di Azure non è supportato. Se si vuole che l'opzione di failover da un ambiente di Active Directory locale ad Azure, è consigliabile usare [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md).

## <a name="restoring-active-directory"></a>Ripristino di Active Directory

È possibile ripristinare i dati Active Directory in una delle due modalità seguenti: **autorevole** o non **autorevole**. In un ripristino autorevole i dati Active Directory ripristinati sostituiranno i dati trovati negli altri controller di dominio della foresta.

Tuttavia, in questo scenario viene ricompilato un controller di dominio in un dominio esistente, pertanto è necessario eseguire un ripristino non **autorevole** .

Durante il ripristino, il server verrà avviato in modalità ripristino servizi directory. È necessario fornire la password dell'amministratore per la modalità di ripristino dei servizi directory.

>[!NOTE]
>Se la password della modalità ripristino servizi directory viene dimenticata, è possibile reimpostarla utilizzando [queste istruzioni](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11)).

### <a name="restoring-azure-vm-domain-controllers"></a>Ripristino di controller di dominio di macchine virtuali di Azure

Per ripristinare un controller di dominio di VM di Azure, vedere [ripristinare le macchine virtuali del controller di dominio](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).

Se si esegue il ripristino di una macchina virtuale con un singolo controller di dominio o di più controller di dominio in un singolo dominio, ripristinare tali macchine come qualsiasi altra VM. È anche disponibile Modalità ripristino servizi directory (Directory Services Restore Mode, DSRM), in modo che tutti gli scenari di ripristino di Active Directory siano attuabili.

Se è necessario ripristinare una macchina virtuale con un singolo controller di dominio in una configurazione a più domini, ripristinare i dischi e creare una VM [usando PowerShell](backup-azure-vms-automation.md#restore-the-disks).

Se si ripristina l'ultimo controller di dominio nel dominio o si ripristinano più domini in una foresta, è consigliabile eseguire un [ripristino della foresta](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

>[!NOTE]
> I controller di dominio virtualizzati, da Windows 2012 in poi, usano [misure di sicurezza basate sulla virtualizzazione](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards). Con queste misure di sicurezza, Active Directory riconosce se la macchina virtuale ripristinata è un controller di dominio ed esegue i passaggi necessari per ripristinare i dati Active Directory.

### <a name="restoring-on-premises-domain-controllers"></a>Ripristino di controller di dominio locali

Per ripristinare un controller di dominio locale, seguire le istruzioni riportate in per ripristinare lo stato del sistema in Windows Server, usando le linee guida per [considerazioni speciali sul ripristino dello stato del sistema in un controller di dominio](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller).

## <a name="next-steps"></a>Passaggi successivi

- [Matrice di supporto per Backup di Azure](backup-support-matrix.md)