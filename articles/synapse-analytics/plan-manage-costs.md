---
title: Pianificare la gestione dei costi per Azure sinapsi Analytics
description: Informazioni su come pianificare e gestire i costi per Azure sinapsi Analytics usando l'analisi dei costi nel portale di Azure.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: e62d494989c86b4c0eab9cdbd51b68fc49fffe76
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681563"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Pianificare e gestire i costi per Azure sinapsi Analytics

Questo articolo descrive come pianificare e gestire i costi per Azure sinapsi Analytics. Usare prima di tutto il calcolatore dei prezzi di Azure per pianificare i costi della sinapsi di Azure prima di aggiungere le risorse per il servizio per stimare i costi. Successivamente, quando si aggiungono le risorse di Azure sinapsi, rivedere i costi stimati.

Dopo aver iniziato a usare le risorse di Azure sinapsi, usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. È anche possibile esaminare i costi previsti e identificare le tendenze di spesa per identificare le aree in cui si vuole agire. I costi per le sinapsi di Azure sono solo una parte dei costi mensili nella fattura di Azure. Anche se in questo articolo viene illustrato come pianificare e gestire i costi per la sinapsi di Azure, vengono addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti.

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi in gestione costi supporta la maggior parte dei tipi di account Azure, ma non tutti. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per visualizzare i dati sui costi, è necessario almeno l'accesso in lettura per un account Azure. Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Stimare i costi prima di usare Azure sinapsi Analytics

Usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per stimare i costi prima di aggiungere Azure sinapsi Analytics.

La sinapsi di Azure dispone di diverse risorse con costi diversi, come illustrato nella stima dei costi riportata di seguito. 

