---
title: Bus di servizio di Azure come origine di griglia di eventi
description: Descrive le proprietà disponibili per gli eventi del bus di servizio con Griglia di eventi di Azure
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: d3e14788d49697a1f86624bbe8d6d0ec2eb072c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363245"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Bus di servizio di Azure come origine di griglia di eventi

Questo articolo illustra le proprietà e lo schema per gli eventi del bus di servizio. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="tutorials-and-how-tos"></a>Esercitazioni e procedure
|Titolo  |Descrizione  |
|---------|---------|
| [Esercitazione: Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Griglia di eventi invia i messaggi dall'argomento del bus di servizio all'app per le funzioni e all'app per la logica. |
| [Integrazione tra il bus di servizio di Azure e griglia di eventi](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Panoramica dell'integrazione del bus di servizio con Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per maggiori dettagli sull'uso di Griglia di eventi di Azure con il bus di servizio, vedere [Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Provare a [ricevere gli eventi del bus di servizio con Funzioni o App per la logica](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
