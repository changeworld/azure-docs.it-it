---
title: Analisi delle modifiche in VM Insights
description: L'integrazione di VM Insights con l'integrazione dell'analisi delle modifiche dell'applicazione consente di visualizzare tutte le modifiche apportate a una macchina virtuale che potrebbe avere influenzato le prestazioni it.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 375812813d704eb9b48d0ed8fbbc65dd5e47da49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046772"
---
# <a name="change-analysis-in-vm-insights"></a>Analisi delle modifiche in VM Insights
L'integrazione di VM Insights con l'integrazione [dell'analisi delle modifiche dell'applicazione](../app/change-analysis.md) consente di visualizzare tutte le modifiche apportate a una macchina virtuale che potrebbe avere influenzato le prestazioni it.

## <a name="overview"></a>Panoramica
Si supponga di disporre di una macchina virtuale in esecuzione lenta e di voler verificare se le modifiche recenti alla configurazione potrebbero influire sulle prestazioni. È possibile visualizzare le prestazioni della macchina virtuale usando VM Insights e verificare che sia presente un aumento dell'utilizzo della memoria nell'ultima ora. L'analisi delle modifiche può consentire di determinare se le modifiche apportate alla configurazione in questo periodo di tempo sono state provocate da questo aumento.

Il servizio di analisi delle modifiche dell'applicazione aggrega le modifiche da [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md) , nonché le modifiche alle proprietà annidate, ad esempio le regole di sicurezza di rete da Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Abilitazione dell'analisi delle modifiche
Per eseguire l'onboarding dell'analisi delle modifiche in VM Insights, è necessario registrare il provider di risorse *Microsoft. ChangeAnalysis* . La prima volta che si avvia VM Insights o l'analisi delle modifiche dell'applicazione nella portale di Azure, questo provider di risorse verrà automaticamente registrato. L'analisi delle modifiche dell'applicazione è un servizio gratuito che non comporta un sovraccarico delle prestazioni sulle risorse.

## <a name="view-change-analysis"></a>Visualizza analisi modifiche
L'analisi delle modifiche è disponibile nella scheda **prestazioni** o **mappa** di VM Insights selezionando l'opzione **Cambia** . 

[![Esaminare le modifiche](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Fare clic sul pulsante **Esamina modifiche** per avviare la pagina di analisi delle modifiche dell'applicazione filtrata per la macchina virtuale. È possibile esaminare le modifiche elencate per verificare se sono presenti eventuali problemi che potrebbero aver causato il problema. Se non si è certi di una modifica particolare, è possibile fare riferimento alla colonna **modifica** per determinare la persona che ha apportato la modifica.

[![Dettagli modifiche](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull' [analisi delle modifiche dell'applicazione](../app/change-analysis.md).
- Scopri di più sul [grafico delle risorse di Azure](../../governance/resource-graph/how-to/get-resource-changes.md). 

