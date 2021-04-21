---
title: Concetti relativi a eventi live e output live
description: Questo argomento offre una panoramica degli eventi live e degli output live in Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: 44ab9e4ff83fec2ddfbd1cb44f503298d12789d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766300"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Eventi live e output live in Servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Servizi multimediali di Azure consente di distribuire eventi live ai clienti nel cloud di Azure. Per configurare gli eventi di streaming live in Servizi multimediali v3, è necessario comprendere i concetti illustrati in questo articolo.

> [!TIP]
> Per i clienti che esereranno  la migrazione dalle API di Servizi multimediali v2, l'entità evento live sostituisce **Channel** in v2 e **l'output live** sostituisce **il programma**.

## <a name="live-events"></a>Eventi live

[Gli eventi](/rest/api/media/liveevents) live sono responsabili dell'inserimento e dell'elaborazione dei feed video live. Quando si crea un evento live, viene creato un endpoint di input primario e secondario che è possibile usare per inviare un segnale live da un codificatore remoto. Il codificatore live remoto invia il feed di contributo all'endpoint di input usando il protocollo di input [RTMP](https://www.adobe.com/devnet/rtmp.html) o [Smooth Streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (fragmented-MP4). Per il protocollo di inserimento RTMP, il contenuto può essere inviato in chiaro ( ) o crittografato in modo sicuro `rtmp://` in rete( `rtmps://` ). Per il protocollo di inserimento Smooth Streaming, gli schemi URL supportati sono `http://` o `https://`.  

## <a name="live-event-types"></a>Tipi di evento live

