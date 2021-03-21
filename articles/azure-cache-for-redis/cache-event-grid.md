---
title: Panoramica di griglia di eventi di cache di Azure per Redis
description: Usare griglia di eventi di Azure per pubblicare la cache di Azure per gli eventi Redis.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99056074"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Panoramica di griglia di eventi di cache di Azure per Redis 

Gli eventi di cache di Azure per Redis, ad esempio l'applicazione di patch, la scalabilità, l'importazione/esportazione (RDB), vengono inseriti tramite [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) per Sottoscrittori, ad esempio funzioni di Azure, app per la logica di Azure o anche il listener HTTP. Griglia di eventi consente il recapito di eventi affidabili alle applicazioni tramite criteri di ripetizione dei tentativi avanzati e messaggi non recapitabili.

Vedere l'articolo [relativo allo schema della cache di Azure per gli eventi Redis](../event-grid/event-schema-azure-cache.md) per visualizzare l'elenco completo degli eventi supportati da cache di Azure per Redis.

Se si vuole provare la cache di Azure per gli eventi Redis, vedere le guide introduttive seguenti:

|Se si desidera utilizzare questo strumento:    |Vedere questa Guida introduttiva: |
|--|-|
|Portale di Azure    |[Guida introduttiva: instradare la cache di Azure per gli eventi Redis all'endpoint Web con il portale di Azure](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Guida introduttiva: eseguire il routing della cache di Azure per gli eventi Redis all'endpoint Web con PowerShell](cache-event-grid-quickstart-powershell.md)|
|Interfaccia della riga di comando di Azure    |[Guida introduttiva: eseguire il routing di cache di Azure per eventi Redis all'endpoint Web con l'interfaccia della riga](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>Modello di eventi

Griglia di eventi usa le [sottoscrizioni](../event-grid/concepts.md#event-subscriptions) di eventi per indirizzare i messaggi di evento ai sottoscrittori. Questa immagine illustra la relazione tra autori di eventi, sottoscrizioni di eventi e gestori eventi.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Modello di griglia di eventi.":::

Prima di tutto, sottoscrivere un endpoint per un evento. Quindi, quando viene attivato un evento, il servizio griglia di eventi invierà i dati relativi all'evento all'endpoint.

Vedere l'articolo [relativo allo schema della cache di Azure per gli eventi Redis](../event-grid/event-schema-azure-cache.md) da visualizzare:

> [!div class="checklist"]
> * Un elenco completo di cache di Azure per gli eventi Redis e la modalità di attivazione di ogni evento.
> * Un esempio dei dati che la griglia di eventi invierà per ognuno di questi eventi.
> * Scopo di ogni coppia chiave-valore visualizzata nei dati.


## <a name="best-practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi

Le applicazioni che gestiscono gli eventi di cache di Azure per Redis devono seguire alcune procedure consigliate:
> [!div class="checklist"]
> * Poiché è possibile configurare più sottoscrizioni per indirizzare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi provengano da un'origine specifica, ma per controllare l'argomento del messaggio per assicurarsi che provenga dalla cache di Azure per l'istanza di redis prevista.
> * Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
> * Cache di Azure per gli eventi Redis garantisce il recapito at-least-once ai sottoscrittori, che garantisce che tutti i messaggi vengano restituiti. Tuttavia, a causa dei tentativi o della disponibilità delle sottoscrizioni, è possibile che si verifichino occasionalmente messaggi duplicati. Per ulteriori informazioni sul recapito dei messaggi e sui tentativi, vedere [recapito dei messaggi di griglia di eventi e riprovare](../event-grid/delivery-and-retry.md).


## <a name="next-steps"></a>Passaggi successivi

Scopri di più su griglia di eventi e assegna la cache di Azure per gli eventi Redis un tentativo:

- [Informazioni sulla griglia di eventi](../event-grid/overview.md)
- [Schema degli eventi di cache di Azure per Redis](../event-grid/event-schema-azure-cache.md)
- [Instradare la cache di Azure per eventi Redis all'endpoint Web con l'interfaccia della riga di comando](cache-event-grid-quickstart-cli.md)
- [Instradare la cache di Azure per gli eventi Redis all'endpoint Web con il portale di Azure](cache-event-grid-quickstart-portal.md)
- [Instradare la cache di Azure per gli eventi Redis all'endpoint Web con PowerShell](cache-event-grid-quickstart-powershell.md)
