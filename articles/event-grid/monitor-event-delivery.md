---
title: Visualizzare le metriche di griglia di eventi di Azure e impostare gli avvisi
description: Questo articolo descrive come usare la portale di Azure per visualizzare le metriche per gli argomenti e le sottoscrizioni di griglia di eventi di Azure e creare avvisi su di essi.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 6f6c119c16452246ec6eeb57ab392b29608938a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598559"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorare il recapito dei messaggio di Griglia di eventi 
Questo articolo descrive come usare il portale per visualizzare le metriche per gli argomenti e le sottoscrizioni di griglia di eventi e per creare avvisi su di essi. 

> [!IMPORTANT]
> Per un elenco delle metriche di griglia di eventi di Azure supportate, vedere [metriche](metrics.md).

## <a name="view-custom-topic-metrics"></a>Visualizzare le metriche degli argomenti personalizzati

Se è stato pubblicato un argomento personalizzato, è possibile visualizzare le relative metriche. 

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella barra di ricerca in questo argomento, digitare **argomenti di griglia di eventi**, quindi selezionare **argomenti griglia di eventi** nell'elenco a discesa. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Cercare e selezionare argomenti di Griglia di eventi":::
3. Selezionare l'argomento personalizzato dall'elenco di argomenti. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Selezionare l'argomento personalizzato":::
4. Visualizzare le metriche per l'argomento dell'evento personalizzato nella pagina dell' **argomento di griglia di eventi** . Nell'immagine seguente la sezione **Essentials** che mostra il gruppo di risorse, la sottoscrizione e così via è ridotta a icona. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Visualizzare le metriche degli eventi":::

    È possibile creare grafici con metriche supportate usando la scheda **metriche** della pagina dell'argomento di **griglia di eventi** .

    :::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Argomento-pagina metrica":::

    Vedere, ad esempio, il grafico delle metriche per la metrica **eventi pubblicati** .

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Metrica eventi pubblicati":::


## <a name="view-subscription-metrics"></a>Visualizzare le metriche delle sottoscrizioni
1. Passare alla pagina dell' **argomento griglia di eventi** attenendosi alla procedura descritta nella sezione precedente. 
2. Selezionare la sottoscrizione nel riquadro inferiore, come illustrato nell'esempio seguente. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Seleziona sottoscrizione evento":::    

    È anche possibile cercare le **sottoscrizioni di griglia di eventi** nella barra di ricerca del portale di Azure, selezionare **tipo di argomento**, **sottoscrizione** e **località** per visualizzare una sottoscrizione di eventi. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Pagina Seleziona sottoscrizione eventi dalla pagina sottoscrizioni di griglia di eventi":::        

    Per gli argomenti personalizzati, selezionare **argomenti griglia di eventi** come **tipo di argomento**. Per gli argomenti di sistema, selezionare il tipo di risorsa di Azure, ad esempio **account di archiviazione (BLOB, GPv2)**. 
3. Vedere le metriche per la sottoscrizione nel home page per la sottoscrizione in un grafico. È possibile visualizzare **le metriche generali**, di **errore** e di **latenza** per le ultime 1 ora, 6 ore, 12 ore, 1 giorno, 7 giorni o 30 giorni. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Metriche sulla sottoscrizione home page":::    

## <a name="view-system-topic-metrics"></a>Visualizza le metriche degli argomenti di sistema

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella barra di ricerca in questo argomento, digitare **argomenti di sistema griglia di eventi**, quindi selezionare **argomenti del sistema di griglia di eventi** nell'elenco a discesa. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Cercare e selezionare gli argomenti di sistema di griglia di eventi":::
3. Selezionare l'argomento di sistema dall'elenco di argomenti. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Selezionare l'argomento di sistema":::
4. Visualizzare le metriche per l'argomento di sistema nella pagina di **argomento del sistema di griglia di eventi** . Nell'immagine seguente la sezione **Essentials** che mostra il gruppo di risorse, la sottoscrizione e così via è ridotta a icona. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Visualizzare le metriche degli argomenti di sistema nella pagina Panoramica":::

    È possibile creare grafici con metriche supportate usando la scheda **metriche** della pagina dell'argomento di **griglia di eventi** .

    :::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Argomento sistema-pagina metrica":::

    > [!IMPORTANT]
    > Per un elenco delle metriche di griglia di eventi di Azure supportate, vedere [metriche](metrics.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- Per informazioni su come creare avvisi per le metriche e le operazioni del log attività, vedere [impostare gli avvisi](set-alerts.md).
- Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
