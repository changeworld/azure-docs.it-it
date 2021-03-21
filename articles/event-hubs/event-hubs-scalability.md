---
title: Scalabilità-Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come ridimensionare Hub eventi di Azure usando partizioni e unità di velocità effettiva.
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: f258ee2a3b4162dabf7a8e615db82b9b889d628b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601280"
---
# <a name="scaling-with-event-hubs"></a>Ridimensionamento con hub eventi

Esistono due fattori che influenzano la scalabilità con hub eventi.
*   Unità elaborate
*   Partizioni

## <a name="throughput-units"></a>Unità elaborate

La capacità di velocità effettiva di hub eventi è controllata dalle *unità di velocità effettiva*. Le unità elaborate sono unità di capacità pre-acquistate. Una singola velocità effettiva consente di:

* Dati in ingresso: fino a 1 MB al secondo o 1000 eventi al secondo, qualunque valore venga raggiunto per primo.
* Dati in uscita: fino a 2 MB al secondo o 4096 eventi al secondo.

Oltre la capacità delle unità elaborate acquistate, i dati in ingresso vengono limitati e viene restituito un valore [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). I dati in uscita non producono eccezioni di limitazione, ma sono ancora limitati alla capacità delle unità elaborate acquistate. Se si ricevono eccezioni di velocità di pubblicazione o sono previste uscite maggiori, controllare il numero di unità elaborate acquistate per lo spazio dei nomi. È possibile gestire le unità elaborate nel pannello **Ridimensionamento** dello spazio dei nomi nel [portale di Azure](https://portal.azure.com). È anche possibile gestire le unità elaborate a livello di programmazione usando le [API degli hub eventi](./event-hubs-samples.md).

Le unità sono pre-acquistate e vengono fatturate su base oraria. Una volta acquistate, le unità elaborate vengono fatturate per un minimo di un'ora. È possibile acquistare fino a 20 unità elaborate per uno spazio dei nomi di Hub eventi, che vengono condivise in tutti gli hub eventi nello spazio dei nomi.

La funzionalità **Aumento automatico** di Hub eventi aumenta automaticamente le prestazioni aumentando il numero di unità elaborate per soddisfare le esigenze di utilizzo. L'aumento delle unità elaborate previene scenari di limitazione in cui:

- Le velocità di ingresso dei dati superano le unità elaborate impostate.
- Le velocità di richiesta dei dati in uscita superano le unità elaborate impostate.

Il servizio Hub eventi aumenta la velocità effettiva quando il carico supera la soglia minima, senza che le richieste abbiano esito negativo con errori ServerBusy. 

Per altre informazioni sulla funzionalità di aumento automatico, vedere [ridimensionare automaticamente le unità di velocità effettiva](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partizioni
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

- [Ridimensionamento automatico delle unità elaborate](event-hubs-auto-inflate.md)
- [Panoramica del servizio Hub eventi](./event-hubs-about.md)
