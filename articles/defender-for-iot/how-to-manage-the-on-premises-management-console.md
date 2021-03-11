---
title: Gestire la console di gestione locale
description: Informazioni sulle opzioni di console di gestione locali come backup e ripristino, definendo il nome host e impostando un proxy per i sensori.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f3c9f8f78f17153c3d2eb7b014cf616253b3c0c9
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618254"
---
# <a name="manage-the-on-premises-management-console"></a>Gestire la console di gestione locale

Questo articolo illustra le opzioni della console di gestione locale, ad esempio backup e ripristino, download del file di attivazione del dispositivo del Comitato, aggiornamento dei certificati e configurazione di un proxy ai sensori.

Caricare la console di gestione locale dal portale di Azure.

## <a name="upload-an-activation-file"></a>Carica un file di attivazione

Quando si accede per la prima volta, viene scaricato un file di attivazione per la console di gestione locale. Questo file contiene i dispositivi di cui è stato eseguito il commit aggregati definiti durante il processo di onboarding. L'elenco include i sensori associati a più sottoscrizioni.

Dopo l'attivazione iniziale, il numero di dispositivi monitorati può superare il numero di dispositivi di cui è stato eseguito il commit definiti durante l'onboarding. Questo evento potrebbe verificarsi, ad esempio, se si connettono più sensori alla console di gestione. In caso di discrepanza tra il numero di dispositivi monitorati e il numero di dispositivi di cui è stato eseguito il commit, viene visualizzato un avviso nella console di gestione. Se si verifica questo evento, è necessario caricare un nuovo file di attivazione.

Per caricare un file di attivazione:

1. Visitare la pagina relativa ai **prezzi** di Azure Defender per l'it.
1. Selezionare la scheda **scaricare il file di attivazione per la console di gestione** . Il file di attivazione viene scaricato.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Scaricare il file di attivazione.":::

1. Selezionare **impostazioni di sistema** dalla console di gestione.
1. Selezionare **Activation**.
1. Selezionare **Scegli un file** e selezionare il file salvato.

## <a name="manage-certificates"></a>Gestire i certificati

Dopo l'installazione della console di gestione locale, viene generato e utilizzato un certificato autofirmato locale per accedere all'applicazione Web della console di gestione. Quando gli utenti amministratori accedono alla console di gestione per la prima volta, viene richiesto di specificare un certificato SSL/TLS. Per ulteriori informazioni sull'impostazione per la prima volta, vedere [attivare e configurare la console di gestione locale](how-to-activate-and-set-up-your-on-premises-management-console.md).

Le sezioni seguenti forniscono informazioni sull'aggiornamento dei certificati, sull'uso dei comandi dell'interfaccia della riga di comando del certificato e sui certificati supportati e sui parametri del certificato.

### <a name="about-certificates"></a>Informazioni sui certificati

Azure Defender per Internet usa i certificati SSL e TLS per:

- Soddisfare i requisiti specifici di certificato e crittografia richiesti dall'organizzazione caricando il certificato firmato da un'autorità di certificazione.

- Consentire la convalida tra la console di gestione e i sensori connessi e tra una console di gestione e una console di gestione a disponibilità elevata. La convalida viene valutata in base a un elenco di revoche di certificati e alla data di scadenza del certificato. *Se la convalida ha esito negativo, la comunicazione tra la console di gestione e il sensore viene interrotta e viene visualizzato un errore di convalida nella console*. Questa opzione è abilitata per impostazione predefinita dopo l'installazione.

Le regole di invio di terze parti non vengono convalidate. Gli esempi sono le informazioni di avviso inviate a SYSLOG, Splunk o ServiceNow; e la comunicazione con Active Directory.

#### <a name="ssl-certificates"></a>Certificati SSL

Defender for Internet e la console di gestione locale usano SSL e i certificati TLS per le funzioni seguenti: 

 - Proteggere le comunicazioni tra gli utenti e la console Web dell'appliance. 
 
 - Proteggere le comunicazioni con l'API REST nel sensore e nella console di gestione locale.
 
 - Proteggere le comunicazioni tra i sensori e una console di gestione locale. 

Una volta installato, l'appliance genera un certificato autofirmato locale per consentire l'accesso preliminare alla console Web. SSL Enterprise e i certificati TLS possono essere installati utilizzando lo [`cyberx-xsense-certificate-import`](#cli-commands) strumento da riga di comando. 

 > [!NOTE]
 > Per le integrazioni e le regole di invio in cui l'appliance è il client e l'initiator della sessione, vengono usati certificati specifici e non sono correlati ai certificati di sistema.  
 >
 >In questi casi, i certificati vengono in genere ricevuti dal server o utilizzano la crittografia asimmetrica, in cui verrà fornito un certificato specifico per configurare l'integrazione. 

### <a name="update-certificates"></a>Aggiornare i certificati

Gli utenti amministratori della console di gestione locale possono aggiornare i certificati.

Per aggiornare un certificato:  

1. Selezionare **impostazioni di sistema**.

1. Selezionare **certificati SSL/TLS**.
1. Eliminare o modificare il certificato e aggiungerne uno nuovo.
   
   - Aggiungere un nome di certificato.
   
   - Caricare un file CRT e un file di chiave e immettere una passphrase.
   - Se necessario, caricare un file PEM.

Per modificare l'impostazione di convalida:

1. Attivare o disattivare l'interruttore **Abilita convalida certificati** .

1. Selezionare **Salva**.

Se l'opzione è abilitata e la convalida ha esito negativo, la comunicazione tra la console di gestione e il sensore viene interrotta e viene visualizzato un errore di convalida nella console di.

### <a name="certificate-support"></a>Supporto per i certificati

Sono supportati i certificati seguenti:

- Infrastruttura a chiave privata ed Enterprise (PKI privata)
 
- Infrastruttura a chiave pubblica (PKI pubblico) 

- Generato localmente nel dispositivo (autofirmato locale) 

  > [!IMPORTANT]
  > Non è consigliabile usare certificati autofirmati. Questo tipo di connessione non è sicuro e deve essere usato solo per gli ambienti di test. Poiché il proprietario del certificato non può essere convalidato e non è possibile mantenere la sicurezza del sistema, i certificati autofirmati non devono mai essere usati per le reti di produzione.

### <a name="supported-ssl-certificates"></a>Certificati SSL supportati 

Sono supportati i seguenti parametri: 

**CRT certificato**

- Il file di certificato primario per il nome di dominio

- Algoritmo di firma = SHA256RSA
- Algoritmo hash della firma = SHA256
- Valido da = data passata valida
- Valido per = data futura valida
- Chiave pubblica = RSA 2048 bit (minimo) o 4096 bit
- Punto di distribuzione CRL = URL del file con estensione CRL
- Subject CN = URL, può essere un certificato con caratteri jolly; ad esempio, Sensor. contoso. <span> com o *. contoso. <span> com
- Subject (C) ountry = defined, ad esempio US
- Oggetto (OU) unità organizzativa = definito; ad esempio, contoso Labs
- Subject (O) ganizzazione = defined; ad esempio, Contoso Inc.

**File di chiave**

- Il file di chiave generato al momento della creazione del CSR

- RSA 2048 bit (minimo) o 4096 bit

 > [!Note]
 > Utilizzando una lunghezza della chiave di 4096bits:
 > - L'handshake SSL all'inizio di ogni connessione sarà più lento.  
 > - Si è verificato un aumento dell'utilizzo della CPU durante gli handshake. 

**Catena di certificati**

- Il file di certificato intermedio, se presente, fornito dalla CA.

- Il certificato della CA che ha emesso il certificato del server deve essere il primo nel file, seguito da qualsiasi altro fino alla radice. 
- La catena può includere attributi del contenitore.

**Passphrase**

- È supportata una chiave.

- Configurare durante l'importazione del certificato.

I certificati con altri parametri potrebbero funzionare, ma Microsoft non li supporta.

#### <a name="encryption-key-artifacts"></a>Elementi chiave di crittografia

**. pem: file contenitore di certificati**

I file Privacy Enhanced Mail (PEM) erano il tipo di file generale usato per proteggere i messaggi di posta elettronica. Attualmente, i file PEM vengono usati con i certificati e usano le chiavi ASN1 di X509.  

Il file contenitore è definito in RFC da 1421 a 1424, un formato contenitore che può includere solo il certificato pubblico. Ad esempio, Apache installa, un certificato CA, file e così via, SSL o certificati. Può includere un'intera catena di certificati, tra cui la chiave pubblica, la chiave privata e i certificati radice.  

Può anche codificare un CSR come formato PKCS10, che può essere convertito in PEM.

**. cert. cer. CRT: file contenitore di certificati**

Oggetto `.pem` o `.der` file formattato con un'estensione diversa. Il file viene riconosciuto da Esplora risorse come un certificato. Il `.pem`   file non è riconosciuto da Esplora risorse.

**. Key: file di chiave privata**

Un file di chiave è nello stesso formato di un file PEM, ma ha un'estensione diversa. 

#### <a name="additional-commonly-available-key-artifacts"></a>Elementi chiave aggiuntivi comunemente disponibili

**. CSR: richiesta di firma del certificato**.  

Questo file viene usato per l'invio alle autorità di certificazione. Il formato effettivo è PKCS10, definito in RFC 2986, e può includere alcuni o tutti i dettagli chiave del certificato richiesto. Ad esempio, Subject, Organization e state. Si tratta della chiave pubblica del certificato che viene firmata dalla CA e riceve un certificato restituito.  

Il certificato restituito è il certificato pubblico, che include la chiave pubblica ma non la chiave privata. 

**. Pkcs12. pfx. P12-contenitore di password**. 

Originariamente definito da RSA in Public-Key Cryptography Standards (PKCS), il 12 Variant è stato originariamente migliorato da Microsoft e successivamente inviato come RFC 7292.  

Questo formato del contenitore richiede una password che contenga sia coppie di certificati pubbliche che private. A differenza dei `.pem`   file, questo contenitore è completamente crittografato.  

È possibile usare OpenSSL per convertire il file in un `.pem`   file con chiavi pubbliche e private: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der: PEM con codifica binaria**.

Per codificare la sintassi ASN. 1 in formato binario, è possibile usare un `.pem`   file, che è solo un file con codifica Base64 `.der` . 

OpenSSL può convertire questi file in `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

Windows rileverà questi file come file di certificato. Per impostazione predefinita, Windows esporterà `.der` i certificati come file formattati con un'estensione diversa.

**. CRL-elenco di revoche di certificati**.  

Le autorità di certificazione producono questi metodi per annullare l'autorizzazione dei certificati prima della scadenza. 

#### <a name="cli-commands"></a>Comandi dell'interfaccia della riga di comando

Usare il `cyberx-xsense-certificate-import` comando dell'interfaccia della riga di comando per importare i certificati. Per usare questo strumento, è necessario caricare i file di certificato nel dispositivo usando strumenti come WinSCP o wget.

Il comando supporta i flag di input seguenti:

- `-h`: Mostra la sintassi della guida della riga di comando.

- `--crt`: Percorso di un file di certificato (estensione CRT).

- `--key`:  \* . file di chiave. La lunghezza della chiave deve essere almeno di 2.048 bit.

- `--chain`: Percorso di un file della catena di certificati (facoltativo).

- `--pass`: Passphrase utilizzata per crittografare il certificato (facoltativo).

- `--passphrase-set`: Valore predefinito = `False` , non utilizzato. Impostare su `True` per usare la passphrase precedente fornita con il certificato precedente (facoltativo).

Quando si usa il comando CLI:

- Verificare che i file di certificato siano leggibili nell'appliance.

- Verificare che il nome di dominio e l'IP nel certificato corrispondano alla configurazione pianificata dal reparto IT.

### <a name="use-openssl-to-manage-certificates"></a>Usare OpenSSL per gestire i certificati

Gestire i certificati con i comandi seguenti:

| Descrizione | Comando CLI |
|--|--|
| Genera una nuova chiave privata e una richiesta di firma del certificato | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Generare un certificato autofirmato | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Generare una richiesta di firma del certificato (CSR) per una chiave privata esistente | `openssl req -out CSR.csr -key privateKey.key -new` |
| Genera una richiesta di firma del certificato basata su un certificato esistente | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Rimuovere una passphrase da una chiave privata | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Se è necessario controllare le informazioni all'interno di un certificato, CSR o chiave privata, usare i comandi seguenti:

| Descrizione | Comando CLI |
|--|--|
| Verificare una richiesta di firma del certificato (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Controllare una chiave privata | `openssl rsa -in privateKey.key -check` |
| Controllare un certificato | `openssl x509 -in certificate.crt -text -noout`  |

Se viene visualizzato un errore che indica che la chiave privata non corrisponde al certificato o che un certificato installato in un sito non è attendibile, usare questi comandi per correggere l'errore.

| Descrizione | Comando CLI |
|--|--|
| Controllare un hash MD5 della chiave pubblica per assicurarsi che corrisponda a ciò che si trova in una CSR o una chiave privata | 1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Per convertire i certificati e le chiavi in formati diversi per renderli compatibili con tipi specifici di server o software, usare questi comandi;

| Descrizione | Comando CLI |
|--|--|
| Convertire un file DER (. CRT. cer. der) in PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Convertire un file PEM in DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Convertire un file PKCS # 12 (. pfx. P12) contenente una chiave privata e i certificati per PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />È possibile aggiungere `-nocerts` solo l'output della chiave privata oppure aggiungere `-nokeys` solo i certificati. |
| Convertire un file di certificato PEM e una chiave privata in PKCS # 12 (. pfx. P12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>Definire le impostazioni di backup e ripristino

Il backup del sistema della console di gestione locale viene eseguito automaticamente, quotidianamente. I dati vengono salvati in un disco diverso. Il percorso predefinito è `/var/cyberx/backups`. 

Il file può essere trasferito automaticamente alla rete interna. 

> [!NOTE]
> È possibile eseguire la procedura di backup e ripristino solo nella stessa versione. 

Per eseguire il backup del computer della console di gestione locale: 

- Accedere a un account amministrativo e immettere `sudo cyberx-management-backup -full` .

Per ripristinare il file di backup più recente:

- Accedere a un account amministrativo e immettere `$ sudo cyberx-management-system-restore` .

Per salvare il backup in un server SMB esterno:

1. Creare una cartella condivisa nel server SMB esterno.  

   Ottenere il percorso della cartella, il nome utente e la password necessari per accedere al server SMB. 

2. In Defender for Internet, creare una directory per i backup:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Modifica fstab:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Modificare o creare le credenziali per il server SMB da condividere: 

   - `sudo nano /etc/samba/user` 

5. Aggiungere: 

   - `username=<user name>`

   - `password=<password>` 

6. Montare la directory: 

   - `sudo mount -a` 

7. Configurare una directory di backup nella cartella condivisa nel Defender per la console di gestione locale:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Modificare il nome host

Per modificare il nome host della console di gestione configurato nel server DNS dell'organizzazione:

1. Nel riquadro sinistro della console di gestione selezionare **impostazioni di sistema**.  

2. Nella sezione rete della console selezionare **rete**. 

3. Immettere il nome host configurato nel server DNS aziendale. 

4. Selezionare **Salva**.

## <a name="define-vlan-names"></a>Definire i nomi VLAN

I nomi VLAN non sono sincronizzati tra il sensore e la console di gestione. È necessario definire nomi identici nei componenti.

Nell'area rete selezionare **VLAN** e aggiungere i nomi agli ID VLAN individuati. Selezionare quindi **Salva**.

## <a name="define-a-proxy-to-sensors"></a>Definire un proxy ai sensori

Migliorare la sicurezza del sistema impedendo l'accesso utente direttamente al sensore. Usare invece il tunneling del proxy per consentire agli utenti di accedere al sensore dalla console di gestione locale con una singola regola del firewall. Questo miglioramento riduce la possibilità di accesso non autorizzato all'ambiente di rete oltre il sensore.

Usare un proxy in ambienti in cui non esiste una connettività diretta ai sensori.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Utente.":::

La seguente procedura connette un sensore alla console di gestione locale e Abilita il tunneling su tale console:

1. Accedere all'interfaccia della riga di comando dell'appliance della console di gestione locale con credenziali amministrative.

2. Digitare `sudo cyberx-management-tunnel-enable` e premere **invio**.

4. Digitare `--port 10000` e premere **invio**.

## <a name="adjust-system-properties"></a>Modificare le proprietà di sistema

Le proprietà di sistema controllano varie operazioni e impostazioni nella console di gestione. La modifica o la modifica di tali elementi potrebbe danneggiare l'operazione della console di gestione. Consultare [supporto tecnico Microsoft](https://support.microsoft.com) prima di modificare le impostazioni.

Per accedere alle proprietà di sistema: 

1. Accedere alla console di gestione locale o al sensore.

2. Selezionare **impostazioni di sistema**.

3. Selezionare **proprietà di sistema** nella sezione **generale** .

## <a name="change-the-name-of-the-on-premises-management-console"></a>Modificare il nome della console di gestione locale

È possibile modificare il nome della console di gestione locale. I nuovi nomi vengono visualizzati nel Web browser console, in diverse finestre della console e nei log per la risoluzione dei problemi. Il nome predefinito è **Management Console**.

Per modificare il nome:

1. Nella parte inferiore del riquadro sinistro selezionare il nome corrente.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Screenshot della versione della console di gestione locale.":::

2. Nella finestra di dialogo **modifica configurazione console di gestione** immettere il nuovo nome. Il nome non può contenere più di 25 caratteri.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Screenshot della modifica del Defender per la configurazione della piattaforma Internet.":::

3. Selezionare **Salva**. Viene applicato il nuovo nome.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Screenshot che mostra il nome modificato della console.":::

## <a name="password-recovery"></a>Ripristino della password

Il recupero della password per la console di gestione locale è associato alla sottoscrizione a cui è collegato il dispositivo. Non è possibile recuperare una password se non si conosce la sottoscrizione a cui è collegato un dispositivo.

Per reimpostare la password:

1. Passare alla pagina di accesso della console di gestione locale.
1. Selezionare il **ripristino della password**.
1. Copiare l'identificatore univoco.
1. Passare alla pagina Defender per i **siti e i sensori** e selezionare la scheda **Ripristina password** .
1. Immettere l'identificatore univoco e selezionare **Ripristina**. Il file di attivazione viene scaricato.
1. Passare alla pagina di **ripristino della password** e caricare il file di attivazione.
1. Selezionare **Avanti**.
 
   A questo punto è stato assegnato il nome utente e una nuova password generata dal sistema.

> [!NOTE]
> Il sensore è collegato alla sottoscrizione a cui è stata originariamente connessa. È possibile recuperare la password solo utilizzando la stessa sottoscrizione a cui è collegata.

## <a name="update-the-software-version"></a>Aggiornare la versione del software

Nella procedura seguente viene descritto come aggiornare la versione del software della console di gestione locale. Il processo di aggiornamento richiede circa 30 minuti.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Passa a Defender for Internet.

1. Passare alla pagina **aggiornamenti** .

1. Selezionare una versione dalla sezione console di gestione locale.

1. Selezionare **Download** e salvare il file.

1. Accedere alla console di gestione locale e selezionare **impostazioni di sistema** dal menu laterale.

1. Nel riquadro **aggiornamento versione** selezionare **Aggiorna**.

1. Selezionare il file scaricato dalla pagina Defender for Internet **Updates** .

## <a name="mail-server-settings"></a>Impostazioni del server di posta

Definire le impostazioni del server di posta SMTP per la console di gestione locale.

Per definire:

1. Accedere all'interfaccia della riga di comando per la gestione locale con credenziali amministrative.
1. Digitare ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Premere INVIO. Vengono visualizzati i messaggi di richiesta seguenti.
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. Immettere il nome del server SMTP e il mittente e premere INVIO.

## <a name="see-also"></a>Vedi anche

[Gestire i sensori dalla console di gestione](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Gestire singoli sensori](how-to-manage-individual-sensors.md)
