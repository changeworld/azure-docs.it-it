---
title: includere file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750788"
---
- Questa funzionalità è attualmente supportata solo per le unità SSD Premium.
- È necessario deallocare la VM o scollegare il disco da una macchina virtuale in esecuzione prima di poter modificare il livello del disco.
- I livelli di prestazioni P60, p70 e P80 possono essere usati solo da dischi di dimensioni superiori a 4.096 GiB.
- Il downgrade del livello di prestazioni di un disco può essere eseguito solo una volta ogni 12 ore.

## <a name="change-performance-tier-without-downtime-preview"></a>Modificare il livello di prestazioni senza tempi di inattività (anteprima)

In genere, è necessario deallocare la VM o scollegare il disco per modificare il livello di prestazioni. Tuttavia, se si abilita questa funzionalità di anteprima, non è necessario deallocare la VM o scollegare il disco per modificare il livello. È possibile iscriversi per l'anteprima [qui](https://aka.ms/liveperftiersignup).

L'anteprima presenta le limitazioni seguenti:
- Disponibile solo nell'area EastUS2EUAP.
- Attualmente non disponibile per i dischi condivisi
- `2020-12-01`Per modificare i livelli di prestazioni senza tempi di inattività, è necessario usare Azure Resource Manager modelli con l'API.