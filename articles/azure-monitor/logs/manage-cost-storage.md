---
title: Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure
description: Informazioni su come modificare il piano tariffario e gestire il volume e i criteri di conservazione dei dati per l'area di lavoro Log Analytics in Monitoraggio di Azure.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: bwren
ms.openlocfilehash: ac2d1ea17460c56a3369d00d2cc8e41cca616363
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310921"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure    

> [!NOTE]
> Questo articolo descrive come comprendere e controllare i costi per i log di Monitoraggio di Azure. Un articolo correlato, [Monitorare l'utilizzo e i costi stimati](../usage-estimated-costs.md), descrive come visualizzare l'utilizzo e i costi stimati relativi a più funzionalità di monitoraggio di Azure per diversi modelli di prezzi. Tutti i prezzi e i costi mostrati in questo articolo sono solo a scopo esemplificativo. 

Log di Monitoraggio di Azure è un servizio progettato per offrire scalabilità e supportare la raccolta, l'indicizzazione e l'archiviazione di grandi quantità di dati al giorno da qualsiasi origine aziendale o distribuita in Azure.  Anche se si tratta di uno strumento importante per l'organizzazione, è comunque fondamentale ottimizzare i costi. A tale scopo, è importante comprendere che il costo di un'area di lavoro Log Analytics non si basa solo sul volume dei dati raccolti, ma dipende anche dal piano selezionato e dal periodo di tempo di archiviazione dei dati generati dalle origini connesse.  

In questo articolo viene esaminato come monitorare in modo proattivo il volume dei dati inseriti e l'aumento dello spazio di archiviazione e definire i limiti per controllare i costi associati. 

## <a name="pricing-model"></a>Modello di prezzi

L’impostazione predefinita dei prezzi per Log Analytics è un modello **con pagamento in base al consumo** basato sul volume di dati inserito e, facoltativamente, per la conservazione dei dati più lunga. Il volume dei dati viene misurato come dimensione dei dati che verranno archiviati in GB (10 ^ 9 byte). Ogni area di lavoro Log Analytics viene addebitata come servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure. L’importo per l’inserimento dati può essere notevole, a seconda dei fattori seguenti: 

  - Numero delle soluzioni di gestione abilitate e relativa configurazione
  - Numero di macchine virtuali monitorate
  - Tipo di dati raccolti da ciascuna VM monitorata 
  
Oltre al modello con pagamento in base al consumo, Log Analytics include livelli di **prenotazione della capacità** che consentono di risparmiare fino al 25% rispetto al prezzo con pagamento in base al consumo. I prezzi per la prenotazione della capacità consentono di acquistare una prenotazione a partire da 100 GB/giorno. Qualsiasi utilizzo sopra il livello di prenotazione verrà fatturato in base alla tariffa con pagamento in base al consumo. I livelli di prenotazione della capacità hanno un periodo di impegno di 31 giorni. Durante il periodo di impegno, è possibile passare a un livello di prenotazione di capacità superiore (che riavvierà il periodo di impegno di 31 giorni) ma non sarà possibile tornare a un livello di prenotazione di capacità inferiore o con pagamento in base al consumo fino al termine del periodo dell'impegno. La fatturazione per i livelli di prenotazione della capacità viene eseguita su base giornaliera. [Altre informazioni](https://azure.microsoft.com/pricing/details/monitor/) sui prezzi di prenotazione della capacità e con pagamento in base al consumo di Log Analytics. 

In tutti i piani tariffari, le dimensioni dei dati di un evento vengono calcolate in base a una rappresentazione di stringa delle proprietà archiviate in Log Analytics per questo evento, indipendentemente dal fatto che i dati vengano inviati da un agente o aggiunti durante il processo di inserimento. Sono inclusi i [campi personalizzati](custom-fields.md) aggiunti quando i dati vengono raccolti e quindi archiviati in log Analytics. Diverse proprietà comuni a tutti i tipi di dati, incluse alcune [log Analytics proprietà standard](./log-standard-columns.md), vengono escluse nel calcolo delle dimensioni dell'evento. Sono inclusi `_ResourceId` ,,, `_SubscriptionId` `_ItemId` `_IsBillable` `_BilledSize` e `Type` . Tutte le altre proprietà archiviate nel Log Analytics sono incluse nel calcolo delle dimensioni dell'evento. Alcuni tipi di dati sono privi di addebiti per l'inserimento dei dati, ad esempio AzureActivity, heartbeat e tipi di utilizzo. Per determinare se un evento è stato escluso dalla fatturazione per l'inserimento dei dati, è possibile usare la `_IsBillable` proprietà come illustrato di [seguito](#data-volume-for-specific-events). L'utilizzo è riportato in GB (1.0 E9 byte). 

Si noti anche che alcune soluzioni, ad esempio [Azure Defender (Centro sicurezza)](https://azure.microsoft.com/pricing/details/azure-defender/), [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) e la [gestione della configurazione](https://azure.microsoft.com/pricing/details/automation/) hanno modelli tariffari propri. 

### <a name="log-analytics-dedicated-clusters"></a>Cluster dedicati di Log Analytics

I cluster dedicati di Log Analytics sono raccolte di aree di lavoro in un singolo cluster di Esplora dati di Azure gestito per supportare scenari avanzati, ad esempio [chiavi gestite dal cliente](customer-managed-keys.md).  Log Analytics cluster dedicati usano un modello di determinazione dei prezzi per la prenotazione della capacità, che deve essere configurato per almeno 1000 GB/giorno. Questo livello di capacità ha uno sconto del 25% rispetto ai prezzi con pagamento in base al consumo. Qualsiasi utilizzo sopra il livello di prenotazione verrà fatturato in base alla tariffa con pagamento in base al consumo. La prenotazione della capacità del cluster ha un periodo di impegno di 31 giorni dopo l'incremento del livello di prenotazione. Durante il periodo di impegno non è possibile ridurre il livello di prenotazione della capacità, ma è possibile aumentarlo in qualsiasi momento. Quando le aree di lavoro sono associate a un cluster, la fatturazione per l'inserimento dei dati per tali aree di lavoro viene eseguita a livello di cluster usando il livello di prenotazione di capacità configurato. Altre informazioni sulla [creazione di un cluster di Log Analytics](customer-managed-keys.md#create-cluster) e l’[associazione di aree di lavoro ad esso](customer-managed-keys.md#link-workspace-to-cluster). Le informazioni sui prezzi per la prenotazione della capacità sono disponibili nella [pagina dei prezzi di monitoraggio di Azure]( https://azure.microsoft.com/pricing/details/monitor/).  

Il livello di prenotazione della capacità del cluster viene configurato tramite a livello di codice con Azure Resource Manager utilizzando il `Capacity` parametro in `Sku` . La `Capacity` viene specificata in unità di GB e può includere valori di 1000 GB/giorno o più in incrementi di 100 GB al giorno. Questa operazione è descritta in dettaglio nella [chiave gestita dal cliente di monitoraggio di Azure](customer-managed-keys.md#create-cluster). Se il cluster necessita di una prenotazione superiore a 2000 GB/giorno, contattare [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com).

Sono disponibili due modalità di fatturazione per l'utilizzo in un cluster. Questi possono essere specificati dal `billingType` parametro durante la [configurazione del cluster](customer-managed-keys.md#customer-managed-key-operations). Le due modalità sono: 

1. **Cluster**: in questo caso (impostazione predefinita), la fatturazione per i dati inseriti viene eseguita a livello di cluster. Le quantità di dati inseriti da ogni area di lavoro associata a un cluster vengono aggregate per calcolare la fattura giornaliera per il cluster. Si noti che le allocazioni per nodo da [Azure Defender (Centro sicurezza)](../../security-center/index.yml) vengono applicate a livello di area di lavoro prima di questa aggregazione di dati aggregati in tutte le aree di lavoro del cluster. 

2. **Aree di lavoro**: i costi di prenotazione della capacità per il cluster sono attribuiti proporzionalmente alle aree di lavoro del cluster, dopo aver tenuto conto delle allocazioni per nodo da [Azure Defender (Centro sicurezza)](../../security-center/index.yml) per ogni area di lavoro. Se il volume totale dei dati inserito in un'area di lavoro per un giorno è inferiore alla prenotazione della capacità, ogni area di lavoro viene fatturata per i dati inseriti in base al tasso di prenotazione della capacità per GB, fatturando una frazione della prenotazione di capacità e la parte inutilizzata della prenotazione di capacità viene fatturata alla risorsa cluster. Se il volume totale dei dati inserito in un'area di lavoro per un giorno è superiore alla prenotazione della capacità, ogni area di lavoro viene fatturata per una frazione della prenotazione di capacità in base alla frazione dei dati inseriti in quel giorno e a ogni area di lavoro per una frazione dei dati inseriti sopra la prenotazione della capacità. Se il volume totale dei dati inserito in un'area di lavoro per un giorno è superiore alla prenotazione di capacità, non viene addebitato alcun valore alla risorsa cluster.

Nelle opzioni di fatturazione del cluster, la conservazione dei dati viene fatturata in base all'area di lavoro. Tenere presente che la fatturazione del cluster viene avviata quando viene creato il cluster, indipendentemente dal fatto che le aree di lavoro siano state associate al cluster. Si noti inoltre che le aree di lavoro associate a un cluster non hanno più un piano tariffario.

## <a name="estimating-the-costs-to-manage-your-environment"></a>Stima dei costi per la gestione dell'ambiente 

Se non si usano ancora i log di Monitoraggio di Azure, è possibile usare il [calcolatore dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/calculator/?service=monitor) per stimare il costo dell'uso di Log Analytics. Per iniziare, immettere "Monitoraggio di Azure" nella casella di ricerca e fare clic sul riquadro Monitoraggio di Azure che viene visualizzato. Scorrere la pagina verso il basso fino a Monitoraggio di Azure e selezionare Log Analytics dall'elenco a discesa Tipo.  Qui è possibile immettere il numero di macchine virtuali e i GB di dati che si prevede di raccogliere da ogni macchina virtuale. In genere vengono inseriti da 1 a 3 GB di dati mensili viene inserito da una tipica macchina virtuale di Azure. Se si stanno già valutando i log di Monitoraggio di Azure, è possibile usare le statistiche dei dati del proprio ambiente. Vedere di seguito per informazioni su come determinare il [numero di macchine virtuali monitorate](#understanding-nodes-sending-data) e il [volume di dati che l'area di lavoro sta inserendo](#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Comprendere l'utilizzo e stimare i costi

Se si sta utilizzando al momento i log di Monitoraggio di Azure, è facile capire quali sono i costi probabili in base ai modelli di utilizzo recenti. A tale scopo, usare la pagina **Utilizzo e costi stimati di Log Analytics** per rivedere e analizzare l'utilizzo dei dati. Questa pagina mostra quanti dati vengono raccolti da ogni soluzione, quanti dati vengono conservati e una stima dei costi in base alla quantità di dati inseriti e a eventuali altri dati conservati oltre la quantità inclusa.

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="Uso e costi stimati":::

Per esplorare i dati in maggiore dettaglio, fare clic sull'icona nell'angolo superiore destro di uno dei grafici nella pagina **Utilizzo e costi stimati**. Ora è possibile usare questa query per esplorare i dettagli dell'utilizzo.  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="Visualizzazione dei log":::

Dalla pagina **Utilizzo e costi stimati** è possibile rivedere il volume dei dati per il mese. Sono inclusi tutti i dati fatturabili ricevuti e conservati nell'area di lavoro Log Analytics.  
 
I costi di Log Analytics vengono addebitati nella fattura di Azure. È possibile visualizzare i dettagli della fattura di Azure nella sezione Fatturazione del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Visualizzazione dell'utilizzo di Log Analytics nella fattura di Azure 

Azure fornisce una grande quantità di funzionalità utili nell'hub [Gestione dei costi e fatturazione](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json). Ad esempio, la funzionalità di analisi dei costi consente di visualizzare le spese per le risorse di Azure. In primo luogo, aggiungere un filtro in base a "tipo di risorsa" (a Microsoft. operationalinsights/Workspace per Log Analytics e Microsoft. operationalinsights/cluster per i cluster Log Analytics) consente di tenere traccia del Log Analytics spesa. Quindi, per "Raggruppa per", selezionare "Categoria del contatore" o "Contatore".  Si noti che altri servizi, ad esempio Azure Defender (Centro sicurezza) e Azure Sentinel, fatturano anche l'utilizzo in Log Analytics risorse dell'area di lavoro. Per visualizzare il mapping al nome del servizio, è possibile selezionare la visualizzazione Tabella anziché un grafico. 

Una maggiore comprensione dell'utilizzo può essere ottenuta [scaricando l'utilizzo dal portale di Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Nel foglio di calcolo scaricato è possibile visualizzare l'utilizzo per ogni risorsa di Azure (ad esempio area di lavoro Log Analytics) al giorno. In questo foglio di calcolo di Excel, l'utilizzo delle aree di lavoro Log Analytics è disponibile per il primo filtro sulla colonna "categoria contatore" per visualizzare "Log Analytics", "Insight and Analytics (usato da alcuni piani tariffari legacy) e" monitoraggio di Azure "(usati dai piani tariffari per la prenotazione della capacità), quindi aggiungere un filtro sulla colonna" ID istanza "che è" Contains Workspace "o" Contains cluster "(il secondo per includere Log Analytics utilizzo del cluster). L'utilizzo viene visualizzato nella colonna "Quantità utilizzata" e l'unità per ogni voce viene visualizzata nella colonna "Unità di misura".  Maggiori dettagli sono disponibili in [Comprendere la fattura di Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md). 

## <a name="changing-pricing-tier"></a>Modifica del piano tariffario

Per modificare il piano tariffario di Log Analytics dell'area di lavoro, 

1. Nel portale di Azure aprire **Utilizzo e costi stimati** dall'area di lavoro in cui verrà visualizzato un elenco di ognuno dei piani tariffari disponibili per l'area di lavoro.

2. Esaminare i costi stimati per ognuno dei piani tariffari. Questa stima è basata sugli ultimi 31 giorni di utilizzo, quindi la stima dei costi si basa sugli ultimi 31 giorni rappresentativi dell'utilizzo tipico. Nell'esempio seguente è possibile vedere in che modo, in base ai modelli di dati degli ultimi 31 giorni, l'area di lavoro potrebbe avere un costo inferiore nel piano con pagamento in base al consumo (1) rispetto al livello di prenotazione della capacità di 100 GB/giorno (2).  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="Piani tariffari":::
    
3. Dopo aver esaminato i costi stimati in base agli ultimi 31 giorni di utilizzo, se si decide di modificare il piano tariffario, fare clic su **Seleziona**.  

È anche possibile [impostare il piano tariffario tramite Azure Resource Manager](./resource-manager-workspace.md) usando il parametro `sku` (`pricingTier` nel modello Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Piani tariffari legacy

Le sottoscrizioni che includevano un'area di lavoro Log Analytics o una risorsa Application Insights prima del 2 aprile 2018 o sono collegate a un contratto Enterprise avviato prima del 1° febbraio 2019, continueranno ad avere accesso per usare i piani tariffari legacy: **Gratuito**, **Autonomo (per GB)** e **Per Nodo (OMS)** .  Le aree di lavoro nel piano tariffario gratuito avranno un inserimento dati giornaliero limitato a 500 MB (eccetto i tipi di dati di sicurezza raccolti da [Azure Defender (Centro sicurezza)](../../security-center/index.yml)) e la conservazione dei dati è limitata a 7 giorni. Il piano tariffario Gratuito è destinato solo a scopo di valutazione. Le aree di lavoro nei piani tariffari Autonomo o Per nodo prevedono una conservazione configurabile dall'utente da 30 a 730 giorni.

L'utilizzo del piano tariffario autonomo viene fatturato in base al volume di dati inserito. Viene segnalato nel servizio **log Analytics** e il contatore è denominato "data analyzed". 

Il piano tariffario Per nodo viene addebitato per ogni macchina virtuale monitorata (nodo) in base a una granularità oraria. Per ogni nodo monitorato, all'area di lavoro vengono allocati 500 MB di dati al giorno che non vengono fatturati. Questa allocazione viene calcolata con granularità oraria e viene aggregata a livello di area di lavoro ogni giorno. I dati inseriti in eccedenza rispetto all'allocazione giornaliera aggregata di dati vengono fatturati per GB come eccedenza di dati. Si noti che nella fattura il servizio sarà **Informazioni dettagliate e analisi** per l'utilizzo di Log Analytics se l'area di lavoro rientra nel piano tariffario Per nodo. L'utilizzo viene segnalato in tre contatori:

1. Node: utilizzo per il numero di nodi monitorati (VM) in unità di nodo * mesi.
2. Dati in eccedenza per nodo: numero di GB di dati inseriti in eccesso nell'allocazione dei dati aggregati.
3. Dati inclusi per nodo: questa è la quantità di dati inseriti che è stata analizzata nell'allocazione dei dati aggregati. Questo contatore viene usato anche quando l'area di lavoro è in tutti i piani tariffari per visualizzare la quantità di dati analizzati da Azure Defender (Centro sicurezza).

> [!TIP]
> Se l'area di lavoro ha accesso al piano tariffario **Per nodo**, ma non si è sicuri se il costo con pagamento in base al consumo sarebbe inferiore, è possibile [usare la query seguente](#evaluating-the-legacy-per-node-pricing-tier) per ottenere facilmente un suggerimento. 

Le aree di lavoro create prima di aprile 2016 possono anche accedere ai piani tariffari **Standard** e **Premium** con una conservazione dei dati fissa rispettivamente di 30 e 365 giorni. Non è possibile creare nuove aree di lavoro nei piani tariffari **Standard** o **Premium** e se un'area di lavoro viene spostata fuori da questi piani, non sarà farla rientrare nuovamente. I contatori di inserimento dei dati per questi livelli legacy sono detti "dati analizzati".

Esistono anche alcuni comportamenti tra l'uso di livelli di Log Analytics legacy e il modo in cui viene addebitato l'utilizzo di [Azure Defender (Centro sicurezza)](../../security-center/index.yml). 

1. Se l'area di lavoro è nel livello legacy standard o Premium, Azure Defender verrà fatturato solo per Log Analytics inserimento dati e non per nodo.
2. Se l'area di lavoro è nel livello legacy per nodo, Azure Defender verrà fatturato usando il [modello di determinazione prezzi basato sui nodi di Azure Defender](https://azure.microsoft.com/pricing/details/security-center/)corrente. 
3. In altri piani tariffari (incluse le prenotazioni di capacità), se Azure Defender è stato abilitato prima del 19 giugno 2017, Azure Defender verrà fatturato solo per Log Analytics inserimento dati. In caso contrario, Azure Defender verrà fatturato usando il modello di determinazione prezzi basato sui nodi di Azure Defender corrente.

Altre informazioni sulle limitazioni del piano tariffario sono disponibili in [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).

Nessuno dei piani tariffari legacy prevede prezzi a livello di area.  

> [!NOTE]
> Per usare i diritti che derivano dall'acquisto di OMS E1 Suite, OMS E2 Suite o un componente aggiuntivo di OMS per System Center, scegliere il piano tariffario *Per nodo* di Log Analytics.

## <a name="log-analytics-and-azure-defender-security-center"></a>Log Analytics e Azure Defender (Centro sicurezza)

La fatturazione di [Azure Defender (Centro sicurezza)](../../security-center/index.yml) è strettamente legata alla fatturazione log Analytics. Azure Defender fornisce l'allocazione di 500 MB/nodo/giorno rispetto ai seguenti subset di [tipi di dati di sicurezza](/azure/azure-monitor/reference/tables/tables-category#security) (WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, windowsfirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus) e ai tipi di dati Update e UpdateSummary quando la soluzione Gestione aggiornamenti non è in esecuzione nell'area di lavoro o la destinazione della soluzione è abilitata [altre informazioni](https://docs.microsoft.com/azure/security-center/security-center-pricing#what-data-types-are-included-in-the-500-mb-free-data-limit). Se l'area di lavoro è in un piano tariffario legacy per nodo, le allocazioni di Azure Defender e Log Analytics vengono combinate e applicate congiuntamente a tutti i dati inseriti fatturabili.  

## <a name="change-the-data-retention-period"></a>Cambiare il periodo di conservazione dei dati

La procedura seguente descrive come configurare il periodo di conservazione dei dati nell'area di lavoro. La conservazione dei dati a livello di area di lavoro può essere configurata da 30 a 730 giorni (2 anni) per tutte le aree di lavoro, a meno che non utilizzi il piano tariffario gratuito legacy. La conservazione per i singoli tipi di dati può essere impostata su un minimo di 4 giorni. Sono disponibili [altre informazioni](https://azure.microsoft.com/pricing/details/monitor/) sui prezzi per la conservazione dei dati più a lungo termine.  Per conservare i dati più lunghi di 730 giorni, è consigliabile utilizzare l' [esportazione dei dati dell'area di lavoro log Analytics](logs-data-export.md).

### <a name="workspace-level-default-retention"></a>Conservazione predefinita a livello di area di lavoro

Per impostare la conservazione predefinita per l'area di lavoro, 
 
1. Nell’area di lavoro del portale di Azure, selezionare **Utilizzo e costi stimati** nel riquadro a sinistra.
2. Nella pagina **Utilizzo e costi stimati** fare clic su **Conservazione dei dati** nella parte superiore della pagina.
3. Nel riquadro spostare il dispositivo di scorrimento per aumentare o diminuire il numero di giorni e quindi fare clic su **OK**.  Se si usa il livello *gratuito*, non è possibile modificare il periodo di conservazione dei dati ed è necessario eseguire l'aggiornamento al piano a pagamento per controllare questa impostazione.

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="Modificare l'impostazione di conservazione dei dati dell'area di lavoro":::

Quando il periodo di conservazione viene ridotto, è previsto un periodo di prova di diversi giorni prima che i dati precedenti alla nuova impostazione di conservazione vengano rimossi. 

La pagina **conservazione dati** consente le impostazioni di conservazione 30, 31, 60, 90, 120, 180, 270, 365, 550 e 730 giorni. Se è necessaria un'altra impostazione, è possibile configurarla utilizzando [Azure Resource Manager](./resource-manager-workspace.md) utilizzando il `retentionInDays` parametro. Quando si imposta la conservazione dei dati su 30 giorni, è possibile attivare un'eliminazione immediata dei dati meno recenti utilizzando il `immediatePurgeDataOn30Days` parametro (eliminando il periodo di tolleranza di diversi giorni). Questo può essere utile per gli scenari correlati alla conformità in cui è imperativa la rimozione immediata dei dati. Questa funzionalità di ripulitura immediata viene esposta solo tramite Azure Resource Manager. 

Le aree di lavoro con conservazione di 30 giorni possono effettivamente conservare i dati per 31 giorni. Se è fondamentale che i dati vengano conservati solo per 30 giorni, utilizzare il Azure Resource Manager per impostare la conservazione su 30 giorni e con il `immediatePurgeDataOn30Days` parametro.  

Due tipi di dati, `Usage` e `AzureActivity`, vengono conservati per un minimo di 90 giorni per impostazione predefinita e non è previsto alcun addebito per il periodo di conservazione di 90 giorni. Se la conservazione dell'area di lavoro viene aumentata oltre 90 giorni, viene anche aumentata la conservazione di questi tipi di dati.  Anche per questi tipi di dati non sono previsti addebiti per l'inserimento di dati. 

Anche i tipi di dati delle risorse Application Insights basate sull'area di lavoro (`AppAvailabilityResults`, `AppBrowserTimings`, `AppDependencies`, `AppExceptions`, `AppEvents`, `AppMetrics`, `AppPageViews`, `AppPerformanceCounters`, `AppRequests`, `AppSystemEvents` e `AppTraces`) vengono conservati per 90 giorni per impostazione predefinita e non è previsto alcun addebito per un periodo di conservazione di 90 giorni. La conservazione può essere regolata usando la funzionalità di conservazione per tipo di dati. 

Notare che l'[API di rimozione](/rest/api/loganalytics/workspacepurge/purge) di Log Analytics non influisce sulla fatturazione della conservazione e deve essere usata per casi molto limitati. Per ridurre la fattura di conservazione, è necessario ridurre il periodo di conservazione per l'area di lavoro o per tipi di dati specifici. 

### <a name="retention-by-data-type"></a>Conservazione per tipo di dati

È anche possibile specificare diverse impostazioni di conservazione per i singoli tipi di dati da 4 a 730 giorni (ad eccezione delle aree di lavoro nel piano tariffario gratuito legacy) che sostituiscono la conservazione predefinita a livello di area di lavoro. Ogni tipo di dati è una risorsa secondaria dell'area di lavoro. Ad esempio, la tabella SecurityEvent può essere considerata in [Azure Resource Manager](../../azure-resource-manager/management/overview.md) come:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Tenere presente che il tipo di dati (tabella) supporta la distinzione tra maiuscole e minuscole.  Per ottenere le impostazioni di conservazione correnti per i tipi di dati di un particolare tipo di dati (in questo esempio SecurityEvent), usare:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> La conservazione viene restituita solo per un tipo di dati se il periodo di memorizzazione è stato impostato in modo esplicito.  I tipi di dati che non hanno impostato la conservazione in modo esplicito (e pertanto ereditano la conservazione dell'area di lavoro) non restituiranno alcun risultato da questa chiamata. 

Per ottenere le impostazioni di conservazione del tipo per dati correnti per tutti i tipi di dati nell'area di lavoro per cui è stato impostato il mantenimento dei tipi per dati, è sufficiente omettere il tipo di dati specifico, ad esempio:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Per impostare la conservazione di un determinato tipo di dati (in questo esempio SecurityEvent) su 730 giorni, fare quanto segue

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

I valori validi per `retentionInDays` sono compresi tra 30 e 730.

I tipi di dati `Usage` e `AzureActivity` non possono essere impostati con la conservazione personalizzata. Verranno accettati al massimo la conservazione predefinita dell'area di lavoro o 90 giorni. 

Un ottimo strumento per connettersi direttamente ad Azure Resource Manager per impostare la conservazione in base al tipo di dati è lo strumento OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Per altre informazioni su ARMclient, vedere gli articoli di [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Ecco un esempio di uso di ARMClient, che imposta i dati di SecurityEvent su un periodo di conservazione di 730 giorni:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Per ridurre i costi di conservazione dei dati, è possibile utilizzare l'impostazione della conservazione per i singoli tipi di dati.  Per i dati raccolti a partire da ottobre 2019 (quando questa funzionalità è stata rilasciata), la riduzione della conservazione per alcuni tipi di dati può ridurre il costo di conservazione nel tempo.  Per i dati raccolti in precedenza, l'impostazione di un periodo di conservazione inferiore per un singolo tipo non influirà sui costi di conservazione.  

## <a name="manage-your-maximum-daily-data-volume"></a>Gestire il volume di dati giornaliero massimo

È possibile configurare un limite giornaliero e limitare l'inserimento giornaliero per l'area di lavoro, tuttavia è necessario tenere presente che l'obiettivo non deve essere quello di raggiungere tale limite.  In caso contrario, si perdono i dati per il resto del giorno, situazione che può influire su altri servizi e soluzioni di Azure la cui funzionalità può dipendere da dati aggiornati disponibili nell'area di lavoro.  Di conseguenza, la possibilità di osservare e ricevere avvisi quando le condizioni di integrità delle risorse che supportano i servizi IT sono interessate.  Il limite giornaliero è destinato a essere utilizzato come metodo per gestire un **aumento imprevisto** del volume di dati dalle risorse gestite e rimanere entro il limite o quando si desidera limitare gli addebiti non pianificati per l'area di lavoro. Non è consigliabile impostare un limite giornaliero in modo che venga raggiunto ogni giorno in un'area di lavoro.

Ogni area di lavoro ha il limite giornaliero applicato in un'ora diversa del giorno. L'ora di reimpostazione viene visualizzata nella pagina **limite giornaliero** (vedere di seguito). Questa ora di ripristino non può essere configurata. 

Non appena viene raggiunto il limite giornaliero, la raccolta di tipi di dati fatturabili viene arrestata per il resto del giorno. La latenza insita nell'applicazione del limite giornaliero significa che il limite non viene applicato esattamente al livello di limite giornaliero specificato. Nella parte superiore della pagina per l'area di lavoro Log Analytics selezionata viene visualizzato un banner di avviso e viene inviato un evento di tipo operazione alla tabella *Operazione* nella categoria **LogManagement**. La raccolta dati riprende dopo l'ora di ripristino definita in *Daily limit will be set at* (Ora impostazione limite giornaliero). Si consiglia di definire una regola di avviso in base a questo evento dell'operazione, configurata per notificare quando è stato raggiunto il limite giornaliero dei dati (vedere di [seguito](#alert-when-daily-cap-reached)). 

> [!NOTE]
> Il limite giornaliero non può arrestare la raccolta dati esattamente come il livello di estremità specificato e sono previsti alcuni dati in eccesso, in particolare se l'area di lavoro riceve volumi elevati di dati. Vedere di [seguito](#view-the-effect-of-the-daily-cap) per una query utile per studiare il comportamento del limite giornaliero. 

> [!WARNING]
> Il limite giornaliero non interrompe la raccolta dei tipi di dati WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus, Update e UpdateSummary, ad eccezione delle aree di lavoro in cui Azure Defender (Centro sicurezza) è stato installato prima del 19 giugno 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificare la soglia dei dati giornaliera da definire

Vedere [Utilizzo e costi stimati di Log Analytics](../usage-estimated-costs.md) per informazioni sulla tendenza nell'inserimento dati e sul limite di volume giornaliero da definire. Fare attenzione nella scelta del limite, in quanto non sarà possibile monitorare le risorse una volta raggiunto il limite. 

### <a name="set-the-daily-cap"></a>Impostare il limite giornaliero

Di seguito viene descritto come configurare un limite per gestire il volume di dati che verranno inseriti dall’area di lavoro Log Analytics al giorno.  

1. Nell'area di lavoro selezionare **Utilizzo e costi stimati** nel riquadro a sinistra.
2. Nella pagina **utilizzo e costi stimati** per l'area di lavoro selezionata, fare clic su **protezione dati** nella parte superiore della pagina. 
3. Per impostazione predefinita, il limite giornaliero è impostato su **DISATTIVA**. Fare clic su **ATTIVA** per abilitarlo e quindi impostare il limite di volume di dati in GB/giorno.

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Configurazione della soglia dei dati in Log Analytics":::
    
Il limite giornaliero può essere configurato tramite ARM impostando il `dailyQuotaGb` parametro in `WorkspaceCapping` come descritto in [area di lavoro-crea o aggiorna](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping). 

È possibile tenere traccia delle modifiche apportate al limite giornaliero usando questa query:

```kusto
_LogOperation | where Operation == "Workspace Configuration" | where Detail contains "Daily quota"
```

Altre informazioni sulla funzione [_LogOperation](https://docs.microsoft.com/azure/azure-monitor/logs/monitor-workspace) . 

### <a name="view-the-effect-of-the-daily-cap"></a>Visualizzare l'effetto del limite giornaliero

Per visualizzare l'effetto del limite giornaliero, è importante tenere conto dei tipi di dati di sicurezza non inclusi nel limite giornaliero e dell'ora di reimpostazione per l'area di lavoro. L'ora di ripristino del limite giornaliero è visibile nella pagina **limite giornaliero** .  La query seguente può essere utilizzata per tenere traccia dei volumi di dati soggetti al limite giornaliero tra le reimpostazioni del limite giornaliero. In questo esempio, l'ora di ripristino dell'area di lavoro è 14:00.  È necessario aggiornarlo per l'area di lavoro.

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

Nel tipo di dati Usage le unità di `Quantity` sono in MB.

### <a name="alert-when-daily-cap-reached"></a>Avvisa al raggiungimento del limite giornaliero

Anche se nel portale di Azure viene visualizzato un segnale visivo quando viene raggiunta la soglia dei dati, questo comportamento potrebbe non soddisfare le esigenze aziendali per la gestione di problemi operativi che richiedono attenzione immediata.  Per ricevere una notifica di avviso, è possibile creare una nuova regola di avviso in Monitoraggio di Azure.  Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](../alerts/alerts-metric.md).

Per iniziare, di seguito sono riportate le impostazioni consigliate per l'avviso che esegue una query sulla `Operation` tabella usando la `_LogOperation` funzione ([altre informazioni](https://docs.microsoft.com/azure/azure-monitor/logs/monitor-workspace)). 

- Destinazione: Selezionare la risorsa di Log Analytics
- Criteri: 
   - Nome segnale: Ricerca log personalizzata
   - Query di ricerca: `_LogOperation | where Operation == "Data collection Status" | where Detail contains "OverQuota"`
   - In base a: Numero di risultati
   - Condizione: Maggiore di
   - Soglia: 0
   - Periodo: 5 (minuti)
   - Frequenza: 5 (minuti)
- Nome regola di avviso: Soglia dei dati giornaliera raggiunta
- Gravità: Avviso (Gravità 1)

Una volta definito l'avviso e raggiunto il limite, viene attivato un avviso e viene eseguita la risposta definita nel gruppo di azioni. È possibile informare il team tramite posta elettronica e SMS oppure automatizzare le operazioni usando webhook o runbook di Automazione oppure tramite l'[integrazione con una soluzione di Gestione dei servizi IT esterna](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Risoluzione dei problemi che determinano un utilizzo superiore al previsto

Un utilizzo più elevato è dovuto a una o entrambe le cause seguenti:
- Più nodi del previsto inviano dati area di lavoro Log Analytics
- Vengono inviati più dati del previsto all'area di lavoro Log Analytics (forse perché si è iniziato a utilizzare una nuova soluzione o a causa di una modifica della configurazione di una soluzione esistente)

## <a name="understanding-nodes-sending-data"></a>Informazioni sui nodi che inviano dati

Per conoscere il numero di nodi che hanno segnalato heartbeat dell’agente ogni giorno nell'ultimo mese, usare

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Per ottenere un conteggio dei nodi che inviano dati nelle ultime 24 ore, usare la query: 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Per ottenere un elenco di nodi che inviano dati (e la quantità di dati inviati da ognuno), è possibile usare la query seguente:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>Nodi fatturati dal piano tariffario legacy per nodo

Le fatture del piano [tariffario per nodo Legacy](#legacy-pricing-tiers) per i nodi con granularità oraria e non contano solo i nodi che inviano solo un set di tipi di dati di sicurezza. Il conteggio giornaliero dei nodi si avvicina alla query seguente:

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

Il numero di unità nella fattura si trova in unità di nodo * mesi rappresentate da `billableNodeMonthsPerDay` nella query. Se per l'area di lavoro è installata la soluzione Gestione aggiornamenti, aggiungere i tipi di dati Update e UpdateSummary all'elenco nella clausola WHERE della query precedente. Infine, esistono alcune complessità aggiuntive nell'algoritmo di fatturazione effettivo quando si usa la destinazione della soluzione che non è rappresentata nella query precedente. 


> [!TIP]
> Usare queste query `find` solo se necessario, poiché le analisi tra tipi di dati comportano un [elevato utilizzo di risorse](./query-optimization.md#query-performance-pane) per l’esecuzione. Se non sono necessari risultati **per computer**, eseguire una query sul tipo di dati Utilizzo (vedere di seguito).

## <a name="understanding-ingested-data-volume"></a>Informazioni sul volume di dati inseriti

Nella pagina **Utilizzo e costi stimati** il grafico *Inserimento dati per soluzione* mostra il volume totale dei dati inviati e la quantità inviata da ogni soluzione. In questo modo è possibile determinare tendenze specifiche, ad esempio se l'utilizzo dei dati complessivo (o da parte di una particolare soluzione) sta aumentando, è stabile o sta diminuendo. 

### <a name="data-volume-for-specific-events"></a>Volume di dati per eventi specifici

Per esaminare le dimensioni dei dati inseriti per un determinato set di eventi, è possibile eseguire una query sulla tabella specifica (in questo esempio `Event`) e quindi limitare la query agli eventi di interesse (in questo esempio ID evento 5145 o 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Si noti che la clausola `where _IsBillable = true` esclude i tipi di dati da determinate soluzioni per le quali non è addebitato alcun inserimento. [Altre](./log-standard-columns.md#_isbillable) informazioni su `_IsBillable` .

### <a name="data-volume-by-solution"></a>Volume dati per soluzione

La query utilizzata per visualizzare il volume di dati fatturabile per soluzione nell'ultimo mese (escluso l'ultimo giorno parziale) può essere compilata utilizzando il tipo di dati [Usage](https://docs.microsoft.com/azure/azure-monitor/reference/tables/usage) come:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

La clausola con `TimeGenerated` è solo per assicurarsi che l'esperienza di query nel portale di Azure esegue la ricerca oltre le 24 ore predefinite. Quando si usa il tipo di dati Utilizzo, `StartTime` e `EndTime` rappresentano i bucket temporali per i quali vengono presentati i risultati. 

### <a name="data-volume-by-type"></a>Volume dati per tipo

È possibile eseguire un ulteriore drill-in per visualizzare le tendenze dei dati per tipo di dati:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

In alternativa, per visualizzare una tabella in base alla soluzione e al tipo per l'ultimo mese,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Volume dati per computer

Il tipo di dati `Usage` non include informazioni a livello di computer. Per visualizzare le **dimensioni** dei dati fatturabili inseriti per computer, utilizzare la `_BilledSize` [proprietà](./log-standard-columns.md#_billedsize), che fornisce la dimensione in byte:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer, Type
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes desc nulls last
```

La [proprietà](./log-standard-columns.md#_isbillable) `_IsBillable` specifica se i dati inseriti comporteranno addebiti. Il tipo di utilizzo viene omesso perché è solo per l'analisi delle tendenze dei dati. 

Per visualizzare il **numero** di eventi fatturabili inseriti per computer, usare 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount desc nulls last
```

> [!TIP]
> Usare queste query `find` solo se necessario, poiché le analisi tra tipi di dati comportano un [elevato utilizzo di risorse](./query-optimization.md#query-performance-pane) per l’esecuzione. Se non sono necessari risultati **per computer**, eseguire una query sul tipo di dati Utilizzo.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume di dati per risorse di Azure, gruppo di risorse o sottoscrizione

Per i dati dei nodi ospitati in Azure, è possibile ottenere le **dimensioni** dei dati inseriti __per computer__, quindi usare la [proprietà](./log-standard-columns.md#_resourceid) _ResourceId, che fornisce il percorso completo della risorsa:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Per i dati dei nodi ospitati in Azure, è possibile ottenere le **dimensioni** dei dati inseriti __per ogni sottoscrizione di Azure__. utilizzare la `_SubscriptionId` proprietà come:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, _SubscriptionId
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _SubscriptionId | sort by BillableDataBytes nulls last
```

Per ottenere il volume di dati per gruppo di risorse, è possibile analizzare `_ResourceId` :

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

È anche possibile analizzare `_ResourceId` più completamente se necessario usando

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> Usare queste query `find` solo se necessario, poiché le analisi tra tipi di dati comportano un [elevato utilizzo di risorse](./query-optimization.md#query-performance-pane) per l’esecuzione. Se non sono necessari i risultati per sottoscrizione, gruppo di risorse o nome della risorsa, eseguire una query sul tipo di dati di utilizzo.

> [!WARNING]
> Benché siano ancora inclusi nello schema, alcuni campi del tipo di dati Utilizzo sono stati deprecati e i rispettivi valori non verranno più popolati. Si tratta del campo **Computer** e dei campi correlati all'inserimento, ossia **TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.


### <a name="querying-for-common-data-types"></a>Esecuzione di query per i tipi di dati comuni

Ecco alcune query di esempio utili per analizzare in maggiore profondità l'origine dei dati di un particolare tipo di dati:

+ Risorse di **Application Insights basate sull’area di lavoro**
  - per altre informazioni [, vedere gestire l'utilizzo e i costi per Application Insights](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
+ Soluzione **Sicurezza**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Soluzione **Gestione log**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo di dati **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo di dati **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo di dati **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo di dati **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>Suggerimenti per ridurre il volume di dati

Ecco alcuni suggerimenti utili per ridurre il volume dei log raccolti:

| Origine del volume di dati elevato | Come ridurre il volume di dati |
| -------------------------- | ------------------------- |
| Regole di raccolta dati      | L' [agente di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) USA regole di raccolta dati per gestire la raccolta di dati. È possibile [limitare la raccolta di dati](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-azure-monitor-agent#limit-data-collection-with-custom-xpath-queries) usando query XPath personalizzate. | 
| Dati analitici sui contenitori         | [Configurare il contenitore Insights](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost) per raccogliere solo i dati necessari. |
| Eventi di sicurezza            | Selezionare gli [eventi di sicurezza comuni o minimi](../../security-center/security-center-enable-data-collection.md#data-collection-tier) <br> Modificare i criteri di controllo di sicurezza in modo che vengano raccolti solo gli eventi necessari. In particolare, esaminare la necessità di raccogliere eventi per: <br> - [controllo piattaforma filtro](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [controllo Registro di sistema](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [controllo file system](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [controllo oggetto kernel](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [controllo manipolazione handle](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - controllo archivi rimovibili |
| Contatori delle prestazioni       | Modificare la [configurazione del contatore delle prestazioni](../agents/data-sources-performance-counters.md) per: <br> - Ridurre la frequenza di raccolta <br> - Ridurre il numero di contatori delle prestazioni |
| Log eventi                 | Modificare la [configurazione del log eventi](../agents/data-sources-windows-events.md) per: <br> - Ridurre il numero di log eventi raccolti <br> - Raccogliere solo i livelli di eventi richiesti, ad esempio non raccogliendo gli eventi di livello *informazioni* |
| syslog                     | Modificare la [configurazione di Syslog](../agents/data-sources-syslog.md) per: <br> - Ridurre il numero di strutture raccolte <br> - Raccogliere solo i livelli di eventi richiesti, ad esempio non raccogliendo gli eventi di livello *informazioni* e *debug* |
| AzureDiagnostics           | Modificare la [raccolta di log delle risorse](../essentials/diagnostic-settings.md#create-in-azure-portal) in: <br> - Ridurre il numero di risorse che inviano log a Log Analytics <br> - Raccogliere solo i log necessari |
| Dati della soluzione da computer che non richiedono la soluzione | Usare il [targeting della soluzione](../insights/solution-targeting.md) per raccogliere dati unicamente dai gruppi di computer necessari |
| Application Insights | Esaminare le opzioni per la [gestione di Application Insights volume di dati](../app/pricing.md#managing-your-data-volume) |
| [SQL Analytics](../insights/azure-sql.md) | Usare [set-AzSqlServerAudit](/powershell/module/az.sql/set-azsqlserveraudit) per ottimizzare le impostazioni di controllo. |
| Azure Sentinel | Esaminare le [origini dati Sentinel](../../sentinel/connect-data-sources.md) che sono state recentemente abilitate come origini di un volume di dati aggiuntivo. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Recupero dei nodi come fatturati nel piano tariffario per nodo

Per ottenere un elenco di computer che verranno fatturati come nodi se l'area di lavoro è nel piano tariffario Per nodo legacy, cercare i nodi che inviano **tipi di dati fatturati** (alcuni tipi di dati sono gratuiti). A tale scopo, utilizzare la [proprietà](./log-standard-columns.md#_isbillable) `_IsBillable` e utilizzare il campo più a sinistra del nome di dominio completo. Viene restituito il numero di computer con dati fatturati all'ora (ovvero la granularità con cui i nodi vengono conteggiati e fatturati):

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Recupero dei conteggi dei nodi di sicurezza e automazione

Per visualizzare il numero di nodi di sicurezza distinti è possibile usare la query:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Per visualizzare il numero di nodi di Automazione distinti usare la query:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Valutazione del piano tariffario Per nodo Legacy

La decisione che consente di determinare se le aree di lavoro con accesso al piano tariffario **Per nodo** legacy sono migliori rispetto al piano **con pagamento in base al consumo** o **prenotazione della capacità** è spesso difficile da valutare per i clienti.  Questo implica la comprensione del compromesso tra il costo fisso per ogni nodo monitorato nel piano tariffario Per nodo e l'allocazione dei dati inclusa di 500 MB/nodo/giorno e il costo di pagamento solo per i dati inseriti nel piano con pagamento in base al consumo (per GB). 

Per facilitare questa valutazione, è possibile usare la query seguente per formulare un suggerimento per il piano tariffario ottimale in base ai modelli di utilizzo di un'area di lavoro.  Questa query esamina i nodi monitorati e i dati inseriti in un'area di lavoro negli ultimi 7 giorni e per ogni giorno valuta il piano tariffario ottimale. Per usare la query, è necessario specificare

1. indica se l'area di lavoro USA Azure Defender (Centro sicurezza) impostando `workspaceHasSecurityCenter` su `true` o `false` , 
2. aggiornare i prezzi se si dispone di sconti specifici e
3. specificare il numero di giorni per cui eseguire la ricerca e l'analisi impostando `daysToEvaluate`. Questa operazione è utile se la query richiede troppo tempo per esaminare 7 giorni di dati. 

Di seguito è riportata la query di raccomandazione del piano tariffario:

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CarRes100Price = 196.; // Enter your price for the 100 GB/day Capacity Reservation
let CarRes200Price = 368.; // Enter your price for the 200 GB/day Capacity Reservation
let CarRes300Price = 540.; // Enter your price for the 300 GB/day Capacity Reservation
let CarRes400Price = 704.; // Enter your price for the 400 GB/day Capacity Reservation
let CarRes500Price = 865.; // Enter your price for the 500 GB/day Capacity Reservation
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CapRes100DailyCost = CarRes100Price + max_of(billableGB - 100, 0.)* PerGBPrice
| extend CapRes200DailyCost = CarRes200Price + max_of(billableGB - 200, 0.)* PerGBPrice
| extend CapRes300DailyCost = CarRes300Price + max_of(billableGB - 300, 0.)* PerGBPrice
| extend CapRes400DailyCost = CarRes400Price + max_of(billableGB - 400, 0.)* PerGBPrice
| extend CapResLevel500AndAbove = max_of(floor(billableGB, 100),500)
| extend CapRes500AndAboveDailyCost = CarRes500Price*CapResLevel500AndAbove/500 + max_of(billableGB - CapResLevel500AndAbove, 0.)* PerGBPrice
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CapRes100DailyCost,CapRes200DailyCost,
    CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CapRes100DailyCost, "Capacity Reservation (100 GB/day)",
    MinCost == CapRes200DailyCost, "Capacity Reservation (200 GB/day)",
    MinCost == CapRes300DailyCost, "Capacity Reservation (300 GB/day)",
    MinCost == CapRes400DailyCost, "Capacity Reservation (400 GB/day)",
    MinCost == CapRes500AndAboveDailyCost, strcat("Capacity Reservation (",CapResLevel500AndAbove," GB/day)"),
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CapRes100DailyCost, CapRes200DailyCost, CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Questa query non è una replica esatta del modo in cui viene calcolato l'utilizzo, ma funzionerà per fornire raccomandazioni sui piani tariffari nella maggior parte dei casi.  

> [!NOTE]
> Per usare i diritti che derivano dall'acquisto di OMS E1 Suite, OMS E2 Suite o un componente aggiuntivo di OMS per System Center, scegliere il piano tariffario *Per nodo* di Log Analytics.

## <a name="create-an-alert-when-data-collection-is-high"></a>Creare un avviso quando la raccolta dati raggiunge quantità elevate

Questa sezione descrive come creare un avviso il volume di dati nelle ultime 24 ore ha superato un importo specificato, usando gli avvisi del [log](../alerts/alerts-unified-log.md)di monitoraggio di Azure. 

Per segnalare se il volume di dati fatturabile inserito nelle ultime 24 ore è superiore a 50 GB, attenersi alla procedura seguente: 

- Per **Definire la condizione dell'avviso**, specificare l'area di lavoro Log Analytics come destinazione della risorsa.
- Per **Criteri di avviso** specificare quanto segue:
   - Per **Nome segnale** selezionare **Ricerca log personalizzata**
   - Eseguire una **query di ricerca** in `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` . Se si desidera un'altra 
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *1440* minuti e **frequenza di avviso** a ogni *1440* minuti per l'esecuzione una volta al giorno.
- Per **Definire i dettagli dell'avviso** specificare quanto segue:
   - **Nome** del *volume di dati fatturabile superiore a 50 GB in 24 ore*
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](../alerts/action-groups.md) esistente o crearne uno nuovo, in modo da ricevere una notifica se l'avviso del log corrisponde ai criteri.

Quando si riceve un avviso, attenersi alla procedura illustrata nelle sezioni precedenti per informazioni su come risolvere i problemi relativi all'utilizzo superiore al previsto.

## <a name="data-transfer-charges-using-log-analytics"></a>Addebiti per il trasferimento dei dati tramite Log Analytics

L'invio di dati a Log Analytics può comportare addebiti per la larghezza di banda dei dati, tuttavia è limitato alle macchine virtuali in cui è installato un agente di Log Analytics e non si applica quando si usano le impostazioni di diagnostica o altri connettori incorporati in Sentinel di Azure. Come descritto nella [pagina dei prezzi per la larghezza di banda di Azure](https://azure.microsoft.com/pricing/details/bandwidth/), il trasferimento dei dati tra i servizi di Azure in due aree viene addebitato come trasferimento dati in uscita alla tariffa normale. Il trasferimento di dati in entrata è gratuito. Tuttavia, questo addebito è minimo (% ridotta) rispetto ai costi per l'inserimento dei dati Log Analytics. Il controllo dei costi per Log Analytics deve quindi concentrarsi sul [volume di dati](#understanding-ingested-data-volume)inserito. 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Risoluzione dei problemi se Log Analytics non sta più raccogliendo dati

Se si sta usando il piano tariffario legacy Gratuito e sono stati inviati più di 500 MB di dati in un giorno, la raccolta dati si interrompe per il resto del giorno. Il raggiungimento del limite giornaliero è un motivo comune per cui Log Analytics interrompe la raccolta dei dati o per cui i dati mancano.  Log Analytics crea un evento di tipo operazione quando la raccolta dati si avvia e si arresta. Eseguire la query seguente per verificare se si raggiunge il limite giornaliero e se i dati sono mancanti: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Quando la raccolta dati si interrompe, OperationStatus è **Avviso**. Quando la raccolta dati si avvia, OperationStatus è **Riuscito**. La tabella seguente descrive i motivi per cui raccolta dei dati si interrompe e un'azione consigliata per riprendere la raccolta dati:  

|Motivo dell'arresto della raccolta| Soluzione| 
|-----------------------|---------|
|È stato raggiunto il limite giornaliero dell'area di lavoro|Attendere il riavvio automatico della raccolta oppure aumentare il limite giornaliero per il volume di dati, come descritto nella sezione sulla gestione del volume di dati giornaliero massimo. Il tempo di reimpostazione limite giornaliero viene visualizzato nella pagina **limite giornaliero** . |
| L'area di lavoro ha raggiunto la [velocità del volume](../service-limits.md#log-analytics-workspaces) di inserimento dati | Il limite di velocità del volume di inserimento predefinito per i dati inviati dalle risorse di Azure con le impostazioni di diagnostica è di circa 6 GB/min per area di lavoro. Si tratta di un valore approssimativo, poiché la dimensione effettiva può variare tra i tipi di dati a seconda della lunghezza del log e del relativo rapporto di compressione. Questo limite non si applica ai dati inviati dagli agenti o dall'API di raccolta dati. Se si inviano dati a una velocità superiore a una singola area di lavoro, alcuni di questi vengono eliminati e un evento viene inviato alla tabella delle operazioni nell'area di lavoro ogni 6 ore mentre la soglia continua a essere superata. Se il volume di inserimento continua a superare il limite di velocità o se si prevede di raggiungerlo presto, è possibile richiedere un aumento per l'area di lavoro inviando un messaggio di posta elettronica a LAIngestionRate@microsoft.com o aprendo una richiesta di supporto. L'evento da cercare che indica un limite della velocità di inserimento dei dati è reperibile nella query `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"`. |
|Limite giornaliero del piano tariffario legacy Gratuito raggiunto |Attendere fino al giorno successivo per il riavvio automatico della raccolta oppure passare a un piano tariffario a pagamento.|
|La sottoscrizione di Azure è in sospeso perché:<br> la versione di prova gratuita è terminata<br> Azure Pass è scaduto<br> Il limite di spesa mensile è stato raggiunto, ad esempio in una sottoscrizione MSDN o in una sottoscrizione di Visual Studio|Passare a una sottoscrizione a pagamento<br> Rimuovere il limite oppure attendere fino ripristino del limite|

Per ricevere una notifica quando la raccolta dati si interrompe, seguire la procedura descritta in *Creare un limite di dati giornaliero*. Seguire la procedura descritta in [Creare un gruppo di azioni](../alerts/action-groups.md) per configurare un'azione di posta elettronica, webhook o runbook per la regola di avviso. 

## <a name="late-arriving-data"></a>Dati in arrivo in ritardo   

È possibile che si verifichino situazioni in cui i dati vengono inseriti con timestamp molto vecchi, ad esempio se un agente non è in grado di comunicare con Log Analytics a causa di un problema di connettività o di situazioni in cui un host ha una data e un'ora non corrette. Per diagnosticare questi problemi, utilizzare la `_TimeReceived` colonna ([altre informazioni](https://docs.microsoft.com/azure/azure-monitor/logs/log-standard-columns#_timereceived)) oltre alla `TimeGenerated` colonna. `TimeReceived` indica l'ora in cui il record è stato ricevuto dal punto di inserimento di monitoraggio di Azure nel cloud di Azure.  

## <a name="limits-summary"></a>Riepilogo dei limiti

Esistono alcuni limiti di Log Analytics aggiuntivi, alcuni dei quali dipendono dal piano tariffario di Log Analytics. Questi sono documentati in [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare il linguaggio di ricerca, vedere [Ricerche nei log di Monitoraggio di Azure](../logs/log-query-overview.md). È possibile usare le query di ricerca per eseguire ulteriori analisi sui dati di utilizzo.
- Per ricevere una notifica quando vengono soddisfatti determinati criteri di ricerca, seguire la procedura descritta in [Creare un nuovo avviso del log](../alerts/alerts-metric.md).
- Usare il [targeting della soluzione](../insights/solution-targeting.md) per raccogliere dati unicamente dai gruppi di computer necessari
- Per configurare un criterio di raccolta eventi efficace, vedere [criteri di filtro di Azure Defender (Centro sicurezza)](../../security-center/security-center-enable-data-collection.md).
- Modificare la [configurazione del contatore delle prestazioni](../agents/data-sources-performance-counters.md).
- Per modificare le impostazioni di raccolta degli eventi, vedere la [configurazione del registro eventi](../agents/data-sources-windows-events.md).
- Per modificare le impostazioni di raccolta di SysLog, vedere la [configurazione di SysLog](../agents/data-sources-syslog.md).
- Per modificare le impostazioni di raccolta di SysLog, vedere la [configurazione di SysLog](../agents/data-sources-syslog.md).
