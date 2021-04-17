---
title: Backup offline tramite Azure Data Box
description: Informazioni su come usare Azure Data Box di backup iniziale di grandi dimensioni offline dall'agente MARS a un insieme di credenziali di Servizi di ripristino.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 78adc479ce5733e208d2334d30d7b88e4edf8d6b
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576092"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Backup offline di Backup di Azure tramite Azure Data Box

È possibile [usare](../databox/data-box-overview.md) Azure Data Box per eseguire il seeded dei backup iniziali di Servizi di ripristino di Microsoft Azure (MARS) di grandi dimensioni offline (senza usare la rete) in un insieme di credenziali di Servizi di ripristino. Questo processo consente di risparmiare tempo e larghezza di banda di rete che altrimenti verrebbero usati per spostare grandi quantità di dati di backup online tramite una rete ad alta latenza. Questo miglioramento è attualmente in anteprima. Il backup offline basato su Azure Data Box offre due vantaggi diversi rispetto al [backup offline basato sul servizio Importazione/Esportazione di Azure](./backup-azure-backup-import-export.md):

- Non è necessario acquistare dischi e connettori compatibili con Azure. Azure Data Box include i dischi associati allo [SKU Data Box](https://azure.microsoft.com/services/databox/data/) selezionato.
- Backup di Azure (agente MARS) può scrivere direttamente i dati di backup negli SKU supportati di Azure Data Box. Questa funzionalità elimina la necessità di effettuare il provisioning di una posizione di staging per i dati di backup iniziali. Non sono neppure necessarie utilità per formattare e copiare i dati sui dischi.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box con l'agente MARS

Questo articolo illustra come usare Azure Data Box di backup iniziale di grandi dimensioni offline dall'agente MARS a un insieme di credenziali di Servizi di ripristino.

## <a name="supported-platforms"></a>Piattaforme supportate

Il processo di seeding dei dati dall'agente MARS Azure Data Box è supportato nelle SKU di Windows seguenti.

| **Sistema operativo**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Workstation**                        |                                                              |
| Windows 10 a 64 bit                     | Enterprise, Pro, Home                                       |
| Windows 8.1 a 64 bit                    | Enterprise, Pro                                             |
| Windows 8 a 64 bit                      | Enterprise, Pro                                             |
| Windows 7 a 64 bit                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019 a 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 a 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 a 64 bit         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 a 64 bit            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 a 64 bit    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 a 64 bit | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 a 64 bit    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 a 64 bit     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 a 64 bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Dimensioni dei dati di backup e SKU Data Box supportati

| Dimensioni dei dati di backup (post-compressione tramite MARS)* per server | SKU Azure Data Box supportati                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7,2 TB                                                    | [Azure Data Box disco](../databox/data-box-disk-overview.md) |
| >7,2 TB e <=80 TB**                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

*Le frequenze di compressione tipiche variano tra il 10% e il 20%. <br>
**Se si prevede di avere più di 80 TB di dati di backup iniziali per un singolo server MARS, contattare [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>I dati di backup iniziali da un singolo server devono essere contenuti in una singola istanza di Azure Data Box o in un disco Azure Data Box e non possono essere condivisi tra più dispositivi con SKU uguali o diversi. Un dispositivo Azure Data Box può tuttavia contenere backup iniziali da più server.

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-subscription-and-required-permissions"></a>Sottoscrizione di Azure e autorizzazioni necessarie

- Il processo richiede una sottoscrizione di Azure.
- Il processo richiede che l'utente designato per eseguire i criteri di backup offline sia un proprietario della sottoscrizione di Azure.
- Il Data Box e l'insieme di credenziali di Servizi di ripristino (in cui è necessario eseguire il seeded dei dati) devono essere nelle stesse sottoscrizioni.
- È consigliabile che l'account di archiviazione di destinazione associato al processo Azure Data Box e all'insieme di credenziali di Servizi di ripristino siano nella stessa area. Tuttavia, questa operazione non è necessaria.

### <a name="get-azure-powershell-370"></a>Ottenere Azure PowerShell 3.7.0

*Questo è il prerequisito più importante per il processo*. Prima di installare Azure PowerShell versione 3.7.0, eseguire i controlli seguenti.

#### <a name="step-1-check-the-powershell-version"></a>Passaggio 1: Controllare la versione di PowerShell

1. Aprire Windows PowerShell ed eseguire il comando seguente:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Se l'output visualizza una versione successiva alla 3.7.0, eseguire "Passaggio 2". In caso contrario, andare al "Passaggio 3".

#### <a name="step-2-uninstall-the-powershell-version"></a>Passaggio 2: Disinstallare la versione di PowerShell

Disinstallare la versione corrente di PowerShell.

1. Rimuovere i moduli dipendenti eseguendo il comando seguente in PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Per assicurarsi che l'eliminazione di tutti i moduli dipendenti sia riuscita, eseguire il comando seguente:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Passaggio 3: Installare PowerShell versione 3.7.0

Dopo aver verificato che non sono presenti moduli AzureRM, installare la versione 3.7.0 usando uno dei metodi seguenti:

- Da GitHub usare [questo collegamento.](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

In alternativa, è possibile:

- Eseguire il comando seguente nella finestra di PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell potrebbe anche essere stato installato usando un file msi. Per rimuoverlo, disinstallarlo usando **l'opzione Disinstalla** programmi in Pannello di controllo.

### <a name="order-and-receive-the-data-box-device"></a>Ordinare e ricevere il dispositivo Data Box

Il processo di backup offline che usa MARS e Azure Data Box richiede che i dispositivi Data Box siano in stato Recapitato prima di attivare il backup offline usando l'agente MARS. Per ordinare lo SKU più adatto alle proprie esigenze, vedere Dimensioni dei dati di [backup e Data Box SKU.](#backup-data-size-and-supported-data-box-skus) Seguire la procedura descritta in [Esercitazione: Ordinare](../databox/data-box-disk-deploy-ordered.md) un disco Azure Data Box per ordinare e ricevere i dispositivi Data Box dispositivo.

> [!IMPORTANT]
> Non selezionare *BlobStorage per* Tipo **di account**. L'agente MARS richiede un account che supporti i BLOB di pagine, che non è supportato quando *è selezionata l'opzione BlobStorage.* Selezionare **Archiviazione V2 (utilizzo generico v2)** come Tipo di account quando si crea l'account di archiviazione di destinazione per il Azure Data Box processo. 

![Scegliere il tipo di account nei dettagli dell'istanza](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Installare e configurare l'agente MARS

1. Assicurarsi di disinstallare eventuali installazioni precedenti dell'agente MARS.
1. Scaricare l'agente MARS più recente da [questo sito Web.](https://aka.ms/azurebackup_agent)
1. Eseguire *MARSAgentInstaller.exe* ed eseguire *solo* i [](./install-mars-agent.md#install-and-register-the-agent) passaggi per installare e registrare l'agente nell'insieme di credenziali di Servizi di ripristino in cui si desidera archiviare i backup.

   > [!NOTE]
   > L'insieme di credenziali di Servizi di ripristino deve essere nella stessa sottoscrizione del processo Azure Data Box ripristino.

   Dopo aver registrato l'agente nell'insieme di credenziali di Servizi di ripristino, seguire la procedura descritta nelle sezioni successive.

## <a name="set-up-azure-data-box-devices"></a>Configurare i Azure Data Box mobili

A seconda dello SKU Azure Data Box ordinato, seguire la procedura descritta nelle sezioni appropriate. La procedura illustra come configurare e preparare i dispositivi Data Box per l'agente MARS per identificare e trasferire i dati di backup iniziali.

### <a name="set-up-azure-data-box-disks"></a>Configurare Azure Data Box dischi

Se sono stati ordinati uno o più dischi Azure Data Box (fino a 8 TB ciascuno), seguire la procedura indicata qui per decomprimere, connettere e sbloccare il disco [Data Box disco.](../databox/data-box-disk-deploy-set-up.md)

>[!NOTE]
>È possibile che il server con l'agente MARS non abbia una porta USB. In questo caso, è possibile connettere il disco Azure Data Box a un altro server o client ed esporre la radice del dispositivo come condivisione di rete.

### <a name="set-up-azure-data-box"></a>Configurare le Azure Data Box

Se è stata ordinata un Azure Data Box di archiviazione (fino a 100 TB), seguire questa procedura per configurare [l'Data Box istanza .](../databox/data-box-deploy-set-up.md)

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Montare Azure Data Box'istanza come sistema locale

L'agente MARS opera nel contesto del sistema locale, quindi richiede che lo stesso livello di privilegio sia fornito al percorso di montaggio in cui è connessa l'istanza Azure Data Box servizio.

Per assicurarsi che sia possibile montare Data Box dispositivo come sistema locale usando il protocollo NFS:

1. Abilitare il client per la funzionalità NFS nel server Windows in cui è installato l'agente MARS. Specificare l'origine *alternativa WIM:D:\Sources\Install.wim:4*.
1. Scaricare PsExec dalla pagina [Sysinternals](/sysinternals/downloads/psexec) nel server in cui è installato l'agente MARS.
1. Aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente con la directory che *contienePSExec.exe* come directory corrente.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   La finestra di comando visualizzata a causa del comando precedente si trova nel contesto del sistema locale. Usare questa finestra di comando per eseguire i passaggi per montare la condivisione BLOB di pagine di Azure come unità di rete nel server Windows.
1. Seguire la procedura descritta in [Connettersi Data Box](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) per connettere il server con l'agente MARS al dispositivo Data Box tramite NFS. Eseguire il comando seguente al prompt dei comandi del sistema locale per montare la condivisione BLOB di pagine di Azure.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Dopo aver montato la condivisione, verificare se è possibile accedere a X: dal server. Se possibile, continuare con la sezione successiva di questo articolo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Trasferire i dati di backup iniziali Azure Data Box dispositivi

1. Aprire **l Backup di Microsoft Azure app** applicazione nel server.
1. Nel riquadro **Azioni** selezionare **Pianifica backup**.

    ![Selezionare Pianifica backup](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Seguire i passaggi della **Pianificazione guidata backup**.

1. Aggiungere elementi selezionando il **pulsante Aggiungi** elementi. Mantenere le dimensioni totali degli elementi entro i limiti di dimensione supportati dal Azure Data Box [SKU](#backup-data-size-and-supported-data-box-skus) ordinato e ricevuto.

    ![Aggiungere elementi al backup](./media/offline-backup-azure-data-box/add-items.png)

1. Selezionare la pianificazione di backup e i criteri di conservazione appropriati **per File e cartelle e** Stato del **sistema**. Lo stato del sistema è applicabile solo ai server Windows e non ai client Windows.
1. Nella pagina **Scegliere il tipo di backup iniziale (file** e cartelle) della procedura guidata selezionare **l'opzione Trasferisci** usando Microsoft Azure Data Box dischi e selezionare **Avanti**.

    ![Scegliere il tipo di backup iniziale](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Accedere ad Azure quando richiesto usando le credenziali utente con accesso proprietario nella sottoscrizione di Azure. Dopo aver completato questa operazione, verrà visualizzata una pagina simile a questa.

    ![Creare risorse e applicare le autorizzazioni necessarie](./media/offline-backup-azure-data-box/creating-resources.png)

   L'agente MARS recupera quindi Data Box processi nella sottoscrizione che sono nello stato Recapitati.

    ![Recuperare Data Box processi per l'ID sottoscrizione](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Selezionare l'Data Box corretto per cui è stato decompresso, connesso e sbloccato il Data Box disco. Selezionare **Avanti**.

    ![Selezionare Data Box ordini](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Selezionare **Rileva dispositivo** nella pagina Rilevamento Data Box **dispositivo.** Questa azione consente all'agente MARS di analizzare i dischi Azure Data Box collegati in locale e di rilevarli.

    ![Data Box del dispositivo](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Se l'istanza di Azure Data Box è stata connessa come condivisione di rete (a causa dell'invariabilità delle porte USB o perché è stato ordinato e montato il dispositivo Data Box da 100 TB), il rilevamento ha esito negativo all'inizio. Viene data la possibilità di immettere il percorso di rete per il Data Box dispositivo.

    ![Immettere il percorso di rete](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Specificare il percorso di rete della directory radice del disco Azure Data Box. Questa directory deve contenere una directory con il nome *PageBlob*.
    >
    >![Directory radice del Azure Data Box disco](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Ad esempio, se il percorso del disco è e disk1 contiene una directory denominata `\\mydomain\myserver\disk1\` *PageBlob,* il percorso immesso nella pagina della procedura guidata dell'agente MARS è  `\\mydomain\myserver\disk1\` .
    >
    >Se si [configura un dispositivo Azure Data Box da 100 TB,](#set-up-azure-data-box-devices)immettere come percorso di rete del `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` dispositivo.

1. Selezionare **Avanti** e selezionare **Fine nella** pagina successiva per salvare i criteri di backup e conservazione con la configurazione del backup offline usando Azure Data Box.

   La pagina seguente conferma che il criterio è stato salvato correttamente.

    ![Il criterio è stato salvato correttamente](./media/offline-backup-azure-data-box/policy-saved.png)

1. Selezionare **Chiudi** nella pagina precedente.

1. Selezionare **Backup ora nel** riquadro Azioni **della** console dell'agente MARS. Selezionare **Backup nella** pagina della procedura guidata.

    ![Backup guidato](./media/offline-backup-azure-data-box/backup-now.png)

L'agente MARS avvia il backup dei dati selezionati nel Azure Data Box dispositivo. Questo processo può richiedere da diverse ore ad alcuni giorni. La quantità di tempo dipende dal numero di file e dalla velocità di connessione tra il server con l'agente MARS e il Azure Data Box remoto.

Al termine del backup dei dati, nell'agente MARS verrà visualizzata una pagina simile a questa.

![Stato del backup visualizzato](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Passaggi da eseguire dopo il backup

In questa sezione vengono illustrati i passaggi da eseguire al termine del backup dei dati nel Azure Data Box Disk esito positivo.

- Seguire i passaggi descritti in questo articolo per [spedire il disco Azure Data Box ad Azure.](../databox/data-box-disk-deploy-picked-up.md) Se è stato usato un Azure Data Box da 100 TB, seguire questa procedura per spedire [il Azure Data Box dispositivo ad Azure.](../databox/data-box-deploy-picked-up.md)

- [Monitorare il processo Data Box](../databox/data-box-disk-deploy-upload-verify.md) nel portale di Azure. Al termine Azure Data Box processo, l'agente MARS sposta automaticamente i dati dall'account di archiviazione all'insieme di credenziali di Servizi di ripristino al momento del successivo backup pianificato. Contrassegna quindi il processo di backup come *Processo completato* se un punto di ripristino viene creato correttamente.

    >[!NOTE]
    >L'agente MARS attiva i backup negli orari pianificati durante la creazione dei criteri. Questi processi contrassegnano "Waiting for Azure Data Box job to be completed" fino al termine del processo.

- Dopo che l'agente MARS ha creato correttamente un punto di ripristino che corrisponde al backup iniziale, è possibile eliminare l'account di archiviazione o il contenuto specifico associato al Azure Data Box processo.

## <a name="troubleshooting"></a>Risoluzione dei problemi

L'Microsoft Azure di Servizi di ripristino di Azure Active Directory (MARS) crea automaticamente un'applicazione Azure Active Directory (Azure AD) nel tenant. Questa applicazione richiede un certificato per l'autenticazione che viene creato e caricato quando si configura un criterio di seeding offline. Si usa Azure PowerShell per creare e caricare il certificato nell'Azure AD applicazione.

### <a name="problem"></a>Problema

Quando si configura il backup offline, è possibile che si sia verificato un problema a causa di un bug Azure PowerShell cmdlet. Potrebbe non essere possibile aggiungere più certificati allo stesso Azure AD'applicazione creata dall'agente MAB. Questo problema si verifica se sono stati configurati criteri di seeding offline per lo stesso server o un server diverso.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Verificare se il problema è causato da questa causa radice specifica

Per verificare se il problema è identico a quello descritto in precedenza, seguire questa procedura.

#### <a name="step-1-of-verification"></a>Passaggio 1 della verifica

Verificare se il messaggio di errore seguente viene visualizzato nella console MAB quando è stato configurato il backup offline.

![Impossibile creare i criteri di backup offline per l'account Azure corrente](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Passaggio 2 della verifica

1. Aprire la **cartella Temp** nel percorso di installazione. Il percorso predefinito della cartella temporanea è *C:\Programmi\Microsoft Azure Recovery Services Agent\Temp*. Cercare il file *CBUICurr* e aprire il file.

1. Nel file *CBUICurr* scorrere fino all'ultima riga e verificare se il problema è identico a quello in questo messaggio di errore: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Soluzione alternativa

Come soluzione alternativa per risolvere questo problema, seguire questa procedura e ripetere la configurazione dei criteri.

#### <a name="step-1-of-workaround"></a>Passaggio 1 della soluzione alternativa

Accedere a PowerShell visualizzato nell'interfaccia utente mab usando un account diverso con accesso amministrativo nella sottoscrizione in cui verrà creato Data Box processo.

#### <a name="step-2-of-workaround"></a>Passaggio 2 della soluzione alternativa

Se nessun altro server ha configurato il seeding offline e nessun altro server dipende `AzureOfflineBackup_<Azure User Id>` dall'applicazione, eliminare questa applicazione. Selezionare **portale di Azure**  >  **Azure Active Directory**  >  **Registrazioni app**.

>[!NOTE]
> Verificare se nell'applicazione non è configurato alcun altro seeding offline e anche se nessun altro `AzureOfflineBackup_<Azure User Id>` server dipende da questa applicazione. Passare a **Chiavi**  >  **delle impostazioni** nella sezione **Chiavi** pubbliche. Non devono essere aggiunte altre chiavi pubbliche. Per informazioni di riferimento, vedere lo screenshot seguente.
>
>![Chiavi pubbliche](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Passaggio 3

Dal server che si sta tentando di configurare per il backup offline, eseguire le azioni seguenti.

1. Passare alla **scheda Gestisci applicazione certificato computer**  >  **Personale** e cercare il certificato con il nome `CB_AzureADCertforOfflineSeeding_<Timestamp>` .

2. Selezionare il certificato, fare clic con il pulsante destro **del mouse** su Tutte le attività e **scegliere Esporta** senza una chiave privata nel formato CER.

3. Passare all'applicazione di backup offline di Azure indicata nel passaggio 2. Selezionare **Settings**  >  **Keys**  >  **Upload Public Key (Carica chiave pubblica).** Caricare il certificato esportato nel passaggio precedente.

    ![Caricare la chiave pubblica](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Nel server aprire il Registro di sistema immettendo **regedit** nella finestra di esecuzione.

5. Passare al registro *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Fare clic con il **pulsante destro del mouse su CloudBackupProvider** e aggiungere un nuovo valore stringa con il nome `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Per ottenere l'ID utente di Azure, eseguire una di queste azioni:
    >
    >- In PowerShell connesso ad Azure eseguire il comando `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"`.
    > - Passare al percorso del Registro `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` di sistema con il nome *CurrentUserId*.

6. Fare clic con il pulsante destro del mouse sulla stringa aggiunta nel passaggio precedente e scegliere **Modifica**. Nel valore specificare l'identificazione personale del certificato esportato nel passaggio 2. Selezionare **OK**.

7. Per ottenere il valore dell'identificazione personale, fare doppio clic sul certificato. Selezionare la **scheda Dettagli** e scorrere verso il basso fino a visualizzare il campo identificazione personale. Selezionare **Identificazione personale** e copiare il valore.

    ![Campo identificazione personale del certificato](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Domande

Per eventuali domande o chiarimenti su eventuali problemi che si sono affrontati, contattare [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
