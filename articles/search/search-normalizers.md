---
title: Normalizzazione del testo per filtri, facet, ordinamento
titleSuffix: Azure Cognitive Search
description: Specificare i normalizzatori per i campi di testo in un indice per personalizzare il comportamento di corrispondenza delle parole chiave Strict in filtro, facet e ordinamento.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609529"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Normalizzazione del testo per filtri senza distinzione tra maiuscole e minuscole, facet e ordinamento

 > [!IMPORTANT]
 > Normalizzatore è disponibile in anteprima pubblica, disponibile tramite l' **API REST 2020-06-30-Preview**. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l'utilizzo aggiuntive.

La ricerca e il recupero di documenti da un indice di ricerca cognitiva di Azure richiedono la corrispondenza tra la query e il contenuto del documento. Il contenuto può essere analizzato in modo da produrre token per la corrispondenza come nel caso in cui si `search` Usa il parametro oppure può essere usato così com'è per la corrispondenza di parole chiave strict come illustrato con `$filter` , `facets` e `$orderby` . Questo approccio di tipo "All-or-Nothing" riguarda la maggior parte degli scenari, ma non è sufficiente se è necessario eseguire operazioni di pre-elaborazione semplici, come la combinazione di maiuscole e minuscole, la rimozione degli accenti, asciifolding e così via

Considera gli esempi che seguono:

+ `$filter=City eq 'Las Vegas'` restituirà solo i documenti che contengono il testo esatto "Las Vegas" ed escludono i documenti con "LAS VEGAS" e "Las Vegas", che non sono adeguati quando il caso d'uso richiede tutti i documenti indipendentemente dalla combinazione di maiuscole e minuscole.

+ `search=*&facet=City,count:5` restituirà "Las Vegas", "LAS VEGAS" e "Las Vegas" come valori distinti nonostante la stessa città.

+ `search=usa&$orderby=City` restituirà le città nell'ordine lessicografico: "Las Vegas", "Seattle", "Las Vegas", anche se lo scopo è quello di ordinare le stesse città a prescindere dal caso. 

## <a name="normalizers"></a>Normalizzatori

Un *normalizzatore* è un componente del motore di ricerca responsabile della pre-elaborazione del testo per la corrispondenza delle parole chiave. I normalizzatori sono simili agli analizzatori, ad eccezione del fatto che non tokenize la query. Di seguito sono riportate alcune delle trasformazioni che è possibile ottenere utilizzando i normalizzatori:

+ Converte in minuscolo o maiuscolo.
+ Normalizzare accenti e segni diacritici come ö o ê nei caratteri ASCII equivalenti "o" e "e".
+ Eseguire il mapping `-` di caratteri come e spazi vuoti in un carattere specificato dall'utente.

I normalizzatori possono essere specificati nei campi di testo nell'indice e vengono applicati sia in fase di indicizzazione che di esecuzione delle query.

## <a name="predefined-and-custom-normalizers"></a>Normalizzatori predefiniti e personalizzati 

Azure ricerca cognitiva supporta normalizzatori predefiniti per i casi d'uso comuni insieme alla capacità di personalizzare in base alle esigenze.

