---
title: Eseguire il backup soluzione Azure VMware macchine virtuali con server di Backup di Azure
description: Configurare l'ambiente soluzione Azure VMware per eseguire il backup delle macchine virtuali usando server di Backup di Azure.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 92affbf883215f0d051115fb64e9e7bf7a1430b3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871778"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Eseguire il backup soluzione Azure VMware macchine virtuali con server di Backup di Azure

In questo articolo verrà eseguito il backup di macchine virtuali VMware in esecuzione in soluzione Azure VMware con server di Backup di Azure. Per prima cosa, vedere Configurare [Backup di Microsoft Azure Server per soluzione Azure VMware](set-up-backup-server-for-azure-vmware-solution.md).

Verranno quindi illustrate tutte le procedure necessarie per:

> [!div class="checklist"] 
> * Configurare un canale sicuro in modo che il server di Backup di Azure possa comunicare con i server VMware tramite HTTPS. 
> * Aggiungere le credenziali dell'account server di Backup di Azure. 
> * Aggiungere vCenter a server di Backup di Azure. 
> * Configurare un gruppo protezione contenente le macchine virtuali VMware di cui si vuole eseguire il backup, specificare le impostazioni di backup e pianificare il backup.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Creare una connessione sicura al server vCenter

Per impostazione predefinita, il server di Backup di Azure comunica con i server VMware tramite HTTPS. Per configurare la connessione HTTPS, scaricare il certificato dell'autorità di certificazione (CA) VMware e importarlo nel server di Backup di Azure.

### <a name="set-up-the-certificate"></a>Configurare il certificato

1. Nel browser, nel computer server di Backup di Azure immettere l'URL del client Web vSphere.

   > [!NOTE] 
   > Se la pagina  Attività iniziali VMware non viene visualizzata, verificare le impostazioni della connessione e del proxy del browser e riprovare.

1. Nella pagina VMware **Attività iniziali** selezionare **Scarica certificati CA radice attendibili**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="Client Web vSphere":::

1. Salvare il file **download.zip** nel computer server di Backup di Azure e quindi estrarne il contenuto nella cartella **certs,** che contiene:

   - File del certificato radice con estensione che inizia con una sequenza numerata come .0 e .1.
   - File CRL con estensione che inizia con una sequenza come r0 o r1.

1. Nella cartella **certs** fare clic con il pulsante destro del mouse sul file del certificato radice e scegliere **Rinomina** per modificare l'estensione in **.crt**.

   L'icona del file si trasforma nell'icona che rappresenta i certificati radice.

1. Fare clic con il pulsante destro del mouse sul certificato radice e **scegliere Installa certificato**.

1. **Nell'Importazione guidata certificati** selezionare Computer **locale** come destinazione per il certificato e selezionare **Avanti.**

   ![Pagina iniziale della procedura guidata](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Se richiesto, confermare che si desidera consentire le modifiche al computer.

1. Selezionare **Colloca tutti i certificati nell'archivio seguente** e selezionare Sfoglia **per** scegliere l'archivio certificati.

   ![Archiviazione dei certificati](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Selezionare **Autorità di certificazione radice disponibile nell'elenco locale** come cartella di destinazione e selezionare **OK.**

1. Rivedere le impostazioni e selezionare **Fine per** avviare l'importazione del certificato.

   ![Verificare che i certificati si trovino nella cartella corretta](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Dopo aver confermato l'importazione del certificato, accedere al server vCenter per verificare che la connessione sia sicura.

### <a name="enable-tls-12-on-azure-backup-server"></a>Abilitare TLS 1.2 in server di Backup di Azure

In VMware 6.7 e in poi TLS era abilitato come protocollo di comunicazione. 

1. Copiare le impostazioni del Registro di sistema seguenti e incollarle nel Blocco note. Salvare quindi il file come TLS. REG senza l'estensione txt.

   ```
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. Fare clic con il pulsante destro del mouse su TLS. Reg e selezionare **Unisci** o **Apri per** aggiungere le impostazioni al Registro di sistema.


## <a name="add-the-account-on-azure-backup-server"></a>Aggiungere l'account nel server di Backup di Azure

1. Aprire server di Backup di Azure e nella console di server di Backup di Azure selezionare Gestione server di produzione  >    >  **Gestisci VMware**.

   ![Console del server di Backup di Azure](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. Nella finestra **di dialogo Gestisci** credenziali selezionare **Aggiungi**.

   ![Nella finestra di dialogo Gestisci credenziali selezionare Aggiungi.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. Nella finestra di dialogo **Aggiungi credenziali** immettere il nome e la descrizione delle nuove credenziali. Specificare il nome utente e la password definiti nel server VMware.

   > [!NOTE] 
   > Se il server VMware server di Backup di Azure non si trova nello stesso dominio, specificare il dominio nella **casella Nome** utente.

   ![Finestra di dialogo Aggiungi credenziali del server di Backup di Azure](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Selezionare **Aggiungi** per aggiungere le nuove credenziali.

   ![Screenshot che mostra la server di Backup di Azure finestra di dialogo Gestisci credenziali con le nuove credenziali visualizzate.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Aggiungere il server vCenter a server di Backup di Azure

1. Nella console server di Backup di Azure selezionare Server   >  **di produzione di gestione**  >  **Aggiungi**.

   ![Aprire l'Aggiunta guidata server di produzione](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Selezionare **Server VMware** e quindi **Avanti.**

   ![Aggiunta guidata server di produzione](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Specificare l'indirizzo IP di vCenter.

   ![Specificare il server VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Nella casella **Porta SSL** immettere la porta usata per comunicare con vCenter.

   > [!TIP] 
   > La porta 443 è la porta predefinita, ma è possibile modificarla se il vCenter è in ascolto su una porta diversa.

1. Nella casella **Specifica** credenziali selezionare le credenziali create nella sezione precedente.

1. Selezionare **Aggiungi** per aggiungere vCenter all'elenco dei server e selezionare **Avanti.**

   ![Aggiungere il server VMware e le credenziali](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Nella pagina **Riepilogo** selezionare **Aggiungi per** aggiungere vCenter a server di Backup di Azure.

   Il nuovo server viene aggiunto immediatamente. vCenter non richiede un agente.

   ![Aggiungere il server VMware al server di Backup di Azure](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Nella pagina **Fine** rivedere le impostazioni e quindi selezionare **Chiudi**.

   ![Pagina Fine](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Il server vCenter è elencato in **Server di produzione** con:
   - Digitare come **server VMware** 
   - Stato dell'agente **come OK** 
   
      Se lo stato **dell'agente** è **Sconosciuto,** selezionare **Aggiorna**.

## <a name="configure-a-protection-group"></a>Configurazione di un gruppo protezione dati

I gruppi protezione dati raccolgono più macchine virtuali e applicano le stesse impostazioni di backup e conservazione dei dati a tutte le macchine virtuali nel gruppo.

1. Nella console server di Backup di Azure selezionare **Protezione**  >  **nuova**.

   ![Aprire la procedura guidata Crea nuovo gruppo protezione dati](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Nella pagina **iniziale della procedura guidata Crea nuovo gruppo** protezione dati selezionare **Avanti.**

   ![Finestra di dialogo della procedura guidata Crea nuovo gruppo protezione dati](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Nella pagina **Selezione tipo di gruppo protezione dati** selezionare **Server**, quindi **Avanti**. Verrà **visualizzata la pagina Seleziona membri** del gruppo.

1. Nella pagina **Selezione membri gruppo** selezionare le macchine virtuali (o le cartelle vm) di cui si vuole eseguire il backup e quindi selezionare **Avanti.**

   > [!NOTE]
   > Quando si seleziona una cartella o macchine virtuali, per il backup vengono selezionate anche le cartelle all'interno di tale cartella. È possibile deselezionare le cartelle o le macchine virtuali da escludere dal backup. Se è già in corso il backup di una macchina virtuale o di una cartella, non è possibile selezionarla, in modo da evitare la creazione di punti di ripristino duplicati per una macchina virtuale.

   ![Seleziona membri del gruppo](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Nella pagina **Seleziona metodo di protezione dati** immettere un nome per il gruppo protezione dati e le impostazioni di protezione. 

1. Impostare la protezione a breve termine **su Disco,** abilitare la protezione online e quindi selezionare **Avanti.**

   ![Seleziona metodo protezione dati](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Specificare per quanto tempo si vuole mantenere i dati sottoposti a backup su disco.

   - **Periodo di mantenimento** dati: numero di giorni di conservazione dei punti di ripristino del disco.
   - **Backup completo rapido:** frequenza con cui vengono presi i punti di ripristino del disco. Per modificare le ore o le date in cui si verificano i backup a breve termine, selezionare **Modifica**.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Specificare gli obiettivi a breve termine per la protezione basata su disco":::

1. Nella pagina **Verifica archiviazione su disco allocazione** esaminare lo spazio su disco fornito per i backup della macchina virtuale.

   - Le allocazioni di dischi consigliate sono basate sull'intervallo di conservazione specificato, il tipo di carico di lavoro e le dimensioni dei dati protetti. Apportare le modifiche necessarie e quindi selezionare **Avanti.**
   - **Dimensione dati:** la dimensione dei dati nel gruppo protezione dati.
   - **Spazio su disco:** Quantità consigliata di spazio su disco per il gruppo protezione dati. Se si vuole modificare questa impostazione, selezionare uno spazio leggermente superiore alla quantità stimata per le dimensioni di ogni origine dati.
   - **Dettagli del pool di archiviazione:** Mostra lo stato del pool di archiviazione, che include le dimensioni totali e rimanenti del disco.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Esaminare lo spazio su disco assegnato nel pool di archiviazione":::

   > [!NOTE]
   > In alcuni scenari, le dimensioni dei dati segnalate sono superiori alle dimensioni effettive della macchina virtuale. Il problema è attualmente in fase di analisi.

1. Nella pagina **Scegliere il metodo di creazione** della replica indicare come si vuole eseguire il backup iniziale e selezionare **Avanti.**

   - Il valore predefinito è **Automaticamente tramite rete** e **Ora**. Se si usa l'impostazione predefinita, specificare un orario non di punta. Se si sceglie **In seguito**, specificare un giorno e un'ora.
   - Per grandi quantità di dati o condizioni della rete non ottimali, tenere in considerazione la replica dei dati offline tramite supporti di memorizzazione rimovibili.

   ![Scelta del metodo per la creazione della replica](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. Per **Opzioni verifica coerenza** selezionare come e quando automatizzare le verifiche di coerenza e selezionare **Avanti.**

   - È possibile eseguire verifiche della coerenza quando i dati di replica diventano incoerenti o in base a una pianificazione impostata.
   - Se non si desidera configurare le verifiche di coerenza automatiche, è possibile eseguire una verifica manuale facendo clic con il pulsante destro del mouse sul gruppo protezione dati **Esegui verifica coerenza**.

1. Nella pagina **Specifica dati di protezione** online selezionare le macchine virtuali o le cartelle di macchine virtuali di cui si vuole eseguire il backup e quindi selezionare **Avanti.** 

   > [!TIP]
   > È possibile selezionare i membri singolarmente o scegliere **Seleziona tutto** per scegliere tutti i membri.

   ![Specifica i dati da proteggere online](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Nella pagina **Specifica pianificazione backup online** indicare la frequenza con cui si vuole eseguire il backup dei dati dall'archiviazione locale ad Azure. 

   - Punti di ripristino cloud per i dati da generare in base alla pianificazione. 
   - Dopo la generazione, il punto di ripristino viene trasferito all'insieme di credenziali di Servizi di ripristino in Azure.

   ![Specificare la pianificazione dei backup online](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Nella pagina **Specifica criteri di conservazione** online indicare per quanto tempo si vogliono mantenere i punti di ripristino creati dai backup in Azure.

   - Non esiste un limite di tempo per la conservazione dei dati in Azure.
   - L'unico limite è che non è possibile avere più di 9.999 punti di ripristino per ogni istanza protetta. In questo esempio l'istanza protetta è il server VMware.

   ![Specificare i criteri di mantenimento online](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Nella pagina **Riepilogo** rivedere le impostazioni e quindi selezionare **Crea gruppo**.

   ![Riepilogo delle impostazioni e dei membri del gruppo protezione dati](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Eseguire il monitoraggio con server di Backup di Azure console

Dopo aver configurato il gruppo protezione dati per il backup soluzione Azure VMware macchine virtuali, è possibile monitorare lo stato del processo di backup e dell'avviso usando la console server di Backup di Azure backup. Ecco cosa è possibile monitorare.

- Nell'area **attività** Monitoraggio:
   - In **Avvisi** è possibile monitorare errori, avvisi e informazioni generali.  È possibile visualizzare gli avvisi attivi e inattivi e configurare le notifiche tramite posta elettronica.
   - In **Processi** è possibile visualizzare i processi avviati da server di Backup di Azure per un'origine dati o un gruppo protezione dati protetto specifico. È possibile seguire lo stato del processo o controllare le risorse utilizzate dai processi.
- **Nell'area** attività Protezione dati è possibile controllare lo stato dei volumi e delle condivisioni nel gruppo protezione dati. È anche possibile controllare le impostazioni di configurazione, ad esempio le impostazioni di ripristino, l'allocazione del disco e la pianificazione del backup.
- **Nell'area** attività Gestione è possibile visualizzare le  schede **Dischi, Online** e Agenti per controllare lo stato dei dischi nel pool di archiviazione, la registrazione in Azure e lo stato dell'agente DPM distribuito.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Monitorare lo stato dei processi di backup in server di Backup di Azure":::

## <a name="restore-vmware-virtual-machines"></a>Ripristinare macchine virtuali VMware

Nell'server di Backup di Azure Console di amministrazione sono disponibili due modi per trovare i dati recuperabili. È possibile cercare o esplorare. Quando si ripristinano i dati, è possibile che si desideri o meno ripristinare i dati o una macchina virtuale nella stessa posizione. Per questo motivo, server di Backup di Azure supporta tre opzioni di ripristino per i backup di macchine virtuali VMware:

- **Ripristino del percorso originale: usare OLR** per ripristinare una macchina virtuale protetta nel percorso originale. È possibile ripristinare una macchina virtuale nel percorso originale solo se non sono stati aggiunti o eliminati dischi dopo l'esecuzione del backup. Se i dischi sono stati aggiunti o eliminati, è necessario usare il ripristino in un percorso alternativo.
- **Ripristino in un percorso** alternativo: usare quando la macchina virtuale originale è mancante o non si vuole alterare la macchina virtuale originale. Specificare il percorso di un host ESXi, il pool di risorse, la cartella e l'archivio dati e il percorso di archiviazione. Per distinguere la macchina virtuale ripristinata dalla macchina virtuale originale, server di Backup di Azure aggiunge *"-Recovered"* al nome della macchina virtuale.
- Ripristino del percorso file singolo: se la macchina virtuale protetta è una macchina virtuale Windows Server, è possibile ripristinare singoli file o cartelle all'interno della macchina virtuale usando la funzionalità **ILR** di server di Backup di Azure. Per ripristinare singoli file, vedere la procedura più avanti in questo articolo. Il ripristino di un singolo file da una macchina virtuale è disponibile solo per le macchine virtuali Windows e i punti di ripristino del disco.

### <a name="restore-a-recovery-point"></a>Ripristinare un punto di ripristino

1. Nel server di Backup di Azure Console di amministrazione selezionare la **visualizzazione** Ripristino. 

1. Usare il riquadro **Sfoglia** per cercare la macchina virtuale da ripristinare o applicare un filtro per trovarla. Dopo aver selezionato una macchina virtuale o una cartella, nel riquadro **Punti di ripristino per vengono visualizzati i punti di ripristino disponibili.

   ![Punti di ripristino disponibili](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. Nel riquadro **Punti di ripristino per** selezionare una data in cui è stato prelevato un punto di ripristino. Per le date visualizzate in grassetto nel calendario sono disponibili punti di ripristino. In alternativa, è possibile fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere Mostra tutti i punti **di ripristino** e quindi selezionare il punto di ripristino dall'elenco.

   > [!NOTE] 
   > Per la protezione a breve termine, selezionare un punto di ripristino basato su disco per un ripristino più rapido. Dopo la scadenza dei punti di ripristino a breve termine, verranno visualizzati solo i **punti** di ripristino online da ripristinare.

1. Prima di eseguire il ripristino da un punto di ripristino online, assicurarsi che il percorso di staging contenga spazio disponibile sufficiente per ospitare le dimensioni non compresse complete della macchina virtuale da ripristinare. Il percorso di staging può essere visualizzato o modificato eseguendo la **Configurazione guidata impostazioni sottoscrizione**.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="server di Backup di Azure delle cartelle di ripristino":::

1. Selezionare **Ripristina** per aprire il **Ripristino guidato.**

   ![Ripristino guidato, pagina Rivedi selezione ripristino](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Selezionare **Avanti** per passare alla schermata **Specifica opzioni di** ripristino. Selezionare **di nuovo** Avanti per passare alla schermata Seleziona tipo **di** ripristino. 

   > [!NOTE]
   > I carichi di lavoro VMware non supportano l'abilitazione della limitazione della larghezza di banda di rete.

1. Nella pagina **Selezione tipo di ripristino** eseguire il ripristino nell'istanza originale o in una nuova posizione.

   - Se si sceglie **Ripristina nell'istanza originale** non è necessario effettuare altre selezioni nella procedura guidata. Vengono usati i dati per l'istanza originale.
   - Se si sceglie **Ripristina come macchina virtuale in qualsiasi host**, nella schermata **Specifica destinazione** specificare le informazioni per **Host ESXi**, **Pool di risorse**, **Cartella** e **Percorso**.

   ![Selezione tipo di ripristino - pagina](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Nella pagina **Riepilogo** rivedere le impostazioni e selezionare **Ripristina** per avviare il processo di ripristino. 

   Nella schermata **Stato ripristino** viene visualizzato l'avanzamento dell'operazione di ripristino.

### <a name="restore-an-individual-file-from-a-vm"></a>Ripristinare un singolo file da una macchina virtuale

È possibile ripristinare singoli file da un punto di ripristino di una macchina virtuale protetta. Questa funzionalità è disponibile solo per le macchine virtuali Windows Server. Il ripristino di singoli file è simile al ripristino dell'intera macchina virtuale, ad eccezione del fatto che si esplora la VMDK e si trovano i file desiderati prima di avviare il processo di ripristino. 

> [!NOTE]
> Il ripristino di un singolo file da una macchina virtuale è disponibile solo per le macchine virtuali Windows e i punti di ripristino del disco.

1. Nella finestra server di Backup di Azure Console di amministrazione selezionare la **visualizzazione** Ripristino.

1. Usare il riquadro **Sfoglia** per cercare la macchina virtuale da ripristinare o applicare un filtro per trovarla. Dopo aver selezionato una macchina virtuale o una cartella, nel riquadro **Punti di ripristino per vengono visualizzati i punti di ripristino disponibili.

   ![Punti di ripristino disponibili](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. Nel riquadro **Punti di ripristino per** usare il calendario per selezionare la data che contiene i punti di ripristino ricercati. A seconda della configurazione dei criteri di backup, le date possono avere più di un punto di ripristino. 

1. Dopo aver selezionato il giorno in cui è stato prelevato il punto di ripristino, assicurarsi di scegliere l'ora **di ripristino corretta.** 

   > [!NOTE]
   > Se per la data selezionata esistono più punti di ripristino, scegliere il punto di ripristino selezionandolo nel menu a discesa **Ora ripristino**. 

   Dopo aver scelto il punto di ripristino, nel riquadro Percorso viene visualizzato l'elenco **degli elementi ripristinabili.**

1. Per trovare i file da ripristinare, nel riquadro Percorso fare doppio clic sull'elemento nella colonna **Elemento** ripristinabile per aprirlo.  Selezionare quindi il file o le cartelle da ripristinare. Per selezionare più elementi, premere **CTRL** mentre si seleziona ogni elemento. Usare il **riquadro** Percorso per cercare nell'elenco di file o cartelle visualizzati nella **colonna Elemento ripristinabile** .
    
   > [!NOTE]
   > **L'elenco di** ricerca seguente non cerca nelle sottocartelle. Per eseguire la ricerca nelle sottocartelle, fare doppio clic sulla cartella. Usare il pulsante **Su** per spostarsi da una cartella figlio alla cartella padre. È possibile selezionare più elementi (file e cartelle), ma devono essere nella stessa cartella padre. Non è possibile recuperare elementi da più cartelle nello stesso processo di ripristino.

   ![Verifica selezione per ripristino](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Dopo aver selezionato gli elementi per il ripristino, nella barra multifunzione dello strumento Console di amministrazione selezionare Ripristina **per** aprire ripristino **guidato.** In Ripristino **guidato** la schermata **Verifica selezione ripristino** mostra gli elementi selezionati da ripristinare.

1. Nella schermata **Specifica opzioni di** ripristino eseguire una delle operazioni seguenti:

   - Selezionare **Modifica per abilitare** la limitazione della larghezza di banda di rete. Nella finestra **di dialogo Limitazione larghezza di** banda selezionare Abilita limitazione all'utilizzo della **larghezza di** banda di rete per attivarla. Dopo aver abilitato questa opzione configurare **Impostazioni** e **Pianificazione del lavoro**.
   - Selezionare **Avanti per** lasciare disabilitata la limitazione della larghezza di banda della rete.

1. Nella schermata **Seleziona tipo di** ripristino selezionare **Avanti.** È possibile ripristinare solo i file o le cartelle in una cartella di rete.

1. Nella schermata **Specifica destinazione** selezionare Sfoglia per trovare **un** percorso di rete per i file o le cartelle. server di Backup di Azure crea una cartella in cui vengono copiati tutti gli elementi ripristinati. Il nome della cartella ha il MABS_day-month-year. Quando si seleziona un percorso per i file o la cartella ripristinati, vengono forniti i dettagli per tale percorso.

   ![Specificare il percorso in cui ripristinare i file](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Nella schermata **Specifica opzioni di ripristino** scegliere quale impostazione di sicurezza applicare. È possibile scegliere di modificare la limitazione per l'utilizzo della larghezza di banda di rete, ma la limitazione è disabilitata per impostazione predefinita. Inoltre, **il ripristino SAN** e **la** notifica non sono abilitati.

1. Nella schermata **Riepilogo** rivedere le impostazioni e selezionare **Ripristina** per avviare il processo di ripristino. Nella schermata **Stato ripristino** viene visualizzato l'avanzamento dell'operazione di ripristino.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato trattato il backup delle macchine virtuali soluzione Azure VMware con server di Backup di Azure, è possibile ottenere informazioni su: 

- [Risoluzione dei problemi durante la configurazione dei backup in server di Backup di Azure](../backup/backup-azure-mabs-troubleshoot.md)
- [Gestione del ciclo di vita soluzione Azure VMware macchine virtuali](lifecycle-management-of-azure-vmware-solution-vms.md)
