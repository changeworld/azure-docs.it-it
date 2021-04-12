---
title: Manutenzione pianificata-database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: Questo articolo descrive la funzionalità di manutenzione pianificata in database di Azure per PostgreSQL-iperscalabilità (CITUS).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: dee7257a296f523dbc9040d65fe68c14edf928f6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106786"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Manutenzione pianificata nel database di Azure per PostgreSQL: iperscalabilità (CITUS)

Database di Azure per PostgreSQL-iperscalabilità (CITUS) esegue una manutenzione periodica per mantenere il database gestito sicuro, stabile e aggiornato.  Durante la manutenzione, tutti i nodi del gruppo di server ottengono nuove funzionalità, aggiornamenti e patch.

Le funzionalità principali di manutenzione pianificata per iperscalabilità (CITUS) sono:

* Gli aggiornamenti vengono applicati contemporaneamente in tutti i nodi del gruppo di server
* Le notifiche relative alla manutenzione imminente vengono pubblicate in integrità dei servizi di Azure cinque giorni prima
* In genere ci sono almeno 30 giorni tra gli eventi di manutenzione riusciti per un gruppo di server
* Il giorno della settimana e l'intervallo di tempo preferiti in tale giorno per l'avvio della manutenzione possono essere definiti singolarmente per ogni gruppo di server

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>Selezione di una finestra di manutenzione e notifica sulla manutenzione imminente

È possibile pianificare la manutenzione in un determinato giorno della settimana e in un intervallo di tempo in tale giorno. In alternativa, è possibile consentire al sistema di selezionare automaticamente un giorno e un intervallo di tempo. In entrambi i casi, il sistema segnalerà cinque giorni prima di eseguire qualsiasi operazione di manutenzione. Il sistema informa anche quando viene avviata la manutenzione e quando viene completata correttamente.

Le notifiche relative alla manutenzione pianificata imminente vengono pubblicate in integrità dei servizi di Azure e possono essere:

* Inviato tramite posta elettronica a un indirizzo specifico
* Inviato tramite posta elettronica a un ruolo Azure Resource Manager
* Inviato in un messaggio di testo (SMS) ai dispositivi mobili
* un push di notifica a un'app di Azure
* un messaggio vocale

Quando si specificano le preferenze per il programma di manutenzione, è possibile scegliere un giorno della settimana e un intervallo di tempo. Se non si specifica, il sistema selezionerà i tempi tra le 23:00 e le 07:00 nell'ora dell'area del gruppo di server. È possibile definire pianificazioni diverse per ogni gruppo di server CITUS (overscale) nella sottoscrizione di Azure.

> [!IMPORTANT]
> Normalmente ci sono almeno 30 giorni tra gli eventi di manutenzione pianificata riusciti per un gruppo di server.
>
> Tuttavia, nel caso di un aggiornamento critico di emergenza, ad esempio una vulnerabilità grave, la finestra di notifica potrebbe essere inferiore a cinque giorni. È possibile applicare l'aggiornamento critico al server anche se è stata eseguita correttamente una manutenzione pianificata negli ultimi 30 giorni.

È possibile aggiornare le impostazioni di pianificazione in qualsiasi momento. Se è stata pianificata la manutenzione per il gruppo di server di iperscalabilità (CITUS) e si aggiorna la pianificazione, gli eventi esistenti continueranno a essere pianificati in origine. Le modifiche apportate alle impostazioni diverranno effettive dopo il completamento degli eventi esistenti.

Se la manutenzione non riesce o viene annullata, il sistema creerà una notifica.
Riproverà a eseguire la manutenzione in base alle impostazioni di pianificazione correnti e invierà una notifica a cinque giorni prima dell'evento di manutenzione successivo.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [modificare la pianificazione di manutenzione](howto-hyperscale-maintenance.md)
* Informazioni su come [ricevere notifiche sulla manutenzione futura](../service-health/service-notifications.md) con l'integrità dei servizi di Azure
* Informazioni su come [configurare gli avvisi relativi agli eventi di manutenzione pianificata imminente](../service-health/resource-health-alert-monitor-guide.md)
