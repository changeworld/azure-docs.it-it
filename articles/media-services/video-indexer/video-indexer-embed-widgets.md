---
title: Incorporare Video Indexer widget nelle app
titleSuffix: Azure Media Services
description: Informazioni su come incorporare Video Indexer widget nelle app.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 822d50bca6bc1139e9b5f0554bcf9a56a8fcbd74
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532872"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Incorporare Video Indexer widget nelle app

Questo articolo illustra come incorporare Video Indexer widget nelle app. Video Indexer supporta l'incorporamento nelle applicazioni di tre tipi di widget: *Dati analitici*, *Lettore* ed *Editor*.

A partire dalla versione 2, l'URL di base del widget include l'area dell'account specificato. Ad esempio, un account nell'area Stati Uniti occidentali genera: `https://www.videoindexer.ai/embed/insights/.../?location=westus2`.

## <a name="widget-types"></a>Tipi di widget

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Un widget Cognitive Insights include tutte le informazioni dettagliate visive estratte dal processo di indicizzazione del video. Il widget Cognitive Insights supporta i parametri URL facoltativi seguenti:

|Nome|Definizione|Descrizione|
|---|---|---|
|`widgets` | Stringhe separate da virgola | Consente di controllare le informazioni dettagliate di cui si vuole eseguire il rendering.<br/>Esempio: esegue il `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` rendering solo delle informazioni dettagliate sull'interfaccia utente relative a persone e parole chiave.<br/>Opzioni disponibili: people, animatedCharacters, keywords, labels, sentiments, emotions, topics, keyframes, transcript, ocr, speakers, scenes e namedEntities.|
|`controls`|Stringhe separate da virgola|Consente di controllare i controlli di cui si vuole eseguire il rendering.<br/>Esempio: esegue `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` il rendering solo dell'opzione di ricerca e del pulsante di download.<br/>Opzioni disponibili: ricerca, download, set di impostazioni, lingua.|
|`language`|Un codice lingua breve (nome della lingua)|Controlla il linguaggio delle informazioni dettagliate.<br/>Esempio: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>o `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Codice lingua breve | Controlla la lingua dell'interfaccia utente. Il valore predefinito è `en`. <br/>Esempio: `locale=de`.|
|`tab` | Scheda selezionata predefinita | Controlla la **scheda Informazioni** dettagliate di cui viene eseguito il rendering per impostazione predefinita. <br/>Esempio: `tab=timeline` esegue il rendering delle informazioni dettagliate con la scheda **Sequenza** temporale selezionata.|
|`location` ||Il `location` parametro deve essere incluso nei collegamenti incorporati. Vedere come ottenere il nome [dell'area.](regions.md) Se l'account è in anteprima, `trial` deve essere usato per il valore della posizione. `trial` è il valore predefinito per il `location` parametro .| 

### <a name="player-widget"></a>Widget Player

È possibile usare il widget Player (Lettore) per trasmettere video in streaming usando la velocità in bit adattiva. Il widget Player supporta i parametri URL facoltativi seguenti.

|Nome|Definizione|Descrizione|
|---|---|---|
|`t` | Secondi dall'inizio | Fa in modo che il giocatore inizi a riprodurre dal punto temporale specificato.<br/> Esempio: `t=60`. |
|`captions` | Un codice lingua | Recupera la didascalia nella lingua specificata durante il caricamento del widget in modo che sia disponibile nel menu **Didascalie.**<br/> Esempio: `captions=en-US`. |
|`showCaptions` | Valore booleano | Carica il lettore con i sottotitoli già abilitati.<br/> Esempio: `showCaptions=true`. |
|`type`| | Attiva un'interfaccia del lettore audio (la parte video viene rimossa).<br/> Esempio: `type=audio`. |
|`autoplay` | Valore booleano | Indica se il lettore deve iniziare a riprodurre il video quando viene caricato. Il valore predefinito è `true`.<br/> Esempio: `autoplay=false`. |
|`language`/`locale` | Un codice lingua | Controlla la lingua del lettore. Il valore predefinito è `en-US`.<br/>Esempio: `language=de-DE`.|
|`location` ||Il `location` parametro deve essere incluso nei collegamenti incorporati. Vedere come ottenere il nome [dell'area.](regions.md) Se l'account è in anteprima, `trial` deve essere usato per il valore della posizione. `trial` è il valore predefinito per il `location` parametro .| 

### <a name="editor-widget"></a>Widget editor

È possibile usare il widget Editor per creare nuovi progetti e gestire le informazioni dettagliate di un video. Il widget Editor supporta i parametri URL facoltativi seguenti.

|Nome|Definizione|Descrizione|
|---|---|---|
|`accessToken`<sup>*</sup> | string | Fornisce l'accesso ai video presenti solo nell'account usato per incorporare il widget.<br> Il widget Editor richiede il `accessToken` parametro . |
|`language` | Un codice lingua | Controlla la lingua del lettore. Il valore predefinito è `en-US`.<br/>Esempio: `language=de-DE`. |
|`locale` | Codice lingua breve | Controlla il linguaggio delle informazioni dettagliate. Il valore predefinito è `en`.<br/>Esempio: `language=de`. |
|`location` ||Il `location` parametro deve essere incluso nei collegamenti incorporati. Vedere come ottenere il nome [dell'area.](regions.md) Se l'account è in anteprima, `trial` deve essere usato per il valore della posizione. `trial` è il valore predefinito per il `location` parametro .| 

<sup>*</sup>Il proprietario deve prestare `accessToken` attenzione.

## <a name="embed-videos"></a>Incorporare video

Questa sezione illustra l'incorporamento di video [tramite il portale](#the-portal-experience) o [assemblando manualmente l'URL](#assemble-the-url-manually) nelle app. 

Il `location` parametro deve essere incluso nei collegamenti incorporati. Vedere come ottenere il nome [dell'area.](regions.md) Se l'account è in anteprima, `trial` deve essere usato per il valore della posizione. `trial` è il valore predefinito per il `location` parametro . Ad esempio: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

### <a name="the-portal-experience"></a>Esperienza del portale

Per incorporare un video, usare il portale come descritto di seguito:

1. Accedere al sito [Web Video Indexer.](https://www.videoindexer.ai/)
1. Selezionare il video che si vuole usare e premere **Riproduci.**
1. Selezionare il tipo di widget desiderato (**Cognitive Insights,** **Player** o **Editor).**
1. Fare clic **&lt; / &gt; su Incorpora.**
5. Copiare il codice di incorporamento (visualizzato in **Copiare il** codice incorporato nella finestra **di dialogo & incorporamento).**
6. Aggiungere il codice all'app.

> [!NOTE]
> La condivisione di un collegamento per il widget **Player** **o Insights** includerà il token di accesso e concederà le autorizzazioni di sola lettura all'account.

### <a name="assemble-the-url-manually"></a>Assemblare manualmente l'URL

#### <a name="public-videos"></a>Video pubblici

È possibile incorporare video pubblici assemblando l'URL come segue:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>`
  
  
#### <a name="private-videos"></a>Video privati

