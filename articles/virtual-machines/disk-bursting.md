---
title: Bursting del disco gestito
description: Informazioni sul picco del disco per i dischi di Azure e le macchine virtuali di Azure.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576194"
---
# <a name="managed-disk-bursting"></a>Bursting del disco gestito
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Bursting a livello di macchina virtuale
L'espansione a livello di macchina virtuale è abilitata nelle serie di macchine virtuali seguenti in tutte le aree in cui sono supportate:
- [Serie Lsv2](lsv2-series.md)
- [Serie Dsv3](dv3-dsv3-series.md)
- [Serie Esv3](ev3-esv3-series.md)

Per impostazione predefinita, il bursting è abilitato per le macchine virtuali che lo supportano.

## <a name="disk-level-bursting"></a>Bursting a livello del disco
Il bursting è disponibile anche nelle unità [SSD Premium](disks-types.md#premium-ssd) per le dimensioni dei dischi P20 e più piccole in tutte le aree dei cloud pubblico, per enti pubblici e per la Cina di Azure. Per impostazione predefinita, il bursting del disco è abilitato nelle distribuzioni nuove ed esistenti delle dimensioni dei dischi che lo supportano. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]
