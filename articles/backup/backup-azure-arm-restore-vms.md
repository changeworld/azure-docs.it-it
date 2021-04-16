---
title: Ripristinare le macchine virtuali usando il portale di Azure
description: Ripristinare una macchina virtuale di Azure da un punto di ripristino usando il portale di Azure, inclusa la funzionalità di ripristino tra aree.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 452ca5e1b1c6554a2ae1651068eae06ad9a6b232
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515227"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Come ripristinare i dati delle macchine virtuali di Azure in portale di Azure

Questo articolo descrive come ripristinare i dati delle macchine virtuali di Azure dai punti di ripristino archiviati negli insiemi di credenziali di Servizi di ripristino di [Backup di Azure](backup-overview.md).

## <a name="restore-options"></a>Opzioni di ripristino

Backup di Azure offre diversi modi per ripristinare una macchina virtuale.

**Opzione di ripristino** | **Dettagli**
--- | ---
**Creazione di una nuova macchina virtuale** | Crea e rende operativa rapidamente una macchina virtuale di base a partire da un punto di ripristino.<br/><br/> È possibile specificare un nome per la macchina virtuale, selezionare il gruppo di risorse e la rete virtuale in cui inserirla, nonché specificare un account di archiviazione per la macchina virtuale ripristinata. La nuova macchina virtuale deve essere creata nella stessa area della macchina virtuale di origine.<br><br>Se il ripristino di una macchina virtuale non riesce perché uno SKU della macchina virtuale di Azure non era disponibile nell'area specificata di Azure o a causa di altri problemi, Backup di Azure ripristina comunque i dischi nel gruppo di risorse specificato.
**Restore disk** (Ripristina disco) | Ripristina un disco della macchina virtuale, che può quindi essere usato per creare una nuova macchina virtuale.<br/><br/> Backup di Azure offre un modello che consente di personalizzare e creare una macchina virtuale. <br/><br> Il processo di ripristino genera un modello che può essere scaricato e usato per specificare impostazioni della macchina virtuale personalizzate e creare una macchina virtuale.<br/><br/> I dischi vengono copiati nel gruppo di risorse specificato dall'utente.<br/><br/> In alternativa, è possibile collegare il disco a una macchina virtuale esistente o creare una nuova macchina virtuale usando PowerShell.<br/><br/> Questa opzione è utile se si vuole personalizzare la macchina virtuale, aggiungere impostazioni di configurazione non presenti al momento del backup o aggiungere impostazioni che devono essere configurate usando il modello o PowerShell.
**Sostituisci esistente** | È possibile ripristinare un disco e usarlo per sostituire un disco nella macchina virtuale esistente.<br/><br/> Deve essere presente la macchina virtuale corrente. Se è stata eliminata, non è possibile usare questa opzione.<br/><br/> Backup di Azure crea uno snapshot della macchina virtuale esistente prima della sostituzione del disco e l'archivia nella posizione di gestione temporanea specificata dall'utente. I dischi esistenti connessi alla macchina virtuale vengono sostituiti con il punto di ripristino selezionato.<br/><br/> Lo snapshot viene copiato nell'insieme di credenziali e conservato in conformità con i criteri di conservazione. <br/><br/> Dopo l'operazione di sostituzione del disco, il disco originale viene conservato nel gruppo di risorse. Se non sono necessari, è possibile scegliere di eliminare manualmente i dischi originali. <br/><br/>La sostituzione esistente è supportata per le macchine virtuali gestite non crittografate, incluse quelle [create con immagini personalizzate.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) Non è supportata per le macchine virtuali classiche e le macchine virtuali non gestite.<br/><br/> Se il punto di ripristino ha un numero maggiore o minore di dischi rispetto alla macchina virtuale corrente, il numero di dischi nel punto di ripristino rifletterà solo la configurazione della macchina virtuale.<br><br> La sostituzione esistente è supportata anche per [](../active-directory/managed-identities-azure-resources/overview.md) le macchine virtuali con risorse collegate, ad esempio identità gestita assegnata dall'utente [o Key Vault](../key-vault/general/overview.md).
**Tra aree (area secondaria)** | Il ripristino tra aree può essere usato per ripristinare macchine virtuali di Azure nell'area secondaria, che è un'[area associata di Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions).<br><br> È possibile ripristinare tutte le macchine virtuali di Azure per il punto di ripristino selezionato se il backup viene eseguito nell'area secondaria.<br><br> Durante il backup, gli snapshot non vengono replicati nell'area secondaria. Vengono replicati solo i dati archiviati nell'insieme di credenziali. I ripristini di aree secondarie sono solo [ripristini a livello di](about-azure-vm-restore.md#concepts) insieme di credenziali. Il tempo di ripristino per l'area secondaria sarà quasi uguale al tempo di ripristino del livello dell'insieme di credenziali per l'area primaria.  <br><br> Questa funzionalità è disponibile per le opzioni seguenti:<br> <li> [Creare una macchina virtuale](#create-a-vm) <br> <li> [Ripristino di dischi](#restore-disks) <br><br> L'opzione di [sostituzione di dischi esistenti](#replace-existing-disks) non è attualmente supportata.<br><br> Autorizzazioni<br> L'operazione di ripristino nell'area secondaria può essere eseguita da amministratori del backup e amministratori di app.

> [!NOTE]
> È anche possibile ripristinare file e cartelle specifici in una macchina virtuale di Azure. [Altre informazioni](backup-azure-restore-files-from-vm.md)

## <a name="storage-accounts"></a>Account di archiviazione

Alcuni dettagli sugli account di archiviazione:

- **Crea macchina virtuale:** quando si crea una nuova macchina virtuale, la macchina virtuale verrà inserita nell'account di archiviazione specificato.
- **Ripristina disco:** quando si ripristina un disco, il disco viene copiato nell'account di archiviazione specificato. Il processo di ripristino genera un modello che è possibile scaricare e usare per specificare le impostazioni personalizzate della macchina virtuale. Questo modello viene inserito nell'account di archiviazione specificato.
- **Sostituisci disco:** quando si sostituisce un disco in una macchina virtuale esistente, Backup di Azure snapshot della macchina virtuale esistente prima di sostituire il disco. Lo snapshot viene copiato anche nell'insieme di credenziali di Servizi di ripristino tramite il trasferimento dei dati, come processo in background. Tuttavia, una volta completata la fase di snapshot, viene attivata l'operazione di sostituzione dei dischi. Dopo l'operazione di sostituzione del disco, i dischi della macchina virtuale di Azure di origine vengono lasciati nel gruppo di risorse specificato per l'operazione e i dischi rigidi virtuali vengono archiviati nell'account di archiviazione specificato. È possibile scegliere di eliminare o conservare questi dischi rigidi virtuali e dischi.
- **Posizione dell'account di** archiviazione: l'account di archiviazione deve essere nella stessa area dell'insieme di credenziali. Vengono visualizzati solo questi account. Se nella posizione non sono presenti account di archiviazione, è necessario crearne uno.
- **Tipo di archiviazione:** l'archiviazione BLOB non è supportata.
- **Ridondanza dell'archiviazione:** l'archiviazione con ridondanza della zona non è supportata. Le informazioni sulla replica e sulla ridondanza per l'account vengono visualizzate tra parentesi dopo il nome dell'account.
- **Archiviazione Premium:**
  - Quando si ripristinano macchine virtuali non Premium, gli account di archiviazione Premium non sono supportati.
  - Quando si ripristinano macchine virtuali gestite, gli account di archiviazione Premium configurati con regole di rete non sono supportati.

## <a name="before-you-start"></a>Prima di iniziare

Per ripristinare una macchina virtuale (creare una nuova macchina virtuale), assicurarsi di avere [](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) le autorizzazioni corrette per il controllo degli accessi in base al ruolo di Azure per l'operazione Ripristina macchina virtuale.

Se non si dispone delle [](#restore-disks)autorizzazioni, è possibile ripristinare un disco e [](#use-templates-to-customize-a-restored-vm) quindi, dopo il ripristino del disco, è possibile usare il modello generato come parte dell'operazione di ripristino per creare una nuova macchina virtuale.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>Selezionare un punto di ripristino

1. Nell'insieme di credenziali associato alla macchina virtuale da ripristinare selezionare **Elementi di backup** Macchina virtuale di  >  **Azure.**
1. Selezionare una macchina virtuale. Per impostazione predefinita, nel dashboard della macchina virtuale verranno visualizzati i punti di ripristino degli ultimi 30 giorni. È possibile visualizzare punti di ripristino antecedenti a 30 giorni o applicare un filtro per trovare punti di ripristino in base a date, intervalli di tempo e tipi diversi di coerenza degli snapshot.
1. Per ripristinare la macchina virtuale, selezionare **Ripristina macchina virtuale.**

    ![Punto di ripristino](./media/backup-azure-arm-restore-vms/restore-point.png)

1. Selezionare un punto di ripristino da usare per il ripristino.

## <a name="choose-a-vm-restore-configuration"></a>Scegliere una configurazione di ripristino per la macchina virtuale

1. In **Ripristina macchina virtuale selezionare** un'opzione di ripristino:
    - **Crea nuova:** usare questa opzione se si vuole creare una nuova macchina virtuale. È possibile creare una macchina virtuale con impostazioni semplici o ripristinare un disco e creare una macchina virtuale personalizzata.
    - **Sostituisci esistente:** usare questa opzione se si vogliono sostituire i dischi in una macchina virtuale esistente.

        ![Ripristino configurazione guidata macchina virtuale](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. Specificare le impostazioni per l'opzione di ripristino selezionata.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Tra le [opzioni di ripristino](#restore-options) è possibile creare rapidamente una macchina virtuale con le impostazioni di base da un punto di ripristino.

1. In **Ripristina macchina virtuale Crea**  >  **nuovo** tipo di  >  **ripristino** selezionare Crea una macchina **virtuale.**
1. In **Nome macchina virtuale** specificare una macchina virtuale che non esiste nella sottoscrizione.
1. In **Gruppo di risorse** selezionare un gruppo di risorse esistente per la nuova macchina virtuale o crearne uno nuovo con un nome univoco globale. Se si assegna un nome già esistente, Azure assegnerà al gruppo lo stesso nome della macchina virtuale.
1. In **Rete virtuale** selezionare la rete virtuale in cui verrà inserita la macchina virtuale. Vengono visualizzate tutte le reti virtuali associate alla sottoscrizione. Selezionare la subnet. La prima subnet è selezionata per impostazione predefinita.
1. In **Percorso di gestione temporanea** specificare l'account di archiviazione per la macchina virtuale. [Altre informazioni](#storage-accounts)

    ![Configurazione guidata del ripristino : scegliere le opzioni di ripristino](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. Selezionare **Ripristina per** attivare l'operazione di ripristino.

## <a name="restore-disks"></a>Ripristinare i dischi

Tra le [opzioni di ripristino](#restore-options) è possibile creare un disco da un punto di ripristino. Con il disco è quindi possibile eseguire una delle azioni seguenti:

- Usare il modello generato durante l'operazione di ripristino per personalizzare le impostazioni e attivare la distribuzione delle macchine virtuali. Modificare le impostazioni predefinite del modello e inviare il modello per la distribuzione della macchina virtuale.
- [Collegare i dischi ripristinati](../virtual-machines/windows/attach-managed-disk-portal.md) a una macchina virtuale esistente.
- [Creare una nuova macchina](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) virtuale dai dischi ripristinati usando PowerShell.

1. In **Configurazione di ripristino** Creare un  >  **nuovo** tipo  >  **di** ripristino selezionare Ripristina **dischi**.
1. In **Gruppo di risorse** selezionare un gruppo di risorse per i dischi ripristinati o crearne uno nuovo con un nome univoco globale.
1. In **Percorso di gestione** temporanea specificare l'account di archiviazione in cui copiare i dischi rigidi virtuali. [Altre informazioni](#storage-accounts)

    ![Selezionare Gruppo di risorse e Percorso di gestione temporanea](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Selezionare **Ripristina per** attivare l'operazione di ripristino.

Quando la macchina virtuale usa dischi  gestiti e si seleziona l'opzione Crea macchina virtuale, Backup di Azure non usa l'account di archiviazione specificato. Nel caso di Restore **disks e** **Instant Restore,** l'account di archiviazione viene usato solo per archiviare il modello. I dischi gestiti vengono creati nel gruppo di risorse specificato.
Quando la macchina virtuale usa dischi non gestiti, vengono ripristinati come BLOB nell'account di archiviazione.

### <a name="use-templates-to-customize-a-restored-vm"></a>Usare i modelli per personalizzare una VM ripristinata

Dopo il ripristino del disco, usare il modello generato come parte dell'operazione di ripristino per personalizzare e creare una nuova macchina virtuale:

1. In **Processi di backup** selezionare il processo di ripristino pertinente.

1. In **Ripristina** selezionare **Distribuisci modello per** avviare la distribuzione del modello.

    ![Drill-down del processo di ripristino](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

1. Per personalizzare l'impostazione della macchina virtuale fornita nel modello, selezionare **Modifica modello.** Per aggiungere altre personalizzazioni, selezionare **Modifica parametri.**
    - [Vedere altre informazioni](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) sulla distribuzione delle risorse da un modello personalizzato.
    - [Vedere altre informazioni](../azure-resource-manager/templates/template-syntax.md) sulla creazione di modelli.

   ![Caricare la distribuzione del modello](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. Immettere i valori personalizzati per la macchina virtuale, accettare **i termini e le condizioni e** selezionare **Acquista.**

   ![Inviare la distribuzione del modello](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Sostituire i dischi esistenti

Tra le [opzioni di ripristino](#restore-options) è possibile sostituire un disco di macchina virtuale esistente con il punto di ripristino selezionato. [Rivedere](#restore-options) tutte le opzioni di ripristino.

1. In **Configurazione di ripristino** selezionare Sostituisci **esistente.**
1. In **Tipo ripristino** selezionare **Replace disk/s** (Sostituisci dischi). Questo è il punto di ripristino che verrà usato per sostituire i dischi delle macchine virtuali esistenti.
1. In **Percorso di** gestione temporanea specificare dove salvare gli snapshot dei dischi gestiti correnti durante il processo di ripristino. [Altre informazioni](#storage-accounts)

   ![Configurazione guidata del ripristino - Sostituisci esistente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Ripristino tra aree

Come una delle [opzioni](#restore-options)di ripristino, il ripristino tra aree consente di ripristinare le macchine virtuali di Azure in un'area secondaria, ovvero un'area abbinata di Azure.

Per iniziare a usare la funzionalità, leggere [la sezione Prima di iniziare](./backup-create-rs-vault.md#set-cross-region-restore).

Per verificare se CRR è abilitato, seguire le istruzioni in [Configurare il ripristino tra aree.](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Visualizzare gli elementi di backup nell'area secondaria

Se CRR è abilitato, è possibile visualizzare gli elementi di backup nell'area secondaria.

1. Dal portale passare a Insieme di credenziali di **Servizi di ripristino** Elementi di  >  **backup**.
1. Selezionare **Area secondaria** per visualizzare gli elementi nell'area secondaria.

>[!NOTE]
>Nell'elenco verranno visualizzati solo i tipi di gestione di backup che supportano la funzionalità CRR. Attualmente è consentito solo il supporto per il ripristino dei dati dell'area secondaria in un'area secondaria.<br></br>CRR per le macchine virtuali di Azure è supportato per le macchine virtuali gestite di Azure (incluse le macchine virtuali di Azure crittografate).

![Macchine virtuali nell'area secondaria](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Selezionare l'area secondaria](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Eseguire il ripristino nell'area secondaria

L'esperienza utente di ripristino dell'area secondaria sarà simile all'esperienza utente di ripristino dell'area primaria. Quando si configurano i dettagli nel riquadro Configurazione di ripristino per configurare il ripristino, verrà richiesto di specificare solo i parametri dell'area secondaria.

Attualmente, [l'obiettivo RPO](azure-backup-glossary.md#rpo-recovery-point-objective) dell'area secondaria è fino a 12 ore dall'area primaria, anche se la replica di archiviazione con ridondanza geografica e accesso in lettura [(RA-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) è di 15 minuti.

![Scegliere la macchina virtuale da ripristinare](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Seleziona punto di ripristino](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Ripristinare la configurazione](./media/backup-azure-arm-restore-vms/rest-config.png)

![Notifica di attivazione del ripristino in corso](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Per ripristinare e creare una macchina virtuale, vedere [Creare una macchina virtuale](#create-a-vm).
- Per eseguire il ripristino come disco, vedere [Ripristinare i dischi](#restore-disks).

>[!NOTE]
>
>- Dopo l'attivazione del ripristino e nella fase di trasferimento dei dati, il processo di ripristino non può essere annullato.
>- La funzionalità di ripristino tra aree ripristina le macchine virtuali di Azure abilitate per CMK (chiavi gestite dal cliente), di cui non viene eseguito il backup in un insieme di credenziali di Servizi di ripristino abilitato per CMK, perché le macchine virtuali non abilitate per CMK nell'area secondaria.
>- I ruoli di Azure necessari per il ripristino nell'area secondaria sono gli stessi dell'area primaria.

[Le macchine virtuali aggiunte alla zona di Azure](../virtual-machines/windows/create-portal-availability-zone.md) possono essere ripristinate in qualsiasi zona di [disponibilità](../availability-zones/az-overview.md) della stessa area.

Nel processo di ripristino verrà visualizzata l'opzione **Zona di disponibilità.** Verrà visualizzata prima la zona predefinita. Per scegliere una zona diversa, scegliere il numero della zona scelta. Se la zona bloccata non è disponibile, non sarà possibile ripristinare i dati in un'altra zona perché i dati di cui è stato eseguito il backup non vengono replicati a livello di zona. Il ripristino nelle zone di disponibilità è possibile solo dai punti di ripristino nel livello dell'insieme di credenziali.

![Scegliere la zona di disponibilità](./media/backup-azure-arm-restore-vms/cross-zonal-restore.png)

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitoraggio dei processi di ripristino dell'area secondaria

1. Dal portale passare a Processi di backup **dell'insieme di credenziali di Servizi** di  >  **ripristino**
1. Selezionare **Area secondaria** per visualizzare gli elementi nell'area secondaria.

    ![Processi di backup filtrati](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>Ripristino di macchine virtuali e dischi non gestiti come gestiti

Viene fornita un'opzione per ripristinare [i dischi non gestiti](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) come dischi gestiti [durante](../virtual-machines/managed-disks-overview.md) il ripristino. Per impostazione predefinita, le macchine virtuali/dischi non gestiti vengono ripristinate come macchine virtuali/dischi non gestiti. Tuttavia, se si sceglie di eseguire il ripristino come macchine virtuali/dischi gestiti, è ora possibile farlo. Questi ripristini non vengono attivati dalla fase snapshot, ma solo dalla fase dell'insieme di credenziali. Questa funzionalità non è disponibile per le macchine virtuali crittografate non gestite.

![Eseguire il ripristino come dischi gestiti](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>Ripristinare macchine virtuali con configurazioni speciali

Esistono molti scenari comuni in cui potrebbe essere necessario ripristinare le macchine virtuali.

**Scenario** | **Indicazioni**
--- | ---
**Ripristino di macchine virtuali con vantaggio Hybrid Use** | Se una macchina virtuale Windows usa la [licenza per il vantaggio Hybrid Use (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md), ripristinare i dischi e creare una nuova macchina virtuale usando il modello fornito (con **Tipo di licenza** impostato su **Windows_Server**) o PowerShell.  È possibile applicare questa impostazione anche dopo aver creato la macchina virtuale.
**Ripristino di macchine virtuali durante un'emergenza nel data center di Azure** | Se l'insieme di credenziali usa l'archiviazione con ridondanza geografica e il data center principale per la macchina virtuale si arresta, Backup di Azure supporta il ripristino delle macchine virtuali sottoposte a backup nel data center associato. Selezionare un account di archiviazione nel data center associato e ripristinare come di consueto. Backup di Azure usa il servizio di calcolo nell'area abbinata per creare la macchina virtuale ripristinata. [Vedere altre informazioni](/azure/architecture/resiliency/recovery-loss-azure-region) sulla resilienza dei data center.<br><br> Se l'insieme di credenziali usa grS, è possibile scegliere la nuova funzionalità Ripristino tra [aree](#cross-region-restore). In questo modo è possibile eseguire il ripristino in una seconda area in scenari di interruzione completa o parziale o anche in assenza di interruzioni.
**Ripristino bare metal** | La differenza principale tra le macchine virtuali di Azure e gli hypervisor locali consiste nel fatto che in Azure non è disponibile una console per macchine virtuali. La console è obbligatoria per alcuni scenari, ad esempio per il ripristino tramite un backup di tipo di ripristino bare metal (BMR). Tuttavia, il ripristino della macchina virtuale dall'insieme di credenziali è una sostituzione completa con il ripristino bare metal.
**Ripristinare le VM con configurazioni di rete speciali** | Configurazioni di rete speciali includono macchine virtuali con bilanciamento del carico interno o esterno, con più schede di interfaccia di rete o con più indirizzi IP riservati. Per ripristinare queste macchine virtuali, usare l'[opzione relativa al disco di ripristino](#restore-disks). Questa opzione crea una copia dei dischi rigidi virtuali nell'account di [](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) archiviazione [](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) specificato ed è quindi possibile creare una macchina virtuale con un servizio di bilanciamento del carico interno o [esterno,](../virtual-machines/windows/multiple-nics.md)più nics o più indirizzi [IP](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)riservati, in base alla configurazione.
**Gruppo di sicurezza di rete (NSG) su nic/subnet** | Il backup delle macchine virtuali di Azure supporta il backup e il ripristino delle informazioni del gruppo di sicurezza di rete a livello di rete virtuale, subnet e scheda di interfaccia di rete.
**Macchine virtuali aggiunte alla zona** | Se si backup di una macchina virtuale di Azure aggiunta a una zona (con Backup di Azure), è possibile ripristinarla nella stessa zona in cui è stata bloccata. [Scopri di più](../availability-zones/az-overview.md)
**Ripristinare la macchina virtuale in qualsiasi set di disponibilità** | Quando si ripristina una macchina virtuale dal portale, non è possibile scegliere un set di disponibilità. Una macchina virtuale ripristinata non ha un set di disponibilità. Se si usa l'opzione restore disk , è possibile specificare un [set](../virtual-machines/windows/tutorial-availability-sets.md) di disponibilità quando si crea una macchina virtuale dal disco usando il modello specificato o PowerShell.
**Ripristinare macchine virtuali speciali, ad esempio macchine virtuali SQL** | Se si esegue il backup di una macchina virtuale SQL usando il backup di macchine virtuali di Azure e quindi si usa l'opzione ripristina macchina virtuale o si crea una macchina virtuale dopo il ripristino dei dischi, la macchina virtuale appena creata deve essere registrata con il provider SQL come indicato [qui.](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash) In questo modo la macchina virtuale ripristinata verrà convertita in una macchina virtuale SQL.

### <a name="restore-domain-controller-vms"></a>Ripristinare le VM del controller di dominio

**Scenario** | **Indicazioni**
--- | ---
**Ripristinare una singola macchina virtuale controller di dominio in un singolo dominio** | Ripristinare la macchina virtuale come qualsiasi altra macchina virtuale. Tenere presente quanto segue:<br/><br/> Dal punto di vista di Active Directory, la macchina virtuale di Azure è come qualsiasi altra macchina virtuale.<br/><br/> È anche disponibile Modalità ripristino servizi directory (Directory Services Restore Mode, DSRM), in modo che tutti gli scenari di ripristino di Active Directory siano attuabili. [Vedere altre informazioni](#post-restore-steps) sul backup e il ripristino dei controller di dominio virtualizzati.
**Ripristinare più macchine virtuali del controller di dominio in un singolo dominio** | Se è possibile raggiungere altri controller di dominio nello stesso dominio tramite la rete, il controller di dominio può essere ripristinato come qualsiasi macchina virtuale. Se si tratta dell'ultimo controller di dominio restante nel dominio o viene eseguito un ripristino in una rete isolata, effettuare un [ripristino della foresta](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Ripristinare una singola macchina virtuale controller di dominio in una configurazione con più domini** |  Ripristinare i dischi e creare una macchina virtuale [usando PowerShell](backup-azure-vms-automation.md#restore-the-disks)  
**Ripristino di domini multipli in una foresta** | È consigliabile eseguire un [ripristino della foresta](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

Per altre informazioni, vedere [Eseguire il backup e il ripristino dei controller di dominio di Active Directory.](active-directory-backup-restore.md)

## <a name="track-the-restore-operation"></a>Tenere traccia dell'operazione di ripristino

Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenerne traccia. Backup di Azure consente di visualizzare le notifiche relative al processo nel portale. Se non sono visibili,  selezionare il simbolo Notifiche e quindi selezionare Altri eventi nel **log attività** per visualizzare lo stato del processo di ripristino.

![Ripristino attivato](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Tenere traccia del ripristino come segue:

1. Per visualizzare le operazioni per il processo, selezionare il collegamento ipertestuale notifiche. In alternativa, nell'insieme di credenziali selezionare **Processi di backup** e quindi selezionare la macchina virtuale pertinente.

    ![Elenco di VM in un insieme di credenziali](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. Per monitorare lo stato del ripristino, selezionare qualsiasi processo di ripristino con stato **In corso.** Verrà visualizzato l'indicatore di stato, che visualizza informazioni sullo stato di avanzamento del ripristino:

    - **Tempo stimato per il ripristino:** fornisce inizialmente il tempo impiegato per completare l'operazione di ripristino. Con il progressivo avanzamento dell'operazione, il tempo impiegato si riduce fino a raggiungere il valore 0 al termine dell'operazione di ripristino.
    - **Percentage of restore** (Percentuale di ripristino): mostra la percentuale di completamento dell'operazione di ripristino.
    - **Numero di byte trasferiti:** se si esegue il ripristino creando una nuova macchina virtuale, vengono visualizzati i byte trasferiti rispetto al numero totale di byte da trasferire.

## <a name="post-restore-steps"></a>Operazioni successive al ripristino

Dopo il ripristino di una macchina virtuale, è necessario tenere presente alcuni aspetti:

- Le estensioni presenti durante la configurazione del backup vengono installate, ma non abilitate. Se si verifica un problema, reinstallare le estensioni.
- Se la macchina virtuale sottoposta a backup era associata a un indirizzo IP statico, la macchina virtuale ripristinata avrà un indirizzo IP dinamico per evitare conflitti. È possibile [aggiungere un indirizzo IP statico alla macchina virtuale ripristinata](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description).
- Una macchina virtuale ripristinata non ha un set di disponibilità. Se si usa l'opzione ripristina disco, è possibile specificare un [set](../virtual-machines/windows/tutorial-availability-sets.md) di disponibilità quando si crea una macchina virtuale dal disco usando il modello fornito o PowerShell.
- Se si usa una distribuzione Linux basata su cloud-init, ad esempio Ubuntu, per motivi di sicurezza la password verrà bloccata dopo il ripristino. Per [reimpostare la password](/troubleshoot/azure/virtual-machines/reset-password) nella VM ripristinata, usare l'estensione VMAccess. È consigliabile usare chiavi SSH in queste distribuzioni in modo da non dover reimpostare la password dopo il ripristino.
- Se non è possibile accedere a una macchina virtuale dopo il ripristino perché la macchina virtuale ha una relazione interrotta con il controller di dominio, seguire questa procedura per visualizzare la macchina virtuale:
  - Collegare il disco del sistema operativo come disco dati a una macchina virtuale ripristinata.
  - Installare manualmente l'agente di macchine virtuali se l'agente di Azure non risponde seguendo questo [collegamento.](/troubleshoot/azure/virtual-machines/install-vm-agent-offline)
  - Abilitare l'accesso alla console seriale nella macchina virtuale per consentire l'accesso dalla riga di comando alla macchina virtuale

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Quando la macchina virtuale viene ricompilata, usare portale di Azure per reimpostare l'account e la password dell'amministratore locale
  - Usare console seriale e CMD per disgiungere la macchina virtuale dal dominio

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Dopo che la macchina virtuale è stata disgiusa e riavviata, sarà possibile accedere correttamente alla macchina virtuale con le credenziali di amministratore locale e ricongiungere la macchina virtuale al dominio correttamente.

## <a name="backing-up-restored-vms"></a>Backup di macchine virtuali ripristinate

- Se una macchina virtuale è stata ripristinata nello stesso gruppo di risorse con lo stesso nome della macchina virtuale di cui è stato originariamente eseguito il backup, l'operazione di backup continuerà nella macchina virtuale dopo il ripristino.
- Se la macchina virtuale è stata ripristinata in un gruppo di risorse diverso o si è specificato un nome diverso per la macchina virtuale ripristinata, sarà necessario configurare il backup per la macchina virtuale ripristinata.

## <a name="next-steps"></a>Passaggi successivi

- In caso di difficoltà durante il processo di ripristino, [esaminare](backup-azure-vms-troubleshoot.md#restore) i problemi e gli errori comuni.
- Dopo il ripristino della macchina virtuale, vedere altre informazioni sulla [gestione delle macchine virtuali](backup-azure-manage-vms.md).