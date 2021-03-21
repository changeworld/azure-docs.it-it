---
title: Creare un database Oracle in una VM di Azure | Microsoft Docs
description: Ottenere rapidamente un database Oracle 12c operativo nell'ambiente Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: 8964248bb23b2b615c7e73e26d730fbd79b4e9e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184458"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Creare un database Oracle in una VM di Azure

Questa guida illustra in dettaglio l'uso dell'interfaccia della riga di comando di Azure per distribuire una macchina virtuale di Azure dall' [immagine della raccolta di Oracle Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) per creare un database Oracle 19C. Dopo avere distribuito il server, verrà effettuata la connessione via SSH per configurare il database Oracle. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

Nell'esempio seguente viene creato un gruppo di risorse denominato *RG-Oracle* nella località *eastus* .

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>Creare macchina virtuale

Per crea una macchina virtuale (VM), usare il comando [az vm create](/cli/azure/vm). 

L'esempio seguente crea una VM denominata `vmoracle19c`. Crea anche le chiavi SSH se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

In seguito alla creazione della VM, l'interfaccia della riga di comando di Azure visualizza informazioni simili a quelle dell'esempio seguente. Notare il valore di `publicIpAddress`. Questo indirizzo verrà usato per accedere alla macchina virtuale.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Creare e alleghi un nuovo disco per i file di i/o di Oracle e FRA

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>Aprire le porte per la connettività
In questa attività è necessario configurare alcuni endpoint esterni per il listener del database e EM Express da usare impostando il gruppo di sicurezza di rete di Azure che protegge la macchina virtuale. 

1. Per aprire l'endpoint utilizzato per accedere al database Oracle in modalità remota, creare una regola del gruppo di sicurezza di rete nel modo seguente:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Per aprire l'endpoint usato per accedere a Oracle EM Express in modalità remota, creare una regola del gruppo di sicurezza di rete con az network nsg rule create come segue:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. Se necessario, ottenere di nuovo l'indirizzo IP pubblico della VM con az network public-ip show.

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>Preparare l'ambiente VM

1. Connettersi alla VM

   Per creare una sessione SSH con la VM, usare il comando seguente. Sostituire l'indirizzo IP con il valore di `publicIpAddress` della macchina virtuale.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. Passa all'utente root

   ```bash
   sudo su -
   ```

3. Verificare la presenza dell'ultimo dispositivo disco creato che è possibile formattare per l'uso di file di data Oracle

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   L'output sarà simile al seguente:
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. Formattare il dispositivo. 
   Quando l'utente root esegue una parte del dispositivo 
   
   Prima di tutto creare un'etichetta disco:
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   Quindi creare una partizione primaria che si estende sull'intero disco:
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   Controllare infine i dettagli del dispositivo stampando i relativi metadati:
   ```bash
   parted /dev/sdc print
   ```
   L'output deve essere simile al seguente:
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. Creare un file System nella partizione del dispositivo

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. Creare un punto di montaggio
   ```bash
   mkdir /u02
   ```

7. Montare il disco

   ```bash
   mount /dev/sdc1 /u02
   ```

8. Modificare le autorizzazioni per il punto di montaggio

   ```bash
   chmod 777 /u02
   ```

9. Aggiungere il montaggio al file/etc/fstab. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. Aggiornare il file ***hosts*** con l'indirizzo IP pubblico e il nome host.

    Modificare l' ***IP pubblico e VMName*** in modo che corrispondano ai valori effettivi:
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. Aggiornare il file hostname
    
    Usare il comando seguente per aggiungere il nome di dominio della macchina virtuale al file **/etc/hostname** . Si presuppone che siano stati creati il gruppo di risorse e la VM nell'area **eastus** :
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. Aprire le porte del firewall
    
    Poiché SELinux è abilitato per impostazione predefinita nell'immagine del Marketplace, è necessario aprire il firewall al traffico per la porta di ascolto del database 1521 ed Enterprise Manager Express porta 5502. Eseguire i comandi seguenti come utente root:

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>Creare il database

Il software Oracle è già installato nell'immagine del Marketplace. Creare un database di esempio come segue. 

1.  Passa all'utente **Oracle** :

    ```bash
    sudo su - oracle
    ```
2. Avvio del listener del database

   ```bash
   lsnrctl start
   ```
   L'output è simile al seguente:
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Creare una directory dei dati per i file di dati Oracle:

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Eseguire l'assistente per la creazione di database:

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    La creazione del database richiede alcuni minuti.

    L'output sarà simile al seguente:

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Impostare le variabili Oracle

    Prima di connettersi, è necessario impostare la variabile di ambiente *ORACLE_SID*:

    ```bash
        export ORACLE_SID=test
    ```

    È anche necessario aggiungere la variabile ORACLE_SID al `oracle` file degli utenti `.bashrc` per gli accessi futuri usando il comando seguente:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Connettività a Oracle EM Express

Per ottenere uno strumento di gestione dell'interfaccia utente grafica da usare per esplorare il database, configurare Oracle EM Express. Per connettersi a Oracle EM Express, prima di tutto è necessario configurare la porta in Oracle. 

1. Connettersi al database usando sqlplus:

    ```bash
    sqlplus sys as sysdba
    ```

2. Dopo la connessione, impostare la porta 5502 per EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  Connettersi a EM Express dal browser. Verificare che il browser sia compatibile con EM Express. È necessario installare Flash. 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    È possibile eseguire l'accesso usando l'account **SYS** e selezionare la casella di controllo **as sysdba**. Usare la password **OraPasswd1** impostata durante l'installazione. 

    ![Screenshot della pagina di accesso a Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>Automatizzare l'avvio e l'arresto del database

Per impostazione predefinita, il database Oracle non viene avviato automaticamente quando si riavvia la VM. Per configurare il database Oracle perché venga avviato automaticamente, accedere prima di tutto come utente ROOT. Quindi creare e aggiornare alcuni file di sistema.

1. Accedere come utente ROOT

    ```bash
    sudo su -
    ```

2.  Eseguire il comando seguente per modificare il flag di avvio automatico da `N` a `Y` nel `/etc/oratab` file:

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  Creare un file denominato `/etc/init.d/dbora` e incollare i contenuti seguenti:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Modificare le autorizzazioni nei file con *chmod* come segue:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Creare collegamenti simbolici per l'avvio e l'arresto come segue:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Per testare le modifiche, riavviare la VM:

    ```bash
    reboot
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esplorazione di un primo database Oracle in Azure e quando la macchina virtuale non è più necessaria, è possibile usare il comando [az group delete](/cli/azure/group) per rimuovere il gruppo di risorse la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come proteggere il database in Azure con le [strategie di backup Oracle](./oracle-database-backup-strategies.md)

Informazioni su altre [soluzioni Oracle in Azure](./oracle-overview.md). 

Provare a eseguire l'esercitazione [Installing and Configuring Oracle Automated Storage Management (Installazione e configurazione di Oracle Automated Storage Management)](configure-oracle-asm.md).
