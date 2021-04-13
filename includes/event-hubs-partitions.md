---
title: includere file
description: includere file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1dd78ba3799573e05e4ebbf55887bae3d9674b7c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310079"
---
Hub eventi organizza le sequenze di eventi inviati a un hub eventi in una o più partizioni. Man mano che arrivano, i nuovi eventi vengono aggiunti alla fine di questa sequenza. 

![Hub eventi](./media/event-hubs-partitions/multiple-partitions.png)

Una partizione può essere considerata come un "log di commit". Le partizioni contengono i dati degli eventi che contengono il corpo dell'evento, un contenitore di proprietà definito dall'utente che descrive l'evento, i metadati, ad esempio l'offset nella partizione, il numero nella sequenza di flusso e il timestamp sul lato servizio in cui è stato accettato.

![Diagramma che visualizza la sequenza di eventi dai meno recenti ai più recenti.](./media/event-hubs-partitions/partition.png)

### <a name="advantages-of-using-partitions"></a>Vantaggi dell'utilizzo di partizioni
Hub eventi è progettato per semplificare l'elaborazione di volumi elevati di eventi e il partizionamento contribuisce a questo scopo in due modi:

- Anche se Hub eventi è un servizio PaaS, esiste una realtà fisica sottostante e la gestione di un log che mantiene l'ordine degli eventi richiede che questi eventi vengano mantenuti insieme nell'archiviazione sottostante e nelle relative repliche e che comportano un limite di velocità effettiva per tale log. Il partizionamento consente di usare più log paralleli per lo stesso hub eventi, moltiplicando quindi la capacità di velocità effettiva di i/o non elaborata disponibile.
- Le applicazioni devono essere in grado di soddisfare l'elaborazione del volume degli eventi inviati a un hub eventi. Questa operazione può essere complessa e richiede una capacità di elaborazione parallela sostanziale e scale-out. La capacità di un singolo processo di gestire gli eventi è limitata, quindi sono necessari diversi processi. Le partizioni rappresentano il modo in cui la soluzione inserisce tali processi, garantendo tuttavia che ogni evento disponga di un proprietario di elaborazione chiaro. 

### <a name="number-of-partitions"></a>Numero di partizioni
In Hub eventi Standard il numero di partizioni viene specificato in fase di creazione e deve essere compreso tra 1 e 32. In Hub eventi Dedicato il numero di partizioni può essere composto da un massimo di 2000 partizioni per unità di capacità. 

È consigliabile scegliere almeno il numero di partizioni che si prevede siano necessarie in [unità elaborate](../articles/event-hubs/event-hubs-faq.yml#what-are-event-hubs-throughput-units-) sostenute durante il picco del carico dell'applicazione per uno specifico hub eventi. È necessario eseguire il calcolo con una singola partizione con una capacità di unità elaborate pari a 1 (1 MByte in ingresso, 2 MByte in uscita). È possibile dimensionare le unità elaborate nello spazio dei nomi o le unità di capacità del cluster indipendentemente dal numero di partizioni. Un hub eventi con 32 partizioni e uno con una partizione comportano esattamente lo stesso costo se lo spazio dei nomi è impostato su una capacità di 1 unità elaborata. 

Il numero di partizioni relativo a un hub eventi in un [cluster di Hub eventi dedicato](../articles/event-hubs/event-hubs-dedicated-overview.md) può essere [aumentato](../articles/event-hubs/dynamically-add-partitions.md) dopo la creazione dell'hub eventi, ma in tal caso la distribuzione dei flussi tra le partizioni cambierà perché il mapping delle chiavi di partizione alle partizioni cambia, di conseguenza è consigliabile provare a evitare tali modifiche se l'ordine relativo degli eventi è importante nell'applicazione.

Si potrebbe essere tentati di impostare il numero di partizioni sul valore massimo consentito, ma tenere sempre presente che i flussi di eventi devono essere strutturati in modo da consentire di usufruire di più partizioni. Se è necessario preservare l'ordine assoluto in tutti gli eventi o solo in pochi sottoflussi, è possibile che non si riesca a sfruttare molte partizioni. Inoltre, molte partizioni rendono l'elaborazione più complessa. 


### <a name="mapping-of-events-to-partitions"></a>Mapping degli eventi alle partizioni
È possibile usare una chiave di partizione per mappare i dati dell'evento in ingresso in partizioni specifiche ai fini dell'organizzazione dei dati. La chiave di partizione è un valore fornito dal mittente che viene passato a un hub eventi. Viene elaborato tramite una funzione di hashing statica, che crea l'assegnazione di partizione. Se non si specifica una chiave di partizione quando si pubblica un evento, viene usata un'assegnazione round robin.

L'autore di eventi è a conoscenza solo della chiave di partizione, non la partizione in cui gli eventi vengono pubblicati. Questa separazione tra chiave e partizione evita che il mittente debba conoscere troppe informazioni sull'elaborazione downstream. Un’identità univoca per dispositivo o utente crea una chiave di partizione efficace, ma è possibile utilizzare anche altri attributi, ad esempio l’area geografica, per raggruppare gli eventi correlati in un'unica partizione.

La specifica di una chiave di partizione consente di mantenere insieme gli eventi correlati nella stessa partizione e nell'ordine esatto in cui sono stati inviati. La chiave di partizione è una stringa derivata dal contesto dell'applicazione che identifica l'interrelazione degli eventi. Una sequenza di eventi identificata da una chiave di partizione è un *flusso*. Una partizione è un archivio di log multiplex per molti flussi di questo tipo. 

> [!NOTE]
> Sebbene sia possibile inviare eventi direttamente alle partizioni, non è consigliabile, soprattutto quando la disponibilità elevata è importante. Esegue il downgrade della disponibilità di un hub eventi a livello di partizione. Per altre informazioni, vedere [disponibilità e coerenza](../articles/event-hubs/event-hubs-availability-and-consistency.md).

