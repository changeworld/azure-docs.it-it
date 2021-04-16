---
title: Distribuzione della piattaforma BI SAP BusinessObjects in Azure per Linux | Microsoft Docs
description: Distribuire e configurare la piattaforma SAP BusinessObjects BI in Azure per Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: b16a2d9f779232e59eb883f6a254be22990f5c78
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520021"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Guida alla distribuzione della piattaforma di business intelligence SAP BusinessObjects per Linux in Azure

Questo articolo descrive la strategia per distribuire SAP BOBI Platform in Azure per Linux. In questo esempio vengono configurate due macchine virtuali SSD Premium Managed Disks come directory di installazione. Database di Azure per MySQL viene usato per il database CMS e Azure NetApp Files per il server del repository di file viene condiviso tra entrambi i server. L'applicazione Web Java Tomcat predefinita e l'applicazione piattaforma BI vengono installate insieme in entrambe le macchine virtuali. Per bilanciare il carico della richiesta utente, viene usato il gateway applicazione con funzionalità di offload TLS/SSL native.

Questo tipo di architettura è efficace per piccole distribuzioni o ambienti non di produzione. Per la distribuzione di produzione o su larga scala, è possibile avere host separati per l'applicazione Web e avere anche più host di applicazioni BOBI che consentono al server di elaborare altre informazioni.

![Distribuzione di SAP BOBI in Azure per Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

In questo esempio vengono usati la versione del prodotto e file system layout

- SAP BusinessObjects Platform 4.3
- SUSE Linux Enterprise Server 12 SP5
- Database di Azure per MySQL (versione: 8.0.15)
- Connettore API MySQL C - libmysqlclient (versione: 6.1.11)

| File system        | Descrizione                                                                                                               | Dimensioni (GB)             | Proprietario  | Group  | Archiviazione                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | La file system per l'installazione dell'istanza di SAP BOBI, dell'applicazione Web Tomcat predefinita e dei driver di database (se necessario) | Linee guida per il ridimensionamento DI SAP | bl1adm | sapsys | Disco Premium gestito - Unità SSD |
| /usr/sap/frsinput  | La directory di montaggio è per i file condivisi tra tutti gli host BOBI che verranno usati come directory del repository dei file di input  | Esigenze aziendali         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | La directory di montaggio è per i file condivisi tra tutti gli host BOBI che verranno usati come directory del repository dei file di output | Esigenze aziendali         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Distribuire una macchina virtuale Linux tramite portale di Azure

In questa sezione verranno create due macchine virtuali (VM) con un'immagine del sistema operativo Linux per la piattaforma SAP BOBI. I passaggi di alto livello per creare le macchine virtuali sono i seguenti:

1. Creare un gruppo [di risorse](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Creare una [rete virtuale](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Non usare una singola subnet per tutti i servizi di Azure nella distribuzione della piattaforma SAP BI. In base all'architettura della piattaforma SAP BI, è necessario creare più subnet. In questa distribuzione verranno create tre subnet: Subnet applicazione, Subnet archivio file e Subnet del gateway applicazione.
   - In Azure, il gateway applicazione e Azure NetApp Files devono sempre essere in una subnet separata. Per [altri gateway applicazione di Azure,](../../../application-gateway/configuration-overview.md) vedere [Azure NetApp Files e linee guida](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) per la pianificazione della rete.

3. Creare un set di disponibilità.

   - Per ottenere la ridondanza per ogni livello nella distribuzione a più istanze, inserire le macchine virtuali per ogni livello in un set di disponibilità. Assicurarsi di separare i set di disponibilità per ogni livello in base all'architettura.

4. Creare la macchina virtuale 1 **(azusbosl1).**

   - È possibile usare un'immagine personalizzata o scegliere un'immagine da Azure Marketplace. Vedere [Deploying a VM from the Azure Marketplace for SAP o](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) [Deploying a VM with a custom image for SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) based on your need (Distribuzione di una macchina virtuale con un'immagine personalizzata per SAP in base alle proprie necessità).

5. Creare la macchina virtuale 2 **(azusbosl2).**
6. Aggiungere un SSD Premium disco. Verrà usato come directory di installazione DI SAP BOBI.

## <a name="provision-azure-netapp-files"></a>Effettuare il provisioning Azure NetApp Files

Prima di continuare con la configurazione per Azure NetApp Files, acquisire familiarità con la documentazione di Azure [NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).

Azure NetApp Files è disponibile in diverse [aree di Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Verificare se l'area di Azure selezionata Azure NetApp Files.

Usare [Azure NetApp Files disponibilità in base all'area di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) per controllare la disponibilità dei Azure NetApp Files in base all'area.

Per richiedere l'onboarding Azure NetApp Files, vedere Registrare Azure NetApp Files [istruzioni](../../../azure-netapp-files/azure-netapp-files-register.md) prima di distribuire Azure NetApp Files.

### <a name="deploy-azure-netapp-files-resources"></a>Distribuire le risorse di Azure NetApp Files

Le istruzioni seguenti presuppongono che la rete virtuale di Azure sia già [stata distribuita.](../../../virtual-network/virtual-networks-overview.md) Le Azure NetApp Files e le macchine virtuali, in cui verranno montate le Azure NetApp Files, devono essere distribuite nella stessa rete virtuale di Azure o in reti virtuali di Azure con peer.

1. Se le risorse non sono già state distribuite, richiedere [l'onboarding Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Creare un account NetApp nell'area di Azure selezionata seguendo le istruzioni riportate in [Creare un account NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

3. Configurare un pool Azure NetApp Files di capacità seguendo le istruzioni in [Configurare un pool Azure NetApp Files capacità.](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)

   - L'architettura della piattaforma SAP BI presentata in questo articolo usa un singolo pool Azure NetApp Files capacità a livello *di servizio* Premium. Per sap BI File Repository Server in Azure, è consigliabile usare un livello di servizio Azure NetApp Files *Premium* [o](../../../azure-netapp-files/azure-netapp-files-service-levels.md) *Ultra.*

4. Delegare una subnet Azure NetApp Files, come descritto nelle istruzioni in [Delegare una subnet a Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Distribuire Azure NetApp Files volumi seguendo le istruzioni in [Creare un volume NFS per Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

   Il volume ANF può essere distribuito come NFSv3 e NFSv4.1, perché entrambi i protocolli sono supportati per SAP BOBI Platform. Distribuire i volumi nella rispettiva subnet Azure NetApp Files subnet. Gli indirizzi IP dei volumi Azure NetApp vengono assegnati automaticamente.

Tenere presente che le Azure NetApp Files e le macchine virtuali di Azure devono essere nella stessa rete virtuale di Azure o nelle reti virtuali di Azure con peer. Ad esempio, azusbobi-frsinput, azusbobi-frsoutput sono i nomi dei volumi e nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput sono i percorsi di file per i volumi Azure NetApp Files.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Considerazioni importanti

Durante la creazione del Azure NetApp Files per SAP BOBI Platform File Repository Server, tenere presente quanto segue:

1. Il pool di capacità minima è di 4 tebibyte (TiB).
2. La dimensione minima del volume è 100 gibibyte (GiB).
3. Azure NetApp Files e tutte le macchine virtuali in cui verranno montati i volumi Azure NetApp Files devono essere nella stessa rete virtuale di Azure o in reti virtuali con [peering](../../../virtual-network/virtual-network-peering-overview.md) nella stessa area. L'accesso ad Azure NetApp Files mediante peering VNET nella stessa area è ora supportato. L'accesso ad Azure NetApp tramite peering globale non è ancora supportato.
4. La rete virtuale selezionata deve avere una subnet delegata a Azure NetApp Files.
5. Con i Azure NetApp Files [di](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)esportazione è possibile controllare i client consentiti, il tipo di accesso (lettura/scrittura, sola lettura e così via).
6. La Azure NetApp Files non è ancora in grado di riconoscere la zona. Attualmente, la funzionalità non viene distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presente le implicazioni di latenza potenziali in alcune aree di Azure.
7. I volumi Azure NetApp Files possono essere distribuiti come volumi NFSv3 o NFSv4.1. Entrambi i protocolli sono supportati per le applicazioni della piattaforma SAP BI.

## <a name="configure-file-systems-on-linux-servers"></a>Configurare i file system nei server Linux

Per i passaggi in questa sezione vengono usati i prefissi seguenti:

**[A]**: Il passaggio si applica a tutti gli host

### <a name="format-and-mount-sap-file-system"></a>Formattare e montare sap file system

1. **[A] Elencare** tutti i dischi collegati

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A] Formattare** il dispositivo a blocchi per /usr/sap

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A] Creare** la directory di montaggio

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A] Ottenere** l'UUID del dispositivo a blocchi

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** Mantenere file system di montaggio in /etc/fstab

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A] Montare** file system

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Montare Azure NetApp Files volume

1. **[A] Creare** directory di montaggio

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A] Configurare** il sistema operativo client per supportare il montaggio NFSv4.1 (applicabile solo se si usa **NFSv4.1)**

   Se si usano volumi Azure NetApp Files con il protocollo NFSv4.1, eseguire la configurazione seguente in tutte le macchine virtuali, in cui è necessario montare i volumi Azure NetApp Files NFSv4.1.

   **Verificare le impostazioni del dominio NFS**

   Assicurarsi che il dominio sia configurato come dominio Azure NetApp Files **predefinito,** ad esempio , defaultv4iddomain.com e che il mapping sia impostato su **nessuno.**

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Assicurarsi di impostare il dominio NFS in /etc/idmapd.conf nella macchina virtuale in modo che corrisponda alla configurazione di dominio predefinita in Azure NetApp Files: **defaultv4iddomain.com**. In caso di mancata corrispondenza tra la configurazione del dominio nel client NFS (ad esempio la macchina virtuale) e il server NFS, ad esempio la configurazione di Azure NetApp, le autorizzazioni per i file nei volumi Azure NetApp montati nelle macchine virtuali verranno visualizzate come nessuno.

   Verificare `nfs4_disable_idmapping`. Il valore deve essere impostato su **Y**. Per creare la struttura di directory in cui si trova `nfs4_disable_idmapping`, eseguire il comando mount. Non sarà possibile creare manualmente la directory in/sys/modules, perché l'accesso è riservato per il kernel/driver.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** Aggiungere le voci di montaggio

   Se si usa NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Se si usa NFSv4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A] Montare** volumi NFS

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>Configurare il database CMS - Database di Azure per MySQL

Questa sezione fornisce informazioni dettagliate su come effettuare il provisioning di Database di Azure per MySQL usando portale di Azure. Fornisce anche istruzioni su come creare database CMS e di controllo per SAP BOBI Platform e un account utente per accedere al database.

Le linee guida sono applicabili solo se si usa Database di Azure per MySQL. Per altri database, vedere la documentazione specifica di SAP o del database per istruzioni.

### <a name="create-an-azure-database-for-mysql"></a>Creare un database di Azure per MySQL

Accedere a portale di Azure e seguire i passaggi indicati in questa Guida introduttiva [di Database di Azure per MySQL.](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md) Alcuni punti da notare durante il provisioning di Database di Azure per MySQL:

1. Selezionare la stessa area per Database di Azure per MySQL in cui sono in esecuzione i server applicazioni della piattaforma SAP BI.

2. Scegliere una versione supportata del database in base alla matrice di disponibilità del prodotto [(PAM) per SAP BI](https://support.sap.com/pam) specifica per la versione BOBI di SAP. Seguire le stesse linee guida per la compatibilità di MySQL AB in SAP PAM

3. In "calcolo e archiviazione" selezionare Configura **server** e selezionare il piano tariffario appropriato in base all'output di ridimensionamento.

4. **L'estensione automatica dell'archiviazione** è abilitata per impostazione predefinita. Tenere presente che [l'archiviazione](../../../mysql/concepts-pricing-tiers.md#storage) può essere solo ridimensionata, non ridotta.

5. Per impostazione predefinita, **il periodo di conservazione del** backup è di sette giorni, ma facoltativamente è possibile configurarlo fino a 35 giorni. [](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)

6. I backup di Database di Azure per MySQL sono con ridondanza locale per  impostazione predefinita, quindi se si vogliono eseguire backup del server nell'archiviazione con ridondanza geografica, selezionare Con ridondanza geografica in Opzioni di ridondanza **del backup.**

> [!NOTE]
> La modifica delle [opzioni di ridondanza del](../../../mysql/concepts-backup.md#backup-redundancy-options) backup dopo la creazione del server non è supportata.

### <a name="configure-connection-security"></a>Configurare la sicurezza della connessione

Per impostazione predefinita, il server creato è protetto da un firewall e non è accessibile pubblicamente. Per fornire l'accesso alla rete virtuale in cui sono in esecuzione i server applicazioni della piattaforma SAP BI, seguire questa procedura:  

1. Passare alle risorse del server nel portale di Azure e selezionare **Sicurezza connessione** dal menu a sinistra per la risorsa server.
2. Selezionare **Sì per** Consentire **l'accesso ai servizi di Azure.**
3. In Regole di rete virtuale selezionare **Aggiunta di una rete virtuale esistente.** Selezionare la rete virtuale e la subnet del server applicazioni della piattaforma SAP BI. È anche necessario fornire l'accesso a Jump Box o ad altri server da cui è possibile connettere [MySQL Workbench](../../../mysql/connect-workbench.md) a Database di Azure per MySQL. MySQL Workbench verrà usato per creare il database cms e audit
4. Dopo aver aggiunto le reti virtuali, selezionare **Salva**.

### <a name="create-cms-and-audit-database"></a>Creare cms e database di controllo

1. Scaricare e installare MySQL Workbench dal sito [Web MySQL.](https://dev.mysql.com/downloads/workbench/) Assicurarsi di installare MySQL Workbench nel server che può accedere a Database di Azure per MySQL.

2. Connettersi al server usando MySQL Workbench. Seguire le istruzioni indicate in questo [articolo.](../../../mysql/connect-workbench.md#get-connection-information) Se il test di connessione ha esito positivo, verrà visualizzato il messaggio seguente:

   ![Connessione a SQL Workbench](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Nella scheda Query SQL eseguire la query seguente per creare lo schema per CMS e il database di controllo.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Creare un account utente per connettersi allo schema

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. Per controllare i privilegi e i ruoli dell'account utente MySQL

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Installare il connettore api C MySQL (libmysqlclient) nel server Linux

Per l'accesso al database da parte del server applicazioni SAP BOBI, sono necessari client/driver di database. Il connettore API C mySQL per Linux deve essere usato per accedere ai database cms e audit. La connessione ODBC al database CMS non è supportata. Questa sezione fornisce istruzioni su come configurare mySQL C API Connector in Linux.

1. Vedere [l'articolo Driver](../../../mysql/concepts-compatibility.md) e strumenti di gestione MySQL compatibili con Database di Azure per MySQL, che descrive i driver compatibili con Database di Azure per MySQL. Controllare il **driver MySQL Connector/C (libmysqlclient)** nell'articolo.

2. Fare riferimento a questo [collegamento per](https://downloads.mysql.com/archives/c-c/) scaricare i driver.

3. Selezionare il sistema operativo e scaricare il pacchetto rpm del componente condiviso del connettore MySQL. In questo esempio viene usata la versione del connettore mysql-connector-c-shared-6.1.11.

4. Installare il connettore in tutte le istanze dell'applicazione SAP BOBI.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Controllare il percorso di libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Impostare LD_LIBRARY_PATH in modo che punti alla `/usr/lib64` directory per l'account utente che verrà usato per l'installazione.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Preparazione del server

Per i passaggi in questa sezione vengono usati i prefissi seguenti:

**[A]**: il passaggio si applica a tutti gli host.

1. **[A]** In base alla versione di Linux (SLES o RHEL), è necessario impostare i parametri del kernel e installare le librerie necessarie. Vedere la **sezione Requisiti di sistema** nella Guida [all'installazione della piattaforma Business Intelligence per Unix.](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)

2. **[A]** Verificare che il fuso orario nel computer sia impostato correttamente. Vedere la [sezione Requisiti aggiuntivi di Unix e Linux](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) nella Guida all'installazione.

3. **[A]** Creare un account utente **(bl1** adm) e un gruppo (sapsys) con cui è possibile eseguire i processi in background del software. Usare questo account per eseguire l'installazione e il software. L'account non richiede privilegi radice.

4. **[A]** Impostare l'ambiente dell'account utente **(bl1** adm) per usare le impostazioni locali UTF-8 supportate e assicurarsi che il software della console supporti i set di caratteri UTF-8. Per assicurarsi che il sistema operativo usi le impostazioni locali corrette, impostare le variabili di ambiente LC_ALL e LANG sulle impostazioni locali preferite nell'ambiente utente **(bl1** adm).

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** Configurare l'account utente (**bl1** adm).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Scaricare ed estrarre supporti per sap BusinessObjects BI Platform da SAP Service Marketplace.

## <a name="installation"></a>Installazione

Controllare le impostazioni locali per l'account utente **bl1** adm nel server

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Passare ai supporti della piattaforma SAP BusinessObjects BI ed eseguire il comando seguente con **l'utente adm bl1**:

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Seguire [la Guida all'installazione della](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) piattaforma SAP BOBI per Unix, specifica per la versione. Alcuni punti da notare durante l'installazione della piattaforma SAP BOBI.

- Nella **schermata Configura** registrazione prodotto è possibile usare il codice di licenza temporaneo per SAP BusinessObjects Solutions dalla nota SAP [1288121](https://launchpad.support.sap.com/#/notes/1288121) o generare il codice di licenza in SAP Service Marketplace

- Nella  schermata Seleziona tipo  di installazione selezionare Installazione completa nel primo server (azusbosl1), per altro server (azusbosl2) selezionare **Personalizzato/Espandi** per espandere la configurazione BOBI esistente.

- Nella **schermata Seleziona database predefinito** o Database esistente selezionare configura un database **esistente,** che richiede di selezionare CMS e Controlla database. Selezionare **MySQL per** Tipo di database CMS e Controlla tipo di database.

  È anche possibile selezionare Nessun database di controllo, se non si vuole configurare il controllo durante l'installazione.

- Selezionare le opzioni appropriate nella **schermata Seleziona server applicazioni Web Java** in base all'architettura BOBI SAP. In questo esempio è stata selezionata l'opzione 1, che installa il server tomcat nella stessa piattaforma SAP BOBI.

- Immettere le informazioni sul database CMS in **Configurare il database del repository CMS - MySQL.** Input di esempio per le informazioni sul database CMS per l'installazione di Linux. Database di Azure per MySQL viene usato sulla porta predefinita 3306
  
  ![Distribuzione DI SAP BOBI in Linux - Database CMS](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (Facoltativo) Immettere Controlla informazioni sul database in **Configura database del repository di controllo - MySQL**. Input di esempio per Controllare le informazioni sul database per l'installazione di Linux.

  ![Distribuzione DI SAP BOBI in Linux - Database di controllo](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Seguire le istruzioni e immettere gli input necessari per completare l'installazione.

Per la distribuzione a istanze multipla, eseguire l'installazione nel secondo host (azusbosl2). Nella **schermata Select Install Type (Seleziona** tipo di installazione) selezionare Custom /Expand (Personalizzato/Espandi) per espandere la configurazione BOBI esistente. 

Nell'offerta database di Azure per MySQL viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL. In Central Management Console (CMC) è quindi disponibile una versione diversa del database che è fondamentalmente la versione impostata nel gateway. Per altri dettagli, vedere Versioni supportate del database di Azure per [il server MySQL.](../../../mysql/concepts-supported-versions.md)

![Distribuzione di SAP BOBI in Linux - Impostazioni di CMC](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Post-installazione

### <a name="tomcat-clustering---session-replication"></a>Clustering Tomcat - Replica di sessione

Tomcat supporta il clustering di due o più server applicazioni per la replica di sessione e il failover. Le sessioni della piattaforma SAP BOBI vengono serializzate. È possibile eseguire facilmente il failover di una sessione utente in un'altra istanza di tomcat, anche in caso di errore di un server applicazioni.

Ad esempio, se un utente è connesso a un server Web che ha esito negativo mentre l'utente esplora una gerarchia di cartelle nell'applicazione SAP BI. Con un cluster configurato correttamente, l'utente può continuare a spostarsi nella gerarchia di cartelle senza essere reindirizzato alla pagina di accesso.

Nella nota SAP [2808640](https://launchpad.support.sap.com/#/notes/2808640)i passaggi per configurare il clustering tomcat vengono forniti tramite multicast. In Azure, tuttavia, il multicast non è supportato. Per il funzionamento del cluster Tomcat in Azure, è quindi necessario usare [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (nota SAP [2764907).](https://launchpad.support.sap.com/#/notes/2764907) Per configurare il cluster tomcat in Azure, vedere il blog [tomcat clustering using Static Membership for SAP BusinessObjects BI Platform (Clustering Tomcat](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) con appartenenza statica per la piattaforma SAP BusinessObjects BI) su SAP.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Livello Web di bilanciamento del carico della piattaforma SAP BI

Nella distribuzione a istanze multipla SAP BOBI i server applicazioni Web Java (livello Web) sono in esecuzione in due o più host. Per distribuire il carico degli utenti in modo uniforme tra i server Web, è possibile usare un servizio di bilanciamento del carico tra gli utenti finali e i server Web. In Azure è possibile usare Azure Load Balancer o gateway applicazione di Azure gestire il traffico verso i server applicazioni Web. I dettagli su ogni offerta sono illustrati nella sezione seguente.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Servizio di bilanciamento del carico di Azure (bilanciamento del carico basato sulla rete)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) è un servizio di bilanciamento del carico a bassa latenza (TCP, UDP) a prestazioni elevate che distribuisce il traffico tra macchine virtuali integre. Un probe di integrità del servizio di bilanciamento del carico monitora una determinata porta in ogni macchina virtuale e distribuisce il traffico solo a una o più macchine virtuali operative. È possibile scegliere un servizio di bilanciamento del carico pubblico o un servizio di bilanciamento del carico interno a seconda che la piattaforma SAP BI sia accessibile o meno da Internet. La zona è ridondante, assicurando la disponibilità elevata zone di disponibilità.

Vedere la sezione Internal Load Balancer nella figura seguente in cui il server applicazioni Web viene eseguito sulla porta 8080, porta HTTP Tomcat predefinita, che verrà monitorata dal probe di integrità. Tutte le richieste in ingresso provenienti dagli utenti finali verranno quindi reindirizzate ai server applicazioni Web (azusbosl1 o azusbosl2) nel pool back-end. Il servizio di bilanciamento del carico non supporta la terminazione TLS/SSL (nota anche come offload TLS/SSL). Se si usa Il servizio di bilanciamento del carico di Azure per distribuire il traffico tra server Web, è consigliabile usare Load Balancer Standard.

> [!NOTE]
> Se vengono inserite macchine virtuali senza indirizzi IP pubblici nel pool back-end di Load Balancer Standard interno ad Azure (nessun indirizzo IP pubblico), non sarà presente alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP](high-availability-guide-standard-load-balancer-outbound-connections.md).

![Azure Load Balancer bilanciare il traffico tra server Web](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Gateway applicazione di Azure (servizio di bilanciamento del carico dell'applicazione Web)

[gateway applicazione di Azure (AGW)](../../../application-gateway/overview.md) forniscono Application Delivery Controller (ADC) come servizio, usato per aiutare l'applicazione a indirizzare il traffico degli utenti a uno o più server applicazioni Web. Offre varie funzionalità di bilanciamento del carico di livello 7, ad esempio l'offload TLS/SSL, Web Application Firewall (WAF), l'affinità di sessione basata su cookie e altre per le applicazioni.

Nella piattaforma SAP BI il gateway applicazione indirizza il traffico Web dell'applicazione alle risorse specificate in un pool back-end, azusbosl1 o azusbos2. Assegnare un listener alla porta, creare regole e aggiungere risorse a un pool back-end. Nella figura seguente il gateway applicazione con indirizzo IP front-end privato (10.31.3.20) funge da punto di ingresso per gli utenti, gestisce le connessioni TLS/SSL in ingresso (HTTPS - TCP/443), decrittografa TLS/SSL e passa la richiesta non crittografata (HTTP - TCP/8080) ai server nel pool back-end. Con la funzionalità di terminazione TLS/SSL incorporata, è sufficiente mantenere un solo certificato TLS/SSL nel gateway applicazione, semplificando le operazioni.

![Gateway applicazione per bilanciare il traffico tra server Web](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Per configurare il gateway applicazione per il server Web SAP BOBI, è possibile fare riferimento al blog [load balancing SAP BOBI Web Servers using gateway applicazione di Azure](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) (Bilanciamento del carico dei server Web SAP BOBI tramite gateway applicazione di Azure sap).

> [!NOTE]
> È consigliabile usare gateway applicazione di Azure per bilanciare il carico del traffico verso il server Web perché offre funzionalità come l'offload SSL, la centralizzazione della gestione SSL per ridurre il sovraccarico di crittografia e decrittografia nel server, l'algoritmo Round-Robin per distribuire il traffico, le funzionalità web application firewall (WAF), la disponibilità elevata e così via.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>Piattaforma SAP BusinessObjects BI - Backup e ripristino

Backup e ripristino è un processo di creazione di copie periodiche di dati e applicazioni in una posizione separata. Può quindi essere ripristinato o ripristinato allo stato precedente se i dati o le applicazioni originali vengono persi o danneggiati. È anche un componente essenziale di qualsiasi strategia di ripristino di emergenza aziendale.

Per sviluppare una strategia di backup e ripristino completa per SAP BOBI Platform, identificare i componenti che causano tempi di inattività del sistema o interruzioni dell'applicazione. In SAP BOBI Platform il backup dei componenti seguenti è fondamentale per proteggere l'applicazione.

- Directory di installazione di SAP BOBI (Managed Premium Disks)
- File Repository Server (Azure NetApp Files o File Premium di Azure)
- Database CMS (Database di Azure per MySQL o Database in una macchina virtuale di Azure)

La sezione seguente descrive come implementare la strategia di backup e ripristino per ogni componente nella piattaforma SAP BOBI.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Backup & ripristino per la directory di installazione di SAP BOBI

In Azure il modo più semplice per eseguire il backup dei server applicazioni e di tutti i dischi collegati è usare Backup di Azure [Service.](../../../backup/backup-overview.md) Fornisce backup indipendenti e isolati per proteggere la distruzione non intenzionale dei dati nelle macchine virtuali. I backup vengono archiviati in un insieme di credenziali di Servizi di ripristino con la gestione predefinita dei punti di ripristino. La configurazione e la scalabilità sono semplici, i backup sono ottimizzati e possono essere ripristinati facilmente quando necessario.

Come parte del processo di backup, viene creato uno snapshot e i dati vengono trasferiti nell'insieme di credenziali del servizio di ripristino senza alcun impatto sui carichi di lavoro di produzione. Lo snapshot offre un livello di coerenza diverso, come descritto [nell'articolo Coerenza degli](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) snapshot. È anche possibile scegliere di eseguire il backup di subset dei dischi dati nella macchina virtuale usando la funzionalità di backup e ripristino di dischi selettivi. Per altre informazioni, vedere il documento [Backup di macchine](../../../backup/backup-azure-vms-introduction.md) virtuali di Azure e domande frequenti - Backup di macchine virtuali di [Azure.](../../../backup/backup-azure-vm-backup-faq.yml)

#### <a name="backup--restore-for-file-repository-server"></a>Backup & ripristino per il server del repository di file

Ad **Azure NetApp Files**, è possibile creare snapshot su richiesta e pianificare snapshot automatici usando i criteri di snapshot. Le copie snapshot forniscono una copia tempor volta del volume ANF. Per altre informazioni, vedere [Gestire gli snapshot usando Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

**File di Azure** backup è integrato con [](../../../backup/backup-overview.md) il servizio Backup di Azure nativo, che centralizza la funzione di backup e ripristino insieme al backup delle macchine virtuali e semplifica il lavoro delle operazioni. Per altre informazioni, vedere [Backup di Condivisione file di Azure](../../../backup/azure-file-share-backup-overview.md) e Domande frequenti - Backup [File di Azure](../../../backup/backup-azure-files-faq.yml).

#### <a name="backup--restore-for-cms-database"></a>Backup & ripristino per il database cms

Database di Azure di MySQL offre DBaaS in Azure, che crea automaticamente i backup del server e li archivia nell'archiviazione con ridondanza locale o con ridondanza geografica configurata dall'utente. Il database di Azure di MySQL accetta i backup dei file di dati e del log delle transazioni. A seconda delle dimensioni massime di archiviazione supportate, vengono esere backup completi e differenziali (server di archiviazione da 4 TB) o backup di snapshot (fino a 16 TB di server di archiviazione massimi). Questi backup consentono di ripristinare un server in qualsiasi momento all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni, che è possibile configurare [facoltativamente](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) fino a tre giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Questi file di backup non sono esposti dall'utente e non possono essere esportati. Questi backup possono essere usati solo per le operazioni di ripristino in Database di Azure per MySQL. È possibile usare [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) per copiare un database. Per altre informazioni, vedere [Backup e ripristino in Database di Azure per MySQL.](../../../mysql/concepts-backup.md)

Per il database installato nelle macchine virtuali, è possibile usare gli strumenti di backup standard [o Backup di Azure](../../../backup/sap-hana-db-about.md) per il database HANA. Se i servizi e gli strumenti di Azure non soddisfano i requisiti, è anche possibile usare altri strumenti o script di backup per creare il backup dei dischi.

## <a name="sap-businessobjects-bi-platform-reliability"></a>Affidabilità della piattaforma BI SAP BusinessObjects

La piattaforma SAP BusinessObjects BI include diversi livelli, ottimizzati per attività e operazioni specifiche. Quando un componente di un livello qualsiasi diventa non disponibile, l'applicazione SAP BOBI diventerà inaccessibile o alcune funzionalità dell'applicazione non funzioneranno. È quindi necessario assicurarsi che ogni livello sia progettato per essere affidabile per mantenere operativa l'applicazione senza interruzioni aziendali.

Questa sezione è incentrata sulle opzioni seguenti per SAP BOBI Platform -

- **Disponibilità elevata:** Una piattaforma a disponibilità elevata include almeno due di tutti gli elementi all'interno dell'area di Azure per mantenere operativa l'applicazione se uno dei server non è più disponibile.
- **Ripristino di emergenza:** Si tratta di un processo di ripristino delle funzionalità dell'applicazione in caso di perdita irreversibile, ad esempio se l'intera area di Azure diventa non disponibile a causa di una calamità naturale.

L'implementazione di questa soluzione varia in base alla natura della configurazione del sistema in Azure. I clienti devono quindi adattare la soluzione a disponibilità elevata e ripristino di emergenza in base alle esigenze aziendali.

### <a name="high-availability"></a>Disponibilità elevata

La disponibilità elevata si riferisce a un set di tecnologie che possono ridurre al minimo le interruzioni IT fornendo la continuità aziendale di applicazioni/servizi tramite componenti ridondanti, a tolleranza di errore o protetti da failover all'interno dello stesso data center. In questo caso, i data center si trova all'interno di un'area di Azure. L'articolo [Architettura e](sap-high-availability-architecture-scenarios.md) scenari a disponibilità elevata per SAP offre informazioni iniziali sulle diverse tecniche di disponibilità elevata e sulle raccomandazioni offerte in Azure per le applicazioni SAP, che completano le istruzioni riportate in questa sezione.

In base al risultato del dimensionamento della piattaforma SAP BOBI, è necessario progettare l'ambiente e determinare la distribuzione dei componenti bi tra macchine virtuali e subnet di Azure. Il livello di ridondanza nell'architettura distribuita dipende dall'obiettivo RTO (Recovery Time Objective) e dall'obiettivo del punto di ripristino (RPO) necessari per l'azienda. La piattaforma SAP BOBI include livelli e componenti diversi in ogni livello deve essere progettata per ottenere la ridondanza. In questo modo, se un componente ha esito negativo, non si verificano interruzioni dell'applicazione SAP BOBI. Ad esempio,

- Server applicazioni ridondanti come server applicazioni BI e server Web
- Componenti univoci come il database CMS, il server del repository di file Load Balancer

La sezione seguente descrive come ottenere disponibilità elevata in ogni componente della piattaforma SAP BOBI.

#### <a name="high-availability-for-application-servers"></a>Disponibilità elevata per i server applicazioni

Per i server applicazioni Web e BI installati separatamente o insieme, non è necessaria una soluzione a disponibilità elevata specifica. È possibile ottenere la disponibilità elevata tramite ridondanza, ad esempio configurando più istanze di BI e server Web in varie macchine virtuali di Azure.

Per ridurre l'impatto del tempo di inattività dovuto a uno o più eventi, è consigliabile seguire la procedura di disponibilità elevata seguente per i server applicazioni in esecuzione in più macchine virtuali.

- Usare zone di disponibilità per proteggere gli errori del data center.
- Configurare più macchine virtuali in un set di disponibilità per la ridondanza.
- Usare Managed Disks per le macchine virtuali in un set di disponibilità.
- Configurare ogni livello applicazione in set di disponibilità separati.

Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali Linux](../../availability.md)

#### <a name="high-availability-for-cms-database"></a>Disponibilità elevata per il database CMS

Se si usa il servizio Database as a Service (DBaaS) di Azure per il database CMS, il framework a disponibilità elevata viene fornito per impostazione predefinita. È sufficiente selezionare l'area e le funzionalità intrinseche di disponibilità elevata, ridondanza e resilienza del servizio senza richiedere la configurazione di componenti aggiuntivi. Per altre informazioni sul contratto di servizio dell'offerta DBaaS supportata in Azure, vedere Disponibilità elevata in Database di Azure per [MySQL](../../../mysql/concepts-high-availability.md) e [Disponibilità elevata](../../../azure-sql/database/high-availability-sla.md) per database SQL di Azure

Per altre distribuzioni DBMS per il database CMS, fare riferimento alle guide alla distribuzione DBMS per il carico di lavoro [SAP,](dbms_guide_general.md)che fornisce informazioni dettagliate sulle diverse distribuzioni DBMS e sul relativo approccio per ottenere disponibilità elevata.

#### <a name="high-availability-for-file-repository-server"></a>Disponibilità elevata per il server del repository di file

File Repository Server (FRS) si riferisce alle directory dei dischi in cui sono archiviati contenuti come report, universo e connessioni. Viene condiviso tra tutti i server applicazioni del sistema. È quindi necessario assicurarsi che sia a disponibilità elevata.

In Azure è possibile scegliere File Premium di [Azure](../../../storage/files/storage-files-introduction.md) [o Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) per la condivisione file progettata per essere a disponibilità elevata e altamente durevole per natura. Per altre informazioni, vedere [la sezione Ridondanza](../../../storage/files/storage-files-planning.md#redundancy) per File di Azure.

> [!NOTE]
> Il protocollo SMB per File di Azure è disponibile a livello generale, ma il supporto del protocollo NFS per File di Azure è attualmente in anteprima. Per altre informazioni, vedere [Supporto di NFS 4.1](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) per File di Azure è ora disponibile in anteprima

Poiché questo servizio di condivisione file non è disponibile [](https://azure.microsoft.com/en-us/global-infrastructure/services/) in tutte le aree, assicurarsi di fare riferimento al sito Prodotti disponibili in base all'area per informazioni aggiornate. Se il servizio non è disponibile nella propria area, è possibile creare un server NFS da cui è possibile condividere il file system con l'applicazione SAP BOBI. Ma è anche necessario prendere in considerazione la disponibilità elevata.

#### <a name="high-availability-for-load-balancer"></a>Disponibilità elevata per il servizio di bilanciamento del carico

Per distribuire il traffico tra server Web, è possibile usare Azure Load Balancer o gateway applicazione di Azure. La ridondanza per uno dei servizi di bilanciamento del carico può essere ottenuta in base alla SKU scelta per la distribuzione.

- Ad Azure Load Balancer, la ridondanza può essere ottenuta configurando Load Balancer Standard front-end come con ridondanza della zona. Per altre informazioni, vedere [Load Balancer Standard e zone di disponibilità](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Per il gateway applicazione, la disponibilità elevata può essere ottenuta in base al tipo di livello selezionato durante la distribuzione.
  - Lo SKU v1 supporta scenari a disponibilità elevata quando sono state distribuite due o più istanze. Azure distribuisce queste istanze nei domini di errore e di aggiornamento per impedire l'arresto simultaneo di tutte le istanze. Con questo SKU è quindi possibile ottenere la ridondanza all'interno della zona
  - Lo SKU v2 garantisce automaticamente che le nuove istanze siano distribuite tra domini di errore e domini di aggiornamento. Se si sceglie la ridondanza della zona, le istanze più recenti vengono anche distribuite tra le zone di disponibilità per offrire resilienza dagli errori delle zone. Per altre informazioni, vedere Gateway applicazione con scalabilità automatica e [ridondanza della zona v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Architettura di riferimento a disponibilità elevata per la piattaforma SAP BusinessObjects BI

L'architettura di riferimento seguente descrive la configurazione della piattaforma SAP BOBI usando il set di disponibilità, che fornisce ridondanza e disponibilità delle macchine virtuali all'interno della zona. L'architettura illustra l'uso di diversi servizi di Azure, ad esempio gateway applicazione di Azure, Azure NetApp Files e Database di Azure per MySQL per sap BOBI Platform, che offre ridondanza incorporata, che riduce la complessità della gestione di diverse soluzioni a disponibilità elevata.

Nella figura seguente il carico del traffico in ingresso (HTTPS - TCP/443) viene bilanciato usando lo SKU gateway applicazione di Azure v1, che è a disponibilità elevata quando viene distribuito in due o più istanze. Più istanze di server Web, server di gestione e server di elaborazione vengono distribuite in macchine virtuali separate per ottenere la ridondanza e ogni livello viene distribuito in set di disponibilità separati. Azure NetApp Files ridondanza incorporata all'interno data center, quindi i volumi ANF per file repository server saranno a disponibilità elevata. Il provisioning del database CMS viene eseguito in Database di Azure per MySQL (DBaaS), che ha una disponibilità elevata intrinseca. Per altre informazioni, vedere [la guida Disponibilità elevata in Database di Azure per MySQL.](../../../mysql/concepts-high-availability.md)

![Ridondanza della piattaforma BI SAP BusinessObjects tramite set di disponibilità](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

L'architettura precedente offre informazioni dettagliate su come è possibile eseguire la distribuzione DI SAP BOBI in Azure. Non vengono tuttavia trattate tutte le opzioni di configurazione possibili per la piattaforma SAP BOBI in Azure. Il cliente può personalizzare la distribuzione in base alle esigenze aziendali, scegliendo prodotti/servizi diversi per componenti diversi, ad esempio Load Balancer, File Repository Server e DBMS.

In diverse aree di Azure sono zone di disponibilità disponibili, il che significa che ha alimentazione indipendente di alimentazione, raffreddamento e rete. Consente al cliente di distribuire l'applicazione in due o tre zone di disponibilità. Per i clienti che vogliono ottenere disponibilità elevata tra le AZ, è possibile distribuire sap BOBI Platform tra zone di disponibilità, assicurando che ogni componente nell'applicazione sia ridondante in zona.

### <a name="disaster-recovery"></a>Ripristino di emergenza

L'istruzione in questa sezione illustra la strategia per fornire la protezione dal ripristino di emergenza per la piattaforma SAP BOBI. Integra il documento Ripristino di emergenza [per SAP,](../../../site-recovery/site-recovery-sap.md) che rappresenta le risorse principali per l'approccio generale al ripristino di emergenza SAP.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Architettura di riferimento del ripristino di emergenza per la piattaforma BI SAP BusinessObjects

Questa architettura di riferimento esegue la distribuzione a più istanze della piattaforma SAP BOBI con server applicazioni ridondanti. Per il ripristino di emergenza, è necessario eseguire il failover di tutti i livelli in un'area secondaria. Ogni livello usa una strategia diversa per fornire la protezione dal ripristino di emergenza.

![Ripristino di emergenza della piattaforma BI SAP BusinessObjects](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Bilanciamento del carico

Load Balancer viene usato per distribuire il traffico tra i server applicazioni Web della piattaforma SAP BOBI. Per ottenere il ripristino di gateway applicazione di Azure, implementare la configurazione parallela del gateway applicazione nell'area secondaria.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Macchine virtuali che eseguono server applicazioni Web e BI

Azure Site Recovery può essere usato per replicare macchine virtuali che eseguono server applicazioni Web e BI nell'area secondaria. Replica i server nell'area secondaria in modo che in caso di emergenza e interruzioni sia possibile eseguire facilmente il failover nell'ambiente replicato e continuare a lavorare

#### <a name="file-repository-servers"></a>Server del repository di file

- **Azure NetApp Files** offre volumi NFS e SMB, quindi qualsiasi strumento di copia basato su file può essere usato per replicare i dati tra aree di Azure. Per altre informazioni su come copiare un volume ANF in un'altra area, vedere Domande [frequenti sulle](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region) Azure NetApp Files

  È possibile usare Azure NetApp Files tra aree, attualmente [in](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) anteprima che usa la tecnologia NetApp SnapMirror®. Pertanto, solo i blocchi modificati vengono inviati in rete in un formato compresso ed efficiente. Questa tecnologia proprietaria riduce al minimo la quantità di dati necessari per la replica tra le aree, con un risparmio sui costi di trasferimento dei dati. Riduce anche il tempo di replica per ottenere un obiettivo del punto di ripristino (RPO) più piccolo. Per altre [informazioni, vedere Requisiti e considerazioni per l'uso](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) della replica tra aree.

- **I file Premium di Azure** supportano solo l'archiviazione con ridondanza locale (LRS) e l'archiviazione con ridondanza della zona (ZRS). Per la strategia di ripristino di emergenza di File Premium di Azure, è possibile usare [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) o [Azure PowerShell](/powershell/module/az.storage/) per copiare i file in un altro account di archiviazione in un'area diversa. Per altre informazioni, vedere Ripristino [di emergenza e failover dell'account di archiviazione](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>Database CMS

Database di Azure per MySQL offre più opzioni per ripristinare il database in caso di emergenza. Scegliere l'opzione appropriata per l'azienda.

- Abilitare le repliche in lettura tra aree per migliorare la continuità aziendale e la pianificazione del ripristino di emergenza. È possibile eseguire la replica dal server di origine a un massimo di cinque repliche. Le repliche di lettura vengono aggiornate in modo asincrono usando la tecnologia di replica dei log binari di MySQL. Le repliche sono nuovi server da gestire in modo simile ai normali server di Database di Azure per MySQL. Per altre informazioni sulle repliche in lettura, sulle aree disponibili, sulle restrizioni e su come eseguire il failover, vedere l'articolo [concetti relativi alle repliche in lettura.](../../../mysql/concepts-read-replicas.md)

- Usare la funzionalità di ripristino geografico di Database di Azure per MySQL che ripristina il server usando backup con ridondanza geografica. Questi backup sono accessibili anche quando l'area in cui è ospitato il server è offline. È possibile eseguire il ripristino da questi backup a qualsiasi altra area e riportare il server online.

  > [!NOTE]
  > Il ripristino geografico è possibile solo se è stato effettuato il provisioning del server con l'archivio di backup con ridondanza geografica. La modifica **delle opzioni di ridondanza del** backup dopo la creazione del server non è supportata. Per altre informazioni, vedere [l'articolo Ridondanza del](../../../mysql/concepts-backup.md#backup-redundancy-options) backup.

Di seguito è riportata la raccomandazione per il ripristino di emergenza di ogni livello usato in questo esempio.

| Livelli della piattaforma SAP BOBI   | Recommendation                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Gateway applicazione di Azure | Configurazione parallela del gateway applicazione nell'area secondaria                                                |
| Server applicazioni Web   | Eseguire la replica usando Site Recovery                                                                         |
| Server applicazioni BI    | Eseguire la replica usando Site Recovery                                                                         |
| Azure NetApp Files        | Strumento di copia basata su file per replicare i dati nell'area **secondaria o** nella replica tra aree ANF (anteprima) |
| Database di Azure per MySQL  | Repliche di lettura tra aree **o ripristino** di backup da backup con ridondanza geografica.                             |

## <a name="next-steps"></a>Passaggi successivi

- [Configurare il ripristino di emergenza per una distribuzione di app SAP multilivello](../../../site-recovery/site-recovery-sap.md)
- [Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP](planning-guide.md)
- [Distribuzione di Macchine virtuali di Azure per SAP](deployment-guide.md)
- [Distribuzione DBMS di Macchine virtuali di Azure per SAP](./dbms_guide_general.md)
