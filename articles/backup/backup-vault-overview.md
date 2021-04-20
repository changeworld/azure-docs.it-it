---
title: Panoramica degli insiemi di credenziali di backup
description: Panoramica degli insiemi di credenziali di backup.
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: e2d720da9474a35870de01559201d22c9e5b567f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739081"
---
# <a name="backup-vaults-overview"></a>Panoramica degli insiemi di credenziali di backup

Questo articolo descrive le funzionalità di un insieme di credenziali di Backup. Un insieme di credenziali di backup è un'entità di archiviazione in Azure che ospita i dati di backup per determinati carichi di lavoro più Backup di Azure supportati. È possibile usare gli insiemi di credenziali di Backup per contenere i dati di backup per vari servizi di Azure, ad esempio i server di Database di Azure per PostgreSQL e i carichi di lavoro più Backup di Azure che saranno supportati. Gli insiemi di credenziali di backup semplificano l'organizzazione dei dati di backup, riducendo al minimo il sovraccarico di gestione. Gli insiemi di credenziali di backup si basano Azure Resource Manager modello di Azure, che offre funzionalità quali:

- **Funzionalità avanzate per proteggere i dati di backup:** con gli insiemi di credenziali di backup, Backup di Azure offre funzionalità di sicurezza per proteggere i backup cloud. Queste funzionalità di sicurezza garantiscono la protezione dei backup e ripristinano i dati in modo sicuro anche se i server di produzione e di backup vengono compromessi. [Scopri di più](backup-azure-security-feature.md)

- **Controllo degli accessi in base al ruolo di Azure:** il controllo degli accessi in base al ruolo di Azure offre un controllo di gestione degli accessi con granularità fine in Azure. [Azure offre diversi ruoli predefiniti](../role-based-access-control/built-in-roles.md) mentre Backup di Azure dispone di tre [ruoli predefiniti per la gestione dei punti di ripristino](backup-rbac-rs-vault.md). Gli insiemi di credenziali di backup sono compatibili con il controllo degli accessi in base al ruolo di Azure, che limita l'accesso di backup e ripristino al set definito di ruoli utente. [Scopri di più](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Impostazioni di archiviazione nell'insieme di credenziali di Backup

Un insieme di credenziali di backup è un'entità che archivia i backup e i punti di ripristino creati nel tempo. L'insieme di credenziali di Backup contiene anche i criteri di backup associati alle macchine virtuali protette.

- Backup di Azure gestisce automaticamente l'archiviazione per l'insieme di credenziali. Scegliere la ridondanza di archiviazione che corrisponde alle esigenze aziendali quando si crea l'insieme di credenziali di Backup.

- Per altre informazioni sulla ridondanza dell'archiviazione, vedere questi articoli sulla [ridondanza geografica](../storage/common/storage-redundancy.md#geo-redundant-storage) [e](../storage/common/storage-redundancy.md#locally-redundant-storage) locale.

## <a name="encryption-settings-in-the-backup-vault"></a>Impostazioni di crittografia nell'insieme di credenziali di Backup

Questa sezione illustra le opzioni disponibili per crittografare i dati di backup archiviati nell'insieme di credenziali per il backup. Backup di Azure usa l'app **Servizio di gestione backup** per accedere Azure Key Vault, ma non all'identità gestita dell'insieme di credenziali di Backup.


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Crittografia dei dati di backup con chiavi gestite dalla piattaforma

Per impostazione predefinita, tutti i dati vengono crittografati usando chiavi gestite dalla piattaforma. Non è necessario eseguire alcuna azione esplicita dall'utente per abilitare questa crittografia. Si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di Backup.

## <a name="create-a-backup-vault"></a>Creare un insieme di credenziali per il backup

Un insieme di credenziali di backup è un'entità di gestione che archivia i punti di ripristino creati nel tempo e fornisce un'interfaccia per eseguire operazioni correlate al backup. come l'esecuzione di backup su richiesta, l'esecuzione di ripristini e la creazione di criteri di backup.

Per creare un insieme di credenziali per il backup, seguire questa procedura.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Creare un insieme di credenziali per il backup

1. Digitare **Insiemi di credenziali di backup** nella casella di ricerca.
1. In **Servizi selezionare** Insiemi di credenziali di **backup.**
1. Nella pagina **Insiemi di credenziali di** backup selezionare **Aggiungi.**
1. Nella scheda **Informazioni di base** in Dettagli **progetto** verificare che sia selezionata la sottoscrizione corretta e quindi scegliere Crea **nuovo gruppo** di risorse. Immettere *myResourceGroup* come nome.

  ![Creare un nuovo gruppo di risorse](./media/backup-vault-overview/new-resource-group.png)

1. In **Dettagli istanza** digitare *myVault come* nome dell'insieme di credenziali di **backup** e scegliere l'area preferita, in questo caso *Stati Uniti* orientali per **Area**.
1. Scegliere ora la **ridondanza di archiviazione.** La ridondanza dell'archiviazione non può essere modificata dopo la protezione degli elementi nell'insieme di credenziali.
1. Se si usa Azure come endpoint di archiviazione di backup primario, è consigliabile continuare a usare l'impostazione **con ridondanza geografica** predefinita.
1. Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure.
1. Altre informazioni sulla [ridondanza](../storage/common/storage-redundancy.md#geo-redundant-storage) geografica [e](../storage/common/storage-redundancy.md#locally-redundant-storage) locale.

  ![Scegliere la ridondanza di archiviazione](./media/backup-vault-overview/storage-redundancy.png)

1. Selezionare il pulsante Rivedi e crea nella parte inferiore della pagina.

    ![Selezionare Rivedi e crea](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Eliminare un insieme di credenziali per il backup

Questa sezione descrive come eliminare un insieme di credenziali di backup. Contiene istruzioni per la rimozione delle dipendenze e l'eliminazione di un insieme di credenziali.

### <a name="before-you-start"></a>Prima di iniziare

Non è possibile eliminare un insieme di credenziali di backup con una delle dipendenze seguenti:

- Non è possibile eliminare un insieme di credenziali che contiene origini dati protette, ad esempio database di Azure per server PostgreSQL.
- Non è possibile eliminare un insieme di credenziali che contiene dati di backup.

Se si tenta di eliminare l'insieme di credenziali senza rimuovere le dipendenze, verranno visualizzati i messaggi di errore seguenti:

>Impossibile eliminare l'insieme di credenziali di backup perché nell'insieme di credenziali sono presenti istanze di backup o criteri di backup esistenti. Eliminare tutte le istanze di backup e i criteri di backup presenti nell'insieme di credenziali e quindi provare a eliminare l'insieme di credenziali.

### <a name="proper-way-to-delete-a-vault"></a>Modo corretto per eliminare un insieme di credenziali

>[!WARNING]
L'operazione seguente è distruttiva e non può essere annullata. Tutti i dati di backup e gli elementi di backup associati al server protetto verranno eliminati definitivamente. Procedere con cautela.

Per eliminare correttamente un insieme di credenziali, è necessario seguire i passaggi nell'ordine seguente:

- Verificare se sono presenti elementi protetti:
  - Passare a **Istanze di backup** nella barra di spostamento a sinistra. Tutti gli elementi elencati qui devono essere eliminati per primi.

Dopo aver completato questi passaggi, è possibile continuare a eliminare l'insieme di credenziali.

### <a name="delete-the-backup-vault"></a>Eliminare l'insieme di credenziali di backup

Quando non sono presenti altri elementi nell'insieme di credenziali, selezionare **Elimina** nel dashboard dell'insieme di credenziali. Verrà visualizzato un testo di conferma che chiede se si vuole eliminare l'insieme di credenziali.

![Eliminare un insieme di credenziali](./media/backup-vault-overview/delete-vault.png)

1. Selezionare **Sì** per verificare che si vuole eliminare l'insieme di credenziali. L'insieme di credenziali viene eliminato. Il portale torna al menu **Nuovo** servizio.

## <a name="monitor-and-manage-the-backup-vault"></a>Monitorare e gestire l'insieme di credenziali per il backup

Questa sezione illustra come usare il dashboard Panoramica dell'insieme di credenziali **di** backup per monitorare e gestire gli insiemi di credenziali di Backup. Il riquadro panoramica contiene due riquadri: **Processi** **e Istanze.**

![Dashboard panoramica](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Gestire le istanze di backup

Nel riquadro **Processi si** ottiene una visualizzazione riepilogata di tutti i processi correlati al backup e al ripristino nell'insieme di credenziali di Backup. La selezione di uno dei numeri in questo riquadro consente di visualizzare altre informazioni sui processi per un tipo di origine dati, un tipo di operazione e uno stato specifici.

![Istanze di backup](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Gestire i processi di backup

Nel riquadro **Istanze di backup** si ottiene una visualizzazione riepilogata di tutte le istanze di backup nell'insieme di credenziali di Backup. La selezione di uno dei numeri in questo riquadro consente di visualizzare altre informazioni sulle istanze di backup per un tipo di origine dati e uno stato di protezione specifici.

![Processi di backup](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare il backup nei database PostgreSQL di Azure](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
