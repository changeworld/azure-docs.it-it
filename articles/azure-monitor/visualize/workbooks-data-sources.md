---
title: Origini dati delle cartelle di lavoro di monitoraggio di Azure | Microsoft docs
description: Semplifica la creazione di report complessi con cartelle di lavoro di monitoraggio di Azure con parametri predefinite e personalizzate compilate da più origini dati
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 862c6c5253c1bb4481476b67c7cfb203c2568e24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700576"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Origini dati delle cartelle di lavoro di monitoraggio di Azure

Le cartelle di lavoro sono compatibili con un numero elevato di origini dati. In questo articolo vengono illustrate le origini dati attualmente disponibili per le cartelle di lavoro di monitoraggio di Azure.

## <a name="logs"></a>Log

Le cartelle di lavoro consentono di eseguire query sui log dalle origini seguenti:

* Log di monitoraggio di Azure (Application Insights risorse e aree di lavoro Log Analytics).
* Dati incentrati sulle risorse (log attività)

Gli autori delle cartelle di lavoro possono utilizzare query KQL che trasformano i dati delle risorse sottostanti per selezionare un set di risultati che può essere visualizzato come testo, grafici o griglie.

![Screenshot dell'interfaccia di report dei log delle cartelle di lavoro](./media/workbooks-data-sources/logs.png)

Gli autori delle cartelle di lavoro possono eseguire facilmente query tra più risorse creando un'esperienza di creazione di report avanzata.

## <a name="metrics"></a>Metriche

Le risorse di Azure emettono [metriche](../essentials/data-platform-metrics.md) a cui è possibile accedere tramite le cartelle di lavoro. È possibile accedere alle metriche nelle cartelle di lavoro tramite un controllo specializzato che consente di specificare le risorse di destinazione, le metriche desiderate e la relativa aggregazione. Questi dati possono quindi essere tracciati in grafici o griglie.

![Screenshot dei grafici di metriche delle cartelle di lavoro di utilizzo della CPU](./media/workbooks-data-sources/metrics-graph.png)

![Screenshot dell'interfaccia della metrica cartella di lavoro](./media/workbooks-data-sources/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

Le cartelle di lavoro supportano l'esecuzione di query per le risorse e i relativi metadati tramite Azure Resource Graph (ARG). Questa funzionalità viene utilizzata principalmente per creare ambiti di query personalizzati per i report. L'ambito della risorsa viene espresso tramite un subset di KQL supportato da ARG, che è spesso sufficiente per i casi d'uso comuni.

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa tipo di query per scegliere Azure Resource Graph e selezionare le sottoscrizioni di destinazione. Usare il controllo query per aggiungere il subset di KQL ARG che seleziona un subset di risorse interessante.

![Screenshot della query KQL Graph di risorse di Azure](./media/workbooks-data-sources/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

La cartella di lavoro supporta le operazioni REST Azure Resource Manager. In questo modo è possibile eseguire query sull'endpoint management.azure.com senza dover fornire il proprio token di intestazione dell'autorizzazione.

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa origine dati per scegliere Azure Resource Manager. Fornire i parametri appropriati, ad esempio il metodo HTTP, il percorso URL, le intestazioni, i parametri URL e/o il corpo.

> [!NOTE]
> `GET` `POST` `HEAD` Attualmente sono supportate solo le operazioni, e.

## <a name="azure-data-explorer"></a>Esplora dati di Azure

Le cartelle di lavoro ora includono il supporto per l'esecuzione di query da cluster [Esplora dati di Azure](/azure/data-explorer/) con il potente linguaggio di query [kusto](/azure/kusto/query/index) .

![Screenshot della finestra di query kusto](./media/workbooks-data-sources/data-explorer.png)

## <a name="workload-health"></a>Stato del carico di lavoro

Monitoraggio di Azure offre funzionalità che monitora in modo proattivo la disponibilità e le prestazioni dei sistemi operativi guest Windows o Linux. Monitoraggio di Azure modella i componenti chiave e le relative relazioni, i criteri per misurare l'integrità di tali componenti e i componenti che inviano un avviso quando viene rilevata una condizione di tipo non integro. Le cartelle di lavoro consentono agli utenti di usare queste informazioni per creare report interattivi avanzati.

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa **tipo di query** per scegliere integrità del carico di lavoro e selezionare la sottoscrizione, il gruppo di risorse o le risorse della macchina virtuale di destinazione. Utilizzare gli elenchi a discesa Filtro integrità per selezionare un subset interessante di eventi imprevisti di integrità per le proprie esigenze analitiche.

![Screenshot della query degli avvisi](./media/workbooks-data-sources/workload-health.png)

## <a name="azure-resource-health"></a>Integrità risorse di Azure

Le cartelle di lavoro supportano l'ottenimento di integrità delle risorse di Azure e la combinazione con altre origini dati per creare report di integrità avanzati e interattivi

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa **tipo di query** per scegliere integrità di Azure e selezionare le risorse di destinazione. Usare gli elenchi a discesa del filtro di integrità per selezionare un subset interessante di problemi di risorse in base alle esigenze analitiche.

![Screenshot della query degli avvisi che Mostra gli elenchi dei filtri di integrità.](./media/workbooks-data-sources/resource-health.png)

## <a name="change-analysis-preview"></a>Analisi delle modifiche (anteprima)

Per eseguire un controllo query usando l' [analisi delle modifiche dell'applicazione](../app/change-analysis.md) come origine dati, usare l'elenco a discesa *origine dati* e scegliere *modifica analisi (anteprima)* e selezionare una singola risorsa. È possibile visualizzare le modifiche fino agli ultimi 14 giorni. È possibile usare l'elenco a discesa *livello* per filtrare tra le modifiche "importanti", "normali" e "rumorose" e questo elenco a discesa supporta i parametri della cartella di lavoro di tipo [elenco a discesa](workbooks-dropdowns.md).

> [!div class="mx-imgBorder"]
> ![Screenshot di una cartella di lavoro con analisi delle modifiche](./media/workbooks-data-sources/change-analysis-data-source.png)

## <a name="merge-data-from-different-sources"></a>Unisci dati da origini diverse

Spesso è necessario riunire i dati da origini diverse che migliorano l'esperienza di Insights. Un esempio è l'aumento delle informazioni di avviso attive con i dati di metrica correlati. Ciò consente agli utenti di vedere non solo l'effetto (un avviso attivo), ma anche le possibili cause, ad esempio un utilizzo elevato della CPU. Il dominio di monitoraggio include numerose origini dati correlate che sono spesso cruciali per il flusso di lavoro di valutazione e diagnostica.

Le cartelle di lavoro di non consentono solo l'esecuzione di query di origini dati diverse, ma forniscono anche semplici controlli che consentono di unire o unire i dati per fornire informazioni dettagliate avanzate. Il `merge` controllo consente di ottenerlo.

Nell'esempio seguente vengono combinati i dati relativi agli avvisi con i dati sulle prestazioni delle macchine virtuali di log Analytics per ottenere una griglia di informazioni dettagliate.

> [!div class="mx-imgBorder"]
> ![Screenshot di una cartella di lavoro con un controllo di merge che combina i dati di avviso e di log Analytics](./media/workbooks-data-sources/merge-control.png)

Le cartelle di lavoro supportano un'ampia gamma di unioni:

* Join univoco interno
* inner join completa
* Full outer join
* Left outer join
* Right outer join
* Left semi join
* Semi join a destra
* Left anti-join
* Anti-join a destra
* Union
* Duplica tabella

## <a name="json"></a>JSON

Il provider JSON consente di creare un risultato di query dal contenuto JSON statico. Viene in genere usato nei parametri per creare parametri a discesa di valori statici. Le matrici o gli oggetti semplici JSON verranno convertiti automaticamente in righe e colonne della griglia.  Per comportamenti più specifici, è possibile usare la scheda risultati e le impostazioni JSONPath per configurare le colonne.

Questo provider supporta [JSONPath](workbooks-jsonpath.md).

## <a name="alerts-preview"></a>Avvisi (anteprima)

> [!NOTE]
> Il modo consigliato per eseguire query per le informazioni sugli avvisi di Azure consiste nell'usare l'origine dati [Graph di risorse di Azure](#azure-resource-graph) , eseguendo una query sulla `AlertsManagementResources` tabella.
>
> Per esempi, vedere il [riferimento alla tabella del grafo delle risorse di Azure](../../governance/resource-graph/reference/supported-tables-resources.md)o il modello di [avviso](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook) .
>
> L'origine dati degli avvisi rimarrà disponibile per un determinato periodo di tempo mentre gli autori passano a utilizzando ARG. L'uso di questa origine dati nei modelli è sconsigliato. 

Le cartelle di lavoro consentono agli utenti di visualizzare gli avvisi attivi correlati alle relative risorse. Limitazioni: l'origine dati degli avvisi richiede l'accesso in lettura alla sottoscrizione per eseguire query sulle risorse e potrebbe non visualizzare tipi più recenti di avvisi. 

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa _origine dati_ per scegliere _avvisi (anteprima)_ e selezionare le sottoscrizioni, i gruppi di risorse o le risorse di destinazione. Usare gli elenchi a discesa del filtro avvisi per selezionare un subset interessante di avvisi per le proprie esigenze analitiche.

## <a name="custom-endpoint"></a>Endpoint personalizzato

Le cartelle di lavoro supportano il recupero di dati da qualsiasi origine esterna. Se i dati si trovano all'esterno di Azure, è possibile portarli nelle cartelle di lavoro usando questo tipo di origine dati.

Per fare in modo che un controllo query usi questa origine dati, usare l'elenco a discesa _origine dati_ per scegliere _endpoint personalizzato_. Fornire i parametri appropriati, ad esempio `Http method` ,, `url` `headers` `url parameters` e/o `body` . Verificare che l'origine dati supporti [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) in caso contrario, la richiesta avrà esito negativo.

Per evitare di effettuare automaticamente chiamate a host non attendibili quando si usano i modelli, l'utente deve contrassegnare gli host usati come attendibili. A tale scopo, è possibile fare clic sul pulsante _Aggiungi come attendibile_ oppure aggiungerlo come host attendibile nelle impostazioni della cartella di lavoro. Queste impostazioni verranno salvate nei [browser che supportano IndexDb con i Web Worker](https://caniuse.com/#feat=indexeddb).

> [!NOTE]
> Non scrivere alcun segreto in nessuno dei campi (,, `headers` `parameters` `body` , `url` ), perché saranno visibili a tutti gli utenti della cartella di lavoro.

Questo provider supporta [JSONPath](workbooks-jsonpath.md).

## <a name="next-steps"></a>Passaggi successivi

* Per [iniziare ad](./workbooks-overview.md#visualizations) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](./workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
* [Suggerimenti per l'ottimizzazione delle query Log Analytics](../logs/query-optimization.md)