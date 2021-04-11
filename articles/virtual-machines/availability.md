---
title: Opzioni di disponibilità per le macchine virtuali di Azure
description: Informazioni sulle opzioni di disponibilità per l'esecuzione di macchine virtuali in Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 821c50b5cb48bddb586c3589326a7f31c7bafded
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604533"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Opzioni di disponibilità per le macchine virtuali di Azure
Questo articolo offre una panoramica delle opzioni di disponibilità per le macchine virtuali (VM) di Azure.

## <a name="availability-zones"></a>Zone di disponibilità

[Zone di disponibilità](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) espande il livello di controllo necessario per mantenere la disponibilità delle applicazioni e dei dati nelle macchine virtuali. Una zona di disponibilità è una zona fisicamente separata all'interno di un'area di Azure. Esistono tre zone di disponibilità per ogni area di Azure supportata. 

Ogni zona di disponibilità può contare su risorse di alimentazione, rete e raffreddamento a sé. Progettando le soluzioni per l'uso di macchine virtuali replicate in zone, è possibile proteggere le app e i dati dalla perdita di un data center. Se una zona è compromessa, le app e i dati replicati diventano immediatamente disponibili in un'altra zona. 

## <a name="availability-sets"></a>Set di disponibilità
Un [set di disponibilità](availability-set-overview.md) è un raggruppamento logico di macchine virtuali che consente ad Azure di comprendere come viene compilata l'applicazione per garantire ridondanza e disponibilità. È consigliabile creare due o più macchine virtuali in un set di disponibilità, per garantire un'elevata disponibilità dell'applicazione e raggiungere il [99,95% di disponibilità previsto dal contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Non è previsto alcun costo per il set di disponibilità, si paga solo per ogni istanza di macchina virtuale creata.


## <a name="virtual-machines-scale-sets"></a>Set di scalabilità di macchine virtuali 

I [set di scalabilità di macchine virtuali di Azure](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) consentono di creare e gestire un gruppo di VM con carico bilanciato. Il numero di istanze di macchine virtuali può aumentare o diminuire automaticamente in risposta alla domanda o in base a una pianificazione definita. I set di scalabilità garantiscono una disponibilità elevata per le applicazioni e consentono di gestire, configurare e aggiornare in modo centralizzato molte macchine virtuali. È consigliabile creare due o più macchine virtuali all'interno di un set di scalabilità per fornire un'applicazione a disponibilità elevata e per soddisfare il [99,95% del contratto](https://azure.microsoft.com/support/legal/sla/virtual-machines/)di base di Azure. Non sono previsti costi per il set di scalabilità, ma si paga solo per ogni istanza di macchina virtuale creata.

Le macchine virtuali in un set di scalabilità possono anche essere distribuite in una singola zona di disponibilità o in un'area geografica. Le opzioni di distribuzione della zona di disponibilità possono variare in base alla [modalità di orchestrazione](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context).

## <a name="load-balancer"></a>Bilanciamento del carico
Combinare la [Azure Load Balancer](../load-balancer/load-balancer-overview.md) con una zona di disponibilità o un set di disponibilità per ottenere la massima resilienza dell'applicazione. Il servizio di bilanciamento del carico distribuisce il traffico tra più macchine virtuali ed è incluso nelle macchine virtuali di livello Standard. Non tutti i livelli delle macchine virtuali includono Azure Load Balancer. Per altre informazioni sul bilanciamento del carico delle macchine virtuali, vedere **bilanciamento del carico delle macchine virtuali** per [Linux](linux/tutorial-load-balancer.md) o [Windows](windows/tutorial-load-balancer.md).


## <a name="azure-storage-redundancy"></a>Ridondanza di Archiviazione di Azure
Archiviazione di Azure archivia sempre più copie dei dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. La ridondanza garantisce che l'account di archiviazione soddisfi gli obiettivi di disponibilità e durabilità anche in caso di errori.

Quando si decide quale opzione di ridondanza è migliore per lo scenario in uso, considerare i compromessi tra i costi più bassi e la disponibilità più elevata. Ecco i fattori che consentono di determinare l'opzione di ridondanza da scegliere:
- Modalità di replica dei dati nell'area primaria
- Se i dati vengono replicati in una seconda area geograficamente distante rispetto all'area primaria, per la protezione da emergenze a livello di area
- Se l'applicazione richiede l'accesso in lettura ai dati replicati nell'area secondaria se l'area primaria, per un qualsiasi motivo, non è più disponibile

Per altre informazioni, vedere [ridondanza di archiviazione di Azure](../storage/common/storage-redundancy.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Un'organizzazione deve adottare una strategia di continuità aziendale e ripristino di emergenza (BCDR) che garantisce la sicurezza dei dati e le app e i carichi di lavoro online, quando si verificano interruzioni pianificate e non pianificate.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) garantisce la continuità aziendale mantenendo le app aziendali e i carichi di lavoro in esecuzione durante le interruzioni. Site Recovery replica i carichi di lavoro in esecuzione su macchine fisiche e virtuali (VM) da un sito primario in una località secondaria. Quando si verifica un'interruzione nel sito primario, viene eseguito il failover nella località secondaria, da dove è possibile accedere alle app. Quando la località primaria è di nuovo disponibile, è possibile eseguire il failback.

Site Recovery può gestire la replica per:
- Replica di VM di Azure tra aree di Azure.
- VM locali, Azure Stack macchine virtuali e server fisici.

## <a name="next-steps"></a>Passaggi successivi
- [Creare una macchina virtuale in una zona di disponibilità](/azure/virtual-machines/linux/create-cli-availability-zone)
- [Creare una macchina virtuale in un set di disponibilità](/azure/virtual-machines/linux/tutorial-availability-sets)
- [Creare un set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/quick-create-portal.md)