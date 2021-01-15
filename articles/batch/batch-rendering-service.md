---
title: Panoramica del rendering
description: Introduzione all'uso di Azure per il rendering e panoramica delle funzionalità di rendering di Azure Batch
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232115"
---
# <a name="rendering-using-azure"></a>Rendering tramite Azure

Il rendering è il processo di conversione di modelli 3D in immagini 2D. I file delle scene 3D vengono creati in applicazioni come Autodesk 3ds Max, Autodesk Maya e Blender.  Le applicazioni di rendering come Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray e Blender Cycles producono immagini 2D.  A volte dai file delle scene vengono create singole immagini. Tuttavia, è prassi comune eseguire la modellazione e il rendering di più immagini e quindi combinarle in un'animazione.

Il carico di lavoro di rendering viene usato in modo intensivo per gli effetti speciali (VFX) nel settore dei media e dell'intrattenimento. Il rendering viene usato anche in molti altri settori, tra cui quelli pubblicitario, manifatturiero, petrolchimico e della vendita al dettaglio.

Il processo di rendering usa risorse di calcolo in modo intensivo, in quanto possono essere presenti molti fotogrammi/immagini da produrre e il rendering di ogni immagine può richiedere molte ore.  Il rendering è quindi un carico di lavoro di elaborazione batch perfetto che può sfruttare Azure per eseguire molti rendering in parallelo e utilizzare un'ampia gamma di hardware, incluse le GPU.

## <a name="why-use-azure-for-rendering"></a>Perché usare Azure per il rendering?

Per molti motivi, il rendering è un carico di lavoro perfettamente adatto per Azure:

* I processi di rendering possono essere suddivisi in molte parti, che a loro volta possono essere eseguite in parallelo usando diverse macchine virtuali:
  * Le animazioni sono costituite da molti fotogrammi, di ciascuno dei quali è possibile eseguire il rendering in parallelo.  Più sono le macchine virtuali disponibili per elaborare ogni fotogramma, più velocemente possono essere prodotti tutti i fotogrammi e l'animazione.
  * Alcuni software di rendering permettono la suddivisione di singoli fotogrammi in più parti, ad esempio in riquadri o sezioni.  Di ogni parte è possibile eseguire il rendering separatamente, per poi ricombinarle nell'immagine finale al termine di tutte le operazioni.  Più sono le macchine virtuali disponibili, più rapidamente avverrà il rendering di un fotogramma.
* I progetti di rendering possono richiedere una scalabilità elevatissima:
  * Il rendering di singoli fotogrammi, che possono essere anche molto complessi, può richiedere molte ore, anche su hardware di fascia alta. Le animazioni possono essere costituite da centinaia di migliaia di fotogrammi.  Per il rendering di animazioni di qualità elevata, è necessaria una notevole capacità di calcolo in un periodo di tempo ragionevole.  In alcuni casi, sono stati usati oltre 100.000 core per il rendering di migliaia di fotogrammi in parallelo.
* Le attività di rendering sono basate sul progetto e richiedono capacità di calcolo variabili:
  * Allocare la capacità di calcolo e archiviazione nel modo necessario, aumentarla o ridurla in base al carico durante un progetto e rimuoverla al termine del progetto.
  * Pagare per la capacità solo dopo che è stata allocata, evitando di pagare in assenza di carico, ad esempio tra progetti.
  * Predisporre l'ambiente per i picchi dovuti a modifiche impreviste: aumentare la capacità in caso di modifiche impreviste nelle fasi successive di un progetto se queste modifiche devono essere elaborate in tempi ridotti.
* Scegliere tra un'ampia gamma di opzioni hardware in base ad applicazione, carico di lavoro e intervallo di tempo:
  * In Azure è disponibile una vasta gamma di soluzioni hardware che possono essere allocate e gestite con Batch.
  * A seconda del progetto, il requisito più importante può essere il miglior rapporto tra prezzo e prestazioni o migliori prestazioni nel complesso.  Scene e/o applicazioni di rendering diverse avranno requisiti di memoria diversi.  Alcune applicazioni di rendering possono sfruttare le GPU per ottenere prestazioni ottimali o determinate funzionalità. 
* Le VM con priorità bassa o [spot](https://azure.microsoft.com/pricing/spot/) riducono i costi:
  * Le VM spot e con priorità bassa sono disponibili per uno sconto elevato rispetto alle VM standard e sono adatte per alcuni tipi di processo.
  
## <a name="existing-on-premises-rendering-environment"></a>Ambiente di rendering locale esistente

Il caso più comune è che la farm di rendering locale esistente sia gestita da un'applicazione di gestione di rendering, ad esempio PipelineFX Qube, Royal render, Thinkbox scadenza o un'applicazione personalizzata.  Il requisito consiste nell'estendere la capacità della farm di rendering locale tramite macchine virtuali di Azure.

L'infrastruttura e i servizi di Azure vengono usati per creare un ambiente ibrido in cui Azure viene usato per integrare la capacità locale. Ad esempio:

* Usare una [rete virtuale](../virtual-network/virtual-networks-overview.md) per inserire le risorse di Azure nella stessa rete della farm di rendering locale.
* Usare [vFXT per Azure o la](../avere-vfxt/avere-vfxt-overview.md) [cache HPC di Azure](../hpc-cache/hpc-cache-overview.md) per memorizzare nella cache i file di origine in Azure per ridurre l'uso della larghezza di banda e la latenza, ottimizzando le prestazioni.
* Verificare che il server licenze esistente si trovi nella rete virtuale e acquistare le licenze aggiuntive necessarie per soddisfare la capacità aggiuntiva basata su Azure.

## <a name="no-existing-render-farm"></a>Nessuna farm di rendering esistente

Le workstation client possono eseguire il rendering, ma il carico di rendering è in aumento e impiega troppo tempo per usare solo la capacità della workstation.

Sono disponibili due opzioni principali:

* Distribuire un gestore di rendering locale, ad esempio Royal rendering, e configurare un ambiente ibrido per l'uso di Azure quando sono necessarie altre capacità o prestazioni. Un gestore di rendering è appositamente progettato per il rendering dei carichi di lavoro e include i plug-in per le applicazioni client più diffuse, consentendo un facile invio di processi di rendering.

* Soluzione personalizzata che usa Azure Batch per allocare e gestire la capacità di calcolo, nonché per fornire la pianificazione dei processi per eseguire i processi di rendering.

## <a name="next-steps"></a>Passaggi successivi

 Informazioni su come [usare l'infrastruttura e i servizi di Azure per estendere una farm di rendering locale esistente](https://azure.microsoft.com/solutions/high-performance-computing/rendering/).

Altre informazioni sulle [funzionalità di rendering Azure batch](batch-rendering-functionality.md).
