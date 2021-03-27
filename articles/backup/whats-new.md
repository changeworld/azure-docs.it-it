---
title: Novità di Backup di Azure
description: Informazioni sulle nuove funzionalità di backup di Azure.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: c5e6734c6a962fa43d79fc90fdfaa85923b6339f
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612484"
---
# <a name="whats-new-in-azure-backup"></a>Novità di Backup di Azure

Backup di Azure migliora costantemente e rilascia nuove funzionalità che migliorano la protezione dei dati in Azure. Queste nuove funzionalità espandono la protezione dei dati in nuovi tipi di carico di lavoro, migliorano la sicurezza e migliorano la disponibilità dei dati di backup. Aggiungono inoltre nuove funzionalità di gestione, monitoraggio e automazione.

Per ulteriori informazioni sulle nuove versioni, è possibile aggiungere un segnalibro a questa pagina o [sottoscrivere gli aggiornamenti qui](https://azure.microsoft.com/updates/?query=backup).

## <a name="updates-summary"></a>Riepilogo aggiornamenti

- Marzo 2021
  - [Il backup su disco di Azure è ora disponibile a livello generale](#azure-disk-backup-is-now-generally-available)
  - [Il Centro backup è ora disponibile a livello generale](#backup-center-is-now-generally-available)
  - [Supporto del livello di archiviazione per backup di Azure (in anteprima)](#archive-tier-support-for-azure-backup-in-preview)
- 2021 febbraio
  - [Backup per i BLOB di Azure (in anteprima)](#backup-for-azure-blobs-in-preview)
- Gennaio 2021
  - [Backup su disco di Azure (in anteprima)](#azure-disk-backup-in-preview)
  - [Crittografia dei componenti inattivi con chiavi gestite dal cliente (disponibilità generale)](#encryption-at-rest-using-customer-managed-keys)
- Novembre 2020
  - [Modello di Azure Resource Manager per il backup di una condivisione file di Azure](#azure-resource-manager-template-for-afs-backup)
  - [Backup incrementali per database SAP HANA in macchine virtuali di Azure (in anteprima)](#incremental-backups-for-sap-hana-databases-in-preview)
- Settembre 2020
  - [Centro di backup (in anteprima)](#backup-center-in-preview)
  - [Backup di database di Azure per PostgreSQL (in anteprima)](#backup-azure-database-for-postgresql-in-preview)
  - [Backup e ripristino di dischi selettivi](#selective-disk-backup-and-restore)
  - [Ripristino tra aree per database SQL Server e SAP HANA in macchine virtuali di Azure (in anteprima)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Supporto per il backup di macchine virtuali con un massimo di 32 dischi (disponibilità generale)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Esperienza di configurazione di backup semplificata per SQL in macchine virtuali di Azure](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Backup SAP HANA in macchine virtuali di Azure RHEL (in anteprima)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [Archiviazione con ridondanza della zona (ZRS) per i dati di backup (in anteprima)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Eliminazione temporanea per carichi di lavoro di SQL Server e SAP HANA in macchine virtuali di Azure](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>Il backup su disco di Azure è ora disponibile a livello generale

Backup di Azure offre la gestione del ciclo di vita degli snapshot in Azure Managed Disks automatizzando la creazione periodica di snapshot e mantenendo questi per le durate configurate con i criteri di backup.

Per altre informazioni, vedere [Panoramica di backup su disco di Azure](disk-backup-overview.md).

## <a name="backup-center-is-now-generally-available"></a>Il Centro backup è ora disponibile a livello generale

Backup Center semplifica la gestione della protezione dei dati su vasta scala, consentendo di individuare, gestire, monitorare, eseguire e ottimizzare la gestione dei backup da un'unica console centrale.

Per ulteriori informazioni, vedere [Panoramica di Centro backup](backup-center-overview.md).

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Supporto del livello di archiviazione per backup di Azure (in anteprima)

Backup di Azure consente ora di ridurre il costo dei backup con conservazione a lungo termine con la disponibilità del livello di archiviazione per le macchine virtuali di Azure e SQL Server in macchine virtuali di Azure.

Per altre informazioni, vedere [supporto del livello di archiviazione (anteprima)](archive-tier-support.md).

## <a name="backup-for-azure-blobs-in-preview"></a>Backup per i BLOB di Azure (in anteprima)

Il backup operativo per i BLOB è una soluzione gestita di protezione dei dati locale che consente di proteggere i BLOB in blocchi da diversi scenari di perdita di dati, ad esempio danneggiamenti, eliminazioni di BLOB e eliminazione accidentale di account di archiviazione. I dati vengono archiviati in locale all'interno dell'account di archiviazione di origine e possono essere ripristinati in un punto nel tempo selezionato, se necessario. Quindi, fornisce un mezzo semplice, sicuro ed economicamente conveniente per proteggere i BLOB.

Il backup operativo per i BLOB si integra con backup Center, tra le altre funzionalità di gestione dei backup, per fornire un unico riquadro di vetro che consente di gestire, monitorare, operare e analizzare i backup su larga scala.

Per altre informazioni, vedere [Panoramica del backup operativo per i BLOB di Azure (in anteprima)](blob-backup-overview.md).

## <a name="azure-disk-backup-in-preview"></a>Backup su disco di Azure (in anteprima)

Il backup su disco di Azure offre una soluzione chiavi in mano che fornisce la gestione del ciclo di vita dello snapshot per [Managed Disks di Azure](../virtual-machines/managed-disks-overview.md) automatizzando la creazione periodica di snapshot e la conservazione per una durata configurata usando i criteri di backup. È possibile gestire gli snapshot del disco con costi di infrastruttura zero e senza la necessità di generare script personalizzati o di qualsiasi sovraccarico di gestione. Si tratta di una soluzione di backup coerente con l'arresto anomalo del sistema che esegue un backup temporizzato di un disco gestito utilizzando [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md) con supporto per più backup al giorno. È anche una soluzione senza agente e non influisca sulle prestazioni dell'applicazione di produzione. Supporta il backup e il ripristino dei dischi del sistema operativo e dei dati (inclusi i dischi condivisi), indipendentemente dal fatto che siano attualmente collegati a una macchina virtuale di Azure in esecuzione.

Per altre informazioni, vedere [backup su disco di Azure (in anteprima)](disk-backup-overview.md).

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Crittografia dei componenti inattivi con chiavi gestite dal cliente

Il supporto per la crittografia dei componenti inattivi tramite chiavi gestite dal cliente è ora disponibile a livello generale. Questo consente di crittografare i dati di backup negli insiemi di credenziali dei servizi di ripristino usando le proprie chiavi archiviate in Azure Key Vault. La chiave di crittografia usata per crittografare i backup nell'insieme di credenziali di servizi di ripristino può essere diversa da quella usata per crittografare l'origine. I dati vengono protetti usando una chiave di crittografia dei dati basata su AES 256, che a sua volta è protetta usando le chiavi archiviate nel Key Vault. Rispetto alla crittografia con chiavi gestite dalla piattaforma, disponibile per impostazione predefinita, offre un maggiore controllo sulle chiavi e consente di soddisfare al meglio le esigenze di conformità.

Per altre informazioni, vedere [crittografia dei dati di backup con chiavi gestite dal cliente](encryption-at-rest-with-cmk.md).

## <a name="azure-resource-manager-template-for-afs-backup"></a>Modello di Azure Resource Manager per il backup AFS

Backup di Azure supporta ora la configurazione del backup per le condivisioni file di Azure esistenti usando un modello di Azure Resource Manager (ARM). Il modello configura la protezione per una condivisione file di Azure esistente specificando i dettagli appropriati per l'insieme di credenziali dei servizi di ripristino e i criteri di backup. Crea facoltativamente un nuovo insieme di credenziali di Servizi di ripristino e nuovi criteri di backup e registra l'account di archiviazione che contiene la condivisione file nell'insieme di credenziali di Servizi di ripristino.

Per altre informazioni, vedere [modelli di Azure Resource Manager per backup di Azure](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Backup incrementali per database SAP HANA (in anteprima)

Backup di Azure supporta ora backup incrementali per i database SAP HANA ospitati nelle VM di Azure. In questo modo è possibile eseguire backup più veloci e più convenienti dei dati SAP HANA.

Per ulteriori informazioni, vedere [diverse opzioni disponibili durante la creazione di un criterio di backup](sap-hana-faq-backup-azure-vm.md#policy) e [come creare criteri di backup per SAP Hana database](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center-in-preview"></a>Centro di backup (in anteprima)

Backup di Azure ha abilitato una nuova funzionalità di gestione nativa per gestire l'intero patrimonio di backup da una console centrale. Il centro di backup ti offre la possibilità di monitorare, operare, governare e ottimizzare la protezione dei dati su larga scala in modo unificato, coerente con le esperienze di gestione native di Azure.

Con backup Center puoi ottenere una visualizzazione aggregata dell'inventario in sottoscrizioni, località, gruppi di risorse, insiemi di credenziali e persino tenant con Azure Lighthouse. Il centro di backup è anche un centro operativo dal quale è possibile attivare le attività correlate al backup, ad esempio la configurazione di backup, ripristino, creazione di criteri o insiemi di credenziali, da un'unica posizione. Inoltre, grazie all'integrazione semplice con i criteri di Azure, è ora possibile governare l'ambiente e tenere traccia della conformità dal punto di vista del backup. I criteri di Azure predefiniti specifici di backup di Azure consentono anche di configurare backup su larga scala.

Per ulteriori informazioni, vedere [Panoramica di Centro backup](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Backup di database di Azure per PostgreSQL (in anteprima)

Backup di Azure e i servizi di database di Azure sono disponibili per creare una soluzione di backup di livello aziendale per Azure PostgreSQL (ora disponibile in anteprima). È ora possibile soddisfare le esigenze di protezione e conformità dei dati con criteri di backup controllati dal cliente che consentono la conservazione dei backup per un massimo di 10 anni. In questo modo si dispone di un controllo granulare per gestire le operazioni di backup e ripristino a livello di singolo database. Analogamente, è possibile eseguire il ripristino in più versioni di PostgreSQL o nell'archiviazione BLOB con facilità.

Per altre informazioni, vedere [backup di database di Azure per PostgreSQL](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Backup e ripristino di dischi selettivi

Backup di Azure supporta il backup di tutti i dischi (sistema operativo e dati) in una macchina virtuale insieme usando la soluzione di backup della macchina virtuale. A questo punto, usando la funzionalità di backup e ripristino dei dischi selettivi, è possibile eseguire il backup di un subset di dischi dati in una macchina virtuale. Questa è una soluzione efficiente ed economica per soddisfare le esigenze di backup e ripristino. Ogni punto di ripristino contiene solo i dischi inclusi nell'operazione di backup.

Per altre informazioni, vedere [backup e ripristino di dischi selettivi per macchine virtuali di Azure](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Ripristino tra aree per SQL Server e SAP HANA (in anteprima)

Con l'introduzione del ripristino tra più aree, è ora possibile avviare i ripristini in un'area secondaria in modo da mitigare i problemi di tempo di inattività reali in un'area primaria per l'ambiente. In questo modo, l'area secondaria esegue il ripristino completamente controllato dal cliente. Backup di Azure usa i dati di cui è stato eseguito il backup replicati nell'area secondaria per tali ripristini.

Ora, oltre al supporto per il ripristino tra aree per macchine virtuali di Azure, la funzionalità è stata estesa al ripristino di database SQL e SAP HANA anche in macchine virtuali di Azure.

Per ulteriori informazioni, vedere [ripristino tra aree per database SQL](restore-sql-database-azure-vm.md#cross-region-restore) e [ripristino tra aree per database SAP Hana](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Supporto per il backup di macchine virtuali con un massimo di 32 dischi

Fino ad ora, backup di Azure ha supportato 16 dischi gestiti per macchina virtuale. Backup di Azure supporta ora il backup di un massimo di 32 dischi gestiti per macchina virtuale.

Per altre informazioni, vedere la [matrice di supporto per l'archiviazione delle macchine virtuali](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Configurazione di backup più semplice per SQL in macchine virtuali di Azure

La configurazione dei backup per la SQL Server in macchine virtuali di Azure è ora ancora più semplice con la configurazione del backup inline integrata nel riquadro VM del portale di Azure. In pochi passaggi è possibile abilitare il backup del SQL Server per proteggere tutti i database esistenti, oltre a quelli che vengono aggiunti in futuro.

Per altre informazioni, vedere [eseguire il backup di un SQL Server dal riquadro VM](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Backup SAP HANA in macchine virtuali di Azure RHEL (in anteprima)

Backup di Azure è la soluzione di backup nativa per Azure e BackInt è certificata da SAP. Backup di Azure ora ha aggiunto il supporto per Red Hat Enterprise Linux (RHEL), uno dei sistemi operativi Linux più diffusi che eseguono SAP HANA.

Per ulteriori informazioni, vedere la [matrice di supporto dello scenario di backup del database SAP Hana](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>Archiviazione con ridondanza della zona (ZRS) per i dati di backup (in anteprima)

Archiviazione di Azure offre un ottimo equilibrio tra prestazioni elevate, disponibilità elevata e resilienza elevata dei dati con le varie opzioni di ridondanza. Backup di Azure consente di estendere questi vantaggi anche ai dati di backup, con le opzioni per archiviare i backup nell'archiviazione con ridondanza locale (con ridondanza locale) e nell'archiviazione con ridondanza geografica (GRS). Sono ora disponibili opzioni di durabilità aggiuntive con il supporto aggiunto per l'archiviazione con ridondanza della zona (ZRS).

Per altre informazioni, vedere [impostare la ridondanza di archiviazione per l'insieme di credenziali di servizi di ripristino](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Eliminazione temporanea per carichi di lavoro SQL Server e SAP HANA

Le preoccupazioni riguardo ai problemi di sicurezza, come malware, ransomware e intrusioni, aumentano continuamente. Questi problemi di sicurezza possono essere costosi in termini di denaro e di dati. Per proteggersi da tali attacchi, backup di Azure fornisce funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione.

Una di queste funzionalità è l'eliminazione temporanea. Con l'eliminazione temporanea, anche se un attore malintenzionato Elimina un backup (o elimina accidentalmente i dati di backup), i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il ripristino di tale elemento di backup senza perdita di dati. Gli altri 14 giorni di conservazione per i dati di backup nello stato "eliminazione temporanea" non comportano alcun costo.

Oltre al supporto per l'eliminazione temporanea per le macchine virtuali di Azure, i carichi di lavoro SQL Server e SAP HANA in macchine virtuali di Azure sono protetti anche con l'eliminazione temporanea.

Per altre informazioni, vedere [eliminazione temporanea per SQL Server in una macchina virtuale di Azure e SAP Hana nei carichi di lavoro delle macchine virtuali di Azure](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="next-steps"></a>Passaggi successivi

- [Indicazioni e procedure consigliate per il backup di Azure](guidance-best-practices.md)