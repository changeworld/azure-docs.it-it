---
title: includere file
description: includere file
services: virtual-machines
author: styli365
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: sttsinar
ms.custom: include file
ms.openlocfilehash: 3d78441e56e23cf49b09073fdf88bef4b3434da9
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473897"
---
Calcolo di Azure offre dimensioni delle macchine virtuali con piano Isolato per uno specifico tipo di hardware e dedicate a un singolo cliente. Le dimensioni isolate si trovano in un ambiente di generazione hardware specifico e verranno deprecate quando la generazione dell'hardware viene ritirata.

Le dimensioni delle macchine virtuali isolate sono ideali per i carichi di lavoro che richiedono un elevato livello di isolamento rispetto ai carichi di lavoro di altri clienti per motivi che includono requisiti di conformità e normativi.  L'utilizzo di dimensioni con piano Isolato garantisce che la macchina virtuale sia l'unica in esecuzione nella specifica istanza del server. 


Inoltre, poiché le macchine virtuali di dimensioni isolate sono di grandi dimensioni, i clienti possono scegliere di suddividere le risorse di queste macchine virtuali usando il [supporto di Azure per le macchine virtuali nidificate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Le offerte di macchine virtuali con piano Isolato correnti includono:
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5


> [!NOTE]
> Le dimensioni delle VM isolate hanno una durata limitata dell'hardware. Per informazioni dettagliate, vedere di seguito.

## <a name="deprecation-of-isolated-vm-sizes"></a>Deprecazione delle dimensioni delle VM isolate

Le dimensioni delle VM isolate hanno una durata limitata dell'hardware. Azure emetterà promemoria 12 mesi prima della data di deprecazione ufficiale delle dimensioni e fornirà un'offerta isolata aggiornata per la valutazione.

| Dimensione | Data di ritiro isolamento | 
| --- | --- |
| Standard_DS15_v2 | 15 maggio 2020 |
| Standard_D15_v2  | 15 maggio 2020 |
| Standard_G5  | 15 febbraio 2021 |
| Standard_GS5  | 15 febbraio 2021 |
| Standard_E64i_v3  | 15 febbraio 2021 |
| Standard_E64is_v3  | 15 febbraio 2021 |


## <a name="faq"></a>Domande frequenti
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>D: le dimensioni verranno ritirate o solo la relativa funzionalità di "isolamento"?
**R**: attualmente è in corso il ritiro solo della funzionalità di isolamento delle dimensioni della macchina virtuale. Le dimensioni isolate deprecate continueranno a esistere nello stato non isolato. Se l'isolamento non è necessario, non viene eseguita alcuna azione e la macchina virtuale continuerà a funzionare come previsto.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>D: si verifica un tempo di inattività quando la macchina virtuale si trova in un hardware non isolato?
**R**: se non è necessario l'isolamento, non è necessaria alcuna azione e non si verifica alcun tempo di inattività. Al contrario, se è necessario l'isolamento, l'annuncio includerà le dimensioni consigliate per la sostituzione. La selezione delle dimensioni di sostituzione richiederà ai clienti di ridimensionare le proprie macchine virtuali.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>D: esiste una Delta di costo per il passaggio a una macchina virtuale non isolata?
**R**: No

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>D: quando le altre dimensioni isolate verranno ritirate?
**R**: verranno forniti promemoria 12 mesi prima della deprecazione ufficiale della dimensione isolata. L'annuncio più recente include il ritiro delle funzionalità di isolamento di Standard_G5, Standard_GS5, Standard_E64i_v3 e Standard_E64i_v3.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>D: sono un cliente di Azure Service Fabric che si basa sui livelli di durabilità Silver o Gold. Questa modifica ha effetto?
**R**: No. Le garanzie fornite dai livelli di [durabilità](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) di Service Fabric continueranno a funzionare anche dopo questa modifica. Se è necessario un isolamento hardware fisico per altri motivi, potrebbe essere comunque necessario eseguire una delle azioni descritte in precedenza. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>D: quali sono le attività cardine per D15_v2 o DS15_v2 il ritiro dell'isolamento? 
**A**: 
 
| Data | Azione |
|---|---| 
| 15 maggio 2019<sup>1</sup> | Annuncio ritiro isolamento D/DS15_v2| 
| 15 maggio 2020 | Garanzia di isolamento D/DS15_v2 rimossa| 

<sup>1</sup> il cliente esistente che usa queste dimensioni riceverà un messaggio di posta elettronica con istruzioni dettagliate sui passaggi successivi.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>D: quali sono le attività cardine per G5, Gs5, E64i_v3 e E64is_v3 il ritiro dell'isolamento? 
**A**: 
 
| Data | Azione |
|---|---|
| 15 febbraio 2020<sup>1</sup> | Avviso di ritiro dell'isolamento G5/GS5/E64i_v3/E64is_v3 |
| 15 febbraio 2021 | Garanzia di isolamento G5/GS5/E64i_v3/E64is_v3 rimossa |

<sup>1</sup> il cliente esistente che usa queste dimensioni riceverà un messaggio di posta elettronica con istruzioni dettagliate sui passaggi successivi.  

## <a name="next-steps"></a>Passaggi successivi

I clienti possono anche scegliere di suddividere ulteriormente le risorse di tali macchine virtuali con piano Isolato usando il [supporto di Azure per le macchine virtuali annidate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
