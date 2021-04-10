---
title: Installare lo strumento di snapshot coerente applicazione Azure per Azure NetApp Files | Microsoft Docs
description: Viene fornita una guida per l'installazione dello strumento applicazione Azure snapshot coerente che è possibile utilizzare con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 458f4d3f29cb08a94095167ed45133f5cd70f5f4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869192"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Installare applicazione Azure strumento di snapshot coerente (anteprima)

In questo articolo viene fornita una guida per l'installazione dello strumento applicazione Azure snapshot coerente che è possibile utilizzare con Azure NetApp Files.

## <a name="introduction"></a>Introduzione

Il programma di installazione automatico scaricabile è progettato per semplificare la configurazione e l'esecuzione degli strumenti di snapshot con privilegi utente non radice (ad esempio, azacsnap). Il programma di installazione configurerà l'utente e inserirà gli strumenti di snapshot nella `$HOME/bin` sottodirectory Users (impostazione predefinita = `/home/azacsnap/bin` ).
Il programma di installazione automatico tenta di determinare le impostazioni e i percorsi corretti per tutti i file in base alla configurazione dell'utente che esegue l'installazione (ad esempio, root). Se i passaggi preliminari (abilitazione della comunicazione con archiviazione e SAP HANA) sono stati eseguiti come radice, l'installazione copierà la chiave privata e `hdbuserstore` il percorso dell'utente di backup. Tuttavia, è possibile che i passaggi che consentono la comunicazione con il back-end di archiviazione e SAP HANA vengano eseguiti manualmente da un amministratore informato dopo l'installazione.

## <a name="prerequisites-for-installation"></a>Prerequisiti per l'installazione

Seguire le linee guida per configurare ed eseguire gli snapshot e i comandi per il ripristino di emergenza. Si consiglia di completare i passaggi seguenti come radice prima di installare e usare gli strumenti di snapshot.

1. **Sistema operativo con patch**: vedere applicazione di patch e configurazione di SMT in [come installare e configurare SAP Hana (istanze large) in Azure](../virtual-machines/workloads/sap/hana-installation.md#operating-system).
1. **La sincronizzazione dell'ora è configurata**. Il cliente dovrà fornire un server ora compatibile con NTP e configurare il sistema operativo di conseguenza.
1. **Hana è installato** : vedere le istruzioni di installazione di Hana nell' [installazione di SAP NETWEAVER nel database Hana](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database).
1. **[Abilitare la comunicazione con l'archiviazione](#enable-communication-with-storage)** (per altre informazioni, vedere la sezione separata): il cliente deve configurare SSH con una coppia di chiavi pubblica/privata e fornire la chiave pubblica per ogni nodo in cui è pianificato l'esecuzione degli strumenti di snapshot per le operazioni Microsoft per l'installazione nel back-end di archiviazione.
   1. **Per Azure NetApp Files (per informazioni dettagliate, vedere la sezione separata)**: il cliente deve generare il file di autenticazione dell'entità servizio.
   1. **Per istanze di grandi dimensioni di Azure (vedere la sezione separata per i dettagli)**: il cliente deve configurare SSH con una coppia di chiavi privata/pubblica e fornire la chiave pubblica per ogni nodo in cui gli strumenti di snapshot sono pianificati per l'esecuzione in operazioni Microsoft per l'installazione nel back-end di archiviazione.

      Testare questa impostazione usando SSH per connettersi a uno dei nodi (ad esempio, `ssh -l <Storage UserName> <Storage IP Address>` ).
      Digitare `exit` per disconnettersi dalla richiesta di archiviazione.

      Microsoft Operations fornirà l'utente di archiviazione e l'indirizzo IP di archiviazione al momento del provisioning.
  
1. **[Abilitare la comunicazione con SAP Hana](#enable-communication-with-sap-hana)** (per altre informazioni, vedere la sezione separata): il cliente deve configurare un utente SAP Hana appropriato con i privilegi necessari per eseguire lo snapshot.
   1. Questa impostazione può essere testata dalla riga di comando come segue usando il testo in `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - Gli esempi precedenti sono per la comunicazione non SSL SAP HANA.

## <a name="enable-communication-with-storage"></a>Abilitare la comunicazione con l'archiviazione

Questa sezione illustra come abilitare la comunicazione con l'archiviazione.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Creare un'entità servizio RBAC

1. All'interno di una sessione di Azure Cloud Shell, assicurarsi di avere eseguito l'accesso alla sottoscrizione in cui si desidera essere associati all'entità servizio per impostazione predefinita:

    ```azurecli-interactive
    az account show
    ```

1. Se la sottoscrizione non è corretta, usare

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Creare un'entità servizio usando l'interfaccia della riga di comando di Azure in base all'esempio seguente

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. Verrà generato un output simile al seguente esempio:

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > Questo comando assegna automaticamente il ruolo Collaboratore RBAC all'entità servizio a livello di sottoscrizione, è possibile restringere l'ambito al gruppo di risorse specifico in cui i test creeranno le risorse.

1. Tagliare e incollare il contenuto di output in un file denominato `azureauth.json` archiviato nello stesso sistema del `azacsnap` comando e proteggere il file con le autorizzazioni di sistema appropriate.

### <a name="azure-large-instance"></a>Istanza large di Azure

La comunicazione con il back-end di archiviazione viene eseguita su un canale SSH crittografato. I passaggi dell'esempio seguente consentono di fornire indicazioni sull'installazione di SSH per la comunicazione.

1. Modificare il `/etc/ssh/ssh_config` file

    Fare riferimento all'output seguente in cui `MACs hmac-sha1` è stata aggiunta la riga:

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Creare una coppia di chiavi privata/pubblica

    Utilizzando il comando di esempio seguente per generare la coppia di chiavi, non immettere una password durante la generazione di una chiave.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Inviare la chiave pubblica a Microsoft Operations

    Inviare l'output del `cat /root/.ssh/id_rsa.pub` comando (esempio riportato di seguito) a Microsoft Operations per abilitare gli strumenti di snapshot per la comunicazione con il sottosistema di archiviazione.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>Abilitare la comunicazione con SAP HANA

Gli strumenti di snapshot comunicano con SAP HANA ed è necessario che un utente disponga delle autorizzazioni appropriate per avviare e rilasciare il punto di salvataggio del database. Nell'esempio seguente viene illustrata la configurazione dell'utente SAP HANA V2 e del `hdbuserstore` per la comunicazione con il database SAP Hana.

I comandi di esempio seguenti configurano un utente (AZACSNAP) in SYSTEMDB su SAP HANA 2.
database, modificare l'indirizzo IP, i nomi utente e le password nel modo appropriato:

1. Connettersi a SYSTEMDB per creare l'utente

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. Creazione dell'utente

    Questo esempio crea l'utente AZACSNAP in SYSTEMDB.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Concedere le autorizzazioni utente

    Questo esempio Mostra come impostare l'autorizzazione per l'utente AZACSNAP in modo da consentire l'esecuzione di uno snapshot di archiviazione coerente con il database.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *Facoltativo* : impedisce la scadenza della password dell'utente

    > [!NOTE]
    > Prima di apportare questa modifica, verificare con i criteri aziendali.

   Questo esempio Mostra come disabilitare la scadenza della password per l'utente AZACSNAP, senza questa modifica la password dell'utente scadrà impedendo la corretta creazione degli snapshot.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. Configurare il SAP HANA archivio utenti protetto (modificare la password) in questo esempio viene usato il `hdbuserstore` comando della shell Linux per configurare l'archivio utenti SAP Hana protetto.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Controllare la SAP HANA archivio utenti protetto per verificare che l'archivio utenti protetto sia configurato correttamente, usare il `hdbuserstore` comando per elencare l'output simile all'esempio seguente. Altre informazioni sull'uso di `hdbuserstore` sono disponibili nel sito Web SAP.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>Uso di SSL per la comunicazione con SAP HANA

Lo `azacsnap` strumento usa il comando SAP Hana `hdbsql` per comunicare con SAP Hana. Questo include l'uso delle opzioni SSL per la crittografia della comunicazione con SAP HANA. `azacsnap` Usa le `hdbsql` opzioni SSL del comando come indicato di seguito.

Quando si usa l'opzione, vengono sempre usati i seguenti elementi `azacsnap --ssl` :

- `-e` -Abilita la crittografia TLS encryptionTLS/SSL. Il server sceglie il massimo disponibile.
- `-ssltrustcert` -Specifica se convalidare il certificato del server.
- `-sslhostnameincert "*"` -Specifica il nome host utilizzato per verificare l'identità del server. Specificando `"*"` come nome host, il nome host del server non viene convalidato.

Per la comunicazione SSL sono necessari anche i file di archivio di chiavi e di attendibilità.  Sebbene sia possibile archiviare questi file in posizioni predefinite in un'installazione di Linux, per assicurarsi che venga usato il materiale della chiave corretto per i vari sistemi di SAP HANA, ovvero nei casi in cui vengono usati file di archivio chiavi e di attendibilità diversi per ogni sistema SAP HANA, `azacsnap` prevede che i file dell'archivio di chiavi e di attendibilità siano archiviati nel `securityPath` percorso specificato nel `azacsnap` file di configurazione.

#### <a name="key-store-files"></a>File archivio chiavi

- Se si usano più SID con lo stesso materiale della chiave, è più facile creare collegamenti nel percorso securityPath come definito nel `azacsnap` file di configurazione.  Verificare che questi valori esistano per ogni SID usando SSL.
  - Per OpenSSL:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Per commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- Se si usano più SID con il materiale della chiave diverso per SID, copiare (o spostare e rinominare) i file nel percorso securityPath come definito nel file di configurazione dei SID `azacsnap` .
  - Per OpenSSL:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Per commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

Quando `azacsnap` chiama `hdbsql` , verrà aggiunto `-sslkeystore=<securityPath>/<SID>_keystore` alla riga di comando.

#### <a name="trust-store-files"></a>File di archivio dei trust

- Se si usano più SID con lo stesso materiale della chiave, creare collegamenti reali nel percorso securityPath, come definito nel `azacsnap` file di configurazione.  Verificare che questi valori esistano per ogni SID usando SSL.
  - Per OpenSSL:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Per commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- Se si usano più SID con il materiale della chiave diverso per SID, copiare (o spostare e rinominare) i file nel percorso securityPath come definito nel file di configurazione dei SID `azacsnap` .
  - Per OpenSSL:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Per commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> Il `<SID>` componente dei nomi di file deve essere l'identificatore di sistema SAP Hana tutti in lettere maiuscole, ad esempio,, `H80` `PR1` e così via.

Quando `azacsnap` chiama `hdbsql` , verrà aggiunto `-ssltruststore=<securityPath>/<SID>_truststore` alla riga di comando.

Pertanto, `azacsnap -c test --test hana --ssl openssl` quando si `SID` trova `H80` nel file di configurazione, esegue le `hdbsql` connessioni come indicato di seguito:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> Il `\` carattere è un ritorno a capo riga di comando per migliorare la chiarezza dei più parametri passati nella riga di comando.

## <a name="installing-the-snapshot-tools"></a>Installazione degli strumenti di snapshot

Il programma di installazione automatico scaricabile è progettato per semplificare la configurazione e l'esecuzione degli strumenti di snapshot con privilegi utente non radice (ad esempio, azacsnap). Il programma di installazione configurerà l'utente e inserirà gli strumenti di snapshot nella `$HOME/bin` sottodirectory Users (impostazione predefinita = `/home/azacsnap/bin` ).

Il programma di installazione automatico tenta di determinare le impostazioni e i percorsi corretti per tutti i file in base alla configurazione dell'utente che esegue l'installazione (ad esempio, root). Se i passaggi di installazione precedenti (abilitazione della comunicazione con archiviazione e SAP HANA) sono stati eseguiti come radice, l'installazione copierà la chiave privata e il nel `hdbuserstore` percorso dell'utente di backup. Tuttavia, è possibile che i passaggi che consentono la comunicazione con il back-end di archiviazione e SAP HANA vengano eseguiti manualmente da un amministratore informato dopo l'installazione.

> [!NOTE]
> Per SAP HANA precedenti sulle installazioni di istanze large di Azure, la directory degli strumenti di snapshot preinstallati era `/hana/shared/<SID>/exe/linuxx86_64/hdb` .

Con i [passaggi preliminari](#prerequisites-for-installation) completati, è ora possibile installare gli strumenti di snapshot usando il programma di installazione automatica come indicato di seguito:

1. Copiare il programma di installazione autonomo scaricato nel sistema di destinazione.
1. Eseguire il programma di installazione automatica come `root` utente, vedere l'esempio seguente. Se necessario, rendere il file eseguibile usando il `chmod +x *.run` comando.

Quando si esegue il comando di installazione automatica senza alcun argomento, viene visualizzata la guida sull'utilizzo del programma di installazione per installare gli strumenti di snapshot come indicato di seguito:

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> Il programma di installazione automatica offre un'opzione per estrarre (-X) gli strumenti di snapshot dal bundle senza eseguire alcuna operazione di creazione e configurazione dell'utente. Questo consente a un amministratore esperto di completare manualmente la procedura di installazione o di copiare i comandi per aggiornare un'installazione esistente.

### <a name="easy-installation-of-snapshot-tools-default"></a>Installazione semplificata di strumenti di snapshot (impostazione predefinita)

Il programma di installazione è stato progettato per installare rapidamente gli strumenti di snapshot per SAP HANA in Azure. Per impostazione predefinita, se il programma di installazione viene eseguito solo con l'opzione-I, eseguirà i passaggi seguenti:

1. Creare l'utente snapshot ' azacsnap ', la home directory e impostare l'appartenenza al gruppo.
1. Configurare l'account di accesso dell'utente azacsnap `~/.profile` .
1. Cercare nel file System le directory da aggiungere a azacsnap `$PATH` , che in genere sono i percorsi degli strumenti di SAP Hana, ad esempio `hdbsql` e `hdbuserstore` .
1. Cercare nel file System le directory da aggiungere a azacsnap `$LD_LIBRARY_PATH` . Per l'esecuzione corretta di molti comandi è necessario impostare un percorso di libreria, in modo da configurarlo per l'utente installato.
1. Copiare le chiavi SSH per l'archiviazione back-end per azacsnap dall'utente "root" (l'utente che esegue l'installazione). Si presuppone che l'utente "root" abbia già configurato la connettività all'archiviazione
    - vedere la sezione "[abilitare la comunicazione con l'archiviazione](#enable-communication-with-storage)".
1. Copiare il SAP HANA archivio utenti protetto della connessione per l'utente di destinazione, azacsnap. Si presuppone che l'utente "root" abbia già configurato l'archivio utenti protetto: vedere la sezione "abilitare la comunicazione con SAP HANA".
1. Gli strumenti di snapshot vengono estratti in `/home/azacsnap/bin/` .
1. I comandi in `/home/azacsnap/bin/` hanno il set di autorizzazioni (proprietà, bit eseguibile e così via).

Nell'esempio seguente viene illustrato l'output corretto del programma di installazione quando viene eseguito con l'opzione di installazione predefinita.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>Disinstallare gli strumenti di snapshot

Se gli strumenti di snapshot sono stati installati usando le impostazioni predefinite, la disinstallazione richiede solo la rimozione dell'utente per cui sono stati installati i comandi (impostazione predefinita = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>Installazione manuale degli strumenti di snapshot

In alcuni casi, è necessario installare manualmente gli strumenti, ma si consiglia di usare l'opzione predefinita del programma di installazione per semplificare questo processo.

Ogni riga che inizia con un `#` carattere Mostra i comandi di esempio che seguono il carattere vengono eseguiti dall'utente root. Alla `\` fine di una riga è il carattere di continuazione di riga standard per un comando della shell.

Come superuser radice, un'installazione manuale può essere eseguita come indicato di seguito:

1. Ottenere l'ID del gruppo "sapsys", in questo caso l'ID gruppo = 1010

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. Creare l'utente snapshot ' azacsnap ', la home directory e impostare l'appartenenza al gruppo usando l'ID gruppo del passaggio 1.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Verificare che l'account di accesso dell'utente azacsnap `.profile` esista.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Cercare nel file System le directory da aggiungere alla $PATH di azacsnap, che in genere sono i percorsi degli strumenti di SAP HANA, ad esempio `hdbsql` e `hdbuserstore` .

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Aggiungere il percorso aggiornato al profilo dell'utente

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Cercare nel file System le directory da aggiungere alla $LD _LIBRARY_PATH di azacsnap.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Aggiungere il percorso della libreria aggiornato al profilo dell'utente

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. In istanze large di Azure
    1. Copiare le chiavi SSH per l'archiviazione back-end per azacsnap dall'utente "root" (l'utente che esegue l'installazione). Si presuppone che l'utente "root" abbia già configurato la connettività all'archiviazione
       > vedere la sezione "[abilitare la comunicazione con l'archiviazione](#enable-communication-with-storage)".

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. Impostare correttamente le autorizzazioni utente per i file SSH

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. In Azure NetApp Files
    1. Configurare il percorso dell'utente in base `DOTNET_BUNDLE_EXTRACT_BASE_DIR` alle linee guida per l'estrazione dei singoli file di .NET Core.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Copiare il SAP HANA archivio utenti protetto della connessione per l'utente di destinazione, azacsnap. Si presuppone che l'utente "root" abbia già configurato l'archivio utenti protetto.
    > vedere la sezione "[abilitare la comunicazione con SAP Hana](#enable-communication-with-sap-hana)".

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. Impostare correttamente le autorizzazioni utente per i `hdbuserstore` file

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. Estrarre gli strumenti di snapshot in/Home/azacsnap/bin/.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. Eseguire l'eseguibile dei comandi

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Verificare che le autorizzazioni di proprietà corrette siano impostate nella home directory dell'utente

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>Completa la configurazione degli strumenti di snapshot

Il programma di installazione fornisce i passaggi da completare dopo l'installazione degli strumenti di snapshot.
Attenersi alla procedura seguente per configurare e testare gli strumenti di snapshot.  Dopo aver completato i test, eseguire il primo snapshot di archiviazione coerente con il database.

L'output seguente mostra i passaggi da completare dopo l'esecuzione del programma di installazione con le opzioni di installazione predefinite:

1. Modificare l'account utente snapshot
    1. `su - azacsnap`
1. Configurare l'archivio utenti protetti HANA
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Passa alla posizione dei comandi
   1. `cd /home/azacsnap/bin/`
1. Configurare il file dei dettagli del cliente
   1. `azacsnap -c configure –-configuration new`
1. Testare la connessione alla risorsa di archiviazione....
   1. `azacsnap -c test –-test storage`
1. Testare la connessione a HANA....
    1. senza SSL
       1. `azacsnap -c test –-test hana`
    1. con SSL, sarà necessario scegliere l'opzione SSL corretta
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Esegui il primo backup di snapshot
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

Il passaggio 2 sarà necessario se "[Abilita la comunicazione con SAP Hana](#enable-communication-with-sap-hana)" non è stata eseguita prima dell'installazione.

> [!NOTE]
> I comandi di test dovrebbero essere eseguiti correttamente. In caso contrario, i comandi potrebbero avere esito negativo.

## <a name="configuring-the-database"></a>Configurazione del database

In questa sezione viene illustrato come configurare il database di.

### <a name="sap-hana-configuration"></a>Configurazione di SAP HANA

Ci sono alcune modifiche consigliate da applicare a SAP HANA per garantire la protezione dei backup del log e del catalogo. Per impostazione predefinita, il `basepath_logbackup` e i `basepath_catalogbackup` relativi file verranno restituiti alla `$(DIR_INSTANCE)/backup/log` Directory ed è improbabile che questo percorso si trovi in un volume `azacsnap` configurato per lo snapshot di questi file non verrà protetto con gli snapshot di archiviazione.

Gli esempi di comando riportati di seguito `hdbsql` hanno lo scopo di illustrare l'impostazione dei percorsi di log e di catalogo su percorsi che si trovano su volumi di archiviazione che possono essere snapshot da `azacsnap` . Verificare che i valori nella riga di comando corrispondano alla configurazione del SAP HANA locale.

### <a name="configure-log-backup-location"></a>Configurare il percorso di backup del log

In questo esempio viene apportata la modifica al `basepath_logbackup` parametro.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>Configurare il percorso di backup del catalogo

In questo esempio viene apportata la modifica al `basepath_catalogbackup` parametro. Per prima cosa, verificare che il `basepath_catalogbackup` percorso esista nel file System, se non si crea il percorso con la stessa proprietà della directory.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Se è necessario creare il percorso, l'esempio seguente crea il percorso e imposta la proprietà e le autorizzazioni corrette. Questi comandi dovranno essere eseguiti come radice.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Nell'esempio seguente viene modificata l'impostazione SAP HANA.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>Controllare i percorsi di backup di log e cataloghi

Dopo avere apportato le modifiche precedenti, verificare che le impostazioni siano corrette con il comando seguente.
In questo esempio le impostazioni che sono state impostate seguendo le istruzioni riportate sopra vengono visualizzate come impostazioni di sistema.

> Questa query restituisce anche le impostazioni predefinite per il confronto.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Configurare il timeout del backup del log

L'impostazione predefinita per SAP HANA per eseguire un backup del log è di 900 secondi (15 minuti). È consigliabile ridurre questo valore a 300 secondi (ovvero 5 minuti).  È quindi possibile eseguire backup regolari (ad esempio, ogni 10 minuti) aggiungendo il volume log_backups nella sezione altro volume del file di configurazione.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Verificare il timeout del backup del log

Dopo aver apportato la modifica al timeout del backup del log, verificare che sia stato impostato come indicato di seguito.
In questo esempio le impostazioni impostate vengono visualizzate come impostazioni di sistema, ma questa query restituisce anche le impostazioni predefinite per il confronto.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Passaggi successivi

- [Configurare applicazione Azure strumento snapshot coerente](azacsnap-cmd-ref-configure.md)