| Category | Descrizione |
|----------|-------------|
| [Normalizzatori predefiniti](#predefined-normalizers) | Fornito e può essere utilizzato senza alcuna configurazione. |
|[Normalizzatori personalizzati](#add-custom-normalizers) | Per gli scenari avanzati. Richiede la configurazione definita dall'utente di una combinazione di elementi esistenti, costituita da filtri char e token. <sup>1</sup>|

<sup>(1)</sup> i normalizzatori personalizzati non specificano Tokenizer poiché i normalizzatori producono sempre un singolo token.

## <a name="how-to-specify-normalizers"></a>Come specificare i normalizzatori

I normalizzatori possono essere specificati in base al campo nei campi `Edm.String` di testo (e `Collection(Edm.String)` ) che hanno almeno una delle `filterable` `sortable` proprietà, o impostate su `facetable` true. L'impostazione di un normalizzatore è facoltativa ed è `null` per impostazione predefinita. È consigliabile valutare i normalizzatori predefiniti prima di configurarne uno personalizzato per facilitarne l'uso. Se i risultati non sono previsti, provare con un normalizzatore diverso.

I normalizzatori possono essere specificati solo quando un nuovo campo viene aggiunto all'indice. È consigliabile valutare le esigenze di normalizzazione in anticipo e assegnare normalizzatori nelle fasi iniziali dello sviluppo quando si eliminano e si ricreano gli indici è routine. I normalizzatori non possono essere specificati in un campo che è già stato creato.

1. Quando si crea una definizione di campo nell' [Indice](/rest/api/searchservice/create-index), impostare la proprietà  **normalizzatore** su uno dei seguenti elementi: un [normalizzatore predefinito](#predefined-normalizers) , ad esempio `lowercase` , o un normalizzatore personalizzato (definito nello stesso schema di indice).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. I normalizzatori personalizzati devono essere definiti nella sezione **[normalizzatori]** dell'indice prima di essere assegnati alla definizione del campo, come illustrato nel passaggio precedente. Per altre informazioni, vedere [creare un indice](/rest/api/searchservice/create-index) e [aggiungere normalizzatori personalizzati](#add-custom-normalizers).


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Per modificare il normalizzatore di un campo esistente, è necessario ricompilare completamente l'indice (non è possibile ricompilare singoli campi).

Una soluzione efficace per gli indici di produzione, in cui la ricompilazione degli indici è costosa, consiste nel creare un nuovo campo identico a quello precedente, ma con il nuovo normalizzatore e usarlo al posto di quello precedente. Usare [Aggiornare un indice](/rest/api/searchservice/update-index) per incorporare il nuovo campo e [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) per compilarlo. In un secondo momento, nell'ambito della manutenzione pianificata dell'indice, sarà possibile pulire l'indice per rimuovere i campi obsoleti.

## <a name="add-custom-normalizers"></a>Aggiungi normalizzatori personalizzati

I normalizzatori personalizzati sono definiti all'interno dello schema dell'indice e possono essere specificati utilizzando la proprietà Field. La definizione del normalizzatore personalizzato include un nome, un tipo, uno o più filtri char e filtri token. I filtri e i filtri per i token sono i blocchi predefiniti di un normalizzatore personalizzato e sono responsabili dell'elaborazione del testo. Questi filtri vengono applicati da sinistra a destra.

 `token_filter_name_1`È il nome del filtro del token e `char_filter_name_1` e `char_filter_name_2` sono i nomi dei filtri char (vedere i filtri del [token supportati](#supported-token-filters) e le tabelle dei filtri char seguenti per i valori validi).

La definizione del normalizzatore fa parte dell'indice più grande. Per informazioni sul resto dell'indice, vedere [Create Index API](/rest/api/searchservice/create-index) (Creare l'API dell'indice).

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

I normalizzatori personalizzati possono essere aggiunti durante la creazione dell'indice o in un secondo momento aggiornando uno esistente. Per aggiungere un normalizzatore personalizzato a un indice esistente, è necessario specificare il flag **allowIndexDowntime** nell' [indice di aggiornamento](/rest/api/searchservice/update-index) e l'indice non sarà disponibile per alcuni secondi.

## <a name="normalizers-reference"></a>Riferimento ai normalizzatori

### <a name="predefined-normalizers"></a>Normalizzatori predefiniti
|**Nome**|**Descrizione e opzioni**|  
|-|-|  
|standard| Minuscole il testo seguito da asciifolding.|  
|lettere minuscole| Trasforma i caratteri in lettere minuscole.|
|maiuscole| Trasforma i caratteri in maiuscolo.|
|asciifolding| Trasforma i caratteri che non si trovano nel blocco Unicode di base per il rispettivo equivalente ASCII, se ne esiste uno. Ad esempio, la modifica di à in a.|
|elision| Rimuove l'Elissa dall'inizio dei token.|

### <a name="supported-char-filters"></a>Filtri char supportati
Per ulteriori informazioni sui filtri char, fare riferimento a [char filters Reference](index-add-custom-analyzers.md#CharFilter).
+ [mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Filtri di token supportati
Nell'elenco seguente sono illustrati i filtri token supportati per i normalizzatori ed è un subset dei filtri di token complessivi che interessano l'analisi lessicale. Per ulteriori informazioni sui filtri, vedere riferimento ai [filtri dei token](index-add-custom-analyzers.md#TokenFilters).

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [minuscolo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [maiuscolo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Esempio di normalizzatore personalizzato

Nell'esempio seguente viene illustrata una definizione di normalizzatore personalizzata con filtri char e filtri di token corrispondenti. Le opzioni personalizzate per i filtri char e i filtri token vengono specificate separatamente come costrutti denominati, quindi vi viene fatto riferimento nella definizione del normalizzatore, come illustrato di seguito.

* Un normalizzatore personalizzato "my_custom_normalizer" è definito nella `normalizers` sezione della definizione dell'indice.
* Il normalizzatore è costituito da due filtri char e tre filtri di token: Elision, minuscole e il filtro asciifolding personalizzato "my_asciifolding".
* Il primo filtro char "map_dash" sostituisce tutti i trattini con caratteri di sottolineatura mentre la seconda "remove_whitespace" rimuove tutti gli spazi.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Vedi anche
+ [Analizzatori per l'elaborazione linguistica e del testo](search-analyzers.md)

+ [Search Documents REST API](/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti) 
