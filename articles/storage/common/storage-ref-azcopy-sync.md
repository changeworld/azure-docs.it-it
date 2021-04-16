---
title: azcopy sync | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 83d10a7a6e9eb14379d32cc88800a2c443feac60
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503049"
---
# <a name="azcopy-sync"></a>azcopy sync

Replica il percorso di origine nel percorso di destinazione. Questo articolo fornisce informazioni di riferimento dettagliate per il comando azcopy sync. Per altre informazioni sulla sincronizzazione dei BLOB tra percorsi di origine e destinazione, vedere Sincronizzare con l'archiviazione BLOB di [Azure usando AzCopy v10.](storage-use-azcopy-blobs-synchronize.md) Per File di Azure, vedere [Sincronizzare i file](storage-use-azcopy-files.md#synchronize-files).

## <a name="synopsis"></a>Riepilogo

Per il confronto vengono utilizzate le ore dell'ultima modifica. Il file viene ignorato se l'ora dell'ultima modifica nella destinazione è più recente.

Le coppie supportate sono:

- BLOB <-> azure locale (è possibile usare l'autenticazione di firma di accesso condiviso o OAuth)
- BLOB di Azure <-> Blob di Azure (l'origine deve includere una firma di accesso condiviso o è accessibile pubblicamente. È possibile usare l'autenticazione saS o OAuth per la destinazione)
- File di Azure <-> file di Azure (l'origine deve includere una firma di accesso condiviso o è accessibile pubblicamente; L'autenticazione di firma di accesso condiviso deve essere usata per la destinazione)

Il comando sync differisce dal comando copy in diversi modi:

1. Per impostazione predefinita, il flag ricorsivo è true e sync copia tutte le sottodirectory. La sincronizzazione copia i file di primo livello all'interno di una directory solo se il flag ricorsivo è false.
2. Quando si esegue la sincronizzazione tra directory virtuali, aggiungere una barra finale al percorso (vedere gli esempi) se è presente un BLOB con lo stesso nome di una delle directory virtuali.
3. Se il flag è impostato su true o su prompt, la sincronizzazione eliminerà i file e i BLOB nella destinazione che `deleteDestination` non sono presenti nell'origine.

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Esercitazione: Eseguire la migrazione dei dati locali in una risorsa di archiviazione nel cloud con AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

### <a name="advanced"></a>Avanzato

Se non si specifica un'estensione di file, AzCopy rileva automaticamente il tipo di contenuto dei file durante il caricamento dal disco locale, in base all'estensione o al contenuto (se non viene specificata alcuna estensione).

La tabella di ricerca predefinita è di piccole dimensioni, ma in Unix viene aumentata dai file mime.types del sistema locale, se disponibili con uno o più di questi nomi:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows i tipi MIME vengono estratti dal Registro di sistema.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Esempio

Sincronizzare un singolo file:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Come sopra, ma anche calcolare un hash MD5 del contenuto del file e quindi salvare tale hash MD5 come proprietà Content-MD5 del BLOB. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Sincronizzare un'intera directory, incluse le relative sottodirectory (si noti che la ricorsiva è impostata su :

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

oppure

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Sincronizzare solo i file all'interno di una directory, ma non le sottodirectory o i file all'interno di sottodirectory:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Sincronizzare un subset di file in una directory (ad esempio, solo file jpg e pdf o se il nome del file è `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Sincronizzare un'intera directory ma escludere determinati file dall'ambito (ad esempio, ogni file che inizia con foo o termina con bar):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Sincronizzare un singolo BLOB:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Sincronizzare una directory virtuale:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Sincronizzare una directory virtuale con lo stesso nome di un BLOB (aggiungere una barra finale al percorso per evitare ambiguità):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Sincronizzare una directory di File di Azure (stessa sintassi del BLOB):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Se i flag include/exclude vengono usati insieme, verranno cercati solo i file che corrispondono ai modelli di inclusione, ma quelli corrispondenti ai criteri di esclusione verranno sempre ignorati.

## <a name="options"></a>Opzioni

**--block-size-mb** float Usare questa dimensione del blocco (specificata in MiB) durante il caricamento in Archiviazione di Azure o il download da Archiviazione di Azure. Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali (ad esempio: `0.25` ).

**--check-md5** string Specifica come convalidare rigorosamente gli hash MD5 durante il download. Questa opzione è disponibile solo durante il download. I valori disponibili includono: `NoCheck` , `LogOnly` , , `FailIfDifferent` `FailIfDifferentOrMissing` . (impostazione `FailIfDifferent` predefinita). (impostazione `FailIfDifferent` predefinita)

**--delete-destination** string Definisce se eliminare dalla destinazione file aggiuntivi che non sono presenti nell'origine. Può essere impostato su `true` `false` , o `prompt` . Se impostato su , all'utente verrà posta una domanda prima di pianificare file `prompt` e BLOB per l'eliminazione. (impostazione `false` predefinita). (impostazione `false` predefinita)

**--exclude-attributes** string (solo Windows) Esclude i file i cui attributi corrispondono all'elenco di attributi. ad esempio `A;S;R`

**--exclude-path string** Consente di escludere questi percorsi quando si confronta l'origine con la destinazione. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo(ad esempio: `myFolder;myFolder/subDirName/file.pdf` ).

**--exclude-pattern string** Consente di escludere i file in cui il nome corrisponde all'elenco di criteri. ad esempio `*.jpg;*.pdf;exactName`

**--help**    help per la sincronizzazione.

**--include-attributes** string (solo Windows) Include solo i file i cui attributi corrispondono all'elenco di attributi. ad esempio `A;S;R`

**--include-pattern** string Consente di includere solo i file in cui il nome corrisponde all'elenco di criteri. ad esempio `*.jpg;*.pdf;exactName`

**--log-level** string Consente di definire il livello di dettaglio del log per il file di log, i livelli disponibili: (tutte le richieste e risposte), (risposte lente), (solo richieste non riuscite) e `INFO` `WARNING` `ERROR` `NONE` (nessun log di output). (impostazione `INFO` predefinita). 

**--preserve-smb-info**   False per impostazione predefinita. Mantiene le informazioni sulle proprietà SMB (ora dell'ultima scrittura, ora di creazione, bit di attributo) tra le risorse in grado di riconoscere SMB (Windows e File di Azure). Questo flag si applica sia ai file che alle cartelle, a meno che non venga specificato un filtro solo file (ad esempio, include-pattern). Le informazioni trasferite per le cartelle sono le stesse di quelle per i file, ad eccezione dell'ora dell'ultima scrittura che non viene mantenuta per le cartelle.

**--preserve-smb-permissions**   False per impostazione predefinita. Mantiene gli ACL SMB tra risorse in grado di riconoscere (Windows e File di Azure). Questo flag si applica sia ai file che alle cartelle, a meno che non venga specificato un filtro solo file (ad esempio, `include-pattern` ).

**--put-md5**     Creare un hash MD5 di ogni file e salvarlo come proprietà Content-MD5 del BLOB o del file di destinazione. Per impostazione predefinita, l'hash NON viene creato. Disponibile solo durante il caricamento.

**--recursive** `True` Per impostazione predefinita, esaminare le sottodirectory in modo ricorsivo durante la sincronizzazione tra directory.     (impostazione `True` predefinita). 

**--s2s-preserve-access-tier**  Mantenere il livello di accesso durante la copia da servizio a servizio. Per assicurarsi che [l'account di](../blobs/storage-blob-storage-tiers.md) archiviazione di destinazione supporti l'impostazione del livello di accesso, vedere Archiviazione BLOB di Azure: livelli di accesso ad accesso rapido, ad accesso rovente e archivio. Nei casi in cui l'impostazione del livello di accesso non è supportata, usare s2sPreserveAccessTier=false per ignorare la copia del livello di accesso. (impostazione `true` predefinita). 

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps uint32|Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.|
|--output-type string|Formato dell'output del comando. Le scelte includono: text, json. Il valore predefinito è "text".|
|--trusted-microsoft-suffixes string   |Specifica suffissi di dominio aggiuntivi a Azure Active Directory possono essere inviati i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati di seguito vengono aggiunti all'impostazione predefinita. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
