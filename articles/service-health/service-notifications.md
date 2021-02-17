---
title: Visualizzare le notifiche sull'integrità del servizio tramite il portale di Azure
description: Visualizzare le notifiche sull'integrità del servizio nell'portale di Azure. Le notifiche sull'integrità del servizio vengono pubblicate dall'infrastruttura di Azure nel log attività di Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 9f9f3e7b10d9aa0014e4e00e7bfa72c9dc66e142
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588007"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visualizzare le notifiche sull'integrità del servizio tramite il portale di Azure

Le notifiche sull'integrità del servizio vengono pubblicate dall'infrastruttura di Azure nel [log attività di Azure](../azure-monitor/essentials/platform-logs-overview.md).  Le notifiche contengono informazioni sulle risorse nella sottoscrizione. Poiché le informazioni archiviate nel log attività possono raggiungere volumi elevati, è disponibile un'interfaccia utente separata che facilita la visualizzazione e la configurazione degli avvisi per le notifiche sull'integrità del servizio. 

Le notifiche sull'integrità del servizio possono essere informative o operative a seconda della classe.

Per ulteriori informazioni sulle varie classi di notifiche sull'integrità del servizio, vedere [proprietà delle notifiche sull'integrità del servizio](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visualizzare le notifiche sull'integrità del servizio nel portale di Azure

1. Nella [portale di Azure](https://portal.azure.com)selezionare **monitoraggio**.

    ![Schermata del menu del portale di Azure, con Monitoraggio selezionato](./media/service-notifications/home-monitor.png)

    Monitoraggio di Azure riunisce tutte le impostazioni e i dati di monitoraggio in un'unica vista consolidata. Per prima cosa si apre la sezione **Log di attività**.

1. Selezionare **Avvisi**.

    ![Schermata di Log attività in Monitoraggio, con Avvisi selezionati](./media/service-notifications/service-health-summary.png)

1. Selezionare **+ Aggiungi avviso del log attività** e configurare un avviso per assicurarsi di ricevere le future notifiche del servizio. Per maggiori informazioni, vedere [Creare gli avvisi del log attività per le notifiche del servizio](./alerts-activity-log-service-notifications-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sugli [avvisi del log attività](../azure-monitor/alerts/activity-log-alerts.md).
