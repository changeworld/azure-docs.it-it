---
title: Che cos'è l'infrastruttura BareMetal per Oracle?
description: Informazioni sulle funzionalità offerte dall'infrastruttura BareMetal per i carichi di lavoro Oracle.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: efb7506a0fa98ca93390bfe7d3cb1401cdbb77e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559158"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Che cos'è l'infrastruttura BareMetal per Oracle?

Questo articolo offre una panoramica delle funzionalità offerte dall'infrastruttura BareMetal per i carichi di lavoro Oracle.

L'infrastruttura BareMetal per Oracle è basata su Unified Computing System (UCS) e FLexPod certificati da Oracle. La piattaforma FlexPod offre tecnologie di archiviazione, rete e server pre-convalidate. Offre l'archiviazione NFS, fornendo l'integrazione tramite il protocollo DirectNFS. I server BareMetal sono dedicati all'utente, senza hypervisor nelle istanze BareMetal. 

Queste istanze sono per l'esecuzione di applicazioni cruciali che richiedono un carico di lavoro Oracle. Le istanze BareMetal forniscono una bassa latenza (0,35 ms) alle applicazioni in esecuzione in macchine virtuali di Azure. BareMetal fornisce un disco di archiviazione condiviso e supporta il multi casting necessario per la comunicazione da nodo a nodo con una rete di interconnessione privata dedicata. 

Altre funzionalità dell'infrastruttura BareMetal per Oracle includono:

- Blade UCS certificati Oracle - UCSB200-M5, UCSB460-M4, UCSB480-M5
- Comunicazione da nodo a nodo (multi cast) di Oracle Real Application Clusters (RAC) tramite la LAN virtuale privata (VLAN) -40 Gb.
- Hardware gestito da Microsoft
  - Archiviazione ridondante, rete, alimentazione, gestione
  - Monitoraggio dell'infrastruttura, delle riparazioni e della sostituzione
  - Include Azure ExpressRoute al controller di dominio del cliente
  - Sicurezza fisica e di rete protetta, in grado di accedere a tutti i servizi cloud di Azure

### <a name="supported-protocols"></a>Protocolli supportati

I protocolli seguenti vengono usati per diversi punti di montaggio all'interno dei server BareMetal per il carico di lavoro Oracle.

- Montaggio del sistema operativo : iSCSI
- Dati/log - NFSv3
- backup/archiviazione - NFSv4

### <a name="licensing"></a>Gestione delle licenze

- È possibile utilizzare il sistema operativo locale e le licenze Oracle.

### <a name="operating-system"></a>Sistema operativo

I server sono precaricati con il sistema operativo RHEL 7.6.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sugli SKU per i carichi di lavoro Oracle BareMetal.

> [!div class="nextstepaction"]
> [SKU BareMetal per carichi di lavoro Oracle](oracle-baremetal-skus.md)
