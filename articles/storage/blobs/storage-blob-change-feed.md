---
title: Feed di modifiche in Archiviazione BLOB di Azure | Microsoft Docs
description: Informazioni sui log dei feed di modifiche Archiviazione BLOB di Azure e su come usarli.
author: normesta
ms.author: normesta
ms.date: 02/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 6da83ceb6d8ee51916d25949309d7ddfba0e4b30
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503602"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Supporto del feed di modifiche in Archiviazione BLOB di Azure

Lo scopo del feed di modifiche è fornire i log delle transazioni di tutte le modifiche apportate ai BLOB e ai metadati BLOB nell'account di archiviazione. Il feed di modifiche **fornisce un** **log** ordinato, **garantito,** **durevole,** **non modificabile** e di sola lettura di queste modifiche. Le applicazioni client possono leggere questi log in qualsiasi momento, in streaming o in modalità batch. Il feed di modifiche consente di creare soluzioni efficienti e scalabili che elaborano eventi di modifica che si verificano nell'account di archiviazione BLOB a un costo contenuto.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-the-change-feed-works"></a>Funzionamento del feed di modifiche

Il feed di modifiche viene archiviato [come BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) in un contenitore speciale nell'account di archiviazione al costo standard dei prezzi [dei BLOB.](https://azure.microsoft.com/pricing/details/storage/blobs/) È possibile controllare il periodo di conservazione di questi file in base ai requisiti (vedere le [condizioni](#conditions) della versione corrente). Gli eventi di modifica vengono aggiunti al feed di modifiche come record nella specifica di formato [Apache Avro:](https://avro.apache.org/docs/1.8.2/spec.html) un formato binario compatto, veloce che fornisce strutture di dati dettagliate con schema inline. Questo formato è largamente usato nell'ecosistema Hadoop, dall'analisi di flusso e da Azure Data Factory.

È possibile elaborare questi log in modo asincrono, incrementale o completo. Qualsiasi numero di applicazioni client può leggere in modo indipendente il feed di modifiche, in parallelo e al proprio ritmo. Le applicazioni di analisi come [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) o [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) possono utilizzare i log direttamente come file Avro, che consentono di elaborarli a basso costo, con larghezza di banda elevata e senza dover scrivere un'applicazione personalizzata.

Il diagramma seguente illustra come vengono aggiunti i record al feed di modifiche:

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="Diagramma che illustra il funzionamento del feed di modifiche per fornire un log ordinato delle modifiche ai BLOB":::

Il supporto dei feed di modifiche è particolarmente adatto per gli scenari che elaborano i dati in base agli oggetti modificati. Ad esempio, le applicazioni possono:

  - Aggiornare un indice secondario ed eseguire la sincronizzazione con una cache, un motore di ricerca o qualsiasi altro scenario di gestione dei contenuti.
  
  - Estrarre informazioni dettagliate e metriche sulle analisi di business basate sulle modifiche apportate agli oggetti in modalità flusso o batch.
  
  - Archiviare, controllare e analizzare le modifiche apportate agli oggetti, in qualsiasi intervallo di tempo, per la gestione dei dati sulla sicurezza, la conformità o l'intelligenza a livello aziendale.

  - Creare soluzioni per il backup, il mirroring o la replica dello stato dell'oggetto nell'account per la gestione o la conformità di emergenza.

  - Compilare pipeline di applicazioni connesse che reagiscono agli eventi di modifica o pianificano le esecuzioni in base all'oggetto creato o modificato.
  
Il feed di modifiche è una funzionalità prerequisita [per](object-replication-overview.md) la replica di oggetti e il ripristino [temporato per i BLOB in blocchi.](point-in-time-restore-overview.md)

> [!NOTE]
> Il feed di modifiche fornisce un modello di log durevole e ordinato delle modifiche apportate a un BLOB. Le modifiche vengono scritte e rese disponibili nel log dei feed di modifiche in un ordine di pochi minuti dalla modifica. Se l'applicazione deve reagire agli eventi molto più rapidamente, è consigliabile usare invece gli [eventi di archiviazione BLOB.](storage-blob-event-overview.md) [Gli eventi di archiviazione BLOB](storage-blob-event-overview.md) forniscono eventi una sola volta in tempo reale che consentono al Funzioni di Azure o alle applicazioni di reagire rapidamente alle modifiche che si verificano in un BLOB. 

## <a name="enable-and-disable-the-change-feed"></a>Abilitare e disabilitare il feed di modifiche

È necessario abilitare il feed di modifiche nell'account di archiviazione per iniziare ad acquisire e registrare le modifiche. Disabilitare il feed di modifiche per arrestare l'acquisizione delle modifiche. È possibile abilitare e disabilitare le modifiche usando Azure Resource Manager modelli nel portale o in PowerShell.

Ecco alcuni aspetti da tenere presenti quando si abilita il feed di modifiche.

- Esiste un solo feed di modifiche per il servizio BLOB in ogni account di archiviazione e viene archiviato nel contenitore **$blobchangefeed** dati.

- Le modifiche di creazione, aggiornamento ed eliminazione vengono acquisite solo a livello di servizio BLOB.

- Il feed di modifiche *acquisisce tutte* le modifiche per tutti gli eventi disponibili che si verificano nell'account. Le applicazioni client possono filtrare i tipi di evento in base alle esigenze. Vedere le [condizioni](#conditions) della versione corrente.

- Solo gli account di archiviazione BLOB e GPv2 possono abilitare il feed di modifiche. Gli account BlockBlobStorage Premium e gli account abilitati per lo spazio dei nomi gerarchico non sono attualmente supportati. Gli account di archiviazione GPv1 non sono supportati, ma possono essere aggiornati a GPv2 senza tempi di inattività. Per altre informazioni, vedere Eseguire l'aggiornamento a un account di archiviazione [GPv2.](../common/storage-account-upgrade.md)

### <a name="portal"></a>[Portale](#tab/azure-portal)

Abilitare il feed di modifiche nell'account di archiviazione usando portale di Azure:

1. Selezionare l'account di archiviazione nel [portale di Azure](https://portal.azure.com/).
1. Passare **all'opzione Protezione dati** in **Gestione dati**.
1. In **Rilevamento** selezionare **Abilita feed di modifiche BLOB.**
1. Scegliere il **pulsante Salva** per confermare le impostazioni di protezione dei dati.

    :::image type="content" source="media/storage-blob-change-feed/change-feed-enable-portal.png" alt-text="Screenshot che mostra come abilitare il feed di modifiche in portale di Azure":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Abilitare il feed di modifiche tramite PowerShell:

1. Installare la versione più recente di PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Chiudere e riaprire la console di PowerShell.

3. Installare la versione 2.5.0 o successiva del **modulo Az.Storage.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

   ```powershell
   Connect-AzAccount
   ```

5. Abilitare il feed di modifiche per l'account di archiviazione.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Modello](#tab/template)
Usare un modello Azure Resource Manager per abilitare il feed di modifiche nell'account di archiviazione esistente tramite portale di Azure:

1. Nella finestra portale di Azure scegliere **Crea una risorsa.**

2. In **Cerca nel Marketplace** digitare distribuzione del **modello** e quindi premere **INVIO.**

3. Scegliere **[Deploy a custom template (Distribuisci un](https://portal.azure.com/#create/Microsoft.Template)** modello personalizzato), quindi scegliere Build your own template **(Compila modello personalizzato) nell'editor.**

4. Nell'editor dei modelli incollare il codice JSON seguente. Sostituire il segnaposto `<accountName>` con il nome del proprio account di archiviazione.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Scegliere il **pulsante Salva,** specificare il gruppo di  risorse dell'account e quindi scegliere il pulsante Acquista per distribuire il modello e abilitare il feed di modifiche.

---

## <a name="consume-the-change-feed"></a>Utilizzare il feed di modifiche

Il feed di modifiche produce diversi metadati e file di log. Questi file si trovano nel contenitore **$blobchangefeed** dell'account di archiviazione. 

> [!NOTE]
> Nella versione corrente il contenitore $blobchangefeed è visibile solo in portale di Azure ma non in Azure Storage Explorer. Attualmente non è possibile visualizzare il contenitore $blobchangefeed quando si chiama l'API ListContainers, ma è possibile chiamare l'API ListBlobs direttamente nel contenitore per visualizzare i BLOB

Le applicazioni client possono utilizzare il feed di modifiche usando la libreria del processore dei feed di modifiche BLOB fornita con l'SDK del processore dei feed di modifiche. 

Vedere [Elaborare i log dei feed](storage-blob-change-feed-how-to.md)di modifiche in Archiviazione BLOB di Azure .

## <a name="understand-change-feed-organization"></a>Comprendere l'organizzazione del feed di modifiche

<a id="segment-index"></a>

### <a name="segments"></a>Segmenti

Il feed di modifiche è un log di modifiche organizzate **in** segmenti *orari,* ma aggiunte a e aggiornate ogni pochi minuti. Questi segmenti vengono creati solo quando sono presenti eventi di modifica del BLOB che si verificano in quell'ora. In questo modo l'applicazione client può utilizzare le modifiche che si verificano entro intervalli di tempo specifici senza dover eseguire ricerche nell'intero log. Per altre informazioni, vedere [Specifiche](#specifications).

Un segmento orario disponibile del feed di modifiche viene descritto in un file manifesto che specifica i percorsi dei file del feed di modifiche per tale segmento. L'elenco della `$blobchangefeed/idx/segments/` directory virtuale mostra questi segmenti ordinati in base al tempo. Il percorso del segmento descrive l'inizio dell'intervallo di tempo orario rappresentato dal segmento. È possibile usare tale elenco per filtrare i segmenti di log di interesse.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json`L'oggetto viene creato automaticamente quando si abilita il feed di modifiche. È possibile ignorare questo file. Si tratta di un file di inizializzazione sempre vuoto. 

Il file manifesto del segmento ( ) mostra il percorso dei file del feed di `meta.json` modifiche per tale segmento nella proprietà `chunkFilePaths` . Di seguito è riportato un esempio di un file manifesto del segmento.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> Il contenitore viene visualizzato solo dopo aver abilitato `$blobchangefeed` la funzionalità del feed di modifiche nell'account. È necessario attendere alcuni minuti dopo aver abilitato il feed di modifiche prima di poter elencare i BLOB nel contenitore. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Modificare i record degli eventi

I file del feed di modifiche contengono una serie di record di eventi di modifica. Ogni record dell'evento di modifica corrisponde a una modifica a un singolo BLOB. I record vengono serializzati e scritti nel file usando la specifica [di formato Apache Avro.](https://avro.apache.org/docs/1.8.2/spec.html) I record possono essere letti usando la specifica del formato di file Avro. Sono disponibili diverse librerie per elaborare i file in tale formato.

I file del feed di modifiche vengono archiviati `$blobchangefeed/log/` nella directory virtuale come BLOB di [accodamento.](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) Il primo file del feed di modifiche in ogni percorso avrà `00000` nel nome file (ad esempio `00000.avro` ). Il nome di ogni file di log successivo aggiunto a tale percorso incrementerà di 1 (ad esempio: `00001.avro` ).

I tipi di evento seguenti vengono acquisiti nei record del feed di modifiche:
- BLOBCreato
- BlobDeleted
- BlobPropertiesUpdated
- BLOBSnapshotCreated

Ecco un esempio di record dell'evento di modifica dal file del feed di modifiche convertito in Json.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Per una descrizione di ogni proprietà, vedere schema [Griglia di eventi di Azure eventi per l'archiviazione BLOB.](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties) Gli eventi BlobPropertiesUpdated e BlobSnapshotCreated sono attualmente esclusivi del feed di modifiche e non sono ancora supportati per gli eventi di archiviazione BLOB.

> [!NOTE]
> I file del feed di modifiche per un segmento non vengono visualizzati immediatamente dopo la creazione di un segmento. La durata del ritardo rientra nell'intervallo normale di latenza di pubblicazione del feed di modifiche che si trova entro pochi minuti dalla modifica.

<a id="specifications"></a>

## <a name="specifications"></a>Specifiche

- I record degli eventi di modifica vengono aggiunti solo al feed di modifiche. Dopo l'aggiunta, questi record non sono modificabili e la posizione dei record è stabile. Le applicazioni client possono mantenere il proprio checkpoint nella posizione di lettura del feed di modifiche.

- I record degli eventi di modifica vengono aggiunti entro pochi minuti dalla modifica. Le applicazioni client possono scegliere di utilizzare i record quando vengono aggiunti per l'accesso in streaming o in blocco in qualsiasi altro momento.

- I record degli eventi di modifica vengono ordinati in base all'ordine **di modifica per BLOB.** L'ordine delle modifiche nei BLOB non è definito in Archiviazione BLOB di Azure. Tutte le modifiche in un segmento precedente sono prima di qualsiasi modifica nei segmenti successivi.

- I record degli eventi di modifica vengono serializzati nel file di log usando la specifica di formato [Apache Avro 1.8.2.](https://avro.apache.org/docs/1.8.2/spec.html)

- I record degli eventi di modifica in cui il valore di è sono record di sistema interni e non riflettono una modifica `eventType` `Control` agli oggetti nell'account. È possibile ignorare questi record in tutta sicurezza.

- I valori nel `storageDiagnostics` contenitore delle proprietà sono solo per uso interno e non sono progettati per l'uso da parte dell'applicazione. Le applicazioni non devono avere una dipendenza contrattuale da tale dati. È possibile ignorare queste proprietà.

- L'ora rappresentata dal segmento è **approssimativa** con limiti di 15 minuti. Pertanto, per garantire l'utilizzo di tutti i record entro un periodo di tempo specificato, utilizzare il segmento di ora precedente e successivo consecutivo.

- Ogni segmento può avere un numero diverso di a causa del partizionamento interno del flusso `chunkFilePaths` di log per gestire la velocità effettiva di pubblicazione. I file di log in ognuno di essi sono garantiti per contenere BLOB che si escludono a vicenda e possono essere utilizzati ed elaborati in parallelo senza violare l'ordine delle modifiche per OGNI BLOB durante `chunkFilePath` l'iterazione.

- I segmenti iniziano con `Publishing` lo stato . Al termine dell'aggiunta dei record al segmento, sarà `Finalized` . I file di log in qualsiasi segmento datato dopo la data della proprietà nel file non devono essere `LastConsumable` `$blobchangefeed/meta/Segments.json` utilizzati dall'applicazione. Ecco un esempio della proprietà `LastConsumable` in un `$blobchangefeed/meta/Segments.json` file:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>Condizioni e problemi noti

Questa sezione descrive i problemi e le condizioni noti nella versione corrente del feed di modifiche. 

- I record degli eventi di modifica per qualsiasi singola modifica potrebbero essere visualizzati più di una volta nel feed di modifiche.
- Non è ancora possibile gestire la durata dei file di log del feed di modifiche impostando criteri di conservazione basati sul tempo e non è possibile eliminare i BLOB.
- La `url` proprietà del file di log è attualmente sempre vuota.
- La proprietà dell'segments.jsnel file non elenca il primo segmento finalizzato `LastConsumable` dal feed di modifiche. Questo problema si verifica solo dopo la finalizzazione del primo segmento. Tutti i segmenti successivi dopo la prima ora vengono acquisiti in modo accurato nella `LastConsumable` proprietà .
- Attualmente non è possibile visualizzare il **contenitore $blobchangefeed** quando si chiama l'API ListContainers e il contenitore non viene visualizzato portale di Azure o Storage Explorer. È possibile visualizzare il contenuto chiamando direttamente l'API ListBlobs $blobchangefeed contenitore.
- Gli account di archiviazione che hanno avviato in precedenza un [failover dell'account](../common/storage-disaster-recovery-guidance.md) possono avere problemi con il file di log non visualizzato. Anche eventuali failover di account futuri potrebbero influire sul file di log.

## <a name="faq"></a>Domande frequenti

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Qual è la differenza tra il feed di modifiche e Analisi archiviazione registrazione?
I log di analisi hanno record di tutte le operazioni di lettura, scrittura, elenco ed eliminazione con richieste riuscite e non riuscite in tutte le operazioni. I log di analisi sono il massimo sforzo e non è garantito alcun ordinamento.

Il feed di modifiche è una soluzione che fornisce il log transazionale delle mutazioni o delle modifiche riuscite all'account, ad esempio la creazione, la modifica e le eliminazioni di BLOB. Il feed di modifiche garantisce che tutti gli eventi siano registrati e visualizzati nell'ordine delle modifiche riuscite per OGNI BLOB, pertanto non è necessario filtrare il rumore da un volume elevato di operazioni di lettura o richieste non riuscite. Il feed di modifiche è fondamentalmente progettato e ottimizzato per lo sviluppo di applicazioni che richiedono determinate garanzie.

### <a name="should-i-use-change-feed-or-storage-events"></a>È necessario usare il feed di modifiche o gli eventi di archiviazione?
È possibile sfruttare entrambe le funzionalità poiché gli eventi del feed di modifiche e dell'archiviazione [BLOB](storage-blob-event-overview.md) forniscono le stesse informazioni con la stessa garanzia di affidabilità del recapito, con la differenza principale che è la latenza, l'ordinamento e l'archiviazione dei record degli eventi. Il feed di modifiche pubblica i record nel log entro pochi minuti dalla modifica e garantisce anche l'ordine delle operazioni di modifica per OGNI BLOB. Gli eventi di archiviazione vengono inseriti in tempo reale e potrebbero non essere ordinati. Gli eventi del feed di modifiche vengono archiviati in modo durevole all'interno dell'account di archiviazione come log stabili di sola lettura con una conservazione definita, mentre gli eventi di archiviazione sono temporanei per essere utilizzati dal gestore eventi, a meno che non vengano archiviati in modo esplicito. Con il feed di modifiche, qualsiasi numero di applicazioni può usare i log in modo comodo usando API BLOB o SDK. 

## <a name="next-steps"></a>Passaggi successivi

- Vedere un esempio di come leggere il feed di modifiche usando un'applicazione client .NET. Vedere [Elaborare i log dei feed](storage-blob-change-feed-how-to.md)di modifiche in Archiviazione BLOB di Azure .
- Informazioni su come reagire agli eventi in tempo reale. Vedere [Reacting to BLOB Storage events (Reazione agli eventi di archiviazione BLOB)](storage-blob-event-overview.md)
- Altre informazioni sulla registrazione dettagliata per le operazioni riuscite e non riuscite per tutte le richieste. Vedere [registrazione Archiviazione di Azure di analisi dei dati](../common/storage-analytics-logging.md)
