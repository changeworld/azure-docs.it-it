---
title: Gestire singoli sensori
description: Informazioni su come gestire i singoli sensori, tra cui la gestione dei file di attivazione, l'esecuzione di backup e l'aggiornamento di un sensore autonomo.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/10/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 25f47be98b11f05ee6ac27018152ece05c0de4e4
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246690"
---
# <a name="manage-individual-sensors"></a>Gestire singoli sensori

Questo articolo descrive come gestire i singoli sensori. Le attività includono la gestione dei file di attivazione, l'esecuzione di backup e l'aggiornamento di un sensore autonomo.

È anche possibile eseguire determinate attività di gestione dei sensori dalla console di gestione locale, in cui è possibile gestire contemporaneamente più sensori.

Usare il portale di Azure per l'onboarding e la registrazione del sensore.

## <a name="manage-sensor-activation-files"></a>Gestire i file di attivazione dei sensori

Il sensore è stato caricato con Azure Defender per gli utenti dal portale di Azure. Ogni sensore è stato caricato come sensore connesso localmente o come sensore connesso al cloud.

Un file di attivazione univoco viene caricato in ogni sensore distribuito. Per altre informazioni su quando e come usare un nuovo file, vedere [caricare nuovi file di attivazione](#upload-new-activation-files). Se non è possibile caricare il file, vedere [risolvere i problemi di caricamento del file di attivazione](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>Informazioni sui file di attivazione per i sensori connessi localmente

I sensori connessi localmente sono associati a una sottoscrizione di Azure. Il file di attivazione per i sensori connessi localmente contiene una data di scadenza. Un mese prima di questa data, nella parte superiore della console dei sensori viene visualizzato un messaggio di avviso. L'avviso rimane fino a dopo l'aggiornamento del file di attivazione.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="Screenshot delle impostazioni di sistema.":::

È possibile continuare a usare Defender per le funzionalità di Internet delle cose anche se il file di attivazione è scaduto. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Informazioni sui file di attivazione per i sensori connessi al cloud

I sensori che sono connessi al cloud sono associati al Defender per l'hub Internet. Questi sensori non sono limitati da periodi di tempo per il file di attivazione. Il file di attivazione per i sensori connessi al cloud viene usato per garantire la connessione al Defender per l'hub Internet.

### <a name="upload-new-activation-files"></a>Carica i nuovi file di attivazione

Potrebbe essere necessario caricare un nuovo file di attivazione per un sensore caricato quando:

- Un file di attivazione scade in un sensore connesso localmente. 

- Si vuole usare una modalità di gestione dei sensori diversa. 

- Si vuole assegnare un nuovo Defender per l'hub Internet a un sensore connesso al cloud.

Per aggiungere un nuovo file di attivazione:

1. Passare alla pagina di **gestione del sensore** .

2. Selezionare il sensore per il quale si vuole caricare un nuovo file di attivazione.

3. Eliminarla.

4. Eseguire di nuovo il caricamento del sensore dalla pagina di **onboarding** nella nuova modalità o con un nuovo Defender per l'hub Internet.

5. Scaricare il file di attivazione dalla pagina **Scarica file di attivazione** .

6. Salvare il file.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Scaricare il file di attivazione dall'hub Defender for Internet.":::

7. Accedere al Defender per la console del sensore Internet.

8. Nella console del sensore selezionare   >  **riattivazione** impostazioni di sistema.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Selezione della riattivazione nella schermata Impostazioni sistema.":::

9. Selezionare **carica** e selezionare il file salvato.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Caricare il file salvato.":::

10. Selezionare **Attiva**.

### <a name="troubleshoot-activation-file-upload"></a>Risolvere i problemi di caricamento del file di attivazione

Se non è stato possibile caricare il file di attivazione, verrà visualizzato un messaggio di errore. Potrebbero essersi verificati gli eventi seguenti:

- **Per i sensori connessi localmente**: il file di attivazione non è valido. Se il file non è valido, passare al portale di Defender per le cose. Nella pagina **Gestione sensori** selezionare il sensore con il file non valido e scaricare un nuovo file di attivazione.

- **Per i sensori connessi al cloud**: il sensore non è in grado di connettersi a Internet. Controllare la configurazione di rete del sensore. Se il sensore deve connettersi tramite un proxy Web per accedere a Internet, verificare che il server proxy sia configurato correttamente nella schermata di **configurazione della rete del sensore** . Verificare che \* l'estensione Azure-Devices.NET:443 sia consentita nel firewall e/o nel proxy. Se i caratteri jolly non sono supportati o si desidera un maggiore controllo, il nome di dominio completo per il Defender specifico per l'hub Internet deve essere aperto nel firewall e/o nel proxy. Per informazioni dettagliate, vedere [endpoint dell'hub](../iot-hub/iot-hub-devguide-endpoints.md)degli elementi di riferimento.  

- **Per i sensori connessi al cloud**: il file di attivazione è valido, ma il Defender per l'it ha rifiutato. Se non è possibile risolvere questo problema, è possibile scaricare un'altra attivazione dalla pagina di **gestione dei sensori** del portale Defender for Internet. Se questa operazione non funziona, contattare supporto tecnico Microsoft.

## <a name="manage-certificates"></a>Gestire i certificati

Dopo l'installazione del sensore, viene generato un certificato autofirmato locale che viene usato per accedere all'applicazione Web del sensore. Quando si accede al sensore per la prima volta, agli utenti amministratori viene richiesto di specificare un certificato SSL/TLS.  Per altre informazioni sulla prima configurazione, vedere [accedere e attivare un sensore](how-to-activate-and-set-up-your-sensor.md).

Questo articolo fornisce informazioni sull'aggiornamento dei certificati, sull'uso dei comandi dell'interfaccia della riga di comando del certificato e sui certificati supportati e sui parametri del certificato.

### <a name="about-certificates"></a>Informazioni sui certificati

Azure Defender per Internet delle cose usa i certificati SSL/TLS per:

1. Soddisfare i requisiti specifici di certificato e crittografia richiesti dall'organizzazione caricando il certificato firmato da un'autorità di certificazione.

1. Consentire la convalida tra la console di gestione e i sensori connessi e tra una console di gestione e una console di gestione a disponibilità elevata. Le convalide vengono valutate in base a un elenco di revoche di certificati e alla data di scadenza del certificato. **Se la convalida ha esito negativo, la comunicazione tra la console di gestione e il sensore viene interrotta e viene visualizzato un errore di convalida nella console. Questa opzione è abilitata per impostazione predefinita dopo l'installazione.**

 Regole di invio di terze parti, ad esempio le informazioni sugli avvisi inviate a SYSLOG, Splunk o ServiceNow; o la comunicazione con Active Directory non viene convalidata.

### <a name="update-certificates"></a>Aggiornare i certificati

Gli utenti dell'amministratore dei sensori possono aggiornare i certificati.

Per aggiornare un certificato:  

1. Selezionare **impostazioni di sistema**.
1. Selezionare **certificati SSL/TLS.**
1. Eliminare o modificare il certificato e aggiungerne uno nuovo.
    - Aggiungere un nome di certificato.
    - Caricare un file CRT e un file di chiave e immettere una passphrase.
    - Se necessario, caricare un file PEM.

Per modificare l'impostazione di convalida:

1. Abilitare o disabilitare l'attivazione/disattivazione della **convalida del certificato** .
1. Selezionare **Salva**.

Se l'opzione è abilitata e la convalida ha esito negativo, la comunicazione tra la console di gestione e il sensore viene interrotta e viene visualizzato un errore di convalida nella console.

### <a name="certificate-support"></a>Supporto per i certificati

Sono supportati i certificati seguenti:

- Infrastruttura a chiave privata/Enterprise (PKI privata) 
- Infrastruttura a chiave pubblica (PKI pubblico) 
- Generato localmente nel dispositivo (autofirmato locale). **Non è consigliabile usare certificati autofirmati.** Questa connessione non è *protetta* e deve essere usata solo per gli ambienti di test. Il proprietario del certificato non può essere convalidato e non è possibile mantenere la sicurezza del sistema. I certificati autofirmati non devono mai essere usati per le reti di produzione.  

Sono supportati i seguenti parametri. CRT certificato

- Il file di certificato primario per il nome di dominio
- Algoritmo di firma = SHA256RSA
- Algoritmo hash della firma = SHA256
- Valido da = data passata valida
- Valido per = data futura valida
- Chiave pubblica = RSA 2048bits (minima) o 4096bits
- Punto di distribuzione CRL = URL del file con estensione CRL
- Subject CN = URL, può essere un certificato con caratteri jolly, ad esempio example.contoso.com o  *contoso.com**
- Subject (C) ountry = defined, ad esempio US
- Subject (OU) org Unit = defined, ad esempio contoso Labs
- Subject (O) ganizzazione = defined, ad esempio Contoso Inc.

File codice

- Il file di chiave generato al momento della creazione di CSR
- RSA 2048bits (minimo) o 4096bits

Catena di certificati

- Il file di certificato intermedio, se presente, fornito dalla CA
- Il certificato della CA che ha emesso il certificato del server deve essere il primo nel file, seguito da qualsiasi altro fino alla radice. 
- Può includere gli attributi del contenitore.

Passphrase

- 1 chiave supportata
- Installazione durante l'importazione del certificato

I certificati con altri parametri possono funzionare, ma non possono essere supportati da Microsoft.

#### <a name="encryption-key-artifacts"></a>Elementi chiave di crittografia

**. pem: file contenitore di certificati**

Il nome è da Privacy Enhanced Mail (PEM), un metodo cronologico per la posta elettronica sicura, ma il formato del contenitore in cui si trovano e è una conversione Base64 delle chiavi X509 ASN. 1.  

Definito in RFC da 1421 a 1424: un formato di contenitore che può includere solo il certificato pubblico (ad esempio con le installazioni Apache e i file di certificato CA/etc/ssl/certs) oppure può includere un'intera catena di certificati, tra cui la chiave pubblica, la chiave privata e i certificati radice.  

Può anche codificare un CSR perché il formato PKCS10 può essere convertito in PEM.

**. cert. cer. CRT-file contenitore di certificati**

File con estensione PEM (o raramente) formattato con un'estensione diversa. Viene riconosciuta da Esplora risorse come un certificato. Il file con estensione PEM non è riconosciuto da Esplora risorse.

**. Key-file di chiave privata**

Un file di chiave è lo stesso "Format" di un file PEM, ma ha un'estensione diversa.
##### <a name="use-cli-commands-to-deploy-certificates"></a>Usare i comandi dell'interfaccia della riga di comando per distribuire certificati

Usare il comando dell'interfaccia della riga di comando *CyberX-xsense-certificate-Import* per importare i certificati. Per usare questo strumento, è necessario caricare i file di certificato nel dispositivo (usando strumenti come WinSCP o wget).

Il comando supporta i flag di input seguenti:

-h Mostra la sintassi della guida della riga di comando

--CRT percorso del file di certificato (estensione CRT)

--Key *. file di chiave, la lunghezza della chiave deve essere minima di 2048 bit

--catena percorso del file di catena di certificati (facoltativo)

--Passphrase passata usata per crittografare il certificato (facoltativo)

--passphrase-set default = false, non usato. Impostare su TRUE per usare la passphrase precedente fornita con il certificato precedente (facoltativo)

Quando si usa il comando CLI:

- Verificare che i file di certificato siano leggibili nell'appliance.

- Verificare che il nome di dominio e l'IP nel certificato corrispondano alla configurazione pianificata dal reparto IT.


## <a name="connect-a-sensor-to-the-management-console"></a>Connettere un sensore alla console di gestione

Questa sezione descrive come garantire la connessione tra il sensore e la console di gestione locale. È necessario eseguire questa operazione se si sta lavorando in una rete gapped e si desidera inviare informazioni sugli asset e sugli avvisi alla console di gestione dal sensore. Questa connessione consente inoltre alla console di gestione di effettuare il push delle impostazioni di sistema al sensore ed eseguire altre attività di gestione sul sensore.

Per connettersi:

1. Accedere alla console di gestione locale.

2. Selezionare **impostazioni di sistema**.

3. Nella sezione **configurazione del sensore-stringa di connessione** copiare la stringa di connessione generata automaticamente.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Copiare la stringa di connessione da questa schermata."::: 

4. Accedere alla console del sensore.

5. Nel riquadro sinistro selezionare impostazioni di **sistema**.

6. Selezionare **connessione console di gestione**.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Screenshot della finestra di dialogo di connessione della console di gestione.":::

7. Incollare la stringa di connessione nella casella **stringa di connessione** e selezionare **Connetti**.

8. Nella finestra di **gestione del sito** della console di gestione locale assegnare il sensore a una zona.

## <a name="change-the-name-of-a-sensor"></a>Modificare il nome di un sensore

È possibile modificare il nome della console del sensore. Il nuovo nome verrà visualizzato nel Web browser console, in diverse finestre della console e nei log per la risoluzione dei problemi.

Il processo per modificare i nomi dei sensori varia per i sensori connessi localmente e i sensori connessi al cloud. Il nome predefinito è **Sensor**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Modificare il nome di un sensore connesso localmente

Per modificare il nome:

1. Nella parte inferiore del riquadro sinistro della console selezionare l'etichetta del sensore corrente.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Screenshot che mostra l'etichetta del sensore.":::

1. Nella finestra di dialogo **modifica nome sensore** immettere un nome.

1. Selezionare **Salva**. Viene applicato il nuovo nome.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Modificare il nome di un sensore connesso al cloud

Se il sensore è stato registrato come sensore connesso al cloud, il nome del sensore viene definito dal nome assegnato durante la registrazione. Il nome viene incluso nel file di attivazione caricato quando si accede per la prima volta. Per modificare il nome del sensore, è necessario caricare un nuovo file di attivazione.

Per modificare il nome:

1. Nel portale di Azure Defender per le cose andare alla pagina di **gestione del sensore** .

1. Eliminare il sensore dalla finestra di **gestione del sensore** .

1. Eseguire la registrazione con il nuovo nome.

1. Scaricare e il nuovo file di attivazione.

1. Accedere al sensore e caricare il nuovo file di attivazione.

## <a name="update-the-sensor-network-configuration"></a>Aggiornare la configurazione di rete del sensore

La configurazione di rete del sensore è stata definita durante l'installazione del sensore. È possibile modificare i parametri di configurazione. È anche possibile configurare una configurazione proxy.

Se si crea un nuovo indirizzo IP, potrebbe essere necessario eseguire di nuovo l'accesso.

Per modificare la configurazione:

1. Nel menu laterale selezionare impostazioni di **sistema**.

2. Nella finestra **Impostazioni sistema** selezionare **rete**.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Configurare le impostazioni di rete.":::

3. Impostare i parametri come segue:

    | Parametro | Descrizione |
    |--|--|
    | Indirizzo IP | Indirizzo IP del sensore |
    | Subnet mask | Indirizzo della maschera |
    | Gateway predefinito | Indirizzo gateway predefinito |
    | DNS | Indirizzo del server DNS |
    | Nome host | Nome host del sensore |
    | Proxy | Host proxy e nome porta |

4. Selezionare **Salva**.

## <a name="synchronize-time-zones-on-the-sensor"></a>Sincronizzare i fusi orari sul sensore

È possibile configurare l'ora e l'area del sensore in modo che tutti gli utenti visualizzino la stessa ora e l'area geografica.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Configurare l'ora e l'area.":::

| Parametro | Descrizione |
|--|--|
| Fuso orario | Definizione del fuso orario per:<br />-Avvisi<br />-Widget tendenze e statistiche<br />-Report di data mining<br />   -Report di valutazione dei rischi<br />-Vettori di attacco |
| Formato data | Selezionare una delle opzioni di formato seguenti:<br />-DD/MM/AAAA HH: mm: SS<br />-MM/GG/AAAA HH: mm: SS<br />-AAAA/MM/GG HH: mm: SS |
| Data e ora | Consente di visualizzare la data e l'ora locali correnti nel formato selezionato.<br />Se, ad esempio, il percorso effettivo è America e New York, ma il fuso orario è impostato su Europa e Berlino, l'ora viene visualizzata in base all'ora locale di Berlino. |

Per configurare il tempo del sensore:

1. Nel menu laterale selezionare impostazioni di **sistema**.

2. Nella finestra **Impostazioni sistema** selezionare **ora & regione**.

3. Impostare i parametri e selezionare **Salva**.

## <a name="set-up-backup-and-restore-files"></a>Configurare i file di backup e ripristino

Il backup di sistema viene eseguito automaticamente alle 3:00 AM ogni giorno. I dati vengono salvati in un disco diverso nel sensore. Il percorso predefinito è `/var/cyberx/backups`.

Il file può essere trasferito automaticamente alla rete interna.

> [!NOTE]
> - È possibile eseguire la procedura di backup e ripristino solo tra le stesse versioni.
> - In alcune architetture il backup è disabilitato. È possibile abilitarlo nel `/var/cyberx/properties/backup.properties` file.

Quando si controlla un sensore usando la console di gestione locale, è possibile usare la pianificazione di backup del sensore per raccogliere questi backup e archiviarli nella console di gestione o in un server di backup esterno.

**Elementi di cui viene eseguito il backup:** Configurazioni e dati.

**Elementi di cui non è stato eseguito il backup:** PCAP i file e i log. È possibile eseguire manualmente il backup e il ripristino di PCAPs e log.

I file di backup del sensore vengono automaticamente denominati nel formato seguente: `<sensor name>-backup-version-<version>-<date>.tar` . Un esempio è `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Per configurare il backup:

- Accedere a un account amministrativo e immettere `$ sudo cyberx-xsense-system-backup` .

Per ripristinare il file di backup più recente:

- Accedere a un account amministrativo e immettere `$ sudo cyberx-xsense-system-restore` .

Per salvare il backup in un server SMB esterno:

1. Creare una cartella condivisa nel server SMB esterno.

    Ottenere il percorso della cartella, il nome utente e la password necessari per accedere al server SMB.

2. Nel sensore, creare una directory per i backup:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Modificare `fstab`: 

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Modificare e creare le credenziali da condividere per il server SMB:

    `sudo nano /etc/samba/user` 

5. Aggiungere:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Montare la directory:

    `sudo mount -a`

7. Configurare una directory di backup nella cartella condivisa in Defender per il sensore Internet:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Ripristinare i sensori

È possibile ripristinare i backup dalla console dei sensori e usando l'interfaccia della riga di comando.

**Per eseguire il ripristino dalla console:**

- Selezionare **Ripristina immagine** dalla finestra impostazioni di **sistema** del sensore.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Ripristinare l'immagine selezionando il pulsante.":::

Nella console vengono visualizzati gli errori di ripristino.

**Per eseguire il ripristino tramite l'interfaccia della riga di comando:**

- Accedere a un account amministrativo e immettere `$ sudo cyberx-management-system-restore` .

## <a name="update-a-standalone-sensor-version"></a>Aggiornare una versione del sensore autonomo

La procedura seguente descrive come aggiornare un sensore autonomo usando la console del sensore. Il processo di aggiornamento richiede circa 30 minuti.

1. Passare al [portale di Azure](https://portal.azure.com/).

2. Passa a Defender for Internet.

3. Passare alla pagina **aggiornamenti** .

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Screenshot della pagina degli aggiornamenti di Defender per l'it.":::

4. Selezionare **Scarica** dalla sezione **sensori** e salvare il file.

5. Nella barra laterale della console del sensore selezionare **impostazioni di sistema**.

6. Nel riquadro **aggiornamento versione** selezionare **Aggiorna**.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Screenshot del riquadro aggiornamento.":::

7. Selezionare il file scaricato dalla pagina Defender for Internet **Updates** .

8. Viene avviato il processo di aggiornamento, durante il quale il sistema viene riavviato due volte. Dopo il primo riavvio (prima del completamento del processo di aggiornamento), il sistema si apre con la finestra di accesso. Dopo aver eseguito l'accesso, la versione dell'aggiornamento viene visualizzata nella parte inferiore sinistra della barra laterale.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Screenshot della versione di aggiornamento visualizzata dopo l'accesso.":::

## <a name="forward-sensor-failure-alerts"></a>Avvisi di errore del sensore in diretta 

È possibile inviare avvisi a terze parti per fornire informazioni dettagliate su:

- Sensori disconnessi

- Errori di backup remoto

Queste informazioni vengono inviate quando si crea una regola di inoltra per le notifiche di sistema.

> [!NOTE]
> Gli amministratori possono inviare notifiche di sistema.

Per inviare notifiche:

1. Accedere alla console di gestione locale.
1. Selezionare **inoltri** dal menu laterale.
1. Creare una regola di invio.
1. Selezionare **notifiche di sistema del report**.

Per ulteriori informazioni sulle regole di invio, vedere [le informazioni di invio dell'avviso](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Modificare le proprietà di sistema

Le proprietà di sistema controllano varie operazioni e impostazioni nel sensore. La modifica o la modifica di tali elementi potrebbe danneggiare il funzionamento della console del sensore.

Prima di modificare le impostazioni, consultare [supporto tecnico Microsoft](https://support.microsoft.com/) .

Per accedere alle proprietà di sistema:

1. Accedere alla console di gestione locale o al sensore.

2. Selezionare **impostazioni di sistema**.

3. Selezionare **proprietà di sistema** nella sezione **generale** .

## <a name="see-also"></a>Vedere anche

[Ricerca e pacchetti di intelligence per le minacce](how-to-work-with-threat-intelligence-packages.md)

[Gestire i sensori dalla console di gestione](how-to-manage-sensors-from-the-on-premises-management-console.md)