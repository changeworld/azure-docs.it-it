---
title: Novità di Ricerca cognitiva di Azure
description: Annunci di funzionalità nuove e migliorate, tra cui il servizio Ricerca di Azure rinominato in Ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/12/2021
ms.custom: references_regions
ms.openlocfilehash: b9ae576a2d0ab8aee00be5b755c348806b412bef
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108490"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novità di Ricerca cognitiva di Azure

Ecco cosa c'è di nuovo nel servizio. Aggiungere un segnalibro a questa pagina per rimanere sempre aggiornati sul servizio. Vedere l' [elenco delle funzionalità di anteprima](search-api-preview.md) per visualizzare un elenco completo delle funzionalità che non sono ancora disponibili a livello generale.

## <a name="march-2021"></a>Marzo 2021

|Funzionalità&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Descrizione | Disponibilità  |
|------------------------------|---------------|---------------|
| [Ricerca semantica](semantic-search-overview.md) | Raccolta di funzionalità correlate a query che consentono di migliorare significativamente la pertinenza dei risultati della ricerca tramite modifiche minime a una richiesta di query. </br></br>La [classificazione semantica](semantic-ranking.md) calcola i punteggi di pertinenza usando il significato semantico alla base delle parole e del contenuto. </br></br>Le [didascalie semantiche](semantic-how-to-query-request.md) restituiscono i passaggi pertinenti dal documento che meglio riepilogano il documento, con evidenziazioni sui termini o le frasi più importanti. </br></br>Le [risposte semantiche](semantic-answers.md) restituiscono passaggi chiave, estratti da un documento di ricerca, formulati come risposta diretta a una query simile a una domanda. | Anteprima pubblica ([per richiesta](https://aka.ms/SemanticSearchPreviewSignup)). </br></br>Usare [Cerca documenti (REST)](/rest/api/searchservice/preview-api/search-documents) API-Version = 2020-06 -30-Preview o [esplora ricerche](search-explorer.md) in portale di Azure. </br></br>Si applicano le restrizioni relative a aree e livelli. |
| [Termini di query del controllo ortografico](speller-how-to-add.md) | Prima che i termini di query raggiungano il motore di ricerca, è possibile verificare la presenza di errori di ortografia. L' `speller` opzione funziona con qualsiasi tipo di query (semplice, completo o semantico). |  Anteprima pubblica, solo REST, API-Version = 2020-06 -30-Preview|
| [Indicizzatore di SharePoint Online](search-howto-index-sharepoint-online.md) | Questo indicizzatore si connette a un sito di SharePoint online in modo che sia possibile indicizzare il contenuto da una raccolta documenti. | Anteprima pubblica, solo REST, API-Version = 2020-06 -30-Preview |
| [Normalizzatori](search-normalizers.md) | I normalizzatori forniscono la pre-elaborazione del testo semplice come la combinazione di maiuscole e minuscole, la rimozione degli accenti, asciifolding e così via, senza dover eseguire l'intera catena| Anteprima pubblica, solo REST, API-Version = 2020-06 -30-Preview |
[**Abilità ricerca entità personalizzata**](cognitive-search-skill-custom-entity-lookup.md ) |  Abilità cognitiva che cerca il testo da un elenco personalizzato di parole e frasi definito dall'utente. Con questo elenco vengono etichettati tutti i documenti con entità corrispondenti. La competenza supporta anche un grado di corrispondenza fuzzy che può essere applicato per trovare corrispondenze simili ma non proprio esatte. | Disponibile a livello generale. |
|

## <a name="february-2021"></a>2021 febbraio

|Funzionalità&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Descrizione | Disponibilità  |
|------------------------------|---------------|---------------|
| [Reimposta documenti (anteprima)](search-howto-run-reset-indexers.md) |  Rielabora i documenti di ricerca selezionati singolarmente nei carichi di lavoro dell'indicizzatore. | [API REST di ricerca 2020-06-30-Preview](/rest/api/searchservice/index-preview) |
| [Zone di disponibilità](search-performance-optimization.md#availability-zones)| I servizi di ricerca con due o più repliche in determinate aree, come elencato in [scalabilità per le prestazioni](search-performance-optimization.md#availability-zones), ottengono la resilienza con repliche in due o più percorsi fisici distinti.  | L'area e la data della creazione del servizio di ricerca determinano la disponibilità. Per informazioni dettagliate, vedere l'articolo relativo alla scalabilità per le prestazioni. |
| [Interfaccia della riga di comando di Azure](/cli/azure/search) </br>[Azure PowerShell](/powershell/module/az.search/) | Le nuove revisioni forniscono ora la gamma completa di operazioni nell'API REST di gestione 2020-08-01, incluso il supporto per le regole del firewall IP e l'endpoint privato. | Disponibile a livello generale. |

## <a name="january-2021"></a>Gennaio 2021

|Funzionalità&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Descrizione | Disponibilità  |
|------------------------------|-------------|---------------|
| [Solution Accelerator per Azure ricerca cognitiva e QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Estrae domande e risposte dal documento e suggerisce le risposte più rilevanti. Un'app demo live si trova in [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) .  | Progetto open source (senza SLA) |

## <a name="2020-archive"></a>Archivio di 2020

| Month | Funzionalità | Descrizione |
|-------|---------|-------------|
| Novembre | [Crittografia della chiave gestita dal cliente (estesa)](search-security-manage-encryption-keys.md) | Estende la crittografia gestita dal cliente sull'intera gamma di asset creati e gestiti da un servizio di ricerca. Disponibile a livello generale.|
| Settembre | [Estensione Visual Studio Code per Azure ricerca cognitiva](search-get-started-vs-code.md) | Aggiunge un'area di lavoro, la navigazione, IntelliSense e i modelli per la creazione di indici, indicizzatori, origini dati e skillsets. Questa funzionalità è attualmente in anteprima pubblica.| 
| Settembre | [Identità del servizio gestito (indicizzatori)](search-howto-managed-identities-data-sources.md) | Disponibile a livello generale.  |
| Settembre | [Richieste in uscita tramite un collegamento privato](search-indexer-howto-access-private.md) | Disponibile a livello generale.  |
| Settembre | [API REST di gestione (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | Disponibile a livello generale. |
| Settembre | [API REST di gestione (2020-08-01-Preview)](/rest/api/searchmanagement/management-api-versions) | Aggiunge una risorsa collegamento privato condivisa per Funzioni di Azure e Azure SQL per database MySQL. |
| Settembre | [.NET SDK 4.0 per la gestione](/dotnet/api/overview/azure/search/management) |  Aggiornamento di Azure SDK per l'SDK di gestione, destinato all'API REST versione 2020-08-01. Disponibile a livello generale.|
| Agosto | [Crittografia doppia](search-security-overview.md#encryption) | Disponibile a livello generale in tutti i servizi di ricerca creati dopo il 1 ° agosto 2020 in queste aree: Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti centro-meridionali, US Gov Virginia US Gov Arizona. |
| Luglio | [Libreria client Azure.Search.Documents](/dotnet/api/overview/azure/search.documents-readme) | Azure SDK per .NET, disponibile a livello generale. |
| Luglio | [Libreria client azure.search.documents](/python/api/overview/azure/search-documents-readme)  | Azure SDK per Python, disponibile a livello generale. |
| Luglio | [@azure/search-documents libreria client](/javascript/api/overview/azure/search-documents-readme)  | Azure SDK per JavaScript, disponibile a livello generale. |
| Giugno | [Knowledge store](knowledge-store-concept-intro.md) | Disponibile a livello generale. |
| Giugno | [API REST di ricerca 2020-06-30](/rest/api/searchservice/) | Disponibile a livello generale. |
| Giugno | [API REST di ricerca 2020-06-30-Preview](/rest/api/searchservice/) | Aggiunge la funzionalità Reimposta competenze per rielaborare selettivamente le competenze e l'arricchimento incrementale. |
| Giugno | [Algoritmo di pertinenza Okapi BM25](index-ranking-similarity.md) | Disponibile a livello generale. |
| Giugno |  **executionEnvironment** (si applica ai servizi di ricerca usando il collegamento privato di Azure). | Disponibile a livello generale. |
| Giugno | [Abilità AML (anteprima)](cognitive-search-aml-skill.md) | Una competenza cognitiva che estende l'arricchimento di intelligenza artificiale con un modello di Azure Machine Learning personalizzato (AML). |
| Mag | [Sessioni di debug (anteprima)](cognitive-search-debug-session.md) | Debugger delle competenze nel portale.  |
| Mag | [Regole IP per il supporto del firewall in ingresso](service-configure-firewall.md) | Disponibile a livello generale.  |
| Mag | [Collegamento privato di Azure per un endpoint di ricerca privato](service-create-private-endpoint.md) | Disponibile a livello generale.  |
| Mag | [Identità del servizio gestito (indicizzatori)-(anteprima)](search-howto-managed-identities-data-sources.md) | Connettersi alle origini dati di Azure usando un'identità gestita.  |
| Mag | [Parametro di query sessionId](index-similarity-and-scoring.md), [scoringStatistics=parametro globale](index-similarity-and-scoring.md#scoring-statistics)  | Statistiche di ricerca globali, utili per i [modelli di Machine Learning (LearnToRank) per la rilevanza della ricerca](https://github.com/Azure-Samples/search-ranking-tutorial).  |
| Mag | [espansione del Punteggio di pertinenza featuresMode (anteprima)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|Marzo  | [Eliminazione temporanea BLOB nativa (anteprima)](search-howto-index-changed-deleted-blobs.md) | Elimina i documenti di ricerca se il BLOB di origine viene eliminato temporaneamente nell'archivio BLOB. |
|Marzo  | [API REST di gestione (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | Disponibile a livello generale. |
|Febbraio | [Abilità rilevamento informazioni personali (anteprima)](cognitive-search-skill-pii-detection.md)  | Una competenza cognitiva che estrae e maschera le informazioni personali. |
|Febbraio | [Abilità ricerca entità personalizzata (anteprima)](cognitive-search-skill-custom-entity-lookup.md) | Abilità cognitiva che trova parole e frasi da un elenco ed etichetta tutti i documenti con entità corrispondenti.  |
|January | [Crittografia della chiave gestita dal cliente](search-security-manage-encryption-keys.md) | Disponibile a livello generale  |
|January | [Regole IP per il supporto del firewall in ingresso (anteprima)](service-configure-firewall.md) | Nuove proprietà **IpRule** e **NetworkRuleSet** nell' [API CreateOrUpdate](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service).  |
|January | [Creare un endpoint privato (anteprima)](service-create-private-endpoint.md) | Configurare un collegamento privato per proteggere le connessioni al servizio di ricerca. Questa funzionalità di anteprima presenta una dipendenza da [collegamento privato di Azure](../private-link/private-link-overview.md) e [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) come parte della soluzione. |

## <a name="2019-archive"></a>Archivio di 2019

| Month | Funzionalità | Descrizione |
|-------|---------|-------------|
|Dicembre | [Crea app demo (anteprima)](search-create-app-portal.md) | Procedura guidata che genera un file HTML scaricabile con accesso di query (di sola lettura) a un indice, progettato come strumento di convalida e di test anziché come un breve taglio a un'app client completa.|
|Novembre | [Arricchimento incrementale (anteprima)](cognitive-search-incremental-indexing-conceptual.md) | Memorizza nella cache l'elaborazione delle competenze per il riutilizzo futuro.  |
|Novembre | [Competenze per l'estrazione di documenti (anteprima)](cognitive-search-skill-document-extraction.md) | Competenze cognitive per estrarre il contenuto di un file dall'interno di un esperto.|
|Novembre | [Traduzione del testo skill](cognitive-search-skill-text-translation.md) | Abilità cognitiva utilizzata durante l'indicizzazione che valuta e converte il testo. Disponibile a livello generale.|
|Novembre | [Modelli di Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Modello per la visualizzazione del contenuto nell'archivio informazioni |
|Novembre | [Azure Data Lake storage Gen2 (anteprima)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB API Gremlin (anteprima)](search-howto-index-cosmosdb.md)e [Cosmos DB API Cassandra (anteprima)](search-howto-index-cosmosdb.md) | Nuove origini dati dell'indicizzatore in anteprima pubblica. |
|Luglio | [Supporto cloud di Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | Disponibile a livello generale.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nuovo nome del servizio

Ricerca di Azure è stato rinominato in **azure ricerca cognitiva** nel 2019 ottobre per riflettere l'uso espanso (ancora facoltativo) di competenze cognitive e l'elaborazione di intelligenza artificiale nelle operazioni principali. Le versioni delle API, i pacchetti NuGet, gli spazi dei nomi e gli endpoint sono rimasti invariati. Le soluzioni di ricerca nuove ed esistenti non sono interessate dalla modifica del nome del servizio.

## <a name="service-updates"></a>Aggiornamenti del servizio

Gli [annunci sugli aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=search&status=all) per Ricerca cognitiva di Azure sono reperibili nel sito Web di Azure.