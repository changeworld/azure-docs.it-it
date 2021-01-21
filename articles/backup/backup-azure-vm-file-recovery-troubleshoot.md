---
title: Risolvere i problemi di ripristino del file di VM di Azure
description: Risolvere i problemi relativi al ripristino di file e cartelle da un backup di VM di Azure.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: aec69b91ad1dae5864e5e8fba61c53e6d15887f4
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624507"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Risolvere i problemi relativi al ripristino dei file di un backup di una macchina virtuale di Azure

Questo articolo illustra i passaggi per la risoluzione dei problemi che consentono di risolvere i problemi relativi al ripristino di file e cartelle da un backup di macchine virtuali (VM) di Azure.

## <a name="common-error-messages"></a>Messaggi di errore comuni

Questa sezione illustra la procedura per risolvere i problemi relativi ai messaggi di errore che potrebbero essere visualizzati.

### <a name="exception-caught-while-connecting-to-target"></a>"Eccezione rilevata durante la connessione alla destinazione"

**Possibili cause**: lo script non è in grado di accedere al punto di ripristino.

**Azione consigliata**: per risolvere il problema, attenersi alla procedura riportata nell' [esecuzione dello script, ma la connessione non è riuscita](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"La destinazione è già stata registrata tramite una sessione iSCSI"

**Possibili cause**: lo script è già stato eseguito nello stesso computer e le unità sono state collegate.

**Azione consigliata**: i volumi del punto di ripristino sono già stati collegati. Non possono essere montati con le stesse lettere di unità della macchina virtuale originale. Esplorare i volumi disponibili in Esplora file.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"Lo script non è valido perché i dischi sono stati smontati tramite il portale o hanno superato il limite di 12 ore. Scaricare un nuovo script dal portale "

**Possibili cause**: i dischi sono stati smontati dal portale o è stato superato il limite di 12 ore.

**Azione consigliata**: 12 ore dopo il download dello script, questo diventa non valido e non può essere eseguito. Passare al portale e quindi scaricare un nuovo script per continuare con il ripristino del file.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>non è possibile caricare il modulo iscsi_tcp (o) iscsi_tcp_module non è stato trovato

**Azione consigliata**: per risolvere questo problema, seguire la procedura descritta in [download dello script, ma non è possibile eseguirlo correttamente](#the-script-downloads-successfully-but-fails-to-run).

## <a name="common-problems"></a>Problemi comuni

In questa sezione vengono illustrati i passaggi per risolvere i problemi comuni che possono verificarsi durante il download e l'esecuzione dello script per il ripristino del file.

### <a name="you-cant-download-the-script"></a>Non è possibile scaricare lo script

1. Assicurarsi di disporre delle [autorizzazioni necessarie per scaricare lo script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
1. Verificare la connessione agli indirizzi IP di destinazione di Azure. Eseguire uno dei comandi seguenti da un prompt dei comandi con privilegi elevati:

   `nslookup download.microsoft.com`

    oppure

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>Lo script viene scaricato correttamente, ma non viene eseguito

Quando si esegue lo script Python per il ripristino a livello di elemento (ILR) in SUSE Linux Enterprise Server 12 SP4, l'errore ha esito negativo con l'errore "Impossibile caricare il modulo iscsi_tcp" oppure "iscsi_tcp_module non trovato".

**Possibili cause**: il modulo ILR Usa **iscsi_tcp** per stabilire una connessione TCP al servizio di backup. Come parte della versione SLES 12 SP4, SUSE ha rimosso **iscsi_tcp** dal pacchetto Open-iSCSI, quindi l'operazione ILR ha esito negativo.

**Azione consigliata**: l'esecuzione di script di ripristino file non è supportata nelle VM SUSE 12 SP4. Provare l'operazione di ripristino in una versione precedente di SUSE 12 SP4.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>Lo script viene eseguito ma la connessione alla destinazione iSCSI non è riuscita

Potrebbe essere visualizzato il messaggio di errore "eccezione intercettata durante la connessione al target".

1. Verificare che il computer in cui viene eseguito lo script soddisfi i [requisiti di accesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
1. Verificare la connessione agli indirizzi IP di destinazione di Azure. Eseguire uno dei comandi seguenti da un prompt dei comandi con privilegi elevati:

   `nslookup download.microsoft.com`

   oppure

   `ping download.microsoft.com`
1. Verificare l'accesso alla porta 3260 in uscita iSCSI.
1. Verificare la presenza di un firewall o NSG che blocca il traffico verso gli indirizzi IP di destinazione di Azure o gli URL del servizio
1. Verificare che il software antivirus non blocchi l'esecuzione dello script.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>Si è connessi al punto di ripristino, ma i dischi non sono stati collegati

Risolvere questo problema attenendosi alla procedura per il sistema operativo in uso.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>Il ripristino dei file di Windows non riesce nel server con pool di archiviazione

Quando si esegue lo script per la prima volta in Windows Server 2012 R2 e Windows Server 2016 (con pool di archiviazione), il pool di archiviazione potrebbe essere collegato alla macchina virtuale in sola lettura.

>[!Tip]
> Assicurarsi di disporre del [computer corretto per l'esecuzione dello script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Per risolvere questo problema, assegnare manualmente l'accesso in lettura/scrittura al pool di archiviazione e alleghi i dischi virtuali:

1. Passare a **Server Manager** i  >  **volumi dei servizi file e archiviazione**  >    >  **pool di archiviazione**.

   ![Screenshot che mostra le opzioni di pool di archiviazione.](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. Nella finestra **pool di archiviazione** fare clic con il pulsante destro del mouse sul pool di archiviazione disponibile e selezionare **imposta Read-Write accesso**.

   ![Screenshot che mostra le opzioni del pulsante destro del mouse per uno spool di archiviazione.](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Quando al pool di archiviazione viene assegnato l'accesso in lettura e scrittura, fare clic con il pulsante destro del mouse nella sezione **dischi virtuali** , quindi scegliere **Connetti disco virtuale**.

   ![Screenshot che mostra le opzioni di clic con il pulsante destro del mouse su un disco virtuale.](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>Il ripristino del file di Linux non riesce a montare automaticamente perché il disco non contiene volumi

Durante l'esecuzione del ripristino dei file, il servizio di backup rileva i volumi e i montaggi automatici. Tuttavia, se i dischi di cui è stato eseguito il backup hanno partizioni non elaborate, questi dischi non sono montati automaticamente e non è possibile visualizzare il disco dati per il ripristino.

Per risolvere questo problema, passare a [ripristinare i file dal backup della macchina virtuale di Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>Il ripristino del file Linux non riesce perché il sistema operativo non è riuscito a identificare il file system

Quando si esegue lo script di ripristino del file, il disco dati non riesce a connettersi. Viene visualizzato un errore che indica che non è stato possibile montare le partizioni seguenti perché il sistema operativo non è riuscito a identificare il file System.

Per risolvere questo problema, controllare se il volume è crittografato con un'applicazione di terze parti. Se è crittografato, il disco o la macchina virtuale non verranno visualizzati come crittografati nel portale.

1. Accedere alla macchina virtuale di cui è stato eseguito il backup ed eseguire questo comando:

   `lsblk -f`

   ![Screenshot che mostra i risultati del comando per elencare i dispositivi a blocchi.](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. Verificare il file system e la crittografia. Se il volume è crittografato, il ripristino dei file non è supportato. Altre informazioni sono disponibili nella [matrice di supporto per il backup delle macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>I dischi sono collegati, ma i volumi non sono montati

Risolvere questo problema attenendosi alla procedura per il sistema operativo in uso.

#### <a name="windows"></a>Windows

Quando si esegue lo script di ripristino del file per Windows, viene visualizzato il messaggio "0 volumi di ripristino allegati". Tuttavia, i dischi vengono individuati nella console Gestione disco.

**Possibili cause**: quando si collegano i volumi tramite iSCSI, alcuni volumi rilevati sono andati offline. Quando il canale iSCSI comunica tra la macchina virtuale e il servizio, rileva questi volumi e li porta online, ma non sono montati.

   ![Screenshot che mostra i volumi di ripristino 0 collegati.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Per identificare e risolvere il problema, seguire questa procedura:

>[!Tip]
>Assicurarsi di disporre del [computer corretto per l'esecuzione dello script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

1. Nella finestra **cmd** eseguire **diskmgmt** per aprire **Gestione disco**.
1. Cercare eventuali dischi aggiuntivi. Nell'esempio seguente, il **disco 2** è un disco aggiuntivo.

   ![Screenshot della finestra di gestione disco con disco aggiuntivo.](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Fare clic con il pulsante destro del mouse su **nuovo volume**, quindi scegliere **Cambia lettera e percorso di unità**.

   ![Screenshot che mostra le opzioni di clic con il pulsante destro del mouse sul disco aggiuntivo.](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. Nella finestra **modifica lettera di unità o percorso** selezionare **assegna la lettera di unità seguente**, assegnare un'unità disponibile, quindi fare clic su **OK**.

   ![Screenshot della finestra Cambia lettera o percorso dell'unità.](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Aprire Esplora file per visualizzare l'unità scelta ed esplorare i file.

#### <a name="linux"></a>Linux

>[!Tip]
>Assicurarsi di disporre del [computer corretto per l'esecuzione dello script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Se la VM Linux protetta usa LVM o array RAID, seguire la procedura descritta in [ripristinare i file dal backup della macchina virtuale di Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>Non è possibile copiare i file dai volumi montati

La copia potrebbe non riuscire con l'errore "0x80070780: Impossibile accedere al file dal sistema". 

Controllare se nel server di origine è abilitata la deduplicazione disco. In caso contrario, verificare che nel server di ripristino sia abilitata anche la deduplicazione sulle unità. È possibile lasciare non configurata la deduplicazione in modo che non vengano deduplicate le unità nel server di ripristino.

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare i file e le cartelle dal backup della macchina virtuale di Azure](backup-azure-restore-files-from-vm.md)
