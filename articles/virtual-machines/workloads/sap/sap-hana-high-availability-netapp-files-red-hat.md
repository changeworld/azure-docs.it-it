---
title: Disponibilità elevata di SAP HANA scalabilità verticale con ANF in RHEL | Microsoft Docs
description: Stabilire la disponibilità elevata SAP HANA con Azure Data Framework in macchine virtuali di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 774344c4215088482b110de91f8951bae4a41d25
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365825"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Disponibilità elevata di SAP HANA scalabilità verticale con Azure NetApp Files in Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Questo articolo descrive come configurare la replica di sistema di SAP HANA nella distribuzione con scalabilità verticale, quando i file system HANA vengono montati tramite NFS usando Azure NetApp Files (ANF). Nelle configurazioni di esempio e nei comandi di installazione vengono usati il numero di istanza **03** e l'ID di sistema HANA **HN1.** La replica SAP HANA è costituita da un nodo primario e da almeno un nodo secondario.

Quando i passaggi in questo documento sono contrassegnati con i prefissi seguenti, il significato è il seguente:

- **[A]**: il passaggio si applica a tutti i nodi
- **[1]**: Il passaggio si applica solo a node1
- **[2]**: Il passaggio si applica solo a node2
 
Leggere prima di tutto i documenti e le note SAP seguenti:

- Nota SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533), contenente:
    - Elenco delle dimensioni delle macchine virtuali di Azure supportate per la distribuzione di software SAP.
    - Informazioni importanti sulla capacità per le dimensioni delle macchine virtuali di Azure.
    - Software SAP e combinazioni di sistemi operativi e database supportati.
    - Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure.
