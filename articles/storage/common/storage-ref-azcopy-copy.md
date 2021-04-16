---
title: azcopy copy| Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7c1e265f473c1c6fb70fd97416722e7b863c429b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503559"
---
# <a name="azcopy-copy"></a>azcopy copy

Copia i dati di origine in un percorso di destinazione.

## <a name="synopsis"></a>Riepilogo

Copia i dati di origine in un percorso di destinazione. Le indicazioni supportate sono:

  - BLOB <-> azure locale (firma di accesso condiviso o autenticazione OAuth)
  - local <-> File di Azure (autenticazione di firma di accesso condiviso di condivisione/directory)
  - local <-> Azure Data Lake Storage Gen 2 (firma di accesso condiviso, OAuth o autenticazione con chiave condivisa)
  - BLOB di Azure (saS o public) -> BLOB di Azure (firma di accesso condiviso o autenticazione OAuth)
  - BLOB di Azure (saS o public) -> File di Azure (SAS)
  - File di Azure (SAS) -> File di Azure (SAS)
  - File di Azure (SAS) -> BLOB di Azure (firma di accesso condiviso o autenticazione OAuth)
  - Amazon Web Services (AWS) S3 (chiave di accesso) -> BLOB in blocchi di Azure (firma di accesso condiviso o autenticazione OAuth)
  - Google Cloud Storage (chiave dell'account del servizio) -> BLOB in blocchi di Azure (firma di accesso condiviso o autenticazione OAuth) [anteprima]

Per altre informazioni, vedere la sezione relativa agli esempi di questo articolo.

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Esercitazione: Eseguire la migrazione dei dati locali in una risorsa di archiviazione nel cloud con AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="advanced"></a>Avanzato

AzCopy rileva automaticamente il tipo di contenuto dei file quando vengono caricati dal disco locale. AzCopy rileva il tipo di contenuto in base all'estensione di file o al contenuto (se non viene specificata alcuna estensione).

La tabella di ricerca predefinita è di piccole dimensioni, ma in Unix viene aumentata dai file del sistema locale se sono disponibili con uno o più di `mime.types` questi nomi:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows i tipi MIME vengono estratti dal Registro di sistema. Questa funzionalità può essere disattivata con l'aiuto di un flag. Per altre informazioni, vedere la sezione flag di questo articolo.

Se si imposta una variabile di ambiente tramite la riga di comando, tale variabile sarà leggibile nella cronologia della riga di comando. Prendere in considerazione la possibilità di cancellare le variabili che contengono credenziali dalla cronologia della riga di comando. Per evitare che le variabili appaiano nella cronologia, è possibile usare uno script per richiedere all'utente le credenziali e impostare la variabile di ambiente.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Esempio

Caricare un singolo file usando l'autenticazione OAuth. Se non è ancora stato eseguito l'accesso ad AzCopy, eseguire il `azcopy login` comando prima di eseguire il comando seguente.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Come sopra, ma questa volta, calcolare anche l'hash MD5 del contenuto del file e salvarlo come proprietà Content-MD5 del BLOB:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Caricare un singolo file usando un token di firma di accesso condiviso:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Caricare un singolo file usando un token di firma di accesso condiviso e un piping (solo BLOB in blocchi):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Caricare un'intera directory usando un token di firma di accesso condiviso:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

oppure

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Caricare un set di file usando un token di firma di accesso condiviso e caratteri jolly (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Caricare file e directory usando un token di firma di accesso condiviso e caratteri jolly (*):

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Caricare file e directory nell'account Archiviazione di Azure e impostare i tag con codifica stringa di query nel BLOB. 

- Per impostare i tag {key = "bla bla", val = "foo"} e {key = "bla bla 2", val = "bar"}, usare la sintassi seguente: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- Le chiavi e i valori sono codificati in URL e le coppie chiave-valore sono separate da una e commerciale ('&')

- Durante l'impostazione dei tag nei BLOB, sono disponibili autorizzazioni aggiuntive ('t' per i tag) nella firma di accesso condiviso senza la quale il servizio restituirà l'errore di autorizzazione.

Scaricare un singolo file usando l'autenticazione OAuth. Se non è ancora stato eseguito l'accesso ad AzCopy, eseguire il `azcopy login` comando prima di eseguire il comando seguente.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Scaricare un singolo file usando un token di firma di accesso condiviso:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Scaricare un singolo file usando un token di firma di accesso condiviso e quindi piping dell'output in un file (solo BLOB in blocchi):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Scaricare un'intera directory usando un token di firma di accesso condiviso:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Nota sull'uso di un carattere jolly (*) negli URL:

Esistono solo due modi supportati per usare un carattere jolly in un URL. 

- È possibile usarne una subito dopo la barra (/) finale di un URL. Questo uso del carattere jolly copia tutti i file di una directory direttamente nella destinazione senza posizionarli in una sottodirectory. 

- È anche possibile usare un carattere jolly nel nome di un contenitore, purché l'URL faccia riferimento solo a un contenitore e non a un BLOB. È possibile usare questo approccio per ottenere file da un subset di contenitori. 

Scaricare il contenuto di una directory senza copiare la directory che lo contiene.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Scaricare un intero account di archiviazione.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Scaricare un subset di contenitori all'interno di un account di archiviazione usando un simbolo jolly (*) nel nome del contenitore.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Copiare un singolo BLOB in un altro BLOB usando un token di firma di accesso condiviso.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiare un singolo BLOB in un altro BLOB usando un token di firma di accesso condiviso e un token di autenticazione. È necessario usare un token di firma di accesso condiviso alla fine dell'URL dell'account di origine, ma l'account di destinazione non ne ha bisogno se si accede ad AzCopy usando il `azcopy login` comando . 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Copiare una directory virtuale BLOB in un'altra usando un token di firma di accesso condiviso:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Copiare tutti i contenitori BLOB, le directory e i BLOB dall'account di archiviazione a un altro usando un token di firma di accesso condiviso:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copiare un singolo oggetto in Archiviazione BLOB da Amazon Web Services (AWS) S3 usando una chiave di accesso e un token di firma di accesso condiviso. Impostare prima di tutto la variabile di ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` per l'origine AWS S3.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiare un'intera directory in Archiviazione BLOB da AWS S3 usando una chiave di accesso e un token di firma di accesso condiviso. Impostare prima di tutto la variabile di `AWS_ACCESS_KEY_ID` ambiente e `AWS_SECRET_ACCESS_KEY` per l'origine AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Fare riferimento https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html a per comprendere meglio il segnaposto [cartella].

Copiare tutti i bucket in Archiviazione BLOB da Amazon Web Services (AWS) usando una chiave di accesso e un token di firma di accesso condiviso. Impostare prima di tutto la variabile di ambiente `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY` per l'origine AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copiare tutti i bucket in Archiviazione BLOB da un'area Amazon Web Services (AWS) usando una chiave di accesso e un token di firma di accesso condiviso. Impostare prima di tutto la variabile di `AWS_ACCESS_KEY_ID` ambiente e `AWS_SECRET_ACCESS_KEY` per l'origine AWS S3.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copiare un subset di bucket usando un simbolo jolly (*) nel nome del bucket. Come negli esempi precedenti, sono necessari una chiave di accesso e un token di firma di accesso condiviso. Assicurarsi di impostare la variabile di ambiente e `AWS_ACCESS_KEY_ID` per `AWS_SECRET_ACCESS_KEY` l'origine AWS S3.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Trasferire file e directory in Archiviazione di Azure account e impostare i tag con codifica stringa di query specificata nel BLOB. 

- Per impostare i tag {key = "bla bla", val = "foo"} e {key = "bla bla 2", val = "bar"}, usare la sintassi seguente: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- Le chiavi e i valori sono codificati in URL e le coppie chiave-valore sono separate da una e commerciale ('&')
    
- Durante l'impostazione dei tag nei BLOB, sono disponibili autorizzazioni aggiuntive ('t' per i tag) nella firma di accesso condiviso senza la quale il servizio restituirà l'errore di autorizzazione.

Copiare un singolo oggetto in Archiviazione BLOB da Google Cloud Storage usando una chiave dell'account del servizio e un token di firma di accesso condiviso. Impostare prima di tutto la variabile di ambiente GOOGLE_APPLICATION_CREDENTIALS'origine di Google Cloud Storage.
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copiare un'intera directory in Archiviazione BLOB da Google Cloud Storage usando una chiave dell'account del servizio e un token di firma di accesso condiviso. Impostare prima di tutto la variabile di ambiente GOOGLE_APPLICATION_CREDENTIALS'origine di Google Cloud Storage.
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Copiare un intero bucket in Archiviazione BLOB da Google Cloud Storage usando una chiave dell'account del servizio e un token di firma di accesso condiviso. Impostare prima di tutto la variabile di ambiente GOOGLE_APPLICATION_CREDENTIALS'origine di Google Cloud Storage.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Copiare tutti i bucket in Archiviazione BLOB da Google Cloud Storage usando una chiave dell'account del servizio e un token di firma di accesso condiviso. Impostare innanzitutto le variabili di ambiente GOOGLE_APPLICATION_CREDENTIALS e GOOGLE_CLOUD_PROJECT=<project-id> per l'origine GCS

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Copiare un subset di bucket usando un simbolo jolly (*) nel nome del bucket da Google Cloud Storage usando una chiave dell'account del servizio e un token di firma di accesso condiviso per la destinazione. Impostare innanzitutto le variabili di ambiente GOOGLE_APPLICATION_CREDENTIALS e GOOGLE_CLOUD_PROJECT=<project-id>'origine di Google Cloud Storage.
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Opzioni

**--backup** Attiva SeBackupPrivilege di Windows per i caricamenti o SeRestorePrivilege per i download, per consentire ad AzCopy di visualizzare e leggere tutti i file, indipendentemente dalle autorizzazioni file system e di ripristinare tutte le autorizzazioni. Richiede che l'account che esegue AzCopy abbia già queste autorizzazioni, ad esempio ha diritti di amministratore o è membro del `Backup Operators` gruppo. Questo flag attiva i privilegi già presenti nell'account.

**--blob-tags string** Imposta i tag nei BLOB per classificare i dati nell'account di archiviazione.

**--blob-type** string Definisce il tipo di BLOB nella destinazione. Viene usato per il caricamento di BLOB e per la copia tra account (impostazione predefinita `Detect` ). I valori validi includono `Detect`, `BlockBlob`, `PageBlob`e `AppendBlob`. Quando si esegue la copia tra account, il valore fa in modo che AzCopy usi il tipo di BLOB di origine `Detect` per determinare il tipo del BLOB di destinazione. Quando si carica un file, determina se il file è un disco rigido virtuale `Detect` o un file VHDX in base all'estensione di file. Se il file è un file VHD o VHDX, AzCopy considera il file come BLOB di pagine. (valore predefinito "Detect")

**--block-blob-tier string** Carica BLOB in Archiviazione di Azure usando questo livello BLOB. (impostazione predefinita "None")

**--block-size-mb** float Usare questa dimensione del blocco (specificata in MiB) durante il caricamento in Archiviazione di Azure e il download da Archiviazione di Azure. Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali (ad esempio: 0,25).

**--cache-control string** Imposta l'intestazione cache-control. Restituito al download.

**--check-length** Controllare la lunghezza di un file nella destinazione dopo il trasferimento. In caso di mancata corrispondenza tra origine e destinazione, il trasferimento viene contrassegnato come non riuscito. (il valore predefinito è `true` )

**--check-md5** string specifica in che modo convalidare rigorosamente gli hash MD5 durante il download. Disponibile solo durante il download. Opzioni disponibili: `NoCheck` , `LogOnly` , , `FailIfDifferent` `FailIfDifferentOrMissing` . (impostazione `FailIfDifferent` predefinita) (impostazione predefinita" FailIfDifferent")

**--content-disposition** string Imposta l'intestazione content-disposition. Restituito al download.

**--content-encoding** string Imposta l'intestazione content-encoding. Restituito al download.

**--content-language** string Imposta l'intestazione content-language. Restituito al download.

**--content-type** string specifica il tipo di contenuto del file. Implica no-guess-mime-type. Restituito al download.

**--decompress** Decomprimere automaticamente i file durante il download, se la codifica del contenuto indica che sono compressi. I valori di codifica del contenuto supportati sono `gzip` e `deflate` . Le estensioni di `.gz` / `.gzip` o non `.zz` sono necessarie, ma verranno rimosse se presenti.

**--exclude-attributes** string (solo Windows) Esclude i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S; R

**--exclude-blob-type** string Specifica facoltativamente il tipo di BLOB ( ) da escludere durante la copia dei BLOB dal `BlockBlob` /  `PageBlob` /  `AppendBlob` contenitore o dall'account. L'uso di questo flag non è applicabile per la copia di dati dal servizio non Azure al servizio. Più BLOB devono essere separati da `;` . 

**--exclude-path string** Consente di escludere questi percorsi durante la copia. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo(ad esempio: `myFolder;myFolder/subDirName/file.pdf` ). Se usati in combinazione con l'attraversamento dell'account, i percorsi non includono il nome del contenitore.

**--exclude-pattern string** Consente di escludere questi file durante la copia. Questa opzione supporta i caratteri jolly (*).

**--follow-symlinks**  Seguire i collegamenti simbolici durante il caricamento da file system.

**--force-if-read-only** Quando si sovrascrive un file esistente in Windows o File di Azure, forzare il funzionamento della sovrascrittura anche se per il file esistente è impostato l'attributo di sola lettura.

**--from-to** stringa Specifica facoltativamente la combinazione di destinazione di origine. Ad esempio: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--help**  help per la copia.

**--include-after** string Include solo i file modificati in base o dopo la data/ora specificata. Il valore deve essere in formato ISO8601. Se non viene specificato alcun fuso orario, si presuppone che il valore sia nel fuso orario locale del computer che esegue AzCopy. ad esempio per `2020-08-19T15:04:00Z` un'ora UTC o `2020-08-19` per la mezzanotte (00:00) nel fuso orario locale. Come in AzCopy 10.5, questo flag si applica solo ai file e non alle cartelle, pertanto le proprietà della cartella non verranno copiate quando si usa questo flag con `--preserve-smb-info` o `--preserve-smb-permissions` .

 **--include-before** string Include solo i file modificati prima o alla data/ora specificata. Il valore deve essere in formato ISO8601. Se non viene specificato alcun fuso orario, si presuppone che il valore sia nel fuso orario locale del computer che esegue AzCopy. ad esempio `2020-08-19T15:04:00Z` per un'ora UTC o `2020-08-19` per la mezzanotte (00:00) nel fuso orario locale. A causa di AzCopy 10.7, questo flag si applica solo ai file e non alle cartelle, pertanto le proprietà della cartella non verranno copiate quando si usa questo flag `--preserve-smb-info` con o `--preserve-smb-permissions` .

**--include-attributes** string (solo Windows) Include i file i cui attributi corrispondono all'elenco di attributi. Ad esempio: A; S; R

**--include-path string** Includere solo questi percorsi durante la copia. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo (ad esempio: `myFolder;myFolder/subDirName/file.pdf` ).

**--include-pattern** string Includere solo questi file durante la copia. Questa opzione supporta i caratteri jolly (*). Separare i file usando un `;` oggetto .

**--list-of-versions** string Specifica un file in cui ogni ID versione è elencato in una riga separata. Assicurarsi che l'origine punti a un singolo BLOB e che tutti gli ID versione specificati nel file usando questo flag appartengano solo al BLOB di origine. AzCopy scariderà le versioni specificate nella cartella di destinazione specificata. Per altre informazioni, vedere [Scaricare le versioni precedenti di un BLOB.](./storage-use-azcopy-v10.md#transfer-data)

**--log-level** string Consente di definire il livello di dettaglio del log per il file di log, i livelli disponibili: INFO(tutte le richieste/risposte), WARNING(risposte lente), ERROR (solo richieste non riuscite) e NONE (nessun log di output). (impostazione `INFO` predefinita). 

**--metadata** string Upload to Archiviazione di Azure con queste coppie chiave-valore come metadati.

**--no-guess-mime-type**  Impedisce ad AzCopy di rilevare il tipo di contenuto in base all'estensione o al contenuto del file.

**--overwrite** string Sovrascrive i file e i BLOB in conflitto nella destinazione se questo flag è impostato su true. (impostazione `true` predefinita) I valori possibili `true` includono , , e `false` `prompt` `ifSourceNewer` . Per le destinazioni che supportano le cartelle, le proprietà a livello di cartella in conflitto verranno sovrascritte. Questo flag è o se viene fornita una risposta positiva `true` al prompt. (impostazione predefinita "true")

**--page-blob-tier** string Carica blob di pagine Archiviazione di Azure usando questo livello BLOB. (impostazione `None` predefinita). (impostazione predefinita "None")

**--preserve-last-modified-time**  Disponibile solo quando la destinazione è file system.

**--preserve-owner**    Ha effetto solo sui download e solo quando `--preserve-smb-permissions` viene usato. Se true (impostazione predefinita), il proprietario e il gruppo del file vengono mantenuti nei download. Se impostato su false, conserverà comunque gli ACL, ma Proprietario e Gruppo saranno basati sull'utente che `--preserve-smb-permissions` esegue AzCopy (impostazione predefinita true)

**--preserve-smb-info**   False per impostazione predefinita. Mantiene le informazioni sulle proprietà SMB (ora dell'ultima scrittura, ora di creazione, bit di attributo) tra le risorse in grado di riconoscere SMB (Windows e File di Azure). Verranno trasferiti solo i bit di attributo File di Azure' tutti gli altri verranno ignorati. Questo flag si applica sia ai file che alle cartelle, a meno che non venga specificato un filtro solo file (ad esempio, include-pattern). Le informazioni trasferite per le cartelle sono le stesse di quelle per i file, ad eccezione dell'ora dell'ultima scrittura che non viene mai mantenuta per le cartelle.

**--preserve-smb-permissions**   False per impostazione predefinita. Mantiene gli ACL SMB tra le risorse in grado di riconoscere (Windows e File di Azure). Per i download, sarà necessario anche il flag per ripristinare le autorizzazioni in cui il nuovo proprietario non sarà `--backup` l'utente che esegue AzCopy. Questo flag si applica sia ai file che alle cartelle, a meno che non venga specificato un filtro solo file (ad esempio, `include-pattern` ).

**--put-md5**    Creare un hash MD5 di ogni file e salvarlo come proprietà Content-MD5 del BLOB o del file di destinazione. Per impostazione predefinita, l'hash NON viene creato. Disponibile solo durante il caricamento.

**--recursive**    Esaminare le sottodirectory in modo ricorsivo durante il caricamento dall'file system.

**--s2s-detect-source-changed**   Rilevare se il file o il BLOB di origine cambia durante la lettura. Questo parametro si applica solo alle copie da servizio a servizio, perché il controllo corrispondente è abilitato in modo permanente per i caricamenti e i download.

**--s2s-handle-invalid-metadata** string specifica come vengono gestite le chiavi di metadati non valide. Opzioni disponibili: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (impostazione `ExcludeIfInvalid` predefinita). (valore predefinito "ExcludeIfInvalid")

**--s2s-preserve-access-tier**   Mantenere il livello di accesso durante la copia da servizio a servizio. Per assicurarsi che [l'account](../blobs/storage-blob-storage-tiers.md) di archiviazione di destinazione supporti l'impostazione del livello di accesso, vedere Archiviazione BLOB di Azure: livelli di accesso ad accesso ad accesso rapido, ad accesso ad accesso archivio e ad accesso archivio. Nei casi in cui l'impostazione del livello di accesso non è supportata, usare s2sPreserveAccessTier=false per ignorare la copia del livello di accesso. (impostazione `true` predefinita).  (valore predefinito "true")

**--s2s-preserve-properties**   Mantenere le proprietà complete durante la copia da servizio a servizio. Per AWS S3 e l'origine file di Azure non a file singolo, l'operazione di elenco non restituisce proprietà complete di oggetti e file. Per mantenere le proprietà complete, AzCopy deve inviare una richiesta aggiuntiva per ogni oggetto o file. (valore predefinito true)

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--cap-mbps float**   Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.

**--output-type** string Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è `text`. (testo predefinito)

**--trusted-microsoft-suffixes** string Specifica suffissi di dominio aggiuntivi in Azure Active Directory i token di accesso.  Il valore predefinito è `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Tutti gli elementi elencati vengono aggiunti al valore predefinito. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
