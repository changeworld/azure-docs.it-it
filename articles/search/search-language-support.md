---
title: Indicizzazione multilingue per query di ricerca non in lingua inglese
titleSuffix: Azure Cognitive Search
description: Creare un indice che supporti il contenuto multilingue, quindi creare query con ambito tale contenuto.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801605"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Come creare un indice per più lingue in Azure ricerca cognitiva

Il requisito principale in un'applicazione di ricerca multilingue è la possibilità di eseguire la ricerca e recuperare i risultati nella lingua dell'utente. In Azure ricerca cognitiva, un modo per soddisfare i requisiti della lingua di un'app multilingue consiste nel creare campi dedicati per l'archiviazione di stringhe in una lingua specifica e quindi limitare la ricerca full-text solo a questi campi in fase di query.

+ Nelle definizioni dei campi impostare un analizzatore del linguaggio che richiami le regole linguistiche della lingua di destinazione. Per visualizzare l'elenco completo degli analizzatori supportati, vedere [aggiungere analizzatori di linguaggi](index-add-language-analyzers.md).

+ Nella richiesta di query impostare i parametri per l'ambito della ricerca full-text in campi specifici e quindi tagliare i risultati dei campi che non forniscono contenuti compatibili con l'esperienza di ricerca che si desidera distribuire.

Il successo di questa tecnica si basa sull'integrità del contenuto dei campi. Azure ricerca cognitiva non converte le stringhe o esegue il rilevamento della lingua come parte dell'esecuzione della query. È responsabilità dell'utente assicurarsi che i campi contengano le stringhe previste.

## <a name="define-fields-for-content-in-different-languages"></a>Definire i campi per il contenuto in lingue diverse

In ricerca cognitiva di Azure le query sono destinate a un singolo indice. Gli sviluppatori che vogliono fornire stringhe specifiche della lingua in un'unica esperienza di ricerca in genere definiscono campi dedicati per l'archiviazione dei valori: un campo per le stringhe in inglese, uno per il francese e così via.

Per impostare l' [analizzatore del linguaggio](index-add-language-analyzers.md), viene utilizzata la proprietà "Analyzer" di una definizione di campo. Verrà usato per l'indicizzazione e l'esecuzione delle query.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Creare e caricare un indice

Un passaggio intermedio necessario (e probabilmente ovvio) consiste nel [compilare e popolare l'indice](search-get-started-dotnet.md) prima di formulare una query. Questo passaggio viene citato qui per motivi di completezza. Un modo per determinare la disponibilità degli indici consiste nel controllare l'elenco degli indici nel [portale](https://portal.azure.com).

> [!TIP]
> Il rilevamento della lingua e la traduzione del testo sono supportati durante l'inserimento dei dati tramite l' [arricchimento ai](cognitive-search-concept-intro.md) e [skillsets](cognitive-search-working-with-skillsets.md). Se si dispone di un'origine dati di Azure con contenuto in linguaggio misto, è possibile provare le funzionalità di rilevamento e traduzione della lingua usando la [procedura guidata Importa dati](cognitive-search-quickstart-blob.md).

## <a name="constrain-the-query-and-trim-results"></a>Limitare la query e ridurre i risultati

I parametri nella query vengono usati per limitare la ricerca ai campi specifici e quindi ridurre i risultati di tutti i campi non utili per lo scenario. 

| Parametri | Scopo |
|-----------|--------------|
| **searchFields** | Limita la ricerca full-text all'elenco dei campi denominati. |
| **$select** | Riduce la risposta in modo da includere solo i campi specificati. Per impostazione predefinita, vengono restituiti tutti i campi recuperabili. Il parametro **$select** permette di scegliere quali restituire. |

Considerando un caso in cui l'obiettivo è limitare la ricerca ai campi che contengono stringhe in francese, è necessario usare **searchFields** perché la destinazione della query siano i campi che contengono stringhe in tale lingua.

Non è necessario specificare l'analizzatore in una richiesta di query. Un analizzatore del linguaggio nella definizione del campo verrà sempre usato durante l'elaborazione della query. Per le query che specificano più campi che richiamano analizzatori di linguaggi diversi, i termini o le frasi verranno elaborati in modo indipendente dagli analizzatori assegnati per ogni campo.

Per impostazione predefinita, una ricerca restituisce tutti i campi contrassegnati come recuperabili. Di conseguenza, si potrebbe voler escludere i campi che non sono conformi all'esperienza di ricerca specifica della lingua che si vuole fornire. In particolare, se si limita la ricerca a un campo con stringhe in francese, probabilmente si vorrà escludere i campi contenenti stringhe in inglese dai risultati. L'uso del parametro di query **$select** consente di controllare i campi restituiti all'applicazione chiamante.

#### <a name="example-in-rest"></a>Esempio di REST

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Esempio in C #

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Aumentare i campi specifici della lingua

È possibile che la lingua dell'agente che esegue una query non sia nota, in tal caso la query può essere inviata a tutti i campi contemporaneamente. È possibile definire la preferenza IA per i risultati in una determinata lingua usando i [profili di Punteggio](index-add-scoring-profiles.md). Nell'esempio riportato di seguito, le corrispondenze rilevate nella descrizione in inglese verranno classificate più in alto rispetto alle corrispondenze in altre lingue:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Includere quindi il profilo di punteggio nella richiesta di ricerca:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Passaggi successivi

+ [Analizzatori di linguaggi](index-add-language-analyzers.md)
+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)
+ [Search Documents REST API](/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti)
+ [Panoramica dell'arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md)
+ [Panoramica di skillsets](cognitive-search-working-with-skillsets.md)