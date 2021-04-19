---
title: Panoramica dell'architettura
description: Panoramica dell'architettura, dei componenti e dei processi usati dal servizio Backup di Azure.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 8fca05f8718fc5e44da33b19447895f5daafc905
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716750"
---
# <a name="azure-backup-architecture-and-components"></a>Backup di Azure e componenti

È possibile usare il [Backup di Azure per](backup-overview.md) eseguire il backup dei dati nella Microsoft Azure cloud. Questo articolo riepiloga l'architettura, i componenti e i processi di Backup di Azure.

## <a name="what-does-azure-backup-do"></a>Che cosa fa Backup di Azure?

Backup di Azure backup dei dati, dello stato del computer e dei carichi di lavoro in esecuzione in computer locali e istanze di macchine virtuali di Azure. Esistono diversi scenari di Backup di Azure.

## <a name="how-does-azure-backup-work"></a>Come funziona Backup di Azure?

È possibile eseguire il backup di computer e dati usando diversi metodi:

- **Backup di computer locali**:
  - È possibile eseguire il backup di computer Windows locali direttamente in Azure usando l Backup di Azure Microsoft Azure di Servizi di ripristino di microsoft Azure. I computer Linux non sono supportati.
  - È possibile eseguire il backup di computer locali in un server di backup, System Center Data Protection Manager (DPM) o Backup di Microsoft Azure Server (MABS). È quindi possibile eseguire il backup del server di backup in un insieme di credenziali di Servizi di ripristino in Azure.

- **Eseguire il backup di macchine virtuali di Azure:**
  - È possibile eseguire il backup di macchine virtuali di Azure direttamente. Backup di Azure installa un'estensione di backup nell'agente di macchine virtuali di Azure in esecuzione nella macchina virtuale. Questa estensione esegue il backup dell'intera macchina virtuale.
  - È possibile eseguire il backup di cartelle e file specifici nella macchina virtuale di Azure eseguendo l'agente MARS.
  - È possibile eseguire il backup di macchine virtuali di Azure nel server di Backup di Microsoft Azure in esecuzione in Azure e quindi eseguire il backup del server di Backup di Microsoft Azure in un insieme di credenziali di Servizi di ripristino.

Altre informazioni su [cosa è possibile eseguire il backup e](backup-overview.md) sugli scenari di backup [supportati.](backup-support-matrix.md)

## <a name="where-is-data-backed-up"></a>Dove viene eseguito il backup dei dati?

Backup di Azure archivia i dati di cui è stato eseguito il backup in insiemi di credenziali: insiemi di credenziali di Servizi di ripristino e insiemi di credenziali di Backup. Un insieme di credenziali è un'entità di archiviazione online in Azure usata per contenere i dati, ad esempio copie di backup, punti di ripristino e criteri di backup.

Gli insiemi di credenziali hanno le funzionalità seguenti:

- Gli insiemi di credenziali semplificano l'organizzazione dei dati di backup, riducendo al minimo l'overhead di gestione.
- È possibile monitorare gli elementi di cui è stato eseguito il backup in un insieme di credenziali, incluse le macchine virtuali di Azure e i computer locali.
- È possibile gestire l'accesso all'insieme [di credenziali con il controllo degli accessi](../role-based-access-control/role-assignments-portal.md)in base al ruolo di Azure.
- È necessario specificare come vengono replicati i dati nell'insieme di credenziali per la ridondanza:
  - **Archiviazione con ridondanza locale:** per proteggersi da errori in un data center, è possibile usare l'archiviazione con ridondanza locale. L'archiviazione con ridondanza locale replica i dati in un'unità di scala di archiviazione. [Altre informazioni](../storage/common/storage-redundancy.md#locally-redundant-storage)
  - **Archiviazione con ridondanza geografica:** per proteggersi da interruzioni a livello di area, è possibile usare l'archiviazione con ridondanza geografica. L'grs replica i dati in un'area secondaria. [Altre informazioni](../storage/common/storage-redundancy.md#geo-redundant-storage)
  - **Archiviazione con ridondanza della** zona : replica i dati [nelle](../availability-zones/az-overview.md#availability-zones)zone di disponibilità, garantendo la residenza e la resilienza dei dati nella stessa area. [Scopri di più](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Per impostazione predefinita, gli insiemi di credenziali di Servizi di ripristino usano grs.

Gli insiemi di credenziali di Servizi di ripristino hanno le funzionalità aggiuntive seguenti:

- In ogni sottoscrizione di Azure è possibile creare fino a 500 insiemi di credenziali.

## <a name="backup-agents"></a>Agenti di backup

Backup di Azure fornisce agenti di backup diversi, a seconda del tipo di computer di cui viene eseguito il backup:

**Agent** | **Dettagli**
--- | ---
**Agente MARS** | <ul><li>Viene eseguito in singoli computer Windows Server locali per eseguire il backup di file, cartelle e stato del sistema.</li> <li>Viene eseguito nelle macchine virtuali di Azure per eseguire il backup di file, cartelle e stato del sistema.</li> <li>Viene eseguito nei server DPM/MABS per eseguire il backup del disco di archiviazione locale DPM/MABS in Azure.</li></ul>
**Estensione per le macchine virtuali di Azure** | Viene eseguito in macchine virtuali di Azure per eseguirne il backup in un insieme di credenziali.

## <a name="backup-types"></a>Tipi di backup

La tabella seguente illustra i diversi tipi di backup e quando vengono usati:

**Tipo di backup** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Completo** | Un backup completo contiene l'intera origine dati. Richiede più larghezza di banda di rete rispetto ai backup differenziali o incrementali. | Usato per il backup iniziale.
**Differenziale** |  Un backup differenziale archivia i blocchi modificati dopo il backup completo iniziale. Usa una quantità inferiore di rete e archiviazione e non mantiene copie ridondanti di dati non modificati.<br/><br/> Inefficiente perché i blocchi di dati non modificati tra i backup successivi vengono trasferiti e archiviati. | Non è usato da Backup di Azure.
**Incrementale** | Un backup incrementale archivia solo i blocchi di dati modificati dopo il backup precedente. Efficienza elevata per rete e archiviazione. <br/><br/> Con il backup incrementale non è necessario integrare i backup completi. | Usato da DPM/MABS per i backup su disco e usato in tutti i backup in Azure. Non usato per il SQL Server backup.

## <a name="sql-server-backup-types"></a>Tipi di backup di SQL Server

La tabella seguente illustra i diversi tipi di backup usati per SQL Server database e la frequenza con cui vengono usati:

**Tipo di backup** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Backup completo** | un backup completo è un backup eseguito per l'intero database. Contiene tutti i dati in un database specifico o in un set di filegroup o file. Un backup completo contiene anche un numero sufficiente di log per ripristinare i dati. | Al massimo, è possibile attivare un backup completo al giorno.<br/><br/> È possibile scegliere di eseguire un backup completo a intervalli giornalieri o settimanali.
**Backup differenziale** | Un backup differenziale si basa sul backup completo dei dati più recente e precedente.<br/><br/> Acquisisce solo i dati che sono stati modificati dopo il backup completo. |  Al massimo, è possibile attivare un backup differenziale al giorno.<br/><br/> Non è possibile configurare un backup completo e un backup differenziale nella stessa giornata.
**Backup del log delle transazioni** | un backup del log consente il ripristino temporizzato fino a uno specifico secondo. | Al massimo, è possibile configurare backup del log delle transazioni ogni 15 minuti.

### <a name="comparison-of-backup-types"></a>Confronto dei tipi di backup

L'utilizzo dell'archiviazione, l'obiettivo del tempo di ripristino (RTO) e l'utilizzo della rete variano per ogni tipo di backup. L'immagine seguente mostra un confronto tra i tipi di backup:

- L'origine dati A è costituita da 10 blocchi di archiviazione, A1-A10, di cui viene eseguito il backup mensile.
- I blocchi A2, A3, A4 e A9 cambiano nel primo mese, mentre il blocco A5 cambia il mese successivo.
- Per i backup differenziali, nel secondo mese viene eseguito il backup dei blocchi modificati A2, A3, A4 e A9. Nel terzo mese, viene eseguito nuovamente il backup di questi stessi blocchi, insieme al blocco A5 modificato. Il backup continua a essere eseguito per i blocchi modificati fino al backup completo successivo.
- Per i backup incrementali, nei blocchi del secondo mese A2, A3, A4 e A9 vengono contrassegnati come modificati e trasferiti. Nel terzo mese, viene contrassegnato e trasferito solo il blocco A5 modificato.

![Immagine che mostra i confronti dei metodi di backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funzionalità di backup

La tabella seguente riepiloga le funzionalità supportate per i diversi tipi di backup:

**Funzionalità** | **Backup diretto di file e cartelle (tramite l'agente MARS)** | **Backup di macchine virtuali di Azure** | **Computer o app con DPM/MABS**
--- | --- | --- | ---
Backup nell'insieme di credenziali | ![Sì][green] | ![Sì][green] | ![Sì][green]
Eseguire il backup su disco DPM/MABS e quindi in Azure | | | ![Sì][green]
Compressione dei dati inviati per il backup | ![Sì][green] | Durante il trasferimento dei dati non viene usata alcuna compressione. Leggero aumento dello spazio di archiviazione richiesto, ma ripristino più veloce.  | ![Sì][green]
Backup incrementale |![Sì][green] |![Sì][green] |![Sì][green]
Backup di dischi deduplicati | | | ![Parziale][yellow]<br/><br/> Solo per i server DPM/MABS distribuiti in locale.

![Chiave tabella](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Informazioni di base sui criteri di backup

- Un criterio di backup viene creato per ogni insieme di credenziali.
- È possibile creare criteri di backup per il backup dei carichi di lavoro seguenti: macchine virtuali di Azure, SQL nelle macchine virtuali di Azure, SAP HANA in macchine virtuali di Azure e condivisioni file di Azure. I criteri per il backup di file e cartelle tramite l'agente MARS vengono specificati nella console di MARS.
  - Condivisione file di Azure
- Un criterio può essere assegnato a più risorse. Un criterio di backup di macchine virtuali di Azure può essere usato per proteggere più macchine virtuali di Azure.
- Un criterio è costituito da due componenti
  - Pianificazione: quando creare il backup
  - Conservazione: per quanto tempo ogni backup deve essere conservato.
- La pianificazione può essere "giornaliera" o "settimanale" rispetto a uno specifico punto temporale.
- La conservazione può essere definita per punti di backup "giornalieri", "settimanali", "mensili" e "annuali".
  - "settimanale" si riferisce a un backup in un determinato giorno della settimana
  - "mensile" fa riferimento a un backup in un determinato giorno del mese
  - "yearly" si riferisce a un backup in un determinato giorno dell'anno
- La conservazione per i punti di backup "mensili", "annuale" viene definita conservazione a lungo termine
- Quando viene creato un insieme di credenziali, viene creato anche un "DefaultPolicy" che può essere usato per eseguire il backup delle risorse.
- Tutte le modifiche apportate al periodo di conservazione di un criterio di backup verranno applicate retroattivamente a tutti i punti di ripristino meno recenti, a parte quelli nuovi.

### <a name="impact-of-policy-change-on-recovery-points"></a>Impatto della modifica dei criteri sui punti di ripristino

- **La durata di conservazione viene aumentata/ridotta:** Quando viene modificata la durata di conservazione, la nuova durata di conservazione viene applicata anche ai punti di ripristino esistenti. Di conseguenza, alcuni dei punti di ripristino verranno puliti. Se il periodo di conservazione viene aumentato, anche i punti di ripristino esistenti avranno un periodo di conservazione maggiore.
- **Modifica da giornaliera a settimanale:** Quando i backup pianificati vengono modificati da giornalieri a settimana, i punti di ripristino giornalieri esistenti vengono puliti.
- **Modifica da settimanale a giornaliera:** I backup settimanali esistenti verranno conservati in base al numero di giorni rimanenti in base ai criteri di conservazione correnti.

### <a name="additional-reference"></a>Ulteriore riferimento

- Macchina virtuale di Azure: come [creare e](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) modificare [i](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) criteri.
- SQL Server database nella macchina virtuale di Azure: come creare [e](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) [modificare i](./manage-monitor-sql-database-backup.md#modify-policy) criteri.
- Condivisione file di Azure: come [creare e](./backup-afs.md) modificare [i](./manage-afs-backup.md#modify-policy) criteri.
- SAP HANA: Come creare [e modificare](./backup-azure-sap-hana-database.md#create-a-backup-policy) [i](./sap-hana-db-manage.md#change-policy) criteri.
- MARS: come [creare e](./backup-windows-with-mars-agent.md#create-a-backup-policy) modificare [i](./backup-azure-manage-mars.md#modify-a-backup-policy) criteri.
- [Esistono limitazioni per la pianificazione del backup in base al tipo di carico di lavoro?](./backup-azure-backup-faq.yml#are-there-limits-on-backup-scheduling-)
- [Cosa accade ai punti di ripristino esistenti se si modificano i criteri di conservazione?](./backup-azure-backup-faq.yml#what-happens-when-i-change-my-backup-policy-)

## <a name="architecture-built-in-azure-vm-backup"></a>Architettura: Backup di macchine virtuali di Azure incorporato

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architettura: backup diretto di computer Windows Server locali o file o cartelle di macchine virtuali di Azure

1. Per configurare lo scenario, scaricare e installare l'agente MARS nel computer. È quindi possibile selezionare gli elementi di cui eseguire il backup, quando verranno eseguiti e per quanto tempo verranno conservati in Azure.
1. Il backup iniziale viene eseguito in base alle impostazioni di backup.
1. L'agente MARS usa VSS per creare uno snapshot temporato dei volumi selezionati per il backup.
    - L'agente MARS usa solo l'operazione di scrittura del sistema Windows per acquisire lo snapshot.
    - Poiché l'agente non usa writer VSS dell'applicazione, non acquisisce snapshot coerenti con l'app.
1. Dopo aver creato lo snapshot con VSS, l'agente MARS crea un disco rigido virtuale (VHD) nella cartella della cache specificata al momento della configurazione del backup. L'agente archivia anche i checksum per ogni blocco di dati. In seguito vengono usati per rilevare i blocchi modificati per i backup incrementali successivi.
1. I backup incrementali vengono eseguiti in base alla pianificazione specificata, a meno che non venga eseguito un backup su richiesta.
1. Nei backup incrementali, i file modificati vengono identificati e viene creato un nuovo disco rigido virtuale, Il disco rigido virtuale viene compresso e crittografato e quindi inviato all'insieme di credenziali.
1. Al termine del backup incrementale, il nuovo disco rigido virtuale viene unito al disco rigido virtuale creato dopo la replica iniziale. Questo disco rigido virtuale unito fornisce lo stato più recente da usare per il confronto per il backup in corso.

![Backup di computer Windows Server locali con agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architettura: Eseguire il backup in DPM/MABS

1. Installare l'agente protezione DPM o MABS nei computer da proteggere. I computer vengono quindi aggiunti a un gruppo protezione dati DPM.
    - Per proteggere i computer locali, il server DPM o MABS deve essere in locale.
    - Per proteggere le macchine virtuali di Azure, il server MABS deve trovarsi in Azure, in esecuzione come macchina virtuale di Azure.
    - Con DPM/MABS è possibile proteggere volumi, condivisioni, file e cartelle di backup. È anche possibile proteggere lo stato del sistema di un computer (bare metal) ed è possibile proteggere app specifiche con impostazioni di backup in grado di riconoscere le app.
1. Quando si configura la protezione per un computer o un'app in DPM/MABS, si sceglie di eseguire il backup nel disco locale MABS/DPM per l'archiviazione a breve termine e in Azure per la protezione online. È anche possibile specificare quando eseguire il backup nell'archiviazione DPM/MABS locale e quando deve essere eseguito il backup online in Azure.
1. Il backup del disco del carico di lavoro protetto viene eseguito nei dischi MABS/DPM locali, in base alla pianificazione specificata.
1. I dischi DPM/MABS vengono sottoposti a backup nell'insieme di credenziali dall'agente MARS in esecuzione nel server DPM/MABS.

![Backup di computer e carichi di lavoro protetti da DPM o MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Risorse di archiviazione delle macchine virtuali di Azure

Le macchine virtuali di Azure usano dischi per archiviare il sistema operativo, le app e i dati. Ogni macchina virtuale di Azure ha almeno due dischi: un disco per il sistema operativo e un disco temporaneo. Le macchine virtuali di Azure possono anche avere dischi dati per i dati delle app. I dischi vengono archiviati come dischi rigidi virtuali.

- I dischi rigidi virtuali vengono archiviati come BLOB di pagine in account di archiviazione Standard o Premium in Azure:
  - **Archiviazione Standard:** Supporto affidabile e a basso costo dei dischi per le macchine virtuali che eseguono carichi di lavoro che non sono sensibili alla latenza. L'archiviazione Standard può usare dischi SSD (Solid-State Drive) standard o dischi HDD (Standard Hard Disk Drive).
  - **Archiviazione Premium:** Supporto per dischi ad alte prestazioni. Usa dischi SSD Premium.
- Sono disponibili tre diversi livelli di prestazioni per i dischi:
  - **HDD Standard disco:** Supportato da unità disco rigido e usato per un'archiviazione conveniente.
  - **SSD Standard disco:** Combina gli elementi dei dischi SSD Premium e dei dischi HDD Standard. Offre prestazioni e affidabilità più coerenti rispetto al disco rigido, ma comunque conveniente.
  - **SSD Premium disco:** Supportato da unità SSD e offre prestazioni elevate e bassa latenza per le macchine virtuali che eseguono carichi di lavoro con uso intensivo di I/O.
- I dischi possono essere gestiti o non gestiti:
  - **Dischi non gestiti:** Tipo tradizionale di dischi usati dalle macchine virtuali. Per questi dischi è necessario creare un account di archiviazione personale e specificarlo durante la creazione del disco. È quindi necessario capire come ottimizzare le risorse di archiviazione per le macchine virtuali.
  - **Dischi gestiti:** Azure crea e gestisce automaticamente gli account di archiviazione. Si specificano le dimensioni del disco e il livello di prestazioni e Azure crea automaticamente dischi gestiti. Quando si aggiungono dischi e si ridimensionano le macchine virtuali, Azure gestisce gli account di archiviazione.

Per altre informazioni sull'archiviazione su disco e sui tipi di disco disponibili per le macchine virtuali, vedere gli articoli seguenti:

- [Azure Managed Disks per macchine virtuali Linux](../virtual-machines/managed-disks-overview.md)
- [Tipi di disco disponibili per le macchine virtuali](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Eseguire il backup e il ripristino di macchine virtuali di Azure con Archiviazione Premium

È possibile eseguire il backup di macchine virtuali di Azure usando l'archiviazione Premium con Backup di Azure:

- Durante il processo di backup delle macchine virtuali con archiviazione Premium, il servizio Backup crea un percorso di staging temporaneo, denominato *AzureBackup-*, nell'account di archiviazione. Le dimensioni del percorso di staging sono uguali alle dimensioni dello snapshot del punto di ripristino.
- Assicurarsi che sia presente spazio libero sufficiente nell'account di archiviazione Premium per il percorso di gestione temporanea. Per altre informazioni, vedere [Obiettivi di scalabilità per gli account di archiviazione BLOB di pagine Premium.](../storage/blobs/scalability-targets-premium-page-blobs.md) Non modificare il percorso di gestione temporanea.
- Al termine del processo di backup, il percorso di gestione temporanea viene eliminato.
- Il prezzo della risorsa di archiviazione usata per il percorso di gestione temporanea è in linea con i [prezzi dell'archiviazione Premium](../virtual-machines/disks-types.md#billing).

Quando si ripristinano macchine virtuali di Azure usando l'archiviazione Premium, è possibile ripristinarle nell'archiviazione Premium o Standard. In genere, vengono ripristinati nell'archiviazione Premium. Tuttavia, se è necessario solo un subset di file dalla macchina virtuale, potrebbe essere conveniente ripristinarli nell'archiviazione standard.

### <a name="back-up-and-restore-managed-disks"></a>Eseguire il backup e il ripristino di dischi gestiti

È possibile eseguire il backup di macchine virtuali di Azure con dischi gestiti:

- Il backup di macchine virtuali con dischi gestiti funziona come per qualsiasi altra macchina virtuale di Azure. È possibile eseguire il backup della macchina virtuale direttamente dalle impostazioni della macchina virtuale oppure è possibile abilitare il backup per le macchine virtuali nell'insieme di credenziali di Servizi di ripristino.
- È possibile eseguire il backup delle macchine virtuali nei dischi gestiti tramite raccolte RestorePoint basate su dischi gestiti.
- Backup di Azure supporta anche il backup di macchine virtuali con dischi gestiti crittografati usando Crittografia dischi di Azure.

Quando si ripristinano macchine virtuali con dischi gestiti, è possibile eseguire il ripristino in una macchina virtuale completa con dischi gestiti o in un account di archiviazione:

- Durante il processo di ripristino, Azure gestisce i dischi gestiti. Se si usa l'opzione dell'account di archiviazione, si gestisce l'account di archiviazione creato durante il processo di ripristino.
- Se si ripristina una macchina virtuale gestita crittografata, assicurarsi che le chiavi e i segreti della macchina virtuale esistano nell'insieme di credenziali delle chiavi prima di avviare il processo di ripristino.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare la matrice di supporto [per informazioni sulle funzionalità e sulle limitazioni supportate per gli scenari di backup.](backup-support-matrix.md)
- Configurare il backup per uno di questi scenari:
  - [Eseguire il backup delle macchine virtuali di Azure](backup-azure-arm-vms-prepare.md).
  - [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
  - [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in MABS.
  - [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
