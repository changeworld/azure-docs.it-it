---
title: Eseguire o reimpostare gli indicizzatori
titleSuffix: Azure Cognitive Search
description: Reimpostare un indicizzatore, le competenze o singoli documenti per l'aggiornamento di tutti gli archivi o di una parte di essi o dell'archivio informazioni.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: bf8a4e51e23f438265af706914a6bc73ec30f64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667661"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>Come eseguire o reimpostare gli indicizzatori, le competenze o i documenti

L'esecuzione dell'indicizzatore può verificarsi quando si crea prima l' [indicizzatore](search-indexer-overview.md), quando si esegue un indicizzatore su richiesta o quando si imposta un indicizzatore in base a una pianificazione. Dopo l'esecuzione iniziale, un indicizzatore tiene traccia dei documenti di ricerca indicizzati tramite un "limite massimo" interno. Il marcatore non viene mai esposto nell'API, ma internamente l'indicizzatore è in grado di individuare la posizione in cui l'indicizzazione è stata arrestata, in modo che possa riprendersi da dove era stata interrotta alla

È possibile cancellare il limite massimo reimpostando l'indicizzatore se si vuole rielaborare da zero. Le API reset sono disponibili a livelli decrescenti nella gerarchia di oggetti:

+ Intero corpus di ricerca (usare gli [indicizzatori Reset](#reset-indexers))
+ Un documento o un elenco di documenti specifico (usare [Reimposta documenti-anteprima](#reset-docs))
+ Abilità o arricchimento specifiche in un documento (usare le [competenze di reimpostazione-anteprima](#reset-skills))

Le API reset vengono usate per aggiornare il contenuto memorizzato nella cache (applicabile negli scenari di [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) ) o per cancellare il limite massimo e ricompilare l'indice.

Il ripristino, seguito da Run, può rielaborare i documenti esistenti e i nuovi documenti, ma non rimuove i documenti di ricerca orfani nell'indice di ricerca creato nelle esecuzioni precedenti. Per ulteriori informazioni sull'eliminazione, vedere [aggiungere, aggiornare o eliminare documenti](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="run-indexers"></a>Esegui indicizzatori

[Create Indexer](/rest/api/searchservice/create-indexer) crea ed esegue l'indicizzatore a meno che non lo si crei in uno stato disabilitato ("disabled": true). La prima esecuzione richiede un po' più tempo perché la creazione dell'oggetto copre.

[Esegui indicizzatore](/rest/api/searchservice/run-indexer) rileva ed elabora solo le operazioni necessarie per sincronizzare l'indice di ricerca con l'origine dati. L'archiviazione BLOB include il rilevamento delle modifiche incorporato. Altre origini dati, ad esempio Azure SQL o Cosmos DB, devono essere configurate per il rilevamento delle modifiche prima che l'indicizzatore possa leggere solo le righe nuove e aggiornate.

È possibile eseguire un indicizzatore usando uno degli approcci seguenti:

+ Portale di Azure, uso del comando **Esegui** nella pagina indicizzatore
+ [Esegui indicizzatore (REST)](/rest/api/searchservice/run-indexer)
+ [Metodo RunIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) in Azure .NET SDK (oppure usando il metodo RunIndexer equivalente in un altro SDK)

L'esecuzione dell'indicizzatore è soggetta ai limiti seguenti:

+ Il numero massimo di processi dell'indicizzatore è 1 per replica senza processi simultanei.

  Se l'esecuzione dell'indicizzatore è già in grado di eseguire questa notifica: "Impossibile eseguire l'indicizzatore ' <Indexer-Name>', errore:" è attualmente in corso un'altra chiamata all'indicizzatore; non sono consentite chiamate simultanee ".

+ Il tempo di esecuzione massimo è di 2 ore se si usa un skillt e 24 ore senza. 

  Per estendere l'elaborazione, è possibile inserire l'indicizzatore in base a una pianificazione. Il livello gratuito presenta limiti di tempo di esecuzione inferiori. Per l'elenco completo, vedere [limiti dell'indicizzatore](search-limits-quotas-capacity.md#indexer-limits)

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Reimpostare un indicizzatore

La reimpostazione di un indicizzatore è tutto incluso. All'interno dell'indice di ricerca, qualsiasi documento di ricerca originariamente popolato dall'indicizzatore viene contrassegnato per l'elaborazione completa. Tutti i nuovi documenti trovati l'origine sottostante verranno aggiunti all'indice come documenti di ricerca. Se l'indicizzatore è configurato per l'uso di un skillt e la [memorizzazione nella cache](search-howto-incremental-index.md), il livello di competenze viene rieseguito e la cache viene aggiornata.

È possibile reimpostare un indicizzatore usando uno di questi approcci, seguito da un indicizzatore eseguito usando uno dei metodi descritti in precedenza.

+ Portale di Azure, usando il comando **Reimposta** nella pagina indicizzatore
+ [Reimposta indicizzatore (REST)](/rest/api/searchservice/reset-indexer)
+ [Metodo ResetIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) in Azure .NET SDK (oppure usando il metodo RunIndexer equivalente in un altro SDK)

Al termine dell'esecuzione, viene cancellato un flag di reimpostazione. Ogni normale logica di rilevamento delle modifiche operativa per l'origine dati riprenderà alla successiva esecuzione, raccogliendo eventuali altri valori nuovi o aggiornati nel resto del set di dati.

> [!NOTE]
> Una richiesta di reimpostazione determina gli elementi rielaborati (indicizzatore, skill o Document), ma non influisce negativamente sul comportamento di runtime dell'indicizzatore. Se l'indicizzatore ha parametri di runtime, mapping di campi, Caching, opzioni batch e così via, queste impostazioni sono attive quando si esegue un indicizzatore dopo averla reimpostata.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Reimposta le competenze (anteprima)

> [!IMPORTANT] 
> Il [ripristino delle competenze](/rest/api/searchservice/preview-api/reset-skills) è disponibile nell'anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per gli indicizzatori con skillsets, è possibile reimpostare competenze specifiche in modo da forzare l'elaborazione di tale competenza e le competenze downstream che dipendono dall'output. Vengono aggiornati anche gli [arricchimenti memorizzati nella cache](search-howto-incremental-index.md) . La reimpostazione delle competenze invalida i risultati della competenza memorizzati nella cache, che risulta utile quando viene distribuita una nuova versione di una competenza e si desidera che l'indicizzatore esegua nuovamente tale capacità per tutti i documenti. 

Il [ripristino delle competenze](/rest/api/searchservice/preview-api/reset-skills) è disponibile tramite Rest **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

È possibile specificare le singole competenze, come indicato nell'esempio precedente, ma se una di queste competenze richiede output da competenze non in elenco (#2 tramite #4), le competenze non in elenco verranno eseguite a meno che la cache non fornisca le informazioni necessarie. Affinché ciò sia vero, i miglioramenti memorizzati nella cache per le competenze #2 tramite #4 non devono avere dipendenza da #1 (elencati per la reimpostazione).

Se non vengono specificate competenze, viene eseguito l'intero insieme di competenze e se la memorizzazione nella cache è abilitata, viene aggiornata anche la cache.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Reimposta docs (anteprima)

> [!IMPORTANT] 
> La [reimpostazione dei documenti](/rest/api/searchservice/preview-api/reset-documents) è in anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L' [API Reimposta documenti](/rest/api/searchservice/preview-api/reset-documents) accetta un elenco di chiavi del documento, in modo che sia possibile aggiornare documenti specifici. Se specificato, i parametri Reset diventano l'unico fattore determinante di ciò che viene elaborato, indipendentemente dalle altre modifiche nei dati sottostanti. Ad esempio, se 20 BLOB sono stati aggiunti o aggiornati dopo l'ultima esecuzione dell'indicizzatore, ma si reimposta solo un documento, verrà elaborato solo un documento.

Per ogni documento, tutti i campi del documento di ricerca vengono aggiornati con i valori dell'origine dati. Non è possibile selezionare i campi da aggiornare. 

Se il documento viene arricchito tramite un skillt e contiene dati memorizzati nella cache, il skillt viene richiamato solo per i documenti specificati e la cache viene aggiornata per i documenti rielaborati.

Quando si esegue il test di questa API per la prima volta, le API seguenti consentiranno di convalidare e testare i comportamenti:

+ [Ottenere lo stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status) con la versione dell'API `2020-06-30-Preview` per controllare lo stato di reimpostazione e lo stato di esecuzione. È possibile trovare informazioni sulla richiesta di reimpostazione alla fine della risposta allo stato.
+ [Reimpostare i documenti](/rest/api/searchservice/preview-api/reset-documents) con la versione dell'API `2020-06-30-Preview` per specificare i documenti da elaborare.
+ [Eseguire l'indicizzatore](/rest/api/searchservice/run-indexer) per eseguire l'indicizzatore (qualsiasi versione dell'API).
+ Eseguire [ricerche nei documenti](/rest/api/searchservice/search-documents) per verificare la presenza di valori aggiornati e anche per restituire le chiavi del documento se non si è certi del valore. Usare `"select": "<field names>"` se si desidera limitare i campi che vengono visualizzati nella risposta.

### <a name="formulate-and-send-the-reset-request"></a>Formulare e inviare la richiesta di reimpostazione

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

Le chiavi del documento fornite nella richiesta sono valori dell'indice di ricerca, che possono essere diversi dai campi corrispondenti nell'origine dati. Se non si è certi del valore della chiave, [inviare una query](search-query-create.md) per restituire il valore. È possibile utilizzare `select` per restituire solo il campo chiave del documento.

Per i BLOB analizzati in più documenti di ricerca (ad esempio, se è stato usato [jsonLines o jsonArrays](search-howto-index-json-blobs.md), o [delimitedText](search-howto-index-csv-blobs.md)) come modalità di analisi, la chiave del documento viene generata dall'indicizzatore e potrebbe essere sconosciuta. In questa situazione, una query per la chiave del documento sarà strumentale per fornire il valore corretto.

La chiamata dell'API più volte con chiavi diverse aggiunge le nuove chiavi all'elenco delle chiavi del documento reimpostate. Se si chiama l'API con il **`overwrite`** parametro impostato su true, l'elenco corrente delle chiavi del documento da reimpostare viene sovrascritto con il payload della richiesta:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Controlla stato di reimpostazione

Per controllare lo stato di una reimpostazione e per vedere quali chiavi dei documenti sono accodate per l'elaborazione, usare [Get Indexer status](/rest/api/searchservice/get-indexer-status) con **`api-version=06-30-2020-Preview`** . L'API di anteprima restituirà la **`currentState`** sezione, che è possibile trovare alla fine della risposta Get Indexer status.

"Mode" sarà **`indexingAllDocs`** per le competenze di ripristino (perché potenzialmente tutti i documenti sono interessati per i campi popolati tramite l'arricchimento di intelligenza artificiale).

Per la reimpostazione dei documenti, la modalità viene impostata su **`indexingResetDocs`** . Questo stato viene mantenuto dall'indicizzatore fino a quando non vengono elaborate tutte le chiavi del documento fornite nella chiamata Reimposta documenti e non verranno eseguiti altri processi dell'indicizzatore durante l'avanzamento dell'operazione. Per trovare tutti i documenti nell'elenco delle chiavi del documento, è necessario che ogni documento venga individuato e corrisponda alla chiave. questa operazione può richiedere alcuni minuti se il set di dati è di grandi dimensioni. Se un contenitore BLOB contiene centinaia di BLOB e la documentazione che si vuole reimpostare è alla fine, l'indicizzatore non troverà i BLOB corrispondenti finché tutti gli altri non saranno stati controllati per primi.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

Dopo la rielaborazione dei documenti, l'indicizzatore torna alla **`indexingAllDocs`** modalità e elaborerà eventuali altri documenti nuovi o aggiornati alla successiva esecuzione.

## <a name="next-steps"></a>Passaggi successivi

Le API reset vengono usate per informare l'ambito della successiva esecuzione dell'indicizzatore. Per l'elaborazione effettiva, è necessario richiamare un indicizzatore su richiesta eseguire o consentire a un processo pianificato di completare il lavoro. Al termine dell'esecuzione, l'indicizzatore torna alla normale elaborazione, a seconda che si tratti di una pianificazione o di un'elaborazione su richiesta.

Dopo aver reimpostato ed eseguito nuovamente i processi dell'indicizzatore, è possibile monitorare lo stato del servizio di ricerca o ottenere informazioni dettagliate tramite la registrazione diagnostica.

+ [Operazioni sugli indicizzatori (REST)](/rest/api/searchservice/indexer-operations)
+ [Monitorare lo stato dell'indicizzatore di ricerca](search-howto-monitor-indexers.md)
+ [Raccogliere e analizzare i dati di log](search-monitor-logs.md)
+ [Pianificare un indicizzatore](search-howto-schedule-indexers.md)