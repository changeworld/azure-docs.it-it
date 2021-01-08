---
title: includere file
description: includere file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 780da47e6f071d854a16ca1d1c5cd02dbdd6bef0
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955646"
---
Hub eventi organizza le sequenze di eventi in una o più partizioni. Man mano che arrivano, i nuovi eventi vengono aggiunti alla fine di questa sequenza. Una partizione può essere considerata come "registro commit".

Le partizioni contengono dati dell'evento come il corpo dell'evento, un contenitore di proprietà definite dall'utente con la descrizione dell'evento e i metadati, ad esempio l'offset nella partizione, il relativo numero nella sequenza di flusso e il timestamp lato servizio in cui è stato accettato.

![Diagramma che visualizza la sequenza di eventi dai meno recenti ai più recenti.](./media/event-hubs-partitions/partition.png)

Hub eventi è progettato per semplificare l'elaborazione di volumi elevati di eventi e il partizionamento contribuisce a questo scopo in due modi:

Per prima cosa, anche se Hub eventi è un servizio PaaS, esiste una realtà fisica sottostante e per mantenere un log che preservi l'ordine degli eventi è necessario che questi eventi vengano tenuti insieme nell'archiviazione sottostante e nelle relative repliche e che il risultato sia un limite di velocità effettiva per tale log. Il partizionamento consente di usare più log paralleli per lo stesso hub eventi, moltiplicando così la capacità di velocità effettiva di I/O non elaborata disponibile.

In secondo luogo, le applicazioni devono essere in grado di tenere il passo con l'elaborazione del volume di eventi inviati a un hub eventi. Questa operazione può essere complessa e richiede una capacità di elaborazione parallela sostanziale e scale-out. La logica per le partizioni è uguale a quella descritta sopra: la capacità di un singolo processo di gestire gli eventi è limitata, pertanto sono necessari diversi processi e le partizioni rappresentano il modo in cui la soluzione invia tali processi, garantendo tuttavia che ogni evento abbia un proprietario di elaborazione ben definito. 

Hub eventi mantiene gli eventi per un periodo di conservazione configurato che viene applicato a tutte le partizioni. Gli eventi vengono rimossi automaticamente al raggiungimento del periodo di conservazione. Se si specifica un periodo di conservazione di un giorno, l'evento diventerà non disponibile esattamente 24 ore dopo che è stato accettato. Non è possibile eliminare in modo esplicito gli eventi. 

Il periodo di conservazione massimo consentito è di 7 giorni per Hub eventi Standard e di 90 giorni per Hub eventi Dedicato. Se è necessario archiviare gli eventi oltre il periodo di conservazione consentito, è possibile [memorizzarli automaticamente in Archiviazione di Azure o in Azure Data Lake attivando la funzionalità di acquisizione di hub eventi](../articles/event-hubs/event-hubs-capture-overview.md) e, se è necessario eseguire ricerche o analisi in tali archivi profondi, è possibile [importarli facilmente in Azure Synapse](../articles/event-hubs/store-captured-data-data-warehouse.md) o in altri archivi e piattaforme di analisi simili. 

Il motivo del limite temporale di Hub eventi per la conservazione dei dati è di impedire che volumi elevati di dati cronologici dei clienti vengano intrappolati in un archivio profondo che viene indicizzato solo in base a timestamp e consente solo l'accesso sequenziale. La filosofia alla base dell'architettura è che i dati cronologici richiedono un'indicizzazione più avanzata e un accesso più diretto rispetto all'interfaccia eventi in tempo reale fornita da Hub eventi o Kafka. I motori dei flussi di eventi non sono particolarmente adatti per l'uso come data lake o come archivi a lungo termine per l'origine eventi. 

Poiché le partizioni sono indipendenti e contengono una sequenza specifica di dati, presentano spesso velocità di crescita diverse. In Hub eventi, questo non è un problema che richiede un intervento amministrativo come sarebbe, ad esempio, in Apache Kafka, ma la distribuzione non uniforme comporterà un carico non uniforme sui processori di eventi downstream.

![Hub eventi](./media/event-hubs-partitions/multiple-partitions.png)

In Hub eventi Standard il numero di partizioni viene specificato in fase di creazione e deve essere compreso tra 1 e 32. In Hub eventi Dedicato il numero di partizioni può essere composto da un massimo di 2000 partizioni per unità di capacità. 

È consigliabile scegliere almeno il numero di partizioni che si prevede siano necessarie in [unità elaborate](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) sostenute durante il picco del carico dell'applicazione per uno specifico hub eventi. È necessario eseguire il calcolo con una singola partizione con una capacità di unità elaborate pari a 1 (1 MByte in ingresso, 2 MByte in uscita). È possibile dimensionare le unità elaborate nello spazio dei nomi o le unità di capacità del cluster indipendentemente dal numero di partizioni. Un hub eventi con 32 partizioni e uno con una partizione comportano esattamente lo stesso costo se lo spazio dei nomi è impostato su una capacità di 1 unità elaborata. 

Le applicazioni controllano il mapping degli eventi alle partizioni in uno dei tre modi seguenti:

- Specificando la chiave di partizione, mappata in modo coerente (usando una funzione hash) a una delle partizioni disponibili. 
- Non specificando una chiave di partizione, il che consente al broker di scegliere in modo casuale una partizione per un determinato evento.
- Inviando esplicitamente gli eventi a una partizione specifica.

La specifica di una chiave di partizione consente di mantenere insieme gli eventi correlati nella stessa partizione e nell'ordine esatto in cui sono stati inviati. La chiave di partizione è una stringa derivata dal contesto dell'applicazione che identifica l'interrelazione degli eventi.

Una sequenza di eventi identificata da una chiave di partizione è un *flusso*. Una partizione è un archivio di log multiplex per molti flussi di questo tipo. 

Il numero di partizioni relativo a un hub eventi in un [cluster di Hub eventi dedicato](../articles/event-hubs/event-hubs-dedicated-overview.md) può essere [aumentato](../articles/event-hubs/dynamically-add-partitions.md) dopo la creazione dell'hub eventi, ma in tal caso la distribuzione dei flussi tra le partizioni cambierà perché il mapping delle chiavi di partizione alle partizioni cambia, di conseguenza è consigliabile provare a evitare tali modifiche se l'ordine relativo degli eventi è importante nell'applicazione.

Si potrebbe essere tentati di impostare il numero di partizioni sul valore massimo consentito, ma tenere sempre presente che i flussi di eventi devono essere strutturati in modo da consentire di usufruire di più partizioni. Se è necessario preservare l'ordine assoluto in tutti gli eventi o solo in pochi sottoflussi, è possibile che non si riesca a sfruttare molte partizioni. Inoltre, molte partizioni rendono l'elaborazione più complessa. 

Anche se è possibile, non è consigliabile inviare eventi direttamente alle partizioni. È invece possibile usare i costrutti più generici introdotti nella sezione [Autori di eventi](../articles/event-hubs/event-hubs-features.md#event-publishers). 

Per altre informazioni sulle partizioni e il necessario equilibrio tra disponibilità e affidabilità, vedere la [Guida alla programmazione di Hub eventi](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e l'articolo [Disponibilità e coerenza nell'Hub eventi](../articles/event-hubs/event-hubs-availability-and-consistency.md).
