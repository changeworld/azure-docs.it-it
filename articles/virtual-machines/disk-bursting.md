---
title: Bursting del disco gestito
description: Informazioni sul picco del disco per i dischi di Azure e le macchine virtuali di Azure.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4024d2b1357f3dda8216e9ebdd2055b28b064d33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677476"
---
# <a name="managed-disk-bursting"></a>Bursting del disco gestito
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

Gli [SSD Premium](disks-types.md#premium-ssd) di Azure offrono due modelli di espansione:

- Un modello di espansione su richiesta (anteprima), in cui il disco viene sottoposto a picchi ogni volta che le sue esigenze superano la capacità corrente. Questo modello comporta addebiti aggiuntivi ogni volta che il disco si esaurisce. Il numero di picchi non creditizi è disponibile solo su dischi di dimensioni superiori a 512 GiB.
- Un modello basato su crediti, in cui il disco si produrrà solo se i crediti di espansione si accumulano nel bucket di credito. Questo modello non comporta addebiti aggiuntivi quando si verificano picchi di disco. Il picchi basato sul credito è disponibile solo su dischi 512 GiB e più piccoli.

Inoltre, il [livello di prestazioni dei dischi gestiti può essere modificato](disks-change-performance.md), il che potrebbe essere ideale se il carico di lavoro altrimenti venisse eseguito in sequenza.

|  |Picchi basati sul credito  |Espansione su richiesta  |Modifica del livello di prestazioni  |
|---------|---------|---------|---------|
| Scenari|Ideale per la scalabilità a breve termine (30 minuti o meno).|Ideale per la scalabilità a breve termine (non limitato all'ora).|Ideale se il carico di lavoro viene eseguito in modo continuativo in un impeto.|
|Costo     |Gratuito         |Il costo è variabile. per informazioni dettagliate, vedere la sezione relativa alla [fatturazione](#billing) .        |Il costo di ogni livello di prestazioni è fisso, vedere [Managed disks prezzi](https://azure.microsoft.com/pricing/details/managed-disks/) per i dettagli.         |
|Disponibilità     |Disponibile solo per le unità SSD Premium 512 GiB e le dimensioni inferiori.         |Disponibile solo per unità SSD Premium di dimensioni superiori a 512 GiB.         |Disponibile per tutte le dimensioni SSD Premium.         |
|Abilitazione     |Abilitata per impostazione predefinita nei dischi idonei.         |Deve essere abilitato dall'utente.         |L'utente deve modificare manualmente il livello.         |

## <a name="common-scenarios"></a>Scenari comuni
Gli scenari seguenti possono trarre molto vantaggio dall'espansione:
- **Migliorare i tempi di avvio**  : con l'espansione, l'istanza di viene avviata con una velocità notevolmente più veloce. Ad esempio, il disco del sistema operativo predefinito per le macchine virtuali abilitate per Premium è il disco P4, ovvero le prestazioni con provisioning fino a 120 IOPS e 25 MB/s. Con l'espansione, il P4 può raggiungere 3500 IOPS e 170 MB/s, consentendo all'avvio di accelerare fino a 6X.
- **Gestire processi batch** : alcuni carichi di lavoro dell'applicazione sono di natura ciclica. Per la maggior parte del tempo è necessario ottenere prestazioni di base e prestazioni più elevate per brevi periodi di tempo. Un esempio è un programma di contabilità che elabora transazioni giornaliere che richiedono una piccola quantità di traffico su disco. Alla fine del mese, il programma completerebbe la riconciliazione dei report che richiedono una quantità molto più elevata di traffico su disco.
- **Picchi di traffico** : i server Web e le relative applicazioni possono riscontrare picchi di traffico in qualsiasi momento. Se il server Web è supportato da macchine virtuali o dischi che usano la modalità di espansione, i server saranno più attrezzati per gestire i picchi di traffico. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Passaggi successivi

Per abilitare l'espansione su richiesta, vedere Abilitare l'espansione [su richiesta](disks-enable-bursting.md).
Per informazioni su come ottenere informazioni sulle risorse di espansione, vedere metriche di espansione del [disco](disks-metrics.md).
