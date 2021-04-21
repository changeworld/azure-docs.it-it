---
title: Eliminare un insieme Microsoft Azure di credenziali di Servizi di ripristino
description: Questo articolo illustra come rimuovere le dipendenze e quindi eliminare un insieme di Backup di Azure di servizi di ripristino.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: bb6be070ac0fb408ac37c8ae7b003b54da5d6dea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773658"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Eliminare un insieme Backup di Azure di servizi di ripristino

Questo articolo descrive come eliminare un insieme di credenziali [Backup di Azure](backup-overview.md) di Servizi di ripristino. Contiene istruzioni per rimuovere le dipendenze e quindi eliminare un insieme di credenziali.

## <a name="before-you-start"></a>Prima di iniziare

Non è possibile eliminare un insieme di credenziali di Servizi di ripristino con una delle seguenti dipendenze:

- Non è possibile eliminare un insieme di credenziali che contiene origini dati protette, ad esempio macchine virtuali IaaS, database SQL e condivisioni file di Azure.
- Non è possibile eliminare un insieme di credenziali contenente dati di backup. Una volta eliminati, i dati di backup entrano nello stato di eliminazione temporanea.
- Non è possibile eliminare un insieme di credenziali che contiene dati di backup nello stato di eliminazione soft.
- Non è possibile eliminare un insieme di credenziali con account di archiviazione registrati.

Se si tenta di eliminare l'insieme di credenziali senza rimuovere le dipendenze, verrà visualizzato uno dei messaggi di errore seguenti:

- Non è possibile eliminare l'insieme di credenziali perché contiene risorse esistenti. Assicurarsi che a questo insieme di credenziali non siano associati elementi di backup, server protetti o server di gestione di backup. Annullare la registrazione dei contenitori seguenti associati a questo insieme di credenziali prima di procedere all'eliminazione.

- L'insieme di credenziali di Servizi di ripristino non può essere eliminato perché sono presenti elementi di backup eliminati temporaneamente nell'insieme di credenziali. Gli elementi eliminati temporaneamente vengono eliminati definitivamente dopo 14 giorni dall'operazione di eliminazione. Provare a eliminare l'insieme di credenziali dopo che gli elementi di backup sono stati eliminati definitivamente e nell'insieme di credenziali non è rimasto alcun elemento nello stato di eliminazione temporaneamente. Per altre informazioni, vedere [Eliminazione Backup di Azure](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>Modo appropriato per eliminare un insieme di credenziali

>[!WARNING]
>L'operazione seguente è distruttiva e non può essere annullata. Tutti i dati di backup e gli elementi di backup associati al server protetto verranno eliminati definitivamente. Procedere con cautela.

Per eliminare correttamente un insieme di credenziali, è necessario seguire i passaggi nell'ordine seguente:

- **Passaggio 1:** Disabilitare la funzionalità di eliminazione temporaneamente. [Vedere qui per](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) la procedura per disabilitare l'eliminazione temporaneamente.

- **Passaggio 2:** dopo aver disabilitato l'eliminazione temporanea, verificare se in precedenza sono presenti elementi nello stato eliminazione temporanea. Se sono presenti elementi in stato di eliminazione soft, è necessario annullare *l'eliminazione* *ed eliminarli* di nuovo. [Seguire questa procedura per](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) trovare gli elementi di eliminazione temporaneamente ed eliminarli in modo permanente.

