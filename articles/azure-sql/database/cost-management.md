---
title: Pianificare e gestire i costi per il database SQL di Azure
description: Informazioni su come pianificare e gestire i costi per il database SQL di Azure tramite l'analisi dei costi nel portale di Azure.
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 56cf30d89460df8ac50d258bd8b29cf4e7236690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734631"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Pianificare e gestire i costi per il database SQL di Azure

Questo articolo descrive come pianificare e gestire i costi per il database SQL di Azure. Usare prima di tutto il calcolatore dei prezzi di Azure per aggiungere le risorse di Azure ed esaminare i costi stimati. Dopo aver iniziato a usare le risorse del database SQL di Azure, usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. È anche possibile esaminare i costi previsti e identificare le tendenze di spesa per identificare le aree in cui si vuole agire. I costi per il database SQL di Azure sono solo una parte dei costi mensili nella fattura di Azure. Sebbene questo articolo spieghi come pianificare e gestire i costi per il database SQL di Azure, verranno addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti.


## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi supporta la maggior parte dei tipi di account Azure, ma non tutti. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Per visualizzare i dati sui costi, è necessario almeno l'accesso in lettura per un account Azure. 

Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>Considerazioni sui costi iniziali del database SQL

Quando si usa il database SQL di Azure, è necessario prendere in considerazione diverse funzionalità di risparmio dei costi:


### <a name="vcore-or-dtu-purchasing-models"></a>modelli di acquisto vCore o DTU 

Il database SQL di Azure supporta due modelli di acquisto: vCore e DTU. Il modo in cui viene addebitato il costo varia in base ai modelli di acquisto. è quindi importante comprendere il modello più adatto al proprio carico di lavoro quando si pianificano e si considerano i costi. Per informazioni sui modelli di acquisto vCore e DTU, vedere [scegliere tra i modelli di acquisto vCore e DTU](purchasing-models.md).


### <a name="provisioned-or-serverless"></a>Con provisioning o senza server

Nel modello di acquisto vCore, il database SQL di Azure supporta anche due tipi di livelli di calcolo: velocità effettiva con provisioning e senza server. Il modo in cui viene addebitato il costo per ogni livello di calcolo varia, quindi è importante comprendere il funzionamento ottimale per il carico di lavoro quando si pianificano e si considerano i costi. Per informazioni dettagliate, vedere [Panoramica del modello vCore-livelli di calcolo](service-tiers-vcore.md#compute-tiers).

Nel livello di calcolo di cui è stato effettuato il provisioning del modello di acquisto basato su vCore è possibile scambiare le licenze esistenti con tariffe scontate. Per informazioni dettagliate, vedere [vantaggio Azure Hybrid (vantaggio Azure Hybrid)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Pool elastici

Per gli ambienti con più database che presentano richieste di utilizzo variabili e non prevedibili, i pool elastici possono garantire risparmi sui costi rispetto al provisioning della stessa quantità di database singoli. Per informazioni dettagliate, vedere [pool elastici](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Stimare i costi del database SQL di Azure

Usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per stimare i costi per diverse configurazioni del database SQL di Azure. Le informazioni e i prezzi nell'immagine seguente sono solo a scopo esemplificativo:

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Esempio di calcolatore dei prezzi del database SQL di Azure":::

È anche possibile stimare il modo in cui le diverse opzioni relative ai criteri di conservazione influiscono Le informazioni e i prezzi nell'immagine seguente sono solo a scopo esemplificativo:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Esempio di calcolatore dei prezzi del database SQL di Azure per l'archiviazione":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Informazioni sul modello di fatturazione completo per il database SQL di Azure

Il database SQL di Azure viene eseguito nell'infrastruttura di Azure che accumula i costi insieme al database SQL di Azure quando si distribuisce la nuova risorsa. È importante comprendere che l'infrastruttura aggiuntiva potrebbe accumulare costi. È necessario gestire questo costo quando si apportano modifiche alle risorse distribuite. 


Il database SQL di Azure, fatta eccezione per i server, viene fatturato in base a una tariffa oraria prevedibile. Se il database SQL è attivo per meno di un'ora, viene fatturata ogni ora di esistenza del database usando il livello di servizio più elevato selezionato, l'archiviazione di cui è stato effettuato il provisioning e l'i/o applicato durante tale ora, indipendentemente dall'utilizzo o dal fatto che il database sia stato attivo per meno di un'ora.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Uso del credito monetario con il database SQL di Azure

È possibile pagare gli addebiti per il database SQL di Azure con il credito prepagato di Azure (in precedenza denominato impegno monetario). Tuttavia, non è possibile usare il credito con pagamento anticipato di Azure per pagare gli addebiti per i prodotti e i servizi di terze parti, inclusi quelli di Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Esaminare i costi stimati nel portale di Azure

Durante il processo di creazione di un database SQL di Azure, è possibile visualizzare i costi stimati durante la configurazione del livello di calcolo. 

Per accedere a questa schermata, selezionare **Configura database** nella scheda **nozioni di base** della pagina **Crea database SQL** . Le informazioni e i prezzi nell'immagine seguente sono solo a scopo esemplificativo:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Esempio che mostra la stima dei costi nel portale di Azure":::



Se la sottoscrizione di Azure ha un limite di spesa, Azure impedisce la spesa rispetto all'importo del credito. Quando si creano e si usano le risorse di Azure, vengono usati i crediti. Quando si raggiunge il limite di credito, le risorse distribuite sono disabilitate per il resto del periodo di fatturazione. Non è possibile modificare il limite di credito, ma è possibile rimuoverlo. Per altre informazioni sui limiti di spesa, vedere [limite di spesa di Azure](../../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Monitorare i costi

Quando si inizia a usare il database SQL di Azure, è possibile visualizzare i costi stimati nel portale. Per esaminare la stima dei costi, attenersi alla procedura seguente:

1. Accedere al portale di Azure e passare al gruppo di risorse del database SQL di Azure. È possibile individuare il gruppo di risorse passando al database e selezionando **gruppo di risorse** nella sezione **Panoramica** .
1. Nel menu selezionare analisi dei **costi**.
1. Visualizzare i **costi accumulati** e impostare il grafico nella parte inferiore del **nome del servizio**. Questo grafico mostra una stima dei costi del database SQL corrente. Per limitare i costi dell'intera pagina al database SQL di Azure, selezionare **Aggiungi filtro** e quindi selezionare **database SQL di Azure**. Le informazioni e i prezzi nell'immagine seguente sono solo a scopo esemplificativo:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Esempio che mostra i costi accumulati nella portale di Azure":::

Da qui è possibile esplorare i costi autonomamente. Per ulteriori informazioni sulle diverse impostazioni di analisi dei costi, vedere [avviare l'analisi dei costi](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Creare i budget

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

È possibile creare [budget](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) per gestire i costi e creare [avvisi](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) che notificano automaticamente agli stakeholder le anomalie di spesa e i rischi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. 

I budget possono essere creati con filtri per risorse o servizi specifici in Azure se si vuole una maggiore granularità nel monitoraggio. I filtri consentono di evitare di creare accidentalmente nuove risorse che costano denaro aggiuntivo. Per ulteriori informazioni sulle opzioni di filtro quando si crea un budget, vedere [Opzioni di raggruppamento e filtro](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Esportare i dati relativi ai costi

È anche possibile [esportare i dati sui costi](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Questa operazione è utile quando sono necessari o altri utenti per eseguire ulteriori analisi dei dati per i costi. Ad esempio, i team finanziari possono analizzare i dati usando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è la modalità consigliata per recuperare i set di dati di costo.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Altri modi per gestire e ridurre i costi per il database SQL di Azure

Il database SQL di Azure consente anche di aumentare o ridurre le risorse per controllare i costi in base alle esigenze dell'applicazione. Per informazioni dettagliate, vedere [ridimensionare in modo dinamico le risorse di database](scale-resources.md).

Risparmia denaro eseguendo il commit di una prenotazione per le risorse di calcolo da uno a tre anni. Per informazioni dettagliate, vedere [ridurre i costi per le risorse con capacità riservata](reserved-capacity-overview.md).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come ottimizzare l'investimento nel cloud con gestione costi di Azure](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Altre informazioni sulla gestione dei costi con l' [analisi dei costi](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Informazioni su come [evitare costi imprevisti](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Segui il corso di apprendimento guidato per [Gestione costi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .