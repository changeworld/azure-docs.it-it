---
title: Manutenzione pianificata-database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: Questo articolo descrive la funzionalità di manutenzione pianificata in database di Azure per PostgreSQL-iperscalabilità (CITUS).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027603"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Manutenzione pianificata nel database di Azure per PostgreSQL: iperscalabilità (CITUS)

Database di Azure per PostgreSQL-iperscalabilità (CITUS) esegue una manutenzione periodica per mantenere il database gestito sicuro, stabile e aggiornato.  Durante la manutenzione, tutti i nodi del gruppo di server ottengono nuove funzionalità, aggiornamenti e patch.

Le funzionalità principali di manutenzione pianificata per iperscalabilità (CITUS) sono:

* Gli aggiornamenti vengono applicati contemporaneamente in tutti i nodi del gruppo di server
* Le notifiche relative alla manutenzione imminente vengono pubblicate in integrità dei servizi di Azure cinque giorni prima
* In genere ci sono almeno 30 giorni tra gli eventi di manutenzione riusciti per un gruppo di server

## <a name="notification-about-upcoming-maintenance"></a>Notifica sulla manutenzione imminente

Le notifiche relative alla manutenzione pianificata imminente vengono pubblicate in integrità dei servizi di Azure e possono essere:

* Inviato tramite posta elettronica a un indirizzo specifico
* Inviato tramite posta elettronica a un ruolo Azure Resource Manager
* Inviato in un messaggio di testo (SMS) ai dispositivi mobili
* un push di notifica a un'app di Azure
* un messaggio vocale

> [!IMPORTANT]
> Normalmente ci sono almeno 30 giorni tra gli eventi di manutenzione pianificata riusciti per un gruppo di server.
>
> Tuttavia, nel caso di un aggiornamento critico di emergenza, ad esempio una vulnerabilità grave, la finestra di notifica potrebbe essere inferiore a cinque giorni. È possibile applicare l'aggiornamento critico al server anche se è stata eseguita correttamente una manutenzione pianificata negli ultimi 30 giorni.

Se la manutenzione non riesce o viene annullata, il sistema creerà una notifica.
Riproverà a eseguire la manutenzione in base alle impostazioni di pianificazione correnti e invierà una notifica a cinque giorni prima dell'evento di manutenzione successivo.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ricevere notifiche sulla manutenzione futura](../service-health/service-notifications.md) con l'integrità dei servizi di Azure
* Informazioni su come [configurare gli avvisi relativi agli eventi di manutenzione pianificata imminente](../service-health/resource-health-alert-monitor-guide.md)
