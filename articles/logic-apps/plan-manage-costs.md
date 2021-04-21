---
title: Pianificare la gestione dei costi per App per la logica di Azure
description: Informazioni su come pianificare e gestire i costi per App per la logica di Azure usando l'analisi dei costi nel portale di Azure
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 03/24/2021
ms.openlocfilehash: ec2e1098df4c21704ee7c17852b893630cd3fd27
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761818"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Pianificare e gestire i costi per App per la logica di Azure

Questo articolo illustra come pianificare e gestire i costi per App per la logica di Azure. Prima di creare o aggiungere risorse usando questo servizio, stimare i costi usando il calcolatore prezzi di Azure. Dopo aver avviato l'uso delle risorse di App per la logica, è possibile impostare i budget e monitorare i costi [usando Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per identificare le aree in cui potrebbe essere necessario agire, è anche possibile esaminare i costi previsti e monitorare le tendenze di spesa.

Tenere presente che i costi per Le app per la logica fanno solo parte dei costi mensili nella fattura di Azure. Anche se questo articolo illustra come stimare e gestire i costi per App per la logica, vengono addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi tutti i servizi di terze parti. Dopo aver familiarità con la gestione dei costi per App per la logica, è possibile applicare metodi simili per gestire i costi per tutti i servizi di Azure usati nella sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) supporta la maggior parte dei tipi di account Azure. Per visualizzare tutti i tipi di account supportati, vedere [Informazioni sui dati di Gestione costi.](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Per visualizzare i dati relativi ai costi, è necessario disporre almeno dell''accesso in lettura per l''account Azure.

Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Informazioni sul modello di fatturazione

App per la logica di Azure viene eseguito nell'infrastruttura di Azure [che accumula i costi](https://azure.microsoft.com/pricing/details/logic-apps/) quando si distribuiscono nuove risorse. Assicurarsi di aver compreso il modello di fatturazione per il servizio App per la logica insieme alle risorse di [Azure](logic-apps-pricing.md)correlate e di gestire i costi dovuti a queste dipendenze quando si apportano modifiche alle risorse distribuite.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Costi che in genere si accumulano con App per la logica di Azure

Il servizio App per la logica applica modelli di determinazione prezzi diversi, in base alle risorse create e usate:

* Le risorse dell'app per la logica create ed eseguite nel servizio App per la logica multi-tenant usano un modello [di determinazione dei prezzi a consumo.](../logic-apps/logic-apps-pricing.md#consumption-pricing)

* Le risorse dell'app per la logica create ed eseguite in un ambiente del servizio di integrazione [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) usano un [modello di determinazione prezzi fisso.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

Ecco altre risorse che comportano costi quando vengono create per l'uso con le app per la logica:

* Un [account di integrazione](../logic-apps/logic-apps-pricing.md#integration-accounts) è una risorsa separata creata e collegata ad app per la logica per la creazione di integrazioni B2B. Gli account di integrazione usano [un modello tariffario fisso](../logic-apps/logic-apps-pricing.md#integration-accounts) in cui la tariffa è basata sul tipo di account di integrazione o sul *livello* in uso.

* Un [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) è una risorsa separata creata come percorso di distribuzione per le app per la logica che necessitano dell'accesso diretto alle risorse in una rete virtuale. Gli ISE usano [un modello di prezzi fisso](../logic-apps/logic-apps-pricing.md#fixed-pricing) in cui la tariffa è basata sullo SKU ISE creato e su altre impostazioni.

* Un [connettore personalizzato](../logic-apps/logic-apps-pricing.md#consumption-pricing) è una risorsa separata creata per un'API REST senza connettore predefinito da usare nelle app per la logica. Le esecuzioni di connettori personalizzati usano un [modello di prezzi a consumo,](../logic-apps/logic-apps-pricing.md#consumption-pricing) tranne quando vengono usate in un ise.

* Nel servizio App per la logica multi-tenant, la conservazione [dei dati](../logic-apps/logic-apps-pricing.md#data-retention) e l'utilizzo dell'archiviazione accumulano i costi usando un modello a [prezzi fissi.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Ad esempio, gli input e gli output della cronologia di esecuzione vengono mantenuti nell'archiviazione dietro le quinte, che differisce dalle risorse di archiviazione create, gestite e a cui si accede in modo indipendente dall'app per la logica.

  In un ISE, la conservazione dei dati e l'utilizzo dello spazio di archiviazione non comportano costi.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>I costi potrebbero accumularsi dopo l'eliminazione delle risorse

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Dopo aver eliminato un'app per la logica, il servizio App per la logica non crea né esegue nuove istanze del flusso di lavoro. Tuttavia, tutte le esecuzioni in corso e in sospeso continuano fino al termine. A seconda del numero di esecuzioni, questo processo potrebbe richiedere del tempo. Per altre informazioni, vedere [Gestire le app per la logica.](manage-logic-apps-with-azure-portal.md#delete-logic-apps)

Se si dispone di queste risorse dopo l'eliminazione di un'app per la logica, queste risorse continuano a esistere e accumulano costi fino a quando non vengono eliminate:

* Risorse di Azure create e gestite in modo indipendente dall'app per la logica che si connette a tali risorse, ad esempio app per le funzioni di Azure, hub eventi, griglie eventi e così via

* Account di integrazione

* Ambienti del servizio di integrazione (ISE)

  Se si [elimina un ambiente del servizio di integrazione,](ise-manage-integration-service-environment.md#delete-ise)la rete virtuale di Azure associata, le subnet e altre risorse correlate continuano a esistere. Dopo aver eliminato l'ISE, potrebbe essere necessario attendere fino a un numero specifico di ore prima di provare a eliminare la rete virtuale o le subnet.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Uso del credito monetario con App per la logica di Azure

È possibile pagare i costi App per la logica di Azure con il credito dell'impegno monetario EA. Tuttavia, non è possibile usare il credito dell'impegno monetario EA per pagare gli addebiti per prodotti e servizi di terze parti, inclusi quelli del Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Stimare i costi

Prima di creare le risorse con App per la logica di Azure, stimare i costi usando il calcolatore [prezzi di Azure.](https://azure.microsoft.com/pricing/calculator/) Per altre informazioni, vedere [Modello di determinazione prezzi per App per la logica di Azure](../logic-apps/logic-apps-pricing.md).

1. Nella pagina [del calcolatore prezzi di](https://azure.microsoft.com/pricing/calculator/) Azure selezionare Integration  >  **App per la logica di Azure (Integrazione App per la logica di Azure).**

   ![Screenshot che mostra il calcolatore prezzi di Azure con l'opzione "App per la logica di Azure" selezionata.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Scorrere verso il basso la pagina fino a visualizzare il calcolatore App per la logica di Azure prezzi. Nelle varie sezioni relative alle risorse di Azure direttamente correlate a App per la logica di Azure immettere il numero di risorse che si prevede di usare e il numero di intervalli in cui è possibile usare tali risorse.

   Questo screenshot mostra una stima dei costi di esempio usando il calcolatore:

   ![Esempio che mostra il costo stimato nel calcolatore prezzi di Azure](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Per aggiornare le stime dei costi durante la creazione e l'uso di nuove risorse correlate, tornare a questo calcolatore e aggiornare tali risorse qui.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Creare budget e avvisi

Per gestire in modo proattivo i costi per l'account [](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) o la [](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) sottoscrizione di Azure, è possibile creare [budget](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) e avvisi usando il servizio e le funzionalità di Gestione costi di Azure e fatturazione.  I budget e gli avvisi vengono creati per le sottoscrizioni di Azure e i gruppi di risorse, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva.

In base alla spesa rispetto alle soglie di budget e costi, gli avvisi inviano automaticamente notifiche agli stakeholder in caso di anomalie di spesa e rischi di spesa in evasa. Se si vuole una maggiore granularità nel monitoraggio, è anche possibile creare budget che usano filtri per risorse o servizi specifici in Azure. I filtri consentono di assicurarsi di non creare accidentalmente nuove risorse con costi aggiuntivi. Per altre informazioni sulle opzioni di filtro, vedere [Opzioni di raggruppamento e filtro.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Monitorare i costi

I costi delle unità di utilizzo delle risorse variano in base a intervalli di tempo, ad esempio secondi, minuti, ore e giorni, oppure in base all'utilizzo delle unità, ad esempio byte, megabyte e così via. Alcuni esempi sono per giorno, mese corrente e precedente e anno. Il passaggio a visualizzazioni più lunghe nel tempo può aiutare a identificare le tendenze di spesa. Quando si usano le funzionalità di analisi dei costi, è possibile visualizzare i costi come grafici e tabelle in vari intervalli di tempo. Se sono stati creati budget e previsioni dei costi, è anche possibile trovare facilmente dove vengono superati i budget e potrebbe essere stato superato il limite di spese.

Dopo aver avviato l'incorso dei costi per le risorse che creano o iniziano a usare in Azure, è possibile esaminare e monitorare questi costi nei modi seguenti:

* [Monitorare le esecuzioni delle app per la logica e l'utilizzo dello](#monitor-billing-metrics) spazio di archiviazione usando Monitoraggio di Azure

* Eseguire [l'analisi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) dei costi [usando Gestione costi di Azure e fatturazione](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Monitorare le esecuzioni delle app per la logica e l'utilizzo dello spazio di archiviazione

Usando Monitoraggio di Azure, è possibile visualizzare queste metriche per un'app per la logica specifica:

* Esecuzioni di azioni fatturabili
* Esecuzioni di trigger fatturabili
* Utilizzo della fatturazione per le esecuzioni di operazioni native
* Utilizzo della fatturazione per le esecuzioni di connettori standard
* Utilizzo della fatturazione per l'utilizzo dello spazio di archiviazione
* Esecuzioni fatturabili totali

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Visualizzare le metriche di esecuzione e utilizzo dello spazio di archiviazione

1. Nel portale di Azure trovare e aprire l'app per la logica. Nel menu dell'app per la logica, in **Monitoraggio,** selezionare **Metriche.**

1. Nel riquadro di destra, in **Titolo** grafico, nella barra delle metriche aprire l'elenco **Metrica** e selezionare la metrica desiderata.

   > [!NOTE]
   > Il consumo di archiviazione viene misurato come numero di unità di archiviazione (GB) che l'app per la logica usa e viene fatturato. Le esecuzioni che usano meno di 500 MB nello spazio di archiviazione potrebbero non essere visualizzate nella visualizzazione di monitoraggio, ma vengono comunque fatturate.

   ![Screenshot che mostra il riquadro Metriche con l'elenco "Metrica" aperto.](./media/logic-apps-pricing/select-metric.png)

1. Nell'angolo superiore destro del riquadro selezionare il periodo di tempo desiderato.

1. Per visualizzare altri dati sull'utilizzo dello spazio di archiviazione, in particolare le dimensioni di input e output delle azioni nella cronologia di esecuzione dell'app per la logica, [seguire questa procedura.](#view-input-output-sizes)

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Visualizzare le dimensioni di input e output delle azioni nella cronologia di esecuzione

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Nel menu dell'app per la logica selezionare **Panoramica.**

1. Nel riquadro di destra, in Cronologia **esecuzioni**, selezionare l'esecuzione che contiene gli input e gli output da visualizzare.

1. In **Esecuzione dell'app per la** logica selezionare Dettagli **esecuzione.**

1. Nel riquadro **dei dettagli dell'esecuzione dell'app** per la logica, nella tabella azioni, in cui sono elencati lo stato e la durata di ogni azione, selezionare l'azione che si vuole visualizzare.

1. Nel riquadro **Azione dell'app per** la logica trovare le dimensioni per gli input e gli output dell'azione. In **Collegamento input e** Output **trovare** i collegamenti a tali input e output.

   > [!NOTE]
   > Per i cicli, solo le azioni di primo livello mostrano le dimensioni per i relativi input e output. Per le azioni all'interno di cicli annidati, gli input e gli output mostrano dimensioni pari a zero e nessun collegamento.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Eseguire l'analisi dei costi usando Gestione costi di Azure fatturazione

Per esaminare i costi per il servizio App per la logica in base [](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a un ambito specifico, ad esempio una sottoscrizione di Azure, è possibile usare le funzionalità di analisi dei costi in Gestione costi di Azure [e Fatturazione.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

1. Nel portale di Azure aprire l'ambito desiderato, ad esempio la sottoscrizione di Azure. Nel menu a sinistra, in **Gestione costi,** selezionare **Analisi dei costi.**

   Quando si apre il riquadro di analisi dei costi per la prima volta, il grafico superiore mostra i costi di utilizzo effettivi e previsti per tutti i servizi nella sottoscrizione per il mese corrente.

   ![Screenshot che mostra il portale di Azure e l'analisi dei costi con un esempio dei costi effettivi e previsti in una sottoscrizione.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Per modificare gli ambiti, **nella** barra dei filtri del riquadro Analisi dei costi selezionare il **filtro Ambito.** Nel riquadro **Seleziona** ambito passare all'ambito desiderato.

   Sotto, i grafici ad anello mostrano i costi correnti per servizi di Azure, per area di Azure (località) e per gruppo di risorse.

   ![Screenshot che mostra il portale di Azure e l'analisi dei costi con grafici ad anello di esempio per servizi, aree e gruppi di risorse.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Per filtrare il grafico in base a un'area specifica, ad esempio un servizio o una risorsa, nella barra dei filtri selezionare **Aggiungi filtro.**

1. Nell'elenco a sinistra selezionare il tipo di filtro, ad esempio Nome **servizio**. Nell'elenco a destra selezionare il filtro, ad esempio App **per la logica.** Al termine, selezionare il segno di spunta verde.

   ![Screenshot che mostra il portale di Azure e l'analisi dei costi con le selezioni del filtro.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Ad esempio, di seguito è riportato il risultato per il servizio App per la logica:

   ![Screenshot che mostra il portale di Azure e l'analisi dei costi con i risultati filtrati in base alle "app per la logica".](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Esportare i dati relativi ai costi

Quando è necessario eseguire un'analisi più approfondita dei dati sui costi, è possibile [esportare i dati sui costi](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Ad esempio, un team finanziario può analizzare questi dati usando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è il modo consigliato per recuperare i set di dati sui costi.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Altri modi per gestire e ridurre i costi

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Per ridurre i costi relativi ai criteri di protezione della logica e alle risorse correlate, provare queste opzioni:

* Se possibile, usare [trigger](../connectors/built-in.md)e azioni predefiniti, che hanno un costo inferiore per l'esecuzione rispetto ai trigger e alle azioni [del connettore gestito.](../connectors/managed.md)

  Ad esempio, potrebbe essere possibile ridurre i costi quando si accede ad altre risorse usando l'azione [HTTP](../connectors/connectors-native-http.md) o chiamando una funzione creata usando il servizio [Funzioni di Azure](../azure-functions/functions-overview.md) e usando l'azione Funzioni di Azure [predefinita](../logic-apps/logic-apps-azure-functions.md). Tuttavia, l Funzioni di Azure comporta anche costi, quindi assicurarsi di confrontare le opzioni.

* [Specificare condizioni di trigger precise per](logic-apps-workflow-actions-triggers.md#trigger-conditions) l'esecuzione di un flusso di lavoro.

  Ad esempio, è possibile specificare che un trigger viene attivato solo quando il sito Web di destinazione restituisce un errore interno del server. Nella definizione JSON del trigger usare la proprietà per specificare una condizione che fa riferimento al codice di stato `conditions` del trigger.

* Se un trigger ha una versione di polling e una versione di webhook, provare la versione del webhook, che attende che l'evento specificato si verifica prima della generazione, anziché controllare regolarmente l'evento.

* Chiamare l'app per la logica tramite un altro servizio in modo che il trigger sia attivato solo quando il flusso di lavoro deve essere eseguito.

  Ad esempio, è possibile chiamare l'app per la logica da una funzione creata ed eseguita usando il Funzioni di Azure servizio. Ad esempio, vedere [Chiamare o attivare app per la logica usando Funzioni di Azure e bus di servizio di Azure](logic-apps-scenario-function-sb-trigger.md).

* [Disabilitare le](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) app per la logica che non devono essere eseguite costantemente o eliminare app per [la logica](manage-logic-apps-with-azure-portal.md#delete-logic-apps) che non sono più necessarie. Se possibile, disabilitare tutte le altre risorse che non sono necessarie costantemente attive.

## <a name="next-steps"></a>Passaggi successivi

* [Ottimizzare gli investimenti per il cloud con Gestione costi di Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Gestire i costi usando l'analisi dei costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Evitare i costi imprevisti](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Eseguire il [corso di apprendimento](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guidato su Gestione costi