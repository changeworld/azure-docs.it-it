---
title: Pianificare la gestione dei costi per servizi cognitivi di Azure
description: Informazioni su come pianificare e gestire i costi per i servizi cognitivi di Azure tramite l'analisi dei costi nel portale di Azure.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 48e111ac8b2b4fd3c1e2fee568e20699896dfff3
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705787"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Pianificare e gestire i costi per servizi cognitivi di Azure

Questo articolo descrive come pianificare e gestire i costi per i servizi cognitivi di Azure. Usare prima di tutto il calcolatore dei prezzi di Azure per pianificare i costi dei servizi cognitivi prima di aggiungere le risorse per il servizio per stimare i costi. Successivamente, quando si aggiungono le risorse di Azure, esaminare i costi stimati. Dopo aver iniziato a usare le risorse di servizi cognitivi, ad esempio vocale, Visione artificiale, LUIS, Analisi del testo, translator e così via, usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. È anche possibile esaminare i costi previsti e identificare le tendenze di spesa per identificare le aree in cui si vuole agire. I costi per i servizi cognitivi sono solo una parte dei costi mensili nella fattura di Azure. Anche se in questo articolo viene illustrato come pianificare e gestire i costi per i servizi cognitivi, vengono addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti.

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi in gestione costi supporta la maggior parte dei tipi di account Azure, ma non tutti. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per visualizzare i dati sui costi, è necessario almeno l'accesso in lettura per un account Azure. Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Stimare i costi prima di usare servizi cognitivi

Usa il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per stimare i costi prima di aggiungere servizi cognitivi.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Calcolatore prezzi di Azure per servizi cognitivi" border="true":::

Quando si aggiungono nuove risorse all'area di lavoro, tornare al calcolatore e aggiungere la stessa risorsa qui per aggiornare le stime dei costi.

Per altre informazioni, vedere [prezzi di servizi cognitivi di Azure](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Informazioni sul modello di fatturazione completo per servizi cognitivi

Servizi cognitivi viene eseguito nell'infrastruttura di Azure che [accumula i costi](https://azure.microsoft.com/pricing/details/cognitive-services/) quando si distribuisce la nuova risorsa. È importante comprendere che l'infrastruttura aggiuntiva potrebbe accumulare costi. È necessario gestire questo costo quando si apportano modifiche alle risorse distribuite. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Costi che in genere si accumulano con servizi cognitivi

In genere, dopo la distribuzione di una risorsa di Azure, i costi sono determinati dal piano tariffario e dalle chiamate API effettuate all'endpoint. Se il servizio che si sta usando ha un livello di impegno, il superamento delle chiamate assegnate nel livello può comportare un addebito in eccedenza.

Quando si usano questi servizi, è possibile che si accumulino costi aggiuntivi:

#### <a name="qna-maker"></a>QnA Maker

Quando si creano risorse per QnA Maker, è possibile che vengano create anche risorse per altri servizi di Azure. e comprendono:

- [Servizio app Azure (per il Runtime)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure ricerca cognitiva (per i dati)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Costi che potrebbero accumularsi dopo l'eliminazione delle risorse

#### <a name="qna-maker"></a>QnA Maker

Dopo aver eliminato QnA Maker risorse, le risorse seguenti potrebbero continuare a esistere. Continuano a accumulare costi fino a quando non vengono eliminati.

- [Servizio app Azure (per il Runtime)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure ricerca cognitiva (per i dati)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-monetary-credit-with-cognitive-services"></a>Uso del credito monetario con servizi cognitivi

È possibile pagare gli addebiti per servizi cognitivi con il credito dell'impegno monetario EA. Tuttavia, non è possibile usare il credito dell'impegno monetario EA per pagare gli addebiti per i prodotti e i servizi di terze parti, inclusi quelli di Azure Marketplace.

## <a name="create-budgets"></a>Creare i budget

È possibile creare [budget](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) per gestire i costi e creare [avvisi](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) che notificano automaticamente agli stakeholder le anomalie di spesa e i rischi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. 

I budget possono essere creati con filtri per risorse o servizi specifici in Azure se si vuole una maggiore granularità nel monitoraggio. I filtri consentono di evitare di creare accidentalmente nuove risorse che costano denaro aggiuntivo. Per ulteriori informazioni sulle opzioni di filtro quando si crea un budget, vedere [Opzioni di raggruppamento e filtro](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Esportare i dati relativi ai costi

È anche possibile [esportare i dati sui costi](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Questa operazione è utile quando sono necessari o altri utenti per eseguire ulteriori analisi dei dati per i costi. I team finanziari possono ad esempio analizzare i dati utilizzando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è la modalità consigliata per recuperare i set di dati di costo.

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come ottimizzare l'investimento nel cloud con gestione costi di Azure](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Altre informazioni sulla gestione dei costi con l' [analisi dei costi](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Informazioni su come [evitare costi imprevisti](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Segui il corso di apprendimento guidato per [Gestione costi](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
