---
title: Eseguire il backup e il ripristino di un database di Oracle Database 19C in una VM Linux di Azure con backup di Azure
description: Informazioni su come eseguire il backup e il ripristino di un Oracle Database database 19C usando il servizio backup di Azure.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 3122b1c5d7ac8b9dca0e244a4b7e73a57c4c5fca
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072405"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Eseguire il backup e il ripristino di un database di Oracle Database 19C in una VM Linux di Azure con backup di Azure

Questo articolo illustra l'uso di backup di Azure per creare snapshot del disco dei dischi delle macchine virtuali, inclusi i file di database e l'area di ripristino rapido. Con backup di Azure è possibile eseguire snapshot del disco completi appropriati come backup, archiviati nell'insieme di credenziali di [servizi di ripristino](../../../backup/backup-azure-recovery-services-vault-overview.md).  Backup di Azure fornisce anche backup coerenti con l'applicazione, che assicurano la necessità di correzioni aggiuntive per il ripristino dei dati. Il ripristino di dati coerenti con l'applicazione riduce il tempo di ripristino e consente quindi di tornare rapidamente allo stato operativo.

> [!div class="checklist"]
>
> * Eseguire il backup del database con il backup coerente con l'applicazione
> * Ripristinare e recuperare il database da un punto di ripristino
> * Ripristinare la macchina virtuale da un punto di ripristino

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Per eseguire il processo di backup e ripristino, è prima necessario creare una VM Linux con un'istanza installata di Oracle Database 19C. L'immagine del Marketplace attualmente usata per creare la VM è  **Oracle: Oracle-database-19-3: Oracle-database-19-0904: Latest**. Per completare questa esercitazione, attenersi alla procedura descritta nella [Guida introduttiva a Oracle create database](./oracle-database-quick-create.md) per creare un database Oracle.


## <a name="prepare-the-environment"></a>Preparare l'ambiente

Per preparare l'ambiente, eseguire i passaggi seguenti:

1. Connettersi alla macchina virtuale.
1. Preparare il database.

### <a name="connect-to-the-vm"></a>Connettersi alla VM

1. Per creare una sessione Secure Shell, SSH con la macchina virtuale, usare il comando seguente. Sostituire l'indirizzo IP e la combinazione di nome host con il valore `<publicIpAddress>` per la macchina virtuale.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Passa all'utente *root* :

   ```bash
   sudo su -
   ```
    
1. Aggiungere l'utente Oracle al file */etc/sudoers* :

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>Preparare il database

Questo passaggio presuppone che si disponga di un'istanza di Oracle (*test*) in esecuzione in una macchina virtuale denominata *vmoracle19c*.

1. Passa all'utente *Oracle* :
 
   ```bash
    sudo su - oracle
    ```
    
2. Prima di connettersi, è necessario impostare la variabile di ambiente ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```

    È anche necessario aggiungere la variabile ORACLE_SID al `oracle` file degli utenti `.bashrc` per gli accessi futuri usando il comando seguente:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Avviare il listener Oracle se non è già in esecuzione:

    ```output
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

