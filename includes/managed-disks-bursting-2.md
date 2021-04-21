---
title: includere file
description: includere file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4162fe12ff54f16cd5f982f6a576905227c9a107
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107821000"
---
## <a name="disk-level-bursting"></a>Burst a livello di disco

### <a name="on-demand-bursting-preview"></a>Bursting su richiesta (anteprima)

I dischi che usano il modello di bursting su richiesta di burst del disco possono superare le destinazioni originali di cui è stato effettuato il provisioning, con la frequenza necessaria per il carico di lavoro, fino alla destinazione burst massima. In un disco P30 da 1 TiB, ad esempio, le operazioni di I/O al secondo di cui è stato effettuato il provisioning sono 5000. Quando il bursting del disco è abilitato su questo disco, i carichi di lavoro possono eseguire operazioni di I/O su questo disco fino a ottenere prestazioni di burst ma non superiori a 30.000 IOPS e 1.000 MBps.

Se si prevede che i carichi di lavoro esercitino spesso oltre la destinazione di prestazioni di cui è stato effettuato il provisioning, l'burst del disco non sarà conveniente. In questo caso, è consigliabile modificare il livello di prestazioni del disco [a](../articles/virtual-machines/disks-performance-tiers.md) un livello superiore, per migliorare le prestazioni di base. Esaminare i dettagli di fatturazione e valutarne il modello di traffico dei carichi di lavoro.

Prima di abilitare il bursting su richiesta, comprendere quanto segue:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Fatturazione

Ai dischi che usano il modello di burst su richiesta viene addebitata una tariffa fissa per l'abilitazione di burst oraria e i costi delle transazioni si applicano a qualsiasi transazione burst oltre la destinazione di cui è stato effettuato il provisioning. I costi delle transazioni vengono addebitati usando il modello con pagamento in base al pagamento in base alle operazioni di I/O su disco non memorizzate nella cache, incluse le operazioni di lettura e scrittura che superano le destinazioni di cui è stato effettuato il provisioning. Di seguito è riportato un esempio di modelli di traffico su disco in un'ora di fatturazione:

Configurazione del disco: SSD Premium - 1 TiB (P30), Burst del disco abilitato.

- 00:00:00 – 00:10:00 Operazioni di I/O al secondo del disco al di sotto della destinazione di cui è stato effettuato il provisioning di 5.000 operazioni di I/O al secondo 
- 00:10:01 – 00:10:10 L'applicazione ha eseguito un processo batch causando un burst delle operazioni di I/O al secondo del disco a 6.000 IOPS per 10 secondi 
- 00:10:11 – 00:59:00 Operazioni di I/O al secondo del disco al di sotto della destinazione di cui è stato effettuato il provisioning di 5.000 operazioni di I/O al secondo 
- 00:59:01 – 01:00:00 L'applicazione ha eseguito un altro processo batch causando un burst delle operazioni di I/O al secondo del disco a 7.000 IOPS per 60 secondi 

In questa ora di fatturazione il costo di burst è costituito da due addebiti:

Il primo addebito è la tariffa flat di abilitazione burst di $X (determinata dall'area). Questa tariffa fissa viene sempre addebitata in caso di disattesa del disco dello stato del collegamento fino a quando non viene disabilitata. 

Il secondo è il costo delle transazioni burst. Il bursting del disco si è verificato in due fasce orarie. Da 00:10:01 a 00:10:10, la transazione burst accumulata è (6.000 - 5.000) X 10 = 10.000. Dalle 00:59:01 alle 01:00:00, la transazione burst accumulata è (7.000 - 5.000) X 60 = 120.000. Le transazioni burst totali sono 10.000 + 120.000 = 130.000. Il costo delle transazioni burst verrà addebitato $Y in base a 13 unità di 10.000 transazioni (in base ai prezzi regionali).

In questo modo, il costo totale per il bursting del disco di questa ora di fatturazione equivale a $X + $Y. Lo stesso calcolo si applica al burst rispetto alla destinazione con provisioning di MBps. L'overage di MB viene tradotto in transazioni con dimensioni di I/O di 256 KB. Se il traffico del disco supera sia le operazioni di I/O al secondo con provisioning che la destinazione MBps, è possibile fare riferimento all'esempio seguente per calcolare le transazioni burst. 

Configurazione del disco: SSD Premium - 1 TB (P30), bursting del disco abilitato.

- 00:00:01 – 00:00:05 L'applicazione ha eseguito un processo batch causando il burst delle operazioni di I/O al secondo del disco a 10.000 IOPS e 300 MBps per cinque secondi.
- 00:00:06 - 00:00:10 L'applicazione ha eseguito un processo di ripristino causando il burst delle operazioni di I/O al secondo del disco a 6.000 IOPS e 600 MBps per cinque secondi.

La transazione burst viene conteggiata come numero massimo di transazioni da operazioni di I/O al secondo o burst di MBps. Da 00:00:01 a 00:00:05, la transazione burst accumulata è massima ((10.000 - 5.000), (300 - 200) * 1024 / 256)) * 5 = 25.000 transazioni. Da 00:00:06 a 00:00:10, la transazione burst accumulata è massima ((6.000 - 5.000), (600 - 200) * 1024 / 256)) * 5 = 8.000 transazioni. Si include anche la tariffa fissa per l'abilitazione del burst per ottenere il costo totale per l'abilitazione del bursting dei dischi su richiesta. 

