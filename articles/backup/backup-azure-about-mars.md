---
title: Informazioni sull'agente MARS
description: Informazioni su come l'agente MARS supporta gli scenari di backup
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9e01694aca386482f9ff7ba52593c88326ba3d62
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517743"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informazioni sull Microsoft Azure di Servizi di ripristino di Microsoft (MARS)

Questo articolo descrive come il servizio Backup di Azure usa l'agente di Servizi di ripristino di Microsoft Azure (MARS) per eseguire il backup e il ripristino di file, cartelle e lo stato del volume o del sistema da un computer locale ad Azure.

## <a name="backup-scenarios"></a>Scenari di backup

L'agente MARS supporta gli scenari di backup seguenti:

![Scenari di backup MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **File e cartelle:** proteggere in modo selettivo file e cartelle di Windows.
- **Livello volume:** proteggere un intero volume Windows del computer.
- **Livello di sistema:** consente di proteggere un intero stato del sistema Windows.

### <a name="additional-scenarios"></a>Scenari aggiuntivi

- **Eseguire il backup di** cartelle e file specifici all'interno di macchine virtuali di Azure: il metodo principale per il backup di macchine virtuali di Azure è l'uso di un'estensione Backup di Azure nella macchina virtuale. L'estensione backup l'intera macchina virtuale. Se si vuole eseguire il backup di cartelle e file specifici all'interno di una macchina virtuale, è possibile installare l'agente MARS nelle macchine virtuali di Azure. Per altre informazioni, vedere Architettura: Backup di macchine [virtuali di Azure incorporato.](./backup-architecture.md#architecture-built-in-azure-vm-backup)

- **Seeding offline:** i backup completi iniziali dei dati in Azure trasferiscono in genere grandi quantità di dati e richiedono una maggiore larghezza di banda di rete. I backup successivi trasferiscono solo la quantità di dati differenziale, o incrementale. Backup di Azure comprime i backup iniziali. Tramite il processo di *seeding offline,* Backup di Azure possibile usare i dischi per caricare i dati di backup iniziale compressi offline in Azure. Per altre informazioni, vedere [Backup di Azure offline-backup using Azure Data Box](offline-backup-azure-data-box.md).

## <a name="restore-scenarios"></a>Scenari di ripristino

L'agente MARS supporta gli scenari di ripristino seguenti:

![Scenari di ripristino MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Stesso server:** il server in cui è stato originariamente creato il backup.
  - **File e cartelle:** scegliere i singoli file e cartelle da ripristinare.
  - **Livello volume:** scegliere il volume e il punto di ripristino da ripristinare. Quindi ripristinarlo nello stesso percorso o in un percorso alternativo nello stesso computer.  Creare una copia dei file esistenti, sovrascrivere i file esistenti o ignorare il ripristino dei file esistenti.
  - **Livello di sistema:** scegliere lo stato del sistema e il punto di ripristino da ripristinare nello stesso computer in un percorso specificato.

- **Server alternativo:** un server diverso dal server in cui è stato eseguito il backup.
  - **File e cartelle:** scegliere i singoli file e cartelle di cui si vuole ripristinare il punto di ripristino in un computer di destinazione.
  - **Livello volume:** scegliere il volume e il punto di ripristino da ripristinare in un'altra posizione. Creare una copia dei file esistenti, sovrascrivere i file esistenti o ignorare il ripristino dei file esistenti.
  - **Livello di sistema:** scegliere lo stato del sistema e il punto di ripristino da ripristinare come file dello stato del sistema in un computer alternativo.

## <a name="backup-process"></a>Processo di backup

1. Dal portale di Azure creare un insieme di credenziali di Servizi di ripristino [e](install-mars-agent.md#create-a-recovery-services-vault)scegliere file, cartelle e lo stato del sistema dall'elenco Obiettivi **di backup**.
2. [Scaricare le credenziali dell'insieme di credenziali di Servizi](./install-mars-agent.md#download-the-mars-agent) di ripristino e il programma di installazione dell'agente in un computer locale.

3. [Installare l'agente e](./install-mars-agent.md#install-and-register-the-agent) usare le credenziali dell'insieme di credenziali scaricate per registrare il computer nell'insieme di credenziali di Servizi di ripristino.
4. Dalla console dell'agente nel client, configurare il backup per specificare gli elementi di cui eseguire il [backup,](./backup-windows-with-mars-agent.md#create-a-backup-policy) quando eseguire il backup (pianificazione), per quanto tempo i backup devono essere conservati in Azure (criteri di conservazione) e avviare la protezione.

![Backup di Azure dell'agente](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Informazioni aggiuntive

- Il **backup iniziale** (primo backup) viene eseguito in base alle impostazioni di backup.  L'agente MARS usa VSS per creare uno snapshot tempormente dei volumi selezionati per il backup. L'agente usa solo l'operazione del writer di sistema windows per acquisire lo snapshot. Non usa writer VSS dell'applicazione e non acquisisce snapshot coerenti con l'app. Dopo aver creato lo snapshot con VSS, l'agente MARS crea un disco rigido virtuale (VHD) nella cartella della cache specificata al momento della configurazione del backup. L'agente archivia anche i checksum per ogni blocco di dati.

- **I backup incrementali** (backup successivi) vengono eseguiti in base alla pianificazione specificata. Durante i backup incrementali vengono identificati i file modificati e viene creato un nuovo disco rigido virtuale. Il disco rigido virtuale viene compresso e crittografato e quindi inviato all'insieme di credenziali. Al termine del backup incrementale, il nuovo disco rigido virtuale viene unito al disco rigido virtuale creato dopo la replica iniziale. Questo disco rigido virtuale unito fornisce lo stato più recente da usare per il confronto per il backup in corso.

- L'agente MARS può eseguire il processo di backup **in** modalità ottimizzata usando il journal delle modifiche USN (Update Sequence Number) o **in** modalità non ottimizzata controllando la presenza di modifiche nelle directory o nei file tramite l'analisi dell'intero volume. La modalità non impostata è più lenta perché l'agente deve analizzare ogni file nel volume e confrontarlo con i metadati per determinare i file modificati.  Il **backup iniziale** verrà sempre eseguito in modalità non inizializzata. Se il backup precedente non è riuscito, il processo di backup pianificato successivo verrà eseguito in modalità non inizializzata. Per altre informazioni su queste modalità e su come verificarle, vedere [questo articolo.](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)

## <a name="next-steps"></a>Passaggi successivi

[Matrice di supporto per l'agente MARS](./backup-support-matrix-mars-agent.md)

[Domande frequenti sull'agente MARS](./backup-azure-file-folder-backup-faq.yml)
