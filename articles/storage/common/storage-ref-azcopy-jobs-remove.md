---
title: azcopy jobs remove | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy jobs remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c6a4745c4059c81384448deba37495030c4bf3a3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503372"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Rimuovere tutti i file associati all'ID processo specificato.

> [!NOTE] 
> È possibile personalizzare il percorso in cui vengono salvati i file di log e di piano. Per altre informazioni, [vedere il comando azcopy env.](storage-ref-azcopy-env.md)

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="examples"></a>Esempio

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opzioni

**--help**                Guida per la rimozione.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--cap-mbps float**      Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.

**--output-type** string Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è `text`. (impostazione `text` predefinita)

**--trusted-microsoft-suffixes** string Specifica suffissi di dominio aggiuntivi in Azure Active Directory i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati vengono aggiunti all'impostazione predefinita. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.

## <a name="see-also"></a>Vedi anche

- [azcopy jobs](storage-ref-azcopy-jobs.md)
