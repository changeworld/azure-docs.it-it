---
title: Configurare applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Fornisce una guida per l'esecuzione del comando configure dello strumento di snapshot coerente applicazione Azure che è possibile utilizzare con Azure NetApp Files.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632978"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>Configurare applicazione Azure strumento snapshot coerente (anteprima)

Questo articolo fornisce una guida per l'esecuzione del comando configure dello strumento di snapshot coerente applicazione Azure che è possibile usare con Azure NetApp Files.

## <a name="introduction"></a>Introduzione

Il file di configurazione può essere creato o modificato tramite il `azacsnap -c configure` comando.

## <a name="command-options"></a>Opzioni del comando

Per il `-c configure` comando sono disponibili le opzioni seguenti:

- `--configuration new` per creare un nuovo file di configurazione.

- `--configuration edit` per modificare un file di configurazione esistente.

- `[--configfile <config filename>]` è un parametro facoltativo che consente i nomi dei file di configurazione personalizzati.

## <a name="configuration-file-for-snapshot-tools"></a>File di configurazione per gli strumenti di snapshot

È possibile creare un file di configurazione eseguendo `azacsnap -c configure --configuration new` .  Per impostazione predefinita, il nome del file di configurazione è `azacsnap.json` .  Un nome file personalizzato può essere usato con il `--configfile=` parametro (ad esempio `--configfile=<customname>.json` ). l'esempio seguente è relativo alla configurazione di istanze large di Azure:

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>Dettagli dei valori obbligatori

Le sezioni seguenti forniscono indicazioni dettagliate sui vari valori necessari per il file di configurazione.

### <a name="sap-hana-values"></a>Valori SAP HANA

Quando si aggiunge un *database* alla configurazione, sono necessari i valori seguenti:

- **Indirizzo del server Hana** = il nome host o l'indirizzo IP del server SAP Hana.
- **Hana SID** = ID del sistema di SAP Hana.
- **Numero di istanza di Hana** = il numero di istanza del SAP Hana.
- **Chiave dell'archivio utente Hana HDB** = il SAP Hana utente configurato con le autorizzazioni per l'esecuzione dei backup del database.

- Nodo singolo: IP e nome host del nodo
- HSR con STONITH: IP e nome host del nodo
- Scalabilità orizzontale (N + N, N + M): IP del nodo master corrente e nome host
- HSR senza STONITH: IP e nome host del nodo
- Più SID in un singolo nodo: nome host e IP del nodo che ospita i SID

### <a name="azure-large-instance-hli-storage-values"></a>Valori di archiviazione di istanze large di Azure (HLI)

Quando si aggiunge una risorsa di *archiviazione HLI* a una sezione del database, sono necessari i valori seguenti:

- **Storage User Name** = questo valore è il nome utente usato per stabilire la connessione SSH alla risorsa di archiviazione.
- **Storage IP address** = indirizzo del sistema di archiviazione.
- **Storage volume name** = nome del volume da snapshot.  Questo valore può essere determinato in diversi modi, probabilmente il più semplice consiste nel provare il comando della Shell seguente:

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Valori di archiviazione Azure NetApp Files (e)

Quando si aggiunge una risorsa di *archiviazione e* a una sezione del database, sono necessari i valori seguenti:

- **Nome utente autenticazione entità servizio** = questo è il `authfile.json` file generato nel cloud shell quando si configura la comunicazione con Azure NetApp files archiviazione.
- **ID risorsa volume di archiviazione e completo** = ID risorsa completo del volume da snapshot.  Questa operazione può essere recuperata da: portale di Azure-> e-> volume-> Settings/Properties-> Resource ID

## <a name="configuration-file-overview-azacsnapjson"></a>Cenni preliminari sul file di configurazione ( `azacsnap.json` )

Nell'esempio seguente, l'oggetto `azacsnap.json` viene configurato con l'unico SID.

I valori dei parametri devono essere impostati sull'ambiente di SAP HANA specifico del cliente.
Per il sistema di **istanze large di Azure** , queste informazioni vengono fornite da Microsoft Service Management durante la chiamata di onboarding/restituzione e vengono rese disponibili in un file di Excel fornito durante la consegna. Se è necessario fornire nuovamente queste informazioni, aprire una richiesta di servizio.

Di seguito è riportato solo un esempio. aggiornare tutti i valori di conseguenza.

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
              }
            ]
          }
        ],
        "anfStorage": []
      }
    }
  ]
}
```

> [!NOTE]
> Per uno scenario di ripristino di emergenza in cui i backup devono essere eseguiti nel sito di ripristino di emergenza, il nome del server HANA configurato nel file di configurazione di ripristino di emergenza, ad esempio, nel sito di ripristino di emergenza `DR.json` deve corrispondere al nome del server di produzione.

> [!NOTE]
> Per istanze di grandi dimensioni di Azure, l'indirizzo IP di archiviazione deve trovarsi nella stessa subnet del pool di server. Ad esempio, in questo caso, la subnet del pool di server è 172. 18. 18.0/24 e l'IP di archiviazione assegnato è 172.18.18.11.

## <a name="next-steps"></a>Passaggi successivi

- [Testare applicazione Azure strumento di snapshot coerente](azacsnap-cmd-ref-test.md)
