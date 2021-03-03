---
title: Configurare un'appliance con scalabilità orizzontale Azure Migrate per la migrazione di VMware senza agenti
description: Informazioni su come configurare un'appliance con scalabilità orizzontale Azure Migrate per eseguire la migrazione di macchine virtuali Hyper-V.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: fda9026adf46a26927912b9e1983a537470c37ee
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745582"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>Ridimensionare la migrazione senza agenti di macchine virtuali VMware in Azure

Questo articolo illustra come usare un'appliance con scalabilità orizzontale per eseguire la migrazione di un numero elevato di macchine virtuali (VM) VMware in Azure usando la funzionalità di migrazione senza agenti dello strumento di migrazione di Azure Migrate server. 

Con la migrazione senza agente delle macchine virtuali VMware funzionalità dello strumento di migrazione del server, è possibile:

- Eseguire la replica di un massimo di 300 VM da un singolo server vCenter simultaneamente usando una Azure Migrate Appliance.
- Replicare fino a 500 VM da un singolo server vCenter simultaneamente distribuendo una seconda Appliance con scalabilità orizzontale per la migrazione.

In questo articolo verrà spiegato come:

- Distribuire una appliance con scalabilità orizzontale per la migrazione a VMware
- Eseguire la migrazione simultanea di un massimo di 500 VM usando l'appliance con scalabilità orizzontale.

##  <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario eseguire i passaggi seguenti:

- Creare il progetto Azure Migrate.
- Distribuire il dispositivo Azure Migrate (appliance principale) e completare l'individuazione delle macchine virtuali VMware gestite dal server vCenter.
- Configurare la replica per una o più macchine virtuali di cui verrà eseguita la migrazione.
> [!IMPORTANT]
> Prima di poter aggiungere un'appliance con scalabilità orizzontale per la migrazione, è necessario che nel progetto sia presente almeno una macchina virtuale di replica.

## <a name="deploy-a-scale-out-appliance"></a>Distribuire un'appliance con scalabilità orizzontale


Per aggiungere un'appliance con scalabilità orizzontale, attenersi alla procedura indicata di seguito:

1. Fare clic su **individua** perché  >  **i computer sono virtualizzati?** 
1. Selezionare **Sì con VMware vSphere hypervisor.**
1. Nel passaggio successivo selezionare replica senza agenti.
1. Selezionare **scalabilità orizzontale di un dispositivo primario esistente** nel menu Seleziona il tipo di appliance.
1. Selezionare l'appliance principale (l'appliance che usa la quale è stata eseguita l'individuazione) di cui si vuole eseguire la scalabilità orizzontale.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Pagina Individua macchine virtuali per l'onboarding con scalabilità orizzontale":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. generare la chiave del progetto Azure Migrate

1. In **genera Azure migrate chiave del progetto** specificare un nome di suffisso per l'appliance con scalabilità orizzontale. Il suffisso può contenere solo caratteri alfanumerici e deve avere una lunghezza massima di 14 caratteri.
2. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina di individuazione durante la creazione delle risorse.
3. Copiare la chiave generata. La chiave sarà necessaria in un secondo momento per completare la registrazione del dispositivo con scalabilità orizzontale.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. scaricare il programma di installazione per l'appliance con scalabilità orizzontale

In **scarica Azure migrate Appliance** fare clic su  **download**. È necessario scaricare lo script del programma di installazione di PowerShell per distribuire il dispositivo con scalabilità orizzontale in un server esistente che esegue Windows Server 2016 e con la configurazione hardware richiesta (32-GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e accesso a Internet, direttamente o tramite un proxy).
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Scaricare lo script per l'appliance con scalabilità orizzontale":::

> [!TIP]
> È possibile convalidare il checksum del file zip scaricato attenendosi alla procedura seguente:
>
> 1. Apri prompt dei comandi come amministratore
> 2. Eseguire il comando seguente per generare l'hash per il file compresso:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo per il cloud pubblico: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. Scaricare la versione più recente del programma di installazione del dispositivo con scalabilità orizzontale dal portale se il valore hash calcolato non corrisponde a questa stringa: e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

