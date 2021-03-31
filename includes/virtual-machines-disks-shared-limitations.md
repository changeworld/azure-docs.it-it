---
title: includere file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 22a1a4b99717df32a40ea69ebb65a3a8e14ee2b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102510894"
---
L'abilitazione dei dischi condivisi è disponibile solo per un subset di tipi di disco. Attualmente solo dischi e SSD Premium possono abilitare i dischi condivisi. Ogni disco gestito con dischi condivisi abilitati è soggetto alle limitazioni seguenti, organizzati in base al tipo di disco:

### <a name="ultra-disks"></a>Dischi Ultra

I dischi Ultra includono un elenco separato di limitazioni, non correlate ai dischi condivisi. Per le limitazioni del disco Ultra, vedere [uso di dischi ultra di Azure](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Quando si condividono dischi Ultra, presentano le seguenti limitazioni aggiuntive:

- Attualmente limitato al supporto Azure Resource Manager o SDK. 
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server. per informazioni dettagliate, vedere [requisiti hardware per il clustering di failover e opzioni di archiviazione](/windows-server/failover-clustering/clustering-requirements).

I dischi Ultra condivisi sono disponibili in tutte le aree che supportano i dischi ultra per impostazione predefinita e non richiedono l'iscrizione per l'uso.

### <a name="premium-ssds"></a>SSD Premium

- Attualmente limitato al supporto Azure Resource Manager o SDK. 
- Può essere abilitato solo sui dischi dati, non sui dischi del sistema operativo.
- La memorizzazione nella cache dell'host **ReadOnly** non è disponibile per le unità SSD Premium con `maxShares>1` .
- L'espansione del disco non è disponibile per SSD Premium con `maxShares>1` .
- Quando si usano i set di disponibilità e i set di scalabilità di macchine virtuali con i dischi condivisi di Azure, l' [allineamento del dominio di errore di archiviazione](../articles/virtual-machines/availability.md) con il dominio di errore della macchina virtuale non viene applicato per il disco dati condiviso.
- Quando si usano i [gruppi di posizionamento prossimità (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md), tutte le macchine virtuali che condividono un disco devono far parte dello stesso PPG.
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server. per informazioni dettagliate, vedere [requisiti hardware per il clustering di failover e opzioni di archiviazione](/windows-server/failover-clustering/clustering-requirements).
- Il supporto Azure Site Recovery non è ancora disponibile.
- Backup di Azure è disponibile tramite [backup dischi di Azure (anteprima)](../articles/backup/disk-backup-overview.md).

#### <a name="regional-availability"></a>Disponibilità a livello di area

Le unità SSD Premium condivise sono disponibili in tutte le aree in cui sono disponibili i dischi gestiti.