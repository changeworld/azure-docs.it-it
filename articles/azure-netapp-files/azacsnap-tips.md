---
title: Suggerimenti e consigli per l'uso di applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Vengono forniti suggerimenti e consigli per l'utilizzo dello strumento applicazione Azure snapshot coerente che è possibile utilizzare con Azure NetApp Files.
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
ms.openlocfilehash: 86d7b1a6d361ebbc0d8419d184f9a11d390a37f1
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803188"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>Suggerimenti e consigli per l'uso di applicazione Azure strumento snapshot coerente (anteprima)

Questo articolo fornisce suggerimenti e consigli che potrebbero essere utili quando si usa AzAcSnap.

## <a name="limit-service-principal-permissions"></a>Limitare le autorizzazioni dell'entità servizio

Potrebbe essere necessario limitare l'ambito dell'entità servizio AzAcSnap.  Per altri dettagli sulla gestione degli accessi con granularità fine delle risorse di Azure, vedere la [documentazione relativa a RBAC di Azure](https://docs.microsoft.com/azure/role-based-access-control/) .  

Di seguito è riportata una definizione di ruolo di esempio con le azioni minime necessarie per il funzionamento di AzAcSnap.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

## <a name="take-snapshots-manually"></a>Eseguire manualmente gli snapshot

Prima di eseguire i comandi di backup ( `azacsnap -c backup` ), verificare la configurazione eseguendo i comandi di test e verificare che vengano eseguiti correttamente.  L'esecuzione corretta dei test dimostrati `azacsnap` può comunicare con il database di SAP Hana installato e con il sistema di archiviazione sottostante del SAP Hana in un' **istanza di grandi dimensioni** o in un sistema **Azure NetApp files** di Azure.

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

Quindi, per eseguire un backup manuale dello snapshot del database, eseguire il comando seguente:

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>Configurare il backup automatico degli snapshot

Si tratta di una pratica comune nei sistemi UNIX/Linux da usare `cron` per automatizzare l'esecuzione di comandi in un sistema. La procedura standard per gli strumenti di snapshot consiste nel configurare l'utente `crontab` .

Di seguito è riportato un esempio di un oggetto `crontab` per l' `azacsnap` automazione degli snapshot.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

Spiegazione del crontab precedente.

- `MAILTO=""`: con un valore vuoto questa operazione impedisce a cron di provare automaticamente a inviare un messaggio di posta elettronica all'utente quando si esegue la voce crontab, in quanto sarebbe probabile che si trovi nel file di posta elettronica dell'utente locale.
- Le versioni abbreviate della temporizzazione per le voci crontab sono di chiara interpretazione:
  - `@monthly` = Eseguito una volta al mese, ovvero "0 0 1 * *".
  - `@weekly`  = Eseguito una volta alla settimana, ovvero "0 0 * * 0".
  - `@daily`   = Eseguito una volta al giorno, ovvero "0 0 * * *".
  - `@hourly`  = Esegui una volta all'ora, ovvero "0 * * * *".
- Le prime cinque colonne vengono usate per definire le ore, fare riferimento agli esempi di colonna seguenti:
  - `0,15,30,45`: Ogni 15 minuti
  - `0-23`: Ogni ora
  - `*` : Ogni giorno
  - `*` : Ogni mese
  - `*` : Ogni giorno della settimana
- Riga di comando da eseguire inclusa tra parentesi quadre "()"
  - `. /home/azacsnap/.profile` = esegue il pull del profilo dell'utente per configurare l'ambiente, inclusi $PATH e così via.
  - `cd /home/azacsnap/bin` = modificare la directory di esecuzione nel percorso "/Home/azacsnap/bin" in cui si trovano i file di configurazione.
  - `azacsnap -c .....` = comando azacsnap completo da eseguire, incluse tutte le opzioni.

Altre informazioni su cron e sul formato del file crontab sono disponibili qui: <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> Gli utenti sono responsabili del monitoraggio dei processi cron per garantire la corretta generazione degli snapshot.

## <a name="monitor-the-snapshots"></a>Monitorare gli snapshot

Per garantire un sistema integro, è necessario monitorare le condizioni seguenti:

1. Spazio su disco disponibile. Gli snapshot useranno lentamente lo spazio su disco, in quanto i blocchi disco meno recenti vengono conservati nello snapshot.
    1. Per automatizzare la gestione dello spazio su disco, utilizzare le `--retention` `--trim` Opzioni e per pulire automaticamente i vecchi snapshot e i file di log del database.
1. Esecuzione corretta degli strumenti di snapshot
    1. Controllare il `*.result` file in caso di esito positivo o negativo dell'ultima esecuzione di `azacsnap` .
    1. Controllare `/var/log/messages` l'output del `azacsnap` comando.
1. Coerenza degli snapshot tramite il ripristino periodico di un altro sistema.

> [!NOTE]
> Per elencare i dettagli dello snapshot, eseguire il comando `azacsnap -c details` .

## <a name="delete-a-snapshot"></a>Eliminare uno snapshot

Per eliminare uno snapshot, eseguire il comando `azacsnap -c delete` . Non è possibile eliminare gli snapshot dal livello del sistema operativo. `azacsnap -c delete`Per eliminare gli snapshot di archiviazione, è necessario usare il comando corretto ().

> [!IMPORTANT]
> Quando si elimina uno snapshot, è necessario essere vigili. Una volta eliminata, non è **possibile** recuperare gli snapshot eliminati.

## <a name="restore-a-snapshot"></a>Ripristinare uno snapshot

Uno snapshot del volume di archiviazione può essere ripristinato in un nuovo volume ( `-c restore --restore snaptovol` ).  Per istanze large di Azure, il volume può essere ripristinato in uno snapshot ( `-c restore --restore revertvolume` ).

> [!NOTE]
> Non è stato fornito **alcun** comando di ripristino del database.

Uno snapshot può essere copiato nuovamente nell'area dati SAP HANA, ma SAP HANA non deve essere in esecuzione quando viene eseguita una copia ( `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` ).

Per istanze di grandi dimensioni di Azure, è possibile contattare il team operativo Microsoft aprendo una richiesta di servizio per ripristinare uno snapshot desiderato dagli snapshot disponibili esistenti. È possibile aprire una richiesta di servizio da portale di Azure: <https://portal.azure.com>

Se si decide di eseguire il failover di ripristino di emergenza, il `azacsnap -c restore --restore revertvolume` comando nel sito di ripristino di emergenza renderà automaticamente disponibili gli snapshot del volume più recenti ( `/hana/data` e `/hana/logbackups` ) per consentire un ripristino SAP Hana. Usare questo comando con cautela mentre interrompe la replica tra i siti di produzione e di ripristino di emergenza.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>Configurare gli snapshot solo per i volumi ' boot '

> [!IMPORTANT]
> Questa operazione si applica solo alle istanze large di Azure.

In alcuni casi, i clienti dispongono già di strumenti per proteggere SAP HANA e vogliono configurare solo gli snapshot del volume di "avvio".  In questo caso, l'attività viene semplificata ed è necessario eseguire i passaggi seguenti.

1. Completare i passaggi 1-4 dei prerequisiti per l'installazione.
1. Abilitare la comunicazione con l'archiviazione.
1. Scaricare l'esecuzione del programma di installazione per installare gli strumenti di snapshot.
1. Completa la configurazione degli strumenti di snapshot.
1. Creare un nuovo file di configurazione come segue. I dettagli del volume di avvio devono trovarsi nella OtherVolume strofa (voci utente in <span style="color:red">rosso</span>):
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. Controllare il file di configurazione, fare riferimento all'esempio seguente:

    Usare `cat` il comando per visualizzare il contenuto del file di configurazione:

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
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

1. Testare un backup del volume di avvio

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. Verificare che sia elencato. si noti l'aggiunta dell' `--snapshotfilter` opzione per limitare l'elenco di snapshot restituito.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    Output del comando:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. Configurare il backup automatico degli snapshot.

> [!NOTE]
> Non è necessario impostare la comunicazione con SAP HANA.

## <a name="restore-a-boot-snapshot"></a>Ripristinare uno snapshot di "avvio"

> [!IMPORTANT]
> Questa operazione è per l'istanza di grandi dimensioni di Azure.
> Il server verrà ripristinato fino al momento in cui lo snapshot è stato recuperato.

È possibile recuperare uno snapshot di "avvio" come indicato di seguito:

1. Il cliente dovrà arrestare il server.
1. Dopo l'arresto del server, il cliente dovrà aprire una richiesta di servizio che contiene l'ID e lo snapshot del computer da ripristinare.
    > I clienti possono aprire una richiesta di servizio dalla portale di Azure: <https://portal.azure.com>
1. Microsoft ripristinerà il LUN del sistema operativo usando l'ID e lo snapshot del computer specificati, quindi avvierà il server.
1. Il cliente dovrà quindi verificare che il server sia avviato e integro.

Non è necessario eseguire ulteriori passaggi dopo il ripristino.

## <a name="key-facts-to-know-about-snapshots"></a>Aspetti principali da conoscere sugli snapshot

Attributi chiave degli snapshot del volume di archiviazione:

- **Percorso degli snapshot**: gli snapshot si trovano in una directory virtuale ( `.snapshot` ) all'interno del volume.  Vedere gli esempi seguenti per **istanze large di Azure**:
  - Database `/hana/data/<SID>/mnt00001/.snapshot`
  - Condiviso `/hana/shared/<SID>/.snapshot`
  - Log `/hana/logbackups/<SID>/.snapshot`
  - Avvio: gli snapshot di avvio per HLI **non sono visibili** dal livello del sistema operativo, ma possono essere elencati usando `azacsnap -c details` .

  > [!NOTE]
  >  `.snapshot` è una cartella *virtuale* nascosta di sola lettura che fornisce l'accesso in sola lettura agli snapshot.

- **Snapshot massimo:** L'hardware può sostenere fino a 250 snapshot per ogni volume. Il comando snapshot manterrà un numero massimo di snapshot per il prefisso in base al set di conservazione nella riga di comando e eliminerà lo snapshot meno recente se va oltre il numero massimo da mantenere.
- **Nome snapshot:** Il nome dello snapshot include l'etichetta di prefisso fornita dal cliente.
- **Dimensioni dello snapshot:** Dipende dalle dimensioni o dalle modifiche a livello di database.
- **Percorso file di log:** I file di log generati dai comandi vengono restituiti in cartelle come definito nel file di configurazione JSON, che per impostazione predefinita è una sottocartella in cui viene eseguito il comando (ad esempio, `./logs` ).

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi](azacsnap-troubleshoot.md)
