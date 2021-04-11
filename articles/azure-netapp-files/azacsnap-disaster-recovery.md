---
title: Ripristino di emergenza con applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Viene illustrato come eseguire il ripristino di emergenza quando si utilizza lo strumento di snapshot coerente applicazione Azure che è possibile utilizzare con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 554fa394179e7cfc5b86a2b50eb754547d137a44
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870390"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Ripristino di emergenza con applicazione Azure strumento di snapshot coerente (anteprima)

Questo articolo illustra come eseguire il ripristino di emergenza quando si usa lo strumento di snapshot coerente applicazione Azure che è possibile usare con Azure NetApp Files.

> [!IMPORTANT]
> Questa operazione si applica solo alle **istanze large di Azure** .

## <a name="introduction"></a>Introduzione

La piattaforma di istanze large di Azure può anche avere un sito di ripristino di emergenza configurato in cui è possibile replicare gli snapshot del volume di archiviazione.  Se gli snapshot sono stati configurati correttamente con una configurazione di questo tipo, è possibile eseguire un ripristino di emergenza in questo sito.  Questo documento è destinato a essere una guida per eseguire il ripristino di emergenza per questa configurazione.

## <a name="prerequisites-for-disaster-recovery-setup"></a>Prerequisiti per la configurazione del ripristino di emergenza

Prima di pianificare il failover di ripristino di emergenza, è necessario soddisfare i prerequisiti seguenti.

- È stato eseguito il provisioning di un nodo di ripristino di emergenza nel sito di ripristino di emergenza. Sono disponibili due opzioni per il ripristino di emergenza. Uno è il ripristino di emergenza normale e altro il ripristino di emergenza multifunzione.
- La replica di archiviazione è funzionante. Il team operativo Microsoft esegue la configurazione della replica di archiviazione al momento del provisioning automatico del ripristino di emergenza. È possibile monitorare la replica di archiviazione usando il comando `azacsnap -c details --details replication` nel sito di ripristino di emergenza.
- Gli snapshot di archiviazione sono stati impostati e configurati nella posizione primaria.
- Si dispone di un'istanza di HANA installata nel sito di ripristino di emergenza per il database primario con lo stesso SID dell'istanza primaria.
- Leggere e comprendere la procedura di failover di ripristino di [emergenza descritta in SAP Hana in istanze large disponibilità elevata e ripristino di emergenza in Azure](../virtual-machines/workloads/sap/hana-failover-procedure.md)
- Gli snapshot di archiviazione sono stati impostati e configurati nella posizione di ripristino di emergenza.
- Un file di configurazione (ad esempio `DR.json` ) è stato creato con i volumi di archiviazione di ripristino di emergenza e le informazioni associate sul server di ripristino di emergenza.
- Sono stati completati i passaggi nel sito di ripristino di emergenza per:
  - Abilitare la comunicazione con l'archiviazione.
  - Abilitare la comunicazione con SAP HANA.

## <a name="set-up-disaster-recovery"></a>Configurare il ripristino di emergenza

Microsoft supporta la replica a livello di archiviazione per il ripristino di emergenza. Esistono due modi per configurare il ripristino di emergenza.

Uno è **normale** e altro è **multifunzione**. Nel ripristino di emergenza **normale** è presente un'istanza dedicata nella posizione di ripristino di emergenza per il failover. Nello scenario di ripristino di emergenza **multiscopo** , è presente un'altra istanza di controllo di qualità o di sviluppo Hana in esecuzione nell'unità di istanze large di Hana nel sito di ripristino di emergenza. È stata anche installata un'istanza di HANA preinstallata che è inattiva e ha lo stesso SID dell'istanza di HANA di cui si vuole eseguire il failover su un'unità di istanze large di HANA. Microsoft Operations configura l'ambiente per l'utente, inclusa la replica di archiviazione basata sull'input fornito nel modulo di richiesta di servizio (SRF) al momento dell'onboarding.

> [!IMPORTANT]
> Verificare che tutti i prerequisiti siano soddisfatti per l'installazione di ripristino di emergenza.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Monitorare la replica dei dati dal sito primario al sito di ripristino di emergenza

Il team operativo Microsoft ha già gestito e monitorato il collegamento di ripristino di emergenza dal sito primario al sito di ripristino di emergenza.
È possibile monitorare la replica dei dati dal server primario al server di ripristino di emergenza usando il comando snapshot `azacsnap -c details --details replication` .

## <a name="perform-a-failover-to-dr-site"></a>Eseguire un failover al sito di ripristino di emergenza

Eseguire il comando di failover nel sito di ripristino di emergenza ( `azacsnap -c restore --restore revertvolume` ).

> [!IMPORTANT]
> Il `azacsnap -c restore --restore revertvolume` comando interrompe la replica di archiviazione dal sito di produzione al sito di ripristino di emergenza. Per configurare di nuovo la replica, è necessario contattare le operazioni Microsoft. Dopo che la replica è stata riabilitata, tutti i dati nella risorsa di archiviazione di ripristino di emergenza per questo SID vengono inizializzati. Il comando che esegue il failover rende disponibile lo snapshot di archiviazione replicato più di recente. Se è necessario eseguire il ripristino a uno snapshot precedente, aprire una richiesta di supporto in modo che le operazioni possano supportare la creazione di uno snapshot precedente ripristinato nel sito di ripristino di emergenza.

A livello generale, ecco i passaggi da seguire per il failover di ripristino di emergenza:

- È necessario arrestare l'istanza di HANA nel sito **primario** . Questa azione è necessaria solo se si esegue effettivamente il failover nel sito di ripristino di emergenza, in modo che non siano presenti incoerenze dei dati.
- Arrestare l'istanza di HANA nel nodo ripristino di emergenza per il SID di produzione.
- Eseguire il comando `azacsnap -c restore --restore revertvolume` nel nodo di ripristino di emergenza con il SID da ripristinare
  - Il comando interrompe il collegamento di replica di archiviazione dal sito primario al sito di ripristino di emergenza
  - Il comando Ripristina solo il volume/data e/logbackups, il volume/documenti condivisi non viene recuperato, ma usa il/documenti condivisi esistente per SID nella posizione di ripristino di emergenza.
  - Montare il volume/data e/logbackups: assicurarsi di aggiungerlo al file fstab
- Ripristinare lo snapshot SYSTEMDB HANA. HANA Studio Mostra solo la versione più recente dello snapshot HANA disponibile nello snapshot di archiviazione ripristinato come parte dell'esecuzione del comando `azacsnap -c restore --restore revertvolume` .
- Ripristinare il database tenant.
- Avviare l'istanza di HANA nel sito di ripristino di emergenza per il SID di produzione (ad esempio: H80 in questo caso).
- Eseguire i test.

### <a name="example-performing-disaster-recovery"></a>Esempio di esecuzione del ripristino di emergenza

Questa sottosezione descrive i passaggi dettagliati per un failover nel sito di ripristino di emergenza.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>Passaggio 1: ottenere i dettagli del volume del nodo di ripristino di emergenza

Eseguire il comando `df –h` per elencare i filesystem e i volumi associati a cui fare riferimento dopo il failover.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>Passaggio 2: arrestare HANA nel sito primario

Se si esegue un failover completo dei carichi di lavoro di produzione, è possibile connettersi al sito di produzione primario, quindi arrestare le SAP HANA istanze di cui viene eseguito il failover nel ripristino di emergenza.

Se ad esempio si è connessi come root, nell'esempio seguente viene illustrato come SAP HANA possibile arrestare.  Sostituire <sid> con il SID del SAP Hana.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>Passaggio 3: arrestare HANA nel sito di ripristino di emergenza

Prima di ripristinare i volumi, è importante arrestare SAP HANA nel sito di ripristino di emergenza.

Se ad esempio si è connessi come root, nell'esempio seguente viene illustrato come SAP HANA possibile arrestare.  Sostituire <sid> con il SID del SAP Hana.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> Assicurarsi che le istanze di HANA nel sito di ripristino di emergenza siano offline prima di ripristinare i volumi.

#### <a name="step-4-restore-the-volumes"></a>Passaggio 4: ripristinare i volumi

```bash
azacsnap -c restore --restore revertvolume --dbsid H80
```

**_Output del comando di failover di ripristino di_** emergenza.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --dbsid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> Per completare la preparazione dell'archiviazione per un failover di ripristino di emergenza, è necessario eseguire i passaggi alla fine della visualizzazione della console.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>Passaggio 5: smontare i filesystem non necessari

Eseguire il comando `umount` per smontare i volumi o i filesystem non necessari.

```bash
umount <Mount point>
```

Smontare i dati e il backup del log montaggio. Nello scenario con scalabilità orizzontale possono essere presenti più mountpoint di dati.

#### <a name="step-6-configure-the-mount-points"></a>Passaggio 6: configurare i punti di montaggio

Modificare il file `/etc/fstab` per impostare come commento i dati e le voci dei backup del log per il SID primario (in questo esempio, SID = H80) e aggiungere le nuove voci del punto di montaggio create dal sito primario volumi di ripristino di emergenza. Le nuove voci del punto di montaggio vengono fornite nell'output del comando.

- Impostare come commento i punti di montaggio esistenti in esecuzione nel sito di ripristino di emergenza con il `#` carattere:

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Aggiungere le righe seguenti a `/etc/fstab`
  > deve corrispondere all'output del comando

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>Passaggio 7: montare i volumi di ripristino

Eseguire il comando `mount –a` per montare tutti i punti di montaggio.

```bash
mount -a
```

A questo punto, se si esegue, verranno `df –h` visualizzati i `*_dp` volumi montati.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>Passaggio 8: ripristinare il SYSTEMDB

In HANA Studio fare clic con il pulsante destro del mouse su istanza SYSTEMDB e scegliere "backup e ripristino" e quindi "Ripristina database di sistema".

Vedere la guida per ripristinare un database da uno snapshot, in particolare SYSTEMDB.

#### <a name="step-9-recover-the-tenant-database"></a>Passaggio 9: ripristinare il database tenant

In HANA Studio fare clic con il pulsante destro del mouse su istanza SYSTEMDB e scegliere "backup e ripristino" e quindi "Ripristina database tenant".

Vedere la guida per ripristinare un database da uno snapshot, in particolare i database TENANT.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>Eseguire `azacsnap -c backup` nel sito di ripristino di emergenza

Se si eseguono backup basati su snapshot nel sito di ripristino di emergenza, il nome del server HANA configurato nel `azacsnap` file di configurazione nel sito di ripristino di emergenza deve corrispondere al nome del server di produzione.

> [!IMPORTANT]
> L'esecuzione di `azacsnap -c backup` può creare snapshot di archiviazione nel sito di ripristino di emergenza, che non vengono replicati automaticamente in un altro sito.  Collaborare con le operazioni Microsoft per comprendere meglio la restituzione di file o dati al sito di produzione originale.

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere i dettagli dello snapshot](azacsnap-cmd-ref-details.md)
- [Eseguire un backup](azacsnap-cmd-ref-backup.md)
