---
title: azcopy | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b3b4f7737320cc0359192f947271a0f4beb3c478
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502998"
---
# <a name="azcopy"></a>azcopy

AzCopy è uno strumento da riga di comando che sposta i dati all'interno e all'Archiviazione di Azure. Vedere [l'articolo Introduzione ad AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire credenziali di autorizzazione al servizio di archiviazione.

## <a name="synopsis"></a>Riepilogo

Il formato generale dei comandi è: `azcopy [command] [arguments] --[flag-name]=[flag-value]` .

Per segnalare problemi o per altre informazioni sullo strumento, vedere [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy) .

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Esercitazione: Eseguire la migrazione dei dati locali in una risorsa di archiviazione nel cloud con AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="options"></a>Opzioni

**--cap-mbps** (float) consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.

**--help** Guida per azcopy
      
**--output-type**  (stringa) Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è `text`. (impostazione `text` predefinita)

**--trusted-microsoft-suffixes** (string) Specifica suffissi di dominio aggiuntivi in Azure Active Directory i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati vengono aggiunti al valore predefinito. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.

## <a name="see-also"></a>Vedi anche

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [azcopy bench](storage-ref-azcopy-bench.md)
- [azcopy copy](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy jobs](storage-ref-azcopy-jobs.md)
- [azcopy jobs clean](storage-ref-azcopy-jobs-clean.md)
- [azcopy jobs list](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs remove](storage-ref-azcopy-jobs-remove.md)
- [azcopy jobs resume](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobs show](storage-ref-azcopy-jobs-show.md)
- [azcopy list](storage-ref-azcopy-list.md)
- [azcopy login](storage-ref-azcopy-login.md)
- [azcopy logout](storage-ref-azcopy-logout.md)
- [azcopy make](storage-ref-azcopy-make.md)
- [azcopy remove](storage-ref-azcopy-remove.md)
- [azcopy sync](storage-ref-azcopy-sync.md)
