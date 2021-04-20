---
title: Creare avvisi con informazioni dettagliate su SQL (anteprima)
description: Creare avvisi con informazioni dettagliate su SQL in Monitoraggio di Azure
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: bb42f74f6ac8487a93479bdf980c66ef87e8e742
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726863"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Creare avvisi con informazioni dettagliate su SQL (anteprima)
Informazioni dettagliate su SQL include un set di modelli di regole di avviso che è possibile usare per creare regole di [avviso in](../alert/../alerts/alerts-overview.md) Monitoraggio di Azure per problemi SQL comuni. Le regole di avviso nelle informazioni dettagliate di SQL sono regole di avviso del log basate sui dati sulle prestazioni archiviati nella tabella *InsightsMetrics* Monitoraggio di Azure log.  

> [!NOTE]
> Per creare un avviso per informazioni dettagliate su SQL usando un modello di Resource Manager, vedere esempi di Resource Manager [modello per informazioni dettagliate su SQL.](resource-manager-sql-insights.md#create-an-alert-rule-for-sql-insights)


> [!NOTE]
> Se sono presenti richieste per altri modelli di regole di avviso di SQL Insights, inviare commenti e suggerimenti usando il collegamento nella parte inferiore di questa pagina o il collegamento di commenti e suggerimenti di SQL Insights nel portale di Azure.

## <a name="enable-alert-rules"></a>Abilitare le regole di avviso 
Usare la procedura seguente per abilitare gli avvisi Monitoraggio di Azure dal portale di Azure.L'ambito delle regole di avviso create verrà esteso a tutte le risorse SQL monitorate nel profilo di monitoraggio selezionato.  Quando viene attivata, una regola di avviso viene attivata nell'istanza o nel database SQL specifico.

> [!NOTE]
> È anche possibile creare regole di avviso del [log](../alerts/alerts-log.md) personalizzate eseguendo query sui set di dati nella *tabella InsightsMetrics* e quindi salvando tali query come regola di avviso. 

Selezionare **SQL (anteprima)** nella **sezione Informazioni** dettagliate del menu Monitoraggio di Azure nel portale di Azure. Fare clic **su Avvisi**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Pulsante Avvisi":::

Il **riquadro** Avvisi si apre sul lato destro della pagina. Per impostazione predefinita, visualizza gli avvisi generati per le risorse SQL nel profilo di monitoraggio selezionato in base alle regole di avviso già create. Selezionare **Modelli di avviso**, che visualizza l'elenco dei modelli disponibili che è possibile usare per creare una regola di avviso.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Modelli di avviso":::

Nella pagina **Crea regola di avviso** esaminare le impostazioni predefinite per la regola e modificarle in base alle esigenze. È anche possibile selezionare un gruppo [di azioni per](../alerts/action-groups.md) creare notifiche e azioni quando viene attivata la regola di avviso. Fare **clic su Abilita regola** di avviso per creare la regola di avviso dopo aver verificato tutte le relative proprietà.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Pagina Regole di avviso":::

Per distribuire immediatamente la regola di avviso, fare clic **su Distribuisci regola di avviso**. Fare **clic su Visualizza** modello se si vuole visualizzare il modello di regola prima di distribuirlo effettivamente.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Distribuisci regola di avviso":::

Se si sceglie di visualizzare i modelli, selezionare **Distribuisci** nella pagina del modello per creare la regola di avviso.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Distribuire dal modello di visualizzazione":::


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli [avvisi in Monitoraggio di Azure](../alerts/alerts-overview.md).

