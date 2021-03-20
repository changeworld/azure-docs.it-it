---
title: Ripristino con applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Viene fornita una guida per l'esecuzione del comando Restore dello strumento applicazione Azure snapshot coerente che è possibile utilizzare con Azure NetApp Files.
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
ms.openlocfilehash: 1c6b7ec6c4ef24ec00fbfc55a65a968e00561c2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632897"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Ripristino con applicazione Azure strumento snapshot coerente (anteprima)

Questo articolo fornisce una guida per l'esecuzione del comando Restore dello strumento di snapshot coerente applicazione Azure che è possibile usare con Azure NetApp Files.

## <a name="introduction"></a>Introduzione

L'operazione di ripristino del volume da uno snapshot viene eseguita con il `azacsnap -c restore` comando.

> [!IMPORTANT]
> Questa operazione non esegue un recupero del database, ma solo un ripristino dei volumi come descritto per ciascuna delle opzioni seguenti.

## <a name="command-options"></a>Opzioni del comando

Per il `-c restore` comando sono disponibili le opzioni seguenti:

- `--restore snaptovol` Crea un nuovo volume basato sullo snapshot più recente nel volume di destinazione. Questo comando crea un nuovo volume "clonato" basato sul volume di destinazione configurato, usando lo snapshot del volume più recente come base per creare il nuovo volume.  Questo comando non interrompe la replica di archiviazione dal database primario a quello secondario. Al contrario, vengono creati cloni dello snapshot disponibile più recente nel sito di ripristino di emergenza e vengono presentati i montaggio di file System consigliati per i volumi clonati. Questo comando deve essere eseguito sul sistema di istanze Large **di Azure nell'area di ripristino di** emergenza, ovvero sul sistema di failover di destinazione.

- `--restore revertvolume` Ripristina lo stato precedente del volume di destinazione in base allo snapshot più recente.  Usare questo comando come parte del failover di ripristino di emergenza nell'area di ripristino di emergenza abbinata. Questo comando **Arresta** la replica di archiviazione dal sito primario al sito secondario e ripristina i volumi di ripristino di emergenza di destinazione nell'ultimo snapshot disponibile nei volumi di ripristino di emergenza insieme al file system consigliato montaggio per i volumi di ripristino di emergenza ripristinati. Questo comando deve essere eseguito sul sistema di istanze Large **di Azure nell'area di ripristino di** emergenza, ovvero sul sistema di failover di destinazione.
    > [!NOTE]
    > Il sottocomando ( `--restore revertvolume` ) è disponibile solo per le istanze large di Azure e non è disponibile per Azure NetApp files.
- `--hanasid <SAP HANA SID>` SID SAP HANA selezionato dal file di configurazione a cui applicare i comandi di ripristino del volume.

- `[--configfile <config filename>]` è un parametro facoltativo che consente i nomi dei file di configurazione personalizzati.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>Eseguire un test del failover di ripristino di emergenza `azacsnap -c restore --restore snaptovol`

Questo comando è simile al comando di failover di ripristino di emergenza "completo" ( `--restore restorevolume` ), ma piuttosto che suddividere la replica tra il sito primario e il sito di ripristino di emergenza, viene creato un volume di clonazione fuori dai volumi di ripristino di emergenza, che consente il ripristino dello snapshot più recente nel sito di ripristino di emergenza. Questi volumi clonati sono quindi utilizzabili dal cliente per testare il ripristino di emergenza senza dover eseguire un failover completo dell'ambiente HANA che interrompe il contratto di replica tra il sito primario e il sito di ripristino di emergenza.

- In questo modo è possibile testare più punti di ripristino diversi, ognuno con il proprio punto di ripristino.
- Il clone viene designato dal timestamp in corrispondenza del momento in cui è stato eseguito il comando e rappresenta i dati più recenti e altri snapshot disponibili quando vengono eseguiti.

> [!IMPORTANT]
> Questa operazione si applica solo alle istanze large di Azure.
>
> - Quando si esegue questo comando, è necessario che l'indirizzo di posta elettronica di contatto per le operazioni si contatti prima dell'eliminazione dei cloni dopo 4 settimane.
> - Ogni esecuzione di questo comando creerà un nuovo clone che deve essere eliminato dalle operazioni Microsoft quando il test viene terminato.
> - Tutti i volumi di clonazione creati verranno eliminati automaticamente dopo 4 settimane.

Il file di configurazione (ad esempio, `DR.json` ) deve contenere solo i volumi di ripristino di emergenza e non i volumi di produzione; in caso contrario, i volumi di produzione potrebbero avere cloni creati.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>Output del `azacsnap -c restore --restore snaptovol` comando (per Single-Node scenario)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --hanasid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> L'output "visualizzazione punti di montaggio per volume" è diverso per i vari scenari.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>Esegui failover di ripristino di emergenza completo `azacsnap -c restore --restore revertvolume`

Questo comando **Arresta** la replica di archiviazione dal sito primario al sito secondario, ripristina lo snapshot più recente nei volumi di ripristino di emergenza e fornisce montaggio per i volumi di ripristino di emergenza.

Questo comando deve essere eseguito nel server di ripristino di emergenza utilizzando un file di configurazione (ad esempio, `DR.json` ) solo con i volumi di ripristino di emergenza.

Eseguire un failover al sito di ripristino di emergenza eseguendo il comando `azacsnap -c restore --restore revertvolume` .  Questo comando richiede l'aggiunta di un SID come parametro. Si tratta del SID dell'istanza HANA, che deve essere ripristinata nel sito di ripristino di emergenza.

> [!IMPORTANT]
> Eseguire questo comando solo se si prevede di eseguire l'esercizio di ripristino di emergenza o un test. Questo comando interrompe la replica. Per riabilitare la replica, è necessario contattare Microsoft Operations.

A livello generale, di seguito sono riportati i passaggi per eseguire un failover di ripristino di emergenza:

- È necessario arrestare l'istanza di HANA nel sito **primario** . Questa azione è necessaria solo se si esegue effettivamente il failover nel sito di ripristino di emergenza per evitare incoerenze dei dati.
- Arrestare l'istanza di HANA nel nodo ripristino di emergenza per il SID di produzione.
- Eseguire il comando `azacsnap -c restore --restore revertvolume` nel nodo di ripristino di emergenza con il SID da ripristinare.
  - Il comando interrompe il collegamento di replica di archiviazione dal sito primario al sito di ripristino di emergenza
  - Il comando Ripristina i volumi "data" e "other" come configurati.  Questa operazione è in genere destinata ai volumi per i `/hana/data` `/hana/logbackups` filesystem e.  Il `/hana/shared` file System non viene recuperato, ma usa il SID esistente `/hana/shared` nella posizione di ripristino di emergenza.
  - Montare i `/hana/data` `/hana/logbackups` volumi e: assicurarsi che siano aggiunti al `/etc/fstab` file
- Ripristinare lo snapshot SYSTEMDB HANA. HANA Studio Mostra solo la versione più recente dello snapshot HANA disponibile nello snapshot di archiviazione ripristinato come parte dell'esecuzione del comando snapshot `azacsnap -c restore --restore revertvolume` .
- Ripristinare il database tenant.
- Avviare l'istanza di HANA nel sito di ripristino di emergenza per il SID di produzione (ad esempio: H80 in questo caso).
- Eseguire qualsiasi test del database.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire un backup](azacsnap-cmd-ref-backup.md)
- [Ottenere i dettagli dello snapshot](azacsnap-cmd-ref-details.md)