Per incorporare un video privato, è necessario passare un token di accesso (usare [Get Video Access Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) nell'attributo `src` dell'iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
  
### <a name="provide-editing-insights-capabilities"></a>Fornire funzionalità di modifica delle informazioni dettagliate

Per fornire funzionalità di modifica delle informazioni dettagliate nel widget incorporato, è necessario passare un token di accesso che include le autorizzazioni di modifica. Usare [Get Video Access Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) con `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interazione dei widget

Il widget Cognitive Insights può interagire con un video nell'app. Questa sezione illustra come ottenere questa interazione.

![Cognitive Insights widget Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Panoramica del flusso

Quando si modificano le trascrizioni, si verifica il flusso seguente:

1. La trascrizione viene modificata nella sequenza temporale.
1. Video Indexer questi aggiornamenti e li salva in [dalle modifiche della trascrizione](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) nel modello linguistico.
1. Le didascalie vengono aggiornate:

    * Se si usa il Video Indexer del lettore, questo viene aggiornato automaticamente.
    * Se si usa un lettore esterno, si ottiene un nuovo file di sottotitoli con la chiamata **Ottieni sottotitoli video.**

### <a name="cross-origin-communications"></a>Comunicazioni tra le origini

Per ottenere Video Indexer widget per comunicare con altri componenti, il Video Indexer seguente:

- Usa il metodo HTML5 per la comunicazione tra le `postMessage` origini.
- Convalida il messaggio nell'origine VideoIndexer.ai.

Se si implementa il codice del lettore e si integra con i widget di Cognitive Insights, è responsabilità dell'utente convalidare l'origine del messaggio che proviene VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Incorporare widget nell'app o nel blog (scelta consigliata)

Questa sezione illustra come ottenere l'interazione tra due widget Video Indexer in modo che quando un utente seleziona il controllo delle informazioni dettagliate nell'app, il lettore passa al momento pertinente.

1. Copiare il codice di incorporamento del widget Player.
2. Copiare il codice di incorporamento di Cognitive Insights.
3. Aggiungere il [file Mediator](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) per gestire la comunicazione tra i due widget:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

A questo punto, quando un utente seleziona il controllo delle informazioni dettagliate nell'app, il giocatore passa al momento pertinente.

Per altre informazioni, vedere la [demo Video Indexer - Incorporare entrambi i widget](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Incorporare il widget Cognitive Insights e usare Azure Media Player per riprodurre il contenuto

Questa sezione illustra come ottenere l'interazione tra un widget cognitive insights e un'istanza Azure Media Player usando il [plug-in AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Aggiungere un plug-in Video Indexer per il lettore AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Creare Azure Media Player con Video Indexer plug-in.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Copiare il codice di incorporamento di Cognitive Insights.

È ora possibile comunicare con Azure Media Player.

Per altre informazioni, vedere la [demo Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Incorporare il Video Indexer Cognitive Insights e usare un lettore video diverso

Se si usa un lettore video diverso da Azure Media Player, è necessario modificare manualmente il lettore video per ottenere la comunicazione.

1. Inserire il lettore video.

    Ad esempio, un lettore HTML5 standard:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Incorporare il widget Cognitive Insights.
3. Implementare la comunicazione per il lettore facendo in modo che resti in ascolto dell'evento "message". Ad esempio:

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videobreakdown domain.
          if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

Per altre informazioni, vedere la [demo Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Aggiunta di sottotitoli

Se si incorporano Video Indexer informazioni dettagliate con il [proprio Azure Media Player](https://aka.ms/azuremediaplayer), è possibile usare il metodo per ottenere i sottotitoli `GetVttUrl` codificati (sottotitoli). È anche possibile chiamare un metodo JavaScript dal plug-in Video Indexer AMP `getSubtitlesUrl` (come illustrato in precedenza).

## <a name="customizing-embeddable-widgets"></a>Personalizzazione di widget incorporabili

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

È possibile scegliere i tipi di informazioni dettagliate desiderate. A tale scopo, specificarli come valore per il parametro URL seguente che viene aggiunto al codice di incorporamento che si ottiene (dall'API o dall'app Web): `&widgets=<list of wanted widgets>` .

I valori possibili sono: `people` , , , , , , , `animatedCharacters` , , , `keywords` , e `labels` `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr` `speakers` `scenes` `namedEntities` .

Ad esempio, se si vuole incorporare un widget che contiene solo informazioni dettagliate su persone e parole chiave, l'URL di incorporamento dell'iframe sarà simile al seguente:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Si può anche personalizzare il titolo della finestra dell'iframe, fornendo il valore `&title=<YourTitle>` all'URL dell'iframe. (Personalizza il valore `<title>` HTML).
   
Ad esempio, se si vuole dare alla finestra dell'iframe il titolo "MyInsights", l'URL avrà un aspetto simile al seguente:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Si noti che questa opzione è pertinente solo nei casi in cui è necessario aprire le informazioni dettagliate in una nuova finestra.

### <a name="player-widget"></a>Widget Player

Se si incorpora il lettore Video Indexer, è possibile sceglierne le dimensioni specificando le dimensioni dell'iframe.

Ad esempio:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Per impostazione predefinita, Video Indexer lettore ha sottotitoli codificati autogenerati basati sulla trascrizione del video. La trascrizione viene estratta dal video con la lingua di origine selezionata al momento del caricamento del video.

Se si vuole incorporare con una lingua diversa, è possibile aggiungere `&captions=<Language Code>` all'URL del lettore di incorporamento. Se si vuole che le didascalie siano visualizzate per impostazione predefinita, è possibile passare &showCaptions=true.

L'URL di incorporamento sarà simile al seguente:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Riproduzione automatica

Per impostazione predefinita, il lettore inizierà a riprodurre il video. È possibile scegliere di non passare `&autoplay=false` all'URL di incorporamento precedente.

## <a name="code-samples"></a>Esempi di codice

Vedere il repo [degli esempi](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) di codice che contiene esempi per l'API Video Indexer e i widget:

| File/cartella                       | Descrizione                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Caricare video dell'indicizzatore video in un Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Incorporare VI Insights con un Azure Media Player.                             |
| `control-vi-embedded-player`      | Incorporare VI Player e controllarlo dall'esterno.                                    |
| `custom-index-location`           | Incorporare VI Insights da una posizione esterna personalizzata (può essere un BLOB del cliente).     |
| `embed-both-insights`             | Utilizzo di base di VI Insights sia per il lettore che per le informazioni dettagliate.                            |
| `embed-insights-with-AMP`         | Incorporare il widget VI Insights con un Azure Media Player.                      |
| `customize-the-widgets`           | Incorporare widget VI con opzioni personalizzate.                                     |
| `embed-both-widgets`              | Incorporare VI Player e Insights e comunicare tra di essi.                      |
| `url-generator`                   | Genera l'URL di incorporamento personalizzato dei widget in base alle opzioni specificate dall'utente.             |
| `html5-player`                    | Incorporare VI Insights con un lettore video HTML5 predefinito.                           |

## <a name="supported-browsers"></a>Browser supportati

Per altre informazioni, vedere [Browser supportati.](video-indexer-overview.md#supported-browsers)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare e modificare le Video Indexer dettagliate, vedere Visualizzare e modificare Video Indexer [dettagliate.](video-indexer-view-edit.md)

Vedere anche [Video Indexer CodePen.](https://codepen.io/videoindexer/pen/eGxebZ)
