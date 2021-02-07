---
title: Eseguire il backup di un database di Oracle Database 19C in una macchina virtuale Linux di Azure con RMAN e archiviazione di Azure
description: Informazioni su come eseguire il backup di un database di Oracle Database 19C nell'archiviazione cloud di Azure.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: fce947c43e8559f4ea2a65645805e987a9015d3f
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806274"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Eseguire il backup e il ripristino di un database di Oracle Database 19C in una VM Linux di Azure con archiviazione di Azure

Questo articolo illustra l'uso di archiviazione di Azure come supporto per il backup e il ripristino di un database Oracle in esecuzione in una macchina virtuale di Azure. Si eseguirà il backup del database usando Oracle RMAN in archiviazione file di Azure montata nella macchina virtuale usando il protocollo SMB. L'uso di archiviazione di Azure per i supporti di backup è estremamente conveniente ed efficiente. Tuttavia, per i database di grandi dimensioni, backup di Azure offre una soluzione migliore.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Per eseguire il processo di backup e ripristino, è prima necessario creare una VM Linux con un'istanza installata di Oracle Database 19C. L'immagine del Marketplace attualmente usata per creare la VM è  **Oracle: Oracle-database-19-3: Oracle-database-19-0904: Latest**. Per completare questa esercitazione, attenersi alla procedura descritta nella [Guida introduttiva a Oracle create database](./oracle-database-quick-create.md) per creare un database Oracle.

## <a name="prepare-the-database-environment"></a>Preparare l'ambiente di database

1. Per creare una sessione Secure Shell, SSH con la macchina virtuale, usare il comando seguente. Sostituire l'indirizzo IP e la combinazione di nome host con il valore `publicIpAddress` per la macchina virtuale.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Passa all'utente ***root*** :
 
   ```bash
   sudo su -
   ```
    
3. Aggiungere l'utente Oracle al file ***/etc/sudoers*** :

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Questo passaggio presuppone che si disponga di un'istanza di Oracle (test) in esecuzione in una macchina virtuale denominata *vmoracle19c*.

   Passa all'utente *Oracle* :

   ```bash
   sudo su - oracle
   ```
    
5. Prima di connettersi, è necessario impostare la variabile di ambiente ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   È anche necessario aggiungere la variabile ORACLE_SID al `oracle` file degli utenti `.bashrc` per gli accessi futuri usando il comando seguente:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Avviare il listener Oracle se non è già in esecuzione:
    
   ```bash
   $ lsnrctl start
   ```

    L'output deve essere simile all'esempio seguente:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

