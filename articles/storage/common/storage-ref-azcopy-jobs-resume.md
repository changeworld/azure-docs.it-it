---
title: azcopy jobs resume | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy jobs resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 497aa3a77397a8a587badc0911176cd092eb1f23
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503338"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Riprende il processo esistente con l'ID processo specificato.

## <a name="synopsis"></a>Riepilogo

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|--destination-sas string|Firma di accesso condiviso di destinazione della destinazione per l'ID processo specificato.|
|--exclude string|Filtro: escludere questi trasferimenti non riusciti quando si riprende il processo. I file devono essere separati da ';'.|
|-h, --help|Visualizzare il contenuto della Guida per il comando resume.|
|--include string|Filtro: includere questi trasferimenti non riusciti solo quando si riprende il processo. I file devono essere separati da ';'.|
|--source-sas string |firma di accesso condiviso di origine dell'origine per l'ID processo specificato.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps float|Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.|
|--output-type string|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|
|--trusted-microsoft-suffixes string   |Specifica suffissi di dominio aggiuntivi in Azure Active Directory i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati vengono aggiunti al valore predefinito. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.|

## <a name="see-also"></a>Vedi anche

- [azcopy jobs](storage-ref-azcopy-jobs.md)
