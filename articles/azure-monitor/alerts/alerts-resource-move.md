---
title: Come aggiornare le regole di avviso o le regole di azione quando la risorsa di destinazione viene spostata in un'area di Azure diversa
description: Informazioni generali e istruzioni su come aggiornare le regole di avviso o le regole di azione quando la risorsa di destinazione viene spostata in un'area di Azure diversa.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: d21ee7a60d11a154737c5380ec20d3e9c4490962
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786064"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Come aggiornare le regole di avviso o le regole di azione quando la risorsa di destinazione viene spostata in un'area di Azure diversa

Questo articolo descrive perché le [](./alerts-action-rules.md) [regole](./alerts-overview.md) di avviso e le regole di azione esistenti possono essere influenzate quando si spostano altre risorse di Azure tra aree e come identificare e risolvere tali problemi. Vedere la documentazione principale [sullo spostamento delle](../../azure-resource-manager/management/move-resources-overview.md) risorse per altre informazioni su quando è utile lo spostamento delle risorse tra aree e un elenco di controllo per la progettazione di un processo di spostamento.

## <a name="why-the-problem-exists"></a>Perché il problema esiste

Le regole di avviso e le regole di azione fanno riferimento ad altre risorse di Azure. Ad [esempio, le macchine virtuali di Azure,](../../site-recovery/azure-to-azure-tutorial-migrate.md) [Azure SQL](../../azure-sql/database/move-resources-across-regions.md)e [Archiviazione di Azure](../../storage/common/storage-account-move.md). Quando si spostano le risorse a cui fanno riferimento queste regole, è probabile che le regole smettano di funzionare correttamente perché non sono in grado di trovare le risorse a cui fanno riferimento.

Esistono due motivi principali per cui le regole potrebbero smettere di funzionare dopo lo spostamento delle risorse di destinazione:

- L'ambito della regola fa riferimento in modo esplicito alla risorsa precedente.
- La regola di avviso si basa sulle metriche.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>L'ambito della regola fa riferimento in modo esplicito alla risorsa precedente

Quando si sposta una risorsa, il relativo ID risorsa cambia nella maggior parte dei casi. In background, il sistema replica la risorsa nella nuova area prima di eliminarla dall'area precedente. Questo processo richiede che due risorse e quindi due ID risorsa diversi esistano contemporaneamente per un breve periodo di tempo. Poiché gli ID risorsa devono essere univoci, è necessario creare un nuovo ID durante il processo. 

**In che modo lo spostamento della risorsa influisce sulle regole esistenti?**

Le regole di avviso e le regole di azione hanno un ambito di risorse a cui si applicano. L'ambito può essere un'intera sottoscrizione, un gruppo di risorse o una o più risorse specifiche.
Ad esempio, ecco una regola con un ambito con due risorse (due macchine virtuali):

![Regola di avviso per più risorse](media/alerts-resource-move/multi-resource-alert-rule.png)

Se l'ambito della regola menziona in modo esplicito una risorsa e tale risorsa ha spostato e modificato l'ID risorsa, tale regola cerca una risorsa errata o inesistente e quindi avrà esito negativo.

**Come risolvere il problema?**

Aggiornare o ricreare la regola interessata in modo che punti alla nuova risorsa. Il processo di aggiornamento dell'ambito è disponibile più avanti in questo articolo.

Il problema si applica a questi tipi di regole:

- Regole di avviso del log attività
- Regole di azione
- Avvisi delle metriche: per altre informazioni, vedere la sezione successiva [Regole di avviso in base alle metriche](#alert-rules-based-on-metrics).

> [!NOTE]
> Le regole di avviso di ricerca log e le regole di avviso del rilevamento intelligente non sono interessate perché il relativo ambito è un'area di lavoro o Application Insights. Nessuno di questi ambiti supporta attualmente gli spostamenti di aree.

## <a name="alert-rules-based-on-metrics"></a>Regole di avviso basate sulle metriche

Le metriche che le risorse di Azure generano sono a livello di regione. Ogni volta che una risorsa si sposta in una nuova area, inizia a emettere le metriche in tale nuova area. Di conseguenza, tutte le regole di avviso basate sulle metriche devono essere aggiornate o ricreate in modo che puntino al flusso delle metriche corrente nell'area corretta.

Questa spiegazione si applica sia alle regole [di avviso delle metriche che](alerts-metric-overview.md) alle regole di avviso del test di [disponibilità](../app/monitor-web-app-availability.md).

Se **tutte** le risorse nell'ambito sono state spostate, non è necessario ricreare la regola. È possibile aggiornare qualsiasi campo della regola di avviso, ad esempio la descrizione della regola di avviso, e salvarlo.
Se **solo alcune** delle risorse nell'ambito sono state spostate, è necessario rimuovere le risorse spostate dalla regola esistente e creare una nuova regola che copre solo le risorse spostate.

## <a name="procedures-to-fix-problems"></a>Procedure per risolvere i problemi

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Identificazione delle regole associate a una risorsa spostata dal portale di Azure

- **Per le regole di** avviso: passare ad Avvisi > Gestisci regole di avviso > filtrare in base alla sottoscrizione contenitore e alla risorsa spostata.
> [!NOTE]
> Le regole di avviso del log attività non supportano questo processo. Non è possibile aggiornare l'ambito di una regola di avviso del log attività e fare in modo che punti a una risorsa in un'altra sottoscrizione. È invece possibile creare una nuova regola che sostituirà quella precedente.

- **Per le regole** di azione: passare ad Avvisi > Gestisci azioni > Regole di azione (anteprima) > filtrare in base alla sottoscrizione contenitore e alla risorsa spostata.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Modificare l'ambito di una regola dal portale di Azure

1. Aprire la regola identificata nel passaggio precedente facendo clic su di essa.
2. In **Risorsa** fare clic **su Modifica** e modificare l'ambito in base alle esigenze.
3. Modificare le altre proprietà della regola in base alle esigenze.
4. Fare clic su **Salva**.

![Modificare l'ambito della regola di avviso](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Modificare l'ambito di una regola usando Azure Resource Manager personalizzati

1. Ottenere il Azure Resource Manager modello della regola.   Per esportare il modello di una regola dal portale di Azure:
   1. Passare alla sezione Gruppi di risorse nel portale e aprire il gruppo di risorse contenente la regola.
   2. Nella sezione Panoramica selezionare la casella **di controllo Mostra** tipo nascosto e filtrare in base al tipo pertinente della regola.
   3. Selezionare la regola pertinente per visualizzarne i dettagli.
   4. In **Impostazioni** selezionare **Esporta modello.**
2. Modificare il modello. Se necessario, suddividere in due regole (rilevanti per alcuni casi di avvisi delle metriche, come indicato in precedenza).
3. Ridistribuire il modello.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Modificare l'ambito di una regola usando l'API REST

1. Ottenere la regola esistente ([avvisi delle metriche,](/rest/api/monitor/metricalerts/get) [avvisi del log attività](/rest/api/monitor/activitylogalerts/get))
2. Modificare l'ambito ([avvisi del log attività](/rest/api/monitor/activitylogalerts/update))
3. Ridistribuire la regola ([avvisi delle metriche,](/rest/api/monitor/metricalerts/createorupdate) [avvisi del log attività](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Modificare l'ambito di una regola usando PowerShell

1. Ottenere la regola esistente ([avvisi delle metriche,](/powershell/module/az.monitor/get-azmetricalertrulev2)avvisi [del log attività,](/powershell/module/az.monitor/get-azactivitylogalert) [regole di azione](/powershell/module/az.alertsmanagement/get-azactionrule)).
2. Modificare l'ambito. Se necessario, suddividere in due regole (rilevanti per alcuni casi di avvisi delle metriche, come indicato in precedenza).
3. Ridistribuire la regola ([avvisi delle metriche,](/powershell/module/az.monitor/add-azmetricalertrulev2)avvisi [del log attività,](/powershell/module/az.monitor/enable-azactivitylogalert) [regole di azione](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Modificare l'ambito di una regola usando l'interfaccia della riga di comando di Azure

1.  Ottenere la regola esistente ([avvisi delle metriche,](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_show) [avvisi del log attività](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log-alert_list)).
2.  Aggiornare direttamente l'ambito della regola ([avvisi delle metriche,](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_update) [avvisi del log attività](/cli/azure/monitor/activity-log/alert/scope))
3.  Se necessario, suddividere in due regole (rilevanti per alcuni casi di avvisi delle metriche, come indicato in precedenza).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla correzione di altri problemi relativi alle notifiche [di avviso,](alerts-troubleshoot.md) [agli avvisi delle metriche](alerts-troubleshoot-metric.md)e agli avvisi del [log.](alerts-troubleshoot-log.md)