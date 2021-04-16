---
title: Eseguire il backup di condivisioni file di Azure nel portale di Azure
description: Informazioni su come usare il portale di Azure per eseguire il backup di condivisioni file di Azure nell'insieme di credenziali di Servizi di ripristino
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: e7f44a71388468be432bdfcb0eb2bf67c0fcc8ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519936"
---
# <a name="back-up-azure-file-shares"></a>Eseguire il backup di condivisioni file di Azure

Questo articolo illustra come eseguire il backup di [condivisioni file di Azure](../storage/files/storage-files-introduction.md) dal portale di Azure.

In questo articolo si apprenderà come:

* Creare un insieme di credenziali dei servizi di ripristino.
* Configurare il backup dall'insieme di credenziali di Servizi di ripristino
* Configurare il backup dal riquadro di condivisione file
* Eseguire un processo di backup su richiesta per creare un punto di ripristino

## <a name="prerequisites"></a>Prerequisiti

* [Informazioni](azure-file-share-backup-overview.md) sulla soluzione di backup basata su snapshot di condivisione file di Azure.
* Assicurarsi che la condivisione file sia presente in uno dei tipi [di account di archiviazione supportati.](azure-file-share-support-matrix.md)
* Identificare o creare un insieme [di credenziali di Servizi](#create-a-recovery-services-vault) di ripristino nella stessa area dell'account di archiviazione che ospita la condivisione file.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Configurare il backup dall'insieme di credenziali di Servizi di ripristino

La procedura seguente illustra come configurare il backup per più condivisioni file dal riquadro insieme di credenziali di Servizi di ripristino:

1. Nel [portale di Azure](https://portal.azure.com/)aprire l'insieme di credenziali di Servizi di ripristino da usare per configurare il backup per la condivisione file.

1. Nel riquadro **Insieme di credenziali di Servizi** di ripristino selezionare **+Backup** dal menu nella parte superiore.

   ![Insieme di credenziali dei servizi di ripristino](./media/backup-afs/recovery-services-vault.png)

    1. Nel riquadro **Obiettivo di backup** impostare Dove è in esecuzione il carico di **lavoro?** su **Azure** selezionando l'opzione **Azure** nell'elenco a discesa.

          ![Scegliere Azure come carico di lavoro](./media/backup-afs/backup-goal.png)

    2. In **Cosa si vuole eseguire il backup?** selezionare Condivisione file di **Azure** nell'elenco a discesa.

          ![Selezionare Condivisione file di Azure](./media/backup-afs/select-azure-file-share.png)

    3. Selezionare **Backup** per registrare l'estensione condivisione file di Azure nell'insieme di credenziali.

          ![Selezionare Backup per associare la condivisione file di Azure all'insieme di credenziali](./media/backup-afs/register-extension.png)

1. Dopo aver selezionato **Backup,** viene visualizzato **il riquadro Backup.** Per selezionare l'account di archiviazione che ospita la  condivisione file da proteggere, selezionare il testo Seleziona collegamento sotto la **casella di testo Account di** archiviazione.

   ![Scegliere il collegamento Seleziona](./media/backup-afs/choose-select-link.png)

1. A **destra verrà visualizzato** il riquadro Seleziona account di archiviazione, che elenca un set di account di archiviazione supportati individuati. Sono associati a questo insieme di credenziali o sono presenti nella stessa area dell'insieme di credenziali, ma non sono ancora associati ad alcun insieme di credenziali di Servizi di ripristino.

1. Nell'elenco degli account di archiviazione individuati selezionare un account e scegliere **OK**.

   ![Selezionare uno degli account di archiviazione individuati](./media/backup-afs/select-discovered-storage-account.png)

1. Il passaggio successivo consiste nel selezionare le condivisioni file di cui si vuole eseguire il backup. Selezionare il **pulsante** Aggiungi nella **sezione Condivisione file per il backup.**

   ![Selezionare le condivisioni file di cui eseguire il backup](./media/backup-afs/select-file-shares-to-back-up.png)

1. Il **riquadro di contesto Seleziona condivisioni** file si apre a destra. Azure cerca nell'account di archiviazione condivisioni file di cui è possibile eseguire il backup. Se le condivisioni file sono state aggiunte di recente e non vengono visualizzate nell'elenco, lasciare un po' di tempo per la visualizzazione delle condivisioni file.

1. **Nell'elenco Seleziona condivisioni file** selezionare una o più condivisioni file di cui si vuole eseguire il backup. Selezionare **OK**.

   ![Selezionare le condivisioni file](./media/backup-afs/select-file-shares.png)

1. Per scegliere un criterio di backup per la condivisione file, sono disponibili tre opzioni:

   * Scegliere i criteri predefiniti.<br>
   Questa opzione consente di abilitare il backup giornaliero che verrà mantenuto per 30 giorni. Se nell'insieme di credenziali non sono presenti criteri di backup, il riquadro di backup viene aperto con le impostazioni predefinite dei criteri. Se si vogliono scegliere le impostazioni predefinite, è possibile selezionare direttamente **Abilita backup**.

   * Creare nuovi criteri <br>

      1. Per creare un nuovo criterio di backup per la condivisione file, selezionare il testo del collegamento sotto l'elenco a discesa nella sezione **Criteri di** backup.<br>

         ![Creare nuovi criteri](./media/backup-afs/create-new-policy.png)

      1. A **destra verrà visualizzato** il riquadro Contesto criteri di backup. Specificare un nome di criterio nella casella di testo e scegliere il periodo di conservazione in base alle esigenze. Per impostazione predefinita, è abilitata solo l'opzione di conservazione giornaliera. Se si vuole avere una conservazione settimanale, mensile o annuale, selezionare la casella di controllo corrispondente e specificare il valore di conservazione desiderato.

      1. Dopo aver specificato i valori di conservazione e un nome di criterio valido, selezionare **OK.**<br>

         ![Assegnare il nome del criterio e i valori di conservazione](./media/backup-afs/policy-name.png)

   * Scegliere uno dei criteri di backup esistenti <br>

      Per scegliere uno dei criteri di backup esistenti per la configurazione della protezione, selezionare i criteri desiderati dall'elenco a discesa **Criteri** di backup.<br>

      ![Scegliere i criteri esistenti](./media/backup-afs/choose-existing-policy.png)

1. Selezionare **Abilita backup** per avviare la protezione della condivisione file.

   ![Scegliere Abilita backup](./media/backup-afs/enable-backup.png)

Dopo aver impostato un criterio di backup, viene creato uno snapshot delle condivisioni file all'ora pianificata. Anche il punto di ripristino viene mantenuto per il periodo scelto.

>[!NOTE]
>Backup di Azure ora supporta i criteri con conservazione giornaliera/settimanale/mensile/annuale per il backup di condivisione file di Azure.

## <a name="configure-backup-from-the-file-share-pane"></a>Configurare il backup dal riquadro condivisione file

I passaggi seguenti illustrano come configurare il backup per singole condivisioni file dal rispettivo riquadro di condivisione file:

1. Nel portale di Azure aprire [l'account](https://portal.azure.com/)di archiviazione che ospita la condivisione file di cui si vuole eseguire il backup.

1. Nell'account di archiviazione selezionare il riquadro condivisioni **file**. È anche possibile passare a **Condivisioni file** tramite il sommario dell'account di archiviazione.

   ![Account di archiviazione](./media/backup-afs/storage-account.png)

1. Nell'elenco delle condivisioni file dovrebbero essere presenti tutte le condivisioni file presenti nell'account di archiviazione. Selezionare la condivisione file di cui si vuole eseguire il backup.

   ![Elenco di condivisioni file](./media/backup-afs/file-shares-list.png)

1. Selezionare **Backup** nella **sezione Operazioni** del riquadro Condivisione file. Il **riquadro Configura backup** verrà caricato a destra.

   ![Configurare il riquadro di backup](./media/backup-afs/configure-backup.png)

1. Per la selezione dell'insieme di credenziali di Servizi di ripristino, eseguire una delle operazioni seguenti:

    * Se si dispone già di  un insieme di credenziali, selezionare il pulsante di opzione Seleziona insieme di credenziali di Servizi di ripristino esistente e scegliere uno degli insiemi di credenziali esistenti dal menu a discesa **Nome** insieme di credenziali.

       ![Selezionare un insieme di credenziali esistente](./media/backup-afs/select-existing-vault.png)

    * Se non si ha un insieme di credenziali, selezionare il pulsante di opzione **Crea nuovo** insieme di credenziali di Servizi di ripristino. Specificare un nome per l'insieme di credenziali. Viene creato nella stessa area della condivisione file. Per impostazione predefinita, l'insieme di credenziali viene creato nello stesso gruppo di risorse della condivisione file. Se si vuole scegliere un gruppo di risorse diverso, selezionare **Crea** nuovo collegamento sotto l'elenco a discesa **Tipo** di risorsa e specificare un nome per il gruppo di risorse. Selezionare **OK** per continuare.

       ![Creare un nuovo insieme di credenziali](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Se l'account di archiviazione è registrato con un insieme di credenziali o sono presenti poche condivisioni protette all'interno dell'account di archiviazione che ospita la condivisione file che si sta tentando di proteggere, il nome dell'insieme di credenziali di Servizi di ripristino verrà pre-popolato e non sarà possibile modificarlo Altre informazioni [qui.](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-)

1. Per la **selezione criteri di** backup, eseguire una delle operazioni seguenti:

    * Lasciare il valore predefinito. Verranno pianificati backup giornalieri con una conservazione di 30 giorni.

    * Selezionare un criterio di backup esistente, se necessario, dal menu a discesa **Criteri** di backup.

       ![Scegliere i criteri di backup](./media/backup-afs/choose-backup-policy.png)

    * Creare un nuovo criterio con conservazione giornaliera/settimanale/mensile/annuale in base alle esigenze.  

         1. Selezionare il **testo del collegamento Crea un nuovo** criterio.

         2. A **destra verrà visualizzato** il riquadro Contesto criteri di backup. Specificare un nome di criterio nella casella di testo e scegliere il periodo di conservazione in base alle esigenze. Per impostazione predefinita, è abilitata solo l'opzione di conservazione giornaliera. Se si vuole avere una conservazione settimanale, mensile o annuale, selezionare la casella di controllo corrispondente e specificare il valore di conservazione desiderato.

         3. Dopo aver specificato i valori di conservazione e un nome di criterio valido, selezionare **OK.**

            ![Creare nuovi criteri di backup](./media/backup-afs/create-new-backup-policy.png)

1. Selezionare **Abilita backup** per iniziare a proteggere la condivisione file.

   ![Selezionare Abilita backup](./media/backup-afs/select-enable-backup.png)

1. È possibile tenere traccia dello stato di avanzamento della configurazione nelle notifiche del portale o monitorando i processi di backup nell'insieme di credenziali in uso per proteggere la condivisione file.

   ![Notifiche del portale](./media/backup-afs/portal-notifications.png)

1. Al termine dell'operazione di configurazione del backup, selezionare **Backup** nella **sezione Operazioni** del riquadro condivisione file. Il riquadro di contesto che **elenca Vault Essentials** verrà caricato a destra. Da qui è possibile attivare le operazioni di backup e ripristino su richiesta.

   ![Elementi di base dell'insieme di credenziali](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Eseguire un processo di backup su richiesta

In alcuni casi, può essere necessario generare uno snapshot di backup o un punto di ripristino al di fuori degli orari pianificati nei criteri di backup. Un motivo comune per generare un backup su richiesta è subito dopo aver configurato i criteri di backup. In base alla pianificazione nei criteri di backup, potrebbero essere presenti ore o giorni prima che venga creato uno snapshot. Per proteggere i dati fino a quando non si attiva il criterio di backup, avviare un backup su richiesta. La creazione di un backup su richiesta è spesso necessaria prima di apportare modifiche pianificate alle condivisioni file.

### <a name="from-the-recovery-services-vault"></a>Dall'insieme di credenziali di Servizi di ripristino

1. Aprire l'insieme di credenziali di Servizi di ripristino usato per eseguire il backup della condivisione file. Nel riquadro **Panoramica** selezionare **Elementi di backup nella** sezione **Elementi** protetti.

   ![Selezionare Elementi di backup](./media/backup-afs/backup-items.png)

1. Dopo aver selezionato **Elementi di backup,** accanto al riquadro Panoramica viene visualizzato un nuovo riquadro in cui sono elencati tutti i tipi **di** gestione dei backup. 

   ![Elenco dei tipi di gestione dei backup](./media/backup-afs/backup-management-types.png)

1. **Nell'elenco Tipo di gestione** backup selezionare Archiviazione di Azure **(File di Azure)**. Verrà visualizzato un elenco di tutte le condivisioni file e degli account di archiviazione corrispondenti di cui è stato eseguito il backup usando questo insieme di credenziali.

   ![Archiviazione di Azure (File di Azure)](./media/backup-afs/azure-files-backup-items.png)

1. Nell'elenco delle condivisioni file di Azure selezionare la condivisione file desiderata. Vengono **visualizzati i dettagli dell'elemento** di backup. Scegliere **Backup adesso** dal menu Elemento **di backup**. Poiché questo processo di backup è su richiesta, al punto di ripristino non sono associati criteri di conservazione.

   ![Selezionare Backup ora](./media/backup-afs/backup-now.png)

1. Verrà **visualizzato il riquadro Backup** adesso. Specificare l'ultimo giorno di conservazione del punto di ripristino. È possibile avere una conservazione massima di 10 anni per un backup su richiesta.

   ![Scegliere la data di conservazione](./media/backup-afs/retention-date.png)

1. Selezionare **OK** per confermare il processo di backup su richiesta eseguito.

1. Monitorare le notifiche del portale per tenere traccia del completamento dell'esecuzione del processo di backup. È possibile monitorare lo stato del processo nel dashboard dell'insieme di credenziali. Selezionare **Processi di backup** in  >  **corso.**

### <a name="from-the-file-share-pane"></a>Dal riquadro di condivisione file

1. Aprire il riquadro Panoramica **della** condivisione file per cui si vuole eseguire un backup su richiesta.

1. Selezionare **Backup** nella **sezione Operazione.** Il riquadro di contesto che **elenca Vault Essentials** verrà caricato a destra. Selezionare **Backup ora** per eseguire un backup su richiesta.

   ![Selezionare Backup adesso](./media/backup-afs/select-backup-now.png)

1. Verrà **aperto il riquadro Backup** now (Backup ora). Specificare la conservazione per il punto di ripristino. È possibile avere una conservazione massima di 10 anni per un backup su richiesta.

   ![Mantieni data backup](./media/backup-afs/retain-backup-date.png)

1. Selezionare **OK** per confermare.

>[!NOTE]
>Backup di Azure blocca l'account di archiviazione quando si configura la protezione per qualsiasi condivisione file nell'account corrispondente. In questo modo viene garantita la protezione dall'eliminazione accidentale di un account di archiviazione con condivisioni file di cui è stato eseguito il backup.

## <a name="best-practices"></a>Procedure consigliate

* Non eliminare gli snapshot creati da Backup di Azure. L'eliminazione degli snapshot può comportare la perdita di punti di ripristino e/o errori di ripristino.

* Non rimuovere il blocco sull'account di archiviazione Backup di Azure. Se si elimina il blocco, l'account di archiviazione sarà in grado di eliminare accidentalmente e, se viene eliminato, gli snapshot o i backup andranno persi.

## <a name="next-steps"></a>Passaggi successivi

Viene illustrato come:

* [Ripristinare condivisioni file di Azure](restore-afs.md)
* [Gestire i backup di condivisioni file di Azure](manage-afs-backup.md)