7.  Creare il percorso area di ripristino rapido (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Connetti al database:

    ```bash
    sqlplus / as sysdba
    ```

9.  Avviare il database se non è già in esecuzione:

    ```bash
    SQL> startup
    ```

10. Imposta le variabili di ambiente del database per l'area di ripristino rapido:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Verificare che il database sia in modalità di log di archiviazione per abilitare i backup online.
    Controllare prima lo stato dell'archivio dei log:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Se si è in modalità NOARCHIVELOG, eseguire i comandi seguenti in sqlplus:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Creare una tabella per testare le operazioni di backup e ripristino:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Backup in File di Azure

Per eseguire il backup in File di Azure, completare i passaggi seguenti:

1. Configurare l'archiviazione file di Azure.
1. Montare la condivisione file di archiviazione di Azure nella macchina virtuale.
1. Eseguire il backup del database.
1. Ripristinare e recuperare il database.

### <a name="set-up-azure-file-storage"></a>Configurare l'archiviazione file di Azure

In questo passaggio si eseguirà il backup del database Oracle usando Oracle Recovery Manager (RMAN) in archiviazione file di Azure. Le condivisioni file di Azure sono condivisioni file completamente gestite che si trovano nel cloud. È possibile accedervi usando il protocollo SMB (Server Message Block) o il protocollo NFS (Network File System). Questo passaggio illustra la creazione di una condivisione file che usa il protocollo SMB per il montaggio nella macchina virtuale. Per informazioni su come eseguire il montaggio usando NFS, vedere [montare l'archiviazione BLOB usando il protocollo nfs 3,0](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

Quando si monta il File di Azure, si userà `cache=none` per disabilitare la memorizzazione nella cache dei dati della condivisione file. Per assicurarsi che i file creati nella condivisione siano di proprietà dell'utente Oracle, impostare `uid=oracle` `gid=oinstall` anche le opzioni e. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per prima cosa, configurare l'account di archiviazione.

1. Configurare l'archiviazione file nel portale di Azure

    Nella portale di Azure selezionare ***+ Crea una risorsa** _ e cercare e selezionare _ *_account di archiviazione_**
    
    ![Screenshot che mostra dove creare una risorsa e selezionare account di archiviazione.](./media/oracle-backup-recovery/storage-1.png)
    
2. Nella pagina Crea account di archiviazione scegliere il gruppo di risorse esistente ***RG-Oracle** _, assegnare un nome all'account di archiviazione _*_Oracbkup1_*_ e scegliere _*_archiviazione V2 (generalpurpose v2)_*_ per tipo di account. Modificare la replica in _*_archiviazione con ridondanza locale (con ridondanza locale)_*_ e impostare le prestazioni su _ *_standard_* *. Assicurarsi che location sia impostato sulla stessa area di tutte le altre risorse nel gruppo di risorse. 
    
    ![Screenshot che mostra dove scegliere il gruppo di risorse esistente.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Fare clic sulla scheda ***Avanzate** e in file di Azure impostare _*_condivisioni file di grandi dimensioni_*_ su _ *_Enabled_* *. Fare clic su Verifica + crea e quindi su Crea.
    
    ![Screenshot che mostra dove impostare le condivisioni file di grandi dimensioni su abilitato.](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. Quando l'account di archiviazione è stato creato, passare alla risorsa e scegliere ***condivisioni file*** .
    
    ![Screenshot che mostra dove selezionare le condivisioni file.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Fare clic su ***+ condivisione file** _ e nel pannello _*_nuova condivisione file_*_ assegnare un nome alla condivisione file _*_orabkup1_*_. Impostare _*_quota_*_ su _*_10240_*_ GiB e controllare la _*_transazione ottimizzata_*_ come livello. La quota riflette un limite superiore che la condivisione file può raggiungere. Quando si usa l'archiviazione standard, le risorse sono PAYG e non vengono sottoposte a provisioning, quindi l'impostazione su 10 TiB non comporta costi superiori a quelli usati. Se la strategia di backup richiede più spazio di archiviazione, è necessario impostare la quota su un livello appropriato per conservare tutti i backup.   Dopo aver completato il pannello nuova condivisione file, fare clic su _ *_Crea_* *.
    
    ![Screenshot che mostra dove aggiungere una nuova condivisione file.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Al termine della creazione, fare clic su ***orabkup1*** nella pagina Impostazioni condivisione file. 
    Fare clic sulla scheda ***Connetti** _ per aprire il pannello Connetti e quindi fare clic sulla scheda _ *_Linux_**. Copiare i comandi forniti per montare la condivisione file usando il protocollo SMB. 
    
    ![Pagina Aggiungi account di archiviazione](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per configurare l'account di archiviazione e la condivisione file, eseguire i comandi seguenti nell'interfaccia della riga di comando di Azure.

1. Creare l'account di archiviazione nello stesso gruppo di risorse e nella stessa posizione della macchina virtuale:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Creare una condivisione file nell'account di archiviazione con una quota di 10 TiB:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Recuperare la chiave primaria dell'account di archiviazione (key1) necessaria quando si monta la condivisione file nella macchina virtuale:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Montare la condivisione file di archiviazione di Azure nella macchina virtuale

1. Creare il punto di montaggio:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Configurare le credenziali:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   Sostituire `<Your Storage Account Key1>` la chiave dell'account di archiviazione recuperata in precedenza, prima di eseguire il comando seguente:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Modificare le autorizzazioni per il file di credenziali:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Aggiungere il montaggio a/etc/fstab:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Eseguire i comandi per montare la risorsa di archiviazione File di Azure usando il protocollo SMB:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Se viene visualizzato un errore simile al seguente:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   il pacchetto CIFS potrebbe quindi non essere installato nell'host Linux. 
   
   Per verificare se CIS è installato, eseguire il comando seguente:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Se il comando non restituisce alcun output, installare il pacchetto CIFS usando il comando seguente:

   ```bash 
   sudo yum install cifs-utils
   ```

   Eseguire quindi di nuovo il comando di montaggio sopra riportato per montare File di Azure archiviazione.

6. Verificare che la condivisione file sia montata correttamente con il comando seguente:

   ```bash
   df -h
   ```

   L'output deve essere simile al seguente:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Eseguire il backup del database

In questa sezione verrà usato Oracle Recovery Manager (RMAN) per eseguire un backup completo del database e archiviare i log e scrivere il backup come set di backup nella condivisione file di Azure montata in precedenza. 

1. Configurare RMAN per eseguire il backup nel punto di montaggio File di Azure:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Poiché le condivisioni file standard di Azure hanno dimensioni massime del file pari a 1 TiB, le dimensioni delle parti di backup RMAN vengono limitate a 1 TiB. Si noti che le condivisioni file Premium hanno un limite massimo di 4 TiB per le dimensioni del file. Per ulteriori informazioni, vedere [obiettivi di scalabilità e prestazioni file di Azure](../../../storage/files/storage-files-scale-targets.md).

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Confermare i dettagli della modifica della configurazione:

    ```bash
    RMAN> show all;
    ```

4. A questo punto, eseguire il backup. Il seguente comando eseguirà un backup completo del database, inclusi i file di log di archiviazione, come un set di backup in formato compresso:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

A questo punto è stato eseguito il backup del database online usando Oracle RMAN, con il backup che si trova in archiviazione file di Azure. Questo metodo ha il vantaggio di usare le funzionalità di RMAN durante l'archiviazione dei backup nell'archiviazione file di Azure, in cui è possibile accedervi da altre macchine virtuali, utile se è necessario clonare il database.  
    
Quando si usa RMAN e l'archiviazione file di Azure per il backup del database sono disponibili molti vantaggi, l'ora di backup e ripristino è collegata alle dimensioni del database, pertanto per i database di grandi dimensioni può essere necessario molto tempo per queste operazioni.  Un meccanismo di backup alternativo, usando backup di VM coerenti con l'applicazione di backup di Azure, usa la tecnologia snapshot per eseguire i backup, che offre il vantaggio di backup molto rapidi indipendentemente dalle dimensioni del database. L'integrazione con l'insieme di credenziali di servizi di ripristino garantisce l'archiviazione sicura dei backup Oracle Database nell'archiviazione cloud di Azure accessibile da altre VM e aree di Azure. 

### <a name="restore-and-recover-the-database"></a>Ripristinare e recuperare il database

1.  Arrestare l'istanza di Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Rimuovere i file di backup e i backup:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. I comandi seguenti usano RMAN per ripristinare i file di dati mancanti e recuperare il database:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Controllare che il contenuto del database sia stato recuperato completamente:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


Il backup e il ripristino del database di Oracle Database 19C in una macchina virtuale Linux di Azure sono ora completati.

## <a name="delete-the-vm"></a>Eliminare la macchina virtuale

Quando la macchina virtuale non è più necessaria, è possibile usare il comando seguente per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Creare VM a disponibilità elevata](../../linux/create-cli-complete.md)

[Esplorare gli esempi dell'interfaccia della riga di comando di Azure per la distribuzione della VM](../../linux/cli-samples.md)
