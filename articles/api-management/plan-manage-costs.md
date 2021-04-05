---
title: Pianificare e gestire i costi per gestione API di Azure
description: Informazioni su come pianificare e gestire i costi per gestione API di Azure tramite l'analisi dei costi nel portale di Azure.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 1ebb89ae318e57f1d4e0708a08019515ca43158d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581330"
---
# <a name="plan-and-manage-costs-for-api-management"></a>Pianificare e gestire i costi per gestione API

Questo articolo descrive come pianificare e gestire i costi per gestione API di Azure. Usare prima di tutto il calcolatore dei prezzi di Azure per pianificare i costi di gestione API prima di aggiungere le risorse per il servizio per stimare i costi. Dopo aver iniziato a usare le risorse di gestione API, usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. È anche possibile esaminare i costi previsti e identificare le tendenze di spesa per identificare le aree in cui si vuole agire. 

I costi per gestione API sono solo una parte dei costi mensili nella fattura di Azure. Sebbene questo articolo spieghi come pianificare e gestire i costi per gestione API, vengono addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti.

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi in gestione costi supporta la maggior parte dei tipi di account Azure, ma non tutti. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per visualizzare i dati sui costi, è necessario almeno l'accesso in lettura per un account Azure. Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-api-management"></a>Stimare i costi prima di usare gestione API

Usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per stimare i costi prima di aggiungere gestione API. 

1. Cercare *gestione API* o selezionare   >  **gestione API** di integrazione.
1. Selezionare **Visualizza** per aggiungere una stima dei costi predefinita per un'istanza del servizio gestione API.

