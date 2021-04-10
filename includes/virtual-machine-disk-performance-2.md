---
title: includere file
description: includere file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 82b4c127f983f3133326bf7fb538e40713ef9655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580404"
---
![Grafico che mostra le specifiche D s v 3.](media/vm-disk-performance/dsv3-documentation.jpg)

- Il numero massimo di velocità effettiva del disco non *memorizzato nella cache* è il limite massimo di archiviazione predefinito che la macchina virtuale è in grado di gestire.
- Il limite massimo di velocità effettiva di archiviazione *memorizzata nella cache* è un limite separato quando si Abilita la memorizzazione nella cache dell'host.

La memorizzazione nella cache dell'host funziona avvicinando la risorsa di archiviazione alla macchina virtuale che può essere scritta o letta rapidamente. La quantità di spazio di archiviazione disponibile per la macchina virtuale per la memorizzazione nella cache dell'host è disponibile nella documentazione di. Ad esempio, è possibile vedere che la Standard_D8s_v3 viene fornita con 200 GiB di archiviazione della cache.

È possibile abilitare la memorizzazione nella cache dell'host quando si crea la macchina virtuale e si alleghino i dischi. È anche possibile attivare e disattivare la memorizzazione nella cache dell'host sui dischi in una macchina virtuale esistente.

