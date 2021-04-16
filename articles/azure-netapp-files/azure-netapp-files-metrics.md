---
title: Metriche per Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files metriche sull'archiviazione allocata, sull'utilizzo effettivo dello spazio di archiviazione, sulle operazioni di I/O al secondo del volume e sulla latenza. Usare queste metriche per comprendere l'utilizzo e le prestazioni.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: b-juche
ms.openlocfilehash: b581470a886ff73739edfee7f45c64295eeeb1f0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388610"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriche per Azure NetApp Files

Azure NetApp Files metriche sull'archiviazione allocata, sull'utilizzo effettivo dello spazio di archiviazione, sulle operazioni di I/O al secondo del volume e sulla latenza. L'analisi di queste metriche consente di comprendere meglio il modello di utilizzo e le prestazioni del volume degli account NetApp.  

È possibile trovare le metriche per un pool di capacità o un volume selezionando il **pool di capacità o** il **volume**.  Fare quindi **clic su Metrica** per visualizzare le metriche disponibili: 

[![Snapshot che illustra come passare all'elenco a discesa Metrica. ](../media/azure-netapp-files/metrics-navigate-volume.png)](../media/azure-netapp-files/metrics-navigate-volume.png#lightbox)

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metriche di utilizzo per i pool di capacità

- *Dimensioni allocate del pool*   
    Dimensioni di cui è stato effettuato il provisioning del pool.

- *Pool allocato alle dimensioni del volume*  
    Totale della quota di volume (GiB) in un determinato pool di capacità, ovvero il totale delle dimensioni di cui è stato effettuato il provisioning dei volumi nel pool di capacità.  
    Queste dimensioni sono le dimensioni selezionate durante la creazione del volume.  

- *Dimensioni utilizzate del pool*  
    Totale dello spazio logico (GiB) usato tra i volumi in un pool di capacità.  

- *Dimensioni totali snapshot per il pool*    
    Somma delle dimensioni dello snapshot da tutti i volumi nel pool.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metriche di utilizzo per i volumi

- *Percentuale volume consumato dimensioni*    
    Percentuale del volume utilizzato, inclusi gli snapshot.  
- *Dimensioni allocate del volume*   
    Dimensioni di cui è stato effettuato il provisioning di un volume
- *Dimensioni della quota del volume*    
    Dimensioni della quota (GiB) con cui viene effettuato il provisioning del volume.   
- *Dimensioni utilizzate del volume*   
    Dimensioni logiche del volume (byte usati).  
    Queste dimensioni includono lo spazio logico usato dai file system e gli snapshot attivi.  
- *Dimensioni snapshot del volume*   
   Dimensioni di tutti gli snapshot in un volume.  

## <a name="performance-metrics-for-volumes"></a>Metriche delle prestazioni per i volumi

- *Latenza media di lettura*   
    Tempo medio in millisecondi per le operazioni di lettura dal volume.
- *Latenza media scrittura*   
    Tempo medio in millisecondi per le scritture dal volume.
- *Operazioni di I/O al secondo di lettura*   
    Numero di operazioni di lettura nel volume al secondo.
- *Operazioni di I/O al secondo di scrittura*   
    Numero di scritture nel volume al secondo.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Metriche di replica dei volumi

> [!NOTE] 
> * Le dimensioni del trasferimento di rete (ad esempio, *le* metriche di trasferimento totale della replica del volume) potrebbero differire dai volumi di origine o di destinazione di una replica tra aree. Questo comportamento è il risultato dell'uso efficiente del motore di replica per ridurre al minimo i costi di trasferimento della rete.
> * Le metriche di replica dei volumi vengono attualmente popolate per i volumi di destinazione della replica e non per l'origine della relazione di replica.

- *Lo stato della replica del volume è integro*   
    Condizione della relazione di replica. Uno stato integro è denotato da `1` . Uno stato non integro viene denotato da `0` .

- *È in corso il trasferimento della replica del volume*    
    Indica se lo stato della replica del volume è "trasferimento in corso". 
 
- *Tempo di ritardo della replica del volume*   
    Intervallo di tempo in secondi in base al quale i dati nel mirror sono in ritardo rispetto all'origine. 

- *Durata dell'ultimo trasferimento della replica del volume*   
    Quantità di tempo in secondi impiegato per il completamento dell'ultimo trasferimento. 

- *Dimensioni dell'ultimo trasferimento della replica del volume*    
    Numero totale di byte trasferiti come parte dell'ultimo trasferimento. 

- *Stato della replica del volume*    
    Quantità totale di dati trasferiti per l'operazione di trasferimento corrente. 

- *Trasferimento totale della replica del volume*   
    Byte cumulativi trasferiti per la relazione. 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
* [Creare un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md)
