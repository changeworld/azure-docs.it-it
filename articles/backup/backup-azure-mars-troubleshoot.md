---
title: Risolvere i problemi dell Backup di Azure asserzione
description: Questo articolo illustra come risolvere i problemi relativi all'installazione e alla registrazione dell Backup di Azure agent.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: c662bf8c8d9490691f45254bef01618f17bd6e2a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518185"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Risolvere i problemi Microsoft Azure'agente di Servizi di ripristino di microsoft Microsoft Azure (MARS)

Questo articolo descrive come risolvere gli errori che possono verificarsi durante la configurazione, la registrazione, il backup e il ripristino.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

È consigliabile verificare quanto segue prima di iniziare a risolvere i problemi relativi all'agente di Servizi di ripristino di Azure:

- [Assicurarsi che l'agente MARS sia aggiornato.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Assicurarsi di disporre della connettività di rete tra l'agente MARS e Azure](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Assicurarsi che MARS sia in esecuzione (nella console del servizio). Se necessario, riavviare e ripetere l'operazione.
- Assicurarsi che nel percorso della cartella scratch sia disponibile dal [5% al 10%](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)di spazio disponibile per il volume.
- [Controllare se un altro processo o un software antivirus interferisce con Backup di Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Se il processo di backup è stato completato con avvisi, vedere [Processi di backup completati con avviso](#backup-jobs-completed-with-warning)
- Se il backup pianificato ha esito negativo ma il backup manuale funziona, vedere [Backup non eseguiti in base alla pianificazione](#backups-dont-run-according-to-schedule).
- Assicurarsi che il sistema operativo abbia gli aggiornamenti più recenti.
- [Assicurarsi che le unità e i file non supportati con attributi non supportati siano esclusi dal backup.](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Assicurarsi che l'orologio nel sistema protetto sia configurato per il fuso orario corretto.
- [Assicurarsi .NET Framework 4.5.2 o](https://www.microsoft.com/download/details.aspx?id=30653)versione successiva sia installato nel server .
- Se si sta tentando di registrare nuovamente il server in un insieme di credenziali:
  - Assicurarsi che l'agente sia disinstallato nel server e che sia stato eliminato dal portale.
  - Usare la stessa passphrase usata inizialmente per registrare il server.
- Per i backup offline, assicurarsi Azure PowerShell 3.7.0 sia installato nel computer di origine che nel computer di copia prima di avviare il backup.
- Se l'agente di Backup è in esecuzione in una macchina virtuale di Azure, vedere [questo articolo.](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

## <a name="invalid-vault-credentials-provided"></a>Sono state specificate credenziali dell'insieme di credenziali non valide

**Messaggio di errore:** Sono specificate credenziali dell'insieme di credenziali non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. (ID: 34513)

| Causa | Azioni consigliate |
| ---     | ---    |
| **Le credenziali dell'insieme di credenziali non sono valide** <br/> <br/> I file delle credenziali dell'insieme di credenziali potrebbero essere danneggiati, essere scaduti o avere un'estensione di file diversa rispetto a *.vaultCredentials*. Ad esempio, potrebbero essere stati scaricati più di 10 giorni prima dell'ora di registrazione.| [Scaricare le nuove credenziali dall'insieme](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) di credenziali di Servizi di ripristino nel portale di Azure. Eseguire quindi questi passaggi, in base alle esigenze: <ul><li> Se MARS è già stato installato e registrato, aprire la console MMC Backup di Microsoft Azure Agent. Selezionare quindi **Registra server** nel **riquadro Azioni** per completare la registrazione con le nuove credenziali. <br/> <li> Se la nuova installazione non riesce, provare a reinstallare con le nuove credenziali.</ul> **Nota:** se sono stati scaricati più file di credenziali dell'insieme di credenziali, solo il file più recente è valido per i 10 giorni successivi. È consigliabile scaricare un nuovo file di credenziali dell'insieme di credenziali.
| **Il server proxy o il firewall blocca la registrazione** <br/>oppure <br/>**Nessuna connettività Internet** <br/><br/> Se il computer o il server proxy ha connettività Internet limitata e non si garantisce l'accesso per gli URL necessari, la registrazione avrà esito negativo.| Seguire questa procedura:<br/> <ul><li> Collaborare con il team IT per assicurarsi che il sistema abbia connettività Internet.<li> Se non si ha un server proxy, assicurarsi che l'opzione proxy non sia selezionata quando si registra l'agente. [Controllare le impostazioni proxy.](#verifying-proxy-settings-for-windows)<li> Se si dispone di un server firewall/proxy, collaborare con il team di rete per assicurarsi che questi URL e indirizzi IP hanno accesso:<br/> <br> **URL**<br> `www.msftncsi.com` <br> . Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**Indirizzi IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Provare a eseguire di nuovo la registrazione dopo aver completato i passaggi precedenti per la risoluzione dei problemi.<br></br> Se la connessione avviene tramite Azure ExpressRoute, assicurarsi che le impostazioni siano configurate come descritto in Azure ExpressRoute [supportare](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Il software antivirus blocca la registrazione** | Se nel server è installato un software antivirus, aggiungere le regole di esclusione necessarie all'analisi antivirus per questi file e cartelle: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> Cartella scratch. Il percorso predefinito è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch. <li> Cartella bin in C:\Programmi\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Suggerimenti aggiuntivi

- Andare a C:/Windows/Temp e controllare se sono presenti più di 60.000 o 65.000 file con estensione tmp. Se sono presenti, eliminare questi file.
- Verificare che la data e l'ora del computer corrispondano al fuso orario locale.
- Assicurarsi [che questi siti](install-mars-agent.md#verify-internet-access) siano aggiunti ai siti attendibili in Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verifica delle impostazioni proxy per Windows

1. Scaricare PsExec dalla [pagina Sysinternals.](/sysinternals/downloads/psexec)
1. Eseguire `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` da un prompt dei comandi con privilegi elevati.

   Questo comando aprirà Internet Explorer.
1. Passare a **Strumenti**  >  **Opzioni Internet**  >  **Connessioni**  >  **IMPOSTAZIONI LAN**.
1. Controllare le impostazioni proxy per l'account di sistema.
1. Se non è configurato alcun proxy e vengono forniti i dettagli del proxy, rimuovere i dettagli.
1. Se è configurato un proxy e i dettagli del proxy non sono corretti, verificare che i dettagli **relativi** a IP e **porta** del proxy siano corretti.
1. Chiudere Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Non è possibile scaricare il file di credenziali dell'insieme di credenziali

| Errore   | Azioni consigliate |
| ---     | ---    |
|Non è stato possibile scaricare il file dell'insieme di credenziali. (ID: 403) | <ul><li> Provare a scaricare le credenziali dell'insieme di credenziali usando un browser diverso oppure seguire questa procedura: <ul><li> Avviare Internet Explorer. Selezionare F12. </li><li> Passare alla scheda **Rete** e cancellare la cache e i cookie. </li> <li> Aggiornare la pagina.<br></li></ul> <li> Controllare se la sottoscrizione è disabilitata/scaduta.<br></li> <li> Controllare se una regola del firewall blocca il download. <br></li> <li> Assicurarsi di non aver esaurito il limite per l'insieme di credenziali (50 computer per ogni insieme di credenziali).<br></li>  <li> Assicurarsi che l'utente abbia Backup di Azure necessarie per scaricare le credenziali dell'insieme di credenziali e registrare un server con l'insieme di credenziali. Vedere [Usare il controllo degli accessi in base al ruolo di Azure](backup-rbac-rs-vault.md)per gestire Backup di Azure di ripristino.</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi a Backup di Microsoft Azure

| Errore  | Possibile causa | Azioni consigliate |
| ---     | ---     | ---    |
| <br /><ul><li>L Microsoft Azure del servizio di ripristino non è stato in grado di connettersi a Backup di Microsoft Azure. (ID: 100050) Controllare le impostazioni di rete e assicurarsi di potersi connettere a Internet.<li>(407) Necessaria autenticazione proxy. |Un proxy blocca la connessione. |  <ul><li>In Internet Explorer passare **a** Strumenti  >  **Opzioni Internet**  >  **Security**  >  **Internet**. Selezionare **Livello personalizzato e** scorrere verso il basso fino alla sezione Download **file.** Selezionare **Abilita**.<p>Potrebbe anche essere necessario aggiungere [URL e indirizzi IP](install-mars-agent.md#verify-internet-access) ai siti attendibili in Internet Explorer.<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy.<li> Se il computer ha accesso a Internet limitato, assicurarsi che le impostazioni del firewall nel computer o nel proxy consentano [questi URL e indirizzi IP.](install-mars-agent.md#verify-internet-access) <li>Se nel server è installato un software antivirus, escludere questi file dall'analisi antivirus: <ul><li>CBengine.exe (anziché dpmra.exe).<li>CSC.exe (correlato a .NET Framework). È disponibile un CSC.exe per ogni .NET Framework installata nel server. Escludere CSC.exe file per tutte le versioni .NET Framework nel server interessato. <li>Cartella scratch o percorso della cache. <br>Il percorso predefinito per la cartella scratch o il percorso della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch.<li>Cartella bin in C:\Programmi\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>Il file delle credenziali dell'insieme di credenziali specificato non può essere usato perché non viene scaricato dall'insieme di credenziali associato a questo server

| Errore  | Possibile causa | Azioni consigliate |
| ---     | ---     | ---    |
| Il file delle credenziali dell'insieme di credenziali specificato non può essere usato perché non viene scaricato dall'insieme di credenziali associato a questo server. (ID: 100110) Specificare le credenziali dell'insieme di credenziali appropriate. | Il file delle credenziali dell'insieme di credenziali è di un insieme di credenziali diverso da quello in cui il server è già registrato. | Assicurarsi che il computer di destinazione e il computer di origine siano registrati nello stesso insieme di credenziali dei servizi di ripristino. Se il server di destinazione è già stato registrato in un insieme di credenziali diverso, usare l'opzione **Registra server** per eseguire la registrazione nell'insieme di credenziali corretto.  

## <a name="backup-jobs-completed-with-warning"></a>Processi di backup completati con avviso

- Quando l'agente MARS esegue l'iterazione su file e cartelle durante il backup, è possibile che si verifichino diverse condizioni che possono causare il completamento del backup con avvisi. Durante queste condizioni, un processo viene visualizzato come completato con avvisi. Questo è un problema, ma significa che non è stato possibile eseguire il backup di almeno un file. Il processo ha quindi ignorato il file, ma ha eseguito il backup di tutti gli altri file in questione nell'origine dati.

  ![Processo di backup completato con avvisi](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- Le condizioni che possono causare l'ignorare i file nei backup includono:
  - Attributi di file non supportati (ad esempio, in una cartella di OneDrive, flusso compresso, reparse point). Per l'elenco completo, vedere la matrice [di supporto](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup).
  - Un file system problema
  - Un altro processo che interferisce (ad esempio: il software antivirus che contiene handle sui file può impedire all'agente MARS di accedere ai file)
  - File bloccati da un'applicazione  

- Il servizio di backup contrassegnerà questi file come non riusciti nel file di log, con la *convenzione* di denominazione seguente:LastBackupFailedFilesxxxx.txtnella cartella *C:\Programmi\Microsoft Azure Recovery Service Agent\temp.*
- Per risolvere il problema, esaminare il file di log per comprendere la natura del problema:

  | Codice di errore             | Motivi                                             | Consigli                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | Il file o la directory è danneggiato e illeggibile. | Eseguire **chkdsk nel** volume di origine.                             |
  | 0x80070002, 0x80070003 | Il sistema non riesce a trovare il file specificato.         | [Assicurarsi che la cartella scratch non sia piena](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)  <br><br>  Controllare se il volume in cui è configurato lo spazio scratch esiste (non eliminato)  <br><br>   [Assicurarsi che l'agente MARS sia escluso dall'antivirus installato nel computer](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Accesso negato                                    | [Controllare se l'accesso è bloccato da un antivirus o da altro software di terze parti](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | Accesso al file cloud negato.                | File di OneDrive, file Git o qualsiasi altro file che può essere in stato offline nel computer |

- È possibile usare [Aggiungi regole di esclusione ai](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) criteri esistenti per escludere i file non supportati, mancanti o eliminati dai criteri di backup per garantire l'esito positivo dei backup.

- Evitare di eliminare e ricreare cartelle protette con gli stessi nomi nella cartella di primo livello. In questo modo il backup potrebbe essere completato con avvisi con l'errore È stata rilevata *un'incoerenza critica,* pertanto le modifiche non possono essere replicate.  Se è necessario eliminare e ricreare le cartelle, è consigliabile eseguire questa operazione nelle sottocartelle della cartella di primo livello protetta.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Impossibile impostare la chiave di crittografia per i backup protetti

| Errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| <br />Impossibile impostare la chiave di crittografia per i backup protetti. L'attivazione non è riuscita completamente, ma la passphrase di crittografia è stata salvata nel file seguente. |<li>Il server è già registrato con un altro insieme di credenziali.<li>Durante la configurazione la passphrase è stata danneggiata.| Annullare la registrazione del server dall'insieme di credenziali e registrarlo di nuovo con una nuova passphrase.

## <a name="the-activation-did-not-complete-successfully"></a>L'attivazione non è stata completata

| Errore  | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|<br />L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft.     | <li> La cartella scratch si trova in un volume che non ha spazio sufficiente. <li> La cartella scratch è stata spostata in modo non corretto. <li> Il file OnlineBackup.KEK è mancante.         | <li>Eseguire l'aggiornamento [alla versione più recente](https://aka.ms/azurebackup_agent) dell'agente MARS.<li>Spostare la cartella scratch o il percorso della cache in un volume con spazio disponibile compreso tra il 5% e il 10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere la procedura descritta in [Domande comuni sul backup di file e cartelle.](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)<li> Verificare che sia presente il file OnlineBackup.KEK. <br>Il percorso predefinito per la cartella scratch o il *percorso della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La passphrase di crittografia non è configurata correttamente

| Errore  | Possibili cause | Azioni consigliate |
|---------|---------|---------|
| <br />Errore 34506. La passphrase di crittografia archiviata nel computer non è configurata correttamente.    | <li> La cartella scratch si trova in un volume che non ha spazio sufficiente. <li> La cartella scratch è stata spostata in modo non corretto. <li> Il file OnlineBackup.KEK è mancante.        | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare la cartella scratch o il percorso della cache in un volume con spazio disponibile compreso tra il 5% e il 10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere la procedura in [Domande comuni sul backup di file e cartelle](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>Il percorso predefinito per la cartella scratch o il *percorso della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>I backup non vengono eseguiti in base alla pianificazione

Se i backup pianificati non vengono attivati automaticamente, ma i backup manuali funzionano correttamente, provare le azioni seguenti:

- Assicurarsi che la pianificazione del backup di Windows Server non sia in conflitto con la pianificazione del backup di file e cartelle di Azure.

- Assicurarsi che lo stato del backup online sia impostato su **Abilita**. Per verificare lo stato, seguire questa procedura:

  1. In Utilità di pianificazione espandere **Microsoft** e selezionare **Backup online**.
  1. Fare doppio clic **su Microsoft-OnlineBackup** e passare alla **scheda Trigger.**
  1. Controllare se lo stato è impostato su **Abilitato.** In caso contrario, selezionare **Modifica,** selezionare **Abilitato** e quindi **selezionare OK.**

- Verificare che l'account utente selezionato per l'esecuzione **dell'attività** sia **SYSTEM** o gruppo Administrators locale nel server. Per verificare l'account utente, passare alla **scheda Generale** e controllare le **opzioni di** sicurezza.

- Assicurarsi che PowerShell 3.0 o versione successiva sia installato nel server. Per controllare la versione di PowerShell, eseguire questo comando e verificare che il numero di versione `Major` sia 3 o versione successiva:

  `$PSVersionTable.PSVersion`

- Assicurarsi che questo percorso sia parte della variabile `PSMODULEPATH` di ambiente:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se i criteri di esecuzione di PowerShell per sono impostati su , il cmdlet di PowerShell che `LocalMachine` attiva `restricted` l'attività di backup potrebbe non riuscire. Eseguire questi comandi in modalità con privilegi elevati per controllare e impostare i criteri di esecuzione su `Unrestricted` o `RemoteSigned` :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Assicurarsi che non siano presenti file MSOnlineBackup del modulo PowerShell mancanti o danneggiati. Se sono presenti file mancanti o danneggiati, seguire questa procedura:

  1. Da qualsiasi computer con un agente MARS che funzioni correttamente, copiare la cartella MSOnlineBackup da C:\Programmi\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Nel computer problematico incollare i file copiati nello stesso percorso della cartella (C:\Programmi\Microsoft Azure Recovery Services Agent\bin\Modules).

     Se nel computer è già presente una cartella MSOnlineBackup, incollare i file al suo interno o sostituire i file esistenti.

> [!TIP]
> Per assicurarsi che le modifiche siano applicate in modo coerente, riavviare il server dopo aver eseguito i passaggi precedenti.

## <a name="resource-not-provisioned-in-service-stamp"></a>Risorsa di cui non è stato effettuato il provisioning nel timestamp del servizio

Errore | Possibili cause | Azioni consigliate
--- | --- | ---
L'operazione corrente non è riuscita a causa di un errore interno del servizio "Risorsa di cui non è stato effettuato il provisioning nel timestamp del servizio". Ripetere l'operazione dopo alcuni minuti. (ID: 230006) | Il server protetto è stato rinominato. | <li> Rinominare il server con il nome originale come registrato con l'insieme di credenziali. <br> <li> Registrare nuovamente il server nell'insieme di credenziali con il nuovo nome.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>Non è stato possibile iniziare il processo poiché era in corso un altro processo

Se si nota un messaggio di avviso nella cronologia processo della **console MARS,** che indica che non è stato possibile avviare il processo perché era in corso un altro processo, il problema potrebbe essere dovuto a un'istanza duplicata del processo attivata dal  >  Utilità di pianificazione.

![Non è stato possibile iniziare il processo poiché era in corso un altro processo](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

Per risolvere il problema:

1. Avviare Utilità di pianificazione snap-in digitando *taskschd.msc* nella finestra Esegui
1. Nel riquadro sinistro passare a Utilità di pianificazione **Library**  ->  **Microsoft**  ->  **OnlineBackup.**
1. Per ogni attività in questa libreria, fare doppio clic sull'attività per aprire le proprietà ed eseguire i passaggi seguenti:
    1. Passare alla scheda **Impostazioni** .

         ![Scheda Settings](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Modificare l'opzione **per Se l'attività è già in esecuzione, viene applicata la regola seguente.** Scegliere **Non avviare una nuova istanza.**

         ![Modificare la regola in modo da non avviare una nuova istanza](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Risolvere i problemi di ripristino

Backup di Azure potrebbe non montare il volume di ripristino in modo corretto, anche dopo diversi minuti. Durante il processo è anche possibile che si ricevano messaggi di errore. Per iniziare il ripristino normalmente, seguire questa procedura:

1. Annullare il processo di montaggio se è in esecuzione da diversi minuti.

2. Verificare di avere la versione più recente dell'agente di backup. Per controllare la versione, nel riquadro **Azioni** della console MARS selezionare **Informazioni Microsoft Azure'agente di Servizi di ripristino.** Verificare che il numero di **versione** sia uguale a o superiore alla versione citata in [questo articolo](https://go.microsoft.com/fwlink/?linkid=229525). Selezionare questo collegamento per [scaricare la versione più recente.](https://go.microsoft.com/fwLink/?LinkID=288905)

3. Passare a **Controller di archiviazione** di  >  **Gestione** dispositivi e individuare **Iniziatore iSCSI Microsoft.** Se lo si individua, andare direttamente al passaggio 7.

4. Se non è possibile individuare il servizio iniziatore iSCSI Microsoft, provare a trovare una voce in Controller di archiviazione di **Gestione** dispositivi denominata Dispositivo sconosciuto con  >   ID hardware **ROOT\ISCSIPRT.** 

5. Fare clic con il **pulsante destro del mouse su** Unknown Device **(Dispositivo sconosciuto) e scegliere Update Driver Software (Aggiorna software driver).**

6. Aggiornare il driver selezionando l'opzione **Cerca automaticamente un driver aggiornato**. Questo aggiornamento dovrebbe cambiare **Dispositivo sconosciuto in** **Iniziatore iSCSI Microsoft:**

    ![Schermata di Gestione dispositivi di Backup di Azure, con l'opzione Controller di archiviazione evidenziata](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Passare a **Gestione attività**  >  **Services (local)**  >  **Microsoft iSCSI Initiator Service (Servizio iniziatore iSCSI Microsoft):**

    ![Schermata di Gestione attività di Backup di Azure con l'opzione Servizi (computer locale) evidenziata](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Riavviare il servizio Iniziatore iSCSI Microsoft. A tale scopo, fare clic con il pulsante destro del mouse sul servizio e scegliere **Arresta**. Fare di nuovo clic con il pulsante destro del mouse e **scegliere Avvia**.

9. Ripetere il ripristino usando l'opzione [Ripristino istantaneo](backup-instant-restore-capability.md).

Se il ripristino ha ancora esito negativo, riavviare il server o il client. Se non si vuole riavviare o se il ripristino ha comunque esito negativo anche dopo il riavvio del server, provare a [eseguire il ripristino da un altro computer.](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="troubleshoot-cache-problems"></a>Risolvere i problemi relativi alla cache

L'operazione di backup potrebbe non riuscire se la cartella della cache (detta anche cartella scratch) non è configurata correttamente, mancano i prerequisiti o ha accesso limitato.

### <a name="prerequisites"></a>Prerequisiti

Per il funzionamento positivo delle operazioni dell'agente MARS, la cartella della cache deve rispettare i requisiti seguenti:

- [Assicurarsi che nel percorso della cartella scratch sia disponibile dal 5% al 10% di spazio disponibile per il volume disponibile](backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Verificare che il percorso della cartella scratch sia valido e accessibile](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)
- [Assicurarsi che gli attributi di file nella cartella della cache siano supportati](backup-azure-file-folder-backup-faq.yml#are-there-any-attributes-of-the-cache-folder-that-aren-t-supported-)
- [Assicurarsi che lo spazio di archiviazione delle copie shadow allocato sia sufficiente per il processo di backup](#increase-shadow-copy-storage)
- [Assicurarsi che non siano presenti altri processi (ad esempio software antivirus) che limitano l'accesso alla cartella della cache](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Aumentare l'archiviazione delle copie shadow

Le operazioni di backup potrebbero non riuscire se lo spazio di archiviazione della copia shadow non è sufficiente per proteggere l'origine dati. Per risolvere questo problema, aumentare lo spazio di archiviazione delle copie shadow nel volume protetto usando **vssadmin,** come illustrato di seguito:

- Controllare lo spazio di archiviazione shadow corrente dal prompt dei comandi con privilegi elevati:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Aumentare lo spazio di archiviazione shadow usando il comando seguente:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Un altro processo o un software antivirus che blocca l'accesso alla cartella della cache

Se nel server è installato un software antivirus, aggiungere le regole di esclusione necessarie all'analisi antivirus per questi file e cartelle:  

- Cartella scratch. La posizione predefinita è `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- Cartella bin in `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Problemi comuni

Questa sezione illustra gli errori comuni che si verificano durante l'uso dell'agente MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Messaggio di errore | Azione consigliata
--|--
Agente di Servizi di ripristino di Microsoft Azure non è riuscito ad accedere al checksum di backup archiviato nell'area di lavoro | Per risolvere questo problema, seguire questa procedura e riavviare il server <br/> - [Verificare se è presente un antivirus o altri processi che bloccano i file di percorso scratch](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controllare se il percorso scratch è valido e accessibile all'agente MARS.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Messaggio di errore | Azione consigliata
--|--
Agente di Servizi di ripristino di Microsoft Azure non è riuscito ad accedere al percorso dei file temporanei per inizializzare VHD | Per risolvere questo problema, seguire questa procedura e riavviare il server <br/> - [Controllare se l'antivirus o altri processi bloccano i file di percorso dei file di percorso scratch](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controllare se il percorso scratch è valido e accessibile all'agente MARS.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Messaggio di errore | Azione consigliata
--|--
Backup non riuscito a causa di spazio di archiviazione insufficiente nel volume in cui si trova la cartella scratch | Per risolvere questo problema, verificare i passaggi seguenti e ripetere l'operazione:<br/>- [Verificare che l'agente MARS sia più recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Verificare e risolvere i problemi di archiviazione che influiscono sugli spazi di lavoro dei backup](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Messaggio di errore | Azione consigliata
--|--
Impossibile trovare le modifiche in un file. Il problema potrebbe essere legato a vari motivi. Ripetere l'operazione | Per risolvere questo problema, verificare i passaggi seguenti e ripetere l'operazione:<br/> - [Verificare che l'agente MARS sia più recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Verificare e risolvere i problemi di archiviazione che influiscono sugli spazi di lavoro dei backup](#prerequisites)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [come eseguire il backup di Windows Server con l'agente Backup di Azure.](tutorial-backup-windows-server-to-azure.md)
- Se è necessario ripristinare un backup, vedere [Ripristinare i file in un computer Windows.](backup-azure-restore-windows-server.md)
