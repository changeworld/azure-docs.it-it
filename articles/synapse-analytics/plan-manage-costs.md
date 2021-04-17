---
title: Pianificare la gestione dei costi per Azure Synapse Analytics
description: Informazioni su come pianificare e gestire i costi per Azure Synapse Analytics usando l'analisi dei costi nel portale di Azure.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 15ac6ce6a1a49bbbd15849adec373dd0fcd42c10
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568523"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Pianificare e gestire i costi per Azure Synapse Analytics

Questo articolo descrive come pianificare e gestire i costi per Azure Synapse Analytics. In primo luogo, usare il calcolatore dei prezzi di Azure per pianificare i costi Azure Synapse prima di aggiungere risorse per il servizio per stimare i costi. Successivamente, quando si aggiungono Azure Synapse, esaminare i costi stimati.

Dopo aver iniziato a usare le Azure Synapse, usare le funzionalità di Gestione costi per impostare i budget e monitorare i costi. È anche possibile esaminare i costi previsti e identificare le tendenze di spesa per identificare le aree in cui potrebbe essere necessario agire. I costi Azure Synapse sono solo una parte dei costi mensili nella fattura di Azure. Anche se questo articolo illustra come pianificare e gestire i costi per Azure Synapse, vengono addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti.

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi in Gestione costi supporta la maggior parte dei tipi di account Azure, ma non tutti. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per visualizzare i dati sui costi, è necessario almeno l'accesso in lettura per un account Azure. Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Stimare i costi prima di usare Azure Synapse Analytics

