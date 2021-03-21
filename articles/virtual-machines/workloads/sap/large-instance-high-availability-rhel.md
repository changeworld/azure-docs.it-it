---
title: Disponibilità elevata in istanze large di Azure per SAP in RHEL
description: Informazioni su come automatizzare un failover del database di SAP HANA usando un cluster Pacemaker in Red Hat Enterprise Linux.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 99e9994d01e4579bf6ef2e369e0fe85c48af52ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182435"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Disponibilità elevata in istanze large di Azure per SAP in RHEL

Questo articolo illustra come configurare il cluster Pacemaker in RHEL 7,6 per automatizzare un failover del database SAP HANA. Per completare i passaggi illustrati in questa guida, è necessario avere una conoscenza corretta di Linux, SAP HANA e pacemaker.

La tabella seguente include i nomi host usati in questo articolo. I blocchi di codice nell'articolo mostrano i comandi che devono essere eseguiti, nonché l'output di tali comandi. Prestare molta attenzione al nodo a cui si fa riferimento in ogni comando.

| Tipo | Nome dell'host | Nodo|
|-------|-------------|------|
|Host primario|`sollabdsm35`|nodo 1|
|Host secondario|`sollabdsm36`|nodo 2|

## <a name="configure-your-pacemaker-cluster"></a>Configurare il cluster Pacemaker


Prima di iniziare la configurazione del cluster, configurare lo scambio di chiavi SSH per stabilire una relazione di trust tra i nodi.

1. Usare i comandi seguenti per creare un identico `/etc/hosts` in entrambi i nodi.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    0.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    0.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    0.20.251.150 sollabdsm36-st

    10.20.251.151 sollabdsm35-st

    

    10.20.252.151 sollabdsm36-back

    10.20.252.150 sollabdsm35-back

    

    10.20.253.151 sollabdsm36-node

    10.20.253.150 sollabdsm35-node

    ```

2.  Creare ed scambiare le chiavi SSH.
    1. Genera chiavi SSH.

       ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
       ```
    2. Copiare le chiavi negli altri host per SSH con password.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Disabilitare SELinux in entrambi i nodi.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Riavviare i server e usare il comando seguente per verificare lo stato di SELinux.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. Configurare NTP (Network Time Protocol). I fusi orari e temporali per entrambi i nodi del cluster devono corrispondere. Usare il comando seguente per aprire `chrony.conf` e verificare il contenuto del file.
    1. Il contenuto seguente deve essere aggiunto al file di configurazione. Modificare i valori effettivi in base all'ambiente in uso.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Abilitare il servizio Chrony. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. Aggiornare il sistema
    1. Installare prima di tutto gli aggiornamenti più recenti nel sistema prima di iniziare a installare il dispositivo SBD.
    1. Se non si desidera un aggiornamento completo del sistema, anche se si consiglia di aggiornare i pacchetti seguenti come minimo.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```
 

7. Installare i repository SAP HANA e RHEL-HA.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Installare gli strumenti pacemaker, SBD, OpenIPMI, ipmitools e fencing_sbd in tutti i nodi.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitools
    ```

  ## <a name="configure-watchdog"></a>Configura watchdog

In questa sezione viene illustrato come configurare watchdog. In questa sezione vengono usati gli stessi due host `sollabdsm35` e `sollabdsm36` , a cui si fa riferimento all'inizio di questo articolo.

1. Verificare che il daemon watchdog non sia in esecuzione in alcun sistema.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. Il watchdog predefinito di Linux, che verrà installato durante l'installazione, è il watchdog iTCO, che non è supportato dai sistemi UCS e HPE SDFlex. Questo watchdog deve pertanto essere disabilitato.
    1. Il watchdog errato è installato e caricato nel sistema:
       ```
   
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. Scaricare il driver errato dall'ambiente:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Per assicurarsi che il driver non venga caricato durante il successivo avvio del sistema, il driver deve essere Blocklisting. Per bloccare i moduli iTCO, aggiungere quanto segue alla fine del `50-blacklist.conf` file:
       ```
   
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. Copiare il file nell'host secondario.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. Verificare se il servizio IPMI è stato avviato. È importante che il timer IPMI non sia in esecuzione. La gestione del timer viene eseguita dal servizio pacemaker SBD.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. Per impostazione predefinita, il dispositivo richiesto è/dev/watchdog non verrà creato.

    ```
    No watchdog device was created

    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. Configurare il watchdog IPMI.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. Copiare il file di configurazione del watchdog nel database secondario.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Abilitare e avviare il servizio IPMI.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     Ora il servizio IPMI viene avviato e viene creato il/dev/watchdog del dispositivo, ma il timer è ancora arrestato. Successivamente, SBD gestirà la reimpostazione del watchdog e Abilita il timer IPMI.
7.  Verificare che/dev/watchdog esista ma non è in uso.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>Configurazione di SBD
In questa sezione viene illustrato come configurare SBD. In questa sezione vengono usati gli stessi due host `sollabdsm35` e `sollabdsm36` , a cui si fa riferimento all'inizio di questo articolo.

1.  Verificare che il disco iSCSI o FC sia visibile in entrambi i nodi. Questo esempio usa un dispositivo SBD basato su FC. Per ulteriori informazioni sulla schermatura SBD, vedere la [documentazione di riferimento](http://www.linux-ha.org/wiki/SBD_Fencing).
2.  Il LUN-ID deve essere identico in tutti i nodi.
  
3.  Controllare lo stato dei percorsi multipli per il dispositivo SBD.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  Creazione dei dischi SBD e configurazione della schermatura primitiva del cluster. Questo passaggio deve essere eseguito nel primo nodo.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  Copiare la configurazione di SBD in Node2.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  Verificare che il disco SBD sia visibile da entrambi i nodi.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  Aggiungere il dispositivo SBD nel file di configurazione di SBD.

    ```
    \# SBD_DEVICE specifies the devices to use for exchanging sbd messages

    \# and to monitor. If specifying more than one path, use ";" as

    \# separator.

    \#

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    \## Type: yesno
     Default: yes
     \# Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Inizializzazione del cluster
In questa sezione si inizializza il cluster. In questa sezione vengono usati gli stessi due host `sollabdsm35` e `sollabdsm36` , a cui si fa riferimento all'inizio di questo articolo.

1.  Configurare la password utente del cluster (tutti i nodi).
    ```
    passwd hacluster
    ```
2.  Avviare i PC in tutti i sistemi.
    ```
    systemctl enable pcsd
    ```
  

3.  Arrestare il firewall e disabilitarlo (tutti i nodi).
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Avviare il servizio PCSD.
    ```
    systemctl start pcsd
    ```
  
  

5.  Eseguire l'autenticazione del cluster solo da Node1.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36



        Username: hacluster

            Password:

            sollabdsm35.localdomain: Authorized

            sollabdsm36.localdomain: Authorized

     ``` 

6.  Creare il cluster.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  Verificare lo stato del cluster.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. Se un nodo non partecipa al controllo del cluster se il firewall è ancora in esecuzione.

  

9. Creare e abilitare il dispositivo SBD
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  

10. Arrestare il cluster riavviare i servizi cluster in tutti i nodi.

    ```
    pcs cluster stop --all
    ```


11. Riavviare i servizi cluster in tutti i nodi.

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Corosync deve avviare il servizio SBD.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Riavviare il cluster, se non è stato avviato automaticamente da PCSD.

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Abilitare le impostazioni di STONITH.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Verificare lo stato del nuovo cluster con una sola risorsa.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. Ora è necessario eseguire il timer IPMI e il dispositivo/dev/watchdog deve essere aperto da SBD.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm351 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. Controllare lo stato di SBD.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Testare la schermatura SBD arrestando in modo anomalo il kernel.

    * Attivare l'arresto anomalo del kernel.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * Il secondo test da eseguire consiste nel recintare un nodo usando i comandi PCS.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. Per il resto del clustering di SAP HANA è possibile disabilitare STONITH impostando:

   * set di proprietà PCS `stonith-enabled=false`
   * Questo parametro deve essere impostato su true per l'utilizzo produttivo. Se questo parametro non è impostato su true, il cluster non sarà supportato.
   * set di proprietà PCS `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>Integrazione di HANA nel cluster

In questa sezione si integra HANA nel cluster. In questa sezione vengono usati gli stessi due host `sollabdsm35` e `sollabdsm36` , a cui si fa riferimento all'inizio di questo articolo.

Sono disponibili due opzioni per l'integrazione di HANA. La prima opzione è una soluzione ottimizzata per i costi in cui è possibile utilizzare il sistema secondario per eseguire il sistema QAS. Questo metodo non è consigliato perché non lascia alcun sistema per testare gli aggiornamenti sul software del cluster, sul sistema operativo o su HANA e gli aggiornamenti della configurazione possono causare tempi di inattività non pianificati del sistema PRD. Inoltre, se il sistema PRD deve essere attivato nel sistema secondario, il QAS deve essere arrestato nel nodo secondario. La seconda opzione consiste nell'installare il sistema QAS in un cluster e usare un secondo cluster per il PRD. Questa opzione consente inoltre di testare tutti i componenti prima che vengano inseriti nell'ambiente di produzione. Questo articolo illustra come configurare la seconda opzione.