> [!NOTE]
> I costi indicati in questo esempio sono solo a scopo dimostrativo. Per informazioni sui prezzi più recenti, vedere [prezzi di gestione API](https://azure.microsoft.com/pricing/details/api-management/) .

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Stimare i costi per il livello Developer":::

* La stima dei costi predefinita si basa su un'istanza del servizio gestione API nel [livello di servizio](api-management-features.md) **dello sviluppatore** con 1 [unità di capacità](api-management-capacity.md). Il livello Developer è per i casi d'uso e le valutazioni non di produzione. Non è supportato da un contratto di servizio.

* Per stimare i costi per unità di capacità aggiuntive o per un livello di servizio diverso, selezionare altre opzioni nell'elenco a discesa **unità** e **livello** .

* A seconda della disponibilità delle funzionalità e del livello di servizio, potrebbero essere applicati addebiti aggiuntivi per l'uso di [gateway indipendenti](self-hosted-gateway-overview.md).

Per ulteriori informazioni sui prezzi e sulle funzionalità, vedere:

* [Prezzi di gestione API](https://azure.microsoft.com/pricing/details/api-management/)
* [Confronto basato sulle funzionalità dei livelli di Gestione API di Azure](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>Uso del credito monetario con gestione API

È possibile pagare gli addebiti per gestione API con il prepagamento di Azure (in precedenza denominato impegno monetario). Tuttavia, non è possibile usare il credito con pagamento anticipato di Azure per pagare gli addebiti per i prodotti e i servizi di terze parti, inclusi quelli di Azure Marketplace.

## <a name="monitor-costs"></a>Monitorare i costi

Quando si usano le risorse di Azure con gestione API, si incorrono i costi. I costi delle unità di utilizzo delle risorse di Azure variano in base a intervalli di tempo (secondi, minuti, ore e giorni) o all'utilizzo di unità (byte, megabyte e così via). Non appena viene avviato l'uso di gestione API, i costi sono sostenuti ed è possibile visualizzare i costi nell' [analisi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)dei costi.

Quando si usa l'analisi dei costi, è possibile visualizzare i costi di gestione API in grafici e tabelle per intervalli di tempo diversi. Alcuni esempi sono il giorno, il mese corrente e quello precedente e l'anno. Puoi anche visualizzare i costi rispetto ai budget e ai costi previsti. Il passare a visualizzazioni più lunghe nel tempo può aiutare a identificare le tendenze di spesa. Viene visualizzato il punto in cui potrebbe essersi verificata la sovraspesa. Se sono stati creati budget, è anche possibile vedere dove sono stati superati.

> [!NOTE]
> I costi indicati in questo esempio sono solo a scopo dimostrativo. I costi variano in base all'utilizzo delle risorse e ai prezzi correnti.

Per visualizzare i costi di gestione API nell'analisi dei costi:

1. Accedere al [portale di Azure](https://azure.microsoft.com).
1. Aprire la finestra **Gestione costi e fatturazione** , selezionare **Gestione costi** dal menu e quindi selezionare un ambito di **fatturazione**. Selezionare, ad esempio, una sottoscrizione dall'elenco.
1. Selezionare **Gestione costi** dal menu e quindi selezionare analisi dei **costi**.
1. Per impostazione predefinita, i costi mensili per tutti i servizi vengono visualizzati nel primo grafico ad anello. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Costi mensili per la sottoscrizione":::

1. Per limitare i costi per un singolo servizio, ad esempio gestione API, selezionare **Aggiungi filtro** e quindi selezionare **nome servizio**. Quindi, selezionare **gestione API**.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="Esempio che mostra i costi accumulati per gestione API":::

Nell'esempio precedente viene visualizzato il costo corrente per il servizio. Vengono visualizzati anche i costi per le aree di Azure (posizioni) e i costi di gestione API per gruppo di risorse. Da qui è possibile esplorare i costi autonomamente.

## <a name="create-budgets"></a>Creare i budget

È possibile creare [budget](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) per gestire i costi e creare [avvisi](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) che notificano automaticamente agli stakeholder le anomalie di spesa e i rischi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. 

I budget possono essere creati con filtri per risorse o servizi specifici in Azure se si vuole una maggiore granularità nel monitoraggio. I filtri consentono di evitare di creare accidentalmente nuove risorse che costano denaro aggiuntivo. Per ulteriori informazioni sulle opzioni di filtro quando si crea un budget, vedere [Opzioni di raggruppamento e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Esportare i dati relativi ai costi

È anche possibile [esportare i dati sui costi](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Questa operazione è utile quando sono necessarie altre attività di analisi dei dati aggiuntive per i costi. Ad esempio, un team Finance può analizzare i dati usando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è la modalità consigliata per recuperare i set di dati di costo.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>Altri modi per gestire e ridurre i costi per gestione API

### <a name="choose-tier"></a>Scegliere il livello

Esaminare il [confronto basato sulle funzionalità dei livelli di gestione API di Azure](api-management-features.md) per decidere quale livello di servizio può essere appropriato per gli scenari. I diversi livelli di servizio supportano combinazioni di caratteristiche e funzionalità progettate per diversi casi d'uso, con costi diversi. 

* Il livello di servizio a **consumo** fornisce un'opzione leggera senza server che non comporta costi fissi. La fatturazione viene addebitata in base al numero di chiamate API al servizio al di sopra di una determinata soglia. La capacità viene inoltre ridimensionata automaticamente in base al carico del servizio.
* I livelli di gestione API **Developer**, **Basic**, **standard** e **Premium** comportano costi mensili e offrono una maggiore velocità effettiva e set di funzionalità più completi per i carichi di lavoro di valutazione e produzione. [Eseguire l'aggiornamento](upgrade-and-scale.md) a un altro livello di servizio in qualsiasi momento.

### <a name="scale-using-capacity-units"></a>Ridimensionare usando unità di capacità

Ad eccezione del livello di servizio a consumo, gestione API supporta la scalabilità tramite l'aggiunta o la rimozione di [*unità di capacità*](api-management-capacity.md). Con l'aumentare del carico su un'istanza di gestione API, l'aggiunta di unità di capacità può risultare più economica rispetto all'aggiornamento a un livello di servizio superiore. Il numero massimo di unità dipende dal livello di servizio.

Ogni unità di capacità ha una determinata funzionalità di elaborazione delle richieste che dipende dal livello del servizio. Ad esempio, un'unità del livello Basic ha una velocità effettiva massima stimata di circa 1.000 richieste al secondo. 

Quando si aggiungono o si rimuovono unità, capacità e scala dei costi proporzionalmente. Ad esempio, due unità del livello standard forniscono una velocità effettiva stimata di circa 2.000 richieste al secondo. La velocità effettiva può variare a causa delle dimensioni di richieste o risposte, modelli di connessione, numero di client che effettuano richieste e altri fattori.

[Monitorare](api-management-howto-use-azure-monitor.md) la metrica della capacità per l'istanza di gestione API per decidere se ridimensionare o aggiornare un'istanza di gestione API per gestire un carico maggiore.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come ottimizzare l'investimento nel cloud con gestione costi di Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Altre informazioni sulla gestione dei costi con l' [analisi dei costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Informazioni su come [evitare costi imprevisti](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Segui il corso di apprendimento guidato per [Gestione costi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
- Informazioni sulla [capacità](api-management-capacity.md)di gestione API.
- Vedere i passaggi per scalare e aggiornare gestione API usando il [portale di Azure](upgrade-and-scale.md)e informazioni sul [ridimensionamento automatico](api-management-howto-autoscale.md).
