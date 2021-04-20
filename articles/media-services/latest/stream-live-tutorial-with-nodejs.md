---
title: Streaming live con Servizi multimediali v3 Node.js
titleSuffix: Azure Media Services
description: Informazioni su come trasmettere in streaming live usando Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730539"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>Esercitazione: Trasmettere in streaming live con Servizi multimediali usando Node.js e TypeScript

> [!NOTE]
> Anche se l'esercitazione usa Node.js esempi, i passaggi generali [](/cli/azure/ams/live-event)sono gli stessi per l'API [REST,](/rest/api/media/liveevents)l'interfaccia della riga di comando o altri [SDK supportati.](media-services-apis-overview.md#sdks) 

In Servizi multimediali di Azure le entità [Eventi live](/rest/api/media/liveevents) sono responsabili dell'elaborazione dei contenuti in streaming live. Un'entità evento live fornisce un endpoint di input (URL di inserimento) che può essere a sua volta fornito al codificatore live. L'entità evento live riceve flussi di input live dal codificatore live e li rende disponibili per lo streaming con uno o più [Endpoint di streaming](/rest/api/media/streamingendpoints). Gli eventi live forniscono anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito. Questa esercitazione illustra come usare Node.js per creare un tipo **pass-through** di un evento live e trasmettere un flusso live ad esso usando [OBS Studio.](https://obsproject.com/download)

L'esercitazione illustra come:

> [!div class="checklist"]
> * Scaricare il codice di esempio descritto nell'argomento .
> * Esaminare il codice che configura ed esegue lo streaming live.
> * Guardare l'evento con [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) all'indirizzo [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Pulire le risorse.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione sono necessari gli elementi seguenti:

- Installare [Node.js](https://nodejs.org/en/download/)
- Installare [TypeScript](https://www.typescriptlang.org/)
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md).<br/>Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.
- Seguire la procedura descritta in [Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure](./access-api-howto.md) e salvare le credenziali. È necessario usarli per accedere all'API e configurare il file delle variabili di ambiente.
- Per informazioni su [come usare l'SDK ](./configure-connect-nodejs-howto.md) Node.js client Node.jsprocedura per configurare e connettersi Node.js
- Installare Visual Studio Code o Visual Studio.
- [Configurare l'Visual Studio Code per](https://code.visualstudio.com/Docs/languages/typescript) supportare il linguaggio TypeScript.

## <a name="additional-settings-for-live-streaming-software"></a>Impostazioni aggiuntive per il software di streaming live

- Una fotocamera o un dispositivo (ad esempio un portatile) usato per trasmettere un evento.
- Un codificatore software locale che codifica il flusso della fotocamera e lo invia al servizio di streaming live di Servizi multimediali usando il protocollo RTMP. Vedere codificatori live [consigliati in locale.](encode-recommended-on-premises-live-encoders.md) Il flusso deve essere in formato **RTMP** oppure **Smooth Streaming**.  
- Per questo esempio, è consigliabile iniziare con un codificatore software come [Open Broadcast Software OBS Studio](https://obsproject.com/download) gratuito per semplificare l'avvio.

Questo esempio presuppone che si userà OBS Studio per trasmettere RTMP all'endpoint di inserimento. Installare prima OBS Studio.
Usare le impostazioni di codifica seguenti in OBS Studio:

- Codificatore: NVIDIA NVENC (se disponibile) o x264
- Controllo della frequenza: CBR
- Velocità in bit: 2500 Kbps (o un valore ragionevole per il portatile)
- Intervallo fotogrammi chiave: 2 s o 1 s per bassa latenza  
- Set di impostazioni: Qualità o prestazioni a bassa latenza (NVENC) o "moltofast" con x264
- Profilo: alto
- GPU: 0 (auto)
- Numero massimo di fotogrammi B: 2

> [!TIP]
> Assicurarsi di leggere [Live streaming with Azure Media Services v3](stream-live-streaming-concept.md) (Streaming live con Servizi multimediali di Azure v3) prima di procedere.

## <a name="download-and-configure-the-sample"></a>Scaricare e configurare l'esempio

Clonare nel computer il repository Git Hub seguente che contiene l'esempio Node.js streaming live usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

L'esempio di streaming live è disponibile nella cartella [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live).

Nella cartella [AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) copiare il file denominato "sample.env" in un nuovo file denominato ".env" per archiviare le impostazioni delle variabili di ambiente raccolte nell'articolo Accedere [all'API Servizi multimediali di Azure](./access-api-howto.md)con l'interfaccia della riga di comando di Azure.
Assicurarsi che il file includa il "punto" (.) davanti a .env perché funzioni correttamente con l'esempio di codice.

Il [file con estensione env](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) contiene la chiave e il segreto dell'applicazione AAD insieme al nome dell'account e alle informazioni sulla sottoscrizione necessarie per autenticare l'accesso dell'SDK all'account di Servizi multimediali. Il file con estensione gitignore è già configurato per impedire la pubblicazione del file nel repository con fork. Non consentire la perdita di queste credenziali perché sono segreti importanti per l'account.

> [!IMPORTANT]
> Questo esempio usa un suffisso univoco per ogni risorsa. Se si annulla il debug o si termina l'app senza eseguirla per intero, per l'account risulteranno disponibili più eventi live. <br/>Assicurarsi di arrestare gli eventi live in esecuzione. In caso contrario, verranno **fatturati**. Eseguire il programma fino al completamento per pulire automaticamente le risorse. Se il programma si arresta in modo anomalo o si arresta inavvertitamente il debugger e si interrompe l'esecuzione del programma, è necessario controllare il portale per verificare che non sia stato lasciato alcun evento live negli stati In esecuzione o Stand-by che comporta costi di fatturazione indesiderati.

## <a name="examine-the-typescript-code-for-live-streaming"></a>Esaminare il codice TypeScript per lo streaming live

Questa sezione esamina le funzioni definite nel file [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) del *progetto* Live.

L'esempio crea un suffisso univoco per ogni risorsa in modo che non si verifichino conflitti di nomi se si esegue l'esempio più volte senza effettuare la pulizia.

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>Iniziare a usare Media Services SDK per Node.js con TypeScript

Per iniziare a usare le API di Servizi multimediali con Node.js, è necessario prima aggiungere il modulo SDK usando gestione [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) pacchetti npm

```bash
npm install @azure/arm-mediaservices
```

Nel package.jsè già configurato automaticamente, quindi è sufficiente eseguire *npm install* per caricare i moduli e le dipendenze.

1. Aprire un **prompt dei comandi** e passare alla directory dell'esempio.
1. Passare alla directory nella cartella AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Installare i pacchetti usati nelpackages.js *file.*

    ```bash
    npm install 
    ```

1. Avviare Visual Studio Code dalla *cartella AMSv3Samples.* Questa operazione è necessaria per l'avvio dalla cartella in cui si trovano la cartella *vscode* *etsconfig.jsfile.*

    ```bash
    cd ..
    code .
    ```

Aprire la cartella *per Live* e aprire il file *index.ts* nell'editor Visual Studio Code.
Nel file *index.ts* premere F5 per avviare il debugger.

### <a name="create-the-media-services-client"></a>Creare il client di Servizi multimediali

Il frammento di codice seguente illustra come creare il client di Servizi multimediali in Node.js.
Si noti che in questo codice viene innanzitutto impostata la proprietà **longRunningOperationRetryTimeout** di AzureMediaServicesOptions su 2 secondi per ridurre il tempo necessario per eseguire il polling dello stato di un'operazione a esecuzione lunga nell'endpoint di Gestione risorse di Azure.  Poiché la maggior parte delle operazioni sugli eventi live sarà asincrona e potrebbe richiedere del tempo, è consigliabile ridurre questo intervallo di polling nell'SDK dal valore predefinito di 30 secondi per velocizzare il tempo necessario per completare le operazioni principali, ad esempio la creazione di eventi live, l'avvio e l'arresto di tutte le chiamate asincrone. Due secondi è il valore consigliato per la maggior parte degli scenari di casi d'uso.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Creare un evento live

Questa sezione mostra come creare un evento live di tipo **pass-through** (LiveEventEncodingType impostato su None). Per altre informazioni sugli altri tipi disponibili di eventi live, vedere [Tipi di eventi live](live-event-outputs-concept.md#live-event-types). Oltre al pass-through, è possibile usare un evento live di transcoding live per la codifica cloud a velocità in bit adattiva 720P o 1080P.
 
Alcune caratteristiche che è possibile specificare durante la creazione dell'evento live sono:

* Il protocollo di inserimento per l'evento live (attualmente sono supportati i protocolli RTMP(S) e Smooth Streaming.<br/>Non è possibile modificare l'opzione relativa al protocollo durante l'esecuzione dell'evento live o degli output live associati. Se sono necessari protocolli diversi, è necessario creare eventi live separati per ogni protocollo di streaming.  
* Restrizioni IP per l'inserimento e l'anteprima. È possibile definire gli indirizzi IP autorizzati a inserire video in questo evento live. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').<br/>Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.<br/>Gli indirizzi IP devono essere in uno dei formati seguenti: indirizzo IpV4 con quattro numeri o intervallo di indirizzi CIDR.
* Quando si crea l'evento, è possibile impostarne l'avvio automatico. <br/>Quando l'avvio automatico è impostato su true, l'evento live verrà avviato dopo la creazione. Ciò significa che la fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing-concept.md).
Sono disponibili anche modalità standby per avviare l'evento live con uno stato "allocato" a un costo inferiore che rende più veloce il passaggio allo stato "In esecuzione". Ciò è utile per situazioni come i pool ad accesso rapido che devono passare rapidamente i canali agli streamer.
* Per un URL di inserimento predittivo e più facile da gestire in un codificatore live basato su hardware, impostare la proprietà "useStaticHostname" su true, nonché usare un GUID univoco personalizzato in "accessToken". Per informazioni dettagliate, vedere [URL di inserimento di eventi live](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Creare un asset per registrare e archiviare l'evento live

In questo blocco di codice si creerà un asset vuoto da usare come "nastro" in cui registrare l'archivio eventi live.
Quando si apprendono questi concetti, è meglio pensare all'oggetto "Asset" come al nastro da inserire in un registratore di nastri video ai vecchi tempi. L'"output live" è il registratore di nastri. L'evento live è solo il segnale video in arrivo nella parte posteriore del computer.

Tenere presente che l'asset, o "nastro", può essere creato in qualsiasi momento. Si tratta semplicemente di un "asset" vuoto che verrà mano all'oggetto Output live, il registratore di nastri in questa analogia.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>Creare l'output live

In questa sezione viene creato un output live che usa il nome dell'asset come input per indicare dove registrare l'evento live. È stata anche impostata la finestra di spostamento temporale (DVR) da usare nella registrazione.
Il codice di esempio mostra come configurare una finestra di spostamento temporale di 1 ora. In questo modo i client potranno riprodurre in qualsiasi punto dell'ultima ora dell'evento.  Inoltre, solo l'ultima ora dell'evento live rimarrà nell'archivio. Se necessario, è possibile estenderlo fino a 25 ore.  Si noti anche che è possibile controllare la denominazione del manifesto di output usata dai manifesti HLS e DASH nei percorsi URL al momento della pubblicazione.

L'output live, o "registratore di nastri" nell'analogia, può essere creato anche in qualsiasi momento. Ciò significa che è possibile creare un output live prima di avviare il flusso del segnale o dopo. Se è necessario velocizzare le operazioni, è spesso utile crearlo prima di avviare il flusso del segnale.

Gli output live iniziano al momento della creazione e terminano quando vengono eliminati.  Quando si elimina l'output live, non si elimina l'asset o il contenuto sottostante nell'asset. Si pensi all'espulso del nastro. L'asset con la registrazione durerà fino a quando si desidera e quando viene espulso (ovvero quando l'output live viene eliminato) sarà immediatamente disponibile per la visualizzazione su richiesta.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>Ottenere gli URL di inserimento

Al termine della creazione dell'evento live, è possibile ottenere gli URL di inserimento che verranno passati al codificatore live. Il codificatore usa questi URL per immettere un flusso live usando il protocollo RTMP

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>Ottenere l'URL di anteprima

Usare previewEndpoint per visualizzare in anteprima e verificare che l'input dal codificatore venga effettivamente ricevuto.

> [!IMPORTANT]
> Assicurarsi che il video raggiunga l'URL di anteprima prima di continuare.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Creare e gestire eventi live e output live

Dopo aver inserito il flusso nell'evento live, è possibile iniziare l'evento di streaming pubblicando un localizzatore di streaming da usare per i lettori client. In questo modo sarà disponibile per i visualizzatori tramite l'endpoint di streaming.

Creare prima il segnale creando l'evento live.  Il segnale non scorre fino a quando non si avvia l'evento live e si connette il codificatore all'input.

Per arrestare il "registratore di nastri", chiamare delete in LiveOutput. Questo non elimina effettivamente **il** contenuto dell'archivio sul nastro "Asset", ma elimina solo il "registratore di nastri" e arresta l'archiviazione. L'asset viene sempre mantenuto con il contenuto video archiviato fino a quando non si chiama delete in modo esplicito sull'asset stesso. Non appena si elimina liveOutput, il contenuto registrato dell'"Asset" è ancora disponibile per la riproduzione tramite gli URL del localizzatore di streaming già pubblicati. Se si vuole rimuovere la possibilità per un cliente di riprodurre il contenuto archiviato, è prima di tutto necessario rimuovere tutti i localizzatori dall'asset e anche scaricare la cache della rete CDN nel percorso URL se si usa una rete CDN per il recapito. In caso contrario, il contenuto verrà memorizzato nella cache della rete CDN per l'impostazione time-to-live standard nella rete CDN (che potrebbe essere fino a 72 ore).

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>Creare un localizzatore di streaming per pubblicare manifesti HLS e DASH

> [!NOTE]
> Quando viene creato l'account Servizi multimediali, all'account viene aggiunto un endpoint di streaming **predefinito** nello stato **Arrestato**. Per avviare lo streaming del [](encode-dynamic-packaging-concept.md) contenuto e sfruttare la creazione dinamica dei pacchetti e la crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello **stato In** esecuzione.

Quando si pubblica l'asset usando un localizzatore di streaming, l'evento live (fino alla lunghezza della finestra DVR) continuerà a essere visualizzabile fino alla scadenza o all'eliminazione del localizzatore di streaming, a seconda del valore che viene prima. Questo è il modo in cui si rende disponibile la registrazione virtuale "nastro" per i destinatari della visualizzazione per la visualizzazione live e su richiesta. Lo stesso URL può essere usato per guardare l'evento live, la finestra DVR o l'asset su richiesta al termine della registrazione (quando viene eliminato l'output live).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>Compilare i percorsi dei manifesti HLS e DASH

Il metodo BuildManifestPaths nell'esempio illustra come creare in modo deterministico i percorsi di streaming da usare per il recapito DASH o HLS a vari client e framework lettore.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>Guardare l'evento

Per guardare l'evento, copiare l'URL di streaming ottenuto durante l'esecuzione del codice descritto in Creare un localizzatore di streaming. È possibile usare il lettore multimediale desiderato. È possibile usare [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) per testare il flusso all'indirizzo https://ampdemo.azureedge.net.

Quando viene arrestato, l'evento live converte automaticamente gli eventi in contenuto su richiesta. Anche dopo l'arresto e l'eliminazione dell'evento, gli utenti potranno riprodurre in streaming il contenuto archiviato sotto forma di video on demand, fintanto che l'asset non viene eliminato. Un asset non può essere eliminato se è usato da un evento. È prima necessario eliminare l'evento.

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Pulizia delle risorse nell'account di Servizi multimediali

Se si esegue l'applicazione fino alla fine, verranno automaticamente pulite tutte le risorse usate nella funzione denominata "cleanUpResources". Assicurarsi che l'applicazione o il debugger venga eseguito fino al completamento o che si possano perdere risorse e terminare con l'esecuzione di eventi live nell'account. Controllare la portale di Azure per verificare che tutte le risorse siano state pulite nell'account di Servizi multimediali.  

Nel codice di esempio fare riferimento al **metodo cleanUpResources** per informazioni dettagliate.

> [!IMPORTANT]
> Lasciare l'evento live in esecuzione comporta costi di fatturazione. Tenere presente, in caso di arresto anomalo o chiusura del programma/progetto, che l'evento live potrebbe rimanere in esecuzione in stato di fatturazione.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Altra documentazione per sviluppatori per Node.js in Azure

- [Azure per sviluppatori & Node.js JavaScript](/azure/developer/javascript/)
- [Codice sorgente di Servizi multimediali nel @azure/azure-sdk-for-js repository Git Hub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentazione del pacchetto di Azure per Node.js sviluppatori](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Passaggi successivi

[Streaming di file](stream-files-tutorial-with-api.md)