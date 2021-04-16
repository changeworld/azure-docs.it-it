---
title: azcopy load clfs | Microsoft Docs
titleSuffix: Azure Storage
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy load clfs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebf04531f29e18f9d120ca2efa17244c4282084c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503270"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Trasferisce i dati locali in un contenitore e li archivia nel formato Microsoft Avere Cloud FileSystem (CLFS).

## <a name="synopsis"></a>Riepilogo

Il comando load copia i dati in contenitori di archiviazione BLOB di Azure e li archivia nel formato ClFS (Avere Cloud FileSystem) di Microsoft. Il formato CLFS proprietario viene usato dai Cache HPC di Azure e Avere vFXT per Azure prodotti.

Per usare questo comando, installare l'estensione necessaria tramite: pip3 install clfsload~=1.0.23. Assicurarsi che CLFSLoad.py sia nel percorso. Per altre informazioni su questo passaggio, vedere [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

Questo comando è un'opzione semplice per spostare i dati esistenti nell'archiviazione cloud per l'uso con prodotti specifici di Microsoft High Performance Computing Cache. 

Poiché questi prodotti usano un formato di file system cloud proprietario per gestire i dati, tali dati non possono essere caricati tramite il comando di copia nativa. 

I dati devono invece essere caricati tramite il prodotto della cache stesso o tramite questo comando di caricamento, che usa il formato proprietario corretto.
Questo comando consente di trasferire i dati senza usare la cache. Ad esempio, per precompilare l'archiviazione o aggiungere file a un working set senza aumentare il carico della cache.

La destinazione è un contenitore Archiviazione di Azure vuoto. Al termine del trasferimento, il contenitore di destinazione può essere usato con un'Cache HPC di Azure o Avere vFXT per Azure cluster.

> [!NOTE] 
> Si tratta di una versione di anteprima del comando load. Segnalare eventuali problemi nel repository Github AzCopy.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="examples"></a>Esempio

Caricare un'intera directory in un contenitore con una firma di accesso condiviso in formato CLFS:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Opzioni

**--compression-type** string specifica il tipo di compressione da usare per i trasferimenti. I valori disponibili sono: `DISABLED` , `LZ4` . (impostazione `LZ4` predefinita)

**--help**    help per il `azcopy load clfs` comando.

**--log-level** string Consente di definire il livello di dettaglio del log per il file di log, i livelli disponibili: `DEBUG` , , , `INFO` `WARNING` `ERROR` . (impostazione `INFO` predefinita)

**--max-errors** uint32 Consente di specificare il numero massimo di errori di trasferimento da tollerare. Se si verificano errori sufficienti, arrestare immediatamente il processo.

**--new-session**   Avviare un nuovo processo anziché continuarne uno esistente le cui informazioni di rilevamento vengono mantenute in `--state-path` . (valore predefinito true)

**--preserve-hardlinks**    Mantenere le relazioni di collegamento rigido.

**--state-path string** Percorso obbligatorio di una directory locale per il rilevamento dello stato del processo. Il percorso deve puntare a una directory esistente per riprendere un processo. Deve essere vuoto per un nuovo processo.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps float|Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.|
|--output-type string|Formato dell'output del comando. Le scelte includono: text, json. Il valore predefinito è "text".|
|--trusted-microsoft-suffixes string   | Specifica suffissi di dominio aggiuntivi a Azure Active Directory possono essere inviati i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati di seguito vengono aggiunti all'impostazione predefinita. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
