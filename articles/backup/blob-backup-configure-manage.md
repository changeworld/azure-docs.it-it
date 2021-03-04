---
title: Configurare il backup operativo per i BLOB di Azure
description: Informazioni su come configurare e gestire il backup operativo per i BLOB di Azure (in anteprima)
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 0dc490842389ba9286799aef5d37c1cf7c1ba64e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051073"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>Configurare il backup operativo per i BLOB di Azure (in anteprima)

Backup di Azure consente di configurare facilmente il backup operativo per la protezione dei BLOB in blocchi negli account di archiviazione. Questo articolo illustra come configurare il backup operativo su uno o più account di archiviazione usando il portale di Azure. Questo articolo illustra quanto segue:

- Informazioni importanti prima di iniziare
- Creazione di un insieme di credenziali per il backup
- Concessione delle autorizzazioni all'insieme di credenziali per il backup negli account di archiviazione da proteggere
- Creazione di un criterio di backup
- Configurazione del backup operativo su uno o più account di archiviazione
- Effetti sugli account di archiviazione di backup

## <a name="before-you-start"></a>Prima di iniziare

- Il backup operativo dei BLOB è una soluzione di backup locale che mantiene i dati per una durata specificata nell'account di archiviazione di origine. Questa soluzione non mantiene una copia aggiuntiva dei dati nell'insieme di credenziali.
- Questa soluzione consente di conservare i dati per il ripristino per un massimo di 360 giorni. Le durate di conservazione lunghe possono tuttavia causare un tempo maggiore durante l'operazione di ripristino.
- La soluzione può essere usata per eseguire ripristini solo nell'account di archiviazione di origine e può causare la sovrascrittura dei dati.
- Se si elimina un contenitore dall'account di archiviazione chiamando l'operazione Delete Container, il contenitore non può essere ripristinato con un'operazione di ripristino. Anziché eliminare un intero contenitore, eliminare i singoli BLOB se si desidera ripristinarli in un secondo momento. Microsoft consiglia inoltre di abilitare l'eliminazione temporanea per i contenitori, oltre al backup operativo, per proteggersi da eliminazioni accidentali di contenitori.
- Per ulteriori informazioni sugli scenari, sulle limitazioni e sulla disponibilità supportati, fare riferimento alla [matrice di supporto](blob-backup-support-matrix.md) .

## <a name="create-a-backup-vault"></a>Creare un insieme di credenziali per il backup

Un insieme di credenziali per il [backup](backup-vault-overview.md) è un'entità di gestione che archivia i punti di ripristino creati nel tempo e fornisce un'interfaccia per eseguire operazioni correlate al backup. come l'esecuzione di backup su richiesta, l'esecuzione di ripristini e la creazione di criteri di backup. Sebbene il backup operativo dei BLOB sia un backup locale e non "memorizzi" i dati nell'insieme di credenziali, l'insieme di credenziali è necessario per varie operazioni di gestione.

>[!NOTE]
>L'insieme di credenziali per il backup è una nuova risorsa che viene usata per eseguire il backup dei nuovi carichi di lavoro supportati ed è diversa dall'insieme di credenziali dei servizi di ripristino già esistente.

Per istruzioni su come creare un insieme di credenziali per il backup, vedere la documentazione sull'insieme di credenziali per il [backup](backup-vault-overview.md#create-a-backup-vault).

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>Concedere le autorizzazioni all'insieme di credenziali per il backup negli account di archiviazione

Il backup operativo protegge anche l'account di archiviazione (che contiene i BLOB da proteggere) da eventuali eliminazioni accidentali applicando un blocco di eliminazione di proprietà del backup. Questa operazione richiede che l'insieme di credenziali per il backup disponga di determinate autorizzazioni per gli account di archiviazione che devono essere protetti. Per praticità d'uso, queste autorizzazioni sono state consolidate nel ruolo Collaboratore backup account di archiviazione. Seguire le istruzioni riportate di seguito per gli account di archiviazione che devono essere protetti:

1. Nell'account di archiviazione da proteggere passare alla **scheda controllo di accesso (IAM)** nel riquadro di spostamento a sinistra.
1. Selezionare **Aggiungi assegnazioni di ruolo** per assegnare il ruolo richiesto.

    ![Aggiungere assegnazioni di ruolo](./media/blob-backup-configure-manage/add-role-assignments.png)

1. Nel riquadro Aggiungi assegnazione ruolo:

    1. In **ruolo** scegliere **collaboratore backup account di archiviazione**.
    1. In **assegna accesso a** scegliere **utente, gruppo o entità servizio**.
    1. Digitare il **nome dell'insieme di** credenziali di backup che si vuole proteggere nei BLOB in questo account di archiviazione e selezionare lo stesso nei risultati della ricerca.
    1. Al termine, selezionare **Salva**.

        ![Opzioni di assegnazione di ruolo](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Per rendere effettive le assegnazioni di ruolo, attendere fino a 10 minuti.

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

Un criterio di backup determina in genere la conservazione e la pianificazione dei backup. Poiché il backup operativo per i BLOB è di natura continua, non è necessaria una pianificazione per eseguire i backup. Il criterio è essenzialmente necessario per specificare il periodo di memorizzazione. È possibile usare e riutilizzare i criteri di backup per configurare il backup per più account di archiviazione in un insieme di credenziali.

Ecco i passaggi per creare un criterio di backup per il backup operativo dei BLOB:

1. Nell'insieme di credenziali per il backup passare a **criteri di backup** e selezionare **+ Aggiungi** per avviare la creazione di un criterio di backup.

    ![Criteri di backup](./media/blob-backup-configure-manage/backup-policies.png)

1. Nella scheda **nozioni di base** specificare un nome per il criterio di backup e selezionare **BLOB di Azure** come tipo di origine dati. È anche possibile visualizzare i dettagli per l'insieme di credenziali selezionato.

    ![Creare criteri di backup](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >Sebbene venga visualizzata la **ridondanza dell'archiviazione di backup** dell'insieme di credenziali, la ridondanza non si applica realmente al backup operativo dei BLOB perché il backup è di natura locale e nessun dato viene archiviato nell'insieme di credenziali per il backup. L'insieme di credenziali per il backup è l'entità di gestione che consente di gestire la protezione dei BLOB in blocchi negli account di archiviazione.

1. Nella scheda **criteri di backup** è possibile specificare i dettagli di conservazione. Si noterà che esiste già una regola di conservazione denominata **default** con un periodo di conservazione di 30 giorni. Se si desidera modificare la durata del periodo di memorizzazione, utilizzare l'icona **Modifica regola di conservazione** per modificare e specificare la durata per cui si desidera mantenere i dati. È possibile specificare la conservazione fino a 360 giorni.

    ![Scheda criteri di backup](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >Il ripristino di durate lunghe può comportare il completamento delle operazioni di ripristino. Inoltre, il tempo necessario per ripristinare un set di dati si basa sul numero di operazioni di scrittura ed eliminazione effettuate durante il periodo di ripristino. Ad esempio, un account con 1 milione oggetti con 3.000 oggetti aggiunti al giorno e 1.000 oggetti eliminati al giorno richiederà circa due ore per il ripristino fino a un punto di 30 giorni nel passato. Un periodo di conservazione e un ripristino più di 90 giorni in passato non sarebbero consigliati per un account con questa frequenza di modifica.

1. Nel riquadro **Rivedi e crea** verificare tutti i dettagli per il criterio e selezionare **Crea** al termine della creazione del criterio. Una notifica conferma una volta che il criterio di backup è stato creato ed è pronto per essere utilizzato.

    ![Esaminare e creare i criteri](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>Configurare il backup

Il backup dei BLOB viene configurato a livello di account di archiviazione. Tutti i BLOB nell'account di archiviazione sono quindi protetti con il backup operativo.

Per avviare la configurazione del backup:

1. Cercare **centro di backup** nella barra di ricerca.
1. Passare a **Panoramica** e  ->  **backup**.

    ![Panoramica di Centro backup](./media/blob-backup-configure-manage/backup-center-overview.png)

1. Nella scheda **Avvia: Configura backup** scegliere **BLOB di Azure (archiviazione di Azure)** come tipo di origine dati.

    ![Avvio: Configura scheda backup](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. Nella scheda **nozioni di base** specificare **BLOB di Azure (archiviazione di Azure)** come tipo di **origine dati** e selezionare l'insieme di credenziali per il backup a cui si desidera associare gli account di archiviazione. È possibile visualizzare i dettagli dell'insieme di credenziali selezionato nel riquadro.

    ![Scheda Informazioni di base](./media/blob-backup-configure-manage/basics-tab.png)

1. Selezionare quindi i criteri di backup che si desidera utilizzare per specificare la conservazione. È possibile visualizzare i dettagli del criterio selezionato nella parte inferiore della schermata. La colonna archivio dati operativi Mostra il periodo di conservazione definito nei criteri. "Operativo" indica che i dati vengono mantenuti localmente nell'account di archiviazione di origine.

    ![Scegliere i criteri di backup](./media/blob-backup-configure-manage/choose-backup-policy.png)

    È anche possibile creare un nuovo criterio di backup. A tale scopo, selezionare **Crea nuovo** e attenersi alla procedura seguente:

    1. Specificare un nome per il criterio che si vuole creare. Verificare che nelle altre caselle siano visualizzati il tipo di origine dati e il nome dell'insieme di credenziali corretti.
    1. Nella scheda **criteri di backup** selezionare l'icona Modifica regola di conservazione per modificare e specificare la durata per cui si desidera mantenere i dati. È possibile specificare la conservazione fino a 360 giorni. Il ripristino di durate lunghe può comportare il completamento delle operazioni di ripristino.

        ![Crea nuovo criterio di backup](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. Al termine, selezionare **Verifica + crea** per creare i criteri di backup.

1. Successivamente, è necessario scegliere gli account di archiviazione per cui si vuole configurare la protezione dei BLOB. È possibile scegliere più account di archiviazione contemporaneamente e scegliere **Seleziona**.

    Verificare tuttavia che l'insieme di credenziali scelto disponga delle autorizzazioni necessarie per configurare il backup negli account di archiviazione, come descritto in precedenza in [concedere le autorizzazioni all'insieme di credenziali per il backup negli account di archiviazione](#grant-permissions-to-the-backup-vault-on-storage-accounts).

    ![Selezionare le risorse di cui eseguire il backup](./media/blob-backup-configure-manage/select-resources.png)

    Backup controlla se l'insieme di credenziali dispone di autorizzazioni sufficienti per consentire la configurazione del backup negli account di archiviazione selezionati.

    ![Backup convalida le autorizzazioni](./media/blob-backup-configure-manage/validate-permissions.png)

    Se la convalida genera errori (come con uno degli account di archiviazione nello screenshot), passare agli account di archiviazione selezionati e assegnare i ruoli appropriati, come descritto [qui](#grant-permissions-to-the-backup-vault-on-storage-accounts), e selezionare **revalidate (riconvalida**). Per rendere effettive le nuove assegnazioni di ruolo possono essere necessari fino a 10 minuti.

1. Quando la convalida ha esito positivo per tutti gli account di archiviazione selezionati, continuare a **rivedere e configurare** per configurare il backup. Verranno visualizzate le notifiche che informano lo stato della configurazione della protezione e il relativo completamento.

## <a name="effects-on-backed-up-storage-accounts"></a>Effetti sugli account di archiviazione di cui è stato eseguito il backup

Una volta configurato il backup, vengono rilevate le modifiche apportate ai BLOB in blocchi negli account di archiviazione e i dati vengono conservati in base ai criteri di backup. Si noteranno le seguenti modifiche negli account di archiviazione per cui è configurato il backup:

- Nell'account di archiviazione sono abilitate le funzionalità seguenti. Questi possono essere visualizzati nella scheda **protezione dati** dell'account di archiviazione.
  - Ripristino temporizzato per i contenitori: con conservazione come specificato nei criteri di backup
  - Eliminazione temporanea per i BLOB: con conservazione come specificato nei criteri di backup + 5 giorni
  - Controllo delle versioni per i BLOB
  - Feed di modifiche BLOB

  Se per l'account di archiviazione configurato per il backup è già stato attivato il  **ripristino temporizzato per i contenitori** o l' **eliminazione temporanea per i BLOB** (prima della configurazione del backup), il backup garantisce che la conservazione sia almeno definita nei criteri di backup. Pertanto, per ogni proprietà:

  - Se la conservazione nei criteri di backup è superiore alla conservazione originariamente presente nell'account di archiviazione: la conservazione nell'account di archiviazione viene modificata in base ai criteri di backup
  - Se la conservazione nei criteri di backup è inferiore alla conservazione originariamente presente nell'account di archiviazione: la conservazione nell'account di archiviazione viene lasciata invariata alla durata impostata originariamente.

  ![Scheda protezione dati](./media/blob-backup-configure-manage/data-protection.png)

- Un **blocco Delete** viene applicato dal backup nell'account di archiviazione protetto. Il blocco è progettato per la protezione da casi di eliminazione accidentale dell'account di archiviazione. Questo può essere visualizzato in blocchi dell' **account di archiviazione**  >  .

    ![Blocchi di eliminazione](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>Gestire il backup operativo

Per la gestione di tutti i backup, è possibile usare il centro di backup come singolo riquadro di vetro. Per quanto riguarda il backup operativo per i BLOB di Azure, è possibile usare il centro di backup per eseguire le operazioni seguenti:

- Come è stato illustrato in precedenza, è possibile usarlo per creare criteri e insiemi di credenziali di backup. È anche possibile visualizzare tutti gli insiemi di credenziali e i criteri nelle sottoscrizioni selezionate.
- Backup Center offre un modo semplice per monitorare lo stato di protezione degli account di archiviazione protetti, nonché gli account di archiviazione per cui il backup non è attualmente configurato.
- È possibile configurare il backup per qualsiasi account di archiviazione usando il pulsante **+ backup** .
- È possibile avviare i ripristini usando il pulsante **Ripristina** e tenere traccia dei ripristini usando i **processi di backup**. Per altre informazioni sull'esecuzione dei ripristini, vedere [ripristinare i BLOB di Azure](blob-backup-support-matrix.md).
- Analizzare l'utilizzo del backup utilizzando i report di backup.

    ![Centro backup](./media/blob-backup-configure-manage/backup-center.png)

Per ulteriori informazioni, vedere [Panoramica di Centro backup (anteprima)](backup-center-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare i BLOB di Azure](blob-restore.md)
