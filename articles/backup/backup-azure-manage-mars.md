---
title: Gestire e monitorare i backup dell'agente MARS
description: Informazioni su come gestire e monitorare Microsoft Azure backup dell'agente di Servizi di ripristino (MARS) usando il Backup di Azure servizio.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 4306f01d608542f7453b32b32a1a6894c2379159
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515023"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Gestire Microsoft Azure backup dell'agente di Servizi di ripristino di microsoft Backup di Azure (MARS)

Questo articolo descrive come gestire i file e le cartelle di cui viene eseguito il backup con l'Microsoft Azure di Servizi di ripristino.

## <a name="modify-a-backup-policy"></a>Modificare un criterio di backup

Quando si modificano i criteri di backup, è possibile aggiungere nuovi elementi, rimuovere gli elementi esistenti dal backup o escludere i file dal backup usando le impostazioni di esclusione.

- **Aggiungi elementi** usare questa opzione solo per aggiungere nuovi elementi di cui eseguire il backup. Per rimuovere elementi esistenti, usare **l'opzione Rimuovi elementi** **o Impostazioni di** esclusione.  
- **Rimuovi elementi** usare questa opzione per rimuovere gli elementi di cui viene eseguito il backup.
  - Usare **Le impostazioni di esclusione** per rimuovere tutti gli elementi all'interno di un volume invece di Rimuovi **elementi**.
  - La cancellazione di tutte le selezioni in un volume fa sì che i vecchi backup degli elementi siano conservati in base alle impostazioni di conservazione al momento dell'ultimo backup, senza ambito di modifica.
  - Se si selezionano di nuovo questi elementi, viene eseguito un primo backup completo e le nuove modifiche ai criteri non vengono applicate ai backup vecchi.
  - Se si deseleziona l'intero volume, il backup precedente viene mantenuto senza alcun ambito per la modifica dei criteri di conservazione.
- **Le impostazioni di** esclusione usano questa opzione per escludere elementi specifici dal backup.

### <a name="add-new-items-to-existing-policy"></a>Aggiungere nuovi elementi ai criteri esistenti

1. In **Azioni** selezionare **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Nella **scheda Seleziona elemento criteri** selezionare Modifica pianificazione backup per file e **cartelle** e selezionare **Avanti.**

    ![Selezionare gli elementi dei criteri](./media/backup-azure-manage-mars/select-policy-items.png)

