---
title: includere file
description: includere file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 02/12/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 54c29d76757916a8eea54af16babdae21b809a19
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750713"
---
## <a name="disk-level-bursting"></a>Espansione a livello di disco

### <a name="on-demand-bursting-preview"></a>Espansione su richiesta (anteprima)

I dischi che usano il modello di espansione su richiesta dello scaricamento del disco possono superare le destinazioni originali di cui è stato effettuato il provisioning, con la frequenza necessaria per il carico di lavoro, fino alla destinazione massima dei picchi. Ad esempio, in un disco P30 da 1 TiB, le operazioni di i/o al secondo con provisioning sono 5000 IOPS. Quando il disco è abilitato su questo disco, i carichi di lavoro possono emettere IOs su questo disco fino al numero massimo di operazioni di i/o a 30.000 IOPS e 1.000 MBps.

Se si prevede che i carichi di lavoro vengano eseguiti spesso oltre la destinazione delle prestazioni con provisioning, l'espansione del disco non sarà conveniente. In questo caso, è consigliabile modificare il livello di prestazioni del disco a un [livello superiore](../articles/virtual-machines/disks-performance-tiers.md) , per ottenere prestazioni di base migliori. Esaminare i dettagli di fatturazione e valutarli in base al modello di traffico dei carichi di lavoro.

Prima di abilitare l'espansione su richiesta, comprendere quanto segue:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Fatturazione

Ai dischi che usano il modello di espansione su richiesta viene addebitata una tariffa flat per l'abilitazione di una sequenza oraria e i costi delle transazioni si applicano a tutte le transazioni di espansione oltre la destinazione con provisioning Il costo delle transazioni viene addebitato usando il modello con pagamento in base al consumo, in base al disco non memorizzato nella cache, incluse le letture e le Scritture che superano le destinazioni di cui è stato effettuato il provisioning. Di seguito è riportato un esempio di modelli di traffico su disco in un'ora di fatturazione:

Configurazione del disco: SSD Premium-1 TiB (P30), l'espansione del disco è abilitata.

- 00:00:00 – 00:10:00 IOPS del disco inferiori al target di cui è stato effettuato il provisioning di 5.000 IOPS 
- 00:10:01 – 00:10:10 l'applicazione ha emesso un processo batch che causa il picchi di IOPS del disco a 6.000 IOPS per 10 secondi 
- 00:10:11 – 00:59:00 IOPS del disco inferiori al target di cui è stato effettuato il provisioning di 5.000 IOPS 
- 00:59:01 – 01:00:00 l'applicazione ha emesso un altro processo batch che causa il picchi di IOPS del disco a 7.000 IOPS per 60 secondi 

In questa ora di fatturazione il costo di espansione è costituito da due addebiti:

Il primo addebito è la tariffa flat per l'abilitazione dei picchi di $X (determinata dall'area geografica). Questa tariffa fissa viene sempre addebitata sulla disattivazione del disco dello stato di connessione fino a quando non viene disabilitata. 

Il secondo è il costo della transazione con picchi. Il disco si è verificato in due intervalli di tempo. Da 00:10:01 a 00:10:10, la transazione con accumulo cumulativo è (6.000 – 5.000) X 10 = 10.000. Da 00:59:01 a 01:00:00, la transazione con accumulo cumulativo è (7.000 – 5.000) X 60 = 120.000. Le transazioni di espansione totali sono 10.000 + 120.000 = 130.000. Il costo della transazione di espansione verrà addebitato a $Y in base a 13 unità di 10.000 transazioni (in base ai prezzi a livello di area).

In questo modo, il costo totale su disco con picchi di questa ora di fatturazione è uguale a $X + $Y. Lo stesso calcolo si applica per il provisioning della destinazione di MBps. L'eccedenza di MB viene convertita in transazioni con le dimensioni di i/o di 256 KB. Se il traffico su disco supera sia la destinazione IOPS che la destinazione MBps con provisioning, è possibile fare riferimento all'esempio seguente per calcolare le transazioni con picchi. 

Configurazione del disco: SSD Premium-1 TB (P30), l'espansione del disco è abilitata.

- 00:00:01 – 00:00:05 l'applicazione ha emesso un processo batch che causa il picchi di IOPS del disco a 10.000 IOPS e 300 MBps per cinque secondi.
- 00:00:06 – 00:00:10 l'applicazione ha emesso un processo di ripristino che causa l'aumento delle operazioni di i/o al secondo 6.000 IOPS e 600 MBps per cinque secondi.

La transazione di espansione viene considerata come il numero massimo di transazioni da IOPS o da espansione MBps. Da 00:00:01 a 00:00:05, la transazione con accumulo cumulativo è Max ((10.000-5.000), (300-200) * 1024/256)) * 5 = 25.000 transazioni. Da 00:00:06 a 00:00:10, la transazione con accumulo cumulativo è Max ((6.000-5.000), (600-200) * 1024/256)) * 5 = 8.000 transazioni. A tale scopo, includere la tariffa flat per l'abilitazione della funzione di espansione per ottenere il costo totale per l'abilitazione del disco su richiesta con picchi. 

Per informazioni dettagliate sui prezzi e per usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=storage) , è possibile fare riferimento alla pagina dei prezzi di [Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/) per eseguire la valutazione del carico di lavoro. 

### <a name="credit-based-bursting"></a>Picchi basati sul credito

Il prolungamento basato sul credito è disponibile per le dimensioni dei dischi P20 e più piccole in tutte le aree del cloud pubblico di Azure, Government e Cina. Per impostazione predefinita, l'espansione del disco è abilitata in tutte le distribuzioni nuove ed esistenti delle dimensioni dei dischi supportate. L'espansione a livello di macchina virtuale usa solo il picchi basato sul credito.

### <a name="virtual-machine-level-bursting"></a>Espansione a livello di macchina virtuale
Il supporto per l'espansione a livello di macchina virtuale è abilitato in tutte le aree del cloud pubblico su queste dimensioni supportate: 
- [Serie Lsv2](../articles/virtual-machines/lsv2-series.md)

L'espansione a livello di macchina virtuale è disponibile anche negli Stati Uniti centro-occidentali per le dimensioni supportate seguenti:
- [Serie Dv3 e Dsv3](../articles/virtual-machines/dv3-dsv3-series.md)
- [Serie Ev3 e Esv3](../articles/virtual-machines/ev3-esv3-series.md)

Per impostazione predefinita, l'espansione è abilitata per le macchine virtuali che lo supportano.

## <a name="bursting-flow"></a>Flusso di espansione

Il sistema di credito in espansione si applica allo stesso modo a livello di macchina virtuale e disco. La risorsa, ovvero una macchina virtuale o un disco, inizierà con crediti con scorte complete nel proprio bucket di espansione. Questi crediti consentono di ottenere fino a 30 minuti al massimo della velocità di espansione. Si accumulano crediti ogni volta che vengono usati i IOPS o i MB/s della risorsa sotto la destinazione di prestazioni della risorsa. Se la risorsa ha accumulato crediti di espansione e il carico di lavoro richiede le prestazioni aggiuntive, la risorsa può usare tali crediti per superare i limiti di prestazioni e migliorare le prestazioni per soddisfare le esigenze del carico di lavoro.

![Diagramma bucket di espansione.](media/managed-disks-bursting/bucket-diagram.jpg)

Il modo in cui si spendono i crediti disponibili dipende dall'utente. Puoi usare i tuoi 30 minuti di crediti di espansione consecutivi o sporadici nel corso della giornata. Quando le risorse vengono distribuite, presentano un'allocazione completa di crediti. Quando si esauriscono, la riconsegna richiede meno di un giorno. I crediti possono essere spesi a discrezione dell'utente, ma non è necessario che il bucket di espansione sia pieno per consentire l'espansione delle risorse. L'accumulo di picchi varia a seconda della risorsa, poiché si basa su IOPS inutilizzati e MB/s al di sotto dei rispettivi obiettivi di prestazioni. Le risorse di base per le prestazioni più elevate possono accumulare crediti di espansione più velocemente rispetto alle risorse di base più ridotte. Ad esempio, un disco P1 con minimo aumenterà 120 IOPS al secondo, mentre un disco P20 di minimo accumula 2.300 IOPS al secondo.

## <a name="bursting-states"></a>Stati con picchi
È possibile che la risorsa si trovi in tre stati con la funzionalità di espansione abilitata:
- **Accumulo** : il traffico di i/o della risorsa sta usando meno della destinazione delle prestazioni. L'accumulo di crediti di espansione per IOPS e MB/s viene eseguito separatamente gli uni dagli altri. La risorsa può accumulare crediti IOPS e spendere i crediti di MB/s o viceversa.
- Espansione **: il** traffico della risorsa sta usando più della destinazione delle prestazioni. Il traffico di espansione utilizzerà in modo indipendente i crediti da IOPS o larghezza di banda.
- **Costante** : il traffico della risorsa è esattamente in corrispondenza della destinazione delle prestazioni.

## <a name="bursting-examples"></a>Esempi di espansione

Gli esempi seguenti illustrano il funzionamento di espansione con varie combinazioni di VM e dischi. Per semplificare la procedura, gli esempi si concentrano su MB/s, ma la stessa logica viene applicata indipendentemente a IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Macchina virtuale non scaricabile con dischi a scaricamento
**Combinazione di VM e dischi:** 
- Standard_D8as_v4 
    - MB/s non memorizzati nella cache: 192
- Disco del sistema operativo P4
    - MB/s di cui è stato effettuato il provisioning: 25
    - Numero massimo di picchi MB/s: 170 
- 2 dischi dati P10 
    - MB/s di cui è stato effettuato il provisioning: 100
    - Numero massimo di picchi MB/s: 170

 Quando la macchina virtuale viene avviata, recupera i dati dal disco del sistema operativo. Poiché il disco del sistema operativo fa parte di una macchina virtuale avviata, il disco del sistema operativo sarà pieno di crediti di espansione. Questi crediti consentiranno al disco del sistema operativo di aumentare il suo avvio a 170 MB/s secondo.

