---
title: Glossario di backup di Azure
description: Questo articolo definisce i termini utili per l'uso con backup di Azure.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: fb46415c8bdb463556d57004e37d741c1b9a9b57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502026"
---
# <a name="azure-backup-glossary"></a>Glossario di backup di Azure

Questo glossario dei termini può essere utile quando si usa backup di Azure.

>[!NOTE]
>
> - I termini contrassegnati con il prefisso "(termine specifico del carico di lavoro)" si riferiscono a tali termini, che sono rilevanti solo nel contesto di un subset specifico di carichi di lavoro supportati da backup di Azure.
> - Per i termini usati comunemente nella documentazione di backup di Azure, ma che fanno riferimento ad altri servizi di Azure, viene fornito un collegamento diretto alla documentazione del servizio di Azure pertinente.

## <a name="afs-azure-file-shares"></a>AFS (condivisioni file di Azure)

Vedere la [documentazione di file di Azure](../storage/files/storage-files-introduction.md).

## <a name="alternate-location-recovery"></a>Ripristino del percorso alternativo

Ripristino eseguito dal punto di ripristino in una posizione diversa dalla posizione originale in cui sono stati eseguiti i backup. Quando si usa il backup di macchine virtuali di Azure, significa ripristinare la macchina virtuale in un server diverso dal server originale in cui sono stati eseguiti i backup. Quando si usa il backup di condivisioni file di Azure, ciò significa ripristinare i dati in una condivisione file diversa dalla condivisione file di cui è stato eseguito il backup.

## <a name="application-consistent-backup"></a>Backup coerente con l'applicazione

(Termine specifico del carico di lavoro)

