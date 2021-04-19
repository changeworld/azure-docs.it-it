---
title: Informazioni sul backup di macchine virtuali di Azure
description: Questo articolo illustra come il servizio Backup di Azure backup delle macchine virtuali di Azure e come seguire le procedure consigliate.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 5ce76f64093bab362d62afcc3f94d07f7ee7883d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718450"
---
# <a name="an-overview-of-azure-vm-backup"></a>Panoramica del backup di macchine virtuali di Azure

Questo articolo descrive come il [servizio Backup di Azure eseguire il](./backup-overview.md) backup di macchine virtuali di Azure.

Backup di Azure offre backup indipendenti e isolati per prevenire la distruzione accidentale dei dati nelle macchine virtuali. I backup vengono archiviati in un insieme di credenziali di Servizi di ripristino con gestione incorporata dei punti di ripristino. La configurazione e il ridimensionamento sono semplici, i backup sono ottimizzati ed è possibile eseguire facilmente il ripristino in base alle esigenze.

Come parte del processo di backup, viene creato uno [snapshot](#snapshot-creation)e i dati vengono trasferiti all'insieme di credenziali di Servizi di ripristino senza alcun impatto sui carichi di lavoro di produzione. Lo snapshot offre diversi livelli di coerenza, come descritto [qui.](#snapshot-consistency)

Backup di Azure offre anche offerte specializzate per carichi di lavoro di database come [SQL Server](backup-azure-sql-database.md) e [SAP HANA](sap-hana-db-about.md) che sono in grado di riconoscere i carichi di lavoro, offrono un RPO di 15 minuti (obiettivo del punto di ripristino) e consentono il backup e il ripristino di singoli database.

## <a name="backup-process"></a>Processo di backup

Di seguito è illustrato come Backup di Azure completa un backup per le macchine virtuali di Azure:

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="encryption-of-azure-vm-backups"></a>Crittografia dei backup delle macchine virtuali di Azure

Quando si esegue il backup di macchine virtuali di Azure con Backup di Azure, viene eseguita la crittografia dei dati inattivi delle macchine virtuali con la crittografia del servizio di archiviazione. Backup di Azure anche possibile eseguire il backup di macchine virtuali di Azure crittografate usando Crittografia dischi di Azure.

**Crittografia** | **Dettagli** | **Supporto**
--- | --- | ---
**Crittografia del servizio di archiviazione** | Con la crittografia SSE, Archiviazione di Azure la crittografia dei dati in stato di inquieto crittografando automaticamente i dati prima di archiviarla. Archiviazione di Azure decrittografa anche i dati prima di recuperarlo. Backup di Azure supporta i backup di macchine virtuali con due tipi di crittografia del servizio di archiviazione:<li> **Crittografia del servizio di archiviazione con chiavi gestite dalla piattaforma:** questa crittografia è per impostazione predefinita per tutti i dischi nelle macchine virtuali. Per altre [informazioni, vedere qui](../virtual-machines/disk-encryption.md#platform-managed-keys).<li> **Crittografia del servizio di crittografia con chiavi gestite dal cliente.** Con la chiave CMK è possibile gestire le chiavi usate per crittografare i dischi. Per altre [informazioni, vedere qui](../virtual-machines/disk-encryption.md#customer-managed-keys). | Backup di Azure usa la crittografia del servizio di archiviazione per la crittografia dei dati in pausa delle macchine virtuali di Azure.
**Azure Disk Encryption** | Crittografia dischi di Azure crittografa i dischi dati e del sistema operativo per le macchine virtuali di Azure.<br/><br/> Crittografia dischi di Azure si integra con le chiavi di crittografia BitLocker (BEK), protette in un insieme di credenziali delle chiavi come segreti. Crittografia dischi di Azure si integra anche con Azure Key Vault chiavi di crittografia della chiave (KEK). | Backup di Azure supporta il backup di macchine virtuali di Azure gestite e non gestite crittografate solo con BEK o con BEK insieme a KEK.<br/><br/> Viene eseguito il backup e la crittografia di entrambe le bek e kek.<br/><br/> Poiché viene eseguito il backup di chiavi KEK e BEK, gli utenti con le autorizzazioni necessarie possono ripristinare chiavi e segreti nell'insieme di credenziali delle chiavi, se necessario. Questi utenti possono anche ripristinare la macchina virtuale crittografata.<br/><br/> Le chiavi e i segreti crittografati non possono essere letti da utenti non autorizzati o da Azure.

Per le macchine virtuali di Azure gestite e non gestite, Backup supporta sia le macchine virtuali crittografate solo con BEK che le macchine virtuali crittografate con BEK insieme alle kek.

I beK (segreti) e le chiavi (chiavi) di cui è stato eseguito il backup vengono crittografati. Possono essere letti e usati solo quando vengono ripristinati nell'insieme di credenziali delle chiavi dagli utenti autorizzati. Né gli utenti non autorizzati, né Azure, possono leggere o usare chiavi o segreti di cui è stato eseguito il backup.

Viene eseguito anche il backup delle bek. Pertanto, se le chiavi BEK vengono perse, gli utenti autorizzati possono ripristinare le chiavi BEK nell'insieme di credenziali delle chiavi e ripristinare le macchine virtuali crittografate. Solo gli utenti con il livello di autorizzazioni necessario possono eseguire il backup e il ripristino di macchine virtuali crittografate o chiavi e segreti.

## <a name="snapshot-creation"></a>Creazione di snapshot

Backup di Azure snapshot vengono evasi in base alla pianificazione del backup.

- **Macchine virtuali Windows:** Per le macchine virtuali Windows, il servizio Backup si coordina con vss per creare uno snapshot coerente con l'app dei dischi delle macchine virtuali.  Per impostazione predefinita, Backup di Azure esegue un backup vss completo (tronca i log dell'applicazione, ad esempio SQL Server al momento del backup per ottenere un backup coerente a livello di applicazione).  Se si usa un database di SQL Server nel backup di macchine virtuali di Azure, è possibile modificare l'impostazione per eseguire un backup di copia vss (per mantenere i log). Per altre informazioni, vedi [questo articolo](./backup-azure-vms-troubleshoot.md#troubleshoot-vm-snapshot-issues).

- **Macchine virtuali Linux:** Per creare snapshot coerenti con l'app delle macchine virtuali Linux, usare il framework di pre-script e post-script linux per scrivere script personalizzati per garantire la coerenza.

  - Backup di Azure richiama solo gli script di pre/post scritti dall'utente.
  - Se i pre-script e i post-script vengono eseguiti correttamente, Backup di Azure contrassegna il punto di ripristino come coerente con l'applicazione. Tuttavia, quando si usano script personalizzati, si è responsabili della coerenza dell'applicazione.
  - [Altre informazioni](backup-azure-linux-app-consistent.md) su come configurare gli script.

## <a name="snapshot-consistency"></a>Coerenza degli snapshot

La tabella seguente illustra i diversi tipi di coerenza dello snapshot:

**Snapshot** | **Dettagli** | **Ripristino** | **Considerazioni**
--- | --- | --- | ---
**Coerenza con le applicazioni** | Il backup coerenti con le app acquisiscono contenuto in memoria e operazioni di I/O in sospeso. Gli snapshot coerenti con l'app usano un VSS writer (o script pre/post per Linux) per garantire la coerenza dei dati dell'app prima che venga eseguito un backup. | Quando si ripristina una macchina virtuale con uno snapshot coerente con l'app, viene avviato. Non si verificano problemi di perdita o danneggiamento dei dati. Le app vengono avviate in uno stato coerente. | Windows: tutti i writer VSS sono riusciti<br/><br/> Linux: gli script pre/post sono configurati e hanno esito positivo
**Coerente con il file system** | I backup coerenti con il file system offrono coerenza tramite l'esecuzione di uno snapshot di tutti i file contemporaneamente.<br/><br/> | Quando si ripristina una macchina virtuale con uno snapshot coerente con il file system, viene avviato. Non si verificano problemi di perdita o danneggiamento dei dati. Le app devono implementare uno specifico meccanismo di correzione per assicurarsi che i dati ripristinati siano coerenti. | Windows: alcuni writer VSS non sono riusciti <br/><br/> Linux: impostazione predefinita (se gli script pre/post non sono configurati o non sono riusciti)
**Coerenza con l'arresto anomalo del sistema** | Gli snapshot coerenti con l'arresto anomalo del sistema si verificano in genere se una macchina virtuale di Azure viene arrestata al momento del backup. Solo i dati già esistenti sul disco al momento del backup vengono acquisiti e sottoposti a backup. | Inizia con il processo di avvio della macchina virtuale seguito da un controllo del disco per correggere gli errori di danneggiamento. Tutte le operazioni di scrittura o dati in memoria non trasferite su disco prima dell'arresto anomalo del sistema vengono perse. Le app implementano una propria procedura di verifica dei dati. Ad esempio, un'app di database può usare il log delle transazioni per la verifica. Se nel log delle transazioni sono presenti voci non presenti nel database, il software di database esegue il rollback delle transazioni fino a quando i dati non sono coerenti. | La macchina virtuale è in stato di arresto (arrestato/deallocato).

>[!NOTE]
> Se lo stato del provisioning ha **esito positivo,** Backup di Azure backup coerenti con il file system. Se lo stato di provisioning non **è disponibile** o non **è riuscito,** vengono evasi backup coerenti con l'arresto anomalo del sistema. Se lo stato del provisioning **sta creando** o **eliminando**, significa Backup di Azure sta ritentando le operazioni.

## <a name="backup-and-restore-considerations"></a>Considerazioni su backup e ripristino

**Considerazioni** | **Dettagli**
--- | ---
**Disco** | Il backup dei dischi delle macchine virtuali è parallelo. Ad esempio, se una macchina virtuale ha quattro dischi, il servizio Backup tenta di eseguire il backup di tutti e quattro i dischi in parallelo. Il backup è incrementale (solo i dati modificati).
**Pianificazione** |  Per ridurre il traffico di backup, eseguire il backup di macchine virtuali diverse in momenti diversi del giorno e assicurarsi che gli orari non si sovrappongano. Il backup di macchine virtuali nello stesso momento crea problemi di traffico.
**Preparazione dei backup** | Tenere presente il tempo necessario per preparare il backup. che include l'installazione o l'aggiornamento dell'estensione di backup, nonché la generazione di uno snapshot in base alla pianificazione del backup.
**Trasferimento dei dati** | Considerare il tempo necessario per Backup di Azure identificare le modifiche incrementali dal backup precedente.<br/><br/> In caso di backup incrementale, Backup di Azure determina le modifiche calcolando il checksum del blocco. Gli eventuali blocchi modificati vengono contrassegnati per il trasferimento nell'insieme di credenziali. Il servizio analizza i blocchi identificati per tentare di ridurre ulteriormente la quantità di dati da trasferire. Dopo aver valutato tutti i blocchi modificati, Backup di Azure trasferisce le modifiche nell'insieme di credenziali.<br/><br/> Può verificarsi un ritardo tra la creazione dello snapshot e la copia nell'insieme di credenziali. Nei periodi di picco possono essere necessario fino a otto ore prima che gli snapshot siano trasferiti nell'insieme di credenziali. Il tempo di backup per una macchina virtuale sarà inferiore a 24 ore per il backup giornaliero.
**Backup iniziale** | anche se il tempo totale di backup per i backup incrementali è inferiore a 24 ore, potrebbe non essere così per il primo backup. Il tempo necessario per il backup iniziale dipenderà dalla dimensione dei dati e dal momento in cui viene elaborato il backup.
**Coda di ripristino** | Backup di Azure processi di ripristino da più account di archiviazione contemporaneamente e inserisce le richieste di ripristino in una coda.
**Copia di ripristino** | Durante il processo di ripristino i dati vengono copiati dall'insieme di credenziali all'account di archiviazione.<br/><br/> Il tempo di ripristino totale dipende dalle operazioni di I/O al secondo (IOPS) e dalla velocità effettiva dell'account di archiviazione.<br/><br/> Per ridurre il tempo di copia, selezionare un account di archiviazione non impegnato con altre operazioni di lettura e scrittura di applicazioni.

### <a name="backup-performance"></a>Prestazioni del backup

Questi scenari comuni possono influire sul tempo totale di backup:

- **Aggiunta di un nuovo disco a una macchina virtuale di Azure protetta:** Se una macchina virtuale è sottoposta a backup incrementale e viene aggiunto un nuovo disco, il tempo di backup aumenterà. Il tempo totale di backup potrebbe durare più di 24 ore a causa della replica iniziale del nuovo disco, insieme delta replication dei dischi esistenti.
- **Dischi frammentati:** Le operazioni di backup sono più veloci quando le modifiche del disco sono contigue. Se invece le modifiche sono distribuite e frammentate su un disco, il backup sarà più lento.
- **Varianza del disco:** Se i dischi protetti sottoposti a backup incrementale hanno una varianza giornaliera superiore a 200 GB, il completamento del backup può richiedere molto tempo (più di otto ore).
- **Versioni di backup:** La versione più recente di Backup (nota come versione di ripristino istantaneo) usa un processo più ottimizzato rispetto al confronto dei checksum per l'identificazione delle modifiche. Tuttavia, se si usa il ripristino istantaneo ed è stato eliminato uno snapshot di backup, il backup passa al confronto dei checksum. In questo caso, l'operazione di backup supererà le 24 ore (o avrà esito negativo).

### <a name="restore-performance"></a>Prestazioni di ripristino

Questi scenari comuni possono influire sul tempo di ripristino totale:

- Il tempo di ripristino totale dipende dalle operazioni di input/output al secondo (IOPS) e dalla velocità effettiva dell'account di archiviazione.
- Il tempo totale di ripristino può essere influenzato se l'account di archiviazione di destinazione viene caricato con altre operazioni di lettura e scrittura dell'applicazione. Per migliorare l'operazione di ripristino, selezionare un account di archiviazione non caricato con altri dati dell'applicazione.

## <a name="best-practices"></a>Procedure consigliate

Quando si configurano backup per macchine virtuali, è consigliabile seguire queste procedure:

- Modificare l'ora di pianificazione predefinita impostata in un criterio. Se, ad esempio, l'ora predefinita per il criterio è impostata su 00:00, applicare un incremento di alcuni minuti affinché le risorse vengano usate in modo ottimale.
- Se si ripristinano macchine virtuali da un singolo insieme di credenziali, è consigliabile usare diversi account di archiviazione per utilizzo generico [v2](../storage/common/storage-account-upgrade.md) per assicurarsi che l'account di archiviazione di destinazione non sia limitato. Ad esempio, ogni macchina virtuale deve avere un account di archiviazione diverso. Ad esempio, se vengono ripristinate 10 macchine virtuali, usare 10 account di archiviazione diversi.
- Per il backup di macchine virtuali che usano l'archiviazione Premium con Ripristino istantaneo, è consigliabile  allocare il *50%* di spazio disponibile dello spazio di archiviazione totale allocato, che è necessario solo per il primo backup. Lo spazio disponibile del 50% non è un requisito per i backup dopo il completamento del primo backup
- Il limite relativo al numero di dischi per account di archiviazione dipende dalla modalità con cui le applicazioni in esecuzione in una macchina virtuale IaaS accedono ai dischi. Come regola generale, se sono presenti da 5 a 10 o più dischi in un solo account di archiviazione, bilanciare il carico spostando alcuni dischi in account di archiviazione separati.
- Per ripristinare macchine virtuali con dischi gestiti tramite PowerShell, specificare il parametro aggiuntivo ***TargetResourceGroupName*** per specificare il gruppo di risorse in cui verranno ripristinati i dischi gestiti. Altre informazioni sono [disponibili qui.](./backup-azure-vms-automation.md#restore-managed-disks)

## <a name="backup-costs"></a>Costi di backup

Per il backup di macchine virtuali di Azure con Backup di Azure vengono applicati i [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

La fatturazione non viene avviata fino al completamento del primo backup riuscito. A questo punto inizia la fatturazione sia per le macchine virtuali di archiviazione sia per quelle protette. La fatturazione continua fino a quando tutti i dati di backup per la macchina virtuale vengono archiviati in un insieme di credenziali. Se si arresta la protezione per una macchina virtuale, ma sono presenti dati di backup della macchina virtuale in un insieme di credenziali, la fatturazione continuerà.

La fatturazione relativa a una macchina virtuale specifica viene interrotta solo se viene arrestata la protezione e vengono eliminati i dati di backup. Quando si arresta la protezione e non vi sono processi di backup attivi, la dimensione dell'ultimo backup della macchina virtuale completato correttamente diventa la dimensione dell'istanza protetta usata per la fatturazione mensile.

Il calcolo delle dimensioni dell'istanza protetta si basa sulle *dimensioni* effettive della macchina virtuale. Le dimensioni della macchina virtuale sono la somma di tutti i dati nella macchina virtuale, esclusa l'archiviazione temporanea. I prezzi si basano sui dati effettivi archiviati nei dischi dati, non sulle dimensioni massime supportate per ogni disco dati collegato alla macchina virtuale.

Analogamente, la fattura di archiviazione di backup si basa sulla quantità di dati archiviati in Backup di Azure, ovvero la somma dei dati effettivi in ogni punto di ripristino.

Ad esempio, prendere una macchina virtuale di dimensioni A2 Standard con due dischi dati aggiuntivi con una dimensione massima di 32 TB ciascuno. La tabella seguente mostra i dati effettivi archiviati in ognuno di questi dischi:

**Disco** | **Dimensioni massime** | **Dati effettivi presenti**
--- | --- | ---
Disco del sistema operativo | 32 TB | 17 GB
Disco locale/temporaneo | 135 GB | 5 GB (non incluso per il backup)
Disco dati 1 | 32 TB| 30 GB
Disco dati 2 | 32 TB | 0 GB

In questo caso, la dimensione effettiva della macchina virtuale è 17 GB + 30 GB + 0 GB = 47 GB. Questa dimensione dell'istanza protetta (47 GB) diventa la base per la fattura mensile. Man mano che aumenta la quantità di dati nella macchina virtuale, le dimensioni dell'istanza protetta usate per la fatturazione cambiano in modo che corrispondano.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare il backup delle macchine virtuali di Azure.](backup-azure-arm-vms-prepare.md)