Un [](/rest/api/media/liveevents) evento live può essere impostato su *un pass-through* (un codificatore live locale invia un flusso a velocità in bit multipla) o su una codifica *live* (un codificatore live locale invia un flusso a velocità in bit singola). I tipi vengono impostati durante la creazione usando [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None:** un codificatore live locale invia un flusso a velocità in bit multipla. Il flusso ingestito passa attraverso l'evento live senza ulteriori elaborazioni. Chiamata anche modalità pass-through.
* **LiveEventEncodingType.Standard:** un codificatore live locale invia un flusso a velocità in bit singola all'evento live e Servizi multimediali crea più flussi a velocità in bit. Se il feed di contributi ha una risoluzione di 720p o superiore, il set di impostazioni **Default720p** codifica un set di 6 coppie risoluzione/velocità in bit.
* **LiveEventEncodingType.Premium1080p:** un codificatore live locale invia un flusso a velocità in bit singola all'evento live e Servizi multimediali crea più flussi a velocità in bit. Il set di impostazioni Default1080p specifica il set di output di coppie risoluzione/velocità in bit.

### <a name="pass-through"></a>Pass-through

![Diagramma di esempio dell'evento live pass-through con Servizi multimediali](./media/live-streaming/pass-through.svg)

Quando si usa l'evento live pass-through, si usa il codificatore live locale per generare un flusso video a velocità in bit multipla e inviarlo come feed di contributi all'evento live (usando il protocollo RTMP o MP4 frammentato). L'evento live passa quindi attraverso i flussi video in ingresso senza ulteriori elaborazioni. Un evento live pass-through di questo tipo è ottimizzato per gli eventi live a esecuzione lunga o lo streaming live lineare 24x365. Quando si crea questo tipo di evento live, specificare None (LiveEventEncodingType.None).

È possibile inviare il feed di contributi a risoluzioni fino a 4K e a una frequenza pari a 60 fotogrammi al secondo, con codec video H.264/AVC o H.265/HEVC e codec audio AAC (AAC-LC, HE-AACv1 o HE-AACv2). Per altre informazioni, vedere [Confronto tra i tipi di eventi live.](live-event-types-comparison-reference.md)

> [!NOTE]
> L'uso di un metodo pass-through è il modo più economico per eseguire lo streaming live quando si e usano più eventi in un lungo periodo di tempo e si è già investito in codificatori locali. Vedere [Dettagli sui](https://azure.microsoft.com/pricing/details/media-services/) prezzi.
>

Vedere l'esempio di codice .NET per la creazione di un evento live pass-through in [un evento live con DVR.](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214)

### <a name="live-encoding"></a>Codifica live  

![Diagramma di esempio della codifica live con Servizi multimediali](./media/live-streaming/live-encoding.svg)

Quando si usa la codifica live con Servizi multimediali, si configura il codificatore live locale per l'invio di un video a velocità in bit singola come feed di contributi all'evento live (usando il protocollo RTMP o Fragmented-Mp4). Si configura quindi un evento live in modo che codifica il flusso a velocità in bit singola in ingresso in un flusso video a velocità in [bit](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)multipla e rende disponibile l'output per la distribuzione per la riproduzione dei dispositivi tramite protocolli come MPEG-DASH, HLS e Smooth Streaming.

Quando si usa la codifica live, è possibile inviare il feed di contributi solo a risoluzioni fino a 1080p con una frequenza di fotogrammi di 30 fotogrammi al secondo, con codec video H.264/AVC e codec audio AAC (AAC-LC, HE-AACv1 o HE-AACv2). Si noti che gli eventi live pass-through possono supportare risoluzioni fino a 4K a 60 fotogrammi al secondo. Per altre informazioni, vedere [Confronto tra i tipi di eventi live.](live-event-types-comparison-reference.md)

Le risoluzioni e le velocità in bit contenute nell'output del codificatore live sono determinate dal set di impostazioni. Se si usa un codificatore live **Standard** (LiveEventEncodingType.Standard), il set di impostazioni *Default720p* specifica un set di sei coppie risoluzione/velocità in bit, che va da 720p a 3,5 Mbps fino a 192p a 200 kbps. In caso contrario, se si usa un codificatore live **Premium1080p** (LiveEventEncodingType.Premium1080p), il set di impostazioni *Default1080p* specifica un set di sei coppie risoluzione/velocità in bit, che va da 1080p a 3,5 Mbps fino a 180p a 200 kbps. Per altre informazioni, vedere [Set di impostazioni di sistema](live-event-types-comparison-reference.md#system-presets).

> [!NOTE]
> Se è necessario personalizzare il set di impostazioni di codifica live, aprire un ticket di supporto tramite portale di Azure. Specificare la tabella di risoluzione e velocità in bit desiderata. Verificare che sia presente un solo livello a 720p (se si richiede un set di impostazioni per un codificatore live Standard) o a 1080p (se si richiede un set di impostazioni per un codificatore live Premium1080p) e 6 livelli al massimo.

## <a name="creating-live-events"></a>Creazione di eventi live

### <a name="options"></a>Opzioni

Quando si crea un evento live, è possibile specificare le opzioni seguenti:

* È possibile assegnare all'evento live un nome e una descrizione.
* La codifica cloud include pass-through (nessuna codifica cloud), Standard (fino a 720p) o Premium (fino a 1080p). Per la codifica Standard e Premium, è possibile scegliere la modalità stretch del video codificato.
  * Nessuno: rispetta rigorosamente la risoluzione di output specificata nel set di impostazioni di codifica senza considerare le proporzioni pixel o le proporzioni dello schermo del video di input.
  * AutoSize: esegue l'override della risoluzione dell'output e la modifica in modo che corrisponda alle proporzioni di visualizzazione dell'input, senza spaziatura interna. Ad esempio, se l'input è 1920x1080 e il set di impostazioni di codifica richiede 1280x1280, il valore nel set di impostazioni viene sostituito e l'output sarà pari a 1280x720, mantenendo le proporzioni di input pari a 16:9.
  * Adatta automaticamente: consente di riempire l'output (con letterbox o pillar box) per rispettare la risoluzione dell'output, assicurando al tempo stesso che l'area video attiva nell'output abbia le stesse proporzioni dell'input. Ad esempio, se l'input è 1920x1080 e il set di impostazioni di codifica richiede 1280x1280, l'output sarà 1280x1280, che contiene un rettangolo interno di 1280x720 con proporzioni di 16:9, con aree della casella colonna di 280 pixel di larghezza a sinistra e a destra.
* Protocollo di streaming (attualmente sono supportati i protocolli RTMP e Smooth Streaming di flusso). Non è possibile modificare l'opzione del protocollo durante l'esecuzione dell'evento live o degli output live associati. Se sono necessari protocolli diversi, creare un evento live separato per ogni protocollo di streaming.
* ID di input che è un identificatore univoco globale per il flusso di input dell'evento live.
* Prefisso del nome host statico che include nessuno (nel qual caso verrà usata una stringa esadecimale casuale a 128 bit), Usa nome evento live o Usa nome personalizzato.  Quando si sceglie di usare un nome cliente, questo valore è prefisso nome host personalizzato.
* È possibile ridurre la latenza end-to-end tra la trasmissione live e la riproduzione impostando l'intervallo del fotogramma chiave di input, ovvero la durata (in secondi) di ogni segmento multimediale nell'output HLS. Il valore deve essere un numero intero diverso da zero compreso tra 0,5 e 20 secondi.  Il valore predefinito è 2 secondi se nessuno degli intervalli di fotogrammi chiave di input o di output è impostato.  L'intervallo dei fotogrammi chiave è consentito solo per gli eventi pass-through.
* Quando si crea l'evento, è possibile impostarlo sull'avvio automatico. Quando l'avvio automatico è impostato su true, l'evento live verrà avviato dopo la creazione. La fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare in modo esplicito Stop sulla risorsa evento live per interrompere la fatturazione. In alternativa, è possibile avviare l'evento quando si è pronti per avviare lo streaming.

> [!NOTE]
> La frequenza massima dei fotogrammi è di 30 fps per la codifica Standard e Premium.

## <a name="standby-mode"></a>Modalità StandBy

Quando si crea un evento live, è possibile impostarlo sulla modalità StandBy. Mentre l'evento è in modalità StandBy, è possibile modificare le impostazioni Descrizione, Prefisso nome host statico e limitare le impostazioni di accesso di input e anteprima.  La modalità StandBy è ancora una modalità fatturabile, ma ha un prezzo diverso rispetto a quando si avvia un flusso live.

Per altre informazioni, vedere [Stati degli eventi live e fatturazione.](live-event-states-billing-concept.md)

* Restrizioni IP per l'inserimento e l'anteprima. È possibile definire gli indirizzi IP consentiti per inserire un video in questo evento live. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').
<br/><br/>
Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.<br/>Gli indirizzi IP devono essere in uno dei formati seguenti: indirizzo IpV4 con quattro numeri o intervallo di indirizzi CIDR.
<br/><br/>
Se si vogliono abilitare determinati indirizzi IP nei firewall o vincolare gli input agli eventi live agli indirizzi IP di Azure, scaricare un file JSON dagli intervalli di indirizzi IP del data center [di Azure.](https://www.microsoft.com/download/details.aspx?id=41653) Per informazioni dettagliate su questo file, selezionare **la sezione** Dettagli nella pagina.

* Quando si crea l'evento, è possibile scegliere di attivare le trascrizioni live. Per impostazione predefinita, la trascrizione live è disabilitata. Per altre informazioni sulla trascrizione live, vedere [Trascrizione live.](live-event-live-transcription-how-to.md)

### <a name="naming-rules"></a>Regole di denominazione

* Il nome massimo dell'evento live è di 32 caratteri.
* Il nome deve seguire questo [modello regex:](/dotnet/standard/base-types/regular-expression-language-quick-reference) `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Vedere anche [Convenzioni di denominazione degli endpoint di streaming](stream-streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Per garantire l'univocità del nome dell'evento live, è possibile generare un GUID e quindi rimuovere tutti i trattini e le parentesi graffe (se presenti). La stringa sarà univoca in tutti gli eventi live e la sua lunghezza sarà garantita pari a 32.

## <a name="live-event-ingest-urls"></a>URL di inserimento eventi live

Dopo aver creato l'evento live, è possibile ottenere gli URL di inserimento che verranno forniti al codificatore locale live. Questi URL vengono usati dal codificatore live per inserire un flusso live. Per altre informazioni, vedere [Codificatori live locali consigliati.](encode-recommended-on-premises-live-encoders.md)

>[!NOTE]
> A data della versione dell'API 2020-05-01, gli URL "vanity" sono noti come nomi host statici (useStaticHostname: true)


> [!NOTE]
> Per un URL di inserimento statico e prevedibile per l'uso in una configurazione del codificatore hardware, impostare la proprietà **useStaticHostname** su true e impostare la proprietà **accessToken** sullo stesso GUID a ogni creazione. 

### <a name="example-liveevent-and-liveeventinput-configuration-settings-for-a-static-non-random-ingest-rtmp-url"></a>Impostazioni di configurazione LiveEvent e LiveEventInput di esempio per un URL RTMP di inserimento statico (non casuale).

```csharp
             LiveEvent liveEvent = new LiveEvent(
                    location: mediaService.Location,
                    description: "Sample LiveEvent from .NET SDK sample",
                    // Set useStaticHostname to true to make the ingest and preview URL host name the same. 
                    // This can slow things down a bit. 
                    useStaticHostname: true,

                    // 1) Set up the input settings for the Live event...
                    input: new LiveEventInput(
                        streamingProtocol: LiveEventInputProtocol.RTMP,  // options are RTMP or Smooth Streaming ingest format.
                                                                         // This sets a static access token for use on the ingest path. 
                                                                         // Combining this with useStaticHostname:true will give you the same ingest URL on every creation.
                                                                         // This is helpful when you only want to enter the URL into a single encoder one time for this Live Event name
                        accessToken: "acf7b6ef-8a37-425f-b8fc-51c2d6a5a86a",  // Use this value when you want to make sure the ingest URL is static and always the same. If omitted, the service will generate a random GUID value.
                        accessControl: liveEventInputAccess, // controls the IP restriction for the source encoder.
                        keyFrameIntervalDuration: "PT2S" // Set this to match the ingest encoder's settings
                    ),
```

* Nome host non statico

    Un nome host non statico è la modalità predefinita in Servizi multimediali v3 quando si crea **un liveevent**. È possibile ottenere l'evento live allocato leggermente più rapidamente, ma l'URL di inserimento necessario per l'hardware o il software di codifica live verrà casualizzato. L'URL cambierà se si arresta/avvia l'evento live. I nomi host non statici sono utili solo negli scenari in cui un utente finale vuole trasmettere in streaming usando un'app che deve ottenere un evento live molto rapidamente e la presenza di un URL di inserimento dinamico non rappresenta un problema.

    Se un'app client non deve pre-generare un URL di inserimento prima della creazione dell'evento live, consentire a Servizi multimediali di generare automaticamente il token di accesso per l'evento live.

* Nomi host statici 

    La modalità nome host statico è preferita dalla maggior parte degli operatori che vogliono pre-configurare l'hardware o il software di codifica live con un URL di inserimento RTMP che non cambia mai durante la creazione o l'arresto/avvio di un evento live specifico. Questi operatori vogliono un URL di inserimento PREDITTIVO RTMP che non cambi nel tempo. Questo è molto utile anche quando è necessario eseguire il push di un URL di inserimento RTMP statico nelle impostazioni di configurazione di un dispositivo di codifica hardware come BlackMagic Atem Mini Pro o strumenti di codifica e produzione hardware simili. 

    > [!NOTE]
    > Nell'portale di Azure, l'URL del nome host statico è denominato "*Prefisso nome host statico*".

    Per specificare questa modalità nell'API, impostare `useStaticHostName` su al momento della creazione `true` (il valore predefinito è `false` ). Quando è impostato su true, specifica la prima parte del nome host assegnato all'anteprima dell'evento live e `useStaticHostname` `hostnamePrefix` agli endpoint di inserimento. Il nome host finale è una combinazione di questo prefisso, il nome dell'account del servizio multimediale e un codice breve per il Servizi multimediali di Azure data center.

    Per evitare un token casuale nell'URL, è anche necessario passare il proprio token di accesso ( `LiveEventInput.accessToken` ) al momento della creazione.  Il token di accesso deve essere una stringa GUID valida (con o senza trattini). Una volta impostata, la modalità non può essere aggiornata.

    Il token di accesso deve essere univoco nell'area di Azure e nell'account di Servizi multimediali. Se l'app deve usare un URL di inserimento del nome host statico, è consigliabile creare sempre una nuova istanza GUID da usare con una combinazione specifica di area, account di Servizi multimediali ed evento live.

    Usare le API seguenti per abilitare l'URL del nome host statico e impostare il token di accesso su un GUID valido, ad esempio `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` .  

    |Linguaggio|Abilitare l'URL del nome host statico|Impostare il token di accesso|
    |---|---|---|
    |REST|[properties.useStaticHostname](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.useStaticHostname](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--use-static-hostname](/cli/azure/ams/live-event#az_ams_live_event_create)|[--access-token](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent.useStaticHostname](/dotnet/api/microsoft.azure.management.media.models.liveevent.usestatichostname?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Management_Media_Models_LiveEvent_UseStaticHostname)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>Regole di denominazione degli URL di inserimento live

* La stringa *casuale* sottostante è un numero esadecimale a 128 bit (costituito da 32 caratteri 0-9 a-f).
* *token di accesso:* stringa GUID valida impostata quando si usa l'impostazione del nome host statico. Ad esempio: `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *stream name*: indica il nome del flusso per una connessione specifica. Il valore del nome del flusso viene in genere aggiunto dal codificatore live in uso. È possibile configurare il codificatore live in modo da usare qualsiasi nome per descrivere la connessione, ad esempio: "video1_audio1", "video2_audio1", "stream".

#### <a name="non-static-hostname-ingest-url"></a>URL di inserimento del nome host non statico

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="static-hostname-ingest-url"></a>URL di inserimento del nome host statico

Nei percorsi seguenti indica il nome assegnato all'evento o il nome personalizzato usato nella creazione `<live-event-name>` dell'evento live.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL anteprima evento live

Quando l'evento live inizia a ricevere il feed di contributi, è possibile usare il relativo endpoint di anteprima per visualizzare in anteprima e convalidare la ricezione del flusso live prima di pubblicare ulteriormente. Dopo aver verificato che il flusso di anteprima sia buono, è possibile usare l'evento live per rendere disponibile il flusso live per il recapito tramite uno o più endpoint di streaming (creati in precedenza). A tale scopo, creare un nuovo [output live](/rest/api/media/liveoutputs) nell'evento live.

> [!IMPORTANT]
> Assicurarsi che il video raggiunga l'URL di anteprima prima di continuare.

## <a name="live-event-long-running-operations"></a>Operazioni a esecuzione lunga per eventi live

Per informazioni dettagliate, vedere [Operazioni a esecuzione lunga.](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Output live

Dopo aver trasmesso il flusso nell'evento live, è possibile iniziare l'evento di streaming creando un [asset,](/rest/api/media/assets)un [output live](/rest/api/media/liveoutputs)e un localizzatore [di streaming.](/rest/api/media/streaminglocators) L'output live archivierà il flusso e lo rende disponibile agli utenti tramite [l'endpoint di streaming](/rest/api/media/streamingendpoints).  

Per informazioni dettagliate sugli output live, vedere [Uso di un DVR cloud.](live-event-cloud-dvr-time-how-to.md)

## <a name="live-event-output-questions"></a>Domande sull'output di eventi live

Vedere [l'articolo domande sull'output degli eventi](questions-collection.md#live-streaming) live.
