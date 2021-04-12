---
title: Database di Azure per PostgreSQL-iperscalabilità (CITUS)-manutenzione pianificata-portale di Azure
description: Informazioni su come configurare le impostazioni di manutenzione pianificata per un database di Azure per PostgreSQL-overscale (CITUS) dal portale di Azure.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 04dd37e3e9abbfbec7badb036802e645cc7732b0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108372"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>Gestire le impostazioni di manutenzione pianificata per database di Azure per PostgreSQL – iperscalabilità (CITUS)

È possibile specificare le opzioni di manutenzione per ogni gruppo di server con iperscalabilità (CITUS) nella sottoscrizione di Azure. Le opzioni includono la pianificazione di manutenzione e le impostazioni di notifica per gli eventi di manutenzione imminenti e completati.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Un [gruppo di server di database di Azure per PostgreSQL-iperscala (CITUS)](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>Specificare le opzioni di pianificazione di manutenzione

1. Nella pagina gruppo di server iperscalabile (CITUS), sotto l'intestazione **Impostazioni** , scegliere **manutenzione** per aprire le opzioni di manutenzione pianificata.
2. La pianificazione predefinita (gestita dal sistema) è un giorno casuale della settimana e la finestra di 30 minuti per la manutenzione inizia tra le 23:00 e l'ora dell' [area di Azure](https://go.microsoft.com/fwlink/?linkid=2143646)del gruppo di server. Se si desidera personalizzare la pianificazione, scegliere **pianificazione personalizzata**. È quindi possibile selezionare un giorno della settimana preferito e una finestra di 30 minuti per l'ora di inizio della manutenzione.

## <a name="notifications-about-scheduled-maintenance-events"></a>Notifiche relative agli eventi di manutenzione pianificata

È possibile usare integrità dei servizi di Azure per [visualizzare le notifiche](../service-health/service-notifications.md) relative alla manutenzione pianificata imminente e passata sul gruppo di server iperscalabile (CITUS). È anche possibile [configurare](../service-health/resource-health-alert-monitor-guide.md) gli avvisi in integrità dei servizi di Azure per ricevere notifiche sugli eventi di manutenzione.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [manutenzione pianificata in database di Azure per PostgreSQL – iperscalabilità (CITUS)](concepts-hyperscale-maintenance.md)
* Informazioni sull' [integrità dei servizi di Azure](../service-health/overview.md)
