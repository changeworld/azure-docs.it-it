---
title: Crea avvisi con SQL Insights (anteprima)
description: Creare avvisi con SQL Insights in monitoraggio di Azure
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: 5fe853ee0f7a113bfb8b0511744d9087f67927c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609906"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Crea avvisi con SQL Insights (anteprima)
SQL Insights include un set di modelli di regole di avviso che è possibile usare per creare [regole di avviso in monitoraggio di Azure](../alert/../alerts/alerts-overview.md) per problemi comuni di SQL. Le regole di avviso in SQL Insights sono regole di avviso del log basate sui dati sulle prestazioni archiviati nella tabella *InsightsMetrics* nei log di monitoraggio di Azure.  

> [!NOTE]
> Se si hanno richieste per altri modelli di regole di avviso di SQL Insights, inviare commenti e suggerimenti usando il collegamento nella parte inferiore della pagina o usare il collegamento feedback di SQL Insights nella portale di Azure.

## <a name="enable-alert-rules"></a>Abilita regole di avviso 
Usare la procedura seguente per abilitare gli avvisi in monitoraggio di Azure dalla portale di Azure.Le regole di avviso create saranno limitate a tutte le risorse SQL monitorate nel profilo di monitoraggio selezionato.  Quando viene attivata una regola di avviso, questa verrà attivata nell'istanza o nel database SQL specifico.

> [!NOTE]
> È anche possibile creare [regole di avviso del log](../alerts/alerts-log.md) personalizzate eseguendo query sui set di dati nella tabella *InsightsMetrics* e quindi salvando tali query come regola di avviso. 

Selezionare **SQL (anteprima)** nella sezione **Insights** del menu di monitoraggio di Azure nel portale di Azure. Fare clic su **avvisi**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Pulsante avvisi":::

Il riquadro **avvisi** si apre sul lato destro della pagina. Per impostazione predefinita, gli avvisi generati per le risorse SQL vengono visualizzati nel profilo di monitoraggio selezionato in base alle regole di avviso già create. Selezionare **modelli di avviso**, in cui viene visualizzato l'elenco dei modelli disponibili che è possibile utilizzare per creare una regola di avviso.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Modelli di avviso":::

Nella pagina **Crea regola di avviso** esaminare le impostazioni predefinite per la regola e modificarle in base alle esigenze. È inoltre possibile selezionare un [gruppo di azioni](../alerts/action-groups.md) per creare notifiche e azioni quando viene attivata la regola di avviso. Fare clic su **Abilita regola di avviso** per creare la regola di avviso dopo aver verificato tutte le relative proprietà.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Pagina regole di avviso":::

Per distribuire immediatamente la regola di avviso, fare clic su **Distribuisci regola di avviso**. Fare clic su **Visualizza modello** se si desidera visualizzare il modello di regola prima di distribuirlo effettivamente.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Distribuisci regola di avviso":::

Se si sceglie di visualizzare i modelli, selezionare **Distribuisci** nella pagina modello per creare la regola di avviso.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Distribuisci dal modello di vista":::


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli [avvisi in monitoraggio di Azure](../alerts/alerts-overview.md).

