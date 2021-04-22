---
title: Pianificare e gestire i costi
titleSuffix: Azure Machine Learning
description: Pianificare e gestire i costi per Azure Machine Learning con l'analisi dei costi in portale di Azure. Altre suggerimenti per risparmiare sui costi per ridurre i costi durante la creazione di modelli di Machine Learning.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: d8f74e438175758b1f09e1809b5eba15c1b26c3c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873632"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Pianificare e gestire i costi per Azure Machine Learning

Questo articolo descrive come pianificare e gestire i costi per Azure Machine Learning. In primo luogo, usare il calcolatore dei prezzi di Azure per pianificare i costi prima di aggiungere le risorse. Successivamente, quando si aggiungono le risorse di Azure, esaminare i costi stimati. Usare infine suggerimenti per risparmiare sui costi durante il training del modello con cluster di calcolo Azure Machine Learning gestiti.

Dopo aver iniziato a usare le Azure Machine Learning, usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. Esaminare anche i costi previsti e identificare le tendenze di spesa per identificare le aree in cui potrebbe essere necessario agire.

Comprendere che i costi per Azure Machine Learning sono solo una parte dei costi mensili nella fattura di Azure. Se si usano altri servizi di Azure, vengono addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti. Questo articolo illustra come pianificare e gestire i costi per Azure Machine Learning. Dopo aver familiarità con la gestione dei costi per Azure Machine Learning, applicare metodi simili per gestire i costi per tutti i servizi di Azure usati nella sottoscrizione.

Quando si esegue il training dei modelli di Machine Learning, usare i cluster Azure Machine Learning di calcolo gestiti per sfruttare i vantaggi di suggerimenti più a basso costo:

* Configurare i cluster di training per la scalabilità automatica
* Impostare quote per la sottoscrizione e le aree di lavoro
* Impostare i criteri di terminazione per l'esecuzione del training
* Usare macchine virtuali con priorità bassa
* Usare un'istanza di macchina virtuale riservata di Azure

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi supporta diversi tipi di account di Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per visualizzare i dati relativi ai costi, è necessario disporre almeno dell''accesso in lettura per l''account Azure. 

Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Stimare i costi prima di usare Azure Machine Learning

Usare il [calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) per stimare i costi prima di creare le risorse in un account Azure Machine Learning azure. A sinistra selezionare **AI + Machine Learning** e quindi selezionare **Azure Machine Learning** per iniziare.  

Lo screenshot seguente mostra la stima dei costi usando il calcolatore:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Stima dei costi nel calcolatore di Azure":::

Quando si aggiungono nuove risorse all'area di lavoro, tornare a questo calcolatore e aggiungere la stessa risorsa qui per aggiornare le stime dei costi.

Per altre informazioni, vedere Azure Machine Learning [prezzi.](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Informazioni sul modello di fatturazione completo per Azure Machine Learning

Azure Machine Learning viene eseguito nell'infrastruttura di Azure che accumula i costi Azure Machine Learning quando si distribuisce la nuova risorsa. È importante comprendere che l'infrastruttura aggiuntiva potrebbe accumulare costi. È necessario gestire tale costo quando si apportano modifiche alle risorse distribuite. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Costi che in genere si accumulano con Azure Machine Learning

Quando si creano risorse per un'area Azure Machine Learning, vengono create anche le risorse per altri servizi di Azure. Ad esempio:

* [Registro Azure Container](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Account di base
* [Archiviazione BLOB in blocchi di Azure](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (utilizzo generico v1)
* [Insieme di credenziali di chiave](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>I costi potrebbero accumularsi dopo l'eliminazione delle risorse

Quando si elimina un'Azure Machine Learning di lavoro nel portale di Azure o con l'interfaccia della riga di comando di Azure, le risorse seguenti continuano a esistere. Continuano a accumulare costi fino a quando non vengono eliminati.

* Registro Azure Container
* Archiviazione BLOB in blocchi di Azure
* Key Vault
* Application Insights

Per eliminare l'area di lavoro insieme a queste risorse dipendenti, usare l'SDK:

```python
ws.delete(delete_dependent_resources=True)
```

Se si creano servizio Azure Kubernetes nell'area di lavoro o se si collegano risorse di calcolo all'area di lavoro, è necessario eliminarle separatamente in [portale di Azure](https://portal.azure.com).

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Uso del credito per il pagamento anticipato di Azure con Azure Machine Learning

È possibile pagare i costi Azure Machine Learning con il credito per il pagamento anticipato di Azure (in precedenza denominato impegno monetario). Tuttavia, non è possibile usare il pagamento anticipato di Azure per pagare gli addebiti per prodotti e servizi di terze parti, inclusi quelli del Azure Marketplace.


## <a name="create-budgets"></a>Creare i budget

È possibile creare [budget per](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) [](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) gestire i costi e creare avvisi che inviino automaticamente notifiche agli stakeholder in caso di anomalie di spesa e rischi di sovraspendenza. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni di Azure e i gruppi di risorse, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. 

È possibile creare budget con filtri per risorse o servizi specifici in Azure se si vuole una maggiore granularità nel monitoraggio. I filtri consentono di evitare di creare accidentalmente nuove risorse con costi aggiuntivi. Per altre informazioni sulle opzioni di filtro quando si crea un budget, vedere [Opzioni di raggruppamento e filtro.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="export-cost-data"></a>Esportare i dati relativi ai costi

È anche possibile [esportare i dati sui costi](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Ciò è utile quando è necessario o altri utenti per eseguire un'analisi dei dati aggiuntiva per i costi. Ad esempio, i team finanziari possono analizzare i dati usando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è il modo consigliato per recuperare i set di dati sui costi.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Altri modi per gestire e ridurre i costi per Azure Machine Learning

Usare questi suggerimenti per contenere i costi delle risorse di calcolo di Machine Learning.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Usare Azure Machine Learning cluster di calcolo (AmlCompute)

Con i dati in continua evoluzione, è necessario eseguire il training e la rieticazione dei modelli veloci e semplificati per mantenere modelli accurati. Tuttavia, il training continuo ha un costo, soprattutto per i modelli di Deep Learning nelle GPU. 

Azure Machine Learning utenti possono usare il cluster Azure Machine Learning di calcolo gestito, denominato anche AmlCompute. AmlCompute supporta un'ampia gamma di opzioni di GPU e CPU. AmlCompute è ospitato internamente per conto della sottoscrizione Azure Machine Learning. Offre la stessa sicurezza, conformità e governance di livello aziendale su scala cloud IaaS di Azure.

Poiché questi pool di calcolo si trova all'interno dell'infrastruttura IaaS di Azure, è possibile distribuire, ridimensionare e gestire il training con gli stessi requisiti di sicurezza e conformità del resto dell'infrastruttura.  Queste distribuzioni si verificano nella sottoscrizione e rispettano le regole di governance. Altre informazioni [sull'Azure Machine Learning calcolo.](how-to-create-attach-compute-cluster.md)

### <a name="configure-training-clusters-for-autoscaling"></a>Configurare i cluster di training per la scalabilità automatica

La scalabilità automatica dei cluster in base ai requisiti del carico di lavoro consente di ridurre i costi in modo da usare solo le risorse necessarie.

I cluster AmlCompute sono progettati per la scalabilità dinamica in base al carico di lavoro. Il cluster può essere ridimensionato fino al numero massimo di nodi configurato. Al termine di ogni esecuzione, il cluster rilascerà i nodi e verrà ridimensionato in base al numero minimo di nodi configurato.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

È anche possibile configurare la quantità di tempo in cui il nodo è inattivo prima della scalabilità verso il basso. Per impostazione predefinita, il tempo di inattività prima della scalabilità orizzontale è impostato su 120 secondi.

+ Se si esegue una sperimentazione meno iterativa, ridurre questo tempo per risparmiare sui costi.
+ Se si esegue una sperimentazione di sviluppo/test altamente iterativa, potrebbe essere necessario aumentare il tempo in modo da non pagare per aumentare e ridurre costantemente le prestazioni dopo ogni modifica allo script o all'ambiente di training.

I cluster AmlCompute possono essere configurati per i requisiti del carico di lavoro mutevoli in portale di Azure, usando la classe [AmlCompute SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute), l'interfaccia della riga di comando [di AmlCompute](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute), con le [API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Impostare quote per le risorse

AmlCompute include una [configurazione di quota (o limite).](how-to-manage-quotas.md#azure-machine-learning-compute) Questa quota è in base alla famiglia di macchine virtuali (ad esempio, serie Dv2, serie NCv3) e varia in base all'area per ogni sottoscrizione. Le sottoscrizioni iniziano con valori predefiniti di piccole dimensioni per iniziare, ma usare questa impostazione per controllare la quantità di risorse Amlcompute disponibili per l'avvio nella sottoscrizione. 

Configurare anche la [quota a livello di area di lavoro per famiglia di macchine](how-to-manage-quotas.md#workspace-level-quotas)virtuali , per ogni area di lavoro all'interno di una sottoscrizione. In questo modo è possibile avere un controllo più granulare sui costi che ogni area di lavoro potrebbe potenzialmente sostenere e limitare determinate famiglie di macchine virtuali. 

Per impostare le quote a livello di area di lavoro, iniziare nel [portale di Azure](https://portal.azure.com).  Selezionare un'area di lavoro nella sottoscrizione e selezionare **Usages + quotas (Utilizzi** e quote) nel riquadro sinistro. Selezionare quindi la **scheda Configura quote** per visualizzare le quote. Per impostare la quota sono necessari privilegi nell'ambito della sottoscrizione, perché si tratta di un'impostazione che interessa più aree di lavoro.

### <a name="set-run-autotermination-policies"></a>Impostare i criteri di esecuzione automatica 

In alcuni casi, è consigliabile configurare le esecuzioni di training per limitarne la durata o terminarle in anticipo. Ad esempio, quando si usa Azure Machine Learning'ottimizzazione degli iperparametrami predefinita o l'apprendimento automatico.

Ecco alcune opzioni disponibili:
* Definire un parametro denominato in RunConfiguration per controllare la durata massima che un'esecuzione può estendere nel calcolo scelto (calcolo cloud locale `max_run_duration_seconds` o remoto).
* Per [l'ottimizzazione degli](how-to-tune-hyperparameters.md#early-termination)iperparamenzi, definire un criterio di terminazione anticipata da un criterio di interruzione, un criterio di arresto mediano o un criterio di selezione troncamento. Per controllare ulteriormente le sweep degli iperparamezzi, usare parametri come `max_total_runs` o `max_duration_minutes` .
* Per [l'apprendimento automatico,](how-to-configure-auto-train.md#exit)impostare criteri di terminazione simili usando il  `enable_early_stopping` flag . Usare anche proprietà come e `iteration_timeout_minutes` per controllare la durata massima di `experiment_timeout_minutes` un'esecuzione o per l'intero esperimento.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Usare macchine virtuali con priorità bassa

Azure consente di usare la capacità inutilizzata in eccesso Low-Priority macchine virtuali tra set di scalabilità di macchine virtuali, Batch e il Machine Learning virtuale. Queste allocazioni sono pre-svuotabili, ma hanno un prezzo ridotto rispetto alle macchine virtuali dedicate. In generale, è consigliabile usare le Low-Priority per i carichi di lavoro di Batch. È consigliabile usarli anche nei casi in cui le interruzioni possono essere recuperate tramite i resubmit (per l'inferenza batch) o tramite riavvii (per il training di Deep Learning con checkpoint).

Low-Priority vm hanno una singola quota separata dal valore di quota dedicato, ovvero in base alla famiglia di macchine virtuali. Altre [informazioni sulle quote AmlCompute](how-to-manage-quotas.md).

 Low-Priority le macchine virtuali non funzionano per le istanze di calcolo, perché devono supportare esperienze di notebook interattive.

### <a name="use-reserved-instances"></a>Usare le istanze riservate

Un altro modo per risparmiare sulle risorse di calcolo è l'istanza di macchina virtuale riservata di Azure. Con questa offerta si esegue il commit a un anno o a tre anni. Questi sconti variano fino al 72% dei prezzi con pagamento in base al consumo e vengono applicati direttamente alla fattura mensile di Azure.

Azure Machine Learning compute supporta intrinsecamente le istanze riservate. Se si acquista un'istanza riservata di un anno o di tre anni, verrà applicato automaticamente lo sconto Azure Machine Learning calcolo gestito.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come ottimizzare l'investimento nel cloud con Gestione costi di Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Altre informazioni sulla gestione dei costi con [l'analisi dei costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Informazioni su come evitare [costi imprevisti.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Eseguire il [corso di](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) apprendimento guidato di Gestione costi.
