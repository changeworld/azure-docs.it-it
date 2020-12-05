---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 372342611265640a2a64100f003880a430d61ca0
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620945"
---
Durante l'anteprima, NFS presenta le limitazioni seguenti:

- NFS 4,1 supporta attualmente solo la maggior parte delle funzionalità della [specifica del protocollo](https://tools.ietf.org/html/rfc5661). Alcune funzionalità, ad esempio le deleghe e il callback di tutti i tipi, gli aggiornamenti e i downgrade dei blocchi, l'autenticazione Kerberos e la crittografia non sono supportati.
- Se la maggior parte delle richieste è incentrata sui metadati, la latenza sarà peggiore rispetto alle operazioni di lettura/scrittura/aggiornamento.
- È necessario creare un nuovo account di archiviazione per creare una condivisione NFS.
- Sono supportate solo le API REST del piano di gestione. Le API REST del piano dati non sono disponibili, quindi gli strumenti come Storage Explorer non funzioneranno con le condivisioni NFS né sarà possibile esplorare i dati della condivisione NFS nel portale di Azure.
- AzCopy non è attualmente supportato.
- Disponibile solo per il livello Premium.
- Le condivisioni NFS accettano solo UID/GID numerici. Per evitare che i client inviino UID/GID alfanumerico, è necessario disabilitare il mapping degli ID.
- Le condivisioni possono essere montate solo da un account di archiviazione in una singola macchina virtuale, quando si usano i collegamenti privati. Il tentativo di montare condivisioni da altri account di archiviazione avrà esito negativo.

### <a name="azure-storage-features-not-yet-supported"></a>Funzionalità di archiviazione di Azure non ancora supportate

Inoltre, le seguenti funzionalità di File di Azure non sono disponibili con le condivisioni NFS:

- Autenticazione basata sull'identità
- Supporto di Backup di Azure
- Snapshot
- Eliminazione temporanea
- Supporto completo per la crittografia in transito (per informazioni dettagliate, vedere la pagina relativa alla [sicurezza NFS](../articles/storage/files/storage-files-compare-protocols.md#security))
- Sincronizzazione file di Azure (disponibile solo per i client Windows, che non sono supportati da NFS 4,1)