![Screenshot che mostra la memorizzazione nella cache dell'host.](media/vm-disk-performance/host-caching.jpg)

È possibile modificare la memorizzazione nella cache dell'host in base ai requisiti del carico di lavoro per ogni disco. È possibile impostare la memorizzazione nella cache dell'host:

- Sola **lettura**: per i carichi di lavoro che eseguono solo operazioni di lettura
- **Lettura/scrittura**: per i carichi di lavoro che eseguono un bilanciamento delle operazioni di lettura e scrittura

Se il carico di lavoro non segue uno di questi modelli, non è consigliabile usare la memorizzazione nella cache dell'host.

Di seguito vengono illustrati alcuni esempi di diverse impostazioni della cache host per vedere come influiscono sul flusso di dati e sulle prestazioni. In questo primo esempio si osserverà cosa accade con le richieste di i/o quando l'impostazione di memorizzazione nella cache dell'host è impostata su **sola lettura**.

**Installazione**

- Standard_D8s_v3
  - IOPS memorizzati nella cache: 16.000
  - IOPS non memorizzati nella cache: 12.800
- Disco dati P30
  - IOPS: 5.000
  - Caching dell'host: sola **lettura**

Quando si esegue una lettura e i dati desiderati sono disponibili nella cache, la cache restituisce i dati richiesti. Non è necessario leggere dal disco. Questa lettura viene conteggiata nei limiti memorizzati nella cache della macchina virtuale.

![Diagramma che illustra un hit Read Caching host Read.](media/vm-disk-performance/host-caching-read-hit.jpg)

Quando si esegue una lettura e i dati desiderati *non* sono disponibili nella cache, la richiesta di lettura viene inoltrata al disco. Il disco viene quindi sottopartito alla cache e alla VM. Questa lettura viene conteggiata per il limite non memorizzato nella cache della macchina virtuale e per il limite memorizzato nella cache della macchina virtuale.

![Diagramma che mostra la lettura della cache dell'host Read Miss.](media/vm-disk-performance/host-caching-read-miss.jpg)

Quando viene eseguita una scrittura, la scrittura deve essere scritta sia nella cache che nel disco prima che venga considerata completata. Questa scrittura viene conteggiata per il limite non memorizzato nella cache della VM e il limite memorizzato nella cache della macchina virtuale.

![Diagramma che mostra la scrittura nella cache dell'host di lettura.](media/vm-disk-performance/host-caching-write.jpg)

Si osserverà ora cosa accade con le richieste IO quando l'impostazione della cache host è impostata su **lettura/scrittura**.

**Installazione**

- Standard_D8s_v3
  - IOPS memorizzati nella cache: 16.000
  - IOPS non memorizzati nella cache: 12.800
- Disco dati P30
  - IOPS: 5.000
  - Caching dell'host: **lettura/scrittura**

Un oggetto Read viene gestito allo stesso modo di un oggetto di sola lettura. Le Scritture sono l'unica cosa diversa con la memorizzazione nella cache di lettura/scrittura. Quando si scrive con la memorizzazione nella cache dell'host è impostato su **lettura/scrittura**, la scrittura deve essere scritta solo nella cache host per essere considerata completa. La scrittura viene quindi scritta pigramente sul disco come processo in background. Ciò significa che una scrittura viene conteggiata per i/o memorizzati nella cache quando viene scritta nella cache. Quando viene scritto in modalità differita sul disco, viene conteggiato per l'i/o non memorizzato nella cache.

![Diagramma che mostra la scrittura nella cache dell'host di lettura/scrittura.](media/vm-disk-performance/host-caching-read-write.jpg)

Continuare con la macchina virtuale Standard_D8s_v3. Ad eccezione di questa volta, si Abilita la memorizzazione nella cache dell'host sui dischi. Inoltre, il limite di IOPS della macchina virtuale è 16.000 IOPS. Collegato alla macchina virtuale si trovano tre dischi P30 sottostanti che possono gestire 5.000 IOPS.

**Installazione**

- Standard_D8s_v3
  - IOPS memorizzati nella cache: 16.000
  - IOPS non memorizzati nella cache: 12.800
- Disco del sistema operativo P30
  - IOPS: 5.000
  - Caching dell'host: **lettura/scrittura**
- Due dischi dati P30 × 2
  - IOPS: 5.000
  - Caching dell'host: **lettura/scrittura**

![Diagramma che illustra un esempio di memorizzazione nella cache dell'host.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

L'applicazione usa una macchina virtuale Standard_D8s_v3 con la memorizzazione nella cache abilitata. Esegue una richiesta per 15.000 IOPS. Le richieste vengono suddivise come 5.000 IOPS a ogni disco sottostante collegato. Non si verifica alcuna riduzione delle prestazioni.

## <a name="combined-uncached-and-cached-limits"></a>Limiti combinati non memorizzati nella cache e memorizzati nella cache

I limiti memorizzati nella cache di una macchina virtuale sono separati dai limiti non memorizzati nella cache. Ciò significa che è possibile abilitare la memorizzazione nella cache dell'host sui dischi collegati a una macchina virtuale senza abilitare la memorizzazione nella cache dell'host su altri dischi. Questa configurazione consente alle macchine virtuali di ottenere un i/o di archiviazione totale del limite memorizzato nella cache oltre al limite non memorizzato nella cache.

Di seguito viene illustrato un esempio che consente di comprendere il modo in cui questi limiti interagiscono. Si proseguirà con la configurazione della macchina virtuale Standard_D8s_v3 e dei dischi Premium.

**Installazione**

- Standard_D8s_v3
  - IOPS memorizzati nella cache: 16.000
  - IOPS non memorizzati nella cache: 12.800
- Disco del sistema operativo P30
  - IOPS: 5.000
  - Caching dell'host: **lettura/scrittura**
- Due dischi dati P30 × 2
  - IOPS: 5.000
  - Caching dell'host: **lettura/scrittura**
- Due dischi dati P30 × 2
  - IOPS: 5.000
  - Caching dell'host: **disabilitato**

![Diagramma che illustra un esempio di memorizzazione nella cache dell'host con archiviazione remota.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

In questo caso, l'applicazione in esecuzione in una macchina virtuale Standard_D8s_v3 esegue una richiesta per 25.000 IOPS. La richiesta viene suddivisa come 5.000 IOPS per ogni disco collegato. Tre dischi usano la memorizzazione nella cache dell'host e due dischi non usano la memorizzazione nella cache dell'host.

- Poiché i tre dischi che usano la memorizzazione nella cache dell'host sono entro i limiti memorizzati nella cache di 16.000, le richieste vengono completate correttamente. Non si verifica alcuna riduzione delle prestazioni di archiviazione.
- Poiché i due dischi che non usano la memorizzazione nella cache dell'host si trovano entro i limiti non memorizzati nella cache di 12.800, anche queste richieste vengono completate correttamente. Non si verifica alcuna copertura.