* Questo processo è la compilazione della descrizione RHEL sulla pagina:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>Passaggi da seguire per configurare HSR

1.  Queste sono le azioni da eseguire su node1 (primario).
    1. Verificare che la modalità log del database sia impostata su Normal.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p SAPhana10 -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. SAP HANA la replica di sistema funzionerà solo dopo l'esecuzione del backup iniziale. Il comando che segue crea un backup iniziale nella `/tmp/` Directory. Selezionare un file System di backup appropriato per il database. 
       ```
       * hdbsql -i 00 -u system -p SAPhana10 "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```
    

    3. Esegui backup di tutti i contenitori di database di questo database.
       ```
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"
   
       
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Abilitare il processo HSR nel sistema di origine.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Verificare lo stato del sistema primario.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. Queste sono le azioni da eseguire su NODE2 (secondario).
     1. Arrestare il database.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Solo per SAP HANA 2.0, copiare il sistema di SAP HANA `PKI SSFS_HR2.KEY` e `SSFS_HR2.DAT` i file dal nodo primario a quello secondario.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. Abilitare secondario come sito di replica.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. Avviare il database.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. Verificare lo stato del database.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. È anche possibile ottenere ulteriori informazioni sullo stato della replica:
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>Descrizione della modalità di replica log

Per ulteriori informazioni sulla modalità di replica dei log, vedere la [documentazione di SAP ufficiale](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/c039a1a5b8824ecfa754b55e0caffc01.html).
  

#### <a name="network-setup-for-hana-system-replication"></a>Configurazione di rete per la replica di sistema HANA


Per assicurarsi che il traffico di replica usi la VLAN corretta per la replica, è necessario configurarlo correttamente in `global.ini` . Se si ignora questo passaggio, HANA utilizzerà la VLAN di accesso per la replica, che potrebbe essere indesiderata.


Gli esempi seguenti illustrano la configurazione della risoluzione dei nomi host per la replica di sistema in un sito secondario. È possibile identificare tre reti distinte:

* Rete pubblica con indirizzi nell'intervallo di 10.0.1. *

* Rete per la comunicazione interna del SAP HANA tra host in ogni sito: 192.168.1. *

* Rete dedicata per la replica di sistema: 10.5.1. *

Nel primo esempio, il `[system_replication_communication]listeninterface` parametro è stato impostato su `.global` e vengono specificati solo gli host del sito di replica adiacente.

Nell'esempio seguente il `[system_replication_communication]listeninterface` parametro è stato impostato su `.internal` e vengono specificati tutti gli host di entrambi i siti.

  

### <a name="source-sap-ag-sap-hana-hrs-networking"></a>Rete SAP AG SAP HANA HRS di origine

  

Per la replica di sistema, non è necessario modificare il `/etc/hosts` file, i nomi host interni ("virtuali") devono essere mappati agli indirizzi IP nel `global.ini` file per creare una rete dedicata per la replica di sistema. La sintassi è la seguente:

global.ini

[system_replication_hostname_resolution]

<IP-address_site>=<Internal-host-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Configurare SAP HANA in un cluster Pacemaker
In questa sezione viene illustrato come configurare SAP HANA in un cluster pacemaker. In questa sezione vengono usati gli stessi due host `sollabdsm35` e `sollabdsm36` , a cui si fa riferimento all'inizio di questo articolo.

Assicurarsi di aver soddisfatto i prerequisiti seguenti:  

* Il cluster Pacemaker è configurato in base alla documentazione e ha una schermatura corretta e funzionante

* SAP HANA avvio all'avvio è disabilitato in tutti i nodi del cluster perché l'avvio e l'arresto verranno gestiti dal cluster

* SAP HANA la replica e l'acquisizione del sistema con gli strumenti di SAP funzionano correttamente tra i nodi del cluster

* SAP HANA contiene un account di monitoraggio che può essere usato dal cluster da entrambi i nodi del cluster

* Entrambi i nodi hanno sottoscritto i canali "disponibilità elevata" e "RHEL for SAP HANA" (RHEL 6, RHEL 7)

  

* In generale, eseguire tutti i comandi PC solo dal nodo on, perché il CIB verrà aggiornato automaticamente dalla shell PC.

* [Altre informazioni sui criteri quorum](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>Passaggi per la configurazione 
1. Configurare i PC.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Configurare Corosync.
    ```
    https://access.redhat.com/solutions/1293523 --> quorum information RHEL7

    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

1.  Creare una risorsa SAPHanaTopology clonata.
    ```
    pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1 interleave=true
    SAPHanaTopology resource is gathering status and configuration of SAP
    HANA System Replication on each node. SAPHanaTopology requires
    following attributes to be configured.



        Attribute Name Description

        SID SAP System Identifier (SID) of SAP HANA installation. Must be
    same for all nodes.

    InstanceNumber 2-digit SAP Instance identifier.
    pcs resource show SAPHanaTopology_HR2_00-clone

    Clone: SAPHanaTopology_HR2_00-clone

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true

        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat
    type=SAPHanaTopology)

        Attributes: InstanceNumber=00 SID=HR2

        Operations: monitor interval=60 timeout=60
    (SAPHanaTopology_HR2_00-monitor-interval-60)

        start interval=0s timeout=180
    (SAPHanaTopology_HR2_00-start-interval-0s)

        stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)

    ```

3.  Creare una risorsa SAPHana primaria/secondaria.

    ```
    SAPHana resource is responsible for starting, stopping and relocating the SAP HANA database. This resource must be run as a Primary/    Secondary cluster resource. The resource has the following attributes.

    

    Attribute Name Required? Default value Description

    SID Yes None SAP System Identifier (SID) of SAP HANA installation. Must be same for all nodes.

    InstanceNumber Yes none 2-digit SAP Instance identifier.

    PREFER_SITE_TAKEOVER

    no yes Should cluster prefer to switchover to secondary instance instead of restarting primary locally? ("no": Do prefer restart locally;   "yes": Do prefer takeover to remote site)

    AUTOMATED_REGISTER no false Should the former SAP HANA primary be registered as secondary after takeover and DUPLICATE_PRIMARY_TIMEOUT?     ("false": no, manual intervention will be needed; "true": yes, the former primary will be registered by resource agent as secondary)

    DUPLICATE_PRIMARY_TIMEOUT no 7200 Time difference (in seconds) needed between primary time stamps, if a dual-primary situation occurs. If   the time difference is less than the time gap, then the cluster holds one or both instances in a "WAITING" status. This is to give an   admin a chance to react on a failover. A failed former primary will be registered after the time difference is passed. After this   registration to the new primary all data will be overwritten by the system replication.
    ```
  

5.  Creare la risorsa HANA.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200   AUTOMATED_REGISTER=true primary notify=true clone-max=2 clone-node-max=1 interleave=true

    pcs resource show SAPHana_HR2_00-primary



    Primary: SAPHana_HR2_00-primary

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true

        Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)

        Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200
    InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2

        Operations: demote interval=0s timeout=320
    (SAPHana_HR2_00-demote-interval-0s)

        monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)

        monitor interval=121 role=Secondary timeout=60
    (SAPHana_HR2_00-monitor-

        interval-121)

        monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-

        interval-119)

        promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)

        start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)

        stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  Creare una risorsa indirizzo IP virtuale.

    ```
    Cluster will contain Virtual IP address in order to reach the Primary instance of SAP HANA. Below is example command to create IPaddr2  resource with IP 10.7.0.84/24

    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Creazione di vincoli.

    ```
    For correct operation we need to ensure that SAPHanaTopology resources are started before starting the SAPHana resources and also that  the virtual IP address is present on the node where the Primary resource of SAPHana is running. To achieve this, the following 2    constraints need to be created.

    pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
    pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
    ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>Test dello spostamento manuale della risorsa SAPHana in un altro nodo

#### <a name="sap-hana-takeover-by-cluster"></a>(Acquisizione di SAP HANA per cluster)


Per testare lo spostamento della risorsa SAPHana da un nodo a un altro, usare il comando seguente. Si noti che l'opzione `--primary` non deve essere usata quando si esegue il comando seguente a causa del funzionamento interno della risorsa SAPHana.
```pcs resource move SAPHana_HR2_00-primary```

Quando ogni risorsa PC sposta la chiamata al comando, il cluster crea vincoli di posizione per ottenere lo spostamento della risorsa. Questi vincoli devono essere rimossi per consentire il failover automatico in futuro.
Per rimuoverli, è possibile usare il comando seguente.
```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* Accedere a HANA come verifica.

  * host abbassato di sotto:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Host promosso:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

Con l'opzione `AUTOMATED_REGISTER=false` , non è possibile spostarsi avanti e indietro.

Se questa opzione è impostata su false, è necessario ripetere la registrazione del nodo:

  
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```
  

Ora node2, che era il primario, funge da host secondario.

Si consiglia di impostare questa opzione su true per automatizzare la registrazione dell'host abbassato di livello.

  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true

pcs cluster node clear node1
```