3. Nella **scheda Modifica o arresta pianificazione backup** selezionare **Apportare modifiche agli elementi** o agli orari di backup e selezionare **Avanti.**

    ![Modificare o pianificare il backup](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Nella **scheda Seleziona elementi da backup** selezionare Aggiungi **elementi** per aggiungere gli elementi di cui si vuole eseguire il backup.

    ![Modificare o pianificare l'aggiunta di elementi di backup](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Nella **finestra Seleziona** elementi selezionare i file o le cartelle da aggiungere e selezionare **OK.**

    ![Selezionare gli elementi](./media/backup-azure-manage-mars/select-item.png)

6. Completare i passaggi successivi e **selezionare Fine** per completare l'operazione.

### <a name="add-exclusion-rules-to-existing-policy"></a>Aggiungere regole di esclusione ai criteri esistenti

È possibile aggiungere regole di esclusione per ignorare i file e le cartelle di cui non si vuole eseguire il backup. È possibile eseguire questa operazione durante la definizione di un nuovo criterio o la modifica di un criterio esistente.

1. Nel riquadro Azioni selezionare **Pianifica backup.** Passare a **Select items to Backup (Seleziona elementi per il backup)** e selezionare Exclusion Settings **(Impostazioni di esclusione).**

    ![Impostazioni di esclusione](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. In **Impostazioni di esclusione** selezionare Aggiungi **esclusione.**

    ![Aggiungere l'esclusione](./media/backup-azure-manage-mars/add-exclusion.png)

3. In **Selezionare gli elementi da escludere** esplorare i file e le cartelle e selezionare gli elementi da escludere e selezionare **OK.**

    ![Selezionare gli elementi da escludere](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Per impostazione predefinita, **tutte le sottocartelle** all'interno delle cartelle selezionate vengono escluse. È possibile modificare questa impostazione selezionando **Sì** o **No.** È possibile modificare e specificare i tipi di file da escludere, come illustrato di seguito:

    ![Selezionare i tipi di sottocartelle](./media/backup-azure-manage-mars/subfolders-type.png)

5. Completare i passaggi successivi e **selezionare Fine** per completare l'operazione.

### <a name="remove-items-from-existing-policy"></a>Rimuovere elementi dai criteri esistenti

1. Nel riquadro Azioni selezionare **Pianifica backup.** Passare a **Selezionare gli elementi per eseguire il backup.** Nell'elenco selezionare i file e le cartelle da rimuovere dalla pianificazione del backup e selezionare **Rimuovi elementi**.

    ![Selezionare gli elementi da rimuovere](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Procedere con cautela quando si rimuove completamente un volume dai criteri.  Se è necessario aggiungerlo di nuovo, verrà considerato come un nuovo volume. Il backup pianificato successivo eseguirà un backup iniziale (backup completo) anziché un backup incrementale. Se è necessario rimuovere e aggiungere elementi temporaneamente in un  secondo momento,  è consigliabile usare le impostazioni di esclusioni anziché Rimuovi elementi per garantire il backup incrementale anziché il backup completo.

2. Completare i passaggi successivi e **selezionare Fine** per completare l'operazione.

## <a name="stop-protecting-files-and-folder-backup"></a>Interrompere la protezione del backup di file e cartelle

Esistono due modi per interrompere la protezione del backup di file e cartelle:

- **Arrestare la protezione e conservare i dati di backup**.
  - Questa opzione interromperà la protezione di tutti i processi di backup futuri.
  - Backup di Azure servizio continuerà a mantenere tutti i punti di ripristino esistenti.  
  - Sarà possibile ripristinare i dati di cui è stato eseguito il backup per i punti di ripristino non scaduti.
  - Se si decide di riprendere la protezione, è possibile usare *l'opzione Riattiva pianificazione backup.* Successivamente, i dati verranno conservati in base ai nuovi criteri di conservazione.
- **Arrestare la protezione ed eliminare i dati di backup**.
  - Questa opzione impedirà a tutti i processi di backup futuri di proteggere i dati ed eliminare tutti i punti di ripristino.
  - Si riceverà un messaggio di posta elettronica di avviso per l'eliminazione dei dati di backup con un messaggio I *dati per questo elemento di backup sono stati eliminati. Questi dati saranno temporaneamente* disponibili per 14 giorni, dopo i quali verranno eliminati definitivamente e l'azione consigliata Riproteggi l'elemento di backup entro *14* giorni per ripristinare i dati.
  - Per riprendere la protezione, riprotezione entro 14 giorni dall'operazione di eliminazione.

### <a name="stop-protection-and-retain-backup-data"></a>Arrestare la protezione e conservare i dati di backup

1. Aprire la console di gestione di MARS, passare al **riquadro Azioni** e selezionare **Pianifica backup**.

    ![Selezionare pianifica backup](./media/backup-azure-manage-mars/mars-actions.png)
1. Nella pagina **Selezione elemento criteri** selezionare Modifica una pianificazione di backup per i file e le **cartelle** e selezionare **Avanti.**

    ![Selezionare l'elemento dei criteri](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Nella pagina **Modifica o arresta** un backup pianificato selezionare Interrompi l'uso di questa pianificazione di backup, ma mantenere i backup archiviati fino a quando non viene attivata **di nuovo una pianificazione.** Quindi selezionare **Avanti**.

    ![Arrestare un backup pianificato.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. In **Sospendi backup** pianificato esaminare le informazioni e selezionare **Fine**.

    ![Sospendere un backup pianificato.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. In **Modifica stato backup** verificare che lo stato della sospensione del backup pianificato sia riuscito e selezionare **Chiudi** per completare l'operazione.

### <a name="stop-protection-and-delete-backup-data"></a>Arrestare la protezione ed eliminare i dati di backup

1. Aprire la console di gestione di MARS, passare al **riquadro Azioni** e selezionare **Pianifica backup**.
2. Nella pagina **Modifica o arresta un backup pianificato** selezionare Interrompi l'uso di questa pianificazione di backup ed eliminare tutti i backup **archiviati.** Quindi selezionare **Avanti**.

    ![Modificare o arrestare un backup pianificato.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Nella pagina **Arresta un backup pianificato** selezionare **Fine**.

    ![Arrestare un backup pianificato e selezionare Fine](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Viene richiesto di immettere un PIN di sicurezza (numero di identificazione personale), che è necessario generare manualmente. A tale scopo, accedere prima di tutto al portale di Azure.
5. Passare a **Proprietà delle impostazioni dell'insieme**  >  **di credenziali di Servizi** di  >  **ripristino**.
6. In **PIN di sicurezza** selezionare **Genera**. Copiare questo PIN. Il PIN è valido solo per cinque minuti.
7. Nella console di gestione incollare il PIN e quindi selezionare **OK.**

    ![Generare un PIN di sicurezza.](./media/backup-azure-delete-vault/security-pin.png)

8. Nella pagina **Modifica stato backup** viene visualizzato il messaggio seguente: I dati di backup eliminati verranno conservati per *14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente.*  

    ![Modificare lo stato di avanzamento del backup](./media/backup-azure-delete-vault/deleted-backup-data.png)

Dopo aver eliminato gli elementi di backup locali, seguire i passaggi successivi dal portale.

## <a name="re-enable-protection"></a>Ri-abilitare la protezione

Se la protezione è stata arrestata durante la conservazione dei dati e si è deciso di riprendere la protezione, è possibile riattivare la pianificazione del backup usando i criteri di backup di modifica.

1. In **Azioni selezionare** Pianifica **backup**.
1. Selezionare **Ri-abilita pianificazione backup. È anche possibile modificare gli elementi o gli orari di backup** e selezionare **Avanti.**<br>

    ![Ri-abilitare la pianificazione del backup](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. In **Selezionare gli elementi di cui eseguire il backup** selezionare **Avanti.**

    ![Selezionare gli elementi di cui eseguire il backup](./media/backup-azure-manage-mars/re-enable-next.png)
1. In **Specifica pianificazione backup** specificare la pianificazione del backup e selezionare **Avanti.**
1. In **Seleziona criteri di conservazione** specificare la durata della conservazione e selezionare **Avanti.**
1. Infine, nella **schermata Di** conferma esaminare i dettagli dei criteri e selezionare **Fine.**

## <a name="re-generate-passphrase"></a>Generare nuovamente la passphrase

Una passphrase viene usata per crittografare e decrittografare i dati durante il backup o il ripristino del computer locale o locale usando l'agente MARS da e verso Azure. Se la passphrase è stata persa o dimenticata, è possibile rigenerare la passphrase (a condizione che il computer sia ancora registrato nell'insieme di credenziali di Servizi di ripristino e che il backup sia configurato) seguendo questa procedura:

1. Dalla console dell'agente MARS passare a **Riquadro azioni Modificare**  >  **le** proprietà >. Passare quindi alla **scheda Crittografia**.<br>
1. Selezionare la casella di controllo Cambia **passphrase.**<br>
1. Immettere una nuova passphrase o selezionare **Genera passphrase.**
1. Selezionare **Sfoglia** per salvare la nuova passphrase.

    ![Generare la passphrase.](./media/backup-azure-manage-mars/passphrase.png)

1. Selezionare **OK** per applicare le modifiche.  Se la [funzionalità di sicurezza](./backup-azure-security-feature.md#enable-security-features) è abilitata nel portale di Azure per l'insieme di credenziali di Servizi di ripristino, verrà richiesto di immettere il PIN di sicurezza. Per ricevere il PIN, seguire i passaggi elencati in questo [articolo.](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)<br>
1. Incollare il PIN di sicurezza dal portale e selezionare **OK** per applicare le modifiche.<br>

    ![Incollare il PIN di sicurezza](./media/backup-azure-manage-mars/passphrase2.png)
1. Assicurarsi che la passphrase sia salvata in modo sicuro in un percorso alternativo (diverso dal computer di origine), preferibilmente nel Azure Key Vault. Tenere traccia di tutte le passphrase se si dispone di più computer di cui viene eseguito il backup con gli agenti MARS.

## <a name="managing-backup-data-for-unavailable-machines"></a>Gestione dei dati di backup per i computer non disponibili

Questa sezione illustra uno scenario in cui il computer di origine protetto con MARS non è più disponibile perché è stato eliminato, danneggiato, infettato da malware/ransomware o ritirato le autorizzazioni.

Per questi computer, il servizio Backup di Azure garantisce che il punto di ripristino più recente non scada (ovvero non viene eliminati) in base alle regole di conservazione specificate nei criteri di backup. Pertanto, è possibile ripristinare il computer in modo sicuro.  Si considerino gli scenari seguenti che è possibile eseguire sui dati di cui è stato eseguito il backup:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Scenario 1: il computer di origine non è disponibile e non è più necessario conservare i dati di backup

- È possibile eliminare i dati di cui è stato eseguito il backup portale di Azure i passaggi elencati in [questo articolo.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Scenario 2: il computer di origine non è disponibile ed è necessario conservare i dati di backup

La gestione dei criteri di backup per MARS viene eseguita tramite la console MARS e non tramite il portale. Se è necessario estendere le impostazioni di conservazione per i punti di ripristino esistenti prima della scadenza, è necessario ripristinare il computer, installare la console MARS ed estendere i criteri.

- Per ripristinare il computer, seguire questa procedura:
  1. [Ripristinare la macchina virtuale in un computer di destinazione alternativo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Ricreare il computer di destinazione con lo stesso nome host del computer di origine
  1. Installare l'agente e registrarlo di nuovo nello stesso insieme di credenziali e con la stessa passphrase
  1. Avviare il client MARS per estendere la durata della conservazione in base alle esigenze
- Il computer appena ripristinato, protetto con MARS, continuerà a eseguire i backup.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Configurazione dell'antivirus per l'agente MARS

Per evitare conflitti con il funzionamento dell'agente MARS, è consigliabile usare la configurazione seguente per il software antivirus.

1. **Aggiungi esclusioni percorso:** per evitare una riduzione delle prestazioni e dei possibili conflitti, escludere i percorsi seguenti dal monitoraggio in tempo reale da parte del software antivirus:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` Sottocartelle e
    1. **Cartella Scratch:** se la cartella scratch non è nel percorso standard, aggiungerla anche alle esclusioni.  [Vedere qui per i passaggi per](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-) determinare il percorso della cartella scratch.
1. **Aggiungi esclusioni binarie:** per evitare la riduzione delle prestazioni delle attività di backup e della console, escludere i processi per i file binari seguenti dal monitoraggio in tempo reale da parte del software antivirus:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Anche se l'esclusione di questi percorsi sarà sufficiente per la maggior parte del software antivirus, alcuni potrebbero continuare a interferire con le operazioni dell'agente MARS. Se si verificano errori imprevisti, disinstallare temporaneamente il software antivirus e monitorare per verificare se il problema persiste. Se il problema persiste, contattare il fornitore del software antivirus per assistenza con la configurazione corretta del prodotto.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli scenari supportati e sulle limitazioni, vedere Matrice di supporto per [l'agente MARS.](./backup-support-matrix-mars-agent.md)
- Altre informazioni sul comportamento [di conservazione dei criteri di backup su richiesta.](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)
- Per domande più frequenti, vedere le domande [frequenti sull'agente MARS.](backup-azure-file-folder-backup-faq.yml)
