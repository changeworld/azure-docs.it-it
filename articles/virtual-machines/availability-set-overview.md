---
title: Panoramica dei set di disponibilità
description: Informazioni sui set di disponibilità in Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: b4e9f106354915fe40a4bcf9b60bc35443345202
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530544"
---
# <a name="availability-sets-overview"></a>Panoramica dei set di disponibilità

Questo articolo offre una panoramica delle funzionalità di disponibilità delle macchine virtuali di Azure.

## <a name="what-is-an-availability-set"></a>Che cos'è un set di disponibilità? 

Un set di disponibilità è un raggruppamento logico di macchine virtuali che permette ad Azure di comprendere come è compilata l'applicazione per garantirne la ridondanza e la disponibilità. È consigliabile creare due o più macchine virtuali in un set di disponibilità, per garantire un'elevata disponibilità dell'applicazione e raggiungere il [99,95% di disponibilità previsto dal contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Non è previsto alcun costo per il set di disponibilità, si paga solo per ogni istanza di macchina virtuale creata.

## <a name="how-do-availability-sets-work"></a>Come funzionano i set di disponibilità?
A ciascuna macchina virtuale nel set di disponibilità viene assegnato un **dominio di aggiornamento** e un **dominio di errore** dalla piattaforma Azure sottostante. Ogni set di disponibilità può essere configurato con un massimo di tre domini di errore e venti domini di aggiornamento. I domini di aggiornamento indicano gruppi di macchine virtuali e l'hardware fisico sottostante che possono essere riavviati nello stesso momento. Se in un set di disponibilità vengono configurate più di cinque macchine virtuali, la sesta macchina viene inserita nello stesso dominio di aggiornamento della prima, la settima nel dominio di aggiornamento della seconda e così via. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante la manutenzione pianificata, ma ne viene riavviato uno solo alla volta. Un dominio di aggiornamento riavviato ha 30 minuti di tempo per eseguire il ripristino prima che venga avviata la manutenzione in un altro dominio di aggiornamento.

I domini di errore definiscono il gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate all'interno del set di disponibilità sono separate in un massimo di tre domini di errore. Il raggruppamento di più macchine virtuali in un set di disponibilità non garantisce la protezione dell'applicazione da eventuali errori del sistema operativo o di singole applicazioni, ma limita le conseguenze prodotte da potenziali guasti dell'hardware fisico e interruzioni di rete o di alimentazione.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="Diagramma che mostra i vari cluster di calcolo suddivisi in domini di errore e, all'interno di tali domini di errore, sono presenti più domini di aggiornamento":::

Le macchine virtuali sono anche allineate ai domini di errore del disco. Questo allineamento garantisce che tutti i dischi gestiti collegati a una macchina virtuale siano all'interno degli stessi domini di errore. 

In un set di disponibilità gestito possono essere create solo VM con dischi gestiti. Il numero di domini di errore dei dischi gestiti varia in base all'area: due o tre domini di errore di dischi gestiti per area. 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="Diagramma che illustra l'allineamento dei domini di errore per dischi e macchine virtuali.":::

## <a name="next-steps"></a>Passaggi successivi
Ora è possibile iniziare a usare le funzionalità di ridondanza e disponibilità per compilare l'ambiente Azure. Per altre informazioni, vedere le [procedure consigliate per la disponibilità di Azure](/azure/architecture/checklist/resiliency-per-service).

