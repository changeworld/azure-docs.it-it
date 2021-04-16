---
title: Archiviazione in BareMetal per carichi di lavoro Oracle
description: Informazioni sull'archiviazione offerta dall'infrastruttura BareMetal per i carichi di lavoro Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559089"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Archiviazione in BareMetal per carichi di lavoro Oracle

In questo articolo verrà fornita una panoramica dell'archiviazione offerta dall'infrastruttura BareMetal per i carichi di lavoro Oracle.

L'infrastruttura BareMetal per Oracle offre l'archiviazione NFS (Network File System) di NetApp. L'archiviazione NFS non richiede la certificazione Oracle Real Application Clusters (RAC). Per altre informazioni, vedere [Oracle RAC Technologies Matrix for Linux Clusters](https://www.oracle.com/database/technologies/tech-generic-linux-new.html).

Questa offerta di archiviazione include il supporto di livello 3 di un partner OEM, che usa controller di archiviazione A700 o A800.

L'archiviazione dell'infrastruttura BareMetal offre queste funzionalità di archiviazione Premium:

- Volumi di archiviazione per Data/log/quorum/FSA offerti tramite il protocollo dNFS.
- Ridondanza del disco (*protezione da un massimo di due errori del disco*).
- Aumentare i dati a più volumi con un limite di 100 TB per volume.
- Scalabilità orizzontale a più controller di archiviazione fino a 12 controller.
- Nessuna gestione a livello di disco (*add/remove disks*), eseguita automaticamente da Infra.
- Nessun tempo di inattività per la ridistribuzione del contenuto del file in volumi diversi.
- Possibilità di aumentare/ridurre i volumi.
- Integrazione di SnapCenter per il backup tramite clonazione e SnapVault.
- Crittografia dei dati in pausa, che supporta FIPS (140-2).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle considerazioni sull'applicazione di patch all'infrastruttura BareMetal.

> [!div class="nextstepaction"]
> [Considerazioni sull'applicazione di patch per BareMetal per Oracle](oracle-baremetal-patching.md)

