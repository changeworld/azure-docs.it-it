---
title: Ottenere dettagli utilizzando applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Viene fornita una guida per l'esecuzione del comando Details dello strumento applicazione Azure snapshot coerente che è possibile utilizzare con Azure NetApp Files.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 1d6a5488ee761acec57e6a1030bcb17872b09258
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865299"
---
# <a name="obtain-details-using-azure-application-consistent-snapshot-tool-preview"></a>Ottenere i dettagli usando applicazione Azure strumento snapshot coerente (anteprima)

In questo articolo viene fornita una guida per l'esecuzione del comando Details dello strumento applicazione Azure snapshot coerente che è possibile utilizzare con Azure NetApp Files.

## <a name="introduction"></a>Introduzione

Il `azacsnap -c details` comando elenca i dettagli degli snapshot (nome del volume, nome dello snapshot, ora di creazione, commenti, dimensioni dello snapshot) archiviati in tutti i volumi del file di configurazione.  L'output della console può essere incollato in un foglio di calcolo per un'ulteriore analisi.

A causa delle informazioni archiviate nei sistemi back-end, l'output del comando è leggermente diverso quando viene eseguito in **istanze large di Azure** rispetto a **Azure NetApp files**.

## <a name="command-options"></a>Opzioni del comando

Per il `-c details` comando sono disponibili le opzioni seguenti:

- `--details snapshots` Fornisce un elenco di dettagli di base sugli snapshot per ogni volume configurato. Questo comando può essere eseguito nel server primario o in un server nella posizione di ripristino di emergenza. Il comando fornisce le informazioni seguenti suddivise in base a ogni volume contenente gli snapshot:
  - Dimensioni totali degli snapshot in un volume.
  - Per ogni snapshot nel volume, vengono fornite le informazioni seguenti:
  - Nome dello snapshot
  - Ora di creazione
  - Dimensioni dello snapshot
  - Frequenza dello snapshot

- `--details replication` Fornisce informazioni di base sullo stato della replica dal sito di produzione al sito di ripristino di emergenza. Il comando fornisce l'output per consentire il monitoraggio per garantire che la replica avvenga.  Mostra le dimensioni degli elementi replicati. Fornisce inoltre indicazioni se la replica sta richiedendo troppo tempo o se il collegamento è inattivo.

- `[--configfile <config filename>]` è un parametro facoltativo che consente i nomi dei file di configurazione personalizzati.

### <a name="output-of-the-azacsnap--c-details---details-snapshots-command"></a>Output del `azacsnap -c details --details snapshots` comando

L'esempio seguente è stato eseguito in un' **istanza di grandi dimensioni di Azure** e l'output è stato tagliato per brevità.  Tenere inoltre presente che è possibile che alcune righe siano state incapsulate in modo da adattarsi all'output.

```bash
azacsnap -c details --details snapshots
```

