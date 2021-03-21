---
title: Oracle Database nelle strategie di backup di macchine virtuali di Azure
description: Opzioni per eseguire il backup dei database Oracle in un ambiente VM Linux di Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 8a1eb1c21663e0294cd384daa0ba644adf78007a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673206"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Oracle Database nelle strategie di backup delle macchine virtuali Linux di Azure

I backup del database proteggono il database dalla perdita di dati a causa di un errore del componente di archiviazione e data center errore. Possono anche essere un mezzo di recupero da errori umani e un modo per clonare un database a scopo di sviluppo o test. 

In Azure, poiché tutte le risorse di archiviazione sono altamente ridondanti e la perdita di uno o più dischi non comporta un'interruzione del database, i backup vengono spesso usati per proteggersi da errori umani, per la clonazione di operazioni o la conservazione dei dati ai fini della normativa. Si tratta anche di un mezzo per proteggersi da un'interruzione a livello di area in cui una tecnologia di ripristino di emergenza come DataGuard non è in uso. In questo caso, i backup devono essere archiviati in diverse aree di Azure usando la replica con ridondanza geografica, in modo da essere disponibili al di fuori dell'area del database primario.

## <a name="azure-storage"></a>Archiviazione di Azure 

I [servizi di archiviazione di Azure](../../../storage/common/storage-introduction.md) sono la soluzione di archiviazione cloud Microsoft per gli scenari di archiviazione dei dati moderni. Archiviazione di Azure offre una serie di servizi che possono essere usati per montare l'archiviazione esterna nella macchina virtuale Linux di Azure, idonea come supporto di backup per i database Oracle. Uno strumento di backup come Oracle RMAN è necessario per avviare un'operazione di backup o ripristino e copiare il backup nel/dal servizio di archiviazione di Azure.
 
I servizi di archiviazione di Azure offrono i vantaggi seguenti:

-  Durabilità e disponibilità elevata. La ridondanza garantisce che i dati siano al sicuro in caso di errori hardware temporanei. Per impostazione predefinita, l'archiviazione è con mirroring tripla. Si può anche scegliere di replicare i dati tra data center o aree geografiche per una protezione aggiuntiva da catastrofi locali o calamità naturali. Con questo tipo di replica, i dati mantengono disponibilità elevata in caso di interruzioni impreviste.

-  Livello di sicurezza medio. Tutti i dati scritti in un account di archiviazione di Azure vengono crittografati dal servizio. Archiviazione di Azure offre un controllo dettagliato su chi potrà accedere ai dati.

-  Scalabile. La soluzione Archiviazione di Azure è progettata per offrire scalabilità elevata in modo da soddisfare le esigenze di archiviazione dati e di prestazioni delle attuali applicazioni.

-  Gestito. Azure gestisce automaticamente la manutenzione dell'hardware, gli aggiornamenti e i problemi critici.

-  Accessibilità. I dati in Archiviazione di Azure sono accessibili da ogni parte del mondo tramite HTTP o HTTPS. Microsoft fornisce librerie client per archiviazione di Azure in un'ampia gamma di linguaggi, tra cui .NET, Java, Node.js, Python, PHP, Ruby, go e altri ancora, oltre a un'API REST matura. Archiviazione di Azure supporta l'esecuzione di script in Azure PowerShell o nell'interfaccia della riga di comando di Azure. Il portale di Azure e Azure Storage Explorer, inoltre, offrono semplici soluzioni visive per l'uso dei dati.

La piattaforma di archiviazione di Azure include i servizi dati seguenti, idonei per l'utilizzo come supporti di backup per il database Oracle:

-  BLOB di Azure: archivio oggetti a scalabilità elevata per dati di testo e binari. Include anche il supporto per Big Data Analytics tramite Data Lake Storage Gen2.

-  File di Azure: condivisioni file gestite per distribuzioni cloud o locali.

-  Dischi di Azure: volumi di archiviazione a livello di blocco per le macchine virtuali di Azure.

### <a name="cross-regional-storage-mounting"></a>Montaggio tra più aree geografiche

La possibilità di accedere all'archivio di backup tra le aree è un aspetto importante di BCDR e utile per la clonazione dei database da backup in aree geografiche diverse. Archiviazione cloud di Azure fornisce cinque livelli di [ridondanza](../../../storage/common/storage-redundancy.md)
- Archiviazione con ridondanza locale [(con ridondanza locale)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) in cui i dati vengono replicati tre volte all'interno di una singola posizione fisica nell'area primaria.  
- Archiviazione con ridondanza della zona [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) in cui i dati sono protetti da con ridondanza locale nell'area primaria e replicati in modo sincrono in tre zone di disponibilità (AZ) nell'area primaria. Ogni AZ è con ridondanza locale protected. 
- Archiviazione Geo-Redundant [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) in cui i dati sono protetti da con ridondanza locale nell'area primaria e replicati in modo asincrono in un'area secondaria in cui è anche protetto da con ridondanza locale.
- Archiviazione con ridondanza geografica [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) copia i dati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria usando ZRS. Copia quindi i dati in modo asincrono in un'unica posizione fisica nell'area secondaria. In tutti i percorsi i dati sono protetti anche da con ridondanza locale. 
- L'archiviazione con ridondanza geografica e accesso in lettura Geo-Redundant archiviazione [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) e con ridondanza geografica e accesso in lettura [(RA-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) consente l'accesso in sola lettura ai dati replicati nell'area secondaria in qualsiasi momento. 

#### <a name="blob-storage-and-file-storage"></a>Archiviazione BLOB e archiviazione file

Quando si usa File di Azure con i protocolli SMB o NFS 4,1 (anteprima) per il montaggio come archivio di backup, si noti che File di Azure non supporta l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e l'archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS). 

Inoltre, se il requisito di archiviazione di backup è maggiore di 5 TiB File di Azure richiede la [funzionalità di condivisioni file di grandi dimensioni](../../../storage/files/storage-files-planning.md) abilitata che non supporta la ridondanza GRS o GZRS, è supportato solo con ridondanza locale. 

Il protocollo BLOB di Azure montato con NFS 3,0 (anteprima) supporta attualmente solo la ridondanza con ridondanza locale e ZRS.  

Il BLOB di Azure configurato con qualsiasi opzione di ridondanza può essere montato usando Blobfuse.

#### <a name="recovery-services-vault"></a>Insieme di credenziali dei servizi di ripristino 

Un insieme di credenziali di Servizi di ripristino è un'entità di gestione che archivia i punti di ripristino creati nel tempo e fornisce un'interfaccia per l'esecuzione di operazioni correlate al backup, come l'esecuzione di backup su richiesta, l'esecuzione di ripristini e la creazione di criteri di backup.
Backup di Azure gestisce automaticamente lo spazio di archiviazione per l'insieme di credenziali. È necessario specificare la modalità di replica dell'archiviazione al momento della creazione. Non può essere modificato dopo che gli elementi sono protetti nell'insieme di credenziali. Per la ridondanza a livello di area, scegliere l'impostazione con ridondanza geografica.

Se si intende eseguire il ripristino in un' [area associata di Azure](../../../best-practices-availability-paired-regions.md) secondaria, abilitare la funzionalità di [ripristino tra aree](../../../backup/backup-create-rs-vault.md) . Quando il ripristino tra aree è abilitato, l'archivio di backup viene spostato da GRS a archiviazione con ridondanza geografica e accesso in lettura (RA-GRS). 

### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

Archiviazione BLOB di Azure è un servizio di archiviazione a costo elevato, durevole e protetto basato sul cloud usato per archiviare grandi quantità di dati non strutturati ed è un supporto ideale da usare per i backup Oracle Database. L'archiviazione BLOB di Azure può essere montata nelle VM Linux di Azure usando il protocollo NFS v 3.0 o Blobfuse (fusibile Linux).

#### <a name="azure-blob-blobfuse"></a>Blobfuse BLOB di Azure

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) è un progetto open source sviluppato per fornire un filesystem virtuale supportato dall'archiviazione BLOB di Azure. Usa la libreria open source libfuse per comunicare con il modulo del kernel FUSE Linux e implementa le operazioni del file System usando le API REST di BLOB del servizio di archiviazione di Azure. Blobfuse è attualmente disponibile per le distribuzioni Ubuntu e CentOS/RedHat. È anche disponibile per Kubernetes con il [driver CSI](https://github.com/kubernetes-sigs/blob-csi-driver). 

Sebbene sia onnipresente in tutte le aree di Azure e funzioni con tutti i tipi di account di archiviazione, inclusi gli scopi generali V1/V2 e Azure Data Lake Store Gen2, le prestazioni offerte da Blobfuse sono risultate inferiori rispetto ai protocolli alternativi, ad esempio SMB o NFS. Per l'idoneità come supporto di backup del database, è consigliabile usare i protocolli SMB o [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) per montare l'archiviazione BLOB di Azure. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure BLOB NFS v 3.0 (anteprima)

Il supporto di Azure per il protocollo NFS (Network File System) v 3.0 è ora disponibile in anteprima. Il supporto per [NFS](../../../storage/blobs/network-file-system-protocol-support.md) consente ai client Windows e Linux di montare un contenitore di archiviazione BLOB in una macchina virtuale di Azure. 

L'anteprima pubblica di NFS 3,0 supporta gli account di archiviazione GPV2 con prestazioni del livello standard nelle aree seguenti: 
- Australia orientale
- Corea centrale
- Stati Uniti centro-meridionali. 

Per garantire la sicurezza di rete, l'account di archiviazione usato per il montaggio NFS deve essere contenuto all'interno di un VNet. La sicurezza Azure Active Directory (AD) e gli elenchi di controllo di accesso (ACL) non sono ancora supportati negli account in cui è abilitato il supporto del protocollo NFS 3,0.

### <a name="azure-files"></a>File di Azure

[File di Azure](../../../storage/files/storage-files-introduction.md) è un file System distribuito completamente gestito e basato sul cloud che può essere montato in client Windows, Linux o MacOS locali o basati sul cloud.

File di Azure offre condivisioni file multipiattaforma completamente gestite nel cloud accessibili tramite il protocollo SMB (Server Message Block) e il protocollo NFS (Network File System) (anteprima). File di Azure attualmente non supporta l'accesso a più protocolli, una condivisione può essere solo una condivisione NFS o una condivisione SMB. È consigliabile determinare il protocollo più adatto alle proprie esigenze prima di creare condivisioni file di Azure.

Le condivisioni file di Azure possono anche essere protette tramite backup di Azure nell'insieme di credenziali di servizi di ripristino, offrendo un ulteriore livello di protezione ai backup di Oracle RMAN.

#### <a name="azure-files-nfs-v41-preview"></a>File di Azure NFS v 4.1 (anteprima)

Le condivisioni file di Azure possono essere montate nelle distribuzioni di Linux usando il protocollo NFS (Network File System) v 4.1. In anteprima esistono diverse limitazioni per le funzionalità supportate, descritte [qui](../../../storage/files/storage-files-how-to-mount-nfs-shares.md). 

Anche se in anteprima File di Azure NFS v 4.1 è limitato alle [aree](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)seguenti:
- Stati Uniti orientali (con ridondanza locale e ZRS)
- Stati Uniti orientali 2
- Stati Uniti occidentali 2
- Europa occidentale
- Asia sud-orientale
- Regno Unito meridionale
- Australia orientale

#### <a name="azure-files-smb-30"></a>File di Azure SMB 3,0

Le condivisioni file di Azure possono essere montate nelle distribuzioni Linux usando il client del kernel SMB. Il protocollo CIFS (Common Internet file System), disponibile nelle distribuzioni Linux, è un sottolinguaggio SMB. Quando si monta File di Azure SMB nelle VM Linux, questo viene montato come tipo di file System CIFS ed è necessario installare il pacchetto CIFS. 

File di Azure SMB è disponibile a livello generale in tutte le aree di Azure e Mostra le stesse caratteristiche di prestazioni dei protocolli NFS v 3.0 e v 4.1, quindi è attualmente il metodo consigliato per fornire supporti di archiviazione di backup alle macchine virtuali Linux di Azure.  

Sono disponibili due versioni supportate di SMB, SMB 2,1 e SMB 3,0, con la seconda opzione consigliata, in quanto supporta la crittografia in transito. Tuttavia, diverse versioni di kernel Linux hanno un supporto diverso per SMB 2,1 e 3,0 ed è necessario controllare la tabella [qui](../../../storage/files/storage-how-to-use-files-linux.md) per assicurarsi che l'applicazione supporti SMB 3,0. 

Poiché File di Azure è progettato per essere un servizio di condivisione file multiutente, è necessario sintonizzare alcune caratteristiche per renderlo più adatto come supporto di archiviazione di backup. È consigliabile disattivare la memorizzazione nella cache e impostare gli ID utente e gruppo per i file creati.

## <a name="azure-netapp-files"></a>Azure NetApp Files

Il servizio [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) è una soluzione di archiviazione completa per i database Oracle in macchine virtuali di Azure. Basato su un archivio di file a consumo e a prestazioni elevate di livello aziendale, supporta qualsiasi tipo di carico di lavoro ed è altamente disponibile per impostazione predefinita. Insieme al driver Oracle Direct NFS (dNFS), Azure NetApp Files fornisce un livello di archiviazione altamente ottimizzato per l'Oracle Database.

Azure NetApp Files fornisce copie snapshot basate su archiviazione efficienti nel sistema di archiviazione sottostante che usa un meccanismo di reindirizzamento in scrittura (riga). Anche se le copie snapshot sono estremamente veloci da eseguire e ripristinare, servono solo come prima linea di difesa, che può considerare la maggior parte delle operazioni di ripristino necessarie di qualsiasi organizzazione, che è spesso il recupero da errori umani. Tuttavia, le copie snapshot non sono un backup completo. Per coprire tutti i requisiti di backup e ripristino, è necessario creare le repliche snapshot esterne e/o altre copie di backup in una geografia remota per proteggersi da un'interruzione a livello di area. Per altre informazioni sull'uso di file NetApp per database Oracle in Azure, leggere questo [report](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf).

## <a name="azure-backup-service"></a>Servizio Backup di Azure

[Backup di Azure](../../../backup/backup-overview.md) è un PaaS completamente gestito che offre soluzioni semplici, sicure ed economicamente convenienti per eseguire il backup dei dati e recuperarli dal cloud Microsoft Azure. Backup di Azure consente di eseguire il backup e il ripristino dei client locali, delle macchine virtuali di Azure, delle condivisioni di File di Azure, nonché dei database di SQL Server, Oracle, MySQL, PostgreSQL e SAP HANA nelle VM di Azure. 

Backup di Azure fornisce backup indipendenti e isolati per salvaguardare dalla distruzione accidentale dei dati originali. I backup vengono archiviati in un insieme di credenziali di [servizi di ripristino](../../../backup/backup-azure-recovery-services-vault-overview.md) con gestione incorporata dei punti di ripristino. La configurazione e la scalabilità sono semplici, i backup sono ottimizzati ed è possibile eseguire il ripristino con facilità secondo necessità. Usa la potenza sottostante e la scalabilità illimitata del cloud di Azure per offrire disponibilità elevata senza sovraccarico di manutenzione o di monitoraggio. Backup di Azure non limita la quantità di dati in ingresso o in uscita trasferiti o i costi per i dati trasferiti e i dati vengono protetti in transito e inattivi. 

Per garantire la durabilità, backup di Azure offre più tipi di replica per mantenere i dati di backup a disponibilità elevata.

- Archiviazione con ridondanza locale [(con ridondanza locale)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) replica i dati tre volte (crea tre copie dei dati) in un'unità di scala di archiviazione in un Data Center.
- L'archiviazione con ridondanza geografica [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) è l'opzione di replica predefinita e consigliata. L'archiviazione con ridondanza geografica replica i dati in un'area secondaria a centinaia di chilometri di distanza dalla posizione primaria dei dati di origine.

Un insieme di credenziali creato con ridondanza GRS include l'opzione per configurare la funzionalità di [ripristino tra aree](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) , che consente di ripristinare i dati in un'area associata di Azure secondaria.

Il servizio backup di Azure fornisce un [Framework](../../../backup/backup-azure-linux-app-consistent.md) per ottenere la coerenza delle applicazioni durante i backup di macchine virtuali Windows e Linux per varie applicazioni come Oracle, MySQL, Mongo DB, SAP HANA e PostgreSQL, denominate snapshot coerenti con l'applicazione. Ciò implica la chiamata di uno script di pre-elaborazione (per mettere in stato le applicazioni) prima di creare uno snapshot dei dischi e la chiamata di post-script (comandi per sbloccare le applicazioni) dopo il completamento dello snapshot, per ripristinare le applicazioni in modalità normale. Sebbene i pre-script di esempio e i post-script siano disponibili su GitHub, la creazione e la manutenzione di questi script è responsabilità dell'utente. Nel caso di Oracle, il database deve essere in modalità ARCHIVELOG per consentire i backup online e i comandi di inizio e fine del database appropriati vengono eseguiti negli script pre e post-backup, che è necessario creare e gestire. 

Backup di Azure offre ora un [Framework avanzato di pre-backup e post-](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts)backup, attualmente in anteprima, in cui il servizio backup di Azure fornirà pre-script e post-script in pacchetto per le applicazioni selezionate. Gli utenti di backup di Azure devono solo assegnare un nome all'applicazione e quindi il backup delle macchine virtuali di Azure richiamerà automaticamente gli script e i post-script pertinenti. Gli script e i post-script in pacchetto verranno gestiti dal team di backup di Azure, in modo che gli utenti possano essere certi del supporto, della proprietà e della validità di tali script. Attualmente, le applicazioni supportate per il Framework migliorato sono Oracle e MySQL, con più tipi di applicazione previsti in futuro. Lo snapshot sarà una copia completa dello spazio di archiviazione e non una copia incrementale o di copia nello snapshot di scrittura, pertanto si tratta di un supporto efficace per il ripristino del database da.

## <a name="next-steps"></a>Passaggi successivi

- [Creazione rapida di Oracle Database](oracle-database-quick-create.md)
- [Eseguire il backup di Oracle Database in File di Azure](oracle-database-backup-azure-storage.md)
- [Eseguire il backup di Oracle Database con il servizio backup di Azure](oracle-database-backup-azure-backup.md)


