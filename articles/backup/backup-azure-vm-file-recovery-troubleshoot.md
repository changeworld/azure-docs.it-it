---
title: Risolvere i problemi di ripristino del file di VM di Azure
description: Risolvere i problemi specifici del ripristino di file e cartelle da un backup di VM di Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513340"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Risoluzione dei problemi relativi al ripristino di file del backup di macchine virtuali di Azure

Questo articolo illustra i passaggi per la risoluzione dei problemi che consentono di risolvere gli errori di backup di Azure correlati a problemi specifici del ripristino di file e cartelle da un backup di VM di Azure. 

## <a name="exception-caught-while-connecting-to-target"></a>Eccezione rilevata durante la connessione alla destinazione

Causa possibile: lo script non è in grado di accedere all'azione consigliata per il punto di ripristino: controllare se il computer soddisfa tutti i [requisiti di accesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>La destinazione è già stata registrata tramite una sessione iSCSI

Possibili cause: lo script è già stato eseguito nello stesso computer e le unità sono state collegate.
Azione consigliata: i volumi del punto di ripristino sono già stati collegati. Potrebbero non essere montati con le stesse lettere di unità della macchina virtuale originale. Esplorare tutti i volumi disponibili in Esplora file.

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Questo script non è valido perché è stato necessario smontare i dischi tramite il portale o è stato superato il limite di 12 ore. Scaricare un nuovo script dal portale

Possibili cause: i dischi sono stati smontati dal portale o è stato superato il limite di 12 ore.
Azione consigliata: lo script non è valido dopo 12 ore dal momento in cui è stato scaricato e non può essere eseguito. Visitare il portale e scaricare un nuovo script per procedere con il ripristino del file.

## <a name="troubleshooting-common-issues"></a>Risoluzione dei problemi comuni

In questa sezione vengono illustrati i passaggi per risolvere i problemi riscontrati durante il download e l'esecuzione dello script per il ripristino del file.

## <a name="cannot-download-the-script"></a>Non è possibile scaricare lo script

Azione consigliata:

1. Assicurarsi di disporre di tutte le [autorizzazioni necessarie per scaricare lo script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
2. Verificare che sia disponibile la connettività agli indirizzi IP di destinazione di Azure.
Per verificare la connessione, eseguire il comando seguente da un prompt dei comandi con privilegi elevati. 
    - *nslookup download.Microsoft.com* o
    - *ping download.microsoft.com*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>Lo script viene scaricato correttamente, ma non viene eseguito

### <a name="for-sles-12-sp4-os-linux"></a>Per il sistema operativo SLES 12 SP4 (Linux)

Errore: iscsi_tcp_module non trovato

Possibili cause: durante l'esecuzione dello script Python per il ripristino a livello di elemento in SUSE Linux OS versione 12sp4, l'operazione ha esito negativo con un errore * non è possibile **caricare il modulo _ iscsi_tcp**. Il modulo ILR USA ' iscsi_tcp ' per stabilire la connessione TCP al servizio di backup, SUSE come parte della versione di 12SP4 rimossa iscsi_tcp dal pacchetto Open-iSCSI, di conseguenza l'operazione ILR ha esito negativo.

Azione consigliata: l'esecuzione di script di ripristino file non è supportata nelle macchine virtuali SUSE 12SP4, provare a eseguire l'operazione di ripristino in una versione precedente di SUSE 12SP4.

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>Lo script viene eseguito ma la connessione alla destinazione iSCSI non è riuscita

Errore: è stata rilevata un'eccezione durante la connessione alla destinazione

1. Verificare che il computer in cui viene eseguito lo script soddisfi tutti i [requisiti di accesso](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
2. Verificare la connettività agli indirizzi IP di destinazione di Azure.
Per verificare la connessione, eseguire il comando seguente da un prompt dei comandi con privilegi elevati. 
    - _nslookup download.microsoft.com * o<br>
    - *ping download.microsoft.com*
3. Verificare che sia disponibile l'accesso alla porta iSCSI in uscita 3260.
4. Assicurarsi che non esistano firewall/NSG che bloccano il traffico verso gli indirizzi IP di destinazione di Azure o gli URL del servizio di ripristino.
5. Controllare se l'esecuzione dello script è bloccata da antivirus.

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>Connessione al punto di ripristino, ma i dischi non vengono collegati

Assicurarsi di disporre del [computer appropriato per eseguire lo script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

### <a name="on-windows-vm"></a>Nella macchina virtuale Windows

**Il pool di archiviazione nella macchina virtuale viene collegato in modalità di sola lettura** Per Windows 2012 R2 e Windows 2016 (con pool di archiviazione), durante l'esecuzione dello script per la prima volta, il pool di archiviazione collegato alla macchina virtuale può entrare nello stato di sola lettura.

Per risolvere questo problema, è necessario impostare manualmente il Read-Write l'accesso al pool di archiviazione per la prima volta e alleghi i dischi virtuali, attenersi alla procedura seguente:

1. Impostare l'accesso Read-Write.
Passare a Server Manager > Servizi file e archiviazione > volumi > pool di archiviazione.

   ![Archiviazione di Windows](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. Nella finestra **pool di archiviazione** fare clic con il pulsante destro del mouse sul pool di archiviazione disponibile e selezionare **imposta Read-Write** opzione di accesso.

   ![Lettura/scrittura di archiviazione di Windows](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. Dopo l'assegnazione di un pool di archiviazione con accesso in lettura/scrittura, è necessario associare il disco virtuale.
Passare a **Server Manager interfaccia utente**, nella sezione dischi virtuali > fare clic con il pulsante destro del mouse per selezionare l'opzione **Connetti disco virtuale** .

   ![Disco virtuale di Server Manager](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>In una VM Linux

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>Impossibile montare automaticamente il ripristino del file perché il disco non contiene volumi

Durante l'esecuzione del ripristino dei file, il servizio di backup rileva i volumi e i montaggi automatici. Tuttavia, se i dischi di cui è stato eseguito il backup hanno partizioni non elaborate, questi dischi non sono montati automaticamente e non è possibile visualizzare il disco dati per il ripristino.

Per risolvere questo problema, seguire i passaggi descritti in questo [articolo](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>Il sistema operativo non è riuscito a identificare il file System che ha causato il ripristino del file di Linux durante i dischi

Durante l'esecuzione del disco dati dello script di ripristino file non è stato possibile connettersi con l'errore seguente: *Impossibile montare le seguenti partizioni perché il sistema operativo non* è riuscito a identificare il file System

- Per risolvere questo problema, controllare se il volume è crittografato con un'applicazione di terze parti. Se crittografato, il disco o la macchina virtuale non verranno visualizzati come crittografati nel portale.
1. Accedere alla macchina virtuale di cui è stato eseguito il backup ed eseguire il comando: *lsblk-f*<br>

   ![Disco senza volume](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Verificare il file System e la crittografia.
3. Se il volume è crittografato, il ripristino del file non è supportato. [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>I dischi sono collegati, ma non sono in grado di montare volumi

- Assicurarsi di disporre del [computer corretto per l'esecuzione dello script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="on-windows-vm"></a>Nella macchina virtuale Windows

Durante l'esecuzione dello script di ripristino del file per Windows è presente un messaggio che indica che i **volumi * 0 sono allegati** _, ma i dischi vengono individuati nella console di gestione disco. Quando si connettono i volumi tramite iSCSI, alcuni volumi rilevati passano allo stato offline. Quando il canale iSCSI comunica tra la macchina virtuale e il servizio, rileva questi volumi e li porta online, ma non è montata.

   ![Disco non collegato](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Per identificare e risolvere il problema, seguire questa procedura:

1. Passare a _ *Gestione disco** eseguendo il comando **diskmgmt** nella finestra cmd.
2. Controllare se sono visualizzati altri dischi. Nell'esempio seguente, il disco 2 è un disco aggiuntivo.

   ![Management0 disco](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. Fare clic con il pulsante destro del mouse sul **volume** e scegliere **Cambia lettera e percorso di unità**.

   ![Management1 disco](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. Nella finestra **Aggiungi lettera di unità o percorso** selezionare **assegna la lettera di unità seguente** e assegnare un'unità disponibile e fare clic su **OK**. 

   ![Management2 disco](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. Da Esplora file è possibile visualizzare l'unità.

   ![Management3 disco](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. I nuovi volumi devono essere collegati.  

   ![Il disco non è montato](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. In Esplora file i nuovi volumi saranno visibili dopo la riesecuzione dello script.

   ![Disco non mounting1](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![Disco non mounting2](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>Nelle VM Linux 

- Assicurarsi di disporre del [computer corretto per l'esecuzione dello script](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Se la VM Linux protetta usa LVM e/o array RAID, seguire i passaggi elencati in questo [articolo](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>Non è possibile copiare i file dai volumi montati

La copia potrebbe non riuscire con un errore 0x80070780: Impossibile accedere al file dal sistema. In questo caso, controllare se nel server di origine è abilitata la deduplicazione disco. Assicurarsi quindi che nel server di ripristino sia abilitata anche la deduplicazione sulle unità. È possibile lasciare invariato il ruolo di deduplicazione in modo da non deduplicare le unità nel server di ripristino.
