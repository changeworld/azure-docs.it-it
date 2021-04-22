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
ms.openlocfilehash: 6e819e1078ac90ef16070702e7961122b06c1d6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107879943"
---
Durante l'anteprima, NFS presenta le limitazioni seguenti:

- NFS 4.1 attualmente supporta solo la maggior parte delle funzionalità della [specifica del protocollo](https://tools.ietf.org/html/rfc5661). Alcune funzionalità, ad esempio deleghe e callback di tutti i tipi, aggiornamenti dei blocchi e downgrade, autenticazione Kerberos e crittografia non sono supportate.
- Se la maggior parte delle richieste è incentrata sui metadati, la latenza sarà peggiore rispetto alle operazioni di lettura/scrittura/aggiornamento.
- Le condivisioni NFS possono essere abilitate/create solo in nuovi account di archiviazione e non in quelli esistenti
- Sono supportate solo le API REST del piano di gestione. Le API REST del piano dati non sono disponibili, il che significa che strumenti come Storage Explorer non funzioneranno con le condivisioni NFS né sarà possibile esplorare i dati della condivisione NFS nel portale di Azure.
- AzCopy non è attualmente supportato.
- Disponibile solo per il livello Premium.
- Le condivisioni NFS accettano solo UID/GID numerici. Per evitare che i client inviino UID/GID alfanumerici, è necessario disabilitare il mapping degli ID.
- Le condivisioni possono essere montate solo da un account di archiviazione in una singola macchina virtuale quando si usano collegamenti privati. Il tentativo di montare condivisioni da altri account di archiviazione avrà esito negativo.
- È meglio fare affidamento sulle autorizzazioni assegnate al gruppo primario. In alcuni casi, le autorizzazioni allocate al gruppo non primario dell'utente possono comportare l'accesso negato a causa di un bug noto.

### <a name="azure-storage-features-not-yet-supported"></a>Archiviazione di Azure funzionalità non ancora supportate

Inoltre, le seguenti File di Azure non sono disponibili con le condivisioni NFS:

- Autenticazione basata sull'identità
- Supporto di Backup di Azure
- Snapshot
- Eliminazione temporanea
- Supporto completo della crittografia in transito (per informazioni dettagliate, vedere [Sicurezza NFS)](../articles/storage/files/storage-files-compare-protocols.md#security)
- Sincronizzazione file di Azure (disponibile solo per i client Windows, che NFS 4.1 non supporta)