### <a name="3-run-the-azure-migrate-installer-script"></a>3. eseguire lo script del programma di installazione Azure Migrate
Lo script del programma di installazione esegue le operazioni seguenti:

- Installa l'agente gateway e gestione configurazione Appliance per eseguire più repliche server simultanee.
- Installa i ruoli di Windows, tra cui il Servizio Attivazione Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo di IIS riscrivibile. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM) con i dettagli delle impostazioni permanenti per Azure Migrate.
- Crea i seguenti file nei relativi percorsi:
    - **File di configurazione**: %Programdata%\Microsoft Azure\Config
    - **File di log**: %Programdata%\Microsoft Azure\Logs

Eseguire lo script nel modo seguente:

1. Estrarre il file zip in una cartella nel server che ospiterà l'appliance con scalabilità orizzontale.  Assicurarsi di non eseguire lo script in un server con un dispositivo Azure Migrate esistente.
2. Avviare PowerShell nello stesso server con privilegi amministrativi (elevati).
3. Modificare la directory di PowerShell nella cartella in cui è stato estratto il contenuto dal file zip scaricato.
4. Eseguire lo script denominato **AzureMigrateInstaller.ps1**  usando il comando seguente:

    - Per il cloud pubblico: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    Quando viene completata l'esecuzione, lo script avvierà Gestione configurazione Appliance.

In caso di problemi, è possibile accedere ai log di script in: <br/> C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log per la risoluzione dei problemi.


### <a name="4-configure-the-appliance"></a>4. configurare l'appliance