![Esempio che mostra il costo stimato nel calcolatore dei prezzi di Azure](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Informazioni sul modello di fatturazione completo per l'analisi delle sinapsi di Azure

La sinapsi di Azure viene eseguita nell'infrastruttura di Azure che accumula i costi insieme alla sinapsi di Azure quando si distribuisce la nuova risorsa. È importante comprendere che l'infrastruttura aggiuntiva potrebbe accumulare costi. È necessario gestire questo costo quando si apportano modifiche alle risorse distribuite. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Costi che in genere si accumulano con Azure sinapsi Analytics

Quando si creano risorse per la sinapsi di Azure, vengono create anche le risorse per altri servizi di Azure. e comprendono:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Potrebbero maturare costi dopo l'eliminazione delle risorse

Dopo aver eliminato le risorse di Azure sinapsi, le risorse seguenti potrebbero continuare a esistere. Continuano a accumulare costi fino a quando non vengono eliminati.

- Data Lake Storage Gen2

### <a name="using-monetary-credit-with-azure-synapse"></a>Uso del credito monetario con la sinapsi di Azure 

È possibile pagare gli addebiti per le sinapsi di Azure con il credito dell'impegno monetario EA. Tuttavia, non è possibile usare il credito dell'impegno monetario EA per pagare gli addebiti per i prodotti e i servizi di terze parti, inclusi quelli di Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Esaminare i costi stimati nel portale di Azure

Quando si creano risorse per Azure sinapsi Analytics, vengono visualizzati i costi stimati. Un'area di lavoro dispone di un pool SQL senza server creato con l'area di lavoro. Il pool SQL senza server non verrà addebitato fino all'esecuzione delle query. Altre risorse, ad esempio i pool SQL dedicati e i pool di Apache Spark senza server, dovranno essere create all'interno dell'area di lavoro.

Per creare un <ResourceName> e visualizzare il prezzo stimato:

1. Passare al servizio nel portale di Azure.
2. Creare la risorsa.
3. Esaminare il prezzo stimato indicato nel riepilogo.
4. Terminare la creazione della risorsa.

![Esempio che mostra i costi stimati durante la creazione di una risorsa](./media/plan-manage-costs/create-workspace-cost.png)


Se la sottoscrizione di Azure ha un limite di spesa, Azure impedisce la spesa rispetto all'importo del credito. Quando si creano e si usano le risorse di Azure, vengono usati i crediti. Quando si raggiunge il limite di credito, le risorse distribuite sono disabilitate per il resto del periodo di fatturazione. Non è possibile modificare il limite di credito, ma è possibile rimuoverlo. Per altre informazioni sui limiti di spesa, vedere [limite di spesa di Azure](https://docs.microsoft.com/azure/billing/billing-spending-limit).

## <a name="monitor-costs"></a>Monitorare i costi

Quando si usano le risorse della sinapsi di Azure, si incorrono i costi. I costi delle unità di utilizzo delle risorse di Azure variano in base a intervalli di tempo (secondi, minuti, ore e giorni) o all'utilizzo di unità (byte, megabyte e così via). Non appena si inizia a usare le risorse nella sinapsi di Azure, i costi sono sostenuti ed è possibile vedere i costi nell' [analisi](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)dei costi.

Quando si usa l'analisi dei costi, è possibile visualizzare i costi per l'analisi delle sinapsi di Azure in grafici e tabelle per intervalli di tempo diversi. Alcuni esempi sono il giorno, il mese corrente e quello precedente e l'anno. Puoi anche visualizzare i costi rispetto ai budget e ai costi previsti. Il passare a visualizzazioni più lunghe nel tempo può aiutare a identificare le tendenze di spesa. Viene visualizzato il punto in cui potrebbe essersi verificata la sovraspesa. Se sono stati creati budget, è anche possibile vedere dove sono stati superati.

Per visualizzare i costi delle sinapsi di Azure nell'analisi dei costi:

1. Accedere al portale di Azure.
2. Aprire l'ambito, ovvero la sottoscrizione o il gruppo di risorse, nella portale di Azure e selezionare **analisi dei costi** nel menu. Ad esempio, passare a **sottoscrizioni**, selezionare una sottoscrizione dall'elenco e quindi selezionare  **analisi dei costi** nel menu. Selezionare **ambito** per passare a un ambito diverso nell'analisi dei costi.
3. Per impostazione predefinita, i costi per i servizi vengono visualizzati nel primo grafico ad anello. Selezionare l'area nel grafico con l'etichetta sinapsi di Azure.

I costi mensili effettivi vengono visualizzati quando inizialmente si apre l'analisi dei costi. Ecco un esempio che Mostra tutti i costi di utilizzo mensili.

![Esempio che mostra i costi accumulati per una sottoscrizione](./media/plan-manage-costs/actual-monthly-costs.png)

- Per limitare i costi per un singolo servizio, ad esempio le sinapsi di Azure, selezionare **Aggiungi filtro** e quindi selezionare **nome servizio**. Quindi, selezionare **Azure sinapsi Analytics**.

Ecco un esempio che mostra i costi solo per le sinapsi di Azure.

![Esempio che mostra i costi accumulati per ServiceName](./media/plan-manage-costs/filtered-monthly-costs.png)

Nell'esempio precedente viene visualizzato il costo corrente per il servizio. Vengono visualizzati anche i costi per le aree di Azure (località) e i costi di sinapsi di Azure per gruppo di risorse. Da qui è possibile esplorare i costi autonomamente.

## <a name="create-budgets"></a>Creare i budget

È possibile creare [budget](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) per gestire i costi e creare [avvisi](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) che notificano automaticamente agli stakeholder le anomalie di spesa e i rischi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. 

I budget possono essere creati con filtri per risorse o servizi specifici in Azure se si vuole una maggiore granularità nel monitoraggio. I filtri consentono di evitare di creare accidentalmente nuove risorse che costano denaro aggiuntivo. Per ulteriori informazioni sulle opzioni di filtro quando si crea un budget, vedere [Opzioni di gruppo e filtro](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Esportare i dati relativi ai costi

È anche possibile [esportare i dati sui costi](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Questa operazione è utile quando sono necessari o altri utenti per eseguire ulteriori analisi dei dati per i costi. Ad esempio, i team finanziari possono analizzare i dati usando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è la modalità consigliata per recuperare i set di dati di costo.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Altri modi per gestire e ridurre i costi per la sinapsi di Azure 

### <a name="serverless-sql-pool"></a>Pool SQL serverless

Per altre informazioni sui costi per il pool SQL senza server, vedere [gestione dei costi per il pool SQL senza server in Azure sinapsi Analytics](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>Pool SQL dedicato

È possibile controllare i costi per un pool SQL dedicato sospendendo la risorsa quando non è in uso. Ad esempio, se non si usa il database durante la notte e nei fine settimana, è possibile sospenderlo in questi intervalli di tempo e riprenderne l'esecuzione durante il giorno. Per altre informazioni [, vedere sospendere e riprendere il calcolo in un pool SQL dedicato tramite il portale di Azure](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

<!-- ### Serverless Apache Spark pool -->

### <a name="data-integration---pipelines-and-data-flows"></a>Integrazione dei dati: pipeline e flussi di dati 

Per altre informazioni sui costi di integrazione dei dati [, vedere Pianificare e gestire i costi per Azure Data Factory](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come ottimizzare l'investimento nel cloud con gestione costi di Azure](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Altre informazioni sulla gestione dei costi con l' [analisi dei costi](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Informazioni su come [evitare costi imprevisti](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Segui il corso di apprendimento guidato per [Gestione costi](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
- Informazioni sulla pianificazione e la gestione dei costi per [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)