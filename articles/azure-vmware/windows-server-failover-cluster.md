---
title: Cluster di failover di Windows Server in Azure rete VSAN soluzione VMware con dischi condivisi nativi
description: Configurare Windows Server failover cluster (WSFC) in una soluzione VMware di Azure e sfruttare le soluzioni che richiedono la funzionalità WSFC.
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 8162e15675d8bbde9267126c785f152d1cb860bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562240"
---
# <a name="windows-server-failover-cluster-on-azure-vmware-solution-vsan-with-native-shared-disks"></a>Cluster di failover di Windows Server in Azure rete VSAN soluzione VMware con dischi condivisi nativi

Questo articolo illustra la procedura dettagliata per la configurazione di Windows Server failover cluster in una soluzione VMware di Azure. L'implementazione in questo articolo è per il modello di prova e per scopi pilota. È consigliabile usare una configurazione cluster-in-a-Box (CIB) fino a quando non sono disponibili criteri di posizionamento.

Windows Server failover cluster (WSFC), noto in precedenza come servizio cluster di servizi Microsoft (MSCS), è una funzionalità del sistema operativo Windows Server. WSFC è una funzionalità cruciale per l'azienda e per molte applicazioni è necessario. WSFC, ad esempio, è necessario per le configurazioni seguenti:

- SQL Server configurato come:
  - Always On istanza del cluster di failover (FCI) per la disponibilità elevata a livello di istanza.
  - Always On gruppo di disponibilità (AG) per la disponibilità elevata a livello di database.
- Servizi file di Windows:
  - Condivisione file generica in esecuzione nel nodo del cluster attivo.
  - Scale-Out file server (SOFS), in cui vengono archiviati i file nei volumi condivisi del cluster (CSV).
  - Spazi di archiviazione diretta (S2D); dischi locali usati per creare pool di archiviazione tra diversi nodi del cluster.

È possibile ospitare il cluster WSFC in diverse istanze della soluzione VMware di Azure, nota come cluster-over-box (CAB). È anche possibile collocare il cluster WSFC in un singolo nodo della soluzione VMware di Azure. Questa configurazione è nota come cluster-in-box (CIB). Non è consigliabile usare una soluzione CIB per un'implementazione di produzione. Se il singolo nodo della soluzione VMware di Azure avesse esito negativo, tutti i nodi del cluster WSFC verrebbero spenti e l'applicazione avrebbe avuto tempi di inattività. La soluzione VMware di Azure richiede almeno tre nodi in un cluster di cloud privato.

È importante distribuire una configurazione WSFC supportata. Si vuole che la soluzione sia supportata in vSphere e con la soluzione VMware di Azure. VMware fornisce un documento dettagliato su WSFC in vSphere 6,7, il [programma di installazione per il clustering di failover e il servizio cluster Microsoft](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf).

Questo articolo è incentrato su WSFC in Windows Server 2016 e Windows Server 2019. Le versioni precedenti di Windows Server sono escluse dal [supporto mainstream](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) e pertanto non vengono considerate qui.

È necessario [creare prima un cluster WSFC](/windows-server/failover-clustering/create-failover-cluster). Per ulteriori informazioni su WSFC, vedere [clustering di failover in Windows Server](/windows-server/failover-clustering/failover-clustering-overview). Usare le informazioni fornite in questo articolo per le specifiche di una distribuzione WSFC in una soluzione VMware di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Ambiente della soluzione VMware di Azure
- Supporto di installazione del sistema operativo Microsoft Windows Server

## <a name="reference-architecture"></a>Architettura di riferimento

La soluzione VMware di Azure fornisce il supporto nativo per WSFC virtualizzato. Supporta le prenotazioni permanenti SCSI-3 (SCSI3PR) a livello di disco virtuale. Questo supporto è richiesto da WSFC per arbitrare l'accesso a un disco condiviso tra i nodi. Il supporto di SCSI3PRs consente la configurazione di WSFC con una risorsa disco condivisa tra VM in modalità nativa negli archivi dati di rete VSAN.

Il diagramma seguente illustra l'architettura dei nodi virtuali WSFC in un cloud privato della soluzione VMware di Azure. Mostra la posizione in cui risiede la soluzione VMware di Azure, inclusi i server virtuali WSFC (casella rossa), in relazione alla piattaforma Azure più ampia. Questo diagramma illustra una tipica architettura hub-spoke, ma è possibile una configurazione simile con l'uso della rete WAN virtuale di Azure. Entrambi offrono tutti i valori che possono essere portati da altri servizi di Azure.

[![Diagramma che illustra l'architettura dei nodi virtuali WSFC in un cloud privato della soluzione VMware di Azure.](media/windows-server-failover-cluster/windows-server-failover-architecture.png)](media/windows-server-failover-cluster/windows-server-failover-architecture.png#lightbox)

## <a name="supported-configurations"></a>Configurazioni supportate

Attualmente sono supportate le configurazioni seguenti:

- Microsoft Windows Server 2012 o versione successiva
- Fino a cinque nodi del clustering di failover per cluster
- Fino a quattro schede PVSCSI per macchina virtuale
- Fino a 64 dischi per scheda PVSCSI

## <a name="virtual-machine-configuration-requirements"></a>Requisiti di configurazione della macchina virtuale

### <a name="wsfc-node-configuration-parameters"></a>Parametri di configurazione del nodo WSFC

- Installare gli strumenti VMware più recenti in ogni nodo WSFC.
- La combinazione di dischi condivisi e non condivisi in una singola scheda SCSI virtuale non è supportata. Ad esempio, se il disco di sistema (unità C:) viene collegato a SCSI0:0, il primo disco condiviso viene collegato a SCSI1:0. Un nodo VM di un cluster WSFC ha lo stesso valore massimo del controller SCSI virtuale come macchina virtuale ordinaria, fino a quattro (4) controller SCSI virtuali.
- Gli ID SCSI dei dischi virtuali devono essere coerenti tra tutte le VM che ospitano nodi dello stesso cluster WSFC.

| **Componente** | **Requisiti** |
| --- | --- |
| Versione hardware della macchina virtuale | 11 o superiore per supportare Live vMotion. |
| SCHEDA di interfaccia di rete virtuale | Scheda di interfaccia di rete (NIC) VMXNET3 paravirtualizzata; abilitare il Windows Receive Side Scaling (RSS) in-guest nella scheda di interfaccia di rete virtuale. |
| Memoria | Usare la memoria di prenotazione della macchina virtuale completa per i nodi nel cluster WSFC. |
| Aumentare il timeout di I/O di ogni nodo WSFC. | Modificare HKEY \_ Local \_ MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet a 60 secondi o superiore. Se si ricrea il cluster, è possibile che questo valore venga reimpostato sull'impostazione predefinita, pertanto è necessario modificarlo di nuovo. |
| Monitoraggio dell'integrità del cluster Windows | Il valore del parametro SameSubnetThreshold del monitoraggio dell'integrità del cluster Windows deve essere modificato per consentire almeno 10 heartbeat mancanti. Questa è [l'impostazione predefinita in Windows Server 2016](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). Questa raccomandazione si applica a tutte le applicazioni che utilizzano WSFC, inclusi i dischi condivisi e non condivisi. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>Nodo WSFC-parametri di configurazione dei dischi di avvio


| **Componente** | **Requisiti** |
| --- | --- |
| Tipo di controller SCSI | SAS per la logica LSI |
| Modalità disco | Le macchine |
| Condivisione bus SCSI | nessuno |
| Modificare le impostazioni avanzate per un controller SCSI virtuale che ospita il dispositivo di avvio. | Aggiungere le impostazioni avanzate seguenti a ogni nodo WSFC:<br /> scsiX. returnNoConnectDuringAPD = "TRUE"<br />scsiX. returnBusyOnNoConnectStatus = "FALSE"<br />Dove X è il numero ID del controller bus SCSI del dispositivo di avvio. Per impostazione predefinita, X è impostato su 0. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>Nodo WSFC-parametri di configurazione dei dischi condivisi


| **Componente** | **Requisiti** |
| --- | --- |
| Tipo di controller SCSI | VMware paravirtualizzate (PVSCSI) |
| Modalità disco | Indipendente-persistente (passaggio 2 nell'illustrazione riportata di seguito). Utilizzando questa impostazione, si garantisce che tutti i dischi siano esclusi dagli snapshot. Gli snapshot non sono supportati per le macchine virtuali basate su WSFC. |
| Condivisione bus SCSI | Fisico (passaggio 1 nell'illustrazione riportata di seguito) |
| Flag per più writer | Non usato |
| Formato del disco | Provisioning di spessore. (EZT) non è obbligatorio con rete VSAN. |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="Screenshot che mostra la pagina Modifica impostazioni per l'hardware virtuale.":::

## <a name="non-supported-scenarios"></a>Scenari non supportati

Le funzionalità seguenti non sono supportate per WSFC in una soluzione VMware di Azure:

- Archivi dati NFS
- Spazi di archiviazione
- Rete VSAN con servizio iSCSI
- Cluster con estensione rete VSAN
- Compatibilità vMotion migliorata (EVC)
- Tolleranza di errore vSphere (FT)
- Snapshot
- Live (online) Storage vMotion
- N-Port ID Virtualization (NPIV)

Le modifiche a caldo apportate all'hardware della macchina virtuale potrebbero compromettere l'heartbeat tra i nodi WSFC.

Le attività seguenti non sono supportate e possono causare il failover del nodo WSFC:

- Aggiunta di memoria a caldo
- Aggiunta di CPU a caldo
- Uso di snapshot
- Aumento delle dimensioni di un disco condiviso
- Sospensione e ripresa dello stato della macchina virtuale
- Overcommit della memoria che conduce allo scambio ESXi o alla Balloon di memoria della macchina virtuale
- File VMDK locale a caldo esteso, anche se non è associato al controller di condivisione bus SCSI

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Configurare WSFC con dischi condivisi nella soluzione VMware di Azure rete VSAN

1. Verificare che sia disponibile un ambiente Active Directory.
2. Creare macchine virtuali (VM) nell'archivio dati rete VSAN.
3. Accendere tutte le VM, configurare il nome host, gli indirizzi IP, aggiungere tutte le macchine virtuali a un dominio Active Directory e installare gli aggiornamenti più recenti del sistema operativo.
4. Installare gli strumenti VMware più recenti.
5. Abilitare e configurare la funzionalità cluster di failover di Windows Server in ogni macchina virtuale.
6. Configurare un server di controllo per il quorum (una condivisione file di controllo funziona correttamente).
7. Spegnere tutti i nodi del cluster WSFC.
8. Aggiungere uno o più controller SCSI Paravirtual (fino a quattro) a ogni parte della macchina virtuale WSFC. Usare le impostazioni per i paragrafi precedenti.
9. Nel primo nodo del cluster aggiungere tutti i dischi condivisi necessari usando **Aggiungi nuovo**  >  **disco rigido** del dispositivo. La condivisione del disco deve essere lasciata come non **specificata** (impostazione predefinita) e come modalità disco come **persistente indipendente**. Collegarlo ai controller creati nei passaggi precedenti.
10. Continuare con i nodi WSFC rimanenti. Aggiungere i dischi creati nel passaggio precedente selezionando **Aggiungi nuovo dispositivo**  >  **disco rigido esistente**. Assicurarsi di mantenere gli stessi ID SCSI del disco in tutti i nodi WSFC.
11. Accendere il primo nodo WSFC; accedere e aprire la console di gestione disco (MMC). Assicurarsi che i dischi condivisi aggiunti possano essere gestiti dal sistema operativo e che siano inizializzati. Formattare i dischi e assegnare una lettera di unità.
12. Accendere gli altri nodi WSFC.
13. Aggiungere il disco al cluster WSFC utilizzando l' **Aggiunta guidata disco** e aggiungerli a una volume condiviso cluster.
14. Testare un failover usando la **procedura guidata di spostamento del disco** e verificare che il cluster WSFC con dischi condivisi funzioni correttamente.
15. Eseguire la **procedura guidata cluster di convalida** per verificare se il cluster e i relativi nodi funzionano correttamente.

    È importante tenere presente gli elementi specifici seguenti dal test di convalida del cluster:

       - **Convalidare la prenotazione permanente degli spazi di archiviazione**. Se non si usano spazi di archiviazione con il cluster, ad esempio in una soluzione VMware di Azure rete VSAN, questo test non è applicabile. È possibile ignorare tutti i risultati del test della prenotazione persistente di spazi di archiviazione, incluso questo avviso. Per evitare gli avvisi, è possibile escludere questo test.
        
      - **Convalidare la comunicazione di rete**. Il test di convalida del cluster genererà un avviso che indica che è disponibile una sola interfaccia di rete per ogni nodo del cluster. È possibile ignorare questo avviso. La soluzione VMware di Azure fornisce la disponibilità e le prestazioni necessarie, perché i nodi sono connessi a uno dei segmenti NSX-T. Tuttavia, conserva questo elemento come parte del test di convalida del cluster, in quanto convaliderà altri aspetti della comunicazione di rete.

16. Creare una regola DRS per inserire le VM WSFC negli stessi nodi della soluzione VMware di Azure. A tale scopo, è necessaria una regola di affinità da host a macchina virtuale. In questo modo, i nodi del cluster vengono eseguiti nello stesso host della soluzione VMware di Azure. Anche in questo caso, questa operazione è a scopo pilota fino a quando non sono disponibili criteri di posizionamento.

    >[!NOTE]
    > A tale proposito è necessario creare un ticket di richiesta di supporto. L'organizzazione del supporto tecnico di Azure sarà in grado di semplificare questa operazione.

## <a name="related-information"></a>Informazioni correlate

- [Clustering di failover in Windows Server](/windows-server/failover-clustering/failover-clustering-overview)
- [Linee guida per Microsoft Clustering su vSphere (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [Informazioni sul programma di installazione per il clustering di failover e il servizio cluster Microsoft (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [Rete VSAN 6,7 U3-WSFC con dischi condivisi &amp; SCSI-3 prenotazioni permanenti (VMware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Limiti della soluzione VMware di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata illustrata la configurazione di un cluster WSFC in una soluzione VMware di Azure, è possibile che si vogliano ottenere informazioni su:

- Configurazione del nuovo WSFC mediante l'aggiunta di altre applicazioni che richiedono la funzionalità WSFC. Ad esempio, SQL Server e SAP ASC.
- Configurazione di una soluzione di backup.
  - [Configurazione di server di Backup di Azure per la soluzione VMware di Azure](./set-up-backup-server-for-azure-vmware-solution.md)
  - [Soluzioni di backup per le macchine virtuali della soluzione VMware di Azure](./ecosystem-back-up-vms.md)