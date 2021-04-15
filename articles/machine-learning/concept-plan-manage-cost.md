---
title: Pianificare e gestire i costi
titleSuffix: Azure Machine Learning
description: Pianificare e gestire i costi per Azure Machine Learning con l'analisi dei costi in portale di Azure. Informazioni su altri suggerimenti per ridurre i costi durante la creazione di modelli di Machine Learning.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 39c649cccdf159810ad01c2312c4ea4837d9f4fc
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478642"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Pianificare e gestire i costi per Azure Machine Learning

Questo articolo descrive come pianificare e gestire i costi per Azure Machine Learning. Per prima cosa, si usa il calcolatore prezzi di Azure per pianificare i costi prima di aggiungere le risorse. Successivamente, quando si aggiungono le risorse di Azure, esaminare i costi stimati. Usare infine suggerimenti per il risparmio sui costi durante il training del modello con cluster di calcolo Azure Machine Learning di calcolo gestiti.

Dopo aver iniziato a usare le Azure Machine Learning, usare le funzionalità di gestione dei costi per impostare budget e monitorare i costi. Esaminare anche i costi previsti e identificare le tendenze di spesa per identificare le aree in cui potrebbe essere necessario intervenire.

Comprendere che i costi per Azure Machine Learning sono solo una parte dei costi mensili nella fattura di Azure. Se si usano altri servizi di Azure, vengono addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti. Questo articolo illustra come pianificare e gestire i costi per Azure Machine Learning. Dopo aver familiarità con la gestione dei costi per Azure Machine Learning, applicare metodi simili per gestire i costi per tutti i servizi di Azure usati nella sottoscrizione.

Quando si esegue il training dei modelli di Machine Learning, usare Azure Machine Learning cluster di calcolo gestiti per sfruttare i suggerimenti più utili per risparmiare sui costi:

* Configurare i cluster di training per la scalabilità automatica
* Impostare quote per la sottoscrizione e le aree di lavoro
* Impostare i criteri di terminazione nell'esecuzione del training
* Usare macchine virtuali con priorità bassa
* Usare un'istanza di macchina virtuale riservata di Azure

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi supporta diversi tipi di account di Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per visualizzare i dati relativi ai costi, è necessario disporre almeno dell''accesso in lettura per l''account Azure. 

Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Stimare i costi prima di usare Azure Machine Learning

Usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) per stimare i costi prima di creare le risorse in un account Azure Machine Learning azure. A sinistra selezionare **AI + Machine Learning**, quindi selezionare Azure Machine Learning per iniziare.   

Lo screenshot seguente mostra la stima dei costi tramite il calcolatore:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Stima dei costi nel calcolatore di Azure":::

Quando si aggiungono nuove risorse all'area di lavoro, tornare a questo calcolatore e aggiungere la stessa risorsa qui per aggiornare le stime dei costi.