```output
List snapshot details called with snapshotFilter ''
#, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
#1, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, hana_hourly.2020-06-17T113043.1586971Z, "Wed Jun 17 11:31:14 2020", "HANA Backup ID: 1592393444174, 702.6MB
#2, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2200.5, "Thu Jul 02 22:01:37 2020", "Backup ID: 1593727205201, 342.3MB
#3, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T220201.5332158Z, "Thu Jul 02 22:03:34 2020", "HANA Backup ID: 1593727322533|azacsnap version: 5.0 Preview (20200617.75879)", 3.27MB
#4, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2205.4, "Thu Jul 02 22:06:36 2020", "Backup ID: 1593727504776, 3.14MB
#5, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T220702.3283669Z, "Thu Jul 02 22:08:37 2020", "HANA Backup ID: 1593727623339|azacsnap version: 5.0 Preview (20200617.75879)", 3.50MB
#6, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2210.3, "Thu Jul 02 22:11:37 2020", "Backup ID: 1593727805216, 2.85MB
#7, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T221201.7013700Z, "Thu Jul 02 22:13:36 2020", "HANA Backup ID: 1593727922724|azacsnap version: 5.0 Preview (20200617.75879)", 3.34MB
#8, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2215.2, "Thu Jul 02 22:16:36 2020", "Backup ID: 1593728104772, 2.73MB
#9, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T221702.2535255Z, "Thu Jul 02 22:18:35 2020", "HANA Backup ID: 1593728223274|azacsnap version: 5.0 Preview (20200617.75879)", 3.39MB
#10, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2220.1, "Thu Jul 02 22:21:37 2020", "Backup ID: 1593728405346, 3.29MB
#11, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T222201.4988618Z, "Thu Jul 02 22:23:36 2020", "HANA Backup ID: 1593728522505|azacsnap version: 5.0 Preview (20200617.75879)", 3.68MB
#12, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2225.0, "Thu Jul 02 22:26:37 2020", "Backup ID: 1593728705321, 2.80MB
#13, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T222702.0521995Z, "Thu Jul 02 22:28:37 2020", "HANA Backup ID: 1593728823058|azacsnap version: 5.0 Preview (20200617.75879)", 1.04MB
, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, , , Size used by Snapshots, 1.05GB
#1, hana_shared_h31_azsollabbl20a31_t210_vol, hana_hourly.2020-06-17T113043.1586971Z, "Wed Jun 17 11:31:12 2020", "HANA Backup ID: 1592393444174, 2.55GB
#2, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2200.5, "Thu Jul 02 22:01:37 2020", "Backup ID: 1593727205201, 4.30MB
#3, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T220201.5332158Z, "Thu Jul 02 22:03:36 2020", "HANA Backup ID: 1593727322533|azacsnap version: 5.0 Preview (20200617.75879)", 8.04MB
#4, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2205.4, "Thu Jul 02 22:06:37 2020", "Backup ID: 1593727504776, 8.12MB
#5, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T220702.3283669Z, "Thu Jul 02 22:08:35 2020", "HANA Backup ID: 1593727623339|azacsnap version: 5.0 Preview (20200617.75879)", 4.28MB
#6, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2210.3, "Thu Jul 02 22:11:37 2020", "Backup ID: 1593727805216, 4.33MB
#7, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T221201.7013700Z, "Thu Jul 02 22:13:34 2020", "HANA Backup ID: 1593727922724|azacsnap version: 5.0 Preview (20200617.75879)", 4.31MB
#8, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2215.2, "Thu Jul 02 22:16:37 2020", "Backup ID: 1593728104772, 4.30MB
#9, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T221702.2535255Z, "Thu Jul 02 22:18:37 2020", "HANA Backup ID: 1593728223274|azacsnap version: 5.0 Preview (20200617.75879)", 7.84MB
#10, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2220.1, "Thu Jul 02 22:21:37 2020", "Backup ID: 1593728405346, 8.09MB
#11, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T222201.4988618Z, "Thu Jul 02 22:23:34 2020", "HANA Backup ID: 1593728522505|azacsnap version: 5.0 Preview (20200617.75879)", 4.34MB
#12, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2225.0, "Thu Jul 02 22:26:37 2020", "Backup ID: 1593728705321, 4.31MB
#13, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T222702.0521995Z, "Thu Jul 02 22:28:35 2020", "HANA Backup ID: 1593728823058|azacsnap version: 5.0 Preview (20200617.75879)", 4.31MB
, hana_shared_h31_azsollabbl20a31_t210_vol, , , Size used by Snapshots, 2.62GB
#1, hana_log_backups_h31_azsollabbl20a31_t210_vol, azacsnap_vesangam_other_test.2020-06-17T113215.4462696Z, "Wed Jun 17 11:32:43 2020", "HANA Log-Backups, 156KB
#2, hana_log_backups_h31_azsollabbl20a31_t210_vol, azacsnap_vesangam_other_test2.2020-06-17T114205.1041364Z, "Wed Jun 17 11:42:35 2020", "HANA Log-Backups, 1.34MB
, hana_log_backups_h31_azsollabbl20a31_t210_vol, , , Size used by Snapshots, 1.49MB
```

> [!NOTE]
> Questo esempio mostra l'output per gli snapshot eseguiti con la versione precedente (v 4.3), nonché gli snapshot eseguiti con la versione più recente (5,0).

Nell'esempio seguente viene restituito l'output per **Azure NetApp files**, si notino le informazioni ridotte con questo comando poiché le informazioni non sono esposte dal servizio.

```bash
azacsnap -c details --details snapshots
```

```output
List snapshot details called with snapshotFilter ''
#, Volume, SnapshotName
#1, HANADATA_P, snapmirror.dd59bda4-d507-11ea-99fc-00a098f31b77_2155201518.2021-03-22_020000
#2, HANADATA_P, quarter_hourly__2021-03-22T020002-3678211Z
#3, HANADATA_P, quarter_hourly__2021-03-22T014502-0716526Z
#4, HANADATA_P, quarter_hourly__2021-03-22T013002-4080651Z
#5, HANADATA_P, quarter_hourly__2021-03-22T011502-8376045Z
#6, HANADATA_P, quarter_hourly__2021-03-22T010002-8810203Z
#7, HANADATA_P, quarter_hourly__2021-03-22T004502-5983306Z
#8, HANADATA_P, quarter_hourly__2021-03-22T003002-1168834Z
#9, HANADATA_P, quarter_hourly__2021-03-22T001501-9781108Z
#10, HANADATA_P, quarter_hourly__2021-03-22T000002-0865483Z
#1, HANASHARED_P, quarter_hourly__2021-03-22T020002-3678211Z
#2, HANASHARED_P, quarter_hourly__2021-03-22T014502-0716526Z
#3, HANASHARED_P, quarter_hourly__2021-03-22T013002-4080651Z
#4, HANASHARED_P, quarter_hourly__2021-03-22T011502-8376045Z
#5, HANASHARED_P, quarter_hourly__2021-03-22T010002-8810203Z
#6, HANASHARED_P, quarter_hourly__2021-03-22T004502-5983306Z
#7, HANASHARED_P, quarter_hourly__2021-03-22T003002-1168834Z
#8, HANASHARED_P, quarter_hourly__2021-03-22T001501-9781108Z
#9, HANASHARED_P, quarter_hourly__2021-03-22T000002-0865483Z
#10, HANASHARED_P, quarter_hourly__2021-03-21T234502-3935816Z
#1, HANALOGBACKUP_P, logs_5mins__2021-03-22T021002-5462356Z
#2, HANALOGBACKUP_P, logs_5mins__2021-03-22T020502-2390356Z
#3, HANALOGBACKUP_P, logs_5mins__2021-03-22T015502-3928726Z
#4, HANALOGBACKUP_P, logs_5mins__2021-03-22T015001-9228768Z
#5, HANALOGBACKUP_P, logs_5mins__2021-03-22T014002-5554548Z
#6, HANALOGBACKUP_P, logs_5mins__2021-03-22T013502-1781392Z
#7, HANALOGBACKUP_P, logs_5mins__2021-03-22T012502-6686004Z
#8, HANALOGBACKUP_P, logs_5mins__2021-03-22T012002-7348198Z
#9, HANALOGBACKUP_P, logs_5mins__2021-03-22T011002-2234413Z
```

