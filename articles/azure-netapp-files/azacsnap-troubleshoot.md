---
title: Risolvere i problemi applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Fornisce contenuto per la risoluzione dei problemi per l'utilizzo dello strumento applicazione Azure snapshot coerente che è possibile utilizzare con Azure NetApp Files.
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
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 903cb3323b9441ec8bb382054f065760875e3e89
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632834"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>Risolvere i problemi di applicazione Azure strumento snapshot coerente (anteprima)

Questo articolo fornisce contenuto per la risoluzione dei problemi relativi all'uso dello strumento di snapshot coerente applicazione Azure che è possibile usare con Azure NetApp Files.

Di seguito sono riportati alcuni problemi comuni che possono verificarsi durante l'esecuzione dei comandi. Per risolvere il problema, seguire le istruzioni di risoluzione indicate. Se si verifica ancora un problema, aprire una richiesta di servizio da portale di Azure e assegnare la richiesta nella coda SAP HANA istanze large per supporto tecnico Microsoft rispondere.

## <a name="log-files"></a>File di registro

Una delle fonti migliori di informazioni per il debug di eventuali errori con AzAcSnap è costituita dai file di log.  

### <a name="log-file-location"></a>Posizione dei file di log

I file di log vengono archiviati nella directory configurata in base al `logPath` parametro nel file di configurazione AzAcSnap.  Il nome del file di configurazione predefinito è `azacsnap.json` e il valore predefinito per `logPath` è `"./logs"` che indica che i file di log vengono scritti nella `./logs` Directory rispetto alla posizione in cui `azacsnap` viene eseguito il comando.  La creazione di `logPath` un percorso assoluto (ad esempio `/home/azacsnap/logs` ) garantirà `azacsnap` l'output dei log in `/home/azacsnap/logs` indipendentemente da dove `azacsnap` è stato eseguito il comando.

### <a name="log-file-naming"></a>Denominazione dei file di log

Il nome del file di log è basato sul nome dell'applicazione, ad esempio, `azacsnap` l'opzione di comando ( `-c` ) usata (ad esempio,, e `backup` `test` `details` così via) e il nome del file di configurazione (ad esempio, default = `azacsnap.json` ).  Quindi, se si usa il `-c backup` comando, il nome file di log per impostazione predefinita `azacsnap-backup-azacsnap.log` è e viene scritto nella directory configurata da `logPath` .  

Questa convenzione di denominazione è stata stabilita per consentire più file di configurazione, uno per database, e garantire la facilità di individuazione dei file di log associati.  Se pertanto il nome del file di configurazione è `SID.json` , il nome del file di risultato quando si utilizzano le `azacsnap -c backup --configfile SID.json` opzioni sarà `azacsnap-backup-SID.log` .

### <a name="result-file-and-syslog"></a>File di risultati e syslog

Per l' `-c backup` opzione di comando AzAcSnap scrive in un `*.result` file e nel registro di sistema ( `/var/log/messages` ) utilizzando il `logger` comando.  Il `*.result` nome file ha lo stesso nome di base del [file di log](#log-file-naming) e viene inserito nello stesso percorso del [file di log](#log-file-location).  Si tratta di un semplice file di output di una riga per gli esempi seguenti.

Output di esempio dal `*.result` file.
```output
Database # 1 (PR1) : completed ok
```

Output di esempio dal `/var/log/messages` file.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-sap-hana"></a>Comunicazione non riuscita con SAP HANA

Quando si convalida la comunicazione con SAP HANA eseguendo un test con `azacsnap -c test --test hana` e viene visualizzato l'errore seguente:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Soluzione:**

1. Controllare il file di configurazione (ad esempio, `azacsnap.json` ) per ogni istanza di Hana per assicurarsi che i valori del database SAP Hana siano corretti.
1. Provare a eseguire il comando seguente per verificare se il `hdbsql` comando si trova nel percorso ed è in grado di connettersi al Server SAP Hana. Nell'esempio seguente viene illustrata l'esecuzione corretta del comando e del relativo output.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    In questo esempio, il `hdbsql` comando non è incluso negli utenti `$PATH` .

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    In questo esempio, il `hdbsql` comando viene aggiunto temporaneamente all'utente `$PATH` , ma quando l'esecuzione mostra che la chiave di connessione non è stata configurata correttamente con il `hdbuserstore Set` comando (per informazioni dettagliate, vedere Introduzione Guida):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > Per aggiungere in modo permanente all'utente `$PATH` , aggiornare il file dell'utente `$HOME/.profile`

## <a name="the-hdbuserstore-location"></a>`hdbuserstore`Posizione

Quando si configura la comunicazione con SAP HANA, il `hdbuserstore` programma viene usato per creare le impostazioni di comunicazione sicura.  Il `hdbuserstore` programma si trova in genere in `/usr/sap/<SID>/SYS/exe/hdb/` o `/usr/sap/hdbclient` .  Normalmente il programma di installazione aggiunge il percorso corretto all' `azacsnap` utente `$PATH` .

## <a name="failed-test-with-storage"></a>Test non superato con archiviazione

Il comando `azacsnap -c test --test storage` non viene completato correttamente.

### <a name="azure-large-instance"></a>Istanza large di Azure

L'esempio seguente viene eseguito `azacsnap` in SAP Hana in un'istanza large di Azure:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Soluzione:** L'errore precedente viene visualizzato in genere quando l'utente di archiviazione di istanze large di Azure non ha accesso all'archiviazione sottostante. Per convalidare l'accesso alla risorsa di archiviazione con l'utente di archiviazione specificato, eseguire il `ssh` comando per convalidare la comunicazione con la piattaforma di archiviazione.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Esempio con output previsto:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>Non è possibile stabilire l'autenticità dell'host ' 172.18.18.11 (172.18.18.11)'

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Soluzione:** Non selezionare Sì. Verificare che l'indirizzo IP di archiviazione sia corretto. Se il problema persiste, confermare l'indirizzo IP di archiviazione con il team operativo Microsoft.

### <a name="azure-netapp-files"></a>Azure NetApp Files

L'esempio seguente è l'esecuzione di `azacsnap` in una macchina virtuale con Azure NetApp Files:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Soluzione:**

1. Verificare l'esistenza del file dell'entità servizio, `azureauth.json` , come impostato nel file di `azacsnap.json` configurazione.
1. Controllare il file di log (ad esempio, `logs/azacsnap-test-azacsnap.log` ) per verificare se l'entità servizio ( `azureauth.json` ) dispone del contenuto corretto.  Esempio da log, come indicato di seguito:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Controllare il file di log (ad esempio, `logs/azacsnap-test-azacsnap.log` ) per verificare se l'entità servizio ( `azureauth.json` ) è scaduta. Esempio da log, come indicato di seguito:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Passaggi successivi

- [Suggerimenti](azacsnap-tips.md)
