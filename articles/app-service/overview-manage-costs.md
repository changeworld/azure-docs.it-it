---
title: Pianificare la gestione dei costi per il servizio app
description: Informazioni su come pianificare e gestire i costi per il servizio app Azure tramite l'analisi dei costi nell'portale di Azure.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: de55a708ab78844fd8e834db0bd88e12dd66885a
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98186479"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Pianificare e gestire i costi per il servizio app Azure

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

Questo articolo descrive come pianificare e gestire i costi per il servizio app Azure. Usare prima di tutto il calcolatore dei prezzi di Azure per pianificare i costi del servizio app prima di aggiungere le risorse per il servizio per stimare i costi. Successivamente, quando si aggiungono le risorse di Azure, esaminare i costi stimati. Dopo aver iniziato a usare le risorse del servizio app, usare le funzionalità di [gestione dei costi](https://docs.microsoft.com/azure/cost-management-billing/) per impostare i budget e monitorare i costi. È anche possibile esaminare i costi previsti e identificare le tendenze di spesa per identificare le aree in cui si vuole agire. I costi per app Azure servizio sono solo una parte dei costi mensili nella fattura di Azure. Sebbene questo articolo spieghi come pianificare e gestire i costi per il servizio app, vengono addebitati tutti i servizi e le risorse di Azure usati nella sottoscrizione di Azure, inclusi i servizi di terze parti.

## <a name="relevant-costs-for-app-service"></a>Costi rilevanti per il servizio app

Il servizio app viene eseguito nell'infrastruttura di Azure che accumula i costi. È importante comprendere che l'infrastruttura aggiuntiva potrebbe accumulare costi. È necessario gestire tale costo quando si apportano modifiche alle risorse distribuite.

### <a name="costs-that-accrue-with-azure-app-service"></a>Costi accumulati con app Azure Service

A seconda della funzionalità usata nel servizio app, è possibile che vengano create le risorse di accumulo di costi seguenti:

- **Piano di servizio app**  Obbligatorio per ospitare un'app del servizio app.
- **Livello isolato**  Per un ambiente del servizio app è necessaria una [rete virtuale](/azure/virtual-network/) .
- **Esegui backup**  Per eseguire i backup è necessario un [account di archiviazione](/azure/storage/) .
- **Log di diagnostica**  È possibile selezionare [account di archiviazione](/azure/storage/) come opzione di registrazione o eseguire l'integrazione con [log Analytics di Azure](../azure-monitor/log-query/log-analytics-tutorial.md).
- **Certificati del servizio app**  I certificati acquistati in Azure devono essere conservati in [Azure Key Vault](/azure/key-vault/).

Altre risorse di costo per il servizio app sono (vedere [prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/) per informazioni dettagliate):

- [Domini del servizio app](manage-custom-dns-buy-domain.md)  Se si Abilita il rinnovo automatico, la sottoscrizione viene addebitata per la registrazione del dominio su base annuale.
- [Certificati del servizio app](configure-ssl-certificate.md#import-an-app-service-certificate)  Addebito una volta al momento dell'acquisto. Se si dispone di più sottodomini da proteggere, è possibile ridurre i costi acquistando un certificato con caratteri jolly anziché più certificati standard.
- [Associazioni di certificati basati su IP](configure-ssl-bindings.md#create-binding)  L'associazione viene configurata su un certificato a livello di app. I costi vengono accumulati per ogni associazione. Per il livello **standard** e versioni successive, la prima associazione basata su IP non viene addebitata.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Costi che potrebbero accumularsi dopo l'eliminazione delle risorse

Quando si eliminano tutte le app in un piano di servizio app, il piano continua a accumulare addebiti in base al piano tariffario configurato e al numero di istanze. Per evitare addebiti indesiderati, eliminare il piano o ridurlo a livello **gratuito** .

Dopo aver eliminato app Azure risorse del servizio, è possibile che le risorse dei servizi di Azure correlati continuino a esistere. Continuano a accumulare costi fino a quando non vengono eliminati. Ad esempio:

- Rete virtuale creata per un piano di servizio app di livello **isolato**
- Account di archiviazione creati per archiviare i backup o i log di diagnostica
- Key Vault creato per archiviare i certificati del servizio app
- Spazi dei nomi di analisi log creati per la spedizione dei log di diagnostica
- [Prenotazioni di istanze o timbri](#azure-reservations) per il servizio app che non sono ancora scadute

### <a name="using-monetary-credit-with-azure-app-service"></a>Uso del credito monetario con il servizio app Azure

È possibile pagare gli addebiti per app Azure servizio con il credito dell'impegno monetario EA. Tuttavia, non è possibile usare il credito dell'impegno monetario EA per pagare gli addebiti per i prodotti e i servizi di terze parti, inclusi quelli di Azure Marketplace.

## <a name="estimate-costs"></a>Stimare i costi

Un modo semplice per stimare e ottimizzare in anticipo il costo del servizio app consiste nell'usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).

Per usare il calcolatore dei prezzi, fare clic su **servizio app** nella scheda **prodotti** . Scorrere quindi verso il basso per usare la calcolatrice. Lo screenshot seguente è un esempio e non riflette i prezzi correnti.

![Esempio che mostra il costo stimato nel calcolatore dei prezzi di Azure](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Esaminare i costi stimati nel portale di Azure

Quando si crea un'app del servizio app o un piano di servizio app, è possibile visualizzare i costi stimati.

Per creare un'app e visualizzare il prezzo stimato:

1. Nella pagina Crea scorrere fino al piano di **servizio app** e fare clic su **Crea nuovo**.
1. Specificare un nome e fare clic su **OK**.
1. Accanto a **SKU e dimensioni** fare clic su **modifica dimensioni**.
1. Esaminare il prezzo stimato indicato nel riepilogo. Lo screenshot seguente è un esempio e non riflette i prezzi correnti.

    ![Esaminare il costo stimato per ogni piano tariffario nel portale](media/overview-manage-costs/pricing-estimates.png)

Se la sottoscrizione di Azure ha un limite di spesa, Azure impedisce la spesa rispetto all'importo del credito. Quando si creano e si usano le risorse di Azure, vengono usati i crediti. Quando si raggiunge il limite di credito, le risorse distribuite sono disabilitate per il resto del periodo di fatturazione. Non è possibile modificare il limite di credito, ma è possibile rimuoverlo. Per altre informazioni sui limiti di spesa, vedere [limite di spesa di Azure](../billing/billing-spending-limit.md).

## <a name="optimize-costs"></a>Ottimizzare i costi

A livello di base, le app del servizio app vengono addebitate dal piano di servizio app che li ospita. I costi associati alla distribuzione del servizio app dipendono da alcuni fattori principali:

- Piano **tariffario**  Altrimenti noto come SKU del piano di servizio app. I livelli superiori forniscono più core CPU, memoria, archiviazione o funzionalità o combinazioni di essi.
- È possibile aumentare il numero di istanze dei livelli dedicati del **numero di istanze** (Basic e versioni successive) e ogni istanza con scalabilità orizzontale accumula i costi.
- **Tassa timbro**  Nel livello isolato, una tariffa fissa viene accumulata nell'ambiente del servizio app, indipendentemente dal numero di app o di istanze di lavoro ospitate.

Un piano di servizio app può ospitare più di un'app. A seconda della distribuzione, è possibile risparmiare sui costi che ospitano più app in un piano di servizio app, ad esempio ospitando le app in un minor numero di piani di servizio app.

Per informazioni dettagliate, vedere [Panoramica del piano di servizio app](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>Carichi di lavoro non di produzione

Per testare il servizio app o la soluzione durante l'accumulo di costi bassi o minimi, è possibile iniziare usando i due piani tariffari a livello di ingresso, **gratuiti** e **condivisi**, ospitati in istanze condivise. Per testare l'app in istanze dedicate con prestazioni migliori, è possibile eseguire l'aggiornamento al livello **Basic** , che supporta le app Windows e Linux. 

> [!NOTE]
> **Prezzi di sviluppo/test di Azure**  Per testare i carichi di lavoro di pre-produzione che richiedono livelli più elevati (tutti i livelli ad eccezione di **isolated**), i sottoscrittori di Visual Studio possono anche sfruttare i [prezzi di sviluppo/test di Azure](https://azure.microsoft.com/pricing/dev-test/), che offrono sconti significativi.
>
> Il livello **gratuito** e **condiviso** , nonché gli sconti per i prezzi di sviluppo/test di Azure, non includono un contratto di licenza con copertura finanziaria.

### <a name="production-workloads"></a>Carichi di lavoro di produzione

I carichi di lavoro di produzione sono dotati di indicazioni sul piano tariffario **standard** dedicato o su un livello superiore. Anche se il prezzo aumenta per i livelli superiori, offre anche maggiore quantità di memoria e archiviazione e hardware con prestazioni più elevate, garantendo una maggiore densità di app per ogni istanza di calcolo. Questo si traduce in un numero di istanze inferiore per lo stesso numero di app e pertanto con costi ridotti. Infatti, **Premium V3** (il livello più alto non **isolato** ) è il modo più conveniente per gestire l'app su larga scala. Per aumentare il risparmio, è possibile ottenere sconti approfonditi sulle [prenotazioni Premium V3](#azure-reservations).

> [!NOTE]
> **Premium V3** supporta i contenitori di Windows e Linux. 

Una volta scelto il piano tariffario desiderato, è necessario ridurre al minimo le istanze inattive. In una distribuzione con scalabilità orizzontale è possibile sprecare denaro per le istanze di calcolo sottoutilizzate. È consigliabile [configurare la scalabilità](../azure-monitor/platform/autoscale-get-started.md)automatica, disponibile nel livello **standard** e versioni successive. Grazie alla creazione di pianificazioni con scalabilità orizzontale e alle regole di scalabilità orizzontale basate su metriche, paghi solo per le istanze effettivamente necessarie in un determinato momento.

### <a name="azure-reservations"></a>Prenotazioni di Azure

Se si prevede di usare un numero minimo noto di istanze di calcolo per un anno o più, è consigliabile sfruttare il livello **Premium V3** e ridurre drasticamente il costo dell'istanza riservando tali istanze in incrementi di 1 anno o di tre anni. Il risparmio sui costi mensili può essere fino al 55% per ogni istanza. Sono possibili due tipi di prenotazione:

- **Windows (o indipendente dalla piattaforma)**  Può essere applicato alle istanze di Windows o Linux nella sottoscrizione.
- **Specifico per Linux**  Si applica solo alle istanze di Linux nella sottoscrizione.

Il prezzo dell'istanza riservata si applica alle istanze applicabili nella sottoscrizione, fino al numero di istanze riservate. Le istanze riservate sono una faccenda di fatturazione e non sono associate a istanze di calcolo specifiche. Se si eseguono meno istanze di quelle riservate in qualsiasi momento durante il periodo di prenotazione, si pagano comunque le istanze riservate. Se si eseguono più istanze di quelle riservate in qualsiasi momento durante il periodo di prenotazione, si paga il normale costo accumulato per le istanze aggiuntive.

Il livello **isolato** (ambiente del servizio app) supporta anche prenotazioni di 1 anno e 3 anni a prezzi ridotti. Per ulteriori informazioni, vedere [la pagina relativa al modo app Azure in cui gli sconti per la prenotazione si applicano agli indicatori isolati](../cost-management-billing/reservations/reservation-discount-app-service-isolated-stamp.md)

## <a name="monitor-costs"></a>Monitorare i costi

Quando si usano le risorse di Azure con il servizio app, si incorrono i costi. I costi delle unità di utilizzo delle risorse di Azure variano in base a intervalli di tempo (secondi, minuti, ore e giorni). Non appena l'uso del servizio app viene avviato, i costi sono sostenuti ed è possibile visualizzare i costi nell' [analisi](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)dei costi.

Quando si usa l'analisi dei costi, è possibile visualizzare i costi del servizio app in grafici e tabelle per intervalli di tempo diversi. Alcuni esempi sono il giorno, il mese corrente e quello precedente e l'anno. Puoi anche visualizzare i costi rispetto ai budget e ai costi previsti. Il passare a visualizzazioni più lunghe nel tempo può aiutare a identificare le tendenze di spesa. Viene visualizzato il punto in cui potrebbe essersi verificata la sovraspesa. Se sono stati creati budget, è anche possibile vedere dove sono stati superati.
    
Per visualizzare i costi del servizio app nell'analisi dei costi:

1. Accedere al portale di Azure.
2. Aprire l'ambito nella portale di Azure e selezionare **analisi dei costi** nel menu. Ad esempio, passare a **sottoscrizioni**, selezionare una sottoscrizione dall'elenco e quindi selezionare  **analisi dei costi** nel menu. Selezionare **ambito** per passare a un ambito diverso nell'analisi dei costi.
3. Per impostazione predefinita, i costi per i servizi vengono visualizzati nel primo grafico ad anello. Selezionare l'area nel grafico con l'etichetta servizio app.

I costi mensili effettivi vengono visualizzati quando inizialmente si apre l'analisi dei costi. Ecco un esempio che Mostra tutti i costi di utilizzo mensili.

![Esempio che mostra i costi accumulati per una sottoscrizione](media/overview-manage-costs/all-costs.png)

Per limitare i costi per un singolo servizio, ad esempio servizio app, selezionare **Aggiungi filtro** e quindi selezionare **nome servizio**. Selezionare quindi **servizio app**.

Di seguito è riportato un esempio che illustra i costi del solo servizio app.

![Esempio che mostra i costi accumulati per ServiceName](media/overview-manage-costs/service-specific-costs.png)

Nell'esempio precedente viene visualizzato il costo corrente per il servizio. Vengono visualizzati anche i costi per le aree di Azure (località) e i costi del servizio app per gruppo di risorse. Da qui è possibile esplorare i costi autonomamente.

## <a name="create-budgets"></a>Creare i budget

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

È possibile creare [budget](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) per gestire i costi e creare [avvisi](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) che notificano automaticamente agli stakeholder le anomalie di spesa e i rischi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. 

I budget possono essere creati con filtri per risorse o servizi specifici in Azure se si vuole una maggiore granularità nel monitoraggio. I filtri consentono di evitare di creare inavvertitamente nuove risorse che costano denaro aggiuntivo. Per ulteriori informazioni sulle opzioni di filtro disponibili quando si crea un budget, vedere [Opzioni di gruppo e filtro](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Esportare i dati relativi ai costi

È anche possibile [esportare i dati sui costi](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in un account di archiviazione. Questa operazione è utile quando sono necessari o altri per eseguire ulteriori analisi dei dati per i costi. Ad esempio, un team Finance può analizzare i dati usando Excel o Power BI. È possibile esportare i costi in base a una pianificazione giornaliera, settimanale o mensile e impostare un intervallo di date personalizzato. L'esportazione dei dati sui costi è la modalità consigliata per recuperare i set di dati di costo.

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più su come funziona il prezzo con archiviazione di Azure. Vedere [prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/).
- Informazioni [su come ottimizzare l'investimento nel cloud con gestione costi di Azure](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Altre informazioni sulla gestione dei costi con l' [analisi dei costi](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Informazioni su come [evitare costi imprevisti](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Segui il corso di apprendimento guidato per [Gestione costi](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->