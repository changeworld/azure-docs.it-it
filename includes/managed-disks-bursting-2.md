---
title: includere file
description: includere file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 801f0f03b49d20c84a4531bd0daad7630a0ed01d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585110"
---
## <a name="common-scenarios"></a>Scenari comuni
Gli scenari seguenti possono trarre molto vantaggio dall'espansione:
- **Miglioramento dei tempi di avvio**  : con il potenziamento, l'istanza di viene avviata con una velocità notevolmente più veloce. Ad esempio, il disco del sistema operativo predefinito per le macchine virtuali abilitate per Premium è il disco P4, ovvero le prestazioni con provisioning fino a 120 IOPS e 25 MB/s. Con l'espansione, il P4 può raggiungere 3500 IOPS e 170 MB/s, consentendo un tempo di avvio per l'accelerazione di 6X.
- **Gestione dei processi batch** : alcuni carichi di lavoro di un'applicazione sono di natura ciclica e richiedono prestazioni di base per la maggior parte del tempo e richiedono prestazioni più elevate per un breve periodo di tempo. Un esempio è un programma di contabilità che elabora transazioni giornaliere che richiedono una piccola quantità di traffico su disco. Quindi, alla fine del mese, la riconciliazione dei report che necessitano di una quantità molto maggiore di traffico su disco.
- **Preparazione per i picchi di traffico** : i server Web e le relative applicazioni possono riscontrare picchi di traffico in qualsiasi momento. Se il server Web è supportato da macchine virtuali o dischi con picchi, i server sono più attrezzati per gestire i picchi di traffico. 

## <a name="bursting-flow"></a>Flusso di espansione
Il sistema di credito in espansione si applica allo stesso modo a livello di macchina virtuale e disco. La risorsa, ovvero una macchina virtuale o un disco, inizierà con crediti completamente disponibili. Questi crediti consentiranno di aumentare di 30 minuti al massimo della velocità di espansione. I crediti di espansione si accumulano quando la risorsa è in esecuzione con i limiti di archiviazione su disco delle prestazioni. Per tutte le operazioni di i/o al secondo e i MB/s usati dalla risorsa al di sotto del limite delle prestazioni, si inizia ad accumulare crediti. Se la risorsa ha accumulato crediti da usare per l'espansione e il carico di lavoro richiede le prestazioni aggiuntive, la risorsa può usare tali crediti per superare il limite di prestazioni per fornire le prestazioni di i/o su disco necessarie per soddisfare la domanda.



![Diagramma di bucket in sequenza](media/managed-disks-bursting/bucket-diagram.jpg)

Il modo in cui si vogliono usare i 30 minuti di espansione è completamente attivo. È possibile usarlo per 30 minuti consecutivamente o sporadicamente nel corso della giornata. Quando il prodotto viene distribuito, è pronto con crediti completi e, quando esaurisce i crediti, è necessario meno di un giorno per ottenere una disponibilità completa dei crediti. È possibile accumulare e spendere i crediti di espansione a propria discrezione e non è necessario che il bucket di 30 minuti venga nuovamente esaurito. Un aspetto da considerare sull'accumulo di picchi è che è diverso per ogni risorsa, perché si basa sulle operazioni di i/o al secondo e di MB/s al di sotto degli importi delle prestazioni. Ciò significa che i prodotti per le prestazioni di base più elevati possono accumulare i propri importi di espansione più velocemente rispetto ai prodotti di base più bassi. Ad esempio, un disco P1 di minimo senza attività accumulerà 120 IOPS al secondo, mentre un disco P20 accumula 2.300 IOPS al secondo mentre è inattivo senza attività.

## <a name="bursting-states"></a>Stati con picchi
È possibile che la risorsa si trovi in tre stati con la funzionalità di espansione abilitata:
- **Accumulo** : il traffico di i/o della risorsa sta usando meno della destinazione delle prestazioni. L'accumulo di crediti di espansione per IOPS e MB/s viene eseguito separatamente gli uni dagli altri. La risorsa può accumulare crediti IOPS e spendere i crediti di MB/s o viceversa.
- Espansione **: il** traffico della risorsa sta usando più della destinazione delle prestazioni. Il traffico di espansione utilizzerà in modo indipendente i crediti da IOPS o larghezza di banda.
- **Costante** : il traffico della risorsa è esattamente in corrispondenza della destinazione delle prestazioni.

## <a name="examples-of-bursting"></a>Esempi di picchi

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

![La macchina virtuale invia una richiesta per 192 MB/s di velocità effettiva al disco del sistema operativo. il disco del sistema operativo risponde con i dati di 170 MB/s.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

Al termine dell'avvio, un'applicazione viene quindi eseguita nella macchina virtuale e ha un carico di lavoro non critico. Questo carico di lavoro richiede 15 MB/S distribuiti in modo uniforme in tutti i dischi.

![L'applicazione invia una richiesta per 15 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale riceve una richiesta e invia ogni disco una richiesta per 5 MB/s, ogni disco restituisce 5 MB/s, la macchina virtuale restituisce 15 MB/s per l'applicazione.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

Quindi, l'applicazione deve elaborare un processo in batch che richiede 192 MB/s. 2 MB/s vengono usati dal disco del sistema operativo e i restanti sono divisi uniformemente tra i dischi dati.

![L'applicazione invia la richiesta per 192 MB/s di velocità effettiva alla macchina virtuale, la macchina virtuale riceve una richiesta e Invia la maggior parte della richiesta ai dischi dati (95 MB/s ciascuno) e 2 MB/s al disco del sistema operativo, i dischi dati sono in grado di soddisfare la richiesta e tutti i dischi restituiscono la velocità effettiva richiesta alla VM, che la restituisce all'applicazione](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

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