- **Passaggio 3:** è necessario controllare tutte le tre posizioni seguenti per verificare se sono presenti elementi protetti:

  - **Elementi protetti dal cloud:** passare al menu del dashboard dell'insieme di credenziali > **elementi di backup**. Tutti gli elementi elencati qui devono essere rimossi con **Arresta backup** o Elimina dati **di backup** insieme ai relativi dati di backup.  [Seguire questa procedura](#delete-protected-items-in-the-cloud) per rimuovere tali elementi.
  - **SQL Server:** passare al menu del dashboard dell'insieme di credenziali > **server protetti**  >  **dell'infrastruttura di backup**. In Server protetti selezionare il server di cui si vuole annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server. Fare clic con il pulsante destro del mouse sul server protetto e **scegliere Annulla registrazione**.
  - **Server protetti da MARS:** passare al menu del dashboard dell'insieme di credenziali > **server protetti**  >  **dell'infrastruttura di backup**. Se si dispone di server protetti da MARS, tutti gli elementi elencati qui devono essere eliminati insieme ai relativi dati di backup. [Seguire questa procedura per](#delete-protected-items-on-premises) eliminare i server protetti da MARS.
  - **Server di gestione MABS o DPM:** passare al menu del dashboard dell'insieme di credenziali > **server** di  >  **gestione del backup dell'infrastruttura di backup**. Se si dispone di DPM o server di Backup di Azure (MABS), tutti gli elementi elencati qui devono essere eliminati o annullati insieme ai relativi dati di backup. [Seguire questa procedura per](#delete-protected-items-on-premises) eliminare i server di gestione.

- **Passaggio 4:** è necessario assicurarsi che tutti gli account di archiviazione registrati siano eliminati. Passare al menu del dashboard dell'insieme di credenziali > **account di archiviazione dell'infrastruttura**  >  **di backup**. Se sono presenti account di archiviazione elencati qui, è necessario annullare la registrazione di tutti gli account. Per informazioni su come annullare la registrazione dell'account, vedere [Annullare la registrazione di un account di archiviazione.](manage-afs-backup.md#unregister-a-storage-account)
- **Passaggio 5:** Assicurarsi che non siano presenti endpoint privati creati per l'insieme di credenziali. Passare al menu del dashboard dell'insieme di credenziali > Connessioni **endpoint** privato in "Impostazioni" > se nell'insieme di credenziali sono state create o tentate connessioni endpoint privato, assicurarsi che siano state rimosse prima di procedere con l'eliminazione dell'insieme di credenziali. 

Dopo aver completato questi passaggi, è possibile continuare a eliminare l'insieme [di credenziali](#delete-the-recovery-services-vault).

Se non sono presenti elementi protetti in locale o nel cloud, ma viene comunque visualizzato l'errore di eliminazione dell'insieme di credenziali, seguire la procedura descritta in Eliminare l'insieme di credenziali di Servizi di ripristino [usando](#delete-the-recovery-services-vault-by-using-azure-resource-manager) Azure Resource Manager

## <a name="delete-protected-items-in-the-cloud"></a>Eliminare elementi protetti nel cloud

Leggere prima di tutto la sezione **[Prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

Per arrestare la protezione ed eliminare i dati di backup, seguire questa procedura:

1. Dal portale passare a Insieme di credenziali di **Servizi di** ripristino e quindi a Elementi **di backup.** Nell'elenco **Tipo** di gestione backup selezionare quindi gli elementi protetti nel cloud, ad esempio Macchine virtuali di Azure, Archiviazione di Azure [servizio File di Azure] o SQL Server in Macchine virtuali di Azure.

    ![Selezionare il tipo di backup.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Verrà visualizzato un elenco di tutti gli elementi per la categoria. Fare clic con il pulsante destro del mouse per selezionare l'elemento di backup. A seconda che l'elemento di backup sia protetto o meno, nel menu viene visualizzato il riquadro **Interrompi** backup o Elimina **dati di** backup.

    - Se viene visualizzato il riquadro **Arresta** backup, selezionare Elimina dati **di backup** dal menu a discesa. Immettere il nome dell'elemento di backup (questo campo fa distinzione tra maiuscole e minuscole) e quindi selezionare un motivo dal menu a discesa. Immettere i commenti, se presenti. Selezionare quindi **Interrompi backup.**

        ![Riquadro Arresta backup.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Se viene **visualizzato il** riquadro Elimina dati di backup, immettere il nome dell'elemento di backup (questo campo fa distinzione tra maiuscole e minuscole) e quindi selezionare un motivo dal menu a discesa. Immettere i commenti, se presenti. Quindi, selezionare **Elimina**.

         ![Riquadro Elimina dati di backup.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   Questa opzione elimina i backup pianificati, elimina anche i backup su richiesta.
3. Selezionare **l'icona** Notifica: ![ icona Notifica.](./media/backup-azure-delete-vault/messages.png) Al termine del processo, il servizio visualizza il messaggio seguente: Arresto del backup ed eliminazione dei dati *di backup per "* Elemento di backup *".* *L'operazione è stata completata correttamente.*
4. Scegliere **Aggiorna** dal menu **Elementi di backup** per assicurarsi che l'elemento di backup sia stato eliminato.

      ![Pagina Elimina elementi di backup.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Eliminare elementi protetti in locale

Leggere prima di tutto la **[sezione Prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

1. Nel menu del dashboard dell'insieme di credenziali selezionare **Infrastruttura di backup**.
2. A seconda dello scenario locale, scegliere una delle opzioni seguenti:

      - Per MARS selezionare **Server protetti e** quindi Backup di Azure **Agent.** Selezionare quindi il server da eliminare.

        ![Per MARS, selezionare l'insieme di credenziali per aprire il dashboard.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Per MABS o DPM selezionare Backup Management Servers ( **Server di gestione backup**). Selezionare quindi il server da eliminare.

          ![Per MABS o DPM, selezionare l'insieme di credenziali per aprire il dashboard.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Verrà **visualizzato il** riquadro Elimina con un messaggio di avviso.

     ![Riquadro di eliminazione.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Esaminare il messaggio di avviso e le istruzioni nella casella di controllo del consenso.
    > [!NOTE]
    >
    >- Se il server protetto è sincronizzato con i servizi di Azure e gli elementi di backup esistono, la casella di controllo del consenso visualizza il numero di elementi di backup dipendenti e il collegamento per visualizzare gli elementi di backup.
    >- Se il server protetto non è sincronizzato con i servizi di Azure ed esistono elementi di backup, la casella di controllo del consenso visualizza solo il numero di elementi di backup.
    >- Se non sono presenti elementi di backup, la casella di controllo del consenso richiederà l'eliminazione.

4. Selezionare la casella di controllo del consenso e quindi selezionare **Elimina.**

5. Selezionare **l'icona di** notifica ![ elimina i dati di ](./media/backup-azure-delete-vault/messages.png) backup. Al termine dell'operazione, il servizio visualizza il messaggio Arresto del backup ed eliminazione dei dati *di backup per "Elemento di backup".* *L'operazione è stata completata correttamente.*
6. Scegliere **Aggiorna** dal menu **Elementi di backup** per assicurarsi che l'elemento di backup sia stato eliminato.

>[!NOTE]
>Se si elimina un elemento protetto locale da un portale che contiene dipendenze, verrà visualizzato un avviso che indica che l'eliminazione della registrazione del server è un'operazione distruttiva e non può essere annullata. Tutti i dati di backup (punti di ripristino necessari per ripristinare i dati) e gli elementi di backup associati al server protetto verranno eliminati definitivamente".

Al termine di questo processo, è possibile eliminare gli elementi di backup dalla console di gestione:

- [Eliminare elementi di backup dalla console di gestione mars](#delete-backup-items-from-the-mars-management-console)
- [Eliminare gli elementi di backup dal server di Backup di Microsoft Windows o dalla console di gestione DPM](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Eliminare gli elementi di backup dalla console di gestione di MARS

>[!NOTE]
>Se il computer di origine è stato eliminato o perso senza arrestare il backup, il successivo backup pianificato avrà esito negativo. Il punto di ripristino precedente scade in base ai criteri, ma l'ultimo punto di recupero singolo viene sempre mantenuto fino a quando non si arresta il backup ed elimina i dati. A tale scopo, seguire i passaggi descritti in [questa sezione.](#delete-protected-items-on-premises)

1. Aprire la console di gestione di MARS, passare **al riquadro** Azioni e selezionare **Pianifica backup.**
2. Nella pagina **Modifica o arresta un backup pianificato** selezionare Interrompi l'uso di questa pianificazione di backup ed eliminare tutti i backup **archiviati.** Quindi selezionare **Avanti**.

    ![Modificare o arrestare un backup pianificato.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Nella pagina **Arresta un backup pianificato** selezionare **Fine**.

    ![Arrestare un backup pianificato.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Viene richiesto di immettere un PIN di sicurezza (numero di identificazione personale), che è necessario generare manualmente. A tale scopo, accedere prima di tutto al portale di Azure.
5. Passare a **Proprietà delle impostazioni dell'insieme**  >  **di credenziali di Servizi** di  >  **ripristino**.
6. In **PIN di sicurezza** selezionare **Genera**. Copiare questo PIN. Il PIN è valido solo per cinque minuti.
7. Nella console di gestione incollare il PIN e quindi selezionare **OK.**

    ![Generare un PIN di sicurezza.](./media/backup-azure-delete-vault/security-pin.png)

8. Nella pagina **Modifica stato backup** viene visualizzato il messaggio seguente: I dati di backup eliminati verranno conservati per *14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente.*  

    ![Eliminare l'infrastruttura di backup.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Dopo aver eliminato gli elementi di backup locali, seguire i passaggi successivi dal portale.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Eliminare gli elementi di backup dalla console di gestione MABS o DPM

>[!NOTE]
>Se il computer di origine è stato eliminato o perso senza arrestare il backup, il backup pianificato successivo avrà esito negativo. Il punto di ripristino precedente scade in base ai criteri, ma l'ultimo singolo punto di ripristino viene sempre mantenuto fino a quando non si arresta il backup e si eliminano i dati. A tale scopo, seguire la procedura descritta in [questa sezione.](#delete-protected-items-on-premises)

Esistono due metodi che è possibile usare per eliminare gli elementi di backup dalla console di gestione MABS o DPM.

#### <a name="method-1"></a>Metodo 1

Per arrestare la protezione ed eliminare i dati di backup, seguire questa procedura:

1. Aprire il Console amministrazione DPM e quindi selezionare **Protezione** sulla barra di spostamento.
2. Nel riquadro informazioni selezionare il membro del gruppo protezione dati che si desidera rimuovere. Fare clic con il pulsante destro del mouse **per selezionare l'opzione Arresta protezione dei membri del** gruppo.
3. Nella finestra **di dialogo** Arresta protezione selezionare Elimina **dati protetti** e quindi selezionare la casella di controllo Elimina spazio **di** archiviazione online. Selezionare quindi **Arresta protezione**.

    ![Selezionare Elimina dati protetti nel riquadro Arresta protezione.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Lo stato del membro protetto cambia *in Replica inattiva disponibile.*

4. Fare clic con il pulsante destro del mouse sul gruppo protezione dati inattivo e **scegliere Rimuovi protezione dati inattiva.**

    ![Rimuovere la protezione inattiva.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Nella finestra **Elimina protezione dati inattiva** selezionare la **casella di controllo Elimina** spazio di archiviazione online e quindi selezionare **OK.**

    ![Eliminare l'archiviazione online.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metodo 2

Aprire la **console di gestione di MABS** o **DPM.** In **Selezionare il metodo di protezione** dei dati deselezionare la casella di controllo  **Protezione** online.

  ![Selezionare il metodo di protezione dati.](./media/backup-azure-delete-vault/data-protection-method.png)

Dopo aver eliminato gli elementi di backup locali, seguire i passaggi successivi dal portale.

## <a name="delete-the-recovery-services-vault"></a>Eliminare l'insieme di credenziali di Servizi di ripristino

1. Dopo aver rimosso tutte le dipendenze, scorrere fino al riquadro **Informazioni di** base nel menu dell'insieme di credenziali.
2. Verificare che non siano elencati elementi di backup, server di gestione di backup o elementi replicati. Se nell'insieme di credenziali sono ancora presenti elementi, vedere la [sezione Prima di](#before-you-start) iniziare.

3. Quando non sono presenti altri elementi nell'insieme di credenziali, selezionare **Elimina** nel dashboard dell'insieme di credenziali.

    ![Selezionare Elimina nel dashboard dell'insieme di credenziali.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selezionare **Sì per** verificare che si voglia eliminare l'insieme di credenziali. L'insieme di credenziali viene eliminato. Il portale torna al menu **Nuovo** servizio.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Eliminare l'insieme di credenziali di Servizi di ripristino con PowerShell

Leggere prima di tutto la **[sezione Prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

Per arrestare la protezione ed eliminare i dati di backup:

- Se si usa SQL nel backup di macchine virtuali di Azure e si è abilitata la protezione automatica per le istanze di SQL, disabilitare prima la protezione automatica.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [Altre informazioni](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) su come disabilitare la protezione per un Backup di Azure protetto.

- Arrestare la protezione ed eliminare i dati per tutti gli elementi protetti da backup nel cloud (ad esempio: macchina virtuale IaaS, condivisione file di Azure e così via):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Altre informazioni](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   informazioni su disabilita la protezione per un elemento protetto da backup.

- Per file e cartelle locali protetti tramite il backup di Backup di Azure Agent (MARS) in Azure, usare il comando di PowerShell seguente per eliminare i dati di cui è stato eseguito il backup da ogni modulo di PowerShell mars:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Successivamente, verrà visualizzato il prompt seguente:

    *Backup di Microsoft Azure si è certi di voler rimuovere questo criterio di backup? I dati di backup eliminati verranno conservati per 14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente. <br/> [Y] Sì [A] Sì a Tutti [N] No [L] No a Tutti [S] Sospendi [?] Guida (il valore predefinito è "Y"):*

- Per i computer locali protetti con MABS (Backup di Microsoft Azure Server) o DPM (System Center Data Protection Manager) in Azure, usare il comando seguente per eliminare i dati di cui è stato eseguito il backup in Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Successivamente, verrà visualizzato il prompt seguente:

   *Backup di Microsoft Azure* Si è certi di voler rimuovere questo criterio di backup? I dati di backup eliminati verranno conservati per 14 giorni. Dopo tale periodo, verranno eliminati definitivamente. <br/>
   [Y] Sì [A] Sì a Tutti [N] No [L] No a Tutti [S] Sospendi [?] Guida (il valore predefinito è "Y") :*

Dopo aver eliminato i dati di cui è stato eseguito il backup, annullare la registrazione di tutti i contenitori e i server di gestione locali.

- Per file e cartelle locali protetti tramite il backup di Backup di Azure Agent (MARS) in Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [Altre informazioni](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) sull'annullamento della registrazione di un windows server o di un altro contenitore dall'insieme di credenziali.

- Per i computer locali protetti tramite MABS (Backup di Microsoft Azure Server) o da DPM ad Azure (System Center Gestione protezione dati:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [Altre informazioni](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) sull'annullamento della registrazione di un contenitore di gestione di backup dall'insieme di credenziali.

Dopo aver eliminato definitivamente i dati di cui è stato eseguito il backup e aver annullamento la registrazione di tutti i contenitori, procedere con l'eliminazione dell'insieme di credenziali.

Per eliminare un insieme di credenziali dei servizi di ripristino:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Altre informazioni sull'eliminazione](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) di un insieme di credenziali di Servizi di ripristino.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Eliminare l'insieme di credenziali di Servizi di ripristino tramite l'interfaccia della riga di comando

Leggere prima di tutto la sezione **[Prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

> [!NOTE]
> Attualmente, Backup di Azure'interfaccia della riga di comando supporta solo la gestione dei backup delle macchine virtuali di Azure, quindi il comando seguente per eliminare l'insieme di credenziali funziona solo se l'insieme di credenziali contiene backup di macchine virtuali di Azure. Non è possibile eliminare un insieme di credenziali usando l'Backup di Azure comando, se l'insieme di credenziali contiene elementi di backup di tipo diverso da Macchine virtuali di Azure.

Per eliminare un insieme di credenziali di Servizi di ripristino esistente, seguire questa procedura:

- Per arrestare la protezione ed eliminare i dati di backup

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Per altre informazioni, vedere questo [articolo.](/cli/azure/backup/protection#az_backup_protection_disable)

- Eliminare un insieme di credenziali di Servizi di ripristino esistente:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Per altre informazioni, vedere questo [articolo](/cli/azure/backup/vault)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Eliminare l'insieme di credenziali di Servizi di ripristino usando Azure Resource Manager

Questa opzione per eliminare l'insieme di credenziali di Servizi di ripristino è consigliata solo se tutte le dipendenze vengono rimosse e viene comunque visualizzato l'errore di eliminazione dell'insieme *di credenziali*. Provare uno o tutti i suggerimenti seguenti:

- Nel riquadro **Informazioni di base del** menu dell'insieme di credenziali verificare che non siano elencati elementi di backup, server di gestione di backup o elementi replicati. Se sono presenti elementi di backup, vedere la [sezione Prima di](#before-you-start) iniziare.
- Provare a [eliminare di nuovo l'insieme di credenziali dal](#delete-the-recovery-services-vault) portale.
- Se tutte le dipendenze vengono rimosse e viene ancora visualizzato l'errore di eliminazione dell'insieme di *credenziali,* usare lo strumento ARMClient per eseguire i passaggi seguenti (dopo la nota).

1. Passare a [chocolatey.org](https://chocolatey.org/) scaricare e installare Chocolatey. Installare quindi ARMClient eseguendo il comando seguente:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Accedere all'account Azure e quindi eseguire il comando seguente:

    `ARMClient.exe login [environment name]`

3. Nel portale di Azure raccogliere l'ID sottoscrizione e il nome del gruppo di risorse per l'insieme di credenziali da eliminare.

Per altre informazioni sul comando ARMClient, vedere [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Usare il client Azure Resource Manager per eliminare un insieme di credenziali di Servizi di ripristino

1. Eseguire il comando seguente usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'insieme di credenziali. Se non sono presenti dipendenze, l'insieme di credenziali viene eliminato quando si esegue il comando seguente:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Se l'insieme di credenziali non è vuoto, verrà visualizzato il messaggio di errore seguente: Impossibile eliminare l'insieme di credenziali perché sono presenti *risorse esistenti all'interno di questo insieme di credenziali.* Per rimuovere un elemento o un contenitore protetto all'interno di un insieme di credenziali, eseguire il comando seguente:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Nella finestra portale di Azure assicurarsi che l'insieme di credenziali sia stato eliminato.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sugli insiemi di credenziali di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md) 
 [Informazioni sul monitoraggio e sulla gestione degli insiemi di credenziali di Servizi di ripristino](backup-azure-manage-windows-server.md)
