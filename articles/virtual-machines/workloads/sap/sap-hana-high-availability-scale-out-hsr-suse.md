---
title: SAP HANA la scalabilità orizzontale con HSR e pacemaker in SLES | Microsoft Docs
description: SAP HANA la scalabilità orizzontale con HSR e pacemaker su SLES.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 637616c3698cc9ec0cd13a4584bad24b6ed02c34
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315496"
---
# <a name="high-availability-for-sap-hana-scale-out-system-with-hsr-on-suse-linux-enterprise-server"></a>Disponibilità elevata per SAP HANA sistema con scalabilità orizzontale con HSR in SUSE Linux Enterprise Server 

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Questo articolo descrive come distribuire un sistema di SAP HANA a disponibilità elevata in una configurazione con scalabilità orizzontale con HANA System Replication (HSR) e pacemaker in Azure SUSE Linux Enterprise Server macchine virtuali (VM). I file system condivisi nell'architettura presentata sono forniti da [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) e sono montati su NFS.  

Nelle configurazioni di esempio, nei comandi di installazione e così via, l'istanza di HANA è **03** e l'ID di sistema Hana è **HN1**. Gli esempi sono basati su HANA 2,0 SP4 e SUSE Linux Enterprise Server 12 SP5. 

Prima di iniziare, vedere le note e i documenti SAP seguenti:

* [Documentazione di Azure NetApp Files][anf-azure-doc] 
* La nota SAP [1928533] include:  
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione del software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * La versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure
* Nota SAP [2015553]: elenca i prerequisiti per le distribuzioni del software SAP supportate da SAP in Azure
* Nota SAP [2205917]: contiene le impostazioni del sistema operativo consigliate per SUSE Linux Enterprise Server per le applicazioni SAP
* Nota SAP [1944799]: contiene le linee guida sap per SUSE Linux Enterprise Server per le applicazioni SAP
* Nota SAP [2178632]: contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure
* Nota SAP [2191498]: contiene la versione richiesta dell'agente host SAP per Linux in Azure
* Nota SAP [2243692]: contiene informazioni sulle licenze SAP in Linux in Azure
* Nota SAP [1984787]: contiene informazioni generali sulla SUSE Linux Enterprise Server 12
* Nota SAP [1999351]: contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP
* Nota SAP [1900823]: contiene informazioni sui requisiti di archiviazione SAP Hana
* [Wiki della community SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): contiene tutte le note SAP necessarie per Linux
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [Guide alle procedure consigliate di SUSE SAP][suse-ha-guide]: contiene tutte le informazioni necessarie per configurare la disponibilità elevata NetWeaver e la replica di SAP Hana sistema locale (da usare come base di riferimento generale; forniscono informazioni molto più dettagliate)
* [Note sulla versione di SUSE High Availability Extension 12 SP5](https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP5/)
* [Gestione della condivisione NFS non riuscita nel cluster SUSE a disponibilità elevata per la replica di sistema HANA](https://www.suse.com/support/kb/doc/?id=000019904)
* [Applicazioni NetApp SAP su Microsoft Azure con Azure NetApp Files][anf-sap-applications-azure]
* [Volumi NFS v4.1 in Azure NetApp Files per SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Panoramica

Un metodo per ottenere la disponibilità elevata HANA per le installazioni con scalabilità orizzontale di HANA consiste nel configurare la replica di sistema HANA e proteggere la soluzione con cluster pacemaker per consentire il failover automatico. Quando si verifica un errore in un nodo attivo, il cluster esegue il failover delle risorse HANA sull'altro sito.  
La configurazione presentata mostra tre nodi HANA in ogni sito, oltre al nodo del creatore di maggioranza per prevenire lo scenario Split Brain. Le istruzioni possono essere adattate per includere più macchine virtuali come nodi del database HANA.  

La file system condivisa HANA `/hana/shared` nell'architettura presentata viene fornita da [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md). Viene montata tramite NFSv 4.1 in ogni nodo HANA nello stesso sito di replica di sistema HANA. I file System `/hana/data` e `/hana/log` sono file system locali e non sono condivisi tra i nodi del database Hana. SAP HANA verrà installato in modalità non condivisa. 

> [!TIP]
> Per le configurazioni di archiviazione SAP HANA consigliate, vedere [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](./hana-vm-operations-storage.md).   

[![SAP HANA la scalabilità orizzontale con cluster HSR e pacemaker su SLES](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse.png)](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse-detail.png#lightbox)

Nel diagramma precedente, tre subnet sono rappresentate all'interno di una rete virtuale di Azure, seguendo le raccomandazioni della rete SAP HANA: 
* per la comunicazione client- `client` 10.23.0.0/24  
* per la comunicazione tra nodi HANA interna- `inter` 10.23.1.128/26  
* per la replica di sistema HANA- `hsr` 10.23.1.192/26  

Quando `/hana/data` e `/hana/log` vengono distribuiti nei dischi locali, non è necessario distribuire una subnet separata e schede di rete virtuali separate per la comunicazione con l'archiviazione.  

I volumi di Azure NetApp vengono distribuiti in una subnet separata, [delegata a Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet): `anf` 10.23.1.0/26.   

> [!IMPORTANT]
> La replica di sistema in un terzo sito non è supportata. Per informazioni dettagliate, vedere la sezione "prerequisiti importanti" nello [scenario ottimizzato per le prestazioni della replica di sistema SLES-SAP Hana](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_important_prerequisites).     

## <a name="set-up-the-infrastructure"></a>Configurare l'infrastruttura

Nelle istruzioni seguenti si presuppone che sia già stato creato il gruppo di risorse, la rete virtuale di Azure con tre subnet di rete di Azure: `client` `inter` e `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Distribuire macchine virtuali Linux tramite il portale di Azure
1. Distribuire le macchine virtuali di Azure.  
Per la configurazione presentata in questo documento, distribuire sette macchine virtuali: 
   - tre macchine virtuali da usare come nodi del database HANA per il sito di replica HANA 1: **Hana-S1-DB1**, **Hana-S1-DB2** e **Hana-S1-DB3**  
   - tre macchine virtuali da usare come nodi del database HANA per la replica HANA sito 2: **Hana-S2-DB1**, **Hana-S2-DB2** e **Hana-S2-DB3**  
   - una macchina virtuale di piccole dimensioni che funge da *creatore della maggioranza*: **Hana-s-mm**

   Le macchine virtuali, distribuite come nodi di SAP DB HANA, devono essere certificate da SAP per HANA come pubblicate nella [directory dell'Hardware SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Quando si distribuiscono i nodi del database HANA, assicurarsi che sia selezionata l'opzione [rete accelerata](../../../virtual-network/create-vm-accelerated-networking-cli.md) .  
  
   Per il nodo creatore della maggioranza, è possibile distribuire una macchina virtuale di piccole dimensioni perché questa macchina virtuale non esegue alcuna delle risorse SAP HANA. La VM di maggioranza viene usata nella configurazione del cluster per ottenere un numero dispari di nodi del cluster in uno scenario Split Brain. In questo esempio la macchina virtuale di maggioranza Maker necessita solo di un'interfaccia di rete virtuale nella `client` subnet.        

   Distribuire dischi gestiti locali per `/hana/data` e `/hana/log` . La configurazione di archiviazione minima consigliata per `/hana/data` ed `/hana/log` è descritta in [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](./hana-vm-operations-storage.md).

   Distribuire l'interfaccia di rete primaria per ogni macchina virtuale nella `client` subnet della rete virtuale.  
   Quando la macchina virtuale viene distribuita tramite portale di Azure, viene generato automaticamente il nome dell'interfaccia di rete. In queste istruzioni per semplicità si farà riferimento alle interfacce di rete primarie generate automaticamente, che sono collegate alla `client` subnet della rete virtuale di Azure come **Hana-S1-DB1-client**, **Hana-S1-DB2-client**, **Hana-S1-DB3-client** e così via.  


   > [!IMPORTANT]
   > Verificare che il sistema operativo selezionato sia certificato per SAP per SAP HANA sui tipi di VM specifici in uso. Per un elenco dei tipi di VM SAP HANA certificati e delle versioni del sistema operativo per questi tipi, accedere al sito [SAP Hana piattaforme IaaS certificate](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Fare clic sui dettagli del tipo di macchina virtuale elencato per ottenere l'elenco completo delle versioni del sistema operativo supportate da SAP HANA per quel tipo.  
  

2. Creare sei interfacce di rete, una per ogni macchina virtuale del database HANA, nella `inter` subnet della rete virtuale (in questo esempio, **Hana-S1-DB1-Inter**, **Hana-S1-DB2-Inter**, **Hana-S1-DB3-Inter**, **Hana-S2-DB1-Inter**, **Hana-S2-DB2-Inter** e **Hana-S2-DB3-Inter**).  

3. Creare sei interfacce di rete, una per ogni macchina virtuale del database HANA, nella `hsr` subnet della rete virtuale (in questo esempio, **Hana-S1-DB1-HSR**, **Hana-S1-DB2-HSR**, **Hana-S1-DB3-HSR**, **Hana-S2-DB1-HSR**, **Hana-S2-DB2-HSR** e **Hana-S2-DB3-HSR**).  

4. Alleghi le interfacce di rete virtuale appena create alle macchine virtuali corrispondenti:  

    a. Passare alla macchina virtuale nella [portale di Azure](https://portal.azure.com/#home).  

    b. Nel riquadro sinistro selezionare **macchine virtuali**. Filtrare sul nome della macchina virtuale (ad esempio, **Hana-S1-DB1**), quindi selezionare la macchina virtuale.  

    c. Nel riquadro **Panoramica** selezionare **Arresta** per deallocare la macchina virtuale.  

    d. Selezionare **rete** e quindi collegare l'interfaccia di rete. Nell'elenco a discesa **Connetti interfaccia di rete** selezionare le interfacce di rete già create per le `inter` subnet e `hsr` .  
    
    e. Selezionare **Salva**. 
 
    f. Ripetere i passaggi da b a e per le macchine virtuali rimanenti (in questo esempio,  **Hana-S1-DB2**, **Hana-S1-DB3**, **Hana-S2-DB1**, **Hana-S2-DB2** e **Hana-S2-DB3**).
 
    g. Per il momento, lasciare le macchine virtuali in stato di arresto. Successivamente, verrà abilitata la [rete accelerata](../../../virtual-network/create-vm-accelerated-networking-cli.md) per tutte le interfacce di rete appena collegate.  

5. Abilitare la rete accelerata per le interfacce di rete aggiuntive per le `inter` subnet e seguendo `hsr` questa procedura:  

    a. Aprire [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) nel [portale di Azure](https://portal.azure.com/#home).  

    b. Eseguire i comandi seguenti per abilitare la rete accelerata per le interfacce di rete aggiuntive, che sono collegate `inter` alle `hsr` subnet e.  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. Avviare le macchine virtuali del database HANA

### <a name="deploy-azure-load-balancer"></a>Distribuisci Azure Load Balancer

1. È consigliabile usare Load Balancer standard. Per distribuire Load Balancer standard, seguire questa procedura di configurazione:
   1. Prima, creare il pool di indirizzi IP front-end:

      1. Aprire il servizio di bilanciamento del carico, selezionare **Pool di indirizzi IP front-end** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, **hana-frontend**).
      1. Impostare l' **assegnazione** su **statico** e immettere l'indirizzo IP (ad esempio, **10.23.0.27**).
      1. Selezionare **OK**.
      1. Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.

   1. Successivamente, creare un pool back-end e aggiungere tutte le macchine virtuali del cluster al pool back-end:

      1. Aprire il servizio di bilanciamento del carico, selezionare **Pool back-end** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo pool back-end (ad esempio, **hana-backend**).
      1. Selezionare **Aggiungi una macchina virtuale**.
      1. Selezionare **Macchina virtuale**.
      1. Selezionare le macchine virtuali del cluster SAP HANA e i relativi indirizzi IP per la `client` subnet.
      1. Selezionare **Aggiungi**.

   1. Creare quindi un probe di integrità:

      1. Aprire il servizio di bilanciamento del carico, selezionare **Probe integrità** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo probe di integrità (ad esempio, **hana-hp**).
      1. Selezionare **TCP** come protocollo e la porta 625 **03**. Lasciare il valore di **Intervallo** impostato su 5 e il valore di **Soglia di non integrità** impostato su 2.
      1. Selezionare **OK**.

   1. Successivamente, creare le regole del servizio di bilanciamento del carico:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
      1. Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, **hana-lb**).
      1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**, **hana-backend** e **hana-hp**).
      1. Selezionare **Porte a disponibilità elevata**.
      1. Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
      1. Selezionare **OK**.

   > [!IMPORTANT]
   > L'IP mobile non è supportato in una configurazione IP secondaria di NIC negli scenari di bilanciamento del carico. Per informazioni dettagliate, vedere limitazioni del servizio di [bilanciamento del carico di Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se è necessario un indirizzo IP aggiuntivo per la macchina virtuale, distribuire una seconda scheda di interfaccia di rete.    
   
   > [!Note]
   > Se vengono inserite macchine virtuali senza indirizzi IP pubblici nel pool back-end di Load Balancer Standard interno ad Azure (nessun indirizzo IP pubblico), non sarà presente alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


   > [!IMPORTANT]
   > Non abilitare i timestamp TCP nelle macchine virtuali di Azure che si trovano dietro Azure Load Balancer. Se si abilitano i timestamp TCP, i probe di integrità avranno esito negativo. Impostare il parametro **net.ipv4.tcp_timestamps** su **0**. Per informazioni dettagliate, vedere [Probe di integrità di Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Vedere anche la nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Distribuire l'infrastruttura Azure NetApp Files 

Distribuire i volumi e per la `/hana/shared` file System. Sarà necessario un volume separato `/hana/shared` per ogni sito di replica di sistema Hana. Per altre informazioni, vedere [configurare l'infrastruttura Azure NetApp files](./sap-hana-scale-out-standby-netapp-files-suse.md#set-up-the-azure-netapp-files-infrastructure).

In questo esempio sono stati usati i volumi Azure NetApp Files seguenti: 

* volume **HN1**-Shared-s1 (NFS://10.23.1.7/**HN1**-Shared-S1)
* volume **HN1**-Shared-s2 (NFS://10.23.1.7/**HN1**-Shared-S2)


## <a name="operating-system-configuration-and-preparation"></a>Configurazione e preparazione del sistema operativo

Le istruzioni riportate nelle sezioni successive sono precedute da una delle abbreviazioni seguenti:
* **[A]**: applicabile a tutti i nodi
* **[Ah]**: applicabile a tutti i nodi del database Hana
* **[M]**: applicabile al nodo del produttore della maggioranza
* **[AH1]**: applicabile a tutti i nodi del database Hana nel sito 1
* **[AH2]**: applicabile a tutti i nodi del database Hana nel sito 2
* **[1]**: applicabile solo al nodo 1 del database Hana, sito 1
* **[2]**: applicabile solo al nodo 1 del database Hana, sito 2

Configurare e preparare il sistema operativo seguendo questa procedura:

1. **[A]** gestire i file host nelle macchine virtuali. Includere le voci per tutte le subnet. Per questo esempio sono state aggiunte le voci seguenti `/etc/hosts` .  

    ```bash
     # Client subnet
     10.23.0.19      hana-s1-db1
     10.23.0.20      hana-s1-db2
     10.23.0.21      hana-s1-db3
     10.23.0.22      hana-s2-db1
     10.23.0.23      hana-s2-db2
     10.23.0.24      hana-s2-db3
     10.23.0.25      hana-s-mm    
     # Internode subnet
     10.23.1.132     hana-s1-db1-inter
     10.23.1.133     hana-s1-db2-inter
     10.23.1.134     hana-s1-db3-inter
     10.23.1.135     hana-s2-db1-inter
     10.23.1.136     hana-s2-db2-inter
     10.23.1.137     hana-s2-db3-inter
     # HSR subnet
     10.23.1.196     hana-s1-db1-hsr
     10.23.1.197     hana-s1-db2-hsr
     10.23.1.198     hana-s1-db3-hsr
     10.23.1.199     hana-s2-db1-hsr
     10.23.1.200     hana-s2-db2-hsr
     10.23.1.201     hana-s2-db3-hsr
    ```

2. **[A]** SUSE offre agenti di risorse speciali per SAP HANA e per impostazione predefinita vengono installati gli agenti per SAP Hana ScaleUp. Disinstallare i pacchetti per ScaleUp, se installati e installare i pacchetti per lo scenario SAP HANAScaleOut. Il passaggio deve essere eseguito su tutte le macchine virtuali del cluster, incluso il creatore di maggioranza.   

    ```bash
     # Uninstall ScaleUp packages and patterns
     zypper remove patterns-sap-hana
     zypper remove SAPHanaSR 
     zypper remove SAPHanaSR-doc
     zypper remove yast2-sap-ha
     # Install the ScaleOut packages and patterns
     zypper in SAPHanaSR-ScaleOut  SAPHanaSR-ScaleOut-doc 
     zypper in -t pattern ha_sles
    ```

3. **[Ah]** Preparare le macchine virtuali: applicare le impostazioni consigliate per la nota SAP [2205917] per SUSE Linux Enterprise Server per le applicazioni SAP.  

## <a name="prepare-the-file-systems"></a>Preparare il file System
### <a name="mount-the-shared-file-systems"></a>Montare i file system condivisi

In questo esempio, i file System HANA condivisi vengono distribuiti in Azure NetApp Files e montati tramite NFSv4.  

1. **[Ah]** Creare punti di montaggio per i volumi del database HANA.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[Ah]** Verificare l'impostazione del dominio NFS. Verificare che il dominio sia configurato come predefinito Azure NetApp Files dominio, ovvero che **`defaultv4iddomain.com`** il mapping sia impostato su **nessuno**.  
   Questo passaggio è necessario solo se si usa Azure NetAppFiles NFSv 4.1.  

    > [!IMPORTANT]
    > Verificare di impostare il dominio NFS in `/etc/idmapd.conf` sulla macchina virtuale in modo che corrisponda alla configurazione del dominio predefinito in Azure NetApp Files: **`defaultv4iddomain.com`** . In caso di mancata corrispondenza tra la configurazione del dominio nel client NFS (ovvero la macchina virtuale) e nel server NFS, ad esempio la configurazione di Azure NetApp, le autorizzazioni per i file nei volumi Azure NetApp montati nelle VM verranno visualizzate come `nobody`.  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[Ah]** Verificare `nfs4_disable_idmapping` . Il valore deve essere impostato su **Y**. Per creare la struttura di directory in cui si trova `nfs4_disable_idmapping`, eseguire il comando mount. Non sarà possibile creare manualmente la directory in/sys/modules, perché l'accesso è riservato per il kernel/driver.  
   Questo passaggio è necessario solo se si usa Azure NetAppFiles NFSv 4.1.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.7:/HN1-share-s1 /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

4. **[AH1]** Montare i volumi di Azure NetApp Files condivisi nelle VM di database Microsoft1 HANA.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s1 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    ```

5. **[AH2]** Montare i volumi di Azure NetApp Files condivisi nelle VM di database SITE2 HANA.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s2 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    ```


10. **[Ah]** Verificare che i `/hana/shared/` file system corrispondenti siano montati in tutte le VM del database Hana con il protocollo NFS versione **NFSv4**.  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Preparare i dati e registrare i file system locali
Nella configurazione presentata, i file System `/hana/data` e `/hana/log` vengono distribuiti in un disco gestito e sono collegati localmente a ogni macchina virtuale del database Hana. È necessario eseguire i passaggi per creare i volumi di dati e di log locali in ogni macchina virtuale HANA DB. 

Configurare il layout del disco con  **Logical Volume Manager (LVM)**. Nell'esempio seguente si presuppone che a ogni macchina virtuale HANA siano collegati tre dischi dati, usati per creare due volumi.

1. **[Ah]** Elencare tutti i dischi disponibili:
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Output di esempio:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[Ah]** Creare volumi fisici per tutti i dischi che si vuole usare:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[Ah]** Creare un gruppo di volumi per i file di dati. Usare un gruppo di volumi per i file di log e uno per la directory condivisa di SAP HANA:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[Ah]** Creare i volumi logici. 
   Quando si usa `lvcreate` senza l'opzione `-i`, viene creato un volume lineare. Si consiglia di creare un volume con striping per migliorare le prestazioni di I/O e allineare le dimensioni di striping ai valori documentati in [Configurazioni di archiviazione della macchina virtuale SAP HANA](./hana-vm-operations-storage.md). L'argomento `-i` deve essere il numero dei volumi fisici sottostanti e l'argomento `-I` è la dimensione della striscia. In questo documento vengono usati due volumi fisici per il volume di dati, quindi l'argomento dell'opzione `-i` è impostato su **2**. La dimensione di striping per il volume di dati è **256 KiB**. Un volume fisico viene usato per il volume di log, quindi non viene usata alcuna opzione `-i` o `-I` in modo esplicito per i comandi del volume di log.  

   > [!IMPORTANT]
   > Usare l' `-i` opzione e impostarla sul numero del volume fisico sottostante quando si usa più di un volume fisico per ogni volume di dati o di log. Usare l'opzione `-I` per specificare le dimensioni di striping durante la creazione di un volume con striping.  
   > Vedere [Configurazioni di archiviazione della macchina virtuale SAP HANA](./hana-vm-operations-storage.md) per le configurazioni di archiviazione consigliate, incluse le dimensioni di striping e il numero di dischi.  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[Ah]** Creare le directory di montaggio e copiare l'UUID di tutti i volumi logici:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[Ah]** Creare `fstab` voci per i volumi logici e montare:
    ```bash
    sudo vi /etc/fstab
    ```

   Inserire la riga seguente nel file `/etc/fstab`:

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Montare i nuovi volumi:

    ```bash
    sudo mount -a
    ```

## <a name="create-a-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire i passaggi descritti in [Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) (Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure) per creare un cluster Pacemaker di base per questo server HANA.
Includere tutte le macchine virtuali, incluso il creatore della maggioranza nel cluster.  

> [!IMPORTANT]
> Non impostare `quorum expected-votes` su 2, in quanto non si tratta di un cluster a due nodi.  
> Verificare che la proprietà del cluster `concurrent-fencing`  sia abilitata, in modo che la schermatura del nodo venga deserializzata.   

## <a name="installation"></a>Installazione  

In questo esempio per la distribuzione di SAP HANA nella configurazione con scalabilità orizzontale con HSR in macchine virtuali di Azure, è stato usato HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparare l'installazione di HANA

1. **[Ah]** Prima dell'installazione di HANA, impostare la password radice. È possibile disabilitare la password radice dopo che l'installazione è stata completata. Comando Execute `root` As `passwd` .  

2. **[1, 2]** modificare le autorizzazioni in `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** verificare che sia possibile accedere tramite SSH alle VM del database Hana in questo sito **Hana-S1-DB2** e **Hana-S1-DB3**, senza che venga richiesta una password. In caso contrario, scambiare le chiavi SSH come descritto in [abilitare l'accesso SSH tramite chiave pubblica](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_enable_ssh_access_via_public_key_optional).  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** verificare che sia possibile accedere tramite SSH alle VM del database Hana in questo sito **Hana-S2-DB2** e **Hana-S2-DB3**, senza che venga richiesta una password.  
   In caso contrario, scambiare le chiavi SSH.    
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Installare pacchetti aggiuntivi, necessari per HANA 2,0 SP4. Per altre informazioni, vedere la nota SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824) per la versione SLES. 

    ```bash
    # In this example, using SLES12 SP5
    sudo zypper install libgcc_s1 libstdc++6 libatomic1
    ```
### <a name="hana-installation-on-the-first-node-on-each-site"></a>Installazione di HANA nel primo nodo in ogni sito

1. **[1]** installare SAP Hana seguendo le istruzioni riportate nella [Guida all'installazione e all'aggiornamento di SAP Hana 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Nelle istruzioni seguenti viene illustrata l'installazione di SAP HANA nel primo nodo nel sito 1.   

   a. Avviare il programma **hdblcm** come `root` dalla directory del software di installazione di Hana. Usare il `internal_network` parametro e passare lo spazio degli indirizzi per la subnet, che viene usato per la comunicazione tra nodi Hana interna.  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. Al prompt dei comandi, immettere i valori seguenti:

     * Per **scegliere un'azione**: immettere **1** (per l'installazione)
     * Per **ulteriori componenti per l'installazione**: immettere **2, 3**
     * Per percorso di installazione: premere INVIO (il valore predefinito è/Hana/Shared)
     * Per **nome host locale**: premere INVIO per accettare il valore predefinito
     * Per **aggiungere host al sistema?**: immettere **n**
     * Per **SAP Hana ID sistema**: immettere **HN1**
     * Per **numero di istanza** [00]: immettere **03**
     * Per il **gruppo di lavoro host locale** [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Per **Seleziona utilizzo sistema/immettere l'indice [4]**: immettere **4** (per personalizzato)
     * Per il **percorso dei volumi di dati** [/Hana/data/HN1]: premere INVIO per accettare il valore predefinito
     * Per il **percorso dei volumi di log** [/Hana/log/HN1]: premere INVIO per accettare il valore predefinito
     * Per **limitare l'allocazione di memoria massima?** [n]: immettere **n**
     * Per **nome host del certificato per l'host Hana-S1-DB1** [Hana-S1-DB1]: premere INVIO per accettare il valore predefinito
     * Per la **password dell'utente agente host SAP (sapadm)**: immettere la password
     * Per **confermare la password dell'utente agente host SAP (sapadm)**: immettere la password
     * Per la **password dell'amministratore di sistema (hn1adm)**: immettere la password
     * Per la **Home directory dell'amministratore di sistema** [/usr/SAP/HN1/Home]: premere INVIO per accettare il valore predefinito
     * Per la **Shell di accesso dell'amministratore di sistema** [/bin/sh]: premere INVIO per accettare il valore predefinito
     * Per l' **ID utente dell'amministratore di sistema** [1001]: premere INVIO per accettare il valore predefinito
     * Per **immettere l'ID del gruppo utenti (sapsys)** [79]: premere INVIO per accettare il valore predefinito
     * Per la **password utente del database di sistema (System)**: immettere la password del sistema
     * Per **Conferma password utente database di sistema (sistema)**: immettere la password del sistema
     * Per **riavviare il sistema dopo il riavvio del computer?** [n]: immettere **n** 
     * Per **continuare (y/n)**: convalidare il riepilogo e, in caso di esito positivo, immettere **y**

2. **[2]** ripetere il passaggio precedente per installare SAP Hana nel primo nodo del sito 2.   

3. **[1, 2]** verifica global.ini  

   Visualizzare global.ini e assicurarsi che sia attiva la configurazione per la comunicazione interna SAP HANA tra nodi. Verificare la sezione **comunicazione** . Deve avere lo spazio degli indirizzi per la `inter` subnet e `listeninterface` deve essere impostato su `.internal` . Verificare la sezione **internal_hostname_resolution** . Deve avere gli indirizzi IP per le macchine virtuali HANA che appartengono alla `inter` subnet.  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.132 = hana-s1-db1
     10.23.1.133 = hana-s1-db2
     10.23.1.134 = hana-s1-db3
   ```

4. **[1, 2]** preparare l' `global.ini` installazione in un ambiente non condiviso, come descritto nella nota SAP [2080991](https://launchpad.support.sap.com/#/notes/0002080991).  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** riavviare SAP HANA per attivare le modifiche.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** verificare che l'interfaccia client utilizzerà gli indirizzi IP dalla `client` subnet per la comunicazione.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.22"
   ```

   Per informazioni su come verificare la configurazione, vedere la nota SAP [2183363-configurazione di SAP Hana rete interna](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[Ah]** Modificare le autorizzazioni per le directory di dati e log per evitare errori di installazione di HANA.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** installare i nodi Hana secondari. Le istruzioni di esempio in questo passaggio sono per il sito 1.  
   a. Avviare il programma **hdblcm** residente come `root` .    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. Al prompt dei comandi, immettere i valori seguenti:

     * Per **scegliere un'azione**: immettere **2** (per aggiungere gli host)
     * Per **immettere i nomi host delimitati da virgole da aggiungere**: Hana-S1-DB2, Hana-S1-DB3
     * Per **ulteriori componenti per l'installazione**: immettere **2, 3**
     * Per **immettere il nome utente radice [root]**: premere INVIO per accettare il valore predefinito
     * Per i **ruoli selezionati per l'host ' Hana-S1-DB2' [1]**: 1 (per il ruolo di lavoro)
     * Per **immettere il gruppo di failover host per l'host ' Hana-S1-DB2' [impostazione predefinita]**: premere INVIO per accettare il valore predefinito
     * Per **immettere il numero della partizione di archiviazione per l'host ' Hana-S1-DB2' [<<assign automatically>>]**: premere INVIO per accettare il valore predefinito
     * Per **immettere il gruppo di lavoro per l'host ' Hana-S1-DB2' [impostazione predefinita]**: premere INVIO per accettare il valore predefinito
     * Per i **ruoli selezionati per l'host ' Hana-S1-DB3' [1]**: 1 (per il ruolo di lavoro)
     * Per **immettere il gruppo di failover host per l'host ' Hana-S1-DB3' [impostazione predefinita]**: premere INVIO per accettare il valore predefinito
     * Per **immettere il numero della partizione di archiviazione per l'host ' Hana-S1-DB3' [<<assign automatically>>]**: premere INVIO per accettare il valore predefinito
     * Per **immettere il gruppo di lavoro per l'host ' Hana-S1-DB3' [impostazione predefinita]**: premere INVIO per accettare il valore predefinito
     * Per la **password dell'amministratore di sistema (hn1adm)**: immettere la password
     * Per **immettere la password dell'utente agente host SAP (sapadm)**: immettere la password
     * Per **confermare la password dell'utente agente host SAP (sapadm)**: immettere la password
     * Per **nome host del certificato per l'host Hana-S1-DB2** [Hana-S1-DB2]: premere INVIO per accettare il valore predefinito
     * Per **nome host del certificato per l'host Hana-S1-DB3** [Hana-S1-DB3]: premere INVIO per accettare il valore predefinito
     * Per **continuare (y/n)**: convalidare il riepilogo e, in caso di esito positivo, immettere **y**

9. **[2]** ripetere il passaggio precedente per installare i nodi del SAP Hana secondario nel sito 2.   

## <a name="configure-sap-hana-20-system-replication"></a>Configurare la replica di sistema di SAP HANA 2.0

1. **[1]** configurare la replica di sistema nel sito 1:

   Eseguire il backup dei database come **HN1** ADM:

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Copiare i file PKI di sistema nel sito secondario:

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Creare il sito primario:

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** configurare la replica di sistema nel sito 2:
    
   Registrare il secondo sito per avviare la replica di sistema. Eseguire il comando seguente come <hanasid\>adm:

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** Verificare lo stato della replica

   Verificare lo stato della replica e attendere che tutti i database siano sincronizzati.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1, 2]** modificare la configurazione di Hana in modo che la comunicazione per la replica di sistema Hana venga diretta tramite le interfacce di rete virtuale di replica di sistema Hana.   
   - Arrestare HANA in entrambi i siti
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Modificare global.ini per aggiungere il mapping dell'host per la replica di sistema HANA: usare gli indirizzi IP dalla `hsr` subnet.  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.196 = hana-s1-db1
    10.23.1.197 = hana-s1-db2
    10.23.1.198 = hana-s1-db3
    10.23.1.199 = hana-s2-db1
    10.23.1.200 = hana-s2-db2
    10.23.1.201 = hana-s2-db3
    ```

   - Avviare HANA in entrambi i siti
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Per ulteriori informazioni, vedere la pagina relativa alla [risoluzione dei nomi host per la replica di sistema](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

## <a name="create-file-system-resources"></a>Creare risorse file system

Creare una risorsa del cluster file system fittizia, che monitorerà e segnali errori, in caso di problemi di accesso ai file system montati da NFS `/hana/shared` . Ciò consente al cluster di attivare il failover, in caso si verifichi un problema di accesso a `/hana/shared` . Per altri dettagli, vedere [gestione della condivisione NFS non riuscita nel cluster SUSE a disponibilità elevata per la replica di sistema Hana](https://www.suse.com/support/kb/doc/?id=000019904) 

1. **[1]** posizionare pacemaker in modalità manutenzione per preparare la creazione delle risorse del cluster Hana.  
    ```bash
    crm configure property maintenance-mode=true
    ```

2. **[1, 2]** creare la directory nel volume/Hana/SAHRED di e, che verrà usato nella risorsa di monitoraggio file System speciale. È necessario creare le directory in entrambi i siti.  
    ```bash
    mkdir -p /hana/shared/HN1/check
    ```

2. **[Ah]** Creare la directory, che verrà usata per montare la risorsa di monitoraggio file system speciale. La directory deve essere creata in tutti i nodi del cluster HANA.  
    ```bash
    mkdir -p /hana/check
    ```

3. **[1]** creare le risorse del cluster file System.     

    ```bash
    crm configure primitive fs_HN1_HDB03_fscheck Filesystem \
      params device="/hana/shared/HN1/check" \
      directory="/hana/check" fstype=nfs4 \
      options="bind,defaults,rw,hard,proto=tcp,intr,noatime,vers=4.1,lock" \
      op monitor interval=120 timeout=120 on-fail=fence \
      op_params OCF_CHECK_LEVEL=20 \
      op start interval=0 timeout=120 op stop interval=0 timeout=120

    crm configure clone cln_fs_HN1_HDB03_fscheck fs_HN1_HDB03_fscheck \
      meta clone-node-max=1 interleave=true

    crm configure location loc_cln_fs_HN1_HDB03_fscheck_not_on_mm \
      cln_fs_HN1_HDB03_fscheck -inf: hana-s-mm    
    ```
 
   `OCF_CHECK_LEVEL=20` l'attributo viene aggiunto all'operazione di monitoraggio, in modo che le operazioni di monitoraggio eseguano un test di lettura/scrittura sul file system. Senza questo attributo, l'operazione di monitoraggio verifica solo che il file system sia montato. Questo può costituire un problema perché, in caso di perdita della connettività, il file system può rimanere montato, sebbene sia inaccessibile.  

   `on-fail=fence` l'attributo viene aggiunto anche all'operazione di monitoraggio. Con questa opzione, se l'operazione di monitoraggio ha esito negativo in un nodo, il nodo viene immediatamente recintato.   

## <a name="create-sap-hana-cluster-resources"></a>Creare le risorse cluster SAP HANA

1. **[1, 2]** installare l'"hook di replica di sistema" Hana. L'hook deve essere installato in un nodo del database HANA in ogni sito di replica di sistema.         

   1. Preparare l'hook come `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Arrestare HANA in entrambi i siti di replica di sistema. Esegui come <SID \> adm:
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Modificare `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[Ah]** Il cluster richiede la configurazione di sudoers sul nodo del cluster per <SID \> adm. In questo esempio si otterrà creando un nuovo file. Eseguire i comandi come `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    EOF
    ```

3. **[1, 2]** avviare SAP Hana in entrambi i siti di replica. Eseguire come <SID \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** verificare l'installazione dell'hook. Eseguire come <SID \> adm nel sito di replica di sistema Hana attivo.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # 2021-03-31 01:02:42.695244 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:02:58.966856 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.453100 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.619768 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.743444 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:04:15.062181 ha_dr_SAPHanaSR SOK

    ```

5. **[1]** creare le risorse del cluster Hana. Eseguire i comandi seguenti come `root` .    
   1. Verificare che il cluster sia già in modalità di manutenzione.  
    
   2. Successivamente, creare la risorsa della topologia HANA.  
      ```bash
      sudo crm configure primitive rsc_SAPHanaTopology_HN1_HDB03 ocf:suse:SAPHanaTopology \
        op monitor interval="10" timeout="600" \
        op start interval="0" timeout="600" \
        op stop interval="0" timeout="300" \
        params SID="HN1" InstanceNumber="03"

      sudo crm configure clone cln_SAPHanaTopology_HN1_HDB03 rsc_SAPHanaTopology_HN1_HDB03 \
       meta clone-node-max="1" target-role="Started" interleave="true"
      ```

   3. Successivamente, creare la risorsa dell'istanza HANA.  
      > [!NOTE] 
      > Questo articolo contiene riferimenti ai termini *Master* e *slave*, che non vengono più utilizzati da Microsoft. Quando questi termini vengono rimossi dal software, verranno rimossi da questo articolo.
 
      ```bash
      sudo crm configure primitive rsc_SAPHana_HN1_HDB03 ocf:suse:SAPHanaController \
        op start interval="0" timeout="3600" \
        op stop interval="0" timeout="3600" \
        op promote interval="0" timeout="3600" \
        op monitor interval="60" role="Master" timeout="700" \
        op monitor interval="61" role="Slave" timeout="700" \
        params SID="HN1" InstanceNumber="03" PREFER_SITE_TAKEOVER="true" \
        DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

      sudo crm configure ms msl_SAPHana_HN1_HDB03 rsc_SAPHana_HN1_HDB03 \
        meta clone-node-max="1" master-max="1" interleave="true"
      ```
      > [!IMPORTANT]
      > Si consiglia di impostare AUTOMATED_REGISTER su **No**, durante l'esecuzione di test di failover completi, per impedire che l'istanza primaria non riuscita venga registrata automaticamente come secondaria. Una volta completati i test di failover, impostare AUTOMATED_REGISTER su **Sì**, in modo che dopo che la replica del sistema di acquisizione delle acquisizioni possa essere ripresa automaticamente. 

   4. Creare un indirizzo IP virtuale e le risorse associate.  
      ```bash
      sudo crm configure primitive rsc_ip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="10.23.0.27"

      sudo crm configure primitive rsc_nc_HN1_HDB03 azure-lb port=62503 \
        meta resource-stickiness=0

      sudo crm configure group g_ip_HN1_HDB03 rsc_ip_HN1_HDB03 rsc_nc_HN1_HDB03
      ```

   5. Creare i vincoli del cluster  
      ```bash
      # Colocate the IP with HANA master
      sudo crm configure colocation col_saphana_ip_HN1_HDB03 4000: g_ip_HN1_HDB03:Started \
        msl_SAPHana_HN1_HDB03:Master  

      # Start HANA Topology before HANA  instance
      sudo crm configure order ord_SAPHana_HN1_HDB03 Optional: cln_SAPHanaTopology_HN1_HDB03 \
        msl_SAPHana_HN1_HDB03

      # HANA resources don't run on the majority maker node
      sudo crm configure location loc_SAPHanaCon_not_on_majority_maker msl_SAPHana_HN1_HDB03 -inf: hana-s-mm
      sudo crm configure location loc_SAPHanaTop_not_on_majority_maker cln_SAPHanaTopology_HN1_HDB03 -inf: hana-s-mm
      ```

6. **[1]** configurare proprietà aggiuntive del cluster   
    ```bash
    sudo crm configure rsc_defaults resource-stickiness=1000
    sudo crm configure rsc_defaults migration-threshold=50
    ```
7. **[1]** verificare la comunicazione tra l'hook e il cluster
    ```bash
    crm_attribute -G -n hana_hn1_glob_srHook
    # Expected result
    # crm_attribute -G -n hana_hn1_glob_srHook
    # scope=crm_config  name=hana_hn1_glob_srHook value=SOK
    ```

8. **[1]** posizionare il cluster fuori dalla modalità di manutenzione. Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate.  
    ```bash
    # Cleanup any failed resources - the following command is example 
    crm resource cleanup rsc_SAPHana_HN1_HDB03

    # Place the cluster out of maintenance mode
    sudo crm configure property maintenance-mode=false
    ```
  
   > [!NOTE]
   > I timeout nella configurazione precedente sono solo esempi e possono essere adattati alla configurazione HANA specifica. Ad esempio, potrebbe essere necessario aumentare il timeout di avvio, se l'avvio del database di SAP HANA richiede più tempo.
  

## <a name="test-sap-hana-failover"></a>Test SAP HANA failover 

> [!NOTE]
> Questo articolo contiene riferimenti ai termini *Master* e *slave*, che non vengono più utilizzati da Microsoft. Quando questi termini vengono rimossi dal software, verranno rimossi da questo articolo.

1. Prima di avviare un test, controllare il cluster e SAP HANA lo stato della replica di sistema.    

   a. Verificare che non siano presenti azioni cluster non riuscite  
     ```bash
     #Verify that there are no failed cluster actions
     crm status
     # Example 
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Full list of resources:
     #
     # stonith-sbd    (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     ```

   b. Verificare che la replica di sistema SAP HANA sia sincronizzata

     ```bash
     # Verify HANA HSR is in sync
     sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
     #| Database | Host         | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary    | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
     #|          |              |       |              |           |         |           | Host         | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
     #| -------- | ------------ | ----- | ------------ | --------- | ------- | --------- | ------------ | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
     #| SYSTEMDB | hana-s1-db1  | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1  |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1  |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db3  | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db3  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db2  | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db2  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #
     #status system replication site "1": ACTIVE
     #overall system replication status: ACTIVE
     #
     #Local System Replication State
     #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     #
     #mode: PRIMARY
     #site id: 1
     #site name: HANA_S1
     ```

2. Si consiglia di convalidare accuratamente la configurazione del cluster di SAP HANA, eseguendo i test, documentati in [disponibilità elevata per SAP Hana in macchine virtuali di Azure in SLES](./sap-hana-high-availability.md#test-the-cluster-setup) e nello [scenario ottimizzato per le prestazioni con scalabilità orizzontale della replica SLES](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_testing_the_cluster).

3. Verificare la configurazione del cluster per uno scenario di errore, quando un nodo perde l'accesso alla condivisione NFS ( `/hana/shared` ).  

   Gli agenti di risorse SAP HANA dipendono dai file binari, archiviati in `/hana/shared` per eseguire operazioni durante il failover. Il file System `/hana/shared` viene montato su NFS nella configurazione presentata. Un test che può essere eseguito consiste nel creare una regola del firewall temporanea per bloccare l'accesso al `/hana/shared` volume e in una delle macchine virtuali del sito primario. Questo approccio verifica che venga eseguito il failover del cluster, se l'accesso a `/hana/shared` viene perso nel sito di replica del sistema attivo.  

   **Risultato previsto**: quando si blocca l'accesso al `/hana/shared` volume e in una delle macchine virtuali del sito primario, l'operazione di monitoraggio che esegue l'operazione di lettura/scrittura su file System, avrà esito negativo, in quanto non è in grado di accedere al file System e attiverà il failover delle risorse Hana. Lo stesso risultato è previsto quando il nodo HANA perde l'accesso alla condivisione NFS.  
     
   È possibile controllare lo stato delle risorse del cluster eseguendo `crm_mon` o `crm status` . Stato delle risorse prima dell'avvio del test:
     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1     
      ```

   Per simulare l'errore per `/hana/shared` , verificare innanzitutto l'indirizzo IP del `/hana/shared` volume e nel sito primario. È possibile eseguire questa operazione eseguendo `df -kh|grep /hana/shared` . 
   Configurare quindi una regola del firewall temporanea per bloccare l'accesso all'indirizzo IP del `/hana/shared` volume e eseguendo il comando seguente in una delle macchine virtuali del sito di replica di sistema Hana primarie.
   In questo esempio il comando è stato eseguito in Hana-S1-DB1.

     ```bash
     iptables -A INPUT -s 10.23.1.7 -j DROP; iptables -A OUTPUT -d 10.23.1.7 -j DROP
     ```

   Verrà eseguita la migrazione delle risorse del cluster all'altro sito di replica di sistema HANA.    
              
   Se si imposta AUTOMATED_REGISTER = "false", sarà necessario configurare SAP HANA la replica di sistema nel sito secondario. In questo caso, è possibile eseguire questi comandi per riconfigurare SAP HANA come secondario.   

     ```bash
     # Execute on the secondary 
     su - hn1adm
     # Make sure HANA is not running on the secondary site. If it is started, stop HANA
     sapcontrol -nr 03 -function StopWait 600 10
     # Register the HANA secondary site
     hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
     # Switch back to root and cleanup failed resources
     crm resource cleanup SAPHana_HN1_HDB03
     ```

   Stato delle risorse, dopo il test: 

     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s2-db1 ]
     #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1
     ```


## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* [Volumi NFS v4.1 in Azure NetApp Files per SAP HANA](./hana-vm-operations-netapp.md)
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA nelle VM di Azure, vedere [disponibilità elevata di SAP Hana in macchine virtuali di Azure][sap-hana-ha].