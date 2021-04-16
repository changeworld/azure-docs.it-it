---
title: azcopy list | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ca436ee440ad7c1dd2caee3a65790f8b630fd949
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503287"
---
# <a name="azcopy-list"></a>azcopy list

Elenca le entità in una determinata risorsa.

## <a name="synopsis"></a>Riepilogo

Nella versione corrente sono supportati solo contenitori BLOB.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="examples"></a>Esempio

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|-h, --help|Visualizzare il contenuto della Guida per il comando list.|
|--machine-readable|Elenca le dimensioni dei file in byte.|
|--mega-units|Visualizza le unità in ordini di 1000, non 1024.|
|--running-tally|Conta il numero totale di file e le relative dimensioni.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps float|Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.|
|--output-type string|Formato dell'output del comando. Le scelte includono: text, json. Il valore predefinito è "text".|
|--trusted-microsoft-suffixes string   |Specifica suffissi di dominio aggiuntivi a Azure Active Directory possono essere inviati i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati di seguito vengono aggiunti all'impostazione predefinita. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure qui. Separare più voci con punti e virgola.|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