È possibile fare riferimento alla pagina [Managed Disks prezzi](https://azure.microsoft.com/pricing/details/managed-disks/) per informazioni dettagliate sui prezzi e usare il Calcolatore prezzi di [Azure](https://azure.microsoft.com/pricing/calculator/?service=storage) per eseguire la valutazione per il carico di lavoro. 


Per abilitare il bursting su richiesta, vedere [Abilitare il bursting](../articles/virtual-machines/disks-enable-bursting.md)su richiesta.

### <a name="credit-based-bursting"></a>Bursting basato sul credito

Il bursting basato sul credito è disponibile per le dimensioni dei dischi P20 e inferiori in tutte le aree nei cloud pubblici, pubblici e cinesi di Azure. Per impostazione predefinita, l'bursting del disco è abilitato in tutte le distribuzioni nuove ed esistenti di dimensioni di disco supportate. Il bursting a livello di macchina virtuale usa solo burst basati sul credito.

## <a name="virtual-machine-level-bursting"></a>Burst a livello di macchina virtuale

Il bursting a livello di macchina virtuale usa solo il modello basato sul credito per il bursting, che è abilitato per impostazione predefinita per tutte le macchine virtuali che lo supportano.

Lo bursting a livello di macchina virtuale è abilitato in tutte le aree del cloud pubblico di Azure con le dimensioni supportate seguenti: 
- [Serie Lsv2](../articles/virtual-machines/lsv2-series.md)
- [Serie Dv3 e Dsv3](../articles/virtual-machines/dv3-dsv3-series.md)
- [Serie Ev3 e Esv3](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Flusso di burst

Il sistema di credito con burst si applica allo stesso modo sia a livello di macchina virtuale che a livello di disco. La risorsa, una macchina virtuale o un disco, inizierà con crediti completamente stoccati nel proprio bucket burst. Questi crediti consentono di eseguire un burst fino a 30 minuti alla frequenza di burst massima. Si accumulano crediti ogni volta che le operazioni di I/O al secondo o MB/s della risorsa vengono utilizzate al di sotto della destinazione delle prestazioni della risorsa. Se la risorsa ha accumulato crediti con burst e il carico di lavoro richiede prestazioni aggiuntive, la risorsa può usare tali crediti per andare oltre i limiti di prestazioni e aumentarne le prestazioni per soddisfare le richieste del carico di lavoro.

![Diagramma di bucket con burst.](media/managed-disks-bursting/bucket-diagram.jpg)

Il modo in cui si spendono i crediti disponibili è l'utente. È possibile usare i 30 minuti di crediti burst consecutivamente o sporadicamente per tutto il giorno. Quando vengono distribuite, le risorse vengono distribuite con un'allocazione completa dei crediti. Quando queste si esezione, il ripristino richiede meno di un giorno. I crediti possono essere spesi a discrezione dell'utente, non è necessario che il bucket di burst sia pieno per consentire il burst delle risorse. L'accumulo di burst varia a seconda di ogni risorsa, poiché si basa su operazioni di I/O al secondo non utilizzate e MB/s inferiori ai relativi obiettivi di prestazioni. Le risorse con prestazioni di base più elevate possono accumulare i crediti di burst più velocemente rispetto alle risorse con prestazioni di base inferiori. Ad esempio, un idling del disco P1 accumula 120 operazioni di I/O al secondo, mentre un disco P20 insoddiscipato accumula 2.300 operazioni di I/O al secondo.

## <a name="bursting-states"></a>Stati di burst
Esistono tre stati in cui la risorsa può essere in cui è abilitato il bursting:
- **Accumulo: il** traffico di I/O della risorsa usa un valore inferiore rispetto alla destinazione delle prestazioni. L'accumulo di crediti con burst per operazioni di I/O al secondo e MB/s viene eseguito separatamente. La risorsa può accumulare crediti di IOPS e spendere crediti MB/s o viceversa.
- **Bursting:** il traffico della risorsa usa più risorse rispetto alla destinazione delle prestazioni. Il traffico burst consumerà in modo indipendente crediti da operazioni di I/O al secondo o larghezza di banda.
- **Costante:** il traffico della risorsa è esattamente in corrispondenza della destinazione delle prestazioni.

## <a name="bursting-examples"></a>Esempi di burst

Gli esempi seguenti illustrano il funzionamento del burst con diverse combinazioni di macchine virtuali e dischi. Per semplificare gli esempi, ci si concentrerà su MB/s, ma la stessa logica viene applicata in modo indipendente alle operazioni di I/O al secondo.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Macchina virtuale senza burst con dischi con burst
**Combinazione di vm e dischi:** 
- Standard_D8as_v4 
    - MB/s non memorizzati nella cache: 192
- Disco del sistema operativo P4
    - MB/s di cui è stato effettuato il provisioning: 25
    - Max burst MB/s: 170 
- 2 dischi dati P10 
    - MB/s di cui è stato effettuato il provisioning: 100
    - Max burst MB/s: 170

 Quando la macchina virtuale si avvia, recupera i dati dal disco del sistema operativo. Poiché il disco del sistema operativo fa parte di una macchina virtuale che viene avviato, il disco del sistema operativo sarà pieno di crediti burst. Questi crediti consentiranno l'avvio del disco del sistema operativo a 170 MB/s al secondo.

![La macchina virtuale invia una richiesta per 192 MB/s di velocità effettiva al disco del sistema operativo, il disco del sistema operativo risponde con dati di 170 MB/s.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

Al termine dell'avvio, un'applicazione viene quindi eseguita nella macchina virtuale con un carico di lavoro non critico. Questo carico di lavoro richiede 15 MB/S che vengono distribuiti in modo uniforme in tutti i dischi.

![L'applicazione invia una richiesta per 15 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale accetta la richiesta e invia a ognuno dei dischi una richiesta per 5 MB/s, ogni disco restituisce 5 MB/s, la macchina virtuale restituisce 15 MB/s all'applicazione.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

L'applicazione deve quindi elaborare un processo in batch che richiede 192 MB/s. 2 MB/s vengono usati dal disco del sistema operativo e il resto viene suddiviso in modo uniforme tra i dischi dati.

![L'applicazione invia una richiesta per 192 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale accetta la richiesta e invia la maggior parte della richiesta ai dischi dati (95 MB/s ciascuno) e 2 MB/s al disco del sistema operativo, i dischi dati vengono burst per soddisfare la richiesta e tutti i dischi restituiscono la velocità effettiva richiesta alla macchina virtuale, che la restituisce all'applicazione.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Macchina virtuale burstable con dischi non burstable
**Combinazione di vm e dischi:** 
- Standard_L8s_v2 
    - MB/s non memorizzati nella cache: 160
    - Max burst MB/s: 1.280
- Disco del sistema operativo P50
    - MB/s di cui è stato effettuato il provisioning: 250 
- 2 dischi dati P10 
    - MB/s di cui è stato effettuato il provisioning: 250

 Dopo l'avvio iniziale, un'applicazione viene eseguita nella macchina virtuale e ha un carico di lavoro non critico. Questo carico di lavoro richiede 30 MB/s che vengono distribuiti uniformemente tra tutti i dischi.
![L'applicazione invia una richiesta per 30 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale accetta la richiesta e invia a ognuno dei dischi una richiesta per 10 MB/s, ogni disco restituisce 10 MB/s, la macchina virtuale restituisce 30 MB/s all'applicazione.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

L'applicazione deve quindi elaborare un processo in batch che richiede 600 MB/s. Il Standard_L8s_v2 picchi per soddisfare questa domanda e quindi le richieste ai dischi vengono distribuite uniformemente ai dischi P50.

![L'applicazione invia una richiesta per 600 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale accetta burst per eseguire la richiesta e invia a ognuno dei dischi una richiesta per 200 MB/s, ogni disco restituisce 200 MB/s, i burst della macchina virtuale per restituire 600 MB/s all'applicazione.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Macchina virtuale con burst con dischi con burst
**Combinazione di vm e dischi:** 
- Standard_L8s_v2 
    - MB/s non memorizzati nella cache: 160
    - Max burst MB/s: 1.280
- Disco del sistema operativo P4
    - MB/s di cui è stato effettuato il provisioning: 25
    - Max burst MB/s: 170 
- 2 dischi dati P4 
    - MB/s di cui è stato effettuato il provisioning: 25
    - Max burst MB/s: 170 

All'avvio della macchina virtuale, verrà generato un burst per richiedere il limite di burst di 1.280 MB/s dal disco del sistema operativo e il disco del sistema operativo risponderà con prestazioni di burst di 170 MB/s.

![All'avvio, la macchina virtuale viene burst per inviare una richiesta di 1.280 MB/s al disco del sistema operativo, il disco del sistema operativo viene burst per restituire i 1.280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Dopo l'avvio, si avvia un'applicazione con un carico di lavoro non critico. Questa applicazione richiede 15 MB/s che vengono distribuiti uniformemente tra tutti i dischi.

![L'applicazione invia una richiesta per 15 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale accetta la richiesta e invia a ognuno dei dischi una richiesta per 5 MB/s, ogni disco restituisce 5 MB/s di risposte, la macchina virtuale restituisce 15 MB/s all'applicazione.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

L'applicazione deve quindi elaborare un processo in batch che richiede 360 MB/s. Il Standard_L8s_v2 burst per soddisfare questa richiesta e quindi le richieste. Solo 20 MB/s sono necessari per il disco del sistema operativo. I restanti 340 MB/s vengono gestiti dai dischi dati P4 in burst.

![L'applicazione invia una richiesta per 360 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale accetta burst per richiedere la richiesta e invia a ognuno dei dischi dati una richiesta per 170 MB/s e 20 MB/s dal disco del sistema operativo, ogni disco restituisce i MB/s richiesti, i burst della macchina virtuale per restituire 360 MB/s all'applicazione.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
