---
title: Configurare un indicizzatore BLOB
titleSuffix: Azure Cognitive Search
description: Configurare un indicizzatore BLOB di Azure per automatizzare l'indicizzazione del contenuto BLOB per le operazioni di ricerca full-text in Azure ricerca cognitiva.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 6f70ae726cf41395e46760dc5cf7da5b4d61478a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802897"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>Come configurare l'indicizzazione BLOB in ricerca cognitiva

Un indicizzatore BLOB viene usato per l'inserimento di contenuto dall'archiviazione BLOB di Azure a un indice ricerca cognitiva. Gli indicizzatori BLOB vengono spesso usati nell' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md), in cui un [competenze](cognitive-search-working-with-skillsets.md) associate aggiunge un'immagine e l'elaborazione del linguaggio naturale per creare contenuto ricercabile. Tuttavia, è anche possibile usare gli indicizzatori BLOB senza l'arricchimento di intelligenza artificiale, per inserire contenuto da documenti basati su testo, ad esempio PDF, documenti Microsoft Office e formati di file.

Questo articolo illustra come configurare un indicizzatore BLOB per uno scenario. Se non si ha familiarità con i concetti relativi all'indicizzatore, iniziare con gli [indicizzatori in Azure ricerca cognitiva](search-indexer-overview.md) e [creare un indicizzatore di ricerca](search-howto-create-indexers.md) prima di immergersi nell'indicizzazione dei BLOB.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Formati di documento supportati

L'indicizzatore BLOB di Azure ricerca cognitiva può estrarre il testo dai formati dei documenti seguenti:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>Definizioni delle origini dati

La differenza principale tra un indicizzatore BLOB e qualsiasi altro indicizzatore è la definizione dell'origine dati assegnata all'indicizzatore. La definizione dell'origine dati specifica il tipo di origine dati ("Type": "azureblob"), nonché altre proprietà per l'autenticazione e la connessione al contenuto da indicizzare.

Una definizione dell'origine dati BLOB è simile all'esempio seguente:

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

La `"credentials"` proprietà può essere una stringa di connessione, come illustrato nell'esempio precedente, oppure uno degli approcci alternativi descritti nella sezione successiva. La `"container"` proprietà fornisce la posizione del contenuto all'interno di archiviazione di Azure e `"query"` viene usata per specificare una sottocartella nel contenitore. Per altre informazioni sulle definizioni delle origini dati, vedere [creare un'origine dati (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Credenziali

Per specificare le credenziali per il contenitore BLOB, sono disponibili questi modi:

**Stringa di connessione identità gestita**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Questa stringa di connessione non richiede una chiave dell'account, ma è necessario seguire le istruzioni per la [configurazione di una connessione a un account di archiviazione di Azure usando un'identità gestita](search-howto-managed-identities-storage.md).

**Stringa di connessione dell'account di archiviazione con accesso completo**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Per ottenere la stringa di connessione dal portale di Azure, passare al pannello dell'account di archiviazione e quindi selezionare Impostazioni > Chiavi (per gli account di archiviazione della versione classica) oppure Impostazioni > Chiavi di accesso (per gli account di archiviazione di Azure Resource Manager).

Stringa di connessione della **firma di accesso condiviso** (SAS) dell'account di archiviazione:`{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

La firma di accesso condiviso deve avere le autorizzazioni per le operazioni di elenco e lettura per i contenitori e gli oggetti (BLOB).

**Firma di accesso condiviso del contenitore**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

La firma di accesso condiviso deve avere le autorizzazioni per le operazioni di elenco e lettura sul contenitore. Per altre informazioni sulle firme di accesso condiviso di archiviazione, vedere [uso delle firme di accesso condiviso](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Se si usano le credenziali di firma di accesso condiviso, sarà necessario aggiornare periodicamente le credenziali dell'origine dati con firme rinnovate per impedire che scadano. Se le credenziali SAS scadono, l'indicizzatore avrà esito negativo e verrà visualizzato un messaggio di errore simile a "le credenziali specificate nella stringa di connessione non sono valide o sono scadute".  

## <a name="index-definitions"></a>Definizioni di indice

L'indice consente di specificare i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza della ricerca. Per tutti gli indicizzatori è necessario specificare una definizione di indice di ricerca come destinazione. Nell'esempio seguente viene creato un indice semplice usando [create index (API REST)](/rest/api/searchservice/create-index). 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

Per le definizioni di indice è necessario che un campo della `"fields"` raccolta funga da chiave del documento. Le definizioni di indice devono includere anche campi per contenuto e metadati.

Un **`content`** campo è comune al contenuto del BLOB. Contiene il testo estratto dai BLOB. La definizione di questo campo potrebbe essere simile a quella illustrata in precedenza. Non è necessario usare questo nome, ma in questo modo è possibile sfruttare i mapping dei campi impliciti. L'indicizzatore BLOB può inviare il contenuto del BLOB a un campo EDM. String del contenuto nell'indice senza che siano necessari mapping dei campi.

È anche possibile aggiungere campi per i metadati del BLOB che si desidera includere nell'indice. L'indicizzatore può leggere proprietà dei metadati personalizzati, proprietà [dei metadati standard](#indexing-blob-metadata) e proprietà [dei metadati specifiche del contenuto](search-blob-metadata-properties.md) . Per ulteriori informazioni sugli indici, vedere [creare un indice](search-what-is-an-index.md).

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definizione di chiavi di documento e dei mapping dei campi

In un indice di ricerca la chiave del documento identifica in modo univoco ogni documento. Il campo scelto deve essere di tipo `Edm.String` . Per il contenuto del BLOB, i candidati migliori per una chiave del documento sono le proprietà dei metadati sul BLOB.

+ **`metadata_storage_name`** -Questa proprietà è un candidato, ma solo se i nomi sono univoci in tutti i contenitori e le cartelle da indicizzare. Indipendentemente dalla posizione del BLOB, il risultato finale è che la chiave del documento (nome) deve essere univoca nell'indice di ricerca dopo che tutto il contenuto è stato indicizzato. 

  Un altro potenziale problema relativo al nome dell'archiviazione è che potrebbe contenere caratteri non validi per le chiavi del documento, ad esempio i trattini. È possibile gestire i caratteri non validi usando la `base64Encode` [funzione di mapping dei campi](search-indexer-field-mappings.md#base64EncodeFunction). In tal caso, ricordarsi di codificare anche le chiavi del documento quando vengono passate nelle chiamate API, ad esempio il [documento di ricerca (REST)](/rest/api/searchservice/lookup-document). In .NET è possibile usare il [metodo UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) per codificare i caratteri.

+ **`metadata_storage_path`** -l'uso del percorso completo garantisce l'univocità, ma il percorso contiene sicuramente `/` caratteri [non validi in una chiave del documento](/rest/api/searchservice/naming-rules). Come sopra, è possibile usare la `base64Encode` [funzione](search-indexer-field-mappings.md#base64EncodeFunction) per codificare i caratteri.

+ Una terza opzione consiste nell'aggiungere una proprietà dei metadati personalizzata ai BLOB. Questa opzione richiede che il processo di caricamento BLOB aggiunga tale proprietà Metadata a tutti i BLOB. Poiché la chiave è una proprietà obbligatoria, non sarà possibile indicizzare tutti i BLOB in cui manca un valore.

> [!IMPORTANT]
> Se non è presente alcun mapping esplicito per il campo chiave nell'indice, Azure ricerca cognitiva usa automaticamente `metadata_storage_path` come chiave e con base 64 codifica i valori di chiave (la seconda opzione precedente).
>

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato `metadata_storage_name` come chiave del documento. Si supponga che l'indice includa un campo chiave denominato `key` e un altro campo denominato `fileSize` per archiviare le dimensioni del documento. I [mapping dei campi](search-indexer-field-mappings.md) nella definizione dell'indicizzatore stabiliscono le associazioni di campi e `metadata_storage_name` la funzione di mapping dei [ `base64Encode` campi](search-indexer-field-mappings.md#base64EncodeFunction) per gestire i caratteri non supportati.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>Come rendere "ricercabile" un campo codificato

In alcuni casi è necessario usare una versione codificata di un campo come `metadata_storage_path` la chiave, ma è necessario anche che il campo sia ricercabile (senza codifica) nell'indice di ricerca. Per supportare entrambi i casi di utilizzo, è possibile eseguire `metadata_storage_path` il mapping a due campi, uno per la chiave (codificata) e un secondo per un campo percorso che è possibile presupporre è attribuito come "ricercabile" nello schema dell'indice. Nell'esempio seguente vengono illustrati due mapping dei campi per `metadata_storage_path` .

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>Indicizzare contenuto e metadati

I BLOB contengono contenuto e metadati. Il parametro di configurazione `dataToExtract` permette di controllare quali parti dei BLOB vengono indicizzate. I valori possibili sono i seguenti:

+ `contentAndMetadata`: specifica che vengono indicizzati tutti i metadati e il contenuto di testo estratti dal BLOB. Si tratta del valore predefinito.

+ `storageMetadata`: specifica che vengono indicizzati solo [i metadati specificati dall'utente e le proprietà BLOB standard](../storage/blobs/storage-blob-container-properties-metadata.md).

+ `allMetadata` : specifica che le proprietà del BLOB standard e gli eventuali [metadati per i tipi di contenuto trovati](search-blob-metadata-properties.md) vengono estratti dal contenuto del BLOB e indicizzati.

Ad esempio, per indicizzare solo i metadati di archiviazione, usare:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>Indicizzazione di contenuto BLOB

Per impostazione predefinita, i BLOB con contenuto strutturato, ad esempio JSON o CSV, vengono indicizzati come un unico blocco di testo. Tuttavia, se i documenti JSON o CSV hanno una struttura interna (delimitatori), è possibile assegnare modalità di analisi per generare singoli documenti di ricerca per ogni riga o elemento. Per altre informazioni, vedere [indicizzazione di BLOB JSON](search-howto-index-json-blobs.md) e [indicizzazione di BLOB CSV](search-howto-index-csv-blobs.md).

Documento composto o incorporato, ad esempio un archivio ZIP, un documento di Word con messaggi di posta elettronica di Outlook incorporati che contengono allegati o. Il file MSG con allegati viene inoltre indicizzato come un singolo documento. Ad esempio, tutte le immagini estratte dagli allegati di un oggetto. Il file MSG verrà restituito nel campo normalized_images.

Il contenuto di testo del documento viene estratto in un campo di tipo stringa denominato `content`.

  > [!NOTE]
  > Azure ricerca cognitiva limita la quantità di testo da estrarre a seconda del piano tariffario. I [limiti di servizio](search-limits-quotas-capacity.md#indexer-limits) correnti sono di 32.000 caratteri per il livello gratuito, 64.000 per Basic, 4 milioni per standard, 8 milioni per standard S2 e 16 milioni per S3 standard. Un avviso è incluso nella risposta dello stato dell'indicizzatore per i documenti troncati.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>Indicizzazione di metadati BLOB

Gli indicizzatori possono anche indicizzare i metadati del BLOB. Per prima cosa, è possibile estrarre le proprietà dei metadati specificati dall'utente. Per ricevere i valori, è necessario definire il campo nell'indice di ricerca di tipo `Edm.String` , con lo stesso nome della chiave di metadati del BLOB. Se, ad esempio, un BLOB ha una chiave di metadati `Sensitivity` con valore `High` , è necessario definire un campo denominato `Sensitivity` nell'indice di ricerca che verrà popolato con il valore `High` .

In secondo luogo, le proprietà dei metadati BLOB standard possono essere estratte nei campi elencati di seguito. L'indicizzatore BLOB crea automaticamente mapping di campi interni per queste proprietà dei metadati del BLOB. È comunque necessario aggiungere i campi in cui si vuole usare la definizione di indice, ma è possibile omettere la creazione di mapping dei campi nell'indicizzatore.

  + **metadata_storage_name** ( `Edm.String` ): nome file del BLOB. Se, ad esempio, è presente un BLOB /my-container/my-folder/subfolder/resume.pdf, il valore di questo campo è `resume.pdf`.

  + **metadata_storage_path** ( `Edm.String` ): URI completo del BLOB, incluso l'account di archiviazione. Ad esempio: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** ( `Edm.String` ): tipo di contenuto specificato dal codice usato per caricare il BLOB. Ad esempio: `application/octet-stream`.

  + **metadata_storage_last_modified** ( `Edm.DateTimeOffset` ): timestamp dell'Ultima modifica per il BLOB. Azure ricerca cognitiva usa questo timestamp per identificare i BLOB modificati, in modo da evitare di reindicizzare tutto dopo l'indicizzazione iniziale.

  + **metadata_storage_size** ( `Edm.Int64` ): dimensioni del BLOB in byte.

  + **metadata_storage_content_md5** ( `Edm.String` ): hash MD5 del contenuto del BLOB, se disponibile.

  + **metadata_storage_sas_token** ( `Edm.String` ): token SAS temporaneo che può essere usato dalle [competenze personalizzate](cognitive-search-custom-skill-interface.md) per ottenere l'accesso al BLOB. Questo token non deve essere archiviato per un uso successivo, perché potrebbe scadere.

Infine, le proprietà dei metadati specifiche del formato del documento dei BLOB che si sta indicizzando possono essere rappresentate anche nello schema dell'indice. Per ulteriori informazioni sui metadati specifici del contenuto, vedere [proprietà dei metadati del contenuto](search-blob-metadata-properties.md).

È importante sottolineare che non è necessario definire i campi per tutte le proprietà sopra elencate nell'indice di ricerca. è sufficiente acquisire le proprietà necessarie per l'applicazione.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Come controllare quali BLOB sono indicizzati

È possibile controllare quali BLOB sono indicizzati e quali vengono ignorati dal tipo di file del BLOB o impostando le proprietà sul BLOB stesso, causando l'omissione dell'indicizzatore.

### <a name="include-specific-file-extensions"></a>Includi estensioni di file specifiche

Usare `indexedFileNameExtensions` per fornire un elenco delimitato da virgole di estensioni di file da indicizzare (con un punto principale). Ad esempio, per indicizzare solo i BLOB .PDF e .DOCX eseguire questa operazione:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Escludi estensioni di file specifiche

Usare `excludedFileNameExtensions` per fornire un elenco delimitato da virgole di estensioni di file da ignorare (anche in questo caso con un punto principale). Ad esempio, per indicizzare tutti i BLOB ad eccezione di quelli con le estensioni .PNG e .JPEG eseguire questa operazione:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Se `indexedFileNameExtensions` `excludedFileNameExtensions` sono presenti entrambi i parametri e, l'indicizzatore esamina prima di tutto `indexedFileNameExtensions` , quindi all'indirizzo `excludedFileNameExtensions` . Se la stessa estensione di file è presente in entrambi gli elenchi, verrà esclusa dall'indicizzazione.

### <a name="add-skip-metadata-the-blob"></a>Aggiungere i metadati "Skip" al BLOB

I parametri di configurazione dell'indicizzatore si applicano a tutti i BLOB nel contenitore o nella cartella. In alcuni casi si vuole controllare come vengono indicizzati i *singoli BLOB* . È possibile eseguire questa operazione aggiungendo le proprietà e i valori dei metadati seguenti ai BLOB nell'archivio BLOB. Quando l'indicizzatore rileva queste proprietà, ignorerà il BLOB o il relativo contenuto nell'esecuzione dell'indicizzazione.

| Nome proprietà | Valore proprietà | Spiegazione |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Indica all'indicizzatore BLOB di ignorare completamente il BLOB. Non verrà tentata l'estrazione dei metadati né del contenuto. È utile quando un determinato BLOB ha ripetutamente esito negativo e interrompe il processo di indicizzazione. |
| `AzureSearch_SkipContent` |`"true"` |Equivale all'impostazione `"dataToExtract" : "allMetadata"` descritta [in precedenza](#PartsOfBlobToIndex) nell'ambito di un BLOB specifico. |

## <a name="index-large-datasets"></a>Indicizzare set di impostazioni di grandi dimensioni

L'indicizzazione di BLOB può richiedere molto tempo. Nei casi in cui si hanno milioni di BLOB da indicizzare, è possibile velocizzare l'indicizzazione partizionando i dati e usando più indicizzatori per [elaborare i dati in parallelo](search-howto-large-index.md#parallel-indexing). A tale scopo, è possibile procedere come segue:

+ Partizionare i dati in più contenitori BLOB o cartelle virtuali.

+ Configurare diverse origini dati, una per ogni contenitore o cartella. Per puntare a una cartella BLOB, usare il parametro `query`:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ Creare un indicizzatore corrispondente per ogni origine dati. Tutti gli indicizzatori devono puntare allo stesso indice di ricerca di destinazione.  

+ Un'unità di ricerca del servizio permette di eseguire un indicizzatore in qualsiasi momento. La creazione di più indicizzatori come descritto in precedenza è utile solo se effettivamente eseguiti in parallelo.

  Per eseguire più indicizzatori in parallelo, scalare orizzontalmente il servizio di ricerca mediante la creazione di un numero appropriato di partizioni e repliche. Ad esempio, se il servizio di ricerca dispone di 6 unità di ricerca (ad esempio 2 partizioni x 3 repliche), con 6 indicizzatori che possono quindi essere eseguiti contemporaneamente, viene determinato un aumento della velocità effettiva di indicizzazione pari a sei volte. Per altre informazioni sulla scalabilità e sulla pianificazione della capacità, vedere [modificare la capacità di un servizio ricerca cognitiva di Azure](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Gestione degli errori

Gli errori che si verificano comunemente durante l'indicizzazione includono i tipi di contenuto non supportati, il contenuto mancante o i BLOB sovradimensionati.

Per impostazione predefinita, l'indicizzatore BLOB viene arrestato non appena viene rilevato un BLOB con un tipo di contenuto non supportato, ad esempio un'immagine. È possibile usare il `excludedFileNameExtensions` parametro per ignorare determinati tipi di contenuto. Tuttavia, potrebbe essere necessario indicizzare per continuare anche se si verificano errori, quindi eseguire il debug di singoli documenti in un secondo momento. Per ulteriori informazioni sugli errori dell'indicizzatore, vedere [risoluzione dei problemi comuni dell'indicizzatore](search-indexer-troubleshooting.md) e degli [errori e degli avvisi](cognitive-search-common-errors-warnings.md)dell'indicizzatore.

### <a name="respond-to-errors"></a>Risposta agli errori

Sono disponibili quattro proprietà di indicizzatore che controllano la risposta dell'indicizzatore quando si verificano errori. Gli esempi seguenti illustrano come impostare queste proprietà nella definizione dell'indicizzatore. Se un indicizzatore esiste già, è possibile aggiungere queste proprietà modificando la definizione nel portale.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` e `"maxFailedItemsPerBatch"`

Continua l'indicizzazione se si verificano errori in qualsiasi punto di elaborazione, durante l'analisi di BLOB o durante l'aggiunta di documenti a un indice. Impostare queste proprietà sul numero di errori accettabili. Il valore `-1` consente l'elaborazione indipendentemente dal numero di errori che si verificano. In caso contrario, il valore è un numero intero positivo.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` e `"failOnUnprocessableDocument"` 

Per alcuni BLOB, Azure ricerca cognitiva non è in grado di determinare il tipo di contenuto o non è in grado di elaborare un documento di un tipo di contenuto altrimenti supportato. Per ignorare queste condizioni di errore, impostare i parametri di configurazione su `false` :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>Vincoli dell'indicizzatore relax

È anche possibile impostare le [proprietà di configurazione BLOB](/rest/api/searchservice/create-indexer#blob-configuration-parameters) che determinano in modo efficace se esiste una condizione di errore. La proprietà seguente può attenuare i vincoli, evitando gli errori che altrimenti si verificheranno.

+ `"indexStorageMetadataOnlyForOversizedDocuments"` per indicizzare i metadati di archiviazione per il contenuto BLOB troppo grande per l'elaborazione. I BLOB sovradimensionati vengono gestiti come errori per impostazione predefinita. Per i limiti sulle dimensioni del BLOB, vedere [limiti dei servizi](search-limits-quotas-capacity.md).

## <a name="see-also"></a>Vedi anche

+ [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
+ [Creare un indicizzatore](search-howto-create-indexers.md)
+ [Panoramica di intelligenza artificiale su BLOB](search-blob-ai-integration.md)
+ [Panoramica sulla ricerca nei BLOB](search-blob-storage-integration.md)