Usare il [calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per stimare i costi prima di aggiungere Azure Synapse Analytics.

Azure Synapse ha varie risorse con addebiti diversi, come illustrato nella stima dei costi riportata di seguito. 

![Esempio che mostra il costo stimato nel calcolatore prezzi di Azure](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Informazioni sul modello di fatturazione completo per Azure Synapse Analytics

Azure Synapse viene eseguito nell'infrastruttura di Azure che accumula i costi Azure Synapse quando si distribuisce la nuova risorsa. È importante comprendere che l'infrastruttura aggiuntiva potrebbe accumulare costi. È necessario gestire tale costo quando si apportano modifiche alle risorse distribuite. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Costi che in genere si accumulano con Azure Synapse Analytics

Quando si creano risorse per Azure Synapse, vengono create anche le risorse per altri servizi di Azure. e comprendono:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>I costi potrebbero accumularsi dopo l'eliminazione delle risorse

Dopo aver eliminato Azure Synapse risorse, è possibile che le risorse seguenti continuino a esistere. Continuano a accumulare costi fino a quando non vengono eliminati.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Uso del credito di pagamento anticipato di Azure con Azure Synapse 

È possibile pagare gli Azure Synapse con il credito di pagamento anticipato di Azure (in precedenza denominato impegno monetario). Tuttavia, non è possibile usare il credito di pagamento anticipato di Azure per pagare gli addebiti per prodotti e servizi di terze parti, inclusi quelli della Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Esaminare i costi stimati nel portale di Azure

Quando si creano risorse per Azure Synapse Analytics, vengono visualizzati i costi stimati. Un'area di lavoro ha un pool SQL serverless creato con l'area di lavoro. Il pool SQL senza server non comporta addebiti fino a quando non si eseguono query. Altre risorse, ad esempio pool SQL dedicati e pool Apache Spark serverless, dovranno essere create all'interno dell'area di lavoro.

Per creare un <ResourceName> oggetto e visualizzare il prezzo stimato:

1. Passare al servizio nel portale di Azure.
2. Creare la risorsa.
3. Esaminare il prezzo stimato visualizzato nel riepilogo.
4. Completare la creazione della risorsa.

![Esempio che mostra i costi stimati durante la creazione di una risorsa](./media/plan-manage-costs/create-workspace-cost.png)


Se la sottoscrizione di Azure ha un limite di spesa, Azure impedisce di spendere oltre l'importo del credito. Quando si creano e si usano le risorse di Azure, vengono usati i crediti. Quando si raggiunge il limite di credito, le risorse distribuite vengono disabilitate per il resto del periodo di fatturazione. Non è possibile modificare il limite di credito, ma è possibile rimuoverlo. Per altre informazioni sui limiti di spesa, vedere Limite [di spesa di Azure.](../cost-management-billing/manage/spending-limit.md)

## <a name="monitor-costs"></a>Monitorare i costi

Quando si usano Azure Synapse risorse, si incorre in costi. I costi delle unità di utilizzo delle risorse di Azure variano in base a intervalli di tempo (secondi, minuti, ore e giorni) o in base all'utilizzo delle unità (byte, megabyte e così via). Non appena si inizia a usare le risorse in Azure Synapse, vengono sostenuti costi ed è possibile visualizzare i costi [nell'analisi dei costi.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Quando si usa l'analisi dei costi, i costi Azure Synapse Analytics in grafici e tabelle per intervalli di tempo diversi. Alcuni esempi sono per giorno, mese corrente e precedente e anno. È anche possibile visualizzare i costi rispetto ai budget e ai costi previsti. Il passaggio a visualizzazioni più lunghe nel tempo può aiutare a identificare le tendenze di spesa. E si può vedere dove si è verificato l'overspending. Se sono stati creati budget, è anche possibile vedere facilmente dove vengono superati.

Per visualizzare i Azure Synapse nell'analisi dei costi:

1. Accedere al portale di Azure.
2. Aprire l'ambito, la sottoscrizione o il gruppo di risorse, nel portale di Azure e selezionare **Analisi dei costi** nel menu. Ad esempio, passare **a Sottoscrizioni,** selezionare una sottoscrizione dall'elenco e quindi selezionare  **Analisi dei costi** nel menu. Selezionare **Ambito** per passare a un ambito diverso nell'analisi dei costi.
3. Per impostazione predefinita, i costi per i servizi vengono visualizzati nel primo grafico ad anello. Selezionare l'area nel grafico con etichetta Azure Synapse.

I costi mensili effettivi vengono visualizzati quando si apre inizialmente l'analisi dei costi. Ecco un esempio che mostra tutti i costi di utilizzo mensili.

![Esempio che mostra i costi accumulati per una sottoscrizione](./media/plan-manage-costs/actual-monthly-costs.png)

- Per limitare i costi per un singolo servizio, ad esempio Azure Synapse, selezionare **Aggiungi** filtro e quindi selezionare **Nome servizio**. Selezionare quindi **Azure Synapse Analytics**.

Ecco un esempio che mostra i costi solo per Azure Synapse.

![Esempio che mostra i costi accumulati per ServiceName](./media/plan-manage-costs/filtered-monthly-costs.png)

Nell'esempio precedente viene visualizzato il costo corrente per il servizio. Vengono visualizzati anche i costi per aree di Azure (località) Azure Synapse per gruppo di risorse. Da qui è possibile esplorare i costi in modo personalizzato.

## <a name="create-budgets"></a>Creare i budget

È possibile creare [budget per](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) [](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) gestire i costi e creare avvisi che notificano automaticamente agli stakeholder le anomalie di spesa e i rischi di esinging. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni di Azure e i gruppi di risorse, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. 

I budget possono essere creati con filtri per risorse o servizi specifici in Azure se si vuole una maggiore granularità nel monitoraggio. I filtri consentono di evitare la creazione accidentale di nuove risorse con costi aggiuntivi. Per altre informazioni sulle opzioni di filtro quando si crea un budget, vedere [Opzioni di raggruppamento e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Esportare i dati relativi ai costi

È anche possibile [esportare i dati sui costi](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Ciò è utile quando è necessario o altri utenti per eseguire un'analisi dei dati aggiuntiva per i costi. Ad esempio, i team finanziari possono analizzare i dati usando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è il modo consigliato per recuperare i set di dati dei costi.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Altri modi per gestire e ridurre i costi per Azure Synapse 

### <a name="serverless-sql-pool"></a>Pool SQL serverless

Per altre informazioni sui costi per il pool SQL serverless, vedere Gestione dei costi per [il pool SQL senza server in Azure Synapse Analytics](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>Pool SQL dedicato

È possibile controllare i costi per un pool SQL dedicato sospendere la risorsa quando non è in uso. Ad esempio, se non si usa il database durante la notte e nei fine settimana, è possibile sospenderlo in questi intervalli di tempo e riprenderne l'esecuzione durante il giorno. Per altre informazioni, vedere [Sospendere e riprendere il calcolo nel pool SQL dedicato tramite il portale di Azure](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="serverless-apache-spark-pool"></a>Pool di Apache Spark serverless

Per controllare i costi per il pool di Apache Spark serverless, abilitare la funzionalità di sospensione automatica Apache Spark serverless e impostare il valore di timeout di conseguenza.  Quando si Synapse Studio per lo sviluppo, Studio invia un messaggio keep-alive per mantenere attiva la sessione che è anche configurabile, quindi impostare un valore di timeout breve per la sospensione automatica.  Al termine, chiudere la sessione e il pool Apache Spark verrà automaticamente sospeso una volta raggiunto il valore di timeout.
 
Durante lo sviluppo, creare più Apache Spark di pool di dimensioni diverse.  La Apache Spark definizioni di pool è gratuita e verrà addebitato solo l'utilizzo.  Apache Spark'utilizzo in Azure Synapse viene addebitato per ogni ora di vCore e rierato di un minuto.  Ad esempio, usare pool di piccole dimensioni per lo sviluppo e la convalida del codice mentre si usano pool di dimensioni maggiori per i test delle prestazioni.


### <a name="data-integration---pipelines-and-data-flows"></a>Integrazione dei dati: pipeline e flussi di dati 

Per altre informazioni sui costi di integrazione dei dati, vedere [Pianificare e gestire i costi per Azure Data Factory](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come ottimizzare gli investimenti nel cloud con Gestione costi di Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Altre informazioni sulla gestione dei costi con [l'analisi dei costi.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Informazioni su come evitare [costi imprevisti.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Eseguire il [corso di](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) apprendimento guidato su Gestione costi.
- Informazioni sulla pianificazione e sulla gestione dei costi [per Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)