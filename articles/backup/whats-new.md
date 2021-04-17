---
title: Novità di Backup di Azure
description: Informazioni sulle nuove funzionalità in Backup di Azure.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 68e0e5cc0876840c30ab9e428a2b96bd7d667756
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516332"
---
# <a name="whats-new-in-azure-backup"></a>Novità di Backup di Azure

Backup di Azure continua a migliorare e rilasciare nuove funzionalità che migliorano la protezione dei dati in Azure. Queste nuove funzionalità espandono la protezione dei dati a nuovi tipi di carico di lavoro, migliorano la sicurezza e migliorano la disponibilità dei dati di backup. Aggiungono anche nuove funzionalità di gestione, monitoraggio e automazione.

Per altre informazioni sulle nuove versioni, è possibile aggiungere un segnalibro a questa pagina o [sottoscrivere gli aggiornamenti qui.](https://azure.microsoft.com/updates/?query=backup)

## <a name="updates-summary"></a>Riepilogo degli aggiornamenti

- Marzo 2021
  - [Backup su disco di Azure è ora disponibile a livello generale](#azure-disk-backup-is-now-generally-available)
  - [Il Centro backup è ora disponibile a livello generale](#backup-center-is-now-generally-available)
  - [Supporto del livello archivio Backup di Azure archiviazione (in anteprima)](#archive-tier-support-for-azure-backup-in-preview)
- Febbraio 2021
  - [Backup per BLOB di Azure (in anteprima)](#backup-for-azure-blobs-in-preview)
- Gennaio 2021
  - [Backup su disco di Azure (in anteprima)](#azure-disk-backup-in-preview)
  - [Crittografia dei dati in pausa con chiavi gestite dal cliente (disponibilità generale)](#encryption-at-rest-using-customer-managed-keys)
- Novembre 2020
  - [Azure Resource Manager per il backup di condivisione file di Azure (AFS)](#azure-resource-manager-template-for-afs-backup)
  - [Backup incrementali per SAP HANA database nelle macchine virtuali di Azure (in anteprima)](#incremental-backups-for-sap-hana-databases-in-preview)
- Settembre 2020
  - [Centro backup (in anteprima)](#backup-center-in-preview)
  - [Eseguire il backup di Database di Azure per PostgreSQL (in anteprima)](#backup-azure-database-for-postgresql-in-preview)
  - [Backup e ripristino selettivi del disco](#selective-disk-backup-and-restore)
  - [Ripristino tra aree per SQL Server e SAP HANA in macchine virtuali di Azure (in anteprima)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Supporto per il backup di macchine virtuali con un massimo di 32 dischi (disponibilità generale)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Esperienza di configurazione del backup semplificata per SQL nelle macchine virtuali di Azure](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Backup SAP HANA nelle macchine virtuali RHEL di Azure (in anteprima)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [Archiviazione con ridondanza della zona (ZRS) per i dati di backup (in anteprima)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Eliminazione soft per carichi SQL Server e SAP HANA nelle macchine virtuali di Azure](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>Backup su disco di Azure è ora disponibile a livello generale

Backup di Azure la gestione del ciclo di vita degli snapshot in Azure Managed Disks automatizzando la creazione periodica di snapshot e mantenendoli per le durate configurate usando i criteri di backup.

Per altre informazioni, vedere [Panoramica di Backup su disco di Azure.](disk-backup-overview.md)

## <a name="backup-center-is-now-generally-available"></a>Il Centro backup è ora disponibile a livello generale

Il Centro backup semplifica la gestione della protezione dei dati su larga scala consentendo di individuare, gestire, monitorare, usare e ottimizzare la gestione dei backup da un'unica console centrale.

Per altre informazioni, vedere [Panoramica di Centro backup.](backup-center-overview.md)

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Supporto del livello archivio Backup di Azure archiviazione (in anteprima)

Backup di Azure ora consente di ridurre il costo dei backup con conservazione a lungo termine con la disponibilità del livello archivio per le macchine virtuali di Azure e SQL Server nelle macchine virtuali di Azure.

Per altre informazioni, vedere [Supporto del livello archivio (anteprima).](archive-tier-support.md)

## <a name="backup-for-azure-blobs-in-preview"></a>Backup per BLOB di Azure (in anteprima)

Il backup operativo per i BLOB è una soluzione gestita di protezione dei dati locale che consente di proteggere i BLOB in blocchi da vari scenari di perdita di dati, ad esempio danneggiamenti, eliminazioni di BLOB ed eliminazione accidentale di account di archiviazione. I dati vengono archiviati in locale all'interno dell'account di archiviazione di origine stesso e possono essere ripristinati fino a un momento selezionato quando necessario. Offre quindi un modo semplice, sicuro ed economicamente conveniente per proteggere i BLOB.

Il backup operativo per i BLOB si integra con Centro backup, tra le altre funzionalità di gestione dei backup, per offrire un unico pannello di controllo che consente di gestire, monitorare, gestire e analizzare i backup su larga scala.

Per altre informazioni, vedere [Panoramica del backup operativo per i BLOB di Azure (in anteprima).](blob-backup-overview.md)

## <a name="azure-disk-backup-in-preview"></a>Backup su disco di Azure (in anteprima)

Backup su disco di Azure offre una soluzione chiavi in volta che offre la gestione del ciclo di vita degli snapshot per [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) automatizzando la creazione periodica di snapshot e conservandoli per una durata configurata usando i criteri di backup. È possibile gestire gli snapshot del disco senza costi di infrastruttura e senza la necessità di script personalizzati o sovraccarico di gestione. Si tratta di una soluzione di backup coerente con l'arresto anomalo del sistema che consente di eseguire il backup temporetto di un disco gestito usando [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md) con supporto per più backup al giorno. È anche una soluzione senza agente e non influisce sulle prestazioni dell'applicazione di produzione. Supporta il backup e il ripristino dei dischi del sistema operativo e dei dischi dati (inclusi i dischi condivisi), indipendentemente dal fatto che siano attualmente collegati a una macchina virtuale di Azure in esecuzione.

Per altre informazioni, vedere [Backup su disco di Azure (in anteprima).](disk-backup-overview.md)

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Crittografia dei dati in pausa con chiavi gestite dal cliente

Il supporto per la crittografia dei dati in pausa con chiavi gestite dal cliente è ora disponibile a livello generale. In questo modo è possibile crittografare i dati di backup negli insiemi di credenziali di Servizi di ripristino usando le proprie chiavi archiviate in Azure Key Vault. La chiave di crittografia usata per crittografare i backup nell'insieme di credenziali di Servizi di ripristino può essere diversa da quelle usate per crittografare l'origine. I dati vengono protetti tramite una chiave DEK (Data Encryption Key) basata su AES 256, che a sua volta è protetta usando le chiavi archiviate nel Key Vault. Rispetto alla crittografia che usa chiavi gestite dalla piattaforma (disponibile per impostazione predefinita), offre un maggiore controllo sulle chiavi e consente di soddisfare al meglio le esigenze di conformità.

Per altre informazioni, vedere [Crittografia dei dati di backup con chiavi gestite dal cliente.](encryption-at-rest-with-cmk.md)

## <a name="azure-resource-manager-template-for-afs-backup"></a>Azure Resource Manager modello per il backup AFS

Backup di Azure ora supporta la configurazione del backup per le condivisioni file di Azure esistenti usando un modello di Azure Resource Manager (ARM). Il modello configura la protezione per una condivisione file di Azure esistente specificando i dettagli appropriati per l'insieme di credenziali di Servizi di ripristino e i criteri di backup. Crea facoltativamente un nuovo insieme di credenziali di Servizi di ripristino e nuovi criteri di backup e registra l'account di archiviazione che contiene la condivisione file nell'insieme di credenziali di Servizi di ripristino.

Per altre informazioni, vedere Modelli [Azure Resource Manager per Backup di Azure](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Backup incrementali per SAP HANA database (in anteprima)

Backup di Azure ora supporta i backup incrementali per SAP HANA database ospitati in macchine virtuali di Azure. In questo modo è possibile eseguire backup più veloci ed efficienti in termini di costi dei SAP HANA dati.

Per altre informazioni, vedere [varie opzioni disponibili durante la](/sap-hana-faq-backup-azure-vm.yml#policy) creazione di un criterio di backup e come creare criteri di backup per SAP HANA [database](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center-in-preview"></a>Centro backup (in anteprima)

Backup di Azure ha abilitato una nuova funzionalità di gestione nativa per gestire l'intero spazio di backup da una console centrale. Backup Center offre la possibilità di monitorare, gestire, gestire e ottimizzare la protezione dei dati su larga scala in modo unificato coerente con le esperienze di gestione native di Azure.

Con Centro backup è possibile ottenere una visualizzazione aggregata dell'inventario tra sottoscrizioni, località, gruppi di risorse, insiemi di credenziali e persino tenant usando Azure Lighthouse. Backup Center è anche un centro notifiche da cui è possibile attivare le attività correlate al backup, ad esempio la configurazione di backup, ripristino, creazione di criteri o insiemi di credenziali, il tutto da un'unica posizione. Inoltre, con una facile integrazione con Criteri di Azure, è ora possibile controllare l'ambiente e tenere traccia della conformità dal punto di vista del backup. I criteri di Azure predefiniti specifici per Backup di Azure consentono anche di configurare i backup su larga scala.

Per altre informazioni, vedere [Panoramica di Centro backup](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Backup di Database di Azure per PostgreSQL (in anteprima)

Backup di Azure e Servizi di database di Azure si sono riuniti per creare una soluzione di backup di livello aziendale per Azure PostgreSQL (ora disponibile in anteprima). È ora possibile soddisfare le esigenze di protezione e conformità dei dati con criteri di backup controllati dal cliente che consentono la conservazione dei backup per un massimo di 10 anni. A questo scopo, si ha un controllo granulare per gestire le operazioni di backup e ripristino a livello di database singolo. Analogamente, è possibile eseguire il ripristino tra le versioni di PostgreSQL o nell'archiviazione BLOB con facilità.

Per altre informazioni, vedere [Backup di Database di Azure per PostgreSQL](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Backup e ripristino selettivi del disco

Backup di Azure supporta il backup di tutti i dischi (sistema operativo e dati) in una macchina virtuale usando la soluzione di backup della macchina virtuale. A questo punto, usando la funzionalità di backup e ripristino dei dischi selettivi, è possibile eseguire il backup di un subset dei dischi dati in una macchina virtuale. Questa è una soluzione efficiente ed economica per soddisfare le esigenze di backup e ripristino. Ogni punto di ripristino contiene solo i dischi inclusi nell'operazione di backup.

Per altre informazioni, vedere Backup e ripristino [su disco selettivo per le macchine virtuali di Azure.](selective-disk-backup-restore.md)

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Ripristino tra aree per SQL Server e SAP HANA (in anteprima)

Con l'introduzione del ripristino tra aree, è ora possibile avviare i ripristini in un'area secondaria in modo da attenuare i problemi di tempo di inattività reale in un'area primaria per l'ambiente. In questo modo l'area secondaria viene ripristinata completamente controllata dal cliente. Backup di Azure i dati di cui è stato eseguito il backup replicati nell'area secondaria per tali ripristini.

Ora, oltre al supporto per il ripristino tra aree per le macchine virtuali di Azure, la funzionalità è stata estesa anche al ripristino di database SQL e SAP HANA in macchine virtuali di Azure.

Per altre informazioni, vedere [Ripristino tra aree per i database SQL](restore-sql-database-azure-vm.md#cross-region-restore) e Ripristino tra aree per SAP HANA [database](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Supporto per il backup di macchine virtuali con un massimo di 32 dischi

Fino ad ora, Backup di Azure ha supportato 16 dischi gestiti per macchina virtuale. A questo Backup di Azure supporta il backup di un massimo di 32 dischi gestiti per macchina virtuale.

Per altre informazioni, vedere matrice di supporto [dell'archiviazione delle macchine virtuali](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Configurazione di backup più semplice per SQL nelle macchine virtuali di Azure

La configurazione dei backup per le SQL Server nelle macchine virtuali di Azure è ora ancora più semplice con la configurazione del backup inline integrata nel riquadro vm del portale di Azure. In pochi passaggi è possibile abilitare il backup del SQL Server per proteggere tutti i database esistenti e quelli che verranno aggiunti in futuro.

Per altre informazioni, vedere [Eseguire il backup di un SQL Server dal riquadro vm](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Backup SAP HANA nelle macchine virtuali RHEL di Azure (in anteprima)

Backup di Azure è la soluzione di backup nativa per Azure e BackInt è certificato da SAP. Backup di Azure è stato aggiunto il supporto per Red Hat Enterprise Linux (RHEL), uno dei sistemi operativi Linux più diffusi che eseguono SAP HANA.

Per altre informazioni, vedere la matrice [di supporto SAP HANA scenario di backup del database](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>Archiviazione con ridondanza della zona (ZRS) per i dati di backup (in anteprima)

Archiviazione di Azure offre un ottimo equilibrio tra prestazioni elevate, disponibilità elevata e resilienza dei dati elevata con varie opzioni di ridondanza. Backup di Azure consente di estendere questi vantaggi anche ai dati di backup, con opzioni per archiviare i backup nell'archiviazione con ridondanza locale (LRS) e nell'archiviazione con ridondanza geografica (GRS). Sono ora disponibili opzioni di durabilità aggiuntive con il supporto aggiunto per l'archiviazione con ridondanza della zona (ZRS).

Per altre informazioni, vedere Impostare la [ridondanza di archiviazione per l'insieme di credenziali di Servizi di ripristino.](backup-create-rs-vault.md#set-storage-redundancy)

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Eliminazione soft per carichi SQL Server e SAP HANA lavoro

Le preoccupazioni riguardo ai problemi di sicurezza, come malware, ransomware e intrusioni, aumentano continuamente. Questi problemi di sicurezza possono essere costosi in termini di denaro e di dati. Per proteggersi da tali attacchi, Backup di Azure funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione.

Una di queste funzionalità è l'eliminazione automatica. Con l'eliminazione temporaneamente, anche se un utente malintenzionato elimina un backup (o i dati di backup vengono accidentalmente eliminati), i dati di backup vengono conservati per altri 14 giorni, consentendo il ripristino di tale elemento di backup senza perdita di dati. I 14 giorni aggiuntivi di conservazione per i dati di backup nello stato di "eliminazione software" non comportano alcun costo.

Ora, oltre al supporto dell'eliminazione soft per le macchine virtuali di Azure, anche i carichi di lavoro SQL Server e SAP HANA nelle macchine virtuali di Azure sono protetti con l'eliminazione soft.

Per altre informazioni, vedere Eliminazione soft per SQL Server in macchine virtuali di Azure e SAP HANA [nei carichi di lavoro delle macchine virtuali di Azure.](soft-delete-sql-saphana-in-azure-vm.md)

## <a name="next-steps"></a>Passaggi successivi

- [Backup di Azure linee guida e procedure consigliate](guidance-best-practices.md)