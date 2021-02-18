---
title: Offerta senza server basata sul consumo in Azure Cosmos DB
description: Scopri di più sull'offerta senza server basata sul consumo di Azure Cosmos DB.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3ee8d5f36977a5a9f20c7e636118ffa9f6ee0b6d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570992"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB senza server (anteprima)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB senza server consente di usare l'account Azure Cosmos in modo basato sul consumo, in cui vengono addebitate solo le unità di richiesta utilizzate dalle operazioni del database e la risorsa di archiviazione utilizzata dai dati. I contenitori senza server possono gestire migliaia di richieste al secondo senza alcun addebito minimo e non è necessaria alcuna pianificazione della capacità.

> [!IMPORTANT] 
> Ci sono commenti e suggerimenti su senza server? Vogliamo sentirci! È possibile rilasciare un messaggio al team Azure Cosmos DB senza server: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

Quando si utilizza Azure Cosmos DB, tutte le operazioni di database hanno un costo espresso in [unità richiesta](request-units.md). Il modo in cui viene addebitato questo costo dipende dal tipo di account Azure Cosmos usato:

- In modalità di [velocità effettiva con provisioning](set-throughput.md) , è necessario eseguire il commit di una determinata quantità di velocità effettiva (espressa in unità richiesta al secondo) di cui viene eseguito il provisioning nei database e nei contenitori. Il costo delle operazioni del database viene quindi detratto dal numero di unità richiesta disponibili ogni secondo. Alla fine del periodo di fatturazione, viene addebitata la quantità di velocità effettiva di cui è stato effettuato il provisioning.
- In modalità senza server non è necessario effettuare il provisioning di una velocità effettiva durante la creazione di contenitori nell'account Azure Cosmos. Alla fine del periodo di fatturazione, viene addebitato il numero di unità richiesta utilizzate dalle operazioni del database.

## <a name="use-cases"></a>Casi d'uso

Azure Cosmos DB gli scenari più adatti per i server in cui si prevede un **traffico intermittente e non prevedibile** con tempi di inattività prolungati. Poiché la capacità di provisioning in tali situazioni non è necessaria e può essere dispendiosa in termini di costi, Azure Cosmos DB senza server deve essere considerata nei casi d'uso seguenti:

- Introduzione a Azure Cosmos DB
- Esecuzione di applicazioni con
    - un traffico intermittente molto difficile da prevedere o
    - bassa (<10%) rapporto di traffico medio-picco
- Sviluppo, test, creazione di prototipi e esecuzione in applicazioni di produzione nuove in cui il modello di traffico è sconosciuto
- Integrazione con servizi di calcolo senza server come [funzioni di Azure](../azure-functions/functions-overview.md)

Per altre indicazioni su come scegliere l'offerta più adatta ai casi d'uso, vedere l'articolo [come scegliere tra velocità effettiva con provisioning e senza server](throughput-serverless.md) .

## <a name="using-serverless-resources"></a>Uso di risorse senza server

Senza server è un nuovo tipo di account Azure Cosmos, il che significa che è necessario scegliere tra la **velocità effettiva con provisioning** e senza **Server** quando si crea un nuovo account. Per iniziare a usare senza server, è necessario creare un nuovo account senza server. Durante la versione di anteprima, l'unico modo supportato per creare un nuovo account senza server consiste nell' [usare la portale di Azure](create-cosmosdb-resources-portal.md). La migrazione degli account esistenti da e verso la modalità senza server non è attualmente supportata.

Qualsiasi contenitore creato in un account senza server è un contenitore senza server. I contenitori senza server espongono le stesse funzionalità dei contenitori creati in modalità di velocità effettiva con provisioning, in modo da leggere, scrivere ed eseguire query sui dati esattamente allo stesso modo. Tuttavia, anche gli account senza server e i contenitori hanno caratteristiche specifiche:

> [!IMPORTANT]
> Alcune di queste limitazioni possono essere attenuate o rimosse quando il server diventa disponibile a livello generale e **i commenti e suggerimenti** ci aiuteranno a decidere. Scopri di più sull'esperienza senza server: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- Un account senza server può essere eseguito solo in una singola area di Azure. Non è possibile aggiungere altre aree di Azure a un account senza server dopo averlo creato.
- Non è possibile abilitare la funzionalità di [anteprima del collegamento sinapsi](synapse-link.md) in un account senza server.
- La velocità effettiva del provisioning non è necessaria per i contenitori senza server, pertanto sono applicabili le seguenti istruzioni:
    - Non è possibile passare una velocità effettiva quando si crea un contenitore senza server e in questo modo viene restituito un errore.
    - Non è possibile leggere o aggiornare la velocità effettiva in un contenitore senza server e in questo modo viene restituito un errore.
    - Non è possibile creare un database di velocità effettiva condivisa in un account senza server e in questo modo viene restituito un errore.
- I contenitori senza server possono archiviare un massimo di 50 GB di dati e indici.

## <a name="monitoring-your-consumption"></a>Monitoraggio del consumo

Se in precedenza è stata Azure Cosmos DB usata la modalità di velocità effettiva con provisioning, si noterà che il server non è più conveniente quando il traffico non giustifica la capacità con provisioning. Il compromesso consiste nel fatto che i costi diventeranno meno prevedibili perché vengono addebitati in base al numero di richieste elaborate dal database. Per questo motivo, è importante tenere sotto controllo il consumo corrente.

Quando si Esplora il riquadro **metriche** dell'account, si troverà un grafico denominato **unità richiesta utilizzate** nella scheda **Panoramica** . Questo grafico mostra il numero di unità richiesta utilizzate dall'account:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Grafico che mostra le unità richiesta utilizzate" border="false":::

È possibile trovare lo stesso grafico quando si usa monitoraggio di Azure, come descritto [qui](monitor-request-unit-usage.md). Si noti che monitoraggio di Azure consente di configurare gli [avvisi](../azure-monitor/alerts/alerts-metric-overview.md), che possono essere usati per notificare quando il consumo delle unità richiesta ha superato una determinata soglia.

## <a name="performance"></a><a id="performance"></a>Prestazioni

Le risorse senza server producono caratteristiche specifiche delle prestazioni diverse da quelle offerte dalle risorse della velocità effettiva con provisioning. Una volta che l'offerta senza server diventa disponibile a livello generale, la latenza dei contenitori senza server sarà coperta da un obiettivo del livello di servizio (SLO) di 10 millisecondi o meno per le scritture di punti e 30 millisecondi o meno per le Scritture. Un'operazione di lettura di punti consiste nel recupero di un singolo elemento in base al relativo ID e valore della chiave di partizione.

## <a name="next-steps"></a>Passaggi successivi

Introduzione a senza server con gli articoli seguenti:

- [Unità richiesta in Azure Cosmos DB](request-units.md)
- [Scegliere tra le modalità Provisioning velocità effettiva e Serverless](throughput-serverless.md)
- [Modello di determinazione dei prezzi di Azure Cosmos DB](how-pricing-works.md)
