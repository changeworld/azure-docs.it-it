---
title: flusso live con servizi multimediali V3: Descrizione di servizi multimediali di Azure: informazioni su come eseguire lo streaming live con servizi multimediali di Azure V3.
Servizi: Media-Services documentationcenter:'' Author: IngridAtMicrosoft Manager: FEMila Editor:''

ms. Service: Media-Services ms. workload: media ms.tgt_pltfrm: na ms. DevLang: na ms. Topic: esercitazione ms. Custom: "MVC, DevX-Track-CSharp" ms. Date: 06/13/2019 ms. Author: inhenkel

---

# <a name="tutorial-stream-live-with-media-services"></a>Esercitazione: flusso live con servizi multimediali

> [!NOTE]
> Anche se l'esercitazione usa esempi di [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent), i passaggi generali sono gli stessi per [API REST](/rest/api/media/liveevents), [Interfaccia della riga di comando](/cli/azure/ams/live-event) o altri [SDK](media-services-apis-overview.md#sdks) supportati. 

In Servizi multimediali di Azure le entità [Eventi live](/rest/api/media/liveevents) sono responsabili dell'elaborazione dei contenuti in streaming live. Un'entità evento live fornisce un endpoint di input (URL di inserimento) che può essere a sua volta fornito al codificatore live. L'entità evento live riceve flussi di input live dal codificatore live e li rende disponibili per lo streaming con uno o più [Endpoint di streaming](/rest/api/media/streamingendpoints). Gli eventi live forniscono anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito. Questa esercitazione illustra come usare .NET Core per creare un evento live di tipo **pass-through**.

L'esercitazione illustra come:

> [!div class="checklist"]
> * Scaricare l'app di esempio descritta nell'argomento.
> * Esaminare il codice che esegue lo streaming live.
> * Guardare l'evento con [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) all'indirizzo [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Pulire le risorse.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione sono necessari gli elementi seguenti:

- Installare Visual Studio Code o Visual Studio.
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md).<br/>Assicurarsi di copiare i dettagli di accesso all'API in formato JSON o archiviare i valori necessari per connettersi all'account di servizi multimediali nel formato di file con estensione ENV usato in questo esempio.
- Seguire la procedura descritta in [Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure](./access-api-howto.md) e salvare le credenziali. È necessario usarli per accedere all'API in questo esempio o immetterli nel formato di file. env. 
- Una fotocamera o un dispositivo (ad esempio un portatile) usato per trasmettere un evento.
- Un codificatore software locale che codifica il flusso della fotocamera e lo invia al servizio di streaming live di servizi multimediali usando il protocollo RTMP, vedere [codificatori Live locali consigliati](recommended-on-premises-live-encoders.md). Il flusso deve essere in formato **RTMP** oppure **Smooth Streaming**.  
- Per questo esempio, è consigliabile iniziare con un codificatore software, ad esempio [Open Broadcast software OBS Studio](https://obsproject.com/download) , per semplificarne l'introduzione. 

> [!TIP]
> Assicurarsi di leggere [Live streaming with Azure Media Services v3](live-streaming-overview.md) (Streaming live con Servizi multimediali di Azure v3) prima di procedere. 

## <a name="download-and-configure-the-sample"></a>Scaricare e configurare l'esempio

Clonare il repository dell'hub git seguente che contiene l'esempio di .NET streaming live nel computer usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

L'esempio di streaming live è disponibile nella cartella [Live](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live).

Aprire il file [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/appsettings.json) nel progetto scaricato. Sostituire i valori con le credenziali ottenute durante l'[accesso alle API](./access-api-howto.md).

Si noti che è inoltre possibile utilizzare il formato di file con estensione ENV alla radice del progetto per impostare le variabili di ambiente solo una volta per tutti i progetti nel repository di esempi .NET. È sufficiente copiare il file Sample. env, compilare le informazioni ottenute dalla pagina di accesso all'API di servizi multimediali portale di Azure o dall'interfaccia della riga di comando di Azure.  Rinominare il file con estensione ENV di esempio in ". env" per utilizzarlo in tutti i progetti.
Il file con estensione gitignore è già configurato per evitare la pubblicazione del contenuto di questo file nel repository con fork. 

> [!IMPORTANT]
> Questo esempio usa un suffisso univoco per ogni risorsa. Se si annulla il debug o si termina l'app senza eseguirla per intero, per l'account risulteranno disponibili più eventi live. <br/>Assicurarsi di arrestare gli eventi live in esecuzione. In caso contrario, verranno **fatturati**.

## <a name="examine-the-code-that-performs-live-streaming"></a>Esaminare il codice che esegue lo streaming live

In questa sezione vengono esaminate le funzioni definite nel file [Program. cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) del progetto *LiveEventWithDVR* .

L'esempio crea un suffisso univoco per ogni risorsa in modo che non si verifichino conflitti di nomi se si esegue l'esempio più volte senza effettuare la pulizia.


### <a name="start-using-media-services-apis-with-net-sdk"></a>Iniziare a usare le API di Servizi multimediali con .NET SDK

Per iniziare a usare le API di Servizi multimediali con .NET, è necessario creare un oggetto **AzureMediaServicesClient**. Per eseguire questa operazione, specificare le credenziali necessarie per consentire al client di connettersi ad Azure tramite Azure AD. Nel codice clonato all'inizio dell'articolo, la funzione **GetCredentialsAsync** crea l'oggetto ServiceClientCredentials in base alle credenziali fornite nel file di configurazione locale (appsettings.json) o tramite il file delle variabili di ambiente. env che si trova nella radice del repository.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Creare un evento live

Questa sezione mostra come creare un evento live di tipo **pass-through** (LiveEventEncodingType impostato su None). Per altre informazioni sugli altri tipi disponibili di eventi live, vedere [tipi di eventi live](live-events-outputs-concept.md#live-event-types). Oltre al pass-through, è possibile usare un evento live di transcodifica Live per la codifica cloud con velocità in bit adattiva 720P o 1080P. 
 
Alcune caratteristiche che è possibile specificare durante la creazione dell'evento live sono:

* Il protocollo di inserimento per l'evento Live (attualmente, i protocolli RTMP e Smooth Streaming sono supportati).<br/>Non è possibile modificare l'opzione relativa al protocollo durante l'esecuzione dell'evento live o degli output live associati. Se sono necessari protocolli diversi, è necessario creare eventi live separati per ogni protocollo di streaming.  
* Restrizioni IP per l'inserimento e l'anteprima. È possibile definire gli indirizzi IP autorizzati a inserire video in questo evento live. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').<br/>Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.<br/>Gli indirizzi IP devono essere in uno dei formati seguenti: Indirizzo IpV4 con quattro numeri o intervallo di indirizzi CIDR.
* Quando si crea l'evento, è possibile impostarne l'avvio automatico. <br/>Quando l'avvio automatico è impostato su true, l'evento live verrà avviato dopo la creazione. Ciò significa che la fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).
Sono disponibili anche modalità standby per avviare l'evento live con uno stato "allocato" del costo inferiore che rende più veloce lo spostamento allo stato "in esecuzione". Questa operazione è utile per situazioni come hotpools che devono distribuire rapidamente i canali ai flussi.
* Affinché un URL di inserimento sia predittivo e più semplice da gestire in un codificatore Live basato su hardware, impostare la proprietà "useStaticHostname" su true. Per informazioni dettagliate, vedere [URL di inserimento di eventi live](live-events-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Ottenere gli URL di inserimento

Al termine della creazione dell'evento live, è possibile ottenere gli URL di inserimento che verranno passati al codificatore live. Questi URL vengono usati dal codificatore per inserire un flusso live.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Ottenere l'URL di anteprima

Usare previewEndpoint per visualizzare in anteprima e verificare che l'input dal codificatore venga effettivamente ricevuto.

> [!IMPORTANT]
> Assicurarsi che il video raggiunga l'URL di anteprima prima di continuare.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Creare e gestire eventi live e output live

Dopo l'avvio del flusso nell'evento live, è possibile iniziare l'evento di streaming creando un asset, un output live e un localizzatore di streaming. In questo modo il flusso viene archiviato e reso disponibile agli utenti tramite l'endpoint di streaming.

Quando si apprendono questi concetti, è preferibile considerare l'oggetto "asset" come il nastro da inserire in un registratore video nei giorni precedenti. "Output Live" è il computer registratore di nastri. "Evento live" è solo il segnale video che si trova nella parte posteriore del computer.

Per prima cosa creare il segnale creando il "evento live".  Il segnale non viene propagato fino a quando non si avvia l'evento Live e si connette il codificatore all'input.

Il nastro può essere creato in qualsiasi momento. Si tratta semplicemente di una "risorsa" vuota che verrà passata all'oggetto di output attivo, ovvero il registratore del nastro in questa analogia.

Il registratore di nastri può essere creato in qualsiasi momento. Il che significa che è possibile creare un output Live prima di avviare il flusso del segnale o dopo. Se è necessario velocizzare le operazioni, è talvolta utile crearlo prima di avviare il flusso del segnale.

Per arrestare il registratore del nastro, chiamare delete in LiveOutput. Il contenuto non viene eliminato dal "asset" del nastro.  L'asset viene sempre mantenuto con il contenuto video archiviato fino a quando non si chiama Delete in modo esplicito nell'asset.

Nella sezione successiva verrà illustrata la creazione dell'asset ("Tape") e l'output Live ("registratore di nastri").

#### <a name="create-an-asset"></a>Creare un asset

Creare un asset utilizzabile dall'output live. Nell'analogia precedente, questo sarà il nastro in cui viene registrato il segnale video live. I visualizzatori saranno in grado di visualizzare il contenuto in tempo reale o su richiesta da questo nastro virtuale.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Creare un output live

Gli output live iniziano al momento della creazione e terminano quando vengono eliminati. Questo sarà il "registratore di nastri" per l'evento. Quando si elimina l'output Live, non viene eliminato l'asset o il contenuto sottostante nell'asset. Considerarlo come l'espulsione del nastro. L'asset con la registrazione durerà fino a quando si desidera e quando viene espulso (ovvero, quando l'output Live viene eliminato) sarà disponibile per la visualizzazione su richiesta immediatamente. 

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Creare un localizzatore di streaming

> [!NOTE]
> Quando viene creato l'account Servizi multimediali, all'account viene aggiunto un endpoint di streaming **predefinito** nello stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione [dinamica dei pacchetti](dynamic-packaging-overview.md) e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato in **esecuzione** .

Quando si pubblica l'asset usando un localizzatore di streaming, l'evento Live (fino alla lunghezza della finestra DVR) continuerà a essere visualizzabile fino alla scadenza o all'eliminazione del localizzatore di streaming, a seconda del valore che viene raggiunto per primo. Questo è il modo in cui è possibile rendere disponibile la registrazione "Tape" virtuale per visualizzare i destinatari in diretta e su richiesta. Lo stesso URL può essere usato per controllare l'evento Live, la finestra DVR o l'asset su richiesta al termine della registrazione (quando viene eliminato l'output Live).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Pulizia delle risorse nell'account di Servizi multimediali

Se al termine dello streaming degli eventi si vuole pulire le risorse di cui in precedenza è stato effettuato il provisioning, seguire questa procedura:

* Interrompere il push del flusso dal codificatore.
* Arrestare l'evento live. Dopo l'arresto, l'evento live non è soggetto ad alcun addebito. Quando occorrerà riavviarlo, avrà lo stesso URL di inserimento, per cui non sarà necessario riconfigurare il codificatore.
* È possibile arrestare l'endpoint di streaming, a meno che non si voglia continuare a fornire l'archivio dell'evento live come flusso su richiesta. Se l'evento live si trova nello stato Arrestato, non è soggetto ad alcun addebito.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Guardare l'evento

Per guardare l'evento, copiare l'URL di streaming ottenuto durante l'esecuzione del codice descritto in Creare un localizzatore di streaming. È possibile usare il lettore multimediale desiderato. È possibile usare [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) per testare il flusso all'indirizzo https://ampdemo.azureedge.net.

Quando viene arrestato, l'evento live converte automaticamente gli eventi in contenuto su richiesta. Anche dopo l'arresto e l'eliminazione dell'evento, gli utenti potranno riprodurre in streaming il contenuto archiviato sotto forma di video on demand, fintanto che l'asset non viene eliminato. Un asset non può essere eliminato se è usato da un evento. È prima necessario eliminare l'evento.

## <a name="clean-up-resources"></a>Pulire le risorse

Se nessuna delle risorse usate è più necessaria, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse creato in precedenza.

Eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Lasciare l'evento live in esecuzione comporta costi di fatturazione. Tenere presente, in caso di arresto anomalo o chiusura del programma/progetto, che l'evento live potrebbe rimanere in esecuzione in stato di fatturazione.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Streaming di file](stream-files-tutorial-with-api.md)
 