- La nota SAP [2015553](https://launchpad.support.sap.com/#/notes/2015553) elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
- La nota SAP [405827](https://launchpad.support.sap.com/#/notes/405827) elenca le file system per l'ambiente HANA.
- La nota SAP [2002167 contiene](https://launchpad.support.sap.com/#/notes/2002167) le impostazioni del sistema operativo consigliate per Red Hat Enterprise Linux.
- La nota SAP [2009879](https://launchpad.support.sap.com/#/notes/2009879) SAP HANA linee guida per Red Hat Enterprise Linux.
- La nota SAP [2178632](https://launchpad.support.sap.com/#/notes/2178632) contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
- La nota SAP [2191498](https://launchpad.support.sap.com/#/notes/2191498) contiene la versione dell'agente host SAP per Linux necessaria in Azure.
- La nota SAP [2243692](https://launchpad.support.sap.com/#/notes/2243692) contiene informazioni sulle licenze SAP in Linux in Azure.
- La nota SAP [1999351](https://launchpad.support.sap.com/#/notes/1999351) contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
- [Sap Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) include tutte le note SAP necessarie per Linux.
- [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
- [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide]
- [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
- [SAP HANA replica di sistema nel cluster Pacemaker.](https://access.redhat.com/articles/3004101)
- Documentazione generale di RHEL
    - [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
    - [Disponibilità elevata Add-On amministrazione.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Informazioni di riferimento Add-On disponibilità elevata.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Configurare SAP HANA di sistema in Scale-Up in un cluster Pacemaker quando i file system HANA sono in condivisioni NFS](https://access.redhat.com/solutions/5156571)
- Documentazione di RHEL specifica di Azure:
    - [Criteri di supporto per i cluster RHEL a disponibilità elevata: Macchine virtuali di Microsoft Azure come membri del cluster.](https://access.redhat.com/articles/3131341)
    - [Installazione e configurazione di un Red Hat Enterprise Linux 7.4 (e versioni successive) High-Availability cluster Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Installare SAP HANA in Red Hat Enterprise Linux per l'uso in Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [Configurare SAP HANA la replica di sistema con scalabilità verticale nel cluster Pacemaker quando i file system HANA sono in condivisioni NFS](https://access.redhat.com/solutions/5156571)
- [Applicazioni NetApp SAP su Microsoft Azure con Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
- [Volumi NFS v4.1 in Azure NetApp Files per SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Panoramica

Tradizionalmente nell'ambiente di scalabilità verticale tutti i file system SAP HANA vengono montati dall'archiviazione locale. La configurazione della disponibilità elevata della SAP HANA di sistema in Red Hat Enterprise Linux è pubblicata nella guida Configurare [SAP HANA replica di sistema in RHEL](./sap-hana-high-availability-rhel.md)

Per ottenere una disponibilità elevata del sistema di scalabilità verticale nelle condivisioni NFS di [Azure NetApp Files,](../../../azure-netapp-files/index.yml) è necessaria una configurazione aggiuntiva delle risorse nel cluster, per consentire il ripristino delle risorse HANA, quando un nodo perde l'accesso alle condivisioni NFS in ANF. SAP HANA  Il cluster gestisce i montamenti NFS, consentendo di monitorare l'integrità delle risorse. Vengono applicate le dipendenze tra file system e SAP HANA risorse.  

![SAP HANA ha scale-up in ANF](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA file system vengono montati su condivisioni NFS usando Azure NetApp Files in ogni nodo. I file system /hana/data, /hana/log e /hana/shared sono univoci per ogni nodo. 

Montato su node1 (**hanadb1**)

- 10.32.2.4:/**hanadb1**-data-mnt00001 su /hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 in /hana/log
- 10.32.2.4:/**hanadb1**-shared-mnt00001 in /hana/shared

Montato sul nodo 2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-data-mnt00001 su /hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 in /hana/log
- 10.32.2.4:/**hanadb2**-shared-mnt00001 in /hana/shared

> [!NOTE]
> I file system /hana/shared, /hana/data e /hana/log non vengono condivisi tra i due nodi. Ogni nodo del cluster dispone di file system separati.   

La SAP HANA di replica di sistema usa un nome host virtuale dedicato e indirizzi IP virtuali. Per usare un indirizzo IP virtuale in Azure, occorre il bilanciamento del carico. L'elenco seguente mostra la configurazione del servizio di bilanciamento del carico:

- Configurazione front-end: indirizzo IP 10.32.0.10 per hn1-db
- Configurazione back-end: Connessione a interfacce di rete primarie di tutte le macchine virtuali che devono far parte della replica del sistema HANA
- Porta probe: Porta 62503
- Regole di bilanciamento del carico: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (se si usa Azure Load Balancer Basic)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Configurare l'infrastruttura Azure NetApp File

Prima di procedere con la configurazione per l'infrastruttura Azure NetApp Files, acquisire familiarità con la documentazione di Azure [NetApp Files.](../../../azure-netapp-files/index.yml)

Azure NetApp Files è disponibile in diverse aree [di Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Verificare se l'area di Azure selezionata offre Azure NetApp Files.

Per informazioni sulla disponibilità dei dati in base Azure NetApp Files'area di Azure, vedere Disponibilità Azure NetApp Files [in base all'area di Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)

Prima di distribuire Azure NetApp Files, richiedere l'onboarding Azure NetApp Files in [Registrarsi](../../../azure-netapp-files/azure-netapp-files-register.md)per ottenere Azure NetApp Files istruzioni .

### <a name="deploy-azure-netapp-files-resources"></a>Distribuire le risorse di Azure NetApp Files

Le istruzioni seguenti presuppongono che la rete virtuale di Azure sia già [stata distribuita.](../../../virtual-network/virtual-networks-overview.md) Le Azure NetApp Files e le macchine virtuali, in cui verranno montate le risorse Azure NetApp Files, devono essere distribuite nella stessa rete virtuale di Azure o nelle reti virtuali di Azure con peer.

1. Se le risorse non sono ancora state distribuite, richiedere [l'onboarding Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Creare un account NetApp nell'area di Azure selezionata seguendo le istruzioni riportate in [Creare un account NetApp.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)

3.  Configurare un pool di Azure NetApp Files seguendo le istruzioni riportate in [Configurare un pool Azure NetApp Files capacità.](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)

    L'architettura HANA presentata in questo articolo usa un singolo pool Azure NetApp Files di capacità a livello *di servizio Ultra.* Per i carichi di lavoro HANA in Azure, è consigliabile usare un livello Azure NetApp Files di servizio *Ultra* o *Premium.* [](../../../azure-netapp-files/azure-netapp-files-service-levels.md)

4.  Delegare una subnet Azure NetApp Files, come descritto nelle istruzioni in [Delegare una subnet a Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5.  Distribuire Azure NetApp Files volumi seguendo le istruzioni in [Creare un volume NFS per Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

    Durante la distribuzione dei volumi, assicurarsi di selezionare la versione NFSv4.1. Distribuire i volumi nella subnet di Azure NetApp Files designata. Gli indirizzi IP dei volumi di Azure NetApp vengono assegnati automaticamente.

    Tenere presente che le Azure NetApp Files e le macchine virtuali di Azure devono essere nella stessa rete virtuale di Azure o nelle reti virtuali di Azure con peer. Ad esempio, hanadb1-data-mnt00001, hanadb1-log-mnt00001 e così via, i nomi dei volumi e nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 e così via sono i percorsi di file per i volumi Azure NetApp Files.
    
    In **hanadb1**

    - Volume hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume hanadb1-shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    In **hanadb2**

    - Volume hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume hanadb2-shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Considerazioni importanti

Durante la creazione del Azure NetApp Files per SAP HANA di scalabilità verticale, tenere presente quanto segue:

- Il pool di capacità minima è di 4 tebibyte (TiB).
- La dimensione minima del volume è di 100 gibibyte (GiB).
- Azure NetApp Files e tutte le macchine virtuali in cui verranno montati i volumi Azure NetApp Files devono essere nella stessa rete virtuale di Azure o in reti virtuali [con peering](../../../virtual-network/virtual-network-peering-overview.md) nella stessa area.
- La rete virtuale selezionata deve avere una subnet delegata a Azure NetApp Files.
- La velocità effettiva di un Azure NetApp Files volume è una funzione della quota del volume e del livello di servizio, come documentato in Livello di servizio [per](../../../azure-netapp-files/azure-netapp-files-service-levels.md)Azure NetApp Files . Quando si ridimensionano i volumi HANA Azure NetApp, assicurarsi che la velocità effettiva risultante soddisfi i requisiti di sistema di HANA.
- Con i Azure NetApp Files [di](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)esportazione è possibile controllare i client consentiti, il tipo di accesso (lettura/scrittura, sola lettura e così via).
- La Azure NetApp Files funzionalità non è ancora in grado di riconoscere la zona. Attualmente, la funzionalità non viene distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presente le implicazioni di latenza potenziali in alcune aree di Azure.

> [!IMPORTANT]
> Per carichi di lavoro SAP HANA, è fondamentale una bassa latenza. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi Azure NetApp Files siano distribuiti in prossimità.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Ridimensionamento del database HANA in Azure NetApp Files

La velocità effettiva di un volume Azure NetApp Files è una funzione delle dimensioni del volume e del livello di servizio, come documentato [in](../../../azure-netapp-files/azure-netapp-files-service-levels.md)Livello di servizio per Azure NetApp Files .

Quando si progetta l'infrastruttura per SAP in Azure, tenere presenti alcuni requisiti di archiviazione minimi di SAP, che si traducono in caratteristiche di velocità effettiva minima:

- Lettura/scrittura su /hana/log di 250 megabyte al secondo (MB/s) con dimensioni di I/O di 1 MB.
- Attività di lettura di almeno 400 MB/s per /hana/data per dimensioni di I/O di 16 MB e 64 MB.
- Attività di scrittura di almeno 250 MB/s per /hana/data con dimensioni di I/O di 16 MB e 64 MB.

I [limiti di velocità effettiva di Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) per 1 TiB della quota del volume sono:

- Archiviazione Premium- 64 MiB/s.
- Livello di archiviazione Ultra: 128 MiB/s.

Per soddisfare i requisiti di velocità effettiva minima SAP per /hana/data e /hana/log e le linee guida per /hana/shared, le dimensioni consigliate sono:

|    Volume    | Dimensioni del Archiviazione Premium dati | Dimensioni del livello di archiviazione Ultra | Protocollo NFS supportato |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 o v4.1    |


> [!NOTE]
> Le Azure NetApp Files di dimensionamento seguenti sono destinate a soddisfare i requisiti minimi consigliati da SAP per i provider di infrastruttura. Nelle distribuzioni reali dei clienti e negli scenari di carico di lavoro, queste dimensioni potrebbero non essere sufficienti. Considerare quindi queste indicazioni come punto di inizio e adattarle in base ai requisiti del carico di lavoro specifico.

> [!TIP]
> È possibile ridimensionare Azure NetApp Files volumi in modo dinamico, senza dover *smontare* i volumi, arrestare le macchine virtuali o arrestare SAP HANA. Questo approccio offre flessibilità per soddisfare le esigenze di velocità effettiva previste e impreviste dell'applicazione.

> [!NOTE]
> Tutti i comandi per montare /hana/shared in questo articolo vengono presentati per i volumi NFSv4.1 /hana/shared.
> Se i volumi /hana/shared sono stati distribuiti come volumi NFSv3, non dimenticare di modificare i comandi di montaggio per /hana/shared per NFSv3.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Distribuire una macchina virtuale Linux tramite portale di Azure 

Per prima cosa è necessario creare i volumi Azure NetApp Files. Seguire questa procedura:

1.  Creare un gruppo di risorse.
2.  Creare una rete virtuale.
3.  Creare un set di disponibilità. Impostare il numero massimo di domini di aggiornamento.
4.  Creare un servizio di bilanciamento del carico (interno). Si consiglia di usare Load Balancer Standard.
    Selezionare la rete virtuale creata nel passaggio 2.
5.  Creare la macchina virtuale 1 (**hanadb1**). 
6.  Creare la macchina virtuale 2 (**hanadb2**).  
7.  Durante la creazione della macchina virtuale, non verrà aggiunto alcun disco perché tutti i punti di montaggio saranno in condivisioni NFS da Azure NetApp Files. 

> [!IMPORTANT]
> L'indirizzo IP mobile non è supportato in una configurazione IP secondaria della scheda di interfaccia di rete negli scenari di bilanciamento del carico. Per informazioni dettagliate, [vedere Limitazioni del servizio di bilanciamento del carico di Azure.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Se è necessario un indirizzo IP aggiuntivo per la macchina virtuale, distribuire una seconda scheda di interfaccia di rete.    

> [!NOTE] 
> Se vengono inserite macchine virtuali senza indirizzi IP pubblici nel pool back-end di Load Balancer Standard interno ad Azure (nessun indirizzo IP pubblico), non sarà presente alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).

8.  Se si usa Load Balancer standard, seguire questa procedura di configurazione:
    1.  Prima, creare il pool di indirizzi IP front-end:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Pool di indirizzi IP front-end** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, **hana-frontend**).
        1.  Impostare **Assegnazione su** **Statico** e immettere l'indirizzo IP , ad esempio **10.32.0.10**.
        1.  Selezionare **OK**.
        1.  Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.
    1.  Creare quindi un pool back-end:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Pool back-end** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo pool back-end (ad esempio, **hana-backend**).
        1.  Selezionare **Aggiungi una macchina virtuale**.
        1.  Selezionare **Macchina virtuale**.
        1.  Selezionare le macchine virtuali del cluster SAP HANA e il rispettivo indirizzo IP.
        1.  Selezionare **Aggiungi**.
    1.  Creare quindi un probe di integrità:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Probe integrità** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo probe di integrità (ad esempio, **hana-hp**).
        1.  Selezionare TCP come protocollo e la porta 625 **03**. Lasciare il valore di **Intervallo** impostato su 5 e il valore di **Soglia di non integrità** impostato su 2.
        1.  Selezionare **OK**.
    1.  Successivamente, creare le regole del servizio di bilanciamento del carico:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
        1.  Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, **hana-lb**).
        1.  Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**, **hana-backend** e **hana-hp**).
        1.  Selezionare **Porte a disponibilità elevata**.
        1.  Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
        1.  Selezionare **OK**.


9. In alternativa, se lo scenario prevede l'uso del servizio di bilanciamento del carico di base, seguire questa procedura di configurazione:
    1.  Configurare il servizio di bilanciamento del carico. Prima, creare il pool di indirizzi IP front-end:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Pool di indirizzi IP front-end** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, **hana-frontend**).
        1.  Impostare **Assegnazione su** **Statico** e immettere l'indirizzo IP , ad esempio **10.32.0.10**.
        1.  Selezionare **OK**.
        1.  Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.
    1.  Creare quindi un pool back-end:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Pool back-end** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo pool back-end (ad esempio, **hana-backend**).
        1.  Selezionare **Aggiungi una macchina virtuale**.
        1.  Selezionare il set di disponibilità creato nel passaggio 3.
        1.  Selezionare le macchine virtuali del cluster SAP HANA.
        1.  Selezionare **OK**.
    1.  Creare quindi un probe di integrità:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Probe integrità** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo probe di integrità (ad esempio, **hana-hp**).
        1.  Selezionare **TCP** come protocollo e la porta 625 **03**. Lasciare il valore di **Intervallo** impostato su 5 e il valore di **Soglia di non integrità** impostato su 2.
        1.  Selezionare **OK**.
    1.  Per SAP HANA 1.0, creare le regole di bilanciamento del carico:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
        1.  Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3 **03** 15).
        1.  Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**).
        1.  Lasciare il valore di **Protocollo** impostato su **TCP** e immettere la porta 3 **03** 15.
        1.  Aumentare il valore di **Timeout di inattività** a 30 minuti.
        1.  Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
        1.  Selezionare **OK**.
        1.  Ripetere questi passaggi per la porta 3 **03** 17.
    1.  Per SAP HANA 2.0, creare le regole di bilanciamento del carico per il database di sistema:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
        1.  Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3 **03** 13).
        1.  Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**).
        1.  Lasciare il valore di **Protocollo** impostato su **TCP** e immettere la porta 3 **03** 13.
        1.  Aumentare il valore di **Timeout di inattività** a 30 minuti.
        1.  Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
        1.  Selezionare **OK**.
        1.  Ripetere questi passaggi per la porta 3 **03** 14.
    1.  Per SAP HANA 2.0, creare prima le regole di bilanciamento del carico per il database tenant:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
        1.  Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3 **03** 40).
        1.  Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**).
        1.  Lasciare il valore di **Protocollo** impostato su **TCP** e immettere la porta 3 **03** 40.
        1.  Aumentare il valore di **Timeout di inattività** a 30 minuti.
        1.  Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
        1.  Selezionare **OK**.
        1.  Ripetere questi passaggi per le porte 3 **03** 41 e 3 **03** 42.

Per altre informazioni sulle porte necessarie per SAP HANA, leggere il capitolo Connessioni ai database [tenant](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) nella guida ai database [tenant di SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) o nella nota SAP [2388694](https://launchpad.support.sap.com/#/notes/2388694).

> [!IMPORTANT]
> Non abilitare i timestamp TCP nelle macchine virtuali di Azure che si trovano dietro Azure Load Balancer. Se si abilitano i timestamp TCP, i probe di integrità avranno esito negativo. Impostare il parametro **net.ipv4.tcp_timestamps** su **0**. Per informazioni dettagliate, vedere [Probe di integrità di Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md). Vedere anche la nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Montare il volume Azure NetApp Files locale

1. **[A]** Creare punti di montaggio per i volumi di database HANA. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** Verificare l'impostazione del dominio NFS. Assicurarsi che il dominio sia configurato come dominio Azure NetApp Files predefinito, ad esempio defaultv4iddomain.com **e** che il mapping sia impostato su **nessuno.**

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Assicurarsi di impostare il dominio NFS in /etc/idmapd.conf nella macchina virtuale in modo che corrisponda alla configurazione di dominio predefinita in Azure NetApp Files: **defaultv4iddomain.com**. In caso di mancata corrispondenza tra la configurazione del dominio nel client NFS (ad esempio la macchina virtuale) e il server NFS, ad esempio la configurazione di Azure NetApp, le autorizzazioni per i file nei volumi NetApp di Azure montati nelle macchine virtuali verranno visualizzate come nessuno.
    

3. **[1]** Montare i volumi specifici del nodo in node1 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** Montare i volumi specifici del nodo in node2 (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** Verificare che tutti i volumi HANA siano montati con la versione del protocollo NFS NFSv4.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A] Verificare** **nfs4_disable_idmapping**. Deve essere impostato su **Y.** Per creare la struttura di directory **in nfs4_disable_idmapping,** eseguire il comando mount. Non sarà possibile creare manualmente la directory in/sys/modules, perché l'accesso è riservato per il kernel/driver.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Per altre informazioni su come modificare il **nfs_disable_idmapping,** vedere [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>Installazione di SAP HANA

1. **[T]** Configurare la risoluzione dei nomi host per tutti gli host.

   È possibile usare un server DNS o modificare il file /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts. Sostituire l'indirizzo IP e il nome host nei comandi seguenti:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** Configurazione di RHEL per HANA

   Configurare RHEL come descritto nella nota SAP seguente in base alla versione di RHEL

   - [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (2292690 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7)
   - [2777782 - SAP HANA database: impostazioni del sistema operativo consigliate per RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux: Esecuzione di applicazioni SAP compilate con GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux: esecuzione di applicazioni SAP compilate con GCC 7.x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux: esecuzione di applicazioni SAP compilate con GCC 9.x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[T]** Installare SAP HANA

   A iniziare da HANA 2.0 SPS 01, MDC è l'opzione predefinita. Quando si installa il sistema HANA, SYSTEMDB e un tenant con lo stesso SID verranno creati insieme. In alcuni casi non si vuole usare il tenant predefinito. Se non si vuole creare il tenant iniziale insieme all'installazione, è possibile seguire la nota SAP [2629711](https://launchpad.support.sap.com/#/notes/2629711)

    Eseguire il programma **hdblcm** dal DVD di HANA. Immettere i valori seguenti al prompt:  
    Scegliere l'installazione: immettere **1** (per l'installazione)  
    Selezionare i componenti aggiuntivi per l'installazione: Immettere **1**.  
    Immettere il percorso di installazione [/hana/shared]: premere INVIO per accettare il valore predefinito  
    Immettere il nome host locale [..]: premere INVIO per accettare il valore predefinito  
    Aggiungere altri host al sistema? (y/n) [n]: **n**  
    Immettere SAP HANA ID sistema: immettere **HN1.**  
    Immettere il numero di istanza [00]: immettere **03**  
    Selezionare Modalità database/Immettere indice [1]: premere INVIO per accettare l'impostazione predefinita  
    Selezionare Utilizzo sistema/Immettere l'indice [4]: immettere **4** (per personalizzato)  
    Immettere Location of Data Volumes [/hana/data]: premere INVIO per accettare il valore predefinito  
    Immettere Il percorso dei volumi di log [/hana/log]: premere INVIO per accettare il valore predefinito  
    Limitare l'allocazione massima della memoria? [n]: premere INVIO per accettare il valore predefinito  
    Immettere il nome host del certificato per l'host '...' [...]: premere INVIO per accettare il valore predefinito  
    Immettere la password utente dell'agente host SAP (sapadm): immettere la password utente dell'agente host  
    Confermare la password utente dell'agente host SAP (sapadm): immettere di nuovo la password utente dell'agente host per confermare  
    Immettere la password dell'amministratore di sistema (hn1adm): immettere la password dell'amministratore di sistema  
    Confermare la password dell'amministratore di sistema (hn1adm): immettere di nuovo la password dell'amministratore di sistema per confermare  
    Immettere La home directory dell'amministratore di sistema [/usr/sap/HN1/home]: premere INVIO per accettare il valore predefinito  
    Immettere System Administrator Login Shell [/bin/sh]: premere INVIO per accettare il valore predefinito  
    Immettere l'ID utente amministratore di sistema [1001]: premere INVIO per accettare l'impostazione predefinita  
    Immettere l'ID del gruppo di utenti (sapsys) [79]: premere INVIO per accettare il valore predefinito  
    Immettere la password dell'utente del database (SYSTEM): immettere la password utente del database  
    Conferma password utente database (SYSTEM): immettere di nuovo la password utente del database per confermare  
    Riavviare il sistema dopo il riavvio della macchina? [n]: premere INVIO per accettare il valore predefinito  
    Continuare? (y/n): Convalidare il riepilogo. Immettere **y** per continuare  

4. **[A]** Aggiornare l'agente host SAP

   Scaricare l'archivio dell'agente host SAP più recente dal sito [SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter) ed eseguire il comando seguente per aggiornare l'agente. Sostituire il percorso dell'archivio in modo da puntare al file scaricato:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[T]** Configurare il firewall

   Creare la regola del firewall per la porta probe di Azure Load Balancer.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Configure SAP HANA system replication (Configurare la replica di sistema SAP HANA)

Seguire la procedura descritta in Configurare la SAP HANA [di sistema per](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) configurare la SAP HANA di sistema. 

## <a name="cluster-configuration"></a>Configurazione del cluster

Questa sezione descrive i passaggi necessari per il funzionamento ottimale del cluster quando SAP HANA viene installato in condivisioni NFS usando Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire la procedura descritta in [Configurazione di Pacemaker in Red Hat Enterprise Linux](./high-availability-guide-rhel-pacemaker.md) in Azure per creare un cluster Pacemaker di base per questo server HANA.

### <a name="implement-the-python-system-replication-hook-saphanasr"></a>Implementare l'hook di replica di sistema Python SAPHanaSR

Questo è un passaggio importante per ottimizzare l'integrazione con il cluster e migliorare il rilevamento quando è necessario un failover del cluster. È consigliabile configurare l'hook Python SAPHanaSR.    

1. **[A] Installare** l'hook di replica di sistema HANA. L'hook deve essere installato in entrambi i nodi del database HANA.           

   > [!TIP]
   > L'hook Python può essere implementato solo per HANA 2.0.        

   1. Preparare l'hook come `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Arrestare HANA in entrambi i nodi. Eseguire come <sid \> adm:  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Modificare `global.ini` in ogni nodo del cluster.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** Il cluster richiede la configurazione sudoers in ogni nodo del cluster <sid \> adm. In questo esempio si ottiene creando un nuovo file. Eseguire i comandi come `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```

3. **[A]** Avviare SAP HANA in entrambi i nodi. Eseguire come <sid \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1] Verificare** l'installazione dell'hook. Eseguire come <sid \> adm nel sito di replica di sistema HANA attivo.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

Per altri dettagli sull'implementazione dell'hook SAP HANA di replica di sistema, vedere [Abilitare l'hook del provider SAP HA/DR.](https://access.redhat.com/articles/3004101#enable-srhook)  

### <a name="configure-filesystem-resources"></a>Configurare le risorse del file system

In questo esempio ogni nodo del cluster ha i propri file system NFS di HANA /hana/shared, /hana/data e /hana/log.   

1. **[1]** Mettere il cluster in modalità di manutenzione.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** Creare le risorse del file system per i **montamenti hanadb1.**

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** Creare le risorse del file system per i **montamenti hanadb2.**

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` L'attributo viene aggiunto all'operazione di monitoraggio in modo che ogni monitoraggio esegua un test di lettura/scrittura nel file system. Senza questo attributo, l'operazione di monitoraggio verifica solo che il file system sia montato. Questo può essere un problema perché quando la connettività viene persa, il file system può rimanere montato anche se non è accessibile.

    `on-fail=fence` l'attributo viene aggiunto anche all'operazione di monitoraggio. Con questa opzione, se l'operazione di monitoraggio ha esito negativo su un nodo, tale nodo viene immediatamente recintato. Senza questa opzione, il comportamento predefinito è arrestare tutte le risorse che dipendono dalla risorsa non riuscita, quindi riavviare la risorsa non riuscita, quindi avviare tutte le risorse che dipendono dalla risorsa non riuscita. Non solo questo comportamento può richiedere molto tempo quando una risorsa SAPHana dipende dalla risorsa non riuscita, ma può anche avere esito negativo. La risorsa SAPHana non può essere interrotta correttamente se il server NFS che contiene i file eseguibili HANA non è accessibile.

4. **[1]** Configurazione dei vincoli di posizione

   Configurare i vincoli di percorso per garantire che le risorse che gestiscono montamenti univoci hanadb1 non possano mai essere eseguite in hanadb2 e viceversa.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    `resource-discovery=never`L'opzione è impostata perché i montamenti univoci per ogni nodo condividono lo stesso punto di montaggio. Ad esempio, usa `hana_data1` il punto di montaggio e usa `/hana/data` anche il punto di `hana_data2` montaggio `/hana/data` . Ciò può causare un falso positivo per un'operazione di probe, quando lo stato della risorsa viene controllato all'avvio del cluster e ciò può a sua volta causare un comportamento di ripristino non necessario. Questa operazione può essere evitata impostando `resource-discovery=never`

5. **[1]** Configurazione delle risorse degli attributi

   Configurare le risorse degli attributi. Questi attributi verranno impostati su true se tutti i montamenti NFS di un nodo (/hana/data, /hana/log e /hana/data) vengono montati e verranno impostati su false in caso contrario.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1] Configurazione** dei vincoli di posizione

   Configurare i vincoli di posizione per assicurarsi che la risorsa attributo di hanadb1 non venga mai eseguita in hanadb2 e viceversa.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1] Creazione** di vincoli di ordinamento

   Configurare i vincoli di ordinamento in modo che le risorse degli attributi di un nodo inizino solo dopo il montaggio di tutti i montamenti NFS del nodo.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Se la configurazione include file system, all'esterno del gruppo o , includere l'opzione , in modo che non siano presenti dipendenze `hanadb1_nfs` `hanadb2_nfs` di `sequential=false` ordinamento tra i file system. Tutti i file system devono essere avviati prima di , ma non devono essere avviati `hana_nfs1_active` in alcun ordine l'uno rispetto all'altro. Per altre informazioni, Ricerca per categorie configurare SAP HANA replica di sistema in Scale-Up in un cluster Pacemaker quando i file system HANA si verificano in [condivisioni NFS](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>Configurare le SAP HANA cluster

1. Seguire la procedura descritta in [Creare SAP HANA risorse del cluster](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) per creare SAP HANA risorse nel cluster. Dopo SAP HANA risorse, è necessario creare un vincolo di regola di posizione tra SAP HANA risorse e file system (montamenti NFS)

2. **[1] Configurare** i vincoli tra le SAP HANA e i montamenti NFS

   I vincoli delle regole di posizione verranno impostati in modo che le risorse SAP HANA possano essere eseguite in un nodo solo se sono montati tutti i montamenti NFS del nodo.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Controllare lo stato del cluster e tutte le risorse
   > [!NOTE]
   > Questo articolo contiene riferimenti al termine *slave,* un termine che Microsoft non usa più. Quando il termine verrà rimosso dal software, verrà rimosso anche dall'articolo.
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Configurare la replica di sistema attiva/in lettura di HANA nel cluster Pacemaker

A partire da SAP HANA 2.0 SPS 01 SAP consente configurazioni attive/abilitate per la lettura per la replica di sistema SAP HANA, in cui i sistemi secondari di replica di sistema SAP HANA possono essere usati attivamente per carichi di lavoro con intensa attività di lettura. Per supportare tale configurazione in un cluster, è necessario un secondo indirizzo IP virtuale che consenta ai client di accedere al database secondario abilitato SAP HANA lettura. Per assicurarsi che il sito di replica secondario sia ancora accessibile dopo un'acquisizione, il cluster deve spostare l'indirizzo IP virtuale con il sito secondario della risorsa SAPHana.

La configurazione aggiuntiva necessaria per gestire la replica di sistema abilitata per HANA Active/Read in un cluster a disponibilità elevata Red Hat con secondo IP virtuale è descritta in Configurare la replica di sistema attiva/abilitata per la lettura di [HANA nel cluster Pacemaker.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster)  

Prima di procedere, assicurarsi di aver configurato completamente il cluster Red Hat a disponibilità elevata SAP HANA database, come descritto nei segmenti precedenti della documentazione.    


## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

Questa sezione descrive come testare la configurazione. 

1. Prima di avviare un test, assicurarsi che Pacemaker non abbia alcuna azione non riuscita (tramite lo stato pcs), che non siano presenti vincoli di posizione imprevisti (ad esempio gli elementi rimanenti di un test di migrazione) e che la replica di sistema HANA sia lo stato di sincronizzazione, ad esempio con systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Verificare la configurazione del cluster per uno scenario di errore quando un nodo perde l'accesso alla condivisione NFS (/hana/shared)

   Gli SAP HANA di risorse dipendono dai file binari, archiviati in per `/hana/shared` eseguire operazioni durante il failover. Il file system  `/hana/shared` viene montato su NFS nello scenario presentato.  
   È difficile simulare un errore, in cui uno dei server perde l'accesso alla condivisione NFS. Un test che può essere eseguito è quello di montare nuovamente il file system come di sola lettura.
   Questo approccio convalida che il cluster sarà in grado di eseguire il failover, se l'accesso a `/hana/shared` viene perso nel nodo attivo.     


   **Risultato previsto:** In caso di creazione di un file system di sola lettura, l'attributo della risorsa che esegue operazioni di lettura/scrittura su file system avrà esito negativo perché non è in grado di scrivere alcun elemento nel file system ed eseguirà il failover delle risorse `/hana/shared` `OCF_CHECK_LEVEL` `hana_shared1` HANA.  Lo stesso risultato è previsto quando il nodo HANA perde l'accesso alle condivisioni NFS. 

   Stato delle risorse prima dell'avvio del test:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   È possibile impostare /hana/shared in modalità di sola lettura nel nodo del cluster attivo usando il comando seguente:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 riavvierà o poweroff in base all'azione impostata in stonith ( `pcs property show stonith-action` ).  Quando il server (hanadb1) non è disponibile, la risorsa HANA viene spostata in hanadb2. È possibile controllare lo stato del cluster da hanadb2.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   È consigliabile testare accuratamente la configurazione del cluster SAP HANA, eseguendo anche i test descritti in Configurare SAP HANA replica di [sistema in RHEL.](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* [Volumi NFS v4.1 in Azure NetApp Files per SAP HANA](./hana-vm-operations-netapp.md)