![La macchina virtuale invia una richiesta per 192 MB/s di velocità effettiva al disco del sistema operativo. il disco del sistema operativo risponde con i dati di 170 MB/s.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

Al termine dell'avvio, un'applicazione viene quindi eseguita nella macchina virtuale e ha un carico di lavoro non critico. Questo carico di lavoro richiede 15 MB/S distribuiti in modo uniforme in tutti i dischi.

![L'applicazione invia una richiesta per 15 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale riceve una richiesta e invia ogni disco una richiesta per 5 MB/s, ogni disco restituisce 5 MB/s, la macchina virtuale restituisce 15 MB/s per l'applicazione.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Quindi, l'applicazione deve elaborare un processo in batch che richiede 192 MB/s. 2 MB/s vengono usati dal disco del sistema operativo e i restanti sono divisi uniformemente tra i dischi dati.

![L'applicazione invia la richiesta per 192 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale riceve una richiesta e Invia la maggior parte della richiesta ai dischi dati (95 MB/s ciascuno) e 2 MB/s al disco del sistema operativo, i dischi dati sono in grado di soddisfare la richiesta e tutti i dischi restituiscono la velocità effettiva richiesta alla VM, che la restituisce all'applicazione](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Macchina virtuale scaricabile con dischi non a scaricamento
**Combinazione di VM e dischi:** 
- Standard_L8s_v2 
    - MB/s non memorizzati nella cache: 160
    - Numero massimo di picchi MB/s: 1.280
- Disco del sistema operativo P50
    - MB/s di cui è stato effettuato il provisioning: 250 
- 2 dischi dati P10 
    - MB/s di cui è stato effettuato il provisioning: 250

 Dopo l'avvio iniziale, un'applicazione viene eseguita nella macchina virtuale e ha un carico di lavoro non critico. Questo carico di lavoro richiede 30 MB/s distribuiti in modo uniforme in tutti i dischi.
![L'applicazione invia una richiesta per 30 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale riceve una richiesta e invia ogni disco una richiesta per 10 MB/s, ogni disco restituisce 10 MB/s, la macchina virtuale restituisce 30 MB/s per l'applicazione.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Quindi, l'applicazione deve elaborare un processo in batch che richiede 600 MB/s. Il Standard_L8s_v2 è in grado di soddisfare questa richiesta e quindi le richieste ai dischi vengono distribuite in modo uniforme nei dischi P50.

![L'applicazione invia la richiesta per 600 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale impiega picchi per eseguire la richiesta e invia ogni disco una richiesta per 200 MB/s, ogni disco restituisce 200 MB/s, i picchi della macchina virtuale per restituire 600 MB/s per l'applicazione.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Macchina virtuale scaricabile con dischi a scaricamento
**Combinazione di VM e dischi:** 
- Standard_L8s_v2 
    - MB/s non memorizzati nella cache: 160
    - Numero massimo di picchi MB/s: 1.280
- Disco del sistema operativo P4
    - MB/s di cui è stato effettuato il provisioning: 25
    - Numero massimo di picchi MB/s: 170 
- 2 dischi dati P4 
    - MB/s di cui è stato effettuato il provisioning: 25
    - Numero massimo di picchi MB/s: 170 

Quando la macchina virtuale viene avviata, il numero di picchi di 1.280 MB/s verrà richiesto dal disco del sistema operativo e il disco del sistema operativo risponderà con le prestazioni di espansione di 170 MB/s.

![All'avvio, la macchina virtuale irrompe per inviare una richiesta di 1.280 MB/s al disco del sistema operativo, il disco del sistema operativo viene incrementato per restituire i 1.280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Al termine dell'avvio, un'applicazione con un carico di lavoro non critico viene avviata. Questa applicazione richiede 15 MB/s che vengono distribuiti in modo uniforme in tutti i dischi.

![L'applicazione invia una richiesta per 15 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale riceve una richiesta e invia ogni disco una richiesta per 5 MB/s, ogni disco restituisce 5 MB/s, la macchina virtuale restituisce 15 MB/s per l'applicazione.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Quindi, l'applicazione deve elaborare un processo in batch che richiede 360 MB/s. Il Standard_L8s_v2 si rompe per soddisfare questa richiesta e quindi le richieste. Solo 20 MB/s sono necessari per il disco del sistema operativo. I restanti 340 MB/s vengono gestiti dai dischi dati P4 con picchi.

![L'applicazione invia la richiesta per 360 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale accetta picchi per eseguire la richiesta e invia ogni disco dati a una richiesta per 170 MB/s e 20 MB/s dal disco del sistema operativo, ogni disco restituisce il MB/s richiesto, i picchi della macchina virtuale per restituire 360 MB/s per l'applicazione.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