### <a name="output-of-the-azacsnap--c-details---details-replication-command"></a>Output del `azacsnap -c details --details replication` comando

Questo comando controlla lo stato della replica di archiviazione dal sito primario al percorso di ripristino di emergenza e *deve* essere eseguito sul **server del sito di ripristino** di emergenza. Questo comando controlla **solo** i volumi nel file di configurazione.

> [!NOTE]
> Questa opzione è disponibile solo nei sistemi di **istanze large di Azure** (HLI).

```bash
azacsnap -c details --details replication
```

```output
Getting replication details for HLI systems
Volume, Link status, Current Replication Activity, Latest Snapshot Replicated, Size of Latest Snapshot Replicated, Current Lag Time between snapshots (HH:MM:SS)
hana_data_h80_mnt00001_t250_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036590.2020-11-02_031000, 7.53MB, 00h 06m 02s
hana_shared_h80_t250_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036594.2020-11-02_031000, 1.64MB, 00h 06m 04s
hana_log_backups_h80_t250_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036635.2020-11-02_031500, 2.32KB, 00h 01m 04s
```

Nell'esempio seguente lo stato di replica è interruppe e, in questo scenario, l'attivazione di ripristino di emergenza potrebbe causare dati incompleti nel sito di ripristino di emergenza.

> [!NOTE]
> Si noti l'avviso nell'esempio seguente sulla replica suddivisa per due volumi.

```bash
azacsnap -c details --details replication
```

```output
Getting replication details for HLI systems
Volume, Link status, Current Replication Activity, Latest Snapshot Replicated, Size of Latest Snapshot Replicated, Current Lag Time between snapshots (HH:MM:SS)
hana_data_h80_sapprdhdb80_mnt00001_t020_xdp, Broken-Off - Contact Microsoft Operations immediately!, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036523.2020-03-12_052000, 2.47MB, 99h 99m 99s  WARNING: replication has not occurred for more than 30 minutes!
hana_log_backups_h80_sapprdhdb80_t020_xdp, Broken-Off - Contact Microsoft Operations immediately!, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036539.2020-03-12_052400, 196.7KB, 99h 99m 99s  WARNING: replication has not occurred for more than 30 minutes!
hana_shared_h80_sapprdhdb80_t020_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036524.2020-04-02_020000, 2.94MB, 00h 04m 55s
```

Questo esempio ha una corretta replica tra il sito primario e il sito di ripristino di emergenza, quindi questi sistemi sono pronti per supportare uno scenario di ripristino di emergenza.

```bash
azacsnap -c details --details replication
```

```output
Getting replication details for HLI systems
Volume, Link status, Current Replication Activity, Latest Snapshot Replicated, Size of Latest Snapshot Replicated, Current Lag Time between snapshots (HH:MM:SS)
hana_data_h80_sapprdhdb80_mnt00001_t020_xdp, Active, Idle, snapmirror.21215d07-2653-11e8-8e4c-00a098af659c_2157387233.2019-04-09_055000, 106.8MB, 00h 09m 45s
hana_log_backups_h80_sapprdhdb80_t020_xdp, Active, Idle, snapmirror.21215d07-2653-11e8-8e4c-00a098af659c_2157387278.2019-04-09_055700, 75.57MB, 00h 02m 45s
hana_shared_h80_sapprdhdb80_t020_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036524.2020-04-02_020000, 2.94MB, 00h 04m 55s
```

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire un backup](azacsnap-cmd-ref-backup.md)
- [Eliminare gli snapshot](azacsnap-cmd-ref-delete.md)