I backup coerenti con l'applicazione acquisiscono contenuto di memoria e operazioni di I/O in sospeso. Gli snapshot coerenti con l'app usano un VSS writer [VSS](#vss-windows-volume-shadow-copy-service) (o pre o post script per Linux) per assicurare la coerenza dei dati dell'app prima che venga eseguito un backup. [Altre informazioni](backup-azure-vms-introduction.md)

## <a name="azure-resource-manager-arm-templates"></a>Modelli di Azure Resource Manager

Vedere la [documentazione relativa ai modelli ARM](../azure-resource-manager/templates/overview.md).

## <a name="autoprotection-for-databases"></a>Autoprotection (per i database)

(Termine specifico del carico di lavoro)

La protezione automatica è una funzionalità che consente di proteggere automaticamente tutti i database in un'istanza di SQL Server autonoma o in un SQL Server Always On gruppo di disponibilità. Non solo Abilita i backup per i database esistenti, ma protegge anche tutti i database che possono essere aggiunti in futuro.

## <a name="availability-storage-replication-types"></a>Disponibilità (tipi di replica di archiviazione)

Backup di Azure offre tre tipi di replica per assicurare la disponibilità elevata dell'archiviazione e dei dati:

### <a name="lrs"></a>LRS

[Archiviazione con ridondanza locale (con ridondanza locale)](../storage/common/storage-redundancy.md#locally-redundant-storage) replica i dati di backup tre volte (crea tre copie dei dati di backup) in un'unità di scala di archiviazione in un Data Center. Tutte le copie dei dati di backup sono presenti all'interno della stessa area. CON ridondanza locale è un'opzione a basso costo per la protezione dei dati di backup da errori hardware locali.

### <a name="grs"></a>ARCHIVIAZIONE CON RIDONDANZA GEOGRAFICA

L'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy.md#geo-redundant-storage) è l'opzione di replica predefinita e consigliata. GRS replica i dati di backup in un'area secondaria a centinaia di chilometri di distanza dalla posizione primaria dei dati di origine. GRS costa più di con ridondanza locale, ma GRS offre un livello di durabilità superiore per i dati di backup, anche in caso di interruzione a livello di area.

>[!NOTE]
>Per gli insiemi di credenziali GRS con la funzionalità di ripristino tra aree abilitata, l'archivio di backup viene aggiornato da GRS a RA-GRS (Read-Access Geo-Redundant Storage).

### <a name="zrs"></a>ZRS

L' [archiviazione con ridondanza della zona (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replica i dati di backup nelle [zone di disponibilità](../availability-zones/az-overview.md#availability-zones), garantendo la residenza e la resilienza dei dati di backup nella stessa area. Quindi, i carichi di lavoro critici che richiedono la [residenza dei dati](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) possono essere sottoposti a backup in ZRS.

## <a name="azure-command-line-interface-cli"></a>interfaccia della riga di comando di Azure

Vedere la [documentazione dell'interfaccia](/cli/azure/what-is-azure-cli)della riga di comando di Azure.

## <a name="azure-policy"></a>Criteri di Azure

Vedere la [documentazione di criteri di Azure](../governance/policy/overview.md).

## <a name="azure-powershell"></a>Azure PowerShell

Vedere la [documentazione di Azure PowerShell](/powershell/azure/).

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

Vedere la [documentazione di Azure Resource Manager](../azure-resource-manager/management/overview.md).

## <a name="azure-disk-encryption-ade"></a>Crittografia dischi di Azure

Vedere la [documentazione relativa a crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md).

## <a name="backend-storage--cloud-storage--backup-storage"></a>Archiviazione back-end/archiviazione cloud/archiviazione di backup

Spazio di archiviazione effettivo utilizzato da un'istanza di backup. Include le dimensioni di tutti i punti di conservazione esistenti per l'istanza di backup (come definito dai criteri di backup e conservazione).

## <a name="bare-metal-backup"></a>Backup bare metal

Backup dei file del sistema operativo e di tutti i dati nei volumi critici, ad eccezione dei dati utente. Per definizione, un backup bare metal include un backup dello stato del sistema. Fornisce protezione quando un computer non si avvia ed è necessario ripristinare tutto. [Altre informazioni](backup-mabs-system-state-and-bmr.md)

## <a name="backup-extensions--vm-extensions"></a>Estensioni di backup/estensioni della macchina virtuale

(Specifico per il tipo di carico di lavoro VM di Azure)

Le estensioni macchina virtuale di Azure sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Backup di Azure esegue il backup di macchine virtuali di Azure tramite l'installazione di un'estensione per l'agente di macchine virtuali di Azure in esecuzione nel computer. Backup di Azure gestisce automaticamente queste estensioni e gli utenti non devono aggiornare manualmente queste estensioni.

## <a name="backup-instance--backup-item"></a>Istanza di backup/elemento di backup

Un'origine dati di cui è stato eseguito il backup in un insieme di credenziali con un determinato criterio di backup e conservazione costituisce un'istanza di backup o un elemento di backup.

## <a name="backup-rule--backup-policy"></a>Regola di backup/criterio di backup

Una regola di backup è una regola definita dall'utente che specifica quando e con quale frequenza devono essere eseguiti i backup su un'origine dati. Per alcuni tipi di carico di lavoro, i criteri di backup consentono anche di specificare il metodo di snapshot da applicare all'origine dati (completa, incrementale, differenziale). I criteri di backup vengono spesso creati come una combinazione di regole di backup e regole di conservazione.

## <a name="backup-vault"></a>Insieme di credenziali per il backup

Una risorsa Azure Resource Manager di tipo *Microsoft. dataprotection/BackupVaults*. Attualmente, gli insiemi di credenziali di backup vengono usati per eseguire il backup dei database di Azure per il server PostgreSQL. [Altre informazioni sugli insiemi di credenziali per il backup](backup-azure-recovery-services-vault-overview.md).

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (continuità aziendale e ripristino di emergenza)

BCDR prevede un set di processi che un'organizzazione deve adottare per garantire che le app e i carichi di lavoro siano operativi durante i servizi pianificati e non pianificati o le interruzioni di Azure, con interruzioni delle attività aziendali minime. [Scopri di più](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) sui diversi servizi offerti da Azure per creare una strategia BCdR valida.

## <a name="churn"></a>Zangola

Percentuale di modifiche dei dati di cui viene eseguito il backup tra due backup consecutivi. La causa potrebbe essere l'aggiunta di nuovi dati o la modifica o l'eliminazione di dati esistenti.

## <a name="crash-consistent-backup"></a>Backup coerente con l'arresto anomalo

(Termine specifico del carico di lavoro)

Gli snapshot coerenti con l'arresto anomalo del sistema si verificano in genere se una macchina virtuale di Azure si arresta al momento del backup. Solo i dati già esistenti sul disco al momento del backup vengono acquisiti e sottoposti a backup. [Altre informazioni](backup-azure-vms-introduction.md#snapshot-consistency)

## <a name="cross-region-restore-crr"></a>Ripristino tra aree geografiche (CRR)

Come una delle [Opzioni di ripristino](backup-azure-arm-restore-vms.md#restore-options), Cross Region Restore (CRR) consente di ripristinare gli elementi di backup in un'area secondaria, ovvero un' [area abbinata ad Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="data-box"></a>Data Box

Vedere la [documentazione di data box](../databox/data-box-overview.md).

## <a name="datasource"></a>Origine dati

Una risorsa (risorsa di Azure, proxy-risorsa o risorsa locale) che è un candidato per il backup. Ad esempio, una macchina virtuale di Azure o una condivisione file di Azure.

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager)

(Termine specifico del carico di lavoro)

Vedere la [documentazione di DPM](/system-center/dpm/dpm-overview).

## <a name="expressroute"></a>ExpressRoute

Vedere la [documentazione di ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="file-system-consistent-backup"></a>Backup coerente con il file System

(Termine specifico del carico di lavoro)

I backup coerenti con il file System garantiscono la coerenza eseguendo uno snapshot di tutti i file nello stesso momento. [Altre informazioni](backup-azure-vms-introduction.md#snapshot-consistency)

## <a name="frontend-storage--source-size"></a>Archiviazione front-end/dimensioni origine

Dimensione dei dati di cui eseguire il backup per un'origine dati. La dimensione front-end di un'origine dati determina il numero di [istanze protette](#protected-instance) .

## <a name="full-backup"></a>Backup completo

Nei backup completi viene archiviata una copia dell'intera origine dati per ogni backup.

## <a name="gfs-backup-policy"></a>Criteri di backup GFS

Un criterio di backup GFS (nonno-padre-figlio) è uno che consente di definire pianificazioni di backup settimanali, mensili e annuali oltre alla pianificazione del backup giornaliero. I backup settimanali sono noti come "figli", i backup mensili sono noti come "padri" e i backup annuali sono noti come "nonni". Ognuno di questi set di copie di backup può essere configurato per essere mantenuto per diverse durate di tempo, consentendo una maggiore personalizzazione delle scelte di conservazione per le copie di backup. I criteri GFS sono utili per conservare i backup per un lungo periodo di tempo in modo più efficiente in termini di archiviazione.

## <a name="iaas-vms--azure-vms"></a>VM IaaS/VM di Azure

Vedere la [documentazione di VM di Azure](../virtual-machines/index.yml).

## <a name="incremental-backup"></a>Backup incrementale

I backup incrementali archiviano solo i blocchi che sono stati modificati rispetto al backup precedente.

## <a name="instant-restore"></a>Ripristino istantaneo

(Termine specifico del carico di lavoro) Il ripristino istantaneo implica il ripristino di un computer direttamente dallo snapshot di backup anziché dalla copia dello snapshot nell'insieme di credenziali. I ripristini istantanei sono più veloci dei ripristini da un insieme di credenziali. Il numero di punti di ripristino istantanei disponibile dipende dal periodo di memorizzazione configurato per gli snapshot. Attualmente applicabile solo per il backup di macchine virtuali di Azure.

## <a name="iops"></a>Operazioni di I/O al secondo

Operazioni di input/output al secondo.

## <a name="item-level-restore"></a>Ripristino a livello di elemento

(Termine specifico del carico di lavoro)

Ripristino di singoli file o cartelle all'interno del computer dal punto di ripristino.

## <a name="job"></a>Processo

Attività correlata al backup creata da un utente o dal servizio backup di Azure. I processi possono essere pianificati o su richiesta (ad hoc). Esistono diversi tipi di processi: backup, ripristino, configurazione della protezione e così via. [Altre informazioni sui processi](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

## <a name="mabs--azure-backup-server"></a>MAB/server di Backup di Azure

(Termine specifico del carico di lavoro)

Con il server di Backup di Azure è possibile proteggere i carichi di lavoro dell'applicazione, ad esempio le VM Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e i client di Windows, da una singola console. Eredita gran parte della funzionalità di backup del carico di lavoro da DPM, ma con alcune differenze. [Scopri di più](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>Dischi gestiti

Vedere la [documentazione relativa a Managed disks](../virtual-machines/managed-disks-overview.md).

## <a name="mars-agent"></a>Agente MARS

(Termine specifico del carico di lavoro)

Noto anche come agente di **backup di Azure** o agente di **servizi di ripristino**, l'agente Mars viene usato da backup di Azure per eseguire il backup dei dati da computer locali e VM di Azure a un insieme di credenziali di servizi di ripristino di backup in Azure. [Altre informazioni](backup-support-matrix-mars-agent.md)

## <a name="nsg-network-security-group"></a>NSG (gruppo di sicurezza di rete)

Vedere la [documentazione di NSG](../virtual-network/network-security-groups-overview.md).

## <a name="offline-seeding"></a>Seeding offline

Il seeding offline si riferisce al processo di trasferimento del backup iniziale (completo) offline, senza l'uso della larghezza di banda di rete. Fornisce un meccanismo per copiare i dati di backup nei dispositivi di archiviazione fisica, che vengono quindi spediti a un Data Center di Azure nelle vicinanze e caricati in un insieme di credenziali di servizi di ripristino. [Altre informazioni](offline-backup-overview.md)

## <a name="on-demand-backup--ad-hoc-backup"></a>Backup su richiesta/backup ad hoc

Un processo di backup che viene attivato da un utente in base a una sola volta e non in base alla pianificazione del backup (criterio) configurata per la risorsa.

## <a name="original-location-recovery-olr"></a>Ripristino del percorso originale (OLR)

Un ripristino eseguito dal punto di ripristino al percorso di origine da cui sono stati eseguiti i backup, sostituendolo con lo stato archiviato nel punto di ripristino. Quando si usa il backup di macchine virtuali di Azure, significa ripristinare la macchina virtuale nel server originale in cui sono stati eseguiti i backup. Quando si usa il backup di condivisioni file di Azure, ciò significa ripristinare i dati nella condivisione file di cui è stato eseguito il backup.

## <a name="passphrase"></a>Passphrase

(Termine specifico del carico di lavoro)

Viene usata una passphrase per crittografare e decrittografare i dati durante il backup o il ripristino del computer locale o locale usando l'agente MARS da o verso Azure.

## <a name="private-endpoint"></a>Endpoint privato

Vedere la [documentazione sull'endpoint privato](../private-link/private-endpoint-overview.md).

## <a name="protected-instance"></a>Istanza protetta

Un'istanza protetta si riferisce al computer, al server fisico o virtuale usato per configurare il backup in Azure.  Dal **punto di vista della fatturazione**, il numero di istanze protette per un computer è una funzione delle dimensioni del front-end. Pertanto, una singola istanza di backup, ad esempio una macchina virtuale di cui è stato eseguito il backup in Azure, può corrispondere a più istanze protette, a seconda delle dimensioni del front-end. [Altre informazioni](https://azure.microsoft.com/pricing/details/backup/)

## <a name="rbac-role-based-access-control"></a>RBAC (controllo degli accessi in base al ruolo)

Vedere la [documentazione RBAC](../role-based-access-control/overview.md).

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>Punto di ripristino/punto di ripristino/punto di conservazione/punto in tempo (PIT)

Copia dei dati originali di cui viene eseguito il backup. Un punto di conservazione è associato a un timestamp, quindi è possibile usarlo per ripristinare un elemento in un determinato momento.

## <a name="recovery-services-vault"></a>Insieme di credenziali dei servizi di ripristino

Una risorsa Azure Resource Manager di tipo *Microsoft. RecoveryServices/Vaults*. Attualmente, gli insiemi di credenziali dei servizi di ripristino vengono usati per eseguire il backup dei carichi di lavoro seguenti: macchine virtuali di Azure, SQL in macchine virtuali di Azure, SAP HANA in macchine virtuali di Azure e condivisioni file di Azure. Viene usato anche per eseguire il backup di carichi di lavoro locali con MARS, server di Backup di Azure (MAB) e System Center DPM. [Altre informazioni sugli insiemi di credenziali dei servizi di ripristino](backup-azure-recovery-services-vault-overview.md).

## <a name="resource-group"></a>Resource group

Vedere la [documentazione di Azure Resource Manager](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

## <a name="rest-api"></a>API REST

Vedere la [documentazione dell'API REST di Azure](/rest/api/azure/).

## <a name="retention-rule"></a>Regola di conservazione

Regola definita dall'utente che specifica per quanto tempo devono essere conservati i backup.

## <a name="rpo-recovery-point-objective"></a>RPO (obiettivo del punto di ripristino)

RPO indica la massima perdita di dati possibile in uno scenario di perdita dei dati. Questa operazione è determinata dalla frequenza di backup.

## <a name="rto-recovery-time-objective"></a>RTO (obiettivo del tempo di ripristino)

RTO indica il tempo massimo in cui i dati possono essere ripristinati fino all'ultimo punto nel tempo disponibile dopo uno scenario di perdita dei dati.

## <a name="scheduled-backup"></a>Backup pianificato

Un processo di backup che viene attivato automaticamente dai criteri di backup configurati per l'elemento specificato.

## <a name="secondary-region--paired-region"></a>Area secondaria/area abbinata

Una coppia locale è costituita da due aree all'interno della stessa area geografica. Una è l'area primaria e l'altra è l'area secondaria. Le aree abbinate vengono usate da alcuni servizi di Azure, tra cui backup di Azure con impostazioni GRS, per garantire la continuità aziendale e proteggersi dalla perdita di dati. [Altre informazioni](../best-practices-availability-paired-regions.md)

## <a name="soft-delete"></a>Eliminazione temporanea

L'eliminazione temporanea è una funzionalità che consente di evitare l'eliminazione accidentale di dati di backup. Con l'eliminazione temporanea, anche se un attore malintenzionato Elimina un backup (o i dati di backup vengono accidentalmente eliminati), i dati di backup vengono conservati per un periodo di tempo aggiuntivo, consentendo il ripristino di tale elemento di backup senza perdita di dati. [Altre informazioni](backup-azure-security-feature-cloud.md)

## <a name="snapshot"></a>Snapshot

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale (VHD) o di una condivisione file di Azure. Scopri di più sugli [snapshot del disco](../virtual-machines/windows/snapshot-copy-managed-disk.md) e sugli snapshot dei [file](../storage/files/storage-snapshots-files.md).

## <a name="storage-account"></a>Account di archiviazione

Vedere la [documentazione relativa all'account di archiviazione](../storage/common/storage-account-overview.md).

## <a name="subscription"></a>Subscription

Una sottoscrizione di Azure è un contenitore logico usato per il provisioning delle risorse in Azure. Include i dettagli di tutte le risorse, ad esempio macchine virtuali (VM), database e altro ancora.

## <a name="system-state-backup"></a>Backup dello stato del sistema

(Termine specifico del carico di lavoro)

Esegue il backup dei file del sistema operativo. Questo backup consente di eseguire il ripristino all'avvio di un computer, ma i file di sistema e il registro di sistema andranno persi. [Altre informazioni](backup-mabs-system-state-and-bmr.md)

## <a name="tenant"></a>Tenant

Un tenant è una rappresentazione di un'organizzazione. È un'istanza dedicata di Azure AD che un'organizzazione o uno sviluppatore di app riceve quando crea una relazione con Microsoft, come l'iscrizione ad Azure, Microsoft Intune o Microsoft 365.

## <a name="tier"></a>Livello

Backup di Azure supporta attualmente i livelli di archiviazione di backup seguenti:

### <a name="snapshot-tier"></a>Livello snapshot

(Termine specifico del carico di lavoro) Nella prima fase del backup della macchina virtuale, lo snapshot viene archiviato insieme al disco. Questo tipo di archiviazione viene definito livello snapshot. I ripristini del livello snapshot sono più veloci (rispetto al ripristino da un insieme di credenziali) perché eliminano il tempo di attesa per la copia degli snapshot dall'insieme di credenziali prima di attivare l'operazione di ripristino.

### <a name="vault-standard-tier"></a>Livello Vault-Standard

I dati di backup per tutti i carichi di lavoro supportati da backup di Azure vengono archiviati negli insiemi di credenziali che contengono l'archiviazione di backup, un set di scalabilità automatica di account di archiviazione gestiti da backup di Azure. Il livello Vault-Standard è un livello di archiviazione online che consente di archiviare una copia isolata dei dati di backup in un tenant gestito da Microsoft, creando così un ulteriore livello di protezione. Per i carichi di lavoro in cui è supportato il livello snapshot, esiste una copia dei dati di backup sia nel livello snapshot che nel livello standard dell'insieme di credenziali. Insieme di credenziali: il livello standard garantisce che i dati di backup siano disponibili anche se l'origine dati di cui viene eseguito il backup viene eliminata o compromessa.

## <a name="unmanaged-disk"></a>Dischi non gestiti

Vedere la [documentazione relativa ai dischi non gestiti](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks).

## <a name="vault"></a>Insiemi di credenziali

Entità di archiviazione in Azure che ospita i dati di backup. È anche un'unità di controllo degli accessi in base al ruolo e fatturazione. Attualmente esistono due tipi di insiemi di credenziali: insieme di credenziali di servizi di ripristino e insieme di credenziali per il backup.

## <a name="vault-credentials"></a>Credenziali di insieme

Il file delle credenziali dell'insieme di credenziali è un certificato generato dal portale per ogni insieme di credenziali. Viene usato durante la registrazione di un server locale nell'insieme di credenziali. [Altre informazioni](backup-azure-dpm-introduction.md)

## <a name="vnet-virtual-network"></a>VNET (rete virtuale)

Vedere la [documentazione di VNET](../virtual-network/virtual-networks-overview.md).

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows Servizio Copia Shadow del volume)

Vedere la documentazione del servizio Copia [shadow](/windows-server/storage/file-server/volume-shadow-copy-service)del volume.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di backup di Azure](backup-overview.md)
- [Architettura e componenti di backup di Azure](backup-architecture.md)