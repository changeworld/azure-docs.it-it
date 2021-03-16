---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563241"
---
| Modello di utilizzo | Modalità di memorizzazione nella cache | Verifica del back-end | Ritardo massimo write-back |
|--|--|--|--|
| Lettura di scritture complesse e non frequenti | Lettura | Mai | nessuno |
| Scritture superiori al 15% | Lettura/Scrittura | 8 ore | 20 minuti |
| Client che ignorano la cache | Lettura | 30 secondi | nessuno |
| Scritture maggiori del 15%, controllo di back-end frequente (30 secondi) | Lettura/Scrittura | 30 secondi | 20 minuti |
| Più di 15% Scritture, controllo back-end frequente (60 secondi) | Lettura/Scrittura | 60 secondi | 20 minuti |
| Scritture con dimensioni maggiori del 15%, writeback frequente | Lettura/Scrittura | 30 secondi | 30 secondi |
| Lettura intensiva, controllo del server di backup ogni 3 ore | Lettura | 3 ore | nessuno |
