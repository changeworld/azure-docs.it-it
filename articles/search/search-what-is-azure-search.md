---
title: Introduzione alla ricerca cognitiva di Azure
titleSuffix: Azure Cognitive Search
description: Ricerca cognitiva di Azure è un servizio di ricerca cloud completamente gestito di Microsoft. Descrizioni dei casi d'uso, flusso di lavoro di sviluppo, confronto con altri prodotti per la ricerca di Microsoft e informazioni per iniziare.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/09/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: e17d08d09814c135af3e0b4fc299b6e6f42326d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549890"
---
# <a name="what-is-azure-cognitive-search"></a>Che cos'è la ricerca cognitiva di Azure?

Ricerca cognitiva di Azure ([precedentemente nota come "Ricerca di Azure"](whats-new.md#new-service-name)) è un servizio di ricerca cloud che offre agli sviluppatori le API e gli strumenti per creare un'esperienza di ricerca avanzata su contenuti eterogenei e privati nelle applicazioni Web, per dispositivi mobili e aziendali. 

La ricerca è fondamentale per qualsiasi app che supporti il contenuto agli utenti, con scenari comuni, tra cui ricerca di cataloghi o documenti, ricerca di siti di e-commerce o Knowledge mining per data science. Le API e l'architettura di ricerca cognitiva semplificano l'operazione di aggiunta di un recupero sofisticato di informazioni a qualsiasi soluzione.

Un servizio di ricerca dispone dei componenti seguenti:

+ Motore di ricerca per la ricerca full-text
+ Archiviazione permanente di contenuti indicizzati di proprietà dell'utente
+ API per l'indicizzazione e l'esecuzione di query sul contenuto
+ [Arricchimenti facoltativi basati su intelligenza artificiale](cognitive-search-concept-intro.md), creazione di contenuto ricercabile da immagini, testo non strutturato non elaborato, file applicazione
+ Integrazione facoltativa con altri servizi di Azure per dati, Machine Learning/intelligenza artificiale, monitoraggio e sicurezza
+ Implementazione facoltativa della [ricerca semantica (anteprima)](semantic-search-overview.md) per una maggiore pertinenza

A un'architettura, un servizio di ricerca si trova tra gli archivi dati esterni che contengono i dati non indicizzati e l'app client che invia richieste di query a un indice di ricerca e gestisce la risposta.

![Architettura di Ricerca cognitiva di Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Architettura di Ricerca cognitiva di Azure")

Esternamente, la ricerca può essere integrata con altri servizi di Azure sotto forma di *indicizzatori* che automatizzano l'inserimento o il recupero di dati da origini dati di Azure e *skillsets* che incorporano l'intelligenza artificiale utilizzabile da servizi cognitivi, ad esempio l'analisi di immagini e testo o l'intelligenza artificiale personalizzata creata in Azure Machine Learning o a capo all'interno di funzioni di Azure.

## <a name="inside-a-search-service"></a>All'interno di un servizio di ricerca

Nel servizio di ricerca stesso i due carichi di lavoro principali sono *indicizzazione* ed *esecuzione di query*. 

+ L' [indicizzazione](search-what-is-an-index.md) è un processo di assunzione che carica il contenuto nel servizio di ricerca e lo rende ricercabile. Internamente, il testo in ingresso viene elaborato in token e archiviato in indici invertiti per consentire analisi rapide. È possibile caricare qualsiasi testo sotto forma di documenti JSON.

  Inoltre, se il contenuto include file misti, è possibile aggiungere l' *arricchimento di intelligenza artificiale* attraverso le [competenze cognitive](cognitive-search-working-with-skillsets.md). L'arricchimento di intelligenza artificiale può estrarre il testo incorporato nei file dell'applicazione e dedurre anche testo e struttura da file non di testo analizzando il contenuto. 

  Le competenze fornite dall'analisi sono quelle predefinite di Microsoft o le competenze personalizzate create dall'utente. L'analisi e le trasformazioni successive possono generare nuove informazioni e strutture che non esistevano in precedenza, risultando così particolarmente utili per molti scenari di ricerca e di data mining.

+ L' [esecuzione di query](search-query-overview.md) può verificarsi quando l'indice viene popolato con testo ricercabile, quando l'app client invia richieste di query a un servizio di ricerca e gestisce le risposte. Tutte le query vengono eseguite su un indice di ricerca creato dall'utente e di sua proprietà che viene archiviato nel servizio. Nell'app client l'esperienza di ricerca viene definita usando le API di Ricerca cognitiva di Azure e può includere l'ottimizzazione della pertinenza, il completamento automatico, la corrispondenza dei sinonimi, la corrispondenza fuzzy, i criteri di ricerca, il filtro e l'ordinamento.

La funzionalità viene esposta tramite una semplice [API REST](/rest/api/searchservice/) o un [SDK .NET](search-howto-dotnet-sdk.md) che maschera la complessità intrinseca del recupero delle informazioni. È anche possibile usare il portale di Azure per l'amministrazione del servizio e la gestione dei contenuti, con strumenti per la creazione di prototipi e per l'esecuzione di query su indici e set di competenze. Poiché il servizio viene eseguito nel cloud, disponibilità e infrastruttura sono gestite da Microsoft.

## <a name="why-use-cognitive-search"></a>Perché usare Ricerca cognitiva

Ricerca cognitiva di Azure è una soluzione particolarmente adatta agli scenari di applicazione seguenti:

+ Consolidamento di contenuti eterogenei in un indice di ricerca privato definito dall'utente.

+ Implementare facilmente funzionalità correlate alla ricerca: ottimizzazione della pertinenza, esplorazione in base a facet, filtri (inclusa la ricerca geospaziale), mapping dei sinonimi e completamento automatico.

+ Trasforma file di testo o immagine non differenziati di grandi dimensioni o file di applicazioni archiviati nell'archivio BLOB di Azure o in Cosmos DB, in documenti JSON in cui è possibile eseguire ricerche. Questa operazione viene eseguita durante l'indicizzazione tramite [competenze cognitive](cognitive-search-concept-intro.md) che aggiungono l'elaborazione esterna.

+ Aggiungere un'analisi del testo linguistica o personalizzata. Per il contenuto non in lingua inglese, Ricerca cognitiva di Azure supporta sia gli analizzatori Lucene che i processori di linguaggio naturale Microsoft. È anche possibile configurare gli analizzatori per ottenere un'elaborazione specializzata di contenuto non elaborato, ad esempio l'esclusione tramite filtro dei segni diacritici o il riconoscimento e la conservazione dei criteri nelle stringhe.

Per altre informazioni sulle funzionalità specifiche, vedere [Funzionalità di Ricerca cognitiva di Azure](search-features-list.md)

## <a name="how-to-get-started"></a>Operazioni preliminari

Per un'esplorazione end-to-end delle principali funzionalità di ricerca, seguire questi quattro passaggi:

1. [**Creare un servizio di ricerca**](search-create-service-portal.md) con il livello gratuito condiviso o un [livello fatturabile](https://azure.microsoft.com/pricing/details/search/) per le risorse dedicate usate solo dal servizio. Tutte le guide introduttive e le esercitazioni possono essere completate in un servizio condiviso.

1. [**Creare un indice di ricerca**](search-what-is-an-index.md) usando il portale, l' [API REST](/rest/api/searchservice/create-index), [.NET SDK](search-howto-dotnet-sdk.md)o un altro SDK. Lo schema di indice definisce la struttura del contenuto ricercabile.

1. [**Caricare il contenuto**](search-what-is-data-import.md) usando il [modello "push"](tutorial-optimize-indexing-push-api.md) per eseguire il push dei documenti JSON da qualsiasi origine oppure usare il [modello di "pull" (indicizzatori)](search-indexer-overview.md) se i dati di origine sono in Azure.

1. [**Eseguire query su un indice**](search-query-overview.md) usando [Esplora ricerche](search-explorer.md) nel portale, l'[API REST](search-get-started-rest.md), [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search) o un altro SDK.

Per l'esplorazione iniziale, iniziare con la [**procedura guidata Importa dati**](search-get-started-portal.md) e un'origine dati di Azure incorporata per creare, caricare ed eseguire query su un indice in pochi minuti.

Per assistenza con soluzioni complesse o personalizzate, [**rivolgersi a un partner**](resource-partners-knowledge-mining.md) con competenze approfondite nella tecnologia ricerca cognitiva.

## <a name="compare-search-options"></a>Confrontare le opzioni di ricerca

Spesso i clienti chiedono quali siano le differenze tra Ricerca cognitiva di Azure e le altre soluzioni di ricerca. Nella tabella seguente sono riepilogate le principali differenze.

| Confronto con | Differenze principali |
|-------------|-----------------|
| Microsoft Search | [Microsoft Search](/microsoftsearch/overview-microsoft-search) è destinato agli utenti autenticati di Microsoft 365 che devono eseguire query sui contenuti in SharePoint. Viene offerto come esperienza di ricerca pronta per l'uso, abilitata e configurata da amministratori, con la possibilità di accettare contenuto esterno tramite connettori Microsoft e di altre origini. Se questa descrizione corrisponde al proprio scenario, Microsoft Search con Microsoft 365 è un'opzione interessante da esplorare.<br/><br/>Ricerca cognitiva di Azure, invece, esegue le query su un indice definito dall'utente, popolato con dati e documenti di cui l'utente è proprietario e spesso aventi origini diverse. Ricerca cognitiva di Azure offre funzionalità di ricerca per indicizzazione per alcune origini dati di Azure tramite gli [indicizzatori](search-indexer-overview.md), ma è possibile eseguire il push di qualsiasi documento JSON conforme allo schema di indicizzazione in un'unica risorsa ricercabile consolidata. È anche possibile personalizzare la pipeline di indicizzazione per includere funzionalità di Machine Learning e analizzatori lessicali. Poiché Ricerca cognitiva è progettato come componente plug-in in soluzioni di dimensioni maggiori, è possibile integrare la ricerca in quasi tutte le app, su qualsiasi piattaforma.|
|Bing | [API Ricerca Web Bing](../cognitive-services/bing-web-search/index.yml) ricerca i termini corrispondenti inviati negli indici in Bing.com. Gli indici sono generati da contenuti Web HTML, XML e di altro tipo nei siti pubblici. Con la medesima base, [Ricerca personalizzata Bing ](/azure/cognitive-services/bing-custom-search/) offre la stessa tecnologia di ricerca Web per tipi di contenuto Web aventi come ambito siti Web specifici.<br/><br/>In Ricerca cognitiva è possibile definire e popolare l'indice. È possibile usare gli [indicizzatori](search-indexer-overview.md) per eseguire la ricerca per indicizzazione dei dati in origini dati di Azure o eseguire il push di qualsiasi documento JSON conforme all'indice nel servizio di ricerca. |
|Ricerca nel database | Molte piattaforme di database includono un'esperienza di ricerca predefinita. SQL Server include la [ricerca full-text](/sql/relational-databases/search/full-text-search). Cosmos DB e tecnologie simili usano indici disponibili per query. Durante la valutazione di prodotti che combinano ricerca e archiviazione, può essere difficile operare una scelta. Molte soluzioni usano entrambe le tecnologie, ovvero un sistema DBMS per l'archiviazione e Ricerca cognitiva di Azure per le funzionalità di ricerca specializzate.<br/><br/>Rispetto alla ricerca DBMS, Ricerca cognitiva di Azure archivia il contenuto da origini eterogenee e offre funzionalità di elaborazione del testo specialistiche, come l'elaborazione del testo con riconoscimento linguistico (stemming, lemmatizzazione, forme delle parole) in [56 lingue](/rest/api/searchservice/language-support). Supporta anche la correzione automatica di parole con errori di digitazione, [sinonimi](/rest/api/searchservice/synonym-map-operations), [suggerimenti](/rest/api/searchservice/suggestions), [controlli per il punteggio](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facet](./search-filters-facets.md)e [tokenizzazione personalizzata](/rest/api/searchservice/custom-analyzers-in-azure-search). Il [motore di ricerca full-text](search-lucene-query-architecture.md) di Ricerca cognitiva di Azure è basato su Apache Lucene, uno standard del settore per il recupero delle informazioni. Tuttavia, anche se Ricerca cognitiva di Azure salva in modo permanente i dati sotto forma di indice invertito, non costituisce un'alternativa a una vera soluzione di archiviazione dei dati e non è consigliabile usarla a tale scopo. Per altre informazioni, vedere questo [post di forum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>L'utilizzo delle risorse è un altro punto da considerare in questo ambito. L'indicizzazione e alcune operazioni di query sono spesso onerose dal punto di vista dei calcoli. L'offload della ricerca dal sistema DBMS a una soluzione dedicata nel cloud consente di risparmiare risorse da destinare all'elaborazione delle transazioni. Con l'esternalizzazione della ricerca, inoltre, è possibile modificare facilmente la scala in modo che corrisponda al volume di query.|
|Soluzione di ricerca dedicata | Presupponendo di aver deciso per la ricerca dedicata con funzionalità complete, è opportuno un confronto finale tra soluzioni locali o servizio cloud. Molte tecnologie di ricerca consentono di controllare le pipeline di query e indicizzazione, offrono l'accesso a sintassi di query e filtro più complete, controllano la priorità e la rilevanza e sono caratterizzate da funzionalità di ricerca intelligente e mirata. <br/><br/>Un servizio cloud è la scelta giusta se si vuole una soluzione chiavi in mano con sovraccarico e manutenzione minimi e scala regolabile. <br/><br/>All'interno del paradigma di cloud, molti provider offrono funzionalità di base simili, con ricerca full-text, ricerca geografica e la possibilità di gestire un certo livello di ambiguità negli input di ricerca. In genere sono una [funzionalità specializzata](search-features-list.md) o la semplicità e la facilità d'uso generale di API, strumenti e gestione a determinare la scelta ideale. |

Tra i provider di servizi cloud, Ricerca cognitiva di Azure offre le migliori prestazioni per i carichi di lavoro con ricerca full-text in archivi di contenuti e database in Azure, per le app che fanno principalmente affidamento sulla ricerca per il recupero delle informazioni e l'esplorazione dei contenuti. 

Ecco i principali vantaggi:

+ Integrazione dei dati in Azure (crawler) al livello di indicizzazione
+ Integrazione con il servizio Collegamento privato di Azure per supportare i requisiti di sicurezza al di fuori di Internet
+ Integrazione con l'elaborazione tramite intelligenza artificiale per rendere ricercabile il testo di tipi di contenuto non ricercabili.
+ Analisi linguistica e personalizzata, con analizzatori per una robusta ricerca full-text in 56 lingue
+ [Funzionalità critiche](search-features-list.md): linguaggio di query avanzato, ottimizzazione della pertinenza, facet, completamento automatico, sinonimi, ricerca geografica e composizione dei risultati.
+ Scalabilità, affidabilità e massima disponibilità proprie di Azure

Tra i clienti, i casi in grado di sfruttare la gamma più ampia di funzionalità di Ricerca cognitiva di Azure includono cataloghi online, programmi line-of-business e applicazioni di individuazione di documenti.

## <a name="watch-this-video"></a>Guardare questo video

In questo video di 15 minuti, il Program Manager Luis Cabrera presenta Ricerca cognitiva di Azure.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]