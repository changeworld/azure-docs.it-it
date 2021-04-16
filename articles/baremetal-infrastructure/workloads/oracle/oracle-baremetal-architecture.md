---
title: Architettura dell'infrastruttura BareMetal per Oracle
description: Informazioni sull'architettura di diverse configurazioni dell'infrastruttura BareMetal per Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 1bdc32c14cfc8986c52a4ea916a6130ee29e6028
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559173"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Architettura dell'infrastruttura BareMetal per Oracle

In questo articolo verranno trattate le opzioni di architettura per l'infrastruttura BareMetal per Oracle e le funzionalità supportate da ognuna.

## <a name="single-instance"></a>Istanza singola

Questa topologia supporta una singola istanza di Oracle Database con Oracle Data Guard per la migrazione all'infrastruttura BareMetal. Supporta l'uso del nodo standby per operazioni di manutenzione e disponibilità elevata.

[![Diagramma che illustra l'architettura di una singola istanza di Oracle Database con Oracle Data Guard.](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle Real Application Clusters (RAC) One Node

Questa topologia supporta una configurazione RAC con archiviazione condivisa e cluster GRID. Le istanze del database vengono eseguite solo in un nodo (configurazione attivo-passivo).

Funzionalità incluse:

- Attivo-passivo con Oracle RAC One Node

    - Failover automatico

    - Riavvio rapido nel secondo nodo

- Failover e scalabilità in tempo reale con Oracle RAC

- Zero tempi di inattività per la manutenzione in sequenza

[![Diagramma che illustra l'architettura di una configurazione attiva-passiva di Oracle RAC One Node.](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>Certificato per account con diritti

Questa topologia supporta una configurazione Oracle RAC con archiviazione condivisa e cluster Grid, mentre più istanze per database vengono eseguite contemporaneamente (configurazione attivo-attivo).

- Le prestazioni sono facili da ridimensionare tramite il provisioning online di server aggiunti. 
-  Gli utenti sono attivi in tutti i server e tutti i server condividono l'accesso allo stesso Oracle Database. 
-  Tutti i tipi di manutenzione del database possono essere eseguiti online o in sequenza per un tempo di inattività minimo o pari a zero. 
- I sistemi di standby Oracle Active Data Guard (ADG) possono essere facilmente utilizzati come sistemi di test per due scopi. 

Questa configurazione consente di testare tutte le modifiche in una copia esatta del database di produzione prima che siano applicate all'ambiente di produzione.

> [!NOTE]
> Se si intende usare Active Data Guard Far Sync (modalità sincrona), è necessario prendere in considerazione le zone regionali in cui questa funzionalità è supportata. Solo per le aree geografiche distribuite, è consigliabile usare Data Guard con la modalità asincrona.

[![Diagramma che illustra l'architettura di una configurazione attiva-attiva di Oracle RAC.](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul provisioning delle istanze BareMetal per carichi di lavoro Oracle.

> [!div class="nextstepaction"]
> [Effettuare il provisioning dell'infrastruttura BareMetal per Oracle](oracle-baremetal-provision.md)

