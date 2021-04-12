---
title: Gestisci le VM di avvio/arresto V2 (anteprima)
description: Questo articolo illustra come monitorare lo stato delle macchine virtuali di Azure gestite dalla funzionalità di avvio/arresto di VM V2 (anteprima) ed eseguire altre attività di gestione.
services: azure-functions
ms.subservice: ''
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: 477e3fe51f05bb4323642f56233f1995e6394eec
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111955"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>Come gestire le VM di avvio/arresto V2 (anteprima)

## <a name="azure-dashboard"></a>Dashboard di Azure

Start/Stop VM V2 (anteprima) include un [Dashboard](../../azure-monitor/visualizations.md#azure-dashboards) che consente di comprendere l'ambito di gestione e le operazioni recenti nelle macchine virtuali. Si tratta di un modo rapido e semplice per verificare lo stato di ogni operazione eseguita nelle macchine virtuali di Azure. La visualizzazione in ogni riquadro è basata su una query di log e per visualizzare la query, selezionare l'opzione **Apri nei log** nell'angolo destro del riquadro. Viene aperto lo strumento [log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) nella portale di Azure e da qui è possibile valutare la query e modificarla in modo da supportare le proprie esigenze, ad esempio gli [avvisi del log](../../azure-monitor/alerts/alerts-log.md)personalizzati, una cartella di [lavoro](../../azure-monitor/visualize/workbooks-overview.md)personalizzata e così via.

I dati di log in ogni riquadro visualizzato nel dashboard vengono aggiornati ogni ora, con un'opzione di aggiornamento manuale su richiesta facendo clic sull'icona di **aggiornamento** in una determinata visualizzazione o aggiornando il dashboard completo.

Per informazioni sull'uso di un dashboard basato su log, vedere l' [esercitazione](../../azure-monitor/visualize/tutorial-logs-dashboards.md)seguente.

## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

Per modificare le notifiche tramite posta elettronica dopo la distribuzione di avvio/arresto di macchine virtuali V2 (anteprima), è possibile modificare il gruppo di azioni creato durante la distribuzione.

1. Nella portale di Azure passare a **monitoraggio**, quindi **avvisi**. Selezionare **Gestisci azioni**.

1. Nella pagina **Gestisci azioni** selezionare il gruppo di azioni denominato **StartStopV2_VM_Notication**.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="Screenshot della pagina dei gruppi di azioni.":::

1. Nella pagina **StartStopV2_VM_Notification** è possibile modificare le opzioni di notifica tramite posta elettronica/SMS/push/voce. Aggiungere altre azioni o aggiornare la configurazione esistente a questo gruppo di azione, quindi fare clic su **OK** per salvare le modifiche.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="Screenshot della pagina posta elettronica/SMS/push/Voice che mostra un esempio di indirizzo di posta elettronica aggiornato.":::

    Per ulteriori informazioni sui gruppi di azioni, vedere [gruppi di azioni](../../azure-monitor/alerts/action-groups.md)

Lo screenshot seguente è un esempio di messaggio di posta elettronica che viene inviato quando la funzionalità arresta le macchine virtuali.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="Screenshot di un messaggio di posta elettronica di esempio inviato quando la funzionalità arresta le macchine virtuali.":::

## <a name="next-steps"></a>Passaggi successivi

Per gestire i problemi durante la gestione della macchina virtuale, vedere risolvere i problemi di [avvio/arresto di VM V2](troubleshoot.md) (anteprima).