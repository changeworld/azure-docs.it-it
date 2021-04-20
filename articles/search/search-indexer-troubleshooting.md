---
title: Risolvere i problemi comuni dell'indicizzatore di ricerca
titleSuffix: Azure Cognitive Search
description: Correggere gli errori e i problemi comuni con gli indicizzatori in Ricerca cognitiva di Azure, tra cui la connessione all'origine dati, il firewall e i documenti mancanti.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740063"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Risoluzione dei problemi comuni dell'indicizzatore in Ricerca cognitiva di Azure

Gli indicizzatori possono inserirsi in una serie di problemi durante l'indicizzazione dei dati Ricerca cognitiva di Azure. Le principali categorie di errore includono:

* [Connessione a un'origine dati o ad altre risorse](#connection-errors)
* [Elaborazione di documenti](#document-processing-errors)
* [Inserimento di documenti in un indice](#index-errors)

## <a name="connection-errors"></a>Errori di connessione

> [!NOTE]
> Gli indicizzatori hanno un supporto limitato per l'accesso alle origini dati e ad altre risorse protette dai meccanismi di sicurezza di rete di Azure. Attualmente, gli indicizzatori possono accedere alle origini dati solo tramite meccanismi di restrizione dell'intervallo di indirizzi IP o regole del gruppo di sicurezza di rete corrispondenti, se applicabile. I dettagli per l'accesso a ogni origine dati supportata sono disponibili di seguito.
>
> È possibile trovare l'indirizzo IP del servizio di ricerca effettuando il ping del relativo nome di dominio completo (ad esempio, `<your-search-service-name>.search.windows.net` ).
>
> È possibile trovare l'intervallo di indirizzi IP del tag di servizio usando i file JSON scaricabili o `AzureCognitiveSearch` [](../virtual-network/service-tags-overview.md#available-service-tags) tramite l'API di individuazione dei [tag di servizio](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). [](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) L'intervallo di indirizzi IP viene aggiornato ogni settimana.

### <a name="configure-firewall-rules"></a>Configurare le regole del firewall

Archiviazione di Azure, CosmosDB e Azure SQL un firewall configurabile. Non vengono visualizzati messaggi di errore specifici quando il firewall è abilitato. In genere, gli errori del firewall sono generici e sono simili `The remote server returned an error: (403) Forbidden` a o `Credentials provided in the connection string are invalid or have expired` .

Sono disponibili 2 opzioni per consentire agli indicizzatori di accedere a queste risorse in un'istanza di questo tipo:

* Disabilitare il firewall, consentendo l'accesso da **Tutte le** reti (se possibile).
* In alternativa, è possibile consentire l'accesso per l'indirizzo IP del servizio di ricerca e l'intervallo di indirizzi IP del tag di servizio nelle regole del firewall della risorsa (restrizione dell'intervallo `AzureCognitiveSearch` [](../virtual-network/service-tags-overview.md#available-service-tags) di indirizzi IP).

Per informazioni dettagliate sulla configurazione delle restrizioni dell'intervallo di indirizzi IP per ogni tipo di origine dati, vedere i collegamenti seguenti:

* [Archiviazione di Azure](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [SQL di Azure](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Limitazione:** come indicato nella documentazione precedente per Archiviazione di Azure, le restrizioni dell'intervallo di indirizzi IP funzioneranno solo se il servizio di ricerca e l'account di archiviazione si verificano in aree diverse.

Le funzioni di Azure (che possono essere usate come competenza [api Web personalizzata)](cognitive-search-custom-skill-web-api.md)supportano anche le [restrizioni relative all'indirizzo IP.](../azure-functions/ip-addresses.md#ip-address-restrictions) L'elenco di indirizzi IP da configurare sarà l'indirizzo IP del servizio di ricerca e l'intervallo di indirizzi IP del `AzureCognitiveSearch` tag del servizio.

I dettagli per l'accesso ai dati in SQL Server in una macchina virtuale di Azure sono descritti [qui](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Configurare le regole del gruppo di sicurezza di rete

Quando si accede ai dati in un'istanza gestita di SQL o quando una macchina virtuale di Azure viene usata come URI del servizio Web per una competenza [api Web](cognitive-search-custom-skill-web-api.md)personalizzata, i clienti non devono preoccuparsi di indirizzi IP specifici.

In questi casi, la macchina virtuale di Azure o l'istanza gestita di SQL può essere configurata per risiedere all'interno di una rete virtuale. È quindi possibile configurare un gruppo di sicurezza di rete per filtrare il tipo di traffico di rete che può fluire da e verso le subnet di rete virtuale e le interfacce di rete.

Il tag di servizio può essere usato direttamente nelle regole del gruppo di sicurezza di rete in ingresso senza dover `AzureCognitiveSearch` cercare il relativo intervallo di indirizzi IP. [](../virtual-network/manage-network-security-group.md#work-with-security-rules)

Altri dettagli per l'accesso ai dati in un'istanza gestita di SQL sono descritti [qui](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>L'indicizzazione di CosmosDB non è abilitata

Ricerca cognitiva di Azure ha una dipendenza implicita dall'Cosmos DB indicizzazione. Se si disattiva l'indicizzazione automatica in Cosmos DB, Ricerca cognitiva di Azure restituisce uno stato corretto, ma non riesce a indicizzare il contenuto del contenitore. Per istruzioni su come controllare le impostazioni e attivare l'indicizzazione, vedere [Gestire l'indicizzazione in Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

### <a name="sharepoint-online-conditional-access-policies"></a>Criteri di accesso condizionale di SharePoint Online

Quando si crea un indicizzatore di SharePoint Online, si esegue un passaggio che richiede l'accesso all'app AAD dopo aver fornito il codice del dispositivo. Se viene visualizzato un messaggio che indica che l'accesso è stato eseguito correttamente, ma l'amministratore richiede la gestione del dispositivo che richiede l'accesso, è probabile che all'indicizzatore venga impedito l'accesso alla raccolta documenti di SharePoint Online [a](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview) causa di criteri di accesso condizionale.

Per aggiornare i criteri per consentire all'indicizzatore di accedere alla raccolta documenti, seguire questa procedura:

1. Aprire il portale di Azure e cercare Azure AD **accesso condizionale** e quindi selezionare **Criteri** nel menu a sinistra. Se non si ha accesso per visualizzare questa pagina, è necessario trovare un utente che abbia accesso o ottenere l'accesso.

1. Determinare quali criteri bloccano l'accesso dell'indicizzatore di SharePoint Online alla raccolta documenti. I criteri che potrebbero bloccare l'indicizzatore includeranno l'account utente usato per l'autenticazione durante il passaggio di creazione dell'indicizzatore **nella sezione Utenti e** gruppi. I criteri possono anche avere **condizioni** che:
    * Limitare **le piattaforme Windows.**
    * Limitare **le app per dispositivi mobili e i client desktop**.
    * Impostare **Stato del dispositivo** su **Sì.**

1. Dopo aver confermato che è presente un criterio che blocca l'indicizzatore, è necessario creare un'esenzione per l'indicizzatore. Recuperare l'indirizzo IP del servizio di ricerca.

    1. Ottenere il nome di dominio completo (FQDN) del servizio di ricerca. L'aspetto sarà simile a `<search-service-name>.search.windows.net` . È possibile trovare il nome di dominio completo cercando il servizio di ricerca nella portale di Azure.

   ![Ottenere il nome di dominio completo del servizio](media\search-indexer-howto-secure-access\search-service-portal.png "Ottenere l'FQDN del servizio")

    L'indirizzo IP del servizio di ricerca può essere ottenuto eseguendo `nslookup` un (o `ping` ) dell'FQDN. Nell'esempio seguente si aggiungerà "150.0.0.1" a una regola in ingresso nel firewall Archiviazione di Azure traffico. L'aggiornamento delle impostazioni del firewall per consentire all'indicizzatore del servizio di ricerca di accedere all'account Archiviazione di Azure potrebbe richiedere fino a 15 minuti.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Ottenere gli intervalli di indirizzi IP per l'ambiente di esecuzione dell'indicizzatore per l'area.

    Gli indirizzi IP aggiuntivi vengono usati per le richieste provenienti dall'ambiente di esecuzione [multi-tenant dell'indicizzatore.](search-indexer-securing-resources.md#indexer-execution-environment) È possibile ottenere questo intervallo di indirizzi IP dal tag del servizio.

    Gli intervalli di indirizzi IP per il tag del servizio possono essere ottenuti tramite l'API di individuazione `AzureCognitiveSearch` [(anteprima)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) o il [file JSON scaricabile](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    Per questa procedura dettagliata, supponendo che il servizio di ricerca sia il cloud pubblico di Azure, è necessario scaricare il file JSON pubblico di [Azure.](https://www.microsoft.com/download/details.aspx?id=56519)

   ![Scaricare il file JSON](media\search-indexer-troubleshooting\service-tag.png "Scaricare il file JSON")

    Dal file JSON, supponendo che il servizio di ricerca si trova negli Stati Uniti centro-occidentali, l'elenco di indirizzi IP per l'ambiente di esecuzione dell'indicizzatore multi-tenant è elencato di seguito.

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. Tornare alla pagina Accesso condizionale nel portale di Azure selezionare **Località** denominate dal menu a sinistra, quindi **selezionare + Posizione intervalli IP.** Assegnare un nome al nuovo percorso denominato e aggiungere gli intervalli IP per gli ambienti di esecuzione del servizio di ricerca e dell'indicizzatore raccolti negli ultimi due passaggi.
    * Per l'indirizzo IP del servizio di ricerca potrebbe essere necessario aggiungere "/32" alla fine dell'indirizzo IP perché accetta solo intervalli IP validi.
    * Tenere presente che per gli intervalli IP dell'ambiente di esecuzione dell'indicizzatore è necessario aggiungere solo gli intervalli IP per l'area in cui si trova il servizio di ricerca.

1. Escludere il nuovo percorso denominato dai criteri. 
    1. Selezionare **Criteri** nel menu a sinistra. 
    1. Selezionare i criteri che bloccano l'indicizzatore.
    1. Selezionare **Condizioni**.
    1. Selezionare **Percorsi**.
    1. Selezionare **Escludi** e quindi aggiungere la nuova posizione denominata.
    1. **Salvare** le modifiche.

1. Attendere alcuni minuti per l'aggiornamento e l'applicazione delle nuove regole dei criteri.

1. Provare a creare di nuovo l'indicizzatore
    1. Inviare una richiesta di aggiornamento per l'oggetto origine dati creato.
    1. Inviare nuovamente la richiesta di creazione dell'indicizzatore. Usare il nuovo codice per accedere, quindi inviare un'altra richiesta di creazione dell'indicizzatore dopo l'accesso riuscito.

## <a name="document-processing-errors"></a>Errori di elaborazione dei documenti

### <a name="unprocessable-or-unsupported-documents"></a>Documenti non elaborabili o non supportati

L'indicizzatore BLOB [registra i formati di documento supportati in modo esplicito](search-howto-indexing-azure-blob-storage.md#SupportedFormats). In alcuni casi, un contenitore di archiviazione BLOB contiene documenti non supportati. In altri casi potrebbero essere presenti documenti problematici. È possibile evitare di arrestare l'indicizzatore su questi documenti [modificando le opzioni di configurazione](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Contenuto del documento mancante

L'indicizzatore BLOB [trova ed estrae il testo dai BLOB in un contenitore](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). I problemi relativi all'estrazione del testo includono i seguenti:

* Il documento contiene solo immagini digitalizzate. I BLOB PDF con contenuti non testuali, ad esempio immagini digitalizzate (JPG), non producono risultati in una pipeline di indicizzazione BLOB standard. Se si dispone di contenuto di immagine con elementi di testo, è possibile usare la ricerca [cognitiva](cognitive-search-concept-image-scenarios.md) per trovare ed estrarre il testo.
* L'indicizzatore BLOB è configurato solo per i metadati dell'indice. Per estrarre il contenuto, l'indicizzatore BLOB deve essere configurato per [estrarre sia il contenuto che i metadati](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Errori di indice

### <a name="missing-documents"></a>Documenti mancanti

Gli indicizzatori trovano i documenti da un'[origine dati](/rest/api/searchservice/create-data-source). In alcuni casi un documento dell'origine dati che dovrebbe essere stato indicizzato non è presente in un indice. Esistono alcuni motivi comuni per cui questi errori possono verificarsi:

* Il documento non è stato indicizzato. Cercare nel portale un'esecuzione dell'indicizzatore riuscita.
* Controllare il [valore di rilevamento delle](/rest/api/searchservice/create-data-source#data-change-detection-policies) modifiche. Se il valore limite massimo è una data impostata su un'ora futura, tutti i documenti con una data inferiore a questo verranno ignorati dall'indicizzatore. È possibile comprendere lo stato di rilevamento delle modifiche dell'indicizzatore usando i campi 'initialTrackingState' e 'finalTrackingState' nello [stato dell'indicizzatore.](/rest/api/searchservice/get-indexer-status#indexer-execution-result)
* Il documento è stato aggiornato dopo l'esecuzione dell'indicizzatore. Se l'indicizzatore fa parte di una [pianificazione](/rest/api/searchservice/create-indexer#indexer-schedule), a un certo punto verrà eseguito nuovamente e selezionerà il documento.
* La [query](/rest/api/searchservice/create-data-source) specificata nell'origine dati esclude il documento. Gli indicizzatori non possono indicizzare i documenti che non fanno parte dell'origine dati.
* [I mapping dei campi o](/rest/api/searchservice/create-indexer#fieldmappings) [l'arricchimento dell'intelligenza](./cognitive-search-concept-intro.md) artificiale hanno modificato il documento e l'aspetto è diverso da quello previsto.
* Usare l'[API di ricerca documenti](/rest/api/searchservice/lookup-document) per trovare il documento.