4.  Creare il percorso area di ripristino rapido (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Connetti al database:

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Avviare il database se non è già in esecuzione:

    ```bash
    SQL> startup
    ```

7.  Imposta le variabili di ambiente del database per l'area di ripristino rapido:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Verificare che il database sia in modalità di log di archiviazione per abilitare i backup online.

    Controllare prima lo stato dell'archivio dei log:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Se è in modalità NOARCHIVELOG, eseguire i comandi seguenti:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  Creare una tabella per testare le operazioni di backup e ripristino:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. Configurare RMAN per eseguire il backup nell'area di ripristino rapido situata nel disco della macchina virtuale:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Confermare i dettagli della modifica della configurazione:

    ```bash
    RMAN> show all;
    ```    

12.  A questo punto, eseguire il backup. Il seguente comando eseguirà un backup completo del database, inclusi i file di log di archiviazione, come un set di backup in formato compresso:

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup"></a>Uso di Backup di Azure

Il servizio Backup di Azure offre soluzioni semplici, sicure ed economicamente convenienti per eseguire il backup dei dati e ripristinarli dal cloud di Microsoft Azure. Backup di Azure fornisce backup indipendenti e isolati per salvaguardare dalla distruzione accidentale dei dati originali. I backup vengono archiviati in un insieme di credenziali di Servizi di ripristino con la gestione predefinita dei punti di ripristino. La configurazione e la scalabilità sono semplici, i backup sono ottimizzati ed è possibile eseguire il ripristino con facilità secondo necessità.

Il servizio backup di Azure fornisce un [Framework](../../../backup/backup-azure-linux-app-consistent.md) per ottenere la coerenza delle applicazioni durante i backup di macchine virtuali Windows e Linux per varie applicazioni come Oracle, MySQL, Mongo DB, SAP HANA e PostgreSQL. Ciò implica la chiamata di uno script di pre-elaborazione (per mettere in stato le applicazioni) prima di creare uno snapshot dei dischi e la chiamata di post-script (comandi per sbloccare le applicazioni) dopo il completamento dello snapshot, per ripristinare le applicazioni in modalità normale. Sebbene i pre-script di esempio e i post-script siano disponibili su GitHub, la creazione e la manutenzione di questi script è responsabilità dell'utente. 

Ora backup di Azure fornisce un Framework avanzato di pre-script e post-backup, in cui il servizio backup di Azure fornirà gli script e i post-script in pacchetto per le applicazioni selezionate. Gli utenti di backup di Azure devono solo assegnare un nome all'applicazione e quindi il backup delle macchine virtuali di Azure richiamerà automaticamente gli script pre-post pertinenti. Gli script e i post-script in pacchetto verranno gestiti dal team di backup di Azure, in modo che gli utenti possano essere certi del supporto, della proprietà e della validità di tali script. Attualmente, le applicazioni supportate per il Framework migliorato sono *Oracle* e *MySQL*.

In questa sezione si userà il Framework avanzato di backup di Azure per eseguire snapshot coerenti con l'applicazione della macchina virtuale in esecuzione e del database Oracle. Il database verrà inserito in modalità di backup che consente di eseguire un backup online coerente a livello di transazione mentre backup di Azure esegue uno snapshot dei dischi delle macchine virtuali. Lo snapshot sarà una copia completa dello spazio di archiviazione e non una copia incrementale o di copia nello snapshot di scrittura, pertanto si tratta di un supporto efficace per il ripristino del database da. Il vantaggio dell'uso di snapshot coerenti con l'applicazione di backup di Azure consiste nel fatto che sono estremamente veloci, indipendentemente dalla dimensione del database, ed è possibile usare uno snapshot per le operazioni di ripristino non appena viene eseguita, senza dover attendere che il trasferimento venga trasferito nell'insieme di credenziali di servizi di ripristino.

Per usare backup di Azure per eseguire il backup del database, completare i passaggi seguenti:

1. Preparare l'ambiente per un backup coerente con l'applicazione.
1. Configurare backup coerenti con l'applicazione.
1. Attivare un backup coerente con l'applicazione della macchina virtuale.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Preparare l'ambiente per un backup coerente con l'applicazione

1. Passa all'utente *root* :

   ```bash
   sudo su -
   ```

1. Crea nuovo utente di backup:

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. Configurare l'ambiente di backup utente:

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Configurare l'autenticazione esterna per il nuovo utente di backup. L'utente di backup deve essere in grado di accedere al database usando l'autenticazione esterna, in modo che non venga richiesta una password.

   Per prima cosa, tornare all'utente *Oracle* :

   ```bash
   su - oracle
   ```

   Accedere al database usando sqlplus e controllare le impostazioni predefinite per l'autenticazione esterna:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   L'output dovrebbe essere simile a questo esempio: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   A questo punto, creare un utente di database *azbackup* autenticato esternamente e concedere il privilegio sysbackup:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Se si riceve `ORA-46953: The password file is not in the 12.2 format.`  un errore quando si esegue l' `GRANT` istruzione, attenersi alla procedura seguente per eseguire la migrazione del file orapwd nel formato 12,2:
   >
   > 1. Uscire da sqlplus.
   > 1. Spostare il file di password con il vecchio formato in un nuovo nome.
   > 1. Eseguire la migrazione del file di password.
   > 1. Rimuovere il file precedente.
   > 1. Eseguire il comando seguente:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. Eseguire di nuovo l' `GRANT` operazione in sqlplus.
   >
   
4. Creare una stored procedure per registrare i messaggi di backup nel log degli avvisi del database:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>Configurare backup coerenti con l'applicazione  

1. Passa all'utente *root* :

   ```bash
   sudo su -
   ```

2. Creare la directory di lavoro per il backup coerente con l'applicazione:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. Creare un file nella directory */etc/Azure* denominato *workload. conf* con il contenuto seguente, che deve iniziare con `[workload]` . Il comando seguente creerà il file e compilerà il contenuto:

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

4. Scaricare gli script preOracleMaster. SQL e postOracleMaster. SQL dal [repository GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) e copiarli nella directory */etc/Azure* .

5. Modificare le autorizzazioni per i file

```bash
   chmod 744 workload.conf preOracleMaster.sql postOracleMaster.sql 
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Attivare un backup coerente con l'applicazione della macchina virtuale

# <a name="portal"></a>[Portale](#tab/azure-portal)

1.  Nel portale di Azure andare al gruppo di risorse **RG-Oracle** e fare clic sulla macchina virtuale **vmoracle19c**.

2.  Nel pannello **backup** creare un nuovo insieme di credenziali di **servizi di ripristino** nel gruppo di risorse **RG-Oracle** con il nome " **Vault**".
    Per **scegliere i criteri di backup**, usare **(nuovo) DailyPolicy**. Se si desidera modificare la frequenza di backup o il periodo di mantenimento dati, selezionare **Crea un nuovo criterio** .

    ![Pagina di aggiunta degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery-service-01.png)

3.  Per continuare, fare clic su **Abilita backup**.

    > [!IMPORTANT]
    > Dopo aver fatto clic su **Abilita backup**, il processo di backup non viene avviato fino all'orario pianificato. Per configurare un backup immediato, completare il passaggio successivo.

4. Dalla pagina gruppo di risorse fare clic sull'insieme di credenziali di servizi di ripristino appena **creato.** Suggerimento: potrebbe essere necessario aggiornare la pagina per visualizzarla.

5.  Nel pannello **myVault - Elementi di backup** in **CONTEGGIO DEGLI ELEMENTI DI BACKUP** selezionare il numero di backup degli elementi.

    ![Pagina di dettaglio degli insiemi di credenziali myVault dei servizi di ripristino](./media/oracle-backup-recovery/recovery-service-02.png)

6.  Nel pannello **Backup Items (Azure Virtual Machine)** (Elementi di backup: macchina virtuale di Azure), sul lato destro della pagina, fare clic sui puntini di sospensione (**...**) e su **Backup now** (Esegui il backup ora).

    ![Comando Backup now (Esegui il backup ora) degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Accettare il valore predefinito Mantieni backup fino a e fare clic sul pulsante **OK** . Attendere il completamento del processo di backup. 

    Per visualizzare lo stato del processo di backup, fare clic su **processi di backup**.

    ![Pagina dei processi degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery-service-04.png)

    Lo stato del processo di backup viene visualizzato nella figura seguente:

    ![Pagina dei processi degli insiemi di credenziali dei servizi di ripristino con stato](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Si noti che, mentre richiede solo pochi secondi per l'esecuzione dello snapshot, può richiedere del tempo per trasferirlo nell'insieme di credenziali e il processo di backup non viene completato fino al termine del trasferimento.

8. Per un backup coerente con l'applicazione, risolvere gli eventuali errori nel file di log. Il file di log si trova in/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Creare un insieme di credenziali di servizi di ripristino:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. Abilitare la protezione dei backup per la macchina virtuale:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. Attiva un backup per eseguirlo ora invece di attendere che il backup venga attivato con la pianificazione predefinita (05:00 UTC): 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   È possibile monitorare lo stato di avanzamento del processo di backup utilizzando `az backup job list` e `az backup job show` .

---

## <a name="recovery"></a>Ripristino

Per ripristinare il database, completare i passaggi seguenti:

1. Rimuovere i file di database.
1. Generare uno script di ripristino dall'insieme di credenziali di servizi di ripristino.
1. Montare il punto di ripristino.
1. Eseguire il ripristino.

### <a name="remove-the-database-files"></a>Rimuovere i file di database 

Più avanti in questo articolo verrà illustrato come testare il processo di ripristino. Prima di testare il processo di ripristino, è necessario rimuovere i file di database.

1.  Arrestare l'istanza di Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Rimuovere i file di backup e i backup:

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Generare uno script di ripristino dall'insieme di credenziali di servizi di ripristino

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nella portale di Azure cercare l'elemento insiemi di *credenziali dei servizi* di ripristino di insieme di credenziali e selezionarlo.

    ![Elementi di backup myVault degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery-service-06.png)

2. Nel pannello **Panoramica** selezionare elementi di **backup** e selezionare la **macchina virtuale di Azure** in cui è elencato il numero di elementi di backup anon-zero.

    ![Numero di elementi di backup di macchine virtuali di Azure in insiemi di credenziali di servizi di ripristino](./media/oracle-backup-recovery/recovery-service-07.png)

3. Nella pagina elementi di backup (macchine virtuali di Azure) la macchina virtuale **vmoracle19c** è elencata. Fare clic sui puntini di sospensione a destra per visualizzare il menu e selezionare **ripristino file**.

    ![Schermata della pagina di ripristino dei file degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery-service-08.png)

4. Nel riquadro **Ripristino di file (anteprima)** fare clic su **Scarica script**. Quindi, salvare il file di download con estensione py in una cartella nel computer client. Viene generata una password per eseguire lo script. Copiare la password in un file per usarla in seguito. 

    ![Opzioni di salvataggio e download del file di script](./media/oracle-backup-recovery/recovery-service-09.png)

5. Copiare il file con estensione PY nella macchina virtuale.

    Nell'esempio seguente viene illustrato come usare un comando di copia di sicurezza (scp) per spostare il file nella macchina virtuale. È possibile anche copiare il contenuto negli Appunti e incollarlo in un nuovo file configurato nella macchina virtuale.

    > [!IMPORTANT]
    > Nell'esempio seguente assicurarsi di aggiornare i valori dell'indirizzo IP e della cartella. I valori devono corrispondere a quelli della cartella in cui è stato salvato il file.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per elencare i punti di ripristino per la macchina virtuale, usare AZ backup Recovery Point List. In questo esempio verrà selezionato il punto di recupero più recente per la macchina virtuale denominata myVM protetta in myRecoveryServicesVault:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Per ottenere lo script che si connette a, o contiene, il punto di recupero per la macchina virtuale, usare l'opzione az backup restore files mount-rp. Nell'esempio seguente si ottiene lo script per la macchina virtuale denominata myVM protetta in myRecoveryServicesVault.

Sostituire myRecoveryPointName con il nome del punto di recupero ottenuto nel comando precedente:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

Lo script viene scaricato e viene visualizzata una password, come nell'esempio seguente:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Copiare il file con estensione PY nella macchina virtuale.

Nell'esempio seguente viene illustrato come usare un comando di copia di sicurezza (scp) per spostare il file nella macchina virtuale. È possibile anche copiare il contenuto negli Appunti e incollarlo in un nuovo file configurato nella macchina virtuale.

> [!IMPORTANT]
> Nell'esempio seguente assicurarsi di aggiornare i valori dell'indirizzo IP e della cartella. I valori devono corrispondere a quelli della cartella in cui è stato salvato il file.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Montare il punto di ripristino

1. Creare un punto di montaggio di ripristino e copiarvi lo script.

    Nell'esempio seguente creare una directory */Restore* in cui eseguire il montaggio dello snapshot, spostare il file nella directory e modificare il file in modo che sia di proprietà dell'utente root e che sia stato creato un file eseguibile.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    A questo punto, eseguire lo script per ripristinare il backup. Verrà richiesto di fornire la password generata in portale di Azure. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    L'esempio seguente illustra ciò che dovrebbe venire visualizzato dopo aver eseguito lo script precedente. Quando viene chiesto di continuare, immettere **Y**.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. L'accesso ai volumi montati è stato confermato.

    Per uscire, immettere **q** e cercare i volumi montati. Per creare un elenco dei volumi aggiunti, al prompt dei comandi immettere **df-h**.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Eseguire il ripristino

1. Copiare nuovamente i file di backup mancanti nell'area di ripristino rapido:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. I comandi seguenti usano RMAN per ripristinare i file di dati mancanti e recuperare il database:

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Controllare che il contenuto del database sia stato recuperato completamente:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Smontare il punto di ripristino.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    Nel portale di Azure, nel pannello **Ripristino file (anteprima)** fare clic su **Unmount Disks** (Smonta dischi).

    ![Comando Unmount disks](./media/oracle-backup-recovery/recovery-service-10.png)
    
    È anche possibile smontare i volumi di ripristino eseguendo di nuovo lo script Python con l'opzione **-Clean** .

## <a name="restore-the-entire-vm"></a>Ripristinare l'intera VM

Anziché ripristinare i file eliminati dagli insiemi di credenziali dei servizi di ripristino, è possibile ripristinare l'intera macchina virtuale.

Per ripristinare l'intera macchina virtuale, completare i passaggi seguenti:

1. Arrestare ed eliminare vmoracle19c.
1. Ripristinare la macchina virtuale.
1. Impostare l'indirizzo IP pubblico.
1. Connettersi alla macchina virtuale.
1. Avviare il database per montare la fase ed eseguire il ripristino.

### <a name="stop-and-delete-vmoracle19c"></a>Arrestare ed eliminare vmoracle19c

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nella portale di Azure passare alla macchina virtuale **vmoracle19c** , quindi selezionare **Arresta**.

1. Quando la macchina virtuale non è più in esecuzione, fare clic su **Elimina** e quindi su **Sì**.

   ![Comando Elimina insieme di credenziali](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Arrestare e deallocare la macchina virtuale:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. Eliminare la macchina virtuale. Quando richiesto, immettere ' y ':

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>Ripristinare la macchina virtuale

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Creare un account di archiviazione per la gestione temporanea nel portale di Azure.

   1. Nella portale di Azure selezionare **+ Crea una risorsa** e cercare e selezionare **account di archiviazione**.
    
      ![Pagina Aggiungi account di archiviazione](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. Nella pagina Crea account di archiviazione scegliere il gruppo di risorse esistente **RG-Oracle**, assegnare un nome all'account di archiviazione **Oracrestore** e scegliere **archiviazione V2 (generalpurpose v2)** per tipo di account. Modificare la replica in **archiviazione con ridondanza locale (con ridondanza locale)** e impostare le prestazioni su **standard**. Assicurarsi che location sia impostato sulla stessa area di tutte le altre risorse nel gruppo di risorse. 
    
      ![Pagina Aggiungi account di archiviazione](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Fare clic su Verifica + crea e quindi su Crea.

2. Nella portale di Azure cercare l'elemento insieme di *credenziali dei servizi* di ripristino di insieme di credenziali e fare clic su di esso.

    ![Elementi di backup myVault degli insiemi di credenziali dei servizi di ripristino](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  Nel pannello **Panoramica** selezionare elementi di **backup** e selezionare la **macchina virtuale di Azure** in cui è elencato il numero di elementi di backup anon-zero.

    ![Numero di elementi di backup di macchine virtuali di Azure in insiemi di credenziali di servizi di ripristino](./media/oracle-backup-recovery/recovery-service-07.png)

4.  Per gli elementi di backup (macchine virtuali di Azure), viene visualizzata la pagina **vmoracle19c** della VM. Fare clic sul nome della macchina virtuale.

    ![Pagina di ripristino della macchina virtuale](./media/oracle-backup-recovery/recover-vm-02.png)

5.  Nel pannello **vmoracle19c** scegliere un punto di ripristino con un tipo di coerenza coerente con l' **applicazione** e fare clic sui puntini di sospensione (**...**) a destra per visualizzare il menu.  Dal menu fare clic su **Ripristina macchina virtuale**.

    ![Comando Ripristina macchina virtuale](./media/oracle-backup-recovery/recover-vm-03.png)

6.  Nel pannello **Ripristina macchina virtuale** scegliere **Crea nuovo** e **Crea nuova macchina virtuale**. Immettere il nome della macchina virtuale **vmoracle19c** e scegliere il **vmoracle19cVNET** VNet, la subnet verrà popolata automaticamente in base alla selezione di VNet. Il processo di ripristino della macchina virtuale richiede un account di archiviazione di Azure nello stesso gruppo di risorse e nella stessa area. È possibile scegliere l'account di archiviazione **orarestore** configurato in precedenza.

    ![Valori di configurazione del ripristino](./media/oracle-backup-recovery/recover-vm-04.png)

7.  Per ripristinare la macchina virtuale, fare clic sul pulsante **Ripristina**.

8.  Per visualizzare lo stato del processo di ripristino, fare clic su **Processi** e quindi su **Processi di backup**.

    ![Comando dello stato dei processi di backup](./media/oracle-backup-recovery/recover-vm-05.png)

    Fare clic sull'operazione di ripristino **in corso** per visualizzare lo stato del processo di ripristino:

    ![Stato del processo di ripristino](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per configurare l'account di archiviazione e la condivisione file, eseguire i comandi seguenti nell'interfaccia della riga di comando di Azure.

1. Creare l'account di archiviazione nello stesso gruppo di risorse e nella stessa posizione della macchina virtuale:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Recuperare l'elenco dei punti di ripristino disponibili. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. Ripristinare il punto di ripristino nell'account di archiviazione. Sostituire `<myRecoveryPointName>` con un punto di ripristino dall'elenco generato nel passaggio precedente:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. Recuperare i dettagli del processo di ripristino. Il comando seguente consente di ottenere altri dettagli per il processo ripristinato attivato, incluso il nome, necessario per recuperare l'URI del modello. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   L'output sarà simile al seguente `(Note down the name of the restore job)` :

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. Recuperare i dettagli dell'URI da usare per ricreare la macchina virtuale. Sostituire il nome del processo di ripristino del passaggio precedente per `<RestoreJobName>` .

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   L'output è simile al seguente:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   Il nome del modello, che si trova alla fine dell'URI del BLOB del modello, che in questo esempio è `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` e il nome del contenitore BLOB, che è `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` elencato. 

   Usare questi valori nel comando seguente per assegnare le variabili in preparazione per la creazione della macchina virtuale. Viene generata una chiave di firma di accesso condiviso per il contenitore di archiviazione con durata di 30 minuti.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   A questo punto, distribuire il modello per creare la macchina virtuale.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   Verrà richiesto di specificare un nome per la macchina virtuale.

---

### <a name="set-the-public-ip-address"></a>Impostare l'indirizzo IP pubblico

Dopo il ripristino della macchina virtuale, è necessario riassegnare l'indirizzo IP originale alla nuova VM.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1.  Nella portale di Azure passare alla macchina virtuale **vmoracle19c**. Si noterà che è stato assegnato un nuovo IP pubblico e una scheda di interfaccia di rete simile a vmoracle19c-NIC-XXXXXXXXXXXX, ma non dispone di un indirizzo DNS. Quando la macchina virtuale originale è stata eliminata, l'indirizzo IP pubblico e la NIC vengono conservati e i passaggi successivi li ricollegano alla nuova VM.

    ![Elenco degli indirizzi IP pubblici](./media/oracle-backup-recovery/create-ip-01.png)

2.  Arrestare la VM

    ![Creare un indirizzo IP](./media/oracle-backup-recovery/create-ip-02.png)

3.  Vai alla **rete**

    ![Associare l'indirizzo IP](./media/oracle-backup-recovery/create-ip-03.png)

4.  Fare clic su **Connetti interfaccia di rete**, scegliere la NIC originale * * vmoracle19cVMNic, a cui è ancora associato l'indirizzo IP pubblico originale e fare clic su **OK** .

    ![Selezionare i valori Tipo di risorsa e Interfaccia di rete](./media/oracle-backup-recovery/create-ip-04.png)

5.  A questo punto è necessario scollegare la scheda di interfaccia di rete creata con l'operazione di ripristino della macchina virtuale perché è configurata come interfaccia primaria. Fare clic su **Scollega interfaccia di rete** e scegliere la nuova NIC simile a **vmoracle19c-NIC-xxxxxxxxxxxx** e quindi fare clic su **OK** .

    ![Valore dell'indirizzo IP](./media/oracle-backup-recovery/create-ip-05.png)
    
    La VM ricreata avrà ora la scheda di interfaccia di rete originale, che è associata all'indirizzo IP originale e alle regole del gruppo di sicurezza di rete
    
    ![Valore dell'indirizzo IP](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  Tornare alla **Panoramica** e fare clic su **Avvia** . 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Arrestare e deallocare la macchina virtuale:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. Elencare la scheda di interfaccia di rete VM generata per il ripristino corrente

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   L'output sarà simile a questo, che elenca il nome della scheda di interfaccia di rete generata per il ripristino come `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. Alleghi la scheda di interfaccia di rete originale, che dovrebbe avere il nome `<VMName>VMNic` , in questo caso `vmoracle19cVMNic` . L'indirizzo IP pubblico originale è ancora collegato a questa scheda di interfaccia di rete e verrà ripristinato alla macchina virtuale quando la scheda di interfaccia di rete viene ricollegata. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. Scollega la scheda di interfaccia di rete generata per il ripristino

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. Avviare la macchina virtuale:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Connettersi alla VM

Per eseguire la connessione alla macchina virtuale, usare lo script seguente:

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>Avviare il database per montare la fase ed eseguire il ripristino

1. È possibile che l'istanza sia in esecuzione perché l'avvio automatico ha tentato di avviare il database all'avvio della macchina virtuale. Tuttavia, il database richiede il ripristino ed è probabile che sia solo in fase di montaggio, quindi viene eseguito per primo un arresto preliminare.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Verificare che il contenuto del database sia stato recuperato:

    ```bash
    SQL> select * from scott.scott_table;
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
