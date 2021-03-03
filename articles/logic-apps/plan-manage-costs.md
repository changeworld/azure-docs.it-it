---
title: Pianificare la gestione dei costi per le app per la logica di Azure
description: Informazioni su come pianificare e gestire i costi per le app per la logica di Azure tramite l'analisi dei costi nel portale di Azure
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 44351497ed58c8d49404c094f6800b52186edabb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709632"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Pianificare e gestire i costi per le app per la logica di Azure

Questo articolo consente di pianificare e gestire i costi per le app per la logica di Azure. Prima di creare o aggiungere risorse tramite questo servizio, è necessario stimare i costi usando il calcolatore dei prezzi di Azure. Dopo aver iniziato a usare le risorse delle app per la logica, è possibile impostare i budget e monitorare i costi con [Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per identificare le aree in cui si potrebbe voler agire, è anche possibile esaminare i costi previsti e monitorare le tendenze di spesa.

Tenere presente che i costi per le app per la logica sono solo parte dei costi mensili nella fattura di Azure. Sebbene questo articolo spieghi come stimare e gestire i costi per le app per la logica, verranno addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti. Dopo aver acquisito familiarità con la gestione dei costi per le app per la logica, è possibile applicare metodi simili per gestire i costi per tutti i servizi di Azure usati nella sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) supporta la maggior parte dei tipi di account Azure. Per visualizzare tutti i tipi di account supportati, vedere [informazioni sui dati di gestione dei costi](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per visualizzare i dati relativi ai costi, è necessario disporre almeno dell''accesso in lettura per l''account Azure.

Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Informazioni sul modello di fatturazione

App per la logica di Azure viene eseguito nell'infrastruttura di Azure che [accumula i costi](https://azure.microsoft.com/pricing/details/logic-apps/) quando si distribuiscono nuove risorse. Assicurarsi di aver compreso il [modello di fatturazione per il servizio app per la logica insieme alle risorse di Azure correlate](logic-apps-pricing.md)e gestire i costi a causa di queste dipendenze quando si apportano modifiche alle risorse distribuite.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Costi che in genere si accumulano con le app per la logica di Azure

Il servizio app per la logica applica modelli tariffari diversi, in base alle risorse create e usate:

* Le risorse dell'app per la logica create ed eseguite nel servizio app per la logica multi-tenant usano un [modello di prezzi a consumo](../logic-apps/logic-apps-pricing.md#consumption-pricing).

* Le risorse dell'app per la logica create ed eseguite in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) usano un [modello di determinazione prezzi fisso](../logic-apps/logic-apps-pricing.md#fixed-pricing).

Di seguito sono riportate altre risorse che comportano costi quando vengono create per l'uso con app per la logica:

* Un [account di integrazione](../logic-apps/logic-apps-pricing.md#integration-accounts) è una risorsa separata che è possibile creare e collegare alle app per la logica per la creazione di integrazioni B2B. Gli account di integrazione usano un [modello di determinazione prezzi fisso](../logic-apps/logic-apps-pricing.md#integration-accounts) in cui la velocità è basata sul tipo o sul *livello* dell'account di integrazione usato.

* [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) è una risorsa separata creata come percorso di distribuzione per le app per la logica che necessitano dell'accesso diretto alle risorse in una rete virtuale. ISEs usano un [modello di determinazione prezzi fisso](../logic-apps/logic-apps-pricing.md#fixed-pricing) , in cui la frequenza è basata sullo SKU ISE creato e su altre impostazioni.

* Un [connettore personalizzato](../logic-apps/logic-apps-pricing.md#consumption-pricing) è una risorsa separata creata per un'API REST che non ha un connettore predefinito da usare nelle app per la logica. Le esecuzioni di connettori personalizzati usano un [modello di prezzi a consumo](../logic-apps/logic-apps-pricing.md#consumption-pricing) tranne quando vengono usate in un ISE.

* Nel servizio app per la logica multi-tenant, la [conservazione dei dati e il consumo di spazio di archiviazione](../logic-apps/logic-apps-pricing.md#data-retention) aumentano i costi usando un [modello di determinazione prezzi fisso](../logic-apps/logic-apps-pricing.md#fixed-pricing). Gli input e gli output della cronologia di esecuzione, ad esempio, vengono mantenuti nell'archiviazione dietro le quinte, che differisce dalle risorse di archiviazione che è possibile creare, gestire e accedere in modo indipendente dall'app per la logica.

  In un ISE, la conservazione e l'utilizzo dell'archiviazione dei dati non comportano costi.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Potrebbero maturare costi dopo l'eliminazione delle risorse

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Dopo l'eliminazione di un'app per la logica, il servizio app per la logica non creerà né eseguirà nuove istanze del flusso di lavoro. Tuttavia, tutte le esecuzioni in corso e in sospeso continuano fino a quando non vengono completate. A seconda del numero di queste esecuzioni, questo processo potrebbe richiedere del tempo. Per altre informazioni, vedere [gestire le app](manage-logic-apps-with-azure-portal.md#delete-logic-apps)per la logica.

Se si dispone di queste risorse dopo l'eliminazione di un'app per la logica, queste risorse continuano a esistere e accumulano costi fino a quando non vengono eliminate:

* Risorse di Azure create e gestite in modo indipendente dall'app per la logica che si connette a tali risorse, ad esempio app per le funzioni di Azure, Hub eventi, griglie di eventi e così via

* Account di integrazione

* Ambienti del servizio di integrazione (ISEs)

  Se si [Elimina un ISE](ise-manage-integration-service-environment.md#delete-ise), la rete virtuale di Azure associata, le subnet e altre risorse correlate continueranno a esistere. Dopo aver eliminato ISE, potrebbe essere necessario attendere fino a un numero di ore specifico prima di provare a eliminare la rete virtuale o le subnet.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Uso del credito monetario con app per la logica di Azure

È possibile pagare gli addebiti per le app per la logica di Azure con il credito dell'impegno monetario EA Tuttavia, non è possibile usare il credito dell'impegno monetario EA per pagare gli addebiti per i prodotti e i servizi di terze parti, inclusi quelli di Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Stimare i costi

Prima di creare risorse con app per la logica di Azure, è possibile stimare i costi usando il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/). Per altre informazioni, vedere il [modello di determinazione dei prezzi per app per la logica di Azure](../logic-apps/logic-apps-pricing.md).

1. Nel menu a sinistra della [pagina Calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)selezionare **integrazione** app per la  >  **logica di Azure**.

   ![Screenshot che mostra il calcolatore dei prezzi di Azure con "app per la logica di Azure" selezionata.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Scorrere la pagina verso il basso fino a visualizzare il calcolatore dei prezzi di app per la logica di Azure. Nelle varie sezioni per le risorse di Azure correlate direttamente alle app per la logica di Azure immettere il numero di risorse che si prevede di usare e il numero di intervalli in cui è possibile usare tali risorse.

   Questo screenshot mostra un esempio di stima dei costi usando il calcolatore:

   ![Esempio che mostra il costo stimato nel calcolatore dei prezzi di Azure](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Per aggiornare le stime dei costi durante la creazione e l'uso di nuove risorse correlate, tornare al calcolatore e aggiornare le risorse qui.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Creare budget e avvisi

Per consentire di gestire in modo proattivo i costi per l'account o la sottoscrizione di Azure, è possibile creare [budget](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) e [avvisi](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) usando le funzionalità e il servizio di [fatturazione e gestione dei costi di Azure](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .  I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva.

In base alla spesa rispetto alle soglie di budget e costi, gli avvisi notificano automaticamente agli stakeholder le anomalie di spesa e i rischi. Se si vuole una maggiore granularità nel monitoraggio, è anche possibile creare budget che usano i filtri per risorse o servizi specifici in Azure. I filtri consentono di assicurarsi di non creare accidentalmente nuove risorse che costino denaro aggiuntivo. Per ulteriori informazioni sulle opzioni di filtro, vedere [Opzioni di raggruppamento e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Monitorare i costi

I costi delle unità di utilizzo delle risorse variano in base a intervalli di tempo, ad esempio secondi, minuti, ore e giorni o in base all'utilizzo di unità, ad esempio byte, megabyte e così via. Alcuni esempi sono il giorno, il mese corrente e quello precedente e l'anno. Il passare a visualizzazioni più lunghe nel tempo può aiutare a identificare le tendenze di spesa. Quando si utilizzano le funzionalità di analisi dei costi, è possibile visualizzare i costi come grafici e tabelle in diversi intervalli di tempo. Se sono stati creati budget e previsioni sui costi, è anche possibile trovare facilmente le posizioni in cui sono stati superati i budget e potrebbe essersi verificata la sovraspesa.

Dopo aver iniziato a sostenere i costi per le risorse che creano o iniziano a usare in Azure, è possibile esaminare e monitorare questi costi nei modi seguenti:

* [Monitorare le esecuzioni delle app per la logica e il consumo di archiviazione](#monitor-billing-metrics) con monitoraggio di Azure

* Eseguire l' [analisi dei costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) con [Gestione costi di Azure e fatturazione](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Monitorare le esecuzioni delle app per la logica e il consumo di archiviazione

Con monitoraggio di Azure è possibile visualizzare queste metriche per un'app per la logica specifica:

* Esecuzioni di azioni fatturabili
* Esecuzioni di trigger fatturabili
* Utilizzo di fatturazione per le esecuzioni di operazioni Native
* Uso della fatturazione per le esecuzioni del connettore standard
* Utilizzo della fatturazione per il consumo di spazio di archiviazione
* Totale esecuzioni fatturabili

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Visualizzare le metriche di utilizzo dell'archiviazione e dell'esecuzione

1. Nel portale di Azure trovare e aprire l'app per la logica. Nel menu dell'app per la logica selezionare **metriche** in **monitoraggio**.

1. Nel riquadro di destra, in **titolo grafico**, nella barra metrica, aprire l'elenco **metrica** e selezionare la metrica desiderata.

   > [!NOTE]
   > Il consumo di spazio di archiviazione viene misurato come il numero di unità di archiviazione (GB) utilizzate dall'app per la logica e viene fatturato. Le esecuzioni che utilizzano meno di 500 MB di spazio di archiviazione potrebbero non essere visualizzate nella visualizzazione monitoraggio, ma vengono comunque fatturate.

   ![Screenshot che mostra il riquadro metriche con l'elenco "metrica" aperto.](./media/logic-apps-pricing/select-metric.png)

1. Nell'angolo superiore destro del riquadro selezionare il periodo di tempo desiderato.

1. Per visualizzare altri dati sul consumo di spazio di archiviazione, in particolare le dimensioni di input e output nella cronologia di esecuzione dell'app per la logica, [seguire questa procedura](#view-input-output-sizes).

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Visualizzare le dimensioni di input e output dell'azione nella cronologia di esecuzione

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Scegliere **Panoramica** dal menu dell'app per la logica.

1. Nel riquadro di destra, in **Cronologia esecuzioni**, selezionare l'esecuzione con gli input e gli output che si desidera visualizzare.

1. In **esecuzione App** per la logica selezionare **Dettagli esecuzione**.

1. Nella tabella Actions del riquadro **Dettagli esecuzione App** per la logica, che elenca lo stato e la durata di ogni azione, selezionare l'azione che si desidera visualizzare.

1. Nel riquadro **azione dell'app** per la logica trovare le dimensioni per gli input e gli output dell'azione. In **input collegamento** e **output collegamento** trovare i collegamenti a tali input e output.

   > [!NOTE]
   > Per i cicli for, solo le azioni di primo livello mostrano le dimensioni per gli input e gli output. Per le azioni all'interno di cicli annidati, gli input e gli output mostrano dimensioni zero e nessun collegamento.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Eseguire l'analisi dei costi con gestione costi di Azure e fatturazione

Per esaminare i costi del servizio app per la logica in base a un ambito specifico, ad esempio una sottoscrizione di Azure, è possibile usare le funzionalità di [analisi dei costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in [Gestione costi di Azure e fatturazione](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

1. Nella portale di Azure aprire l'ambito desiderato, ad esempio la sottoscrizione di Azure. Nel menu a sinistra, in **Gestione costi**, selezionare **analisi dei costi**.

   Quando si apre per la prima volta il riquadro analisi costi, il grafico superiore mostra i costi di utilizzo effettivi e previsti in tutti i servizi nella sottoscrizione per il mese corrente.

   ![Screenshot che Mostra portale di Azure e il riquadro analisi costi, ad esempio per i costi effettivi e previsti in una sottoscrizione.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Per modificare gli ambiti, nel riquadro **analisi costi** della barra dei filtri selezionare il filtro **ambito** . Nel riquadro **Seleziona ambito** passare all'ambito desiderato.

   Sotto, i grafici ad anello mostrano i costi correnti per i servizi di Azure, in base all'area di Azure (località) e al gruppo di risorse.

   ![Screenshot che Mostra portale di Azure e il riquadro analisi dei costi con grafici ad anello per servizi, aree e gruppi di risorse.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Per filtrare il grafico in un'area specifica, ad esempio un servizio o una risorsa, selezionare **Aggiungi filtro** nella barra dei filtri.

1. Nell'elenco a sinistra selezionare il tipo di filtro, ad esempio **nome servizio**. Dall'elenco a destra selezionare il filtro, ad esempio app per la **logica**. Al termine, selezionare il segno di spunta verde.

   ![Screenshot che Mostra portale di Azure e il riquadro analisi costi con selezioni filtro.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Ecco ad esempio il risultato per il servizio app per la logica:

   ![Screenshot che Mostra portale di Azure e il riquadro analisi costi con i risultati filtrati in "app per la logica".](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Esportare i dati relativi ai costi

Quando è necessario eseguire più analisi dei dati sui costi, è possibile [esportare i dati sui costi](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Ad esempio, un team Finance può analizzare questi dati utilizzando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è la modalità consigliata per recuperare i set di dati di costo.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Altri modi per gestire e ridurre i costi

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Per ridurre i costi per i punti di APS della logica e le risorse correlate, provare le opzioni seguenti:

* Se possibile, usare [trigger e azioni predefiniti](../connectors/apis-list.md#built-in), che hanno un costo inferiore per l'esecuzione per ogni esecuzione rispetto alle [azioni e ai trigger del connettore gestito](../connectors/apis-list.md#managed-connectors).

  Ad esempio, potrebbe essere possibile ridurre i costi quando si accede ad altre risorse usando l' [azione http](../connectors/connectors-native-http.md) o chiamando una funzione creata usando il [servizio funzioni di Azure](../azure-functions/functions-overview.md) e usando l' [azione incorporata di funzioni di Azure](../logic-apps/logic-apps-azure-functions.md). Tuttavia, l'uso di funzioni di Azure comporta anche costi, quindi assicurarsi di confrontare le opzioni.

* [Specificare condizioni di trigger precise](logic-apps-workflow-actions-triggers.md#trigger-conditions) per l'esecuzione di un flusso di lavoro.

  Ad esempio, è possibile specificare che un trigger venga attivato solo quando il sito Web di destinazione restituisce un errore interno del server. Nella definizione JSON del trigger usare la `conditions` proprietà per specificare una condizione che fa riferimento al codice di stato del trigger.

* Se un trigger ha una versione di polling e una versione del webhook, provare la versione del webhook, che attende l'evento specificato prima dell'attivazione, anziché controllare regolarmente l'evento.

* Chiamare l'app per la logica tramite un altro servizio in modo che il trigger venga attivato solo quando il flusso di lavoro deve essere eseguito.

  Ad esempio, è possibile chiamare l'app per la logica da una funzione creata ed eseguita usando il servizio funzioni di Azure. Vedere ad esempio [chiamare o attivare app per la logica usando funzioni di Azure e il bus di servizio di Azure](logic-apps-scenario-function-sb-trigger.md).

* [Disabilitare le app](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) per la logica che non devono essere eseguite costantemente o [eliminare le app](manage-logic-apps-with-azure-portal.md#delete-logic-apps) per la logica che non sono più necessarie. Se possibile, disabilitare tutte le altre risorse che non sono necessarie costantemente attive.

## <a name="next-steps"></a>Passaggi successivi

* [Ottimizzare gli investimenti per il cloud con Gestione costi di Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Gestisci i costi con l'analisi dei costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Evitare i costi imprevisti](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Segui il corso di apprendimento guidato per [Gestione costi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)