Per altre informazioni, vedere Azure Machine Learning [prezzi.](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Informazioni sul modello di fatturazione completo per Azure Machine Learning

Azure Machine Learning viene eseguito nell'infrastruttura di Azure che accumula i costi Azure Machine Learning quando si distribuisce la nuova risorsa. È importante comprendere che l'infrastruttura aggiuntiva potrebbe accumulare costi. È necessario gestire tale costo quando si apportano modifiche alle risorse distribuite. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Costi che in genere si accumulano con Azure Machine Learning

Quando si creano risorse per un'Azure Machine Learning di lavoro, vengono create anche le risorse per altri servizi di Azure. Ad esempio:

* [Registro Azure Container](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Account Basic
* [Archiviazione BLOB in blocchi di Azure](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (utilizzo generico v1)
* [Insieme di credenziali di chiave](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>I costi potrebbero accumularsi dopo l'eliminazione delle risorse

Quando si elimina un'Azure Machine Learning di lavoro nel portale di Azure o con l'interfaccia della riga di comando di Azure, continuano a esistere le risorse seguenti. Continuano ad accumulare costi fino a quando non vengono eliminati.

* Registro Azure Container
* Archiviazione BLOB in blocchi di Azure
* Key Vault
* Application Insights

Per eliminare l'area di lavoro insieme a queste risorse dipendenti, usare l'SDK:

```python
ws.delete(delete_dependent_resources=True)
```

Se si crea un servizio Azure Kubernetes nell'area di lavoro o si collegano risorse di calcolo all'area di lavoro, è necessario eliminarle separatamente [in portale di Azure](https://portal.azure.com).

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Uso del credito di pagamento anticipato di Azure con Azure Machine Learning

È possibile pagare gli Azure Machine Learning con il credito per il pagamento anticipato di Azure (in precedenza denominato impegno monetario). Tuttavia, non è possibile usare il pagamento anticipato di Azure per pagare gli addebiti per prodotti e servizi di terze parti, inclusi quelli della Azure Marketplace.


## <a name="create-budgets"></a>Creare i budget

È possibile creare [budget per](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) [](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) gestire i costi e creare avvisi che notificano automaticamente agli stakeholder le anomalie di spesa e i rischi di esinging. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni di Azure e i gruppi di risorse, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. 

I budget possono essere creati con filtri per risorse o servizi specifici in Azure se si vuole una maggiore granularità nel monitoraggio. I filtri consentono di evitare la creazione accidentale di nuove risorse con costi aggiuntivi. Per altre informazioni sulle opzioni di filtro quando si crea un budget, vedere [Opzioni di raggruppamento e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Esportare i dati relativi ai costi

È anche possibile [esportare i dati sui costi](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Ciò è utile quando è necessario o altri utenti per eseguire un'analisi dei dati aggiuntiva per i costi. Ad esempio, i team finanziari possono analizzare i dati usando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è il modo consigliato per recuperare i set di dati dei costi.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Altri modi per gestire e ridurre i costi per Azure Machine Learning

Usare questi suggerimenti per contenere i costi delle risorse di calcolo di Machine Learning.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Usare Azure Machine Learning cluster di calcolo (AmlCompute)

Con i dati in continua evoluzione, è necessario un training e un nuovo training del modello veloci e semplificati per mantenere modelli accurati. Tuttavia, il training continuo ha un costo, in particolare per i modelli di Deep Learning nelle GPU. 

Azure Machine Learning utenti possono usare il cluster Azure Machine Learning di calcolo gestito, denominato anche AmlCompute. AmlCompute supporta un'ampia gamma di opzioni GPU e CPU. AmlCompute è ospitato internamente per conto della sottoscrizione da Azure Machine Learning. Offre la stessa sicurezza, conformità e governance di livello aziendale su scala cloud IaaS di Azure.

Poiché questi pool di calcolo sono all'interno dell'infrastruttura IaaS di Azure, è possibile distribuire, ridimensionare e gestire il training con gli stessi requisiti di sicurezza e conformità del resto dell'infrastruttura.  Queste distribuzioni vengono eseguite nella sottoscrizione e rispettano le regole di governance. Altre informazioni su come [Azure Machine Learning calcolo.](how-to-create-attach-compute-cluster.md)

### <a name="configure-training-clusters-for-autoscaling"></a>Configurare cluster di training per la scalabilità automatica

La scalabilità automatica dei cluster in base ai requisiti del carico di lavoro consente di ridurre i costi in modo da usare solo le risorse necessarie.

I cluster AmlCompute sono progettati per la scalabilità dinamica in base al carico di lavoro. Il cluster può essere ridimensionato fino al numero massimo di nodi configurato. Al termine di ogni esecuzione, il cluster rilascerà i nodi e verrà ridimensionato in base al numero minimo di nodi configurato.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

È anche possibile configurare la quantità di tempo per cui il nodo è inattivo prima della scalabilità orizzontale. Per impostazione predefinita, il tempo di inattività prima della scalabilità verso il basso è impostato su 120 secondi.

+ Se si esegue una sperimentazione meno iterativa, ridurre questo tempo per risparmiare sui costi.
+ Se si esegue una sperimentazione di sviluppo/test altamente iterativa, potrebbe essere necessario aumentare il tempo in modo da non pagare per un aumento e una riduzione costanti delle prestazioni dopo ogni modifica apportata allo script di training o all'ambiente.

I cluster AmlCompute possono essere configurati per la modifica dei requisiti del carico di lavoro in portale di Azure, usando la [classe AmlCompute SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute), l'interfaccia della riga di comando di [AmlCompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute), con le [API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Impostare quote per le risorse

AmlCompute include una [configurazione di quota (o limite).](how-to-manage-quotas.md#azure-machine-learning-compute) Questa quota è in base alla famiglia di macchine virtuali (ad esempio, serie Dv2, serie NCv3) e varia in base all'area per ogni sottoscrizione. Le sottoscrizioni iniziano con impostazioni predefinite ridotte per iniziare, ma usare questa impostazione per controllare la quantità di risorse Amlcompute disponibili per l'avvio nella sottoscrizione. 

Configurare anche la [quota a livello di area di lavoro per famiglia di macchine](how-to-manage-quotas.md#workspace-level-quotas)virtuali , per ogni area di lavoro all'interno di una sottoscrizione. In questo modo è possibile avere un controllo più granulare sui costi che ogni area di lavoro potrebbe potenzialmente sostenere e limitare determinate famiglie di macchine virtuali. 

Per impostare le quote a livello di area di lavoro, iniziare nel [portale di Azure](https://portal.azure.com).  Selezionare qualsiasi area di lavoro nella sottoscrizione e selezionare **Utilizzi e quote** nel riquadro sinistro. Selezionare quindi la **scheda Configura quote** per visualizzare le quote. Per impostare la quota sono necessari privilegi nell'ambito della sottoscrizione, perché si tratta di un'impostazione che interessa più aree di lavoro.

### <a name="set-run-autotermination-policies"></a>Impostare i criteri di sterminio automatico per l'esecuzione 

In alcuni casi, è necessario configurare le esecuzioni di training per limitarne la durata o terminarle in anticipo. Ad esempio, quando si usa Azure Machine Learning'ottimizzazione predefinita degli iperparametrami o l'apprendimento automatico.

Di seguito sono disponibili alcune opzioni:
* Definire un parametro denominato in RunConfiguration per controllare la durata massima che un'esecuzione può estendere nel calcolo scelto (calcolo cloud locale `max_run_duration_seconds` o remoto).
* Per [l'ottimizzazione degli](how-to-tune-hyperparameters.md#early-termination)iperparamenzi, definire un criterio di terminazione anticipata da un criterio Bandit, un criterio di arresto mediano o un criterio di selezione troncamento. Per controllare ulteriormente gli sweep degli iperparamezzi, usare parametri come `max_total_runs` o `max_duration_minutes` .
* Per [Machine Learning automatizzato,](how-to-configure-auto-train.md#exit)impostare criteri di terminazione simili usando il  `enable_early_stopping` flag . Usare anche proprietà come e `iteration_timeout_minutes` per controllare la durata massima di `experiment_timeout_minutes` un'esecuzione o per l'intero esperimento.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Usare macchine virtuali con priorità bassa

Azure consente di usare la capacità inutilizzata in eccesso Low-Priority macchine virtuali tra set di scalabilità di macchine virtuali, Batch e il Machine Learning virtuale. Queste allocazioni sono pre-svuotabili, ma hanno un prezzo ridotto rispetto alle macchine virtuali dedicate. In generale, è consigliabile usare Low-Priority macchine virtuali per i carichi di lavoro batch. È consigliabile usarli anche quando le interruzioni possono essere recuperate tramite i resubmit (per l'inferenza batch) o tramite riavvii (per il training di Deep Learning con checkpoint).

Low-Priority macchine virtuali hanno una singola quota separata dal valore di quota dedicato, ovvero in base alla famiglia di macchine virtuali. Altre [informazioni sulle quote di AmlCompute.](how-to-manage-quotas.md)

 Low-Priority macchine virtuali non funzionano per le istanze di calcolo, perché devono supportare esperienze di notebook interattive.

### <a name="use-reserved-instances"></a>Usare le istanze riservate

Un altro modo per risparmiare denaro sulle risorse di calcolo è l'istanza di macchina virtuale riservata di Azure. Con questa offerta, ci si impegna a un anno o tre anni. Questi sconti variano fino al 72% dei prezzi con pagamento in base al consumo e vengono applicati direttamente alla fattura mensile di Azure.

Azure Machine Learning compute supporta intrinsecamente le istanze riservate. Se si acquista un'istanza riservata di uno o tre anni, verrà applicato automaticamente lo sconto per l'Azure Machine Learning di calcolo gestito.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come ottimizzare gli investimenti nel cloud con Gestione costi di Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Altre informazioni sulla gestione dei costi con [l'analisi dei costi.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Informazioni su come evitare [costi imprevisti.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Eseguire il [corso di](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) apprendimento guidato su Gestione costi.
