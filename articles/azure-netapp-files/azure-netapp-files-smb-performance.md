---
title: Domande frequenti sulle prestazioni SMB per Azure NetApp Files| Microsoft Docs
description: Risposte alle domande frequenti sulle prestazioni SMB per Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 1ec58b056bd610773500c8ace1fb12d268b980e0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726719"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Domande frequenti sulle prestazioni SMB per Azure NetApp Files

Questo articolo risponde alle domande frequenti sulle procedure consigliate per le prestazioni SMB per Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>SMB Multichannel è abilitato nelle condivisioni SMB? 

Sì, SMB Multichannel è abilitato per impostazione predefinita, una modifica apportata all'inizio di gennaio 2020. La funzionalità è stata abilitata per tutte le condivisioni SMB pre-esistenti e anche tutti i volumi appena creati avranno la funzionalità abilitata al momento della creazione. 

Qualsiasi connessione SMB stabilita prima dell'abilitazione della funzionalità dovrà essere reimpostata per sfruttare la funzionalità SMB multicanale. Per eseguire la reimpostazione, è possibile disconnettere e riconnettere la condivisione SMB.

## <a name="is-rss-supported"></a>RSS è supportato?

Sì, Azure NetApp Files supporta receive-side-scaling (RSS).

Con SMB Multichannel abilitato, un client SMB3 stabilisce più connessioni TCP al server SMB Azure NetApp Files tramite una scheda di interfaccia di rete (NIC) che supporta rss singolo. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Quali versioni di Windows supportano SMB Multichannel?

Windows supporta SMB Multichannel da Windows 2012 per consentire prestazioni ottimali.  Per [informazioni dettagliate, vedere Distribuire SMB multicanale](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) e Nozioni di [base su SMB multicanale.](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) 


## <a name="does-my-azure-virtual-machine-support-rss"></a>La macchina virtuale di Azure supporta RSS?

Per verificare se le schede di interfaccia di rete della macchina virtuale di Azure supportano RSS, eseguire il comando come indicato di `Get-SmbClientNetworkInterface` seguito e selezionare il campo `RSS Capable` : 

![Screenshot che mostra l'output RSS per la macchina virtuale di Azure.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>La Azure NetApp Files supporta SMB direct?

No, Azure NetApp Files non supporta SMB diretto. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Qual è il vantaggio di SMB multicanale? 

La funzionalità SMB multicanale consente a un client SMB3 di stabilire un pool di connessioni tramite una singola scheda di interfaccia di rete (NIC) o più schede di interfaccia di rete e di usarle per inviare richieste per una singola sessione SMB. Al contrario, per impostazione predefinita, SMB1 e SMB2 richiedono al client di stabilire una connessione e inviare tutto il traffico SMB per una determinata sessione tramite tale connessione. Questa singola connessione limita le prestazioni complessive del protocollo che possono essere ottenute da un singolo client.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>È necessario configurare più schede di interfaccia di rete nel client per SMB?

No. Il client SMB corrisponderà al numero di schede di interfaccia di rete restituito dal server SMB.  Ogni volume di archiviazione è accessibile da un solo endpoint di archiviazione.  Ciò significa che per una determinata relazione SMB verrà usata una sola scheda di interfaccia di rete.  

Come illustrato `Get-SmbClientNetworkInterace` nell'output seguente, la macchina virtuale ha 2 interfacce di rete, 15 e 12.  Come illustrato nel comando seguente, anche se sono presenti due schede di interfaccia di rete che supportano RSS, viene usata solo l'interfaccia 12 in connessione alla condivisione SMB. L'interfaccia `Get-SmbMultichannelConnection` 15 non è in uso.

![Screeshot che mostra l'output per le schede di interfaccia di rete con supporto per RSS.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Gruppo NIC è supportato in Azure?

Gruppo NIC non è supportato in Azure. Anche se nelle macchine virtuali di Azure sono supportate più interfacce di rete, rappresentano un costrutto logico anziché fisico. Di conseguenza, non forniscono tolleranza di errore.  Inoltre, la larghezza di banda disponibile per una macchina virtuale di Azure viene calcolata per la macchina e non per le singole interfacce di rete.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Quali sono le prestazioni per SMB multicanale?

I test e i grafici seguenti illustrano la potenza di SMB multicanale nei carichi di lavoro a istanza singola.

### <a name="random-io"></a>I/O casuale  

Con SMB multicanale disabilitato nel client, i test di lettura e scrittura da 4 KiB puri sono stati eseguiti usando FIO e una versione da 40 GiB working set.  La condivisione SMB è stata scollegata tra ogni test, con incrementi del numero di connessioni client SMB per ogni impostazione dell'interfaccia di rete RSS `1` di , , , , `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . I test mostrano che l'impostazione predefinita di è sufficiente per i carichi di lavoro con utilizzo intensivo di `4` I/O; l'incremento a `8` e ha avuto un effetto `16` trascurabile. 

Il comando `netstat -na | findstr 445` ha dimostrato che sono state stabilite connessioni aggiuntive con incrementi da a e a `1` `4` `8` `16` .  Quattro core CPU sono stati completamente utilizzati per SMB durante ogni test, come confermato dalla statistica perfmon `Per Processor Network Activity Cycles` (non inclusa in questo articolo).

![Grafico che mostra il confronto di I/O casuale di SMB Multichannel.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

La macchina virtuale di Azure non influisce sui limiti di I/O di archiviazione SMB (né NFS).  Come illustrato nel grafico seguente, il tipo di istanza D32ds ha una frequenza limitata di 308.000 per le operazioni di I/O al secondo di archiviazione memorizzate nella cache e di 51.200 per le operazioni di I/O al secondo di archiviazione non memorizzate nella cache.  Tuttavia, il grafico precedente mostra un numero significativamente maggiore di I/O su SMB.

![Grafico che mostra il test di confronto di I/O casuale.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>I/O sequenziale 

Test simili ai test di I/O casuali descritti in precedenza sono stati eseguiti con I/O sequenziale da 64 KiB. Anche se l'aumento del numero di connessioni client per ogni interfaccia di rete RSS oltre 4' non ha avuto alcun effetto evidente sull'I/O casuale, lo stesso non si applica all'I/O sequenziale. Come illustrato nel grafico seguente, ogni aumento è associato a un aumento corrispondente della velocità effettiva di lettura. La velocità effettiva di scrittura è rimasta flat a causa delle restrizioni di larghezza di banda di rete poste da Azure per ogni tipo o dimensione dell'istanza. 

![Grafico che mostra il confronto dei test della velocità effettiva.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure inserisce limiti di frequenza di rete per ogni tipo di macchina virtuale/dimensione. Il limite di velocità viene imposto solo al traffico in uscita. Il numero di schede di interfaccia di rete presenti in una macchina virtuale non ha alcun impatto sulla quantità totale di larghezza di banda disponibile per la macchina.  Ad esempio, il tipo di istanza D32ds ha un limite di rete imposto di 16.000 Mbps (2.000 MiB/s).  Come illustrato nel grafico sequenziale precedente, il limite influisce sul traffico in uscita (scritture) ma non sulle operazioni di lettura multicanale.

![Grafico che mostra il test di confronto di I/O sequenziale.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Quali prestazioni sono previste con una singola istanza con un set di dati da 1 TB?

Per fornire informazioni più dettagliate sui carichi di lavoro con combinazioni di lettura/scrittura, i due grafici seguenti mostrano le prestazioni di un singolo volume cloud a livello di servizio Ultra di 50 TB con un set di dati da 1 TB e con SMB multicanale di 4. È stato usato un IODepth ottimale di 16 e sono stati usati parametri FIO (Flexible IO) per garantire l'uso completo della larghezza di banda di rete ( `numjobs=16` ).

Il grafico seguente mostra i risultati per 4.000 operazioni di I/O casuali, con una singola istanza di macchina virtuale e una combinazione di lettura/scrittura a intervalli del 10%:

![Grafico che mostra il test di I/O casuale _D32ds_v4 4K standard di Windows 2019.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

Il grafico seguente mostra i risultati per l'I/O sequenziale:

![Grafico che mostra la velocità effettiva sequenziale standard di Windows 2019 _D32ds_v4 64.000.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Quali prestazioni sono previste quando si esegue la scalabilità orizzontale usando 5 macchine virtuali con un set di dati da 1 TB?

Questi test con 5 macchine virtuali usano lo stesso ambiente di test della singola macchina virtuale, con ogni processo che scrive nel proprio file.

Il grafico seguente mostra i risultati per l'I/O casuale:

![Grafico che mostra il test di I/O di Windows 2019 standard _D32ds_v4 test di I/O di 4K a 5 istanze.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

Il grafico seguente mostra i risultati per l'I/O sequenziale:

![Grafico che mostra la velocità effettiva sequenziale _D32ds_v4 64.000 istanze standard di Windows 2019.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Come monitorare le schede ethernet Hyper-V e assicurarsi di ottimizzare la capacità di rete?  

Una strategia usata nei test con FIO consiste nell'impostare `numjobs=16` . In questo modo, ogni processo viene forkato in 16 istanze specifiche per ottimizzare la Microsoft Hyper-V di rete.

È possibile verificare l'attività in ognuna delle schede in Windows Monitor prestazioni selezionando Monitor prestazioni > Aggiungi contatori > interfaccia di rete > Microsoft Hyper-V **scheda di rete**.

![Screenshot che mostra Monitor prestazioni'interfaccia Aggiungi contatore.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Dopo aver eseguito il traffico dati nei volumi, è possibile monitorare le schede in Windows Monitor prestazioni. Se non si usano tutte queste 16 schede virtuali, potrebbe non essere possibile ottimizzare la capacità della larghezza di banda di rete.

![Screenshot che mostra l Monitor prestazioni output.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>È consigliabile la rete accelerata?

Per ottenere prestazioni massime, è consigliabile configurare [la rete accelerata laddove](../virtual-network/create-vm-accelerated-networking-powershell.md) possibile. Tenere presenti le considerazioni seguenti:  

* Il portale di Azure abilita la rete accelerata per impostazione predefinita per le macchine virtuali che supportano questa funzionalità.  Tuttavia, altri metodi di distribuzione come Ansible e strumenti di configurazione simili potrebbero non essere disponibili.  La mancata abilitazione della rete accelerata può migliorare le prestazioni di un computer.  
* Se la rete accelerata non è abilitata nell'interfaccia di rete di una macchina virtuale a causa della mancanza di supporto per un tipo o una dimensione di istanza, rimarrà disabilitata con tipi di istanza più grandi. In questi casi sarà necessario un intervento manuale.

## <a name="are-jumbo-frames-supported"></a>I frame jumbo sono supportati?

I frame Jumbo non sono supportati con le macchine virtuali di Azure.

## <a name="is-smb-signing-supported"></a>La firma SMB è supportata? 

Il protocollo SMB fornisce la base per la condivisione di file e stampanti e altre operazioni di rete, ad esempio l'amministrazione remota di Windows. Per impedire gli attacchi man in the middle che modificano i pacchetti SMB durante la trasmissione, il protocollo SMB supporta la firma digitale dei pacchetti SMB. 

La firma SMB è supportata per tutte le versioni del protocollo SMB supportate da Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Qual è l'impatto sulle prestazioni della firma SMB?  

La firma SMB ha un effetto deletero sulle prestazioni SMB. Tra le altre possibili cause della riduzione delle prestazioni, la firma digitale di ogni pacchetto utilizza cpu sul lato client aggiuntiva, come illustrato nell'output perfmon seguente. In questo caso, Core 0 risulta responsabile di SMB, inclusa la firma SMB.  Un confronto con i numeri di velocità effettiva di lettura sequenziale non multicanale nella sezione precedente mostra che la firma SMB riduce la velocità effettiva complessiva da 875MiB/s a circa 250MiB/s. 

![Grafico che mostra l'impatto sulle prestazioni della firma SMB.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)

## <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a>Qual è l'impatto previsto della crittografia SMB sui carichi di lavoro client?

Vedere [Domande frequenti sulla crittografia SMB.](azure-netapp-files-faqs.md#smb_encryption_impact)

## <a name="next-steps"></a>Passaggi successivi  

- [Domande frequenti su Azure NetApp Files](azure-netapp-files-faqs.md)
- Vedere [l'Azure NetApp Files: Condivisioni file](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) aziendali gestite per carichi di lavoro SMB sull'uso di condivisioni file SMB con Azure NetApp Files.