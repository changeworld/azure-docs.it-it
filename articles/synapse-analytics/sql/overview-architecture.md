---
title: Architettura di SQL Synapse
description: Informazioni su Azure Synapse SQL combina le funzionalità di elaborazione delle query distribuite con Archiviazione di Azure per ottenere prestazioni e scalabilità elevate.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f342f39b62956cd85f269918e8e1ef1a2478a3d8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566359"
---
# <a name="azure-synapse-sql-architecture"></a>Architettura di Azure Synapse SQL 

Questo articolo descrive i componenti dell'architettura di Synapse SQL.

## <a name="synapse-sql-architecture-components"></a>Componenti dell'architettura di Synapse SQL

Synapse SQL usa un'architettura ridimensionabile per distribuire l'elaborazione computazionale dei dati su più nodi. Poiché le risorse di calcolo sono separate dall'archiviazione, è possibile ridimensionarle indipendentemente dai dati presenti nel sistema. 

Per il pool SQL dedicato, l'unità di scala è un'astrazione della potenza di calcolo nota come unità data warehouse [di calcolo](resource-consumption-models.md). 

Per il pool SQL senza server, essendo serverless, il ridimensionamento viene eseguito automaticamente per soddisfare i requisiti delle risorse di query. Con il progressivo cambiamento della topologia a seguito dell'aggiunta e della rimozione di nodi o failover, il ridimensionamento viene adattato in base alle modifiche per garantire che la query disponga di risorse sufficienti e venga completata correttamente. Ad esempio, l'immagine seguente mostra un pool SQL serverless che usa 4 nodi di calcolo per eseguire una query.

![Architettura di SQL Synapse](./media//overview-architecture/sql-architecture.png)

Synapse SQL usa un'architettura basata su nodi. L'applicazione si connette ed esegue comandi T-SQL in un nodo di controllo che rappresenta l'unico punto di ingresso per Synapse SQL. 

Il Azure Synapse controllo SQL usa un motore di query distribuite per ottimizzare le query per l'elaborazione parallela e quindi passa le operazioni ai nodi di calcolo per eseguire il lavoro in parallelo. 

Il nodo di controllo del pool SQL serverless usa il motore DQP (Distributed Query Processing) per ottimizzare e orchestrare l'esecuzione distribuita delle query utente suddividendolo in query più piccole che verranno eseguite nei nodi di calcolo. Ognuna delle query più piccole è definita attività e rappresenta un'unità di esecuzione distribuita. Legge i file dall'archiviazione, unisce i risultati di altre attività, gruppi o ordini recuperati da altre attività. 

I nodi di calcolo archiviano tutti i dati utente in Archiviazione di Azure ed eseguono le query parallele. Data Movement Service (DMS) è un servizio interno a livello di sistema che sposta i dati tra i nodi per eseguire query in parallelo e restituire risultati accurati. 

Grazie alla separazione delle risorse di archiviazione dalle risorse di calcolo, l'uso di Synapse SQL offre la vantaggiosa possibilità di ridimensionare la potenza di calcolo indipendentemente, senza dover tener conto delle esigenze di archiviazione. Per il ridimensionamento del pool SQL serverless viene eseguito automaticamente, mentre per il pool SQL dedicato è possibile:

* Aumentare o ridurre la potenza di calcolo, all'interno di un pool SQL dedicato, senza spostare i dati.
* Sospendere la capacità di calcolo mantenendo intatti i dati e consentendo di pagare solo per l'archiviazione.
* Riprendere le capacità di calcolo durante l'orario operativo.

## <a name="azure-storage"></a>Archiviazione di Azure

Synapse SQL usa Archiviazione di Azure per proteggere i dati dell'utente. Poiché i dati vengono archiviati e gestiti da Archiviazione di Azure, è previsto un addebito separato per l'uso dello spazio di archiviazione. 

Il pool SQL serverless consente di eseguire query sui file data lake, mentre il pool SQL dedicato consente di eseguire query e inserire dati dai data lake file. Quando i dati vengono inseriti in un pool SQL  dedicato, i dati vengono partizionati in distribuzioni per ottimizzare le prestazioni del sistema. È possibile scegliere il modello di partizionamento orizzontale da usare per distribuire i dati quando si definisce la tabella. Sono supportati i modelli di partizionamento seguenti:

* Hash
* Round robin
* Replica

## <a name="control-node"></a>Nodo di controllo

Il nodo di controllo costituisce il componente principale dell'architettura. È il front-end che interagisce con tutte le applicazioni e le connessioni. 

In Synapse SQL, il motore di query distribuite viene eseguito nel nodo Controllo per ottimizzare e coordinare le query parallele. Quando si invia una query T-SQL a un pool SQL dedicato, il nodo Di controllo la trasforma in query eseguite su ogni distribuzione in parallelo.

Nel pool SQL serverless il motore DQP viene eseguito nel nodo di controllo per ottimizzare e coordinare l'esecuzione distribuita della query utente suddividendolo in query più piccole che verranno eseguite nei nodi di calcolo. Il motore assegna anche set di file che devono essere elaborati in ogni nodo.

## <a name="compute-nodes"></a>Nodi di calcolo

I nodi di calcolo forniscono la potenza di calcolo. 

Nel pool SQL dedicato le distribuzioni vengono mappate ai nodi di calcolo per l'elaborazione. Quando si ottengono risorse di calcolo aggiuntive a pagamento, il pool esegue di nuovo il mapping delle distribuzioni nei nodi di calcolo disponibili. Il numero di nodi di calcolo è compreso tra 1 e 60 ed è determinato dal livello di servizio per il pool SQL dedicato. Ogni nodo di calcolo ha un ID visibile nelle visualizzazioni di sistema. È possibile individuare l'ID del nodo di calcolo cercando la colonna node_id nelle visualizzazioni di sistema il cui nome inizia con sys.pdw_nodes. Per un elenco di queste viste di sistema, vedere Synapse SQL [viste di sistema](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest&preserve-view=true).

Nel pool SQL serverless a ogni nodo di calcolo vengono assegnati l'attività e il set di file in cui eseguire l'attività. L'attività corrisponde a un'unità di esecuzione della query distribuita, che costituisce una parte effettiva della query utente inviata. Il ridimensionamento automatico è attivato per verificare che venga usato un numero di nodi di calcolo sufficiente per eseguire la query utente.

## <a name="data-movement-service"></a>Data Movement Service

Data Movement Service (DMS) è la tecnologia di trasporto dei dati in un pool SQL dedicato che coordina lo spostamento dei dati tra i nodi di calcolo. Alcune query richiedono lo spostamento dei dati per garantire che le query parallele restituiscano risultati accurati. Quando lo spostamento dei dati è necessario, DMS assicura che i dati corretti vengano spostati nel percorso corretto.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distribuzioni

Una distribuzione è l'unità di base di archiviazione ed elaborazione per le query parallele eseguite su dati distribuiti in un pool SQL dedicato. Quando un pool SQL dedicato esegue una query, il lavoro viene diviso in 60 query più piccole che vengono eseguite in parallelo. 

Ognuna delle 60 query viene eseguita in una distribuzione dei dati. Ogni nodo di calcolo gestisce una o più delle 60 distribuzioni. Un pool SQL dedicato con il numero massimo di risorse di calcolo ha una distribuzione per ogni nodo di calcolo. Un pool SQL dedicato con risorse di calcolo minime include tutte le distribuzioni in un nodo di calcolo. 

## <a name="hash-distributed-tables"></a>Tabelle con distribuzione hash
Una tabella con distribuzione hash offre le prestazioni di query più elevate per join e aggregazioni in tabelle di grandi dimensioni. 

Per partizionare i dati in una tabella con distribuzione hash, il pool SQL dedicato usa una funzione hash per assegnare in modo deterministico ogni riga a una distribuzione. Nella definizione della tabella una delle colonne è definita come colonna di distribuzione. La funzione hash usa i valori della colonna di distribuzione per assegnare ogni riga a una distribuzione.

La figura seguente illustra come una tabella completa non distribuita viene archiviata come tabella con distribuzione hash. 

![Tabella distribuita](media//overview-architecture/hash-distributed-table.png "Tabella distribuita") 

* Ogni riga appartiene a una sola distribuzione. 
* Un algoritmo hash deterministico assegna ogni riga a una sola distribuzione. 
* Il numero di righe di tabella per distribuzione varia come mostrano le diverse dimensioni delle tabelle.

Ai fini delle prestazioni, per la selezione di una colonna di distribuzione è necessario considerare alcuni aspetti, ad esempio la specificità, la differenza dei dati e i tipi di query eseguite nel sistema.

## <a name="round-robin-distributed-tables"></a>Tabelle con distribuzione round robin

La tabella round robin è la tabella più semplice da creare e offre ottime prestazioni quando viene usata come tabella di staging per i caricamenti.

Una tabella con distribuzione round robin distribuisce i dati in modo uniforme all'interno della tabella senza alcuna ottimizzazione aggiuntiva. Viene innanzitutto selezionata casualmente una distribuzione, quindi i buffer di righe vengono assegnati in sequenza alle distribuzioni. Sebbene il caricamento dei dati in una tabella round robin risulti rapido, è spesso possibile ottenere prestazioni di query migliori con le tabelle con distribuzione hash. Per i join nelle tabelle round robin è necessaria una ridistribuzione dei dati, che richiede più tempo.

## <a name="replicated-tables"></a>Tabelle replicate
Una tabella replicata offre le migliori prestazioni di query per le tabelle di piccole dimensioni.

Una tabella replicata memorizza nella cache una copia completa della tabella di ogni nodo di calcolo. La replica di una tabella elimina quindi la necessità di trasferire i dati tra i nodi di calcolo prima di un join o di un'aggregazione. Le tabelle replicate sono particolarmente adatte all'uso con tabelle di piccole dimensioni. È necessario più spazio di archiviazione e si verifica un sovraccarico aggiuntivo durante la scrittura dei dati, che impedisce l'uso di tabelle di grandi dimensioni. 

Il diagramma seguente mostra una tabella replicata memorizzata nella cache durante la prima distribuzione in ogni nodo di calcolo. 

![Tabella replicata](media/overview-architecture/replicated-table.png "Tabella replicata") 

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono le Synapse SQL, vedere come creare rapidamente un [pool SQL](../quickstart-create-sql-pool-portal.md) dedicato e caricare dati di esempio (./sql-data-warehouse-load-sample-databases.md). [](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) Oppure iniziare [a usare il pool SQL serverless](../quickstart-sql-on-demand.md). Se non si ha familiarità con Azure, il [glossario di Azure](../../azure-glossary-cloud-terminology.md) può essere utile quando si incontrano termini nuovi. 
