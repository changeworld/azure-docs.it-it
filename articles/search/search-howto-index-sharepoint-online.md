---
title: Configurare un indicizzatore di SharePoint Online (anteprima)
titleSuffix: Azure Cognitive Search
description: Configurare un indicizzatore di SharePoint Online per automatizzare l'indicizzazione del contenuto della raccolta documenti in Azure ricerca cognitiva.
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 558df115043d76acf865f19611e8c4cd322e00a7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679714"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>Come configurare l'indicizzazione di SharePoint online in ricerca cognitiva (anteprima)

> [!IMPORTANT] 
> Il supporto di SharePoint Online è attualmente disponibile in un' **anteprima pubblica gestita**. È possibile richiedere l'accesso all'anteprima gestita compilando [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview).
>
> La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Questa funzionalità è disponibile nell' [API REST versione 2020-06-30-Preview](search-api-preview.md) . Attualmente non è disponibile alcun supporto per il portale o l'SDK.

Questo articolo descrive come usare Azure ricerca cognitiva per indicizzare i documenti, ad esempio i file PDF, i documenti Microsoft Office e diversi altri formati comuni, archiviati nelle raccolte documenti di SharePoint online in un indice di ricerca cognitiva di Azure. In primo luogo, vengono illustrate le nozioni di base relative alla configurazione e alla configurazione dell'indicizzatore. Vengono inoltre descritti in modo più dettagliato i comportamenti e gli scenari che possono verificarsi.

## <a name="functionality"></a>Funzionalità
Un indicizzatore in ricerca cognitiva di Azure è un crawler che estrae i dati e i metadati ricercabili da un'origine dati. L'indicizzatore di SharePoint Online si connetterà al sito di SharePoint Online e indicizza i documenti da una o più raccolte documenti. L'indicizzatore offre le funzionalità seguenti:
+ Indicizzare il contenuto da una o più raccolte documenti di SharePoint Online.
+ Indicizzare il contenuto dalle raccolte documenti di SharePoint Online che si trovano nello stesso tenant del servizio ricerca cognitiva di Azure. L'indicizzatore non funzionerà con i siti di SharePoint che si trovano in un tenant diverso da quello del servizio ricerca cognitiva di Azure. 
+ L'indicizzatore supporta l'indicizzazione incrementale, ovvero identifica il contenuto della raccolta documenti modificato e indicizza solo il contenuto aggiornato nelle esecuzioni di indicizzazione future. Se, ad esempio, 5 PDF sono originariamente indicizzati dall'indicizzatore, viene aggiornato 1, l'indicizzatore viene nuovamente eseguito, l'indicizzatore indicizza solo il file PDF che è stato aggiornato.
+ Per impostazione predefinita, il testo e le immagini normalizzate verranno estratti dai documenti indicizzati. Facoltativamente, è possibile aggiungere un skillt alla pipeline per arricchire ulteriormente il contenuto. Altre informazioni su skillsets sono disponibili nell'articolo [concetti relativi alle competenze in Azure ricerca cognitiva](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Formati di documento supportati

L'indicizzatore di Azure ricerca cognitiva SharePoint Online può estrarre il testo dai formati dei documenti seguenti:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Indicizzazione incrementale e rilevamento delle eliminazioni
Per impostazione predefinita, l'indicizzatore di SharePoint Online supporta l'indicizzazione incrementale, vale a dire che identifica il contenuto della raccolta documenti modificato e indicizza solo il contenuto aggiornato nelle esecuzioni di indicizzazione future. Se, ad esempio, 5 documenti di Word vengono originariamente indicizzati dall'indicizzatore, 1 viene aggiornato, l'indicizzatore viene nuovamente eseguito, l'indicizzatore Reindicizza solo il documento di 1 Word che è stato aggiornato.

Il rilevamento dell'eliminazione è supportato anche per impostazione predefinita. Ciò significa che se un documento viene eliminato da una raccolta documenti di SharePoint Online, l'indicizzatore rileva l'eliminazione durante un'esecuzione futura dell'indicizzatore e rimuove il documento dall'indice.

## <a name="setting-up-sharepoint-online-indexing"></a>Impostazione dell'indicizzazione di SharePoint Online
Per configurare l'indicizzatore di SharePoint Online, è necessario eseguire alcune azioni nella portale di Azure e alcune azioni usando l'API REST di anteprima. Questa versione di anteprima non è supportata dall'SDK.

### <a name="step-1-enable-system-assigned-managed-identity"></a>Passaggio 1: abilitare l'identità gestita assegnata dal sistema
Quando è abilitata un'identità gestita assegnata dal sistema, Azure crea un'identità per il servizio di ricerca che può essere usata dall'indicizzatore.

![Abilita identità gestita assegnata dal sistema](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Abilita identità gestita assegnata dal sistema")

Dopo aver selezionato **Salva**, viene visualizzato un ID oggetto che è stato assegnato al servizio di ricerca.

![Identità gestita assegnata dal sistema](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Identità gestita assegnata dal sistema")

### <a name="step-2-create-an-aad-application"></a>Passaggio 2: creare un'applicazione AAD
L'indicizzatore di SharePoint Online utilizzerà questa applicazione AAD per l'autenticazione. 

1.  Passare al [portale di Azure](https://portal.azure.com/).

1.  Aprire il menu sul lato sinistro della pagina principale e selezionare **Azure Active Directory** quindi selezionare **registrazioni app**. Selezionare **+ nuova registrazione**.
    1.  Specificare un nome per l'app.
    2.  Selezionare **single tenant**.
    3.  Non è necessario alcun URI di reindirizzamento.
    4.  Selezionare **Registra**

1.  Selezionare **autorizzazioni API** dal menu a sinistra, quindi **aggiungere un'autorizzazione**, quindi **Microsoft Graph** le **autorizzazioni delegate**. Aggiungere le autorizzazioni API seguenti: 
    1.  **Delegated-files. Read. All** 
    2.  **Delegato-sites. Read. All** 
    3.  **Delegato-utente. Read**

    ![Autorizzazioni dell'API delegate](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Autorizzazioni dell'API delegate")

    L'utilizzo di autorizzazioni delegate indica che l'indicizzatore accederà al sito di SharePoint nel contesto utente. Quindi, quando si esegue l'indicizzatore, sarà possibile accedere solo al contenuto a cui ha accesso l'utente connesso. L'accesso utente si verifica quando si crea l'indicizzatore o si aggiorna l'origine della data. Il passaggio di accesso è descritto più avanti in questo articolo.

1.  Selezionare la scheda **Authentication (autenticazione** ). Impostare **Consenti flussi client pubblici** su **Sì** , quindi selezionare **Salva**.

1.  Selezionare **+ Aggiungi una piattaforma**, quindi **applicazioni per dispositivi mobili e desktop**, quindi controlla `https://login.microsoftonline.com/common/oauth2/nativeclient` , quindi **Configura**.

    ![Configurazione dell'autenticazione app AAD](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "Configurazione dell'autenticazione app AAD")

1.  Concedere il consenso dell'amministratore (obbligatorio solo per determinati tenant).

    Alcuni tenant sono bloccati in modo tale che sia necessario il consenso dell'amministratore per queste autorizzazioni dell'API delegata. In tal caso, è necessario avere un amministratore concedere il consenso dell'amministratore per questa applicazione AAD prima di creare l'indicizzatore. 

    Dal momento che non tutti i tenant presentano questo requisito, è consigliabile ignorare questo passaggio e continuare con le istruzioni. Se durante la creazione dell'indicizzatore è necessario il consenso dell'amministratore, l'autenticazione non riesce a indicare che è necessario un amministratore per approvare l'autenticazione. In tal caso, chiedere al tenant di concedere il consenso usando il pulsante seguente.

    ![Consenso dell'amministratore della concessione dell'app AAD](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "Consenso dell'amministratore della concessione dell'app AAD")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>Passaggio 3: creare un'origine dati
> [!IMPORTANT] 
> A partire da questa sezione è necessario usare l'API REST di anteprima per i passaggi rimanenti. Se non si ha familiarità con l'API REST di Azure ricerca cognitiva, è consigliabile dare un'occhiata a questa [Guida introduttiva](search-get-started-rest.md).

Un'origine dati specifica i dati da indicizzare, le credenziali necessarie per accedere ai dati e i criteri che consentono di identificare in modo efficace le modifiche apportate ai dati (righe nuove, modificate o eliminate). Un'origine dati può essere usata da più indicizzatori nello stesso servizio di ricerca.

Per l'indicizzazione di SharePoint, l'origine dati deve disporre delle proprietà obbligatorie seguenti:
+ **name** è il nome univoco dell'origine dati all'interno del servizio di ricerca.
+ il **tipo** deve essere "SharePoint". Con distinzione tra maiuscole e minuscole.
+ le **credenziali** forniscono l'endpoint di SharePoint Online e l'ID dell'applicazione AAD (client). Un endpoint SharePoint Online di esempio è `https://microsoft.sharepoint.com/teams/MySharePointSite` . È possibile ottenere l'endpoint di SharePoint Online passando all'home page del sito di SharePoint e copiando l'URL dal browser.
+ **contenitore** consente di specificare la raccolta documenti da indicizzare. Altre informazioni sulla creazione del contenitore sono disponibili nella sezione [controllo dei documenti indicizzati](#controlling-which-documents-are-indexed) di questo documento.

Per creare un'origine dati:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>Passaggio 4: creare un indice
L'indice consente di specificare i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza della ricerca.

Di seguito viene illustrato come creare un indice con un campo contenuto ricercabile per archiviare il testo estratto dai documenti in una raccolta documenti:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

Per altre informazioni, vedere [create index (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>Passaggio 5: creare un indicizzatore
Un indicizzatore si connette a un'origine dati con un indice di ricerca di destinazione e pianifica l’automatizzazione dell'aggiornamento dei dati. Dopo aver creato l'indice e l'origine dati, si è pronti per creare l'indicizzatore.

In questa sezione verrà richiesto di effettuare l'accesso con le credenziali dell'organizzazione che hanno accesso al sito di SharePoint. Se possibile, è consigliabile creare un nuovo account utente aziendale e assegnare al nuovo utente le autorizzazioni esatte desiderate per l'indicizzatore.

Per la creazione dell'indicizzatore sono necessari alcuni passaggi:

1.  Inviare una richiesta per tentare di creare l'indicizzatore.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index"
        }
    
    ```

1.  Quando si crea l'indicizzatore per la prima volta, non riuscirà e verrà visualizzato l'errore seguente. Passare al collegamento nel messaggio di errore. Se non si passa al collegamento entro 10 minuti, il codice scadrà e sarà necessario ricreare l' [origine dati](#create-data-source).

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Fornire il codice fornito nel messaggio di errore.

    ![Immettere il codice del dispositivo](media/search-howto-index-sharepoint-online/enter-device-code.png "Immettere il codice del dispositivo")

1.  L'indicizzatore di SharePoint accederà al contenuto di SharePoint come utente connesso. L'utente che esegue l'accesso durante questo passaggio sarà l'utente che ha eseguito l'accesso. Quindi, se si accede con un account utente che non ha accesso a un documento nella raccolta documenti che si vuole indicizzare, l'indicizzatore non avrà accesso a tale documento.

    Se possibile, è consigliabile creare un nuovo account utente e assegnare al nuovo utente le autorizzazioni esatte per l'indicizzatore.

1.  Approva le autorizzazioni richieste.

    ![Approva autorizzazioni API](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "Approva autorizzazioni API")

1.  Inviare di nuovo la richiesta di creazione dell'indicizzatore. Questa volta la richiesta deve avere esito positivo. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>Passaggio 6: controllare lo stato dell'indicizzatore
Dopo aver creato l'indicizzatore, è possibile controllare lo stato dell'indicizzatore effettuando la richiesta seguente.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Altre informazioni sullo stato dell'indicizzatore sono disponibili qui: [ottenere lo stato dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Aggiornamento dell'origine dati
Se non sono presenti aggiornamenti per l'oggetto origine dati, l'indicizzatore può essere eseguito in base a una pianificazione senza alcuna interazione da parte dell'utente. Tuttavia, ogni volta che l'oggetto origine dati ricerca cognitiva di Azure viene aggiornato, sarà necessario eseguire di nuovo l'accesso per consentire l'esecuzione dell'indicizzatore. Se ad esempio si modifica la query dell'origine dati, sarà necessario eseguire di nuovo l'accesso utilizzando `https://microsoft.com/devicelogin` e un nuovo codice.

Dopo che l'origine dati è stata aggiornata, attenersi alla procedura seguente:

1.  Avviare manualmente un'esecuzione dell'indicizzatore.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Altre informazioni sulla richiesta di esecuzione dell'indicizzatore sono disponibili qui: [eseguire l'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/run-indexer).

1.  Verificare lo stato dell'indicizzatore. Se l'ultima esecuzione dell'indicizzatore contiene un errore che indica di passare a `https://microsoft.com/devicelogin` , passare a tale pagina e fornire il nuovo codice. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Altre informazioni sullo stato dell'indicizzatore sono disponibili qui: [ottenere lo stato dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

1.  Accedi

1.  Avviare manualmente un indicizzatore e verificare lo stato dell'indicizzatore. Questa volta l'esecuzione dell'indicizzatore dovrebbe essere avviata correttamente.

## <a name="indexing-document-metadata"></a>Indicizzazione dei metadati del documento
Se l'indicizzatore è stato impostato per indicizzare i metadati del documento, i metadati seguenti saranno disponibili per l'indice.

> [!NOTE]
> I metadati personalizzati non sono inclusi nella versione corrente dell'anteprima.

| Identificatore | Tipo | Descrizione | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | Chiave combinata di ID sito, ID libreria e ID elemento che identifica in modo univoco un elemento in una raccolta documenti per un sito. |
| metadata_spo_site_id | Edm.String | ID del sito di SharePoint Online. |
| metadata_spo_library_id | Edm.String | ID della raccolta documenti. |
| metadata_spo_item_id | Edm.String | ID dell'elemento (documento) nella libreria. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | Data/ora dell'Ultima modifica (UTC) dell'elemento. |
| metadata_spo_item_name | Edm.String | Nome dell'elemento. |
| metadata_spo_item_size | Edm.Int64 | Dimensioni (in byte) dell'elemento. | 
| metadata_spo_item_content_type | Edm.String | Tipo di contenuto dell'elemento. | 
| metadata_spo_item_extension | Edm.String | Estensione dell'elemento. |
| metadata_spo_item_weburi | Edm.String | URI dell'elemento. |
| metadata_spo_item_path | Edm.String | Combinazione del percorso e del nome dell'elemento padre. | 

L'indicizzatore di SharePoint Online supporta inoltre metadati specifici per ogni tipo di documento. Altre informazioni sono reperibili nelle [proprietà dei metadati del contenuto usate in Azure ricerca cognitiva](search-blob-metadata-properties.md).

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Controllo dei documenti indicizzati
Un singolo indicizzatore di SharePoint Online può indicizzare il contenuto di una o più raccolte documenti. Utilizzare il parametro *container* quando si crea l'origine dati per indicare le raccolte documenti che si desidera indicizzare. Il *contenitore* dell'origine dati ha due proprietà: *Name* e *query*. 

### <a name="name"></a>Nome
La proprietà *Name* è obbligatoria e deve essere uno dei tre valori seguenti:
+ *defaultSiteLibrary*
    + Indicizzare tutto il contenuto dalla raccolta documenti predefinita dei siti.
+   *allSiteLibraries*
    + Indicizzare tutto il contenuto di tutte le raccolte documenti in un sito. Le raccolte documenti non vengono indicizzate da un sito secondario. Ma possono essere specificati nella *query* .
+   *useQuery*
    + Solo il contenuto dell'indice definito nella *query*.

### <a name="query"></a>Query
La proprietà *query* è costituita da coppie parola chiave/valore. Di seguito sono riportate le parole chiave che è possibile usare. I valori sono URL sito o URL raccolta documenti.

> [!NOTE]
> Per ottenere il valore di una parola chiave specifica, è consigliabile aprire SharePoint online in un browser, passando alla raccolta documenti che si sta tentando di includere/escludere e copiando l'URI dal browser. Questo è il modo più semplice per ottenere il valore da usare con una parola chiave nella query.

| Parola chiave | Descrizione query | Esempio |
| ------------- | -------------- | ----------- |
| Null | Se è null o vuoto, indicizzare la raccolta documenti predefinita o tutte le raccolte documenti a seconda del nome del contenitore. | Indicizzare tutto il contenuto dalla libreria sito predefinita: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | Indicizzare il contenuto di tutte le librerie nel sito definito nella stringa di connessione. Questi sono limitati ai siti secondari del sito <br><br> Il valore della *query* per questa parola chiave deve essere l'URI del sito o del sito secondario. | Indicizzare tutto il contenuto da tutte le raccolte documenti in sito. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | Indicizzare il contenuto da questa libreria. <br><br> Il valore della *query* per questa parola chiave deve essere in uno dei formati seguenti: <br><br> Esempio 1: <br><br> *includeLibrary = [sito o sito secondario]/[raccolta documenti]* <br><br> Esempio 2: <br><br> URI copiato dal browser. | Indicizzare tutto il contenuto da MyDocumentLibrary: <br><br> Esempio 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Esempio 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  Non indicizzare il contenuto da questa libreria. <br><br> Il valore della *query* per questa parola chiave deve essere in uno dei formati seguenti: <br><br> Esempio 1: <br><br> *excludeLibrary = [URI sito o sito secondario]/[raccolta documenti]* <br><br> Esempio 2: <br><br> URI copiato dal browser. | Indicizzare tutto il contenuto di tutte le librerie personali ad eccezione di MyDocumentLibrary: <br><br> Esempio 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Esempio 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>Indice per tipo di file
È possibile controllare quali documenti sono indicizzati e quali vengono ignorati.

### <a name="include-documents-having-specific-file-extensions"></a>Includi documenti con estensioni di file specifiche
È possibile indicizzare solo i documenti con le estensioni di file specificate tramite il `indexedFileNameExtensions` parametro di configurazione dell'indicizzatore. Il valore è una stringa contenente un elenco delimitato da virgole di estensioni di file (precedute da un punto). Ad esempio, per indicizzare solo. PDF e. Documenti DOCX, procedere come segue:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Escludi documenti con estensioni di file specifiche
È possibile escludere dall'indicizzazione documenti con estensioni di file specifiche utilizzando il `excludedFileNameExtensions` parametro di configurazione. Il valore è una stringa contenente un elenco delimitato da virgole di estensioni di file (precedute da un punto). Ad esempio, per indicizzare tutto il contenuto tranne quelli con. PNG e. Estensioni JPEG:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Se `indexedFileNameExtensions` `excludedFileNameExtensions` sono presenti entrambi i parametri e, Azure ricerca cognitiva esamina prima di tutto `indexedFileNameExtensions` , quindi all'indirizzo `excludedFileNameExtensions` . Ciò significa che se la stessa estensione di file è presente in entrambi gli elenchi, verrà esclusa dall'indicizzazione.

## <a name="handling-errors"></a>Gestione degli errori
Per impostazione predefinita, l'indicizzatore di SharePoint Online viene arrestato non appena viene rilevato un documento con un tipo di contenuto non supportato, ad esempio un'immagine. Naturalmente, è possibile usare il parametro `excludedFileNameExtensions` per ignorare determinati tipi di contenuto. Tuttavia, potrebbe essere necessario indicizzare i documenti senza conoscere tutti i possibili tipi di contenuto in anticipo. Per continuare l'indicizzazione quando viene rilevato un tipo di contenuto non supportato, impostare il `failOnUnsupportedContentType` parametro di configurazione su false:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

Per alcuni documenti, Azure ricerca cognitiva non è in grado di determinare il tipo di contenuto o non è in grado di elaborare un documento di tipo di contenuto altrimenti supportato. Per ignorare la modalità di errore, impostare il parametro di configurazione `failOnUnprocessableDocument` su False:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure ricerca cognitiva limita le dimensioni dei documenti indicizzati. Questi limiti sono documentati in [limiti di servizio in Azure ricerca cognitiva](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Per impostazione predefinita, i documenti sovradimensionati vengono considerati come errori. Tuttavia, è comunque possibile indicizzare i metadati di archiviazione dei documenti sovradimensionati se si imposta il `indexStorageMetadataOnlyForOversizedDocuments` parametro di configurazione su true:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

È anche possibile continuare l'indicizzazione se si verificano errori in qualsiasi punto di elaborazione, durante l'analisi dei documenti o durante l'aggiunta di documenti a un indice. Per ignorare un determinato numero di errori, impostare i parametri di configurazione `maxFailedItems` e `maxFailedItemsPerBatch` sui valori desiderati. Ad esempio:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Vedere anche
+ [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
+ [Proprietà dei metadati del contenuto usate in Azure ricerca cognitiva](search-blob-metadata-properties.md)
