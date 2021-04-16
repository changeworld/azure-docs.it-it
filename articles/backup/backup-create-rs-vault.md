---
title: Creare e configurare insiemi di credenziali di Servizi di ripristino
description: Questo articolo illustra come creare e configurare insiemi di credenziali di Servizi di ripristino in cui sono archiviati i backup e i punti di ripristino. Informazioni su come usare il ripristino tra aree per il ripristino in un'area secondaria.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5e2983e473fac72d02f0fdbc8c307e96326ac0a6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518576"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Creare e configurare un insieme di credenziali di Servizi di ripristino

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Impostare la ridondanza di archiviazione

Backup di Azure gestisce automaticamente l'archiviazione per l'insieme di credenziali. È necessario specificare la modalità di replica dell'archiviazione.

> [!NOTE]
> È **necessario modificare il** tipo di replica di archiviazione (con ridondanza locale o con ridondanza geografica) per un insieme di credenziali di Servizi di ripristino prima di configurare i backup nell'insieme di credenziali. Dopo aver configurato il backup, l'opzione da modificare è disabilitata.
>
>- Se il backup non è ancora stato configurato, seguire [questa procedura](#set-storage-redundancy) per esaminare e modificare le impostazioni.
>- Se il backup è già stato configurato ed è necessario passare dall'archiviazione con archiviazione con accesso in archiviazione con archiviazione con accesso in tempo utile a LRS, [esaminare queste soluzioni alternative.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

1. Nel riquadro **Insiemi di credenziali di Servizi di** ripristino selezionare il nuovo insieme di credenziali. Nella sezione **Impostazioni** selezionare  **Proprietà**.
1. In **Proprietà** in **Configurazione backup** selezionare **Aggiorna**.

1. Selezionare il tipo di replica di archiviazione e selezionare **Salva**.

     ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Se si usa Azure come endpoint di archiviazione di backup primario, è consigliabile continuare a usare l'impostazione con ridondanza **geografica** predefinita.
   - Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure.
   - Altre informazioni sulla [ridondanza](../storage/common/storage-redundancy.md#geo-redundant-storage) geografica [e](../storage/common/storage-redundancy.md#locally-redundant-storage) locale.
   - Se è necessaria la disponibilità dei dati senza tempi di inattività in un'area, garantendo la residenza dei dati, scegliere Archiviazione [con ridondanza della zona.](../storage/common/storage-redundancy.md#zone-redundant-storage)

>[!NOTE]
>Le impostazioni di Replica archiviazione per l'insieme di credenziali non sono rilevanti per il backup della condivisione file di Azure perché la soluzione corrente è basata su snapshot e non sono presenti dati trasferiti nell'insieme di credenziali. Gli snapshot vengono archiviati nello stesso account di archiviazione della condivisione file di cui è stato eseguito il backup.

## <a name="set-cross-region-restore"></a>Impostare il ripristino tra aree

L'opzione **di ripristino Ripristino tra aree consente** di ripristinare i dati in un'area secondaria [abbinata di Azure.](../best-practices-availability-paired-regions.md)

Supporta le origini dati seguenti:

- Macchine virtuali di Azure (disponibilità generale)
- Database SQL ospitati in macchine virtuali di Azure (anteprima)
- SAP HANA database ospitati in macchine virtuali di Azure (anteprima)

L'uso del ripristino tra aree consente di:

- eseguire esercitazioni quando è presente un requisito di controllo o conformità
- ripristinare i dati in caso di emergenza nell'area primaria

Quando si ripristina una macchina virtuale, è possibile ripristinare la macchina virtuale o il relativo disco. Se si esegue il ripristino da database SQL/SAP HANA ospitati in macchine virtuali di Azure, è possibile ripristinare i database o i relativi file.

Per scegliere questa funzionalità, selezionare **Abilita ripristino tra aree** nel riquadro Configurazione **backup.**

Poiché questo processo è a livello di archiviazione, esistono implicazioni [in termini di prezzi.](https://azure.microsoft.com/pricing/details/backup/)

>[!NOTE]
>Prima di iniziare:
>
>- Esaminare la [matrice di supporto](backup-support-matrix.md#cross-region-restore) per un elenco di aree e tipi gestiti supportati.
>- La funzionalità di ripristino tra aree per le macchine virtuali di Azure è ora disponibile a carattere generale in tutte le aree pubbliche di Azure.
>- Il ripristino tra aree per i database SQL e SAP HANA è disponibile in anteprima in tutte le aree pubbliche di Azure.
>- CRR è una funzionalità di consenso esplicito a livello di insieme di credenziali per qualsiasi insieme di credenziali con archiviazione con autorizzazioni di archiviazione con autorizzazioni di archiviazione (disattivata per impostazione predefinita).
>- Dopo il consenso esplicito, potrebbero essere necessario fino a 48 ore prima che gli elementi di backup siano disponibili nelle aree secondarie.
>- Attualmente, CRR per le macchine virtuali di Azure è supportato per Azure Resource Manager macchine virtuali di Azure e per le macchine virtuali di Azure crittografate. Le macchine virtuali di Azure classiche non saranno supportate. Quando altri tipi di gestione supportano CRR, verranno **registrati automaticamente.**
>- Il ripristino tra aree attualmente non può essere ripristinato **all'accesso** con grs o LRS dopo che la protezione è stata avviata per la prima volta.
>- Attualmente, [l'RPO](azure-backup-glossary.md#rpo-recovery-point-objective) dell'area secondaria è fino a 12 ore dall'area primaria, anche se la replica di archiviazione con ridondanza geografica e accesso in lettura [è](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) di 15 minuti.

### <a name="configure-cross-region-restore"></a>Configurare il ripristino tra aree

Un insieme di credenziali creato con ridondanza dell'archiviazione con ridondanza geografica include l'opzione per configurare la funzionalità di ripristino tra aree. Ogni insieme di credenziali grs avrà un banner, che verrà creato un collegamento alla documentazione. Per configurare CRR per l'insieme di credenziali, passare al riquadro Configurazione di backup, che contiene l'opzione per abilitare questa funzionalità.

 ![Banner di configurazione del backup](./media/backup-azure-arm-restore-vms/banner.png)

1. Dal portale passare all'insieme di credenziali di Servizi di ripristino > **proprietà** (in **Impostazioni**).
1. In **Configurazione backup** selezionare **Aggiorna.**
1. Selezionare **Abilita ripristino tra aree in questo insieme di credenziali** per abilitare la funzionalità.

   ![Abilitare il ripristino tra aree](./media/backup-azure-arm-restore-vms/backup-configuration.png)

Per altre informazioni sul backup e il ripristino con CRR, vedere gli articoli seguenti:

- [Ripristino tra aree per macchine virtuali di Azure](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Ripristino tra aree per i database SQL](restore-sql-database-azure-vm.md#cross-region-restore)
- [Ripristino tra aree per SAP HANA database](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Impostare le impostazioni di crittografia

Per impostazione predefinita, i dati nell'insieme di credenziali di Servizi di ripristino vengono crittografati usando chiavi gestite dalla piattaforma. Non sono necessarie azioni esplicite dall'utente per abilitare questa crittografia e si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di Servizi di ripristino.  È possibile scegliere di utilizzare la propria chiave per crittografare i dati di backup in questo insieme di credenziali. Questa operazione viene definita chiavi gestite dal cliente. Se si desidera crittografare i dati di backup usando la propria chiave, è necessario specificare la chiave di crittografia prima che qualsiasi elemento sia protetto da questo insieme di credenziali. Dopo aver abilitato la crittografia con la chiave, non può essere annullata.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Configurazione di un insieme di credenziali per la crittografia tramite chiavi gestite dal cliente

Per configurare l'insieme di credenziali per la crittografia con chiavi gestite dal cliente, è necessario seguire questi passaggi nell'ordine seguente:

1. Abilitare l'identità gestita per l'insieme di credenziali di Servizi di ripristino

1. Assegnare le autorizzazioni all'insieme di credenziali per accedere alla chiave di crittografia nel Azure Key Vault

1. Abilitare la protezione dell'eliminazione e dell'eliminazione in Azure Key Vault

1. Assegnare la chiave di crittografia all'insieme di credenziali di Servizi di ripristino

Le istruzioni per ognuno di questi passaggi sono disponibili [in questo articolo.](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys)

## <a name="modifying-default-settings"></a>Modifica delle impostazioni predefinite

È consigliabile rivedere le impostazioni predefinite di **Tipo di replica di archiviazione** e **Impostazioni di sicurezza** prima di configurare i backup nell'insieme di credenziali.

- **Il tipo di replica di** archiviazione per impostazione predefinita è impostato su **Ridondanza geografica.** Dopo aver configurato il backup, l'opzione da modificare è disabilitata.
  - Se il backup non è ancora stato configurato, seguire [questa procedura](#set-storage-redundancy) per esaminare e modificare le impostazioni.
  - Se il backup è già stato configurato ed è necessario passare da GRS a LRS, [esaminare queste soluzioni alternative.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

- **L'eliminazione** software per impostazione predefinita **è abilitata** per gli insiemi di credenziali appena creati per proteggere i dati di backup da eliminazioni accidentali o dannose. [Seguire questa procedura](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) per esaminare e modificare le impostazioni.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Come passare dall'archiviazione con archiviazione con accesso in modalità grs a archiviazione con archiviazione con accesso in modalità lrs dopo la configurazione del backup

Prima di decidere di passare dall'archiviazione con ridondanza locale all'archiviazione con ridondanza locale, esaminare i compromessi tra costi inferiori e durabilità dei dati più elevata che si adattano allo scenario. Se è necessario passare da GRS a LRS, sono disponibili due opzioni. Dipendono dai requisiti aziendali per conservare i dati di backup:

- [Non è necessario conservare i dati di backup precedenti](#dont-need-to-preserve-previous-backed-up-data)
- [Deve mantenere i dati di cui è stato eseguito il backup precedente](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Non è necessario conservare i dati di backup precedenti

Per proteggere i carichi di lavoro in un nuovo insieme di credenziali LRS, la protezione e i dati correnti dovranno essere eliminati nell'insieme di credenziali grs e i backup configurati nuovamente.

>[!WARNING]
>L'operazione seguente è distruttiva e non può essere annullata. Tutti i dati di backup e gli elementi di backup associati al server protetto verranno eliminati definitivamente. Procedere con cautela.

Arrestare ed eliminare la protezione corrente nell'insieme di credenziali grs:

1. Disabilitare l'eliminazione temporaneamente nelle proprietà dell'insieme di credenziali grs. Seguire [questa procedura per](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) disabilitare l'eliminazione temporaneamente.

1. Arrestare la protezione ed eliminare i backup dall'insieme di credenziali grs esistente. Nel menu del dashboard dell'insieme di credenziali selezionare **Elementi di backup.** Gli elementi elencati di seguito che devono essere spostati nell'insieme di credenziali dell'archiviazione con archiviazione con accesso in locale devono essere rimossi insieme ai relativi dati di backup. Informazioni su [come eliminare elementi protetti nel cloud](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) ed eliminare elementi protetti in [locale.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

1. Se si prevede di spostare AFS (condivisioni file di Azure), server SQL o server SAP HANA, sarà necessario annullare anche la registrazione. Nel menu del dashboard dell'insieme di credenziali selezionare **Infrastruttura di backup.** Informazioni su come [annullare la registrazione del server SQL,](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)annullare la registrazione di un account di archiviazione associato alle condivisioni file di [Azure](manage-afs-backup.md#unregister-a-storage-account)e annullare la registrazione di [un'SAP HANA istanza di](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. Dopo che sono stati rimossi dall'insieme di credenziali con archiviazione con archiviazione con accesso in lettura, continuare a configurare i backup per il carico di lavoro nel nuovo insieme di credenziali dell'archiviazione con archiviazione con accesso in lettura.

#### <a name="must-preserve-previous-backed-up-data"></a>Deve mantenere i dati di cui è stato eseguito il backup precedente

Se è necessario mantenere i dati protetti correnti nell'insieme di credenziali dell'archiviazione con accesso in lettura e continuare la protezione in un nuovo insieme di credenziali di archiviazione con archiviazione con accesso in lettura, sono disponibili opzioni limitate per alcuni dei carichi di lavoro:

- Per MARS, è possibile arrestare [la protezione conservando i dati](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) e registrando l'agente nel nuovo insieme di credenziali dell'archiviazione con registrazione locale.

  - Backup di Azure servizio continuerà a mantenere tutti i punti di ripristino esistenti dell'insieme di credenziali grs.
  - È necessario pagare per mantenere i punti di ripristino nell'insieme di credenziali grs.
  - Sarà possibile ripristinare i dati di cui è stato eseguito il backup solo per i punti di ripristino non scaduti nell'insieme di credenziali grs.
  - Sarà necessario creare una nuova replica iniziale dei dati nell'insieme di credenziali LRS.

- Per una macchina virtuale [](backup-azure-manage-vms.md#stop-protecting-a-vm) di Azure, è possibile arrestare la protezione conservando i dati per la macchina virtuale nell'insieme di credenziali grs, spostare la macchina virtuale in un altro gruppo di risorse e quindi proteggere la macchina virtuale nell'insieme di credenziali LRS. Vedere [le linee guida e le limitazioni](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) per lo spostamento di una macchina virtuale in un altro gruppo di risorse.

  Una macchina virtuale può essere protetta in un solo insieme di credenziali alla volta. Tuttavia, la macchina virtuale nel nuovo gruppo di risorse può essere protetta nell'insieme di credenziali LRS perché è considerata una macchina virtuale diversa.

  - Backup di Azure servizio conserverà i punti di ripristino di cui è stato eseguito il backup nell'insieme di credenziali grs.
  - È necessario pagare per mantenere i punti di ripristino nell'insieme di credenziali grs (per informazioni dettagliate, vedere Backup di Azure [prezzi).](azure-backup-pricing.md)
  - Sarà possibile ripristinare la macchina virtuale, se necessario, dall'insieme di credenziali grs.
  - Il primo backup nell'insieme di credenziali LRS della macchina virtuale nella nuova risorsa sarà una replica iniziale.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su](backup-azure-recovery-services-vault-overview.md) Insiemi di credenziali di Servizi di ripristino.
[Informazioni su](backup-azure-delete-vault.md) Eliminare gli insiemi di credenziali di Servizi di ripristino.