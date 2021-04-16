---
title: azcopy remove | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd221215d6be3c14ce1200e8bd374a97cb7608a0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503015"
---
# <a name="azcopy-remove"></a>azcopy remove

Eliminare BLOB o file da un account di archiviazione di Azure.

## <a name="synopsis"></a>Riepilogo

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="examples"></a>Esempio

Rimuovere un singolo BLOB usando un token di firma di accesso condiviso:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Rimuovere un'intera directory virtuale usando un token di firma di accesso condiviso:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Rimuovere solo i BLOB all'interno di una directory virtuale, ma non rimuovere le sottodirectory o i BLOB all'interno di tali sottodirectory:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Rimuovere un subset di BLOB in una directory virtuale(ad esempio, rimuovere solo i file jpg e pdf o se il nome del BLOB è `exactName` ):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Rimuovere un'intera directory virtuale ma escludere determinati BLOB dall'ambito (ad esempio, ogni BLOB che inizia con foo o termina con bar):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Rimuovere BLOB e directory virtuali specifici inserendo i relativi percorsi (non con codifica URL) in un file:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
Rimuovere un singolo file da un account di archiviazione BLOB con uno spazio dei nomi gerarchico (inclusione/esclusione non supportata):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Rimuovere una singola directory da un account di archiviazione BLOB con uno spazio dei nomi gerarchico (inclusione/esclusione non supportata):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opzioni

**--delete-snapshots** string Per impostazione predefinita, l'operazione di eliminazione ha esito negativo se un BLOB include snapshot. Specificare per rimuovere il BLOB radice e tutti i relativi snapshot. In alternativa, specificare per rimuovere solo gli snapshot, ma `include` mantenere il BLOB `only` radice.

**--exclude-path string** Consente di escludere questi percorsi durante la rimozione. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo. ad esempio `myFolder;myFolder/subDirName/file.pdf`

**--exclude-pattern string** Consente di escludere i file in cui il nome corrisponde all'elenco di criteri. Ad esempio: `*.jpg` ; `*.pdf` ;`exactName`

**--force-if-read-only**   Quando si elimina un File di Azure file o una cartella, forzare il funzionamento dell'eliminazione anche se per l'oggetto esistente è impostato l'attributo di sola lettura.

**--help**   help per la rimozione.

**--include-path** string Includere solo questi percorsi durante la rimozione. Questa opzione non supporta i caratteri jolly (*). Controlla il prefisso del percorso relativo. ad esempio `myFolder;myFolder/subDirName/file.pdf`

**--include-pattern** string Consente di includere solo i file in cui il nome corrisponde all'elenco di criteri. Ad esempio: *`.jpg` ;* `.pdf` ;`exactName`

**--list-of-files** string Definisce il percorso di un file, che contiene l'elenco di file e directory da eliminare. I percorsi relativi devono essere delimitati da interruzioni di riga e i percorsi NON devono essere codificati con URL. 

**--list-of-versions** string Specifica un file in cui ogni ID versione è elencato in una riga separata. Assicurarsi che l'origine punti a un singolo BLOB e che tutti gli ID versione specificati nel file usando questo flag appartengano solo al BLOB di origine. Gli ID versione specificati del BLOB specificato verranno eliminati dal Archiviazione di Azure. 

**--log-level** string Consente di definire il livello di dettaglio del log per il file di log. I livelli disponibili includono: `INFO` (tutte le richieste/risposte), `WARNING` (risposte lente), (solo richieste non `ERROR` riuscite) e `NONE` (nessun log di output). (impostazione `INFO` predefinita) (impostazione `INFO` predefinita)

**--recursive**    Esaminare le sottodirectory in modo ricorsivo durante la sincronizzazione tra le directory.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps float|Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è massima.|
|--output-type string|Formato dell'output del comando. Le scelte includono: text, json. Il valore predefinito è "text".|
|--trusted-microsoft-suffixes string   |Specifica suffissi di dominio aggiuntivi a Azure Active Directory possono essere inviati i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati di seguito vengono aggiunti all'impostazione predefinita. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure qui. Separare più voci con punti e virgola.|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
