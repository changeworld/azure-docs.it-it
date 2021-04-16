---
title: azcopy jobs | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 074d60080887ea822a6e189fe82554ad3b998b58
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503304"
---
# <a name="azcopy-jobs"></a>azcopy jobs

Sottocomandi correlati alla gestione dei processi.

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="examples"></a>Esempio

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|-h, --help|Visualizzare il contenuto della Guida per il comando jobs.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps float|Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.|
|--output-type string|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|
|--trusted-microsoft-suffixes string   |Specifica suffissi di dominio aggiuntivi in Azure Active Directory i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati vengono aggiunti al valore predefinito. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
- [azcopy jobs list](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs resume](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobs show](storage-ref-azcopy-jobs-show.md)