Prima di iniziare, assicurarsi che [questi endpoint di Azure](migrate-appliance.md#public-cloud-urls) siano accessibili dall'appliance con scalabilità orizzontale.

- Aprire un browser in qualsiasi computer in grado di connettersi al server del dispositivo di scalabilità orizzontale e aprire l'URL del dispositivo Configuration Manager: **https://*scale-out Appliance nome o indirizzo IP*: 44368**.

   In alternativa, è possibile aprire Gestione configurazione dal desktop del server del dispositivo con scalabilità orizzontale usando il collegamento a Gestione configurazione.
- Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
- In Configuration Manager > **configurare i prerequisiti**, procedere come segue:
   - **Connettività**: il dispositivo verifica che il server disponga di accesso a Internet. Se il server usa un proxy:
     1. Fare clic su **Set up proxy** (Configura proxy) per specificare l'indirizzo proxy (nel formato http://ProxyIPAddress o http://ProxyFQDN) e la porta di ascolto.
     2. Se il proxy richiede l'autenticazione, specificare le credenziali.
     3. È supportato solo il proxy HTTP.
     4. Se sono stati aggiunti dettagli del proxy o sono stati disabilitati il proxy e/o l'autenticazione, fare clic su **Save** (Salva) per attivare di nuovo il controllo della connettività.
   - **Time sync** (Sincronizzazione ora): Per il corretto funzionamento dell'individuazione delle macchine virtuali, l'ora dell'appliance deve essere sincronizzata con l'ora Internet.
   - **Install updates** (Installa aggiornamenti): l'appliance verifica che siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **Visualizza servizi Appliance** per visualizzare lo stato e le versioni dei servizi in esecuzione nel server appliance.
   - **Install VDDK** (Installa VDDK): l'appliance verifica che VMware vSphere Virtual Disk Development Kit (VDDK) sia installato. Se non è installato, scaricare VDDK 6,7 da VMware ed estrarre il contenuto del file zip scaricato nella posizione specificata nell'appliance, come indicato nelle istruzioni di **installazione** nella schermata Configuration Manager Appliance.


### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave del progetto Azure Migrate** copiata dal portale. Se non si dispone della chiave, passare a **Server Assessment> individua> Gestisci appliance esistenti**, selezionare il nome dell'appliance principale, trovare l'appliance con scalabilità orizzontale associata e copiare la chiave corrispondente.
1. Per eseguire l'autenticazione con Azure, è necessario un codice del dispositivo. Facendo clic su **Accedi** si aprirà una finestra modale con il codice del dispositivo, come illustrato di seguito.
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Finestra modale con il codice del dispositivo":::

1. Fare clic su **Copy code & Login** (Copia il codice e accedi) per copiare il codice del dispositivo e aprire una richiesta di accesso ad Azure in una nuova scheda del browser. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
1. Nella nuova scheda incollare il codice del dispositivo e accedere usando il nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Se si chiude la scheda per errore senza eseguire l'accesso, è necessario aggiornare la scheda del browser di gestione configurazione dell'appliance per abilitare di nuovo il pulsante di accesso.
1. Dopo aver eseguito l'accesso, tornare alla scheda precedente con Gestione configurazione Appliance.
1. Se l'account utente di Azure usato per la registrazione ha le autorizzazioni corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="Pannello 2 in Gestione configurazione Appliance":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Importa la configurazione dell'appliance dal dispositivo primario

Per completare la registrazione del dispositivo con scalabilità orizzontale, fare clic su **Importa** per ottenere i file di configurazione necessari dal dispositivo principale.

1. Se si fa clic su **Importa** , viene visualizzata una finestra popup con le istruzioni su come importare i file di configurazione necessari dal dispositivo principale.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Importa configurazione modale":::
1. Accedere (desktop remoto) all'appliance principale ed eseguire i comandi di PowerShell seguenti:

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. Copiare il file zip creato eseguendo i comandi precedenti nell'appliance con scalabilità orizzontale. Il file zip contiene i file di configurazione necessari per registrare il dispositivo con scalabilità orizzontale.
1. Nella finestra popup aperta nel passaggio precedente, selezionare il percorso del file zip di configurazione copiato e fare clic su **Salva**.

Una volta che i file sono stati importati correttamente, la registrazione del dispositivo con scalabilità orizzontale verrà completata e verrà visualizzato il timestamp dell'ultima importazione completata correttamente. È anche possibile visualizzare i dettagli della registrazione facendo clic su **Visualizza dettagli**.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="Importazione riuscita":::

A questo punto è necessario rivalidare che l'appliance con scalabilità orizzontale sia in grado di connettersi al server vCenter. Fare clic su **riconvalida** per convalidare server vCenter connettività dal dispositivo con scalabilità orizzontale.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="Importazione riuscita":::

> [!IMPORTANT]
> Se si modificano le credenziali di server vCenter nell'appliance principale, assicurarsi di importare di nuovo i file di configurazione nell'appliance con scalabilità orizzontale per ottenere la configurazione più recente e continuare le repliche in corso.<br/> Se il dispositivo con scalabilità orizzontale non è più necessario, assicurarsi di disabilitare l'appliance con scalabilità orizzontale. [**Scopri di più**](./common-questions-appliance) su come disabilitare l'appliance con scalabilità orizzontale quando non è necessario.

## <a name="replicate"></a>Replica

1. Dopo aver registrato l'appliance di scalabilità orizzontale, nel riquadro Azure Migrate: migrazione server fare clic su **replica**.

2.  Per avviare la replica di più macchine virtuali, attenersi alla procedura riportata sullo schermo. 

Con l'appliance con scalabilità orizzontale, è ora possibile replicare le VM 500 simultaneamente. È anche possibile eseguire la migrazione di macchine virtuali in batch di 200 tramite la portale di Azure.

Lo strumento di migrazione di Azure Migrate server si occuperà della distribuzione delle macchine virtuali tra l'appliance primaria e quella con scalabilità orizzontale per la replica. Al termine della replica, è possibile eseguire la migrazione delle macchine virtuali.

> [!TIP]
> È consigliabile eseguire la migrazione delle macchine virtuali in batch di 200 per ottenere prestazioni ottimali se si desidera eseguire la migrazione di un numero elevato di macchine virtuali.
  
## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso quanto segue:
- Come configurare un'appliance con scalabilità orizzontale
- Come replicare le VM usando un'appliance con scalabilità orizzontale


[Altre](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) informazioni sulla migrazione di server in Azure con Azure migrate: strumento di migrazione server.