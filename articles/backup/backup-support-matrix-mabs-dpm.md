---
title: Matrice di supporto di MAB & System Center DPM
description: Questo articolo riepiloga il supporto di backup di Azure quando si usa Backup di Microsoft Azure Server (MAB) o System Center DPM per eseguire il backup delle risorse locali e delle macchine virtuali di Azure.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: e888b43ea5641f1943a096f045747d547c52fcfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609754"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matrice di supporto per il backup con Backup di Microsoft Azure server o System Center DPM

È possibile usare il [servizio Backup di Azure](backup-overview.md) per eseguire il backup di computer e carichi di lavoro locali, nonché di macchine virtuali (VM) di Azure. Questo articolo riepiloga le impostazioni di supporto e le limitazioni per il backup dei computer con Backup di Microsoft Azure Server (MAB) o System Center Data Protection Manager (DPM) e backup di Azure.

## <a name="about-dpmmabs"></a>Informazioni su DPM/MAB

[System Center DPM](/system-center/dpm/dpm-overview) è una soluzione aziendale che consente di configurare, semplificare e gestire il backup e il ripristino di dati e computer aziendali. e fa parte della famiglia di prodotti [System Center](https://www.microsoft.com/system-center/pricing).

MAB è un prodotto server che può essere usato per eseguire il backup di server fisici, macchine virtuali e app locali in esecuzione su di essi.

MAB si basa su System Center DPM e fornisce funzionalità simili con alcune differenze:

- Per eseguire il server di Backup di Microsoft Azure non è necessaria alcuna licenza System Center.
- Per gli oggetti MAB e DPM, Azure offre un'archiviazione di backup a lungo termine. DPM inoltre consente di eseguire il backup dei dati per l'archiviazione a lungo termine su nastro. Questa funzionalità non è disponibile con il server di Backup di Microsoft Azure.
- [È possibile eseguire il backup di un server DPM primario con un server DPM secondario](/system-center/dpm/back-up-the-dpm-server). Il server secondario proteggerà il database del server primario e le repliche delle origini dati archiviate sul server primario. In caso di guasto del server primario, il server secondario può continuare a proteggere i carichi di lavoro protetti dal server primario, finché il server primario non sarà nuovamente disponibile.  Questa funzionalità non è disponibile con il server di Backup di Microsoft Azure.

È possibile scaricare MAB dall' [area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57520). Può essere eseguito in locale o in una macchina virtuale di Azure.

DPM e il server di Backup di Microsoft Azure supportano il backup di una vasta gamma di app e di sistemi operativi server e client. Essi offrono diversi scenari di backup:

- È possibile eseguire il backup a livello di computer con un backup dello stato del sistema o un backup bare metal.
- È possibile eseguire il backup di volumi, condivisioni, cartelle e file specifici.
- È possibile eseguire il backup di app specifiche usando impostazioni ottimizzate compatibili con le app.

## <a name="dpmmabs-backup"></a>Backup di DPM/MAB

Il backup con DPM/MAB e backup di Azure funziona nel modo seguente:

1. L'agente protezione DPM/MAB è installato in ogni computer di cui verrà eseguito il backup.
1. Il backup di macchine virtuali e app viene eseguito nella risorsa di archiviazione locale in DPM/MAB.
1. L'agente di Servizi di ripristino di Microsoft Azure viene installato nel server DPM o nel server di Backup di Microsoft Azure.
1. L'agente MARS esegue il backup dei dischi DPM/MAB in un insieme di credenziali di backup di servizi di ripristino in Azure tramite backup di Azure.

Per altre informazioni:

- [Vedere altre informazioni](backup-architecture.md#architecture-back-up-to-dpmmabs) sull'architettura del server di Backup di Microsoft Azure.
- [Esaminare gli elementi supportati](backup-support-matrix-mars-agent.md) per l'agente Mars.

## <a name="supported-scenarios"></a>Scenari supportati

**Scenario** | **Agent** | **Posizione**
--- | --- | ---
**Backup di carichi di lavoro e computer locali** | L'agente protezione DPM/MAB viene eseguito nei computer di cui si vuole eseguire il backup.<br/><br/> Agente MARS nel server DPM/MAB.<br/> La versione minima dell'agente di Servizi di ripristino di Microsoft Azure o dell'agente di Backup di Azure richiesta per l'abilitazione di questa funzionalità è la 2.0.8719.0.  | DPM/MAB deve essere eseguito in locale.

## <a name="supported-deployments"></a>Distribuzioni supportate

DPM/MAB può essere distribuito come riepilogato nella tabella seguente.

**Distribuzione** | **Supporto** | **Dettagli**
--- | --- | ---
**Distribuito in locale** | Server fisico, ma non in un cluster fisico.<br/><br/>Macchina virtuale Hyper-V. È possibile distribuire MAB come macchina guest in un hypervisor o un cluster autonomo. Non può essere distribuita in un nodo di un cluster o di un hypervisor autonomo. Il server di Backup di Azure è progettato per l'esecuzione su un server dedicato a un solo scopo.<br/><br/> Come macchina virtuale Windows in un ambiente VMware. | I server MAB locali non sono in grado di proteggere i carichi di lavoro basati su Azure. <br><br> Per altre informazioni, vedere [matrice di protezione](backup-mabs-protection-matrix.md).
**Distribuito come macchina virtuale di Azure Stack** | Solo server di Backup di Microsoft Azure | Non è possibile usare DPM per eseguire il backup di macchine virtuali di Azure Stack.
**Distribuito come macchina virtuale di Azure** | Protegge le macchine virtuali e i carichi di lavoro di Azure in esecuzione in tali macchine virtuali | DPM/MAB in esecuzione in Azure non può eseguire il backup dei computer locali. Può proteggere solo i carichi di lavoro in esecuzione nelle macchine virtuali IaaS di Azure.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemi operativi supportati per il server di Backup di Microsoft Azure e DPM

Backup di Azure può eseguire il backup di istanze di DPM/MAB che eseguono uno dei sistemi operativi seguenti. Devono essere in uso gli ultimi Service Pack e aggiornamenti dei sistemi operativi.

**Scenario** | **DPM/MAB**
--- | ---
**Server di Backup di Microsoft Azure in una macchina virtuale di Azure** |  Windows 2016 datacenter.<br/><br/> Windows 2019 datacenter.<br/><br/> Si consiglia di iniziare con un'immagine del Marketplace.<br/><br/> Standard_A4_v2 minimo con quattro core e 8 GB di RAM.
**DPM in una macchina virtuale di Azure** | System Center 2012 R2 con Update 3 o versioni successive.<br/><br/> Sistema operativo Windows [richiesto da System Center](/system-center/dpm/prepare-environment-for-dpm#dpm-server).<br/><br/> Si consiglia di iniziare con un'immagine del Marketplace.<br/><br/> Standard_A4_v2 minimo con quattro core e 8 GB di RAM.
**Server di Backup di Microsoft Azure in locale** |  MAB V3 e versioni successive: Windows Server 2016 o Windows Server 2019
**DPM in locale** | Server fisico/macchina virtuale Hyper-V: System Center 2012 SP1 o versione successiva.<br/><br/> VM VMware: System Center 2012 R2 con aggiornamento 5 o versione successiva.

>[!NOTE]
>L'installazione di server di Backup di Azure non è supportata in Windows Server Core o Microsoft Hyper-V Server.

## <a name="management-support"></a>Supporto della gestione

**Problema** | **Dettagli**
--- | ---
**Installazione** | Installare DPM/MAB in un computer con un solo scopo.<br/><br/> Non installare DPM/MAB in un controller di dominio, in un computer con l'installazione del ruolo server applicazioni, in un computer in cui è in esecuzione Microsoft Exchange Server o System Center Operations Manager o in un nodo del cluster.<br/><br/> [Esaminare tutti i requisiti di sistema di DPM](/system-center/dpm/prepare-environment-for-dpm#dpm-server).
**Dominio** | DPM/MAB deve essere aggiunto a un dominio. Installare e quindi aggiungere DPM o il server di Backup di Microsoft Azure a un dominio. Lo spostamento di DPM o del server di Backup di Microsoft Azure in un nuovo dominio dopo la distribuzione non è supportato.
**Archiviazione** | Modern backup storage (MBS) è supportato da DPM 2016/MAB V2 e versioni successive. Non è disponibile per il server di Backup di Microsoft Azure v1.
**Aggiornamento del server di Backup di Microsoft Azure** | È possibile installare direttamente il server di Backup di Microsoft Azure v3 oppure eseguire l'aggiornamento dal server di Backup di Microsoft Azure v2 al server di Backup di Microsoft Azure v3. [Altre informazioni](backup-azure-microsoft-azure-backup.md#upgrade-mabs)
**Spostamento del server di Backup di Microsoft Azure** | Se si usa MBS, è possibile spostare il server di Backup di Microsoft Azure in un nuovo server conservando l'archivio.<br/><br/> Il server deve avere lo stesso nome dell'originale. Non è possibile cambiare il nome se si intende mantenere lo stesso pool di archiviazione e usare lo stesso database del server di Backup di Microsoft Azure per archiviare i punti di ripristino dei dati.<br/><br/> È necessario un backup del database MAB perché sarà necessario ripristinarlo.

>[!NOTE]
>La ridenominazione del server DPM/MAB non è supportata.

## <a name="mabs-support-on-azure-stack"></a>Supporto per il server di Backup di Microsoft Azure in Azure Stack

Se si distribuisce il server di Backup di Microsoft Azure in una macchina virtuale di Azure Stack, sarà possibile gestire il backup delle macchine virtuali di Azure Stack e dei carichi di lavoro da un'unica posizione.

**Componente** | **Dettagli**
--- | ---
**Server di Backup di Microsoft Azure in una macchina virtuale di Azure Stack** | Almeno la dimensione a2. È consigliabile iniziare con un'immagine di Windows Server 2012 R2 o Windows Server 2016 da Azure Marketplace.<br/><br/> Non installare altri oggetti nella VM di MAB.
**Archiviazione del server di Backup di Microsoft Azure** | Usare un account di archiviazione separato per la macchina virtuale di MAB. Per l'agente MARS eseguito su MAB è necessaria l'archiviazione temporanea per un percorso della cache e per conservare i dati ripristinati dal cloud.
**Pool di archiviazione del server di Backup di Microsoft Azure** | Le dimensioni del pool di archiviazione MAB sono determinate dal numero e dalle dimensioni dei dischi collegati alla macchina virtuale di MAB. Per ogni dimensione di macchina virtuale di Azure Stack è previsto un numero massimo di dischi. Ad esempio, per A2 il numero massimo è quattro dischi.
**Conservazione del server di Backup di Microsoft Azure** | Non mantenere i dati di cui è stato eseguito il backup nei dischi MAB locali per più di cinque giorni.
**Aumento delle prestazioni del server di Backup di Microsoft Azure** | Per aumentare le prestazioni della distribuzione, è possibile aumentare le dimensioni della macchina virtuale del server di Backup di Microsoft Azure. Ad esempio, è possibile passare dalla serie a alla serie D.<br/><br/> È anche possibile assicurarsi che si stia eseguendo regolarmente il offload dei dati con backup in Azure. Se necessario, è possibile distribuire server MAB aggiuntivi.
**.NET Framework nel server di Backup di Microsoft Azure** | Per la macchina virtuale MAB è necessario .NET Framework 3,3 SP1 o versione successiva.
**Dominio del server di Backup di Microsoft Azure** | La macchina virtuale del server di Backup di Microsoft Azure deve essere aggiunta a un dominio. Un utente del dominio con privilegi di amministratore deve installare il server di Backup di Microsoft Azure nella macchina virtuale.
**Backup dei dati di macchine virtuali di Azure Stack** | È possibile eseguire il backup di file, cartelle e app.
**Backup supportato** | Questi sistemi operativi sono supportati per le macchine virtuali di cui si vuole eseguire il backup:<br/><br/> Canale Semi-Annual di Windows Server (Datacenter, Enterprise, standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Supporto SQL Server per le macchine virtuali Azure Stack** | Eseguire il backup di SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Eseguire il backup e il ripristino di un database.
**Supporto SharePoint per macchine virtuali di Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Eseguire il backup e il ripristino di una farm, un database, un front-end e un server Web.
**Requisiti di rete per macchine virtuali sottoposte a backup** | Tutte le macchine virtuali nel carico di lavoro Azure Stack devono appartenere alla stessa rete virtuale e appartenere alla stessa sottoscrizione.

## <a name="dpmmabs-networking-support"></a>Supporto delle funzionalità di rete per DPM o il server di Backup di Microsoft Azure

### <a name="url-access"></a>accesso con URL

Il server DPM/server MAB necessita dell'accesso a questi URL e indirizzi IP:

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Indirizzi IP
  * 20.190.128.0/18
  * 40.126.0.0/18:

### <a name="azure-expressroute-support"></a>Supporto di Azure ExpressRoute

È possibile eseguire il backup dei dati tramite Azure ExpressRoute con il peering pubblico (disponibile per i circuiti precedenti) e il peering Microsoft. Il backup sul peering privato non è supportato.

Con peering pubblico: garantire l'accesso ai seguenti domini/indirizzi:

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Indirizzi IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Con il peering Microsoft selezionare i servizi/le aree e i valori della community pertinenti seguenti:

- Azure Active Directory (12076:5060)
- Area Microsoft Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)
- Archiviazione di Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)

Per ulteriori informazioni, vedere i [requisiti di routing di ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>Il peering pubblico è deprecato per i nuovi circuiti.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Connettività di DPM o del server di Backup di Microsoft Azure al servizio Backup di Azure

Per il corretto funzionamento dei backup, è necessaria la connettività al servizio Backup di Azure e la sottoscrizione di Azure deve essere attiva. La tabella seguente illustra il comportamento che si riscontra quando queste due condizioni non si verificano.

**Server di Backup di Microsoft Azure ad Azure** | **Sottoscrizione** | **Backup/ripristino**
--- | --- | ---
Connesso | Attivo | Eseguire il backup nel disco di DPM/MAB.<br/><br/> Esegui il backup in Azure.<br/><br/> Ripristinare dal disco.<br/><br/> Ripristinare da Azure.
Connesso | Scaduta/sottoposta a deprovisioning | Nessun backup su disco o in Azure.<br/><br/> Se la sottoscrizione è scaduta, è possibile eseguire il ripristino dal disco o da Azure.<br/><br/> Se la sottoscrizione viene ritirata, non è possibile eseguire il ripristino dal disco o da Azure. I punti di ripristino di Azure vengono eliminati.
Nessuna connettività per più di 15 giorni | Attivo | Nessun backup su disco o in Azure.<br/><br/> È possibile eseguire il ripristino dal disco o da Azure.
Nessuna connettività per più di 15 giorni | Scaduta/sottoposta a deprovisioning | Nessun backup su disco o in Azure.<br/><br/> Se la sottoscrizione è scaduta, è possibile eseguire il ripristino dal disco o da Azure.<br/><br/> Se la sottoscrizione viene ritirata, non è possibile eseguire il ripristino dal disco o da Azure. I punti di ripristino di Azure vengono eliminati.

## <a name="domain-and-domain-trusts-support"></a>Supporto di trust di dominio e dominio

|Requisito |Dettagli |
|---------|---------|
|Dominio    | Il server DPM/MAB deve trovarsi in un dominio Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012.        |
|Trust tra domini   |  DPM/MAB supporta la protezione dei dati tra foreste, a condizione che si stabilisca una relazione di trust bidirezionale a livello di foresta tra le foreste separate.   <BR><BR>   DPM/MAB può proteggere i server e le workstation tra domini, all'interno di una foresta che abbia una relazione di trust bidirezionale con il dominio del server DPM/MAB. Per proteggere i computer in gruppi di lavoro o domini non trusted, vedere [eseguire il backup e il ripristino dei carichi di lavoro in gruppi di lavoro e domini non trusted.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains) <br><br> Per eseguire il backup di cluster di server Hyper-V, è necessario che si trovino nello stesso dominio del server MAB o in un dominio trusted o figlio. È possibile eseguire il backup di server e cluster in un dominio non trusted o in un carico di lavoro usando l'autenticazione NTLM o del certificato per un singolo server o l'autenticazione del certificato solo per un cluster.  |

## <a name="dpmmabs-storage-support"></a>Supporto dell'archiviazione per DPM o il server di Backup di Microsoft Azure

I dati di cui viene eseguito il backup in DPM/MAB vengono archiviati nell'archiviazione su disco locale.

Le unità USB o rimovibili non sono supportate.

La compressione NTFS non è supportata nei volumi DPM/MAB.

BitLocker può essere abilitato solo dopo aver aggiunto il disco al pool di archiviazione. Non abilitare BitLocker prima di aggiungerlo.

Archiviazione collegata alla rete (NAS) non è supportata per l'uso nel pool di archiviazione DPM.

**Archiviazione** | **Dettagli**
--- | ---
**MBS** | Modern backup storage (MBS) è supportato da DPM 2016/MAB V2 e versioni successive. Non è disponibile per il server di Backup di Microsoft Azure v1.
**Archiviazione del server di Backup di Microsoft Azure in una macchina virtuale di Azure** | I dati vengono archiviati nei dischi di Azure collegati alla macchina virtuale DPM/MAB e gestiti in DPM/MAB. Il numero di dischi che è possibile usare per il pool di archiviazione DPM/MAB è limitato dalle dimensioni della macchina virtuale.<br/><br/> VM a2:4 dischi; VM A3:8 dischi; VM A4:16 dischi, con una dimensione massima di 1 TB per ogni disco. Ciò determina il pool di archiviazione di backup totale disponibile.<br/><br/> La quantità di dati di cui è possibile eseguire il backup dipende dal numero e dalle dimensioni dei dischi collegati.
**Conservazione dei dati del server di Backup di Microsoft Azure in una macchina virtuale di Azure** | Si consiglia di conservare i dati per un giorno nel disco di Azure DPM/MAB ed eseguire il backup da DPM/MAB nell'insieme di credenziali per un periodo di conservazione più lungo. In questo modo è possibile proteggere una quantità maggiore di dati tramite l'offload in backup di Azure.

### <a name="modern-backup-storage-mbs"></a>Modern Backup Storage (MBS)

Da DPM 2016/MAB V2 (in esecuzione su Windows Server 2016) e versioni successive, è possibile sfruttare i vantaggi di Modern backup storage (MBS).

- I backup di MBS vengono archiviati in un disco ReFS (Resilient File System).
- MBS usa la clonazione dei blocchi ReFS per un backup più rapido e un uso più efficiente dello spazio di archiviazione.
- Quando si aggiungono volumi al pool di archiviazione DPM/MAB locale, questi vengono configurati con lettere di unità. È quindi possibile configurare l'archiviazione dei carichi di lavoro in volumi diversi.
- Quando si creano gruppi protezione dati per eseguire il backup dei dati in DPM o nel server di Backup di Microsoft Azure, si seleziona l'unità che si intende usare. Ad esempio, è possibile archiviare i backup per SQL o altri carichi di lavoro di IOPS elevati in un'unità a prestazioni elevate e archiviare i carichi di lavoro di cui è stato eseguito il backup con minore frequenza in un'unità di prestazioni inferiore.

## <a name="supported-backups-to-mabs"></a>Backup nel server di Backup di Microsoft Azure supportati

Per informazioni sui vari server e carichi di lavoro che è possibile proteggere con server di Backup di Azure, vedere la [matrice di protezione server di backup di Azure](./backup-mabs-protection-matrix.md#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Backup in DPM supportati

Per informazioni sui vari server e carichi di lavoro che è possibile proteggere con Data Protection Manager, vedere l'articolo su [cosa è possibile eseguire il backup di DPM](/system-center/dpm/dpm-protection-matrix).

- I carichi di lavoro del cluster sottoposti a backup da DPM/MAB devono trovarsi nello stesso dominio di DPM/MAB o in un dominio figlio o trusted.
- È possibile usare l'autenticazione NTLM/del certificato per eseguire il backup dei dati in gruppi di lavoro o domini non attendibili.

## <a name="deduplicated-volumes-support"></a>Supporto di volumi deduplicati

>[!NOTE]
> Il supporto della deduplicazione per MAB dipende dal supporto del sistema operativo.

### <a name="for-ntfs-volumes"></a>Per i volumi NTFS

| Sistema operativo del server protetto  | Sistema operativo del server MAB  | Versione di MAB  | Deduplicazione supportata |
| ------------------------------------------ | ------------------------------------- | ------------------ | -------------------- |
| Windows Server 2019                       | Windows Server 2019                  | Server di Backup di Microsoft Azure v3            | S                    |
| Windows Server 2016                       | Windows Server 2019                  | Server di Backup di Microsoft Azure v3            | Y*                   |
| Windows Server 2012 R2                    | Windows Server 2019                  | Server di Backup di Microsoft Azure v3            | N                    |
| Windows Server 2012                       | Windows Server 2019                  | Server di Backup di Microsoft Azure v3            | N                    |
| Windows Server 2019                       | Windows Server 2016                  | Server di Backup di Microsoft Azure v3            | Y * *                  |
| Windows Server 2016                       | Windows Server 2016                  | Server di Backup di Microsoft Azure v3            | S                    |
| Windows Server 2012 R2                    | Windows Server 2016                  | Server di Backup di Microsoft Azure v3            | S                    |
| Windows Server 2012                       | Windows Server 2016                  | Server di Backup di Microsoft Azure v3            | S                    |

- \* Quando si protegge un volume di WS 2016 NTFS deduplicato con MAB V3 in esecuzione su WS 2019, è possibile che i recuperi siano interessati. È stata rilevata una correzione per eseguire i recuperi in modo non duplicato. Rivolgersi al supporto di MAB se è necessaria questa correzione in MAB V3 UR1.
- \** Quando si protegge un volume di WS 2019 NTFS deduplicato con MAB V3 in WS 2016, i backup e i ripristini non saranno deduplicati. Ciò significa che i backup utilizzeranno più spazio sul server MAB rispetto al volume NTFS originale deduplicato.

**Problema**: se si aggiorna il sistema operativo server protetto da windows server 2016 a windows server 2019, il backup del volume NTFS deduplicato verrà influenzato a causa delle modifiche apportate alla logica di deduplicazione.

**Soluzione temporanea**: contattare il supporto di MAB nel caso in cui sia necessaria questa correzione per la UR1 di MAB V3.

### <a name="for-refs-volumes"></a>Per i volumi ReFS

>[!NOTE]
> Sono stati identificati alcuni problemi relativi ai backup di volumi ReFS deduplicati. Questi problemi verranno risolti a breve e questa sezione verrà aggiornata non appena sarà disponibile una correzione. Fino ad allora, viene rimosso il supporto per il backup dei volumi ReFS deduplicati da MAB V3.
>
> MAB V3 UR1 e versioni successive continua a supportare la protezione e il ripristino di volumi ReFS normali.

## <a name="next-steps"></a>Passaggi successivi

- [Vedere altre informazioni](backup-architecture.md#architecture-back-up-to-dpmmabs) sull'architettura del server di Backup di Microsoft Azure.
- [Rivedere](backup-support-matrix-mars-agent.md) gli scenari supportati per l'agente di Servizi di ripristino di Microsoft Azure.
- [Installare](backup-azure-microsoft-azure-backup.md) un server di Backup di Microsoft Azure.
- [Configurare DPM](/system-center/dpm/install-dpm).
