---
title: azcopy jobs show | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: a14546d8bfc50531902b5150d38ee5ce8b8b5769
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503355"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Visualizza informazioni dettagliate per l'ID processo specificato.

## <a name="synopsis"></a>Riepilogo

Se viene fornito solo l'ID processo senza un flag, viene restituito il riepilogo dello stato del processo.

I conteggi dei byte e la percentuale di completamento visualizzati quando si esegue questo comando riflettono solo i file completati nel processo. Non riflettono i file parzialmente completati.

Se il flag è impostato, verrà visualizzato l'elenco dei trasferimenti nel processo con `with-status` il valore specificato.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|-h, --help|Mostra il contenuto della Guida per il comando show.|
|--with-status string|Elencare solo i trasferimenti del processo con questo stato, i valori disponibili: Avviato, Riuscito, Non riuscito|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps float|Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.|
|--output-type string|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|
|--trusted-microsoft-suffixes string   |Specifica suffissi di dominio aggiuntivi in Azure Active Directory i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati vengono aggiunti all'impostazione predefinita. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.|

## <a name="see-also"></a>Vedi anche

- [azcopy jobs](storage-ref-azcopy-jobs.md)
