---
title: Servizi multimediali di Azure Video Indexer note sulla versione | Microsoft Docs
description: Per rimanere sempre aggiornati sugli sviluppi più recenti, questo articolo fornisce gli aggiornamenti più recenti su Servizi multimediali di Azure Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.custom: references_regions
ms.date: 03/30/2021
ms.author: juliako
ms.openlocfilehash: b3602d421718cbd1de3509751491ec6db65b1b01
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532896"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Servizi multimediali di Azure Video Indexer sulla versione

>Quando si torna a visitare questa pagina per informazioni sugli aggiornamenti, è possibile ricevere notifiche copiando e incollando l'URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` nel lettore di feed RSS.

Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate

## <a name="march-2021"></a>Marzo 2021

### <a name="audio-analysis"></a>Analisi audio 

L'analisi audio è ora disponibile in un nuovo bundle aggiuntivo di funzionalità audio a un prezzo diverso. Il nuovo set **di impostazioni analisi audio** di base offre un'opzione a basso costo per estrarre solo trascrizioni vocali, traduzione e formattazione di sottotitoli in output e sottotitoli. Il **set di impostazioni Audio** di base produrrà due contatori separati in fattura, tra cui una riga per la trascrizione e una riga separata per la formattazione dei sottotitoli. Per altre informazioni sui prezzi, vedere la pagina [dei prezzi di Servizi](https://azure.microsoft.com/pricing/details/media-services/) multimediali.

Il bundle appena aggiunto è disponibile quando si indicizza o si esegue nuovamente l'indicizzazione del file scegliendo l'opzione Avanzate Set di impostazioni Audio di base (nella casella di riepilogo a discesa Indicizzazione video e  ->   **audio).**

### <a name="new-developer-portal"></a>Nuovo portale per sviluppatori 

Video Indexer ha un nuovo [portale per sviluppatori](https://api-portal.videoindexer.ai/), provare le nuove API Video Indexer e trovare tutte le risorse pertinenti in un'unica posizione: [repository GitHub,](https://github.com/Azure-Samples/media-services-video-indexer) [Stack overflow](https://stackoverflow.com/questions/tagged/video-indexer), community tecnica di Video Indexer con post di blog pertinenti, domande frequenti su [Video Indexer](faq.md), [User Voice](https://feedback.azure.com/forums/932041-cognitive-services?category_id=399016) per fornire commenti e suggerimenti sulle funzionalità e collegamento ["CodePen"](https://codepen.io/videoindexer) con esempi di codice di widget. [](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer) 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>Funzionalità di personalizzazione avanzate per il widget di informazioni dettagliate 

L'SDK è ora disponibile per incorporare Video Indexer widget di informazioni dettagliate nel proprio servizio e personalizzarne lo stile e i dati. L'SDK supporta il widget Video Indexer insights standard e un widget di informazioni dettagliate completamente personalizzabile. L'esempio di codice [è disponibile Video Indexer repository GitHub](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization). Con queste funzionalità di personalizzazione avanzate, lo sviluppatore di soluzioni può applicare stili personalizzati e usare i dati di intelligenza artificiale del cliente e presentarlo nel widget di informazioni dettagliate (con o senza Video Indexer dettagliate). 

### <a name="video-indexer-deployed-in-the-us-north-central--us-west-and-canada-central"></a>Video Indexer distribuiti negli Stati Uniti centro-settersi, negli Stati Uniti occidentali e in Canada centrale 

È ora possibile creare un account Video Indexer a pagamento nelle aree Stati Uniti centro-settenali, Stati Uniti occidentali e Canada centrale
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Supporto delle nuove lingue di origine per la sintesi vocale (STT), la traduzione e la ricerca 

Video Indexer supporta ora STT, traduzione e ricerca in danese ('da-DK'), norvegese('nb-NO'), svedese('sv-SE'), finlandese ('fi-FI'), francese canadese ('fr-CA'), tailandese('th-TH'), arabo ('ar-BH', 'ar-EG', 'ar-IQ', 'ar-JO', 'ar-KW', 'ar-LB', 'ar-OM', 'ar-QA', 'ar-S' e 'ar-SY' e Turkish('tr-TR'). Queste lingue sono disponibili sia nell'API che Video Indexer sito Web. 
 
### <a name="search-by-topic-in-video-indexer-website"></a>Cercare per argomento nel sito Video Indexer Web 

È ora possibile usare la funzionalità di ricerca nella parte superiore della pagina Video Indexer [sito Web](https://www.videoindexer.ai/account/login) per cercare video con argomenti specifici. 

## <a name="february-2021"></a>Febbraio 2021

### <a name="multiple-account-owners"></a>Più proprietari di account 

Il ruolo proprietario dell'account è stato aggiunto Video Indexer. È possibile aggiungere, modificare e rimuovere utenti. modificare il proprio ruolo. Per informazioni dettagliate su come condividere un account, vedere [Invitare utenti.](invite-users.md)

### <a name="audio-event-detection-public-preview"></a>Rilevamento eventi audio (anteprima pubblica)

> [!NOTE]
> Questa funzionalità è disponibile solo negli account di valutazione. 

Video Indexer ora rileva gli effetti audio seguenti nei segmenti non vocali del contenuto: spari, vetri infranti, allarme, campanella, esplosione, grida, urla, reazione della massa (tifo, clapping e sfociare) e Silenzio. 

La funzionalità effetti audio appena aggiunta è disponibile quando si indicizza il file scegliendo l'opzione Avanzate Set di impostazioni audio avanzato  ->   (in Indicizzazione video e audio). L'indicizzazione standard includerà solo il **silenzio e** la **reazione della massa.** 

Il **tipo di evento clapping** incluso nel modello di effetti audio precedente viene ora estratto come parte del tipo di evento di **reazione** della massa.

Quando si sceglie di visualizzare **Informazioni dettagliate** del video nel sito [Web Video Indexer,](https://www.videoindexer.ai/) gli effetti audio vengono visualizzati nella pagina.

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="Rilevamento di eventi audio":::

### <a name="named-entities-enhancement"></a>Miglioramento delle entità denominate  

L'elenco estratto di persone e località è stato esteso e aggiornato in generale. 

Inoltre, il modello ora include persone e posizioni nel contesto che non sono famosi, ad esempio "Sam" o "Home" nel video. 

## <a name="january-2021"></a>Gennaio 2021

### <a name="video-indexer-is-deployed-on-us-government-cloud"></a>Video Indexer distribuito nel cloud del Governo degli Stati Uniti 

È ora possibile creare un account Video Indexer a pagamento nel cloud del governo degli Stati Uniti nelle aree Virginia e Arizona. Video Indexer versione di valutazione gratuita non è disponibile nell'area indicata. Per altre informazioni, vedere Video Indexer documentazione. 

### <a name="video-indexer-deployed-in-the-india-central-region"></a>Video Indexer distribuite nell'area India centrale 

È ora possibile creare un account Video Indexer a pagamento nell'area India centrale. 

### <a name="new-dark-mode-for-the-video-indexer-website-experience"></a>Nuova modalità scura per l'esperienza Video Indexer sito Web

Le Video Indexer del sito Web sono ora disponibili in modalità scura. Per abilitare la modalità scura, aprire il pannello delle impostazioni e attivare o disattivare **l'opzione Modalità** scura. 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Impostazione della modalità scura":::

## <a name="december-2020"></a>Dicembre 2020

### <a name="video-indexer-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Indexer distribuito in Svizzera occidentale e Svizzera settentrionale

È ora possibile creare un account Video Indexer pagamento nelle aree Svizzera occidentale e Svizzera settentrionale.

## <a name="october-2020"></a>Ottobre 2020

### <a name="animated-character-identification-improvements"></a>Miglioramenti dell'identificazione di caratteri animati  

Video Indexer supporta il rilevamento, il raggruppamento e il riconoscimento dei caratteri nel contenuto animato tramite l'integrazione con la visione personalizzata di Servizi cognitivi. È stato aggiunto un miglioramento significativo a questo algoritmo di intelligenza artificiale nel rilevamento e nel riconoscimento dei caratteri, di conseguenza l'accuratezza delle informazioni dettagliate e i caratteri identificati sono notevolmente migliorati.

### <a name="planned-video-indexer-website-authenticatication-changes"></a>Modifiche pianificate Video Indexer'autenticazione del sito Web

A partire dal 1° marzo 2021, non sarà più possibile iscriversi e accedere al portale per sviluppatori del sito Web [di Video Indexer](https://www.videoindexer.ai/) [](video-indexer-use-apis.md) usando Facebook o LinkedIn.

Sarà possibile iscriversi e accedere usando uno di questi provider: Azure AD, Microsoft e Google.

> [!NOTE]
> Gli Video Indexer connessi a LinkedIn e Facebook non saranno accessibili dopo il 1° marzo 2021. 
> 
> È [consigliabile invitare](invite-users.md) un Azure AD, Microsoft o Google di cui si è proprietari all'account Video Indexer in modo da avere ancora accesso. È possibile aggiungere un altro proprietario dei provider supportati, come descritto in [Invitare](invite-users.md). <br/>
> In alternativa, è possibile creare un account a pagamento ed eseguire la migrazione dei dati.

## <a name="august-2020"></a>Agosto 2020

### <a name="mobile-design-for-the-video-indexer-website"></a>Progettazione di dispositivi mobili per il Video Indexer web

L Video Indexer'esperienza del sito Web supporta ora i dispositivi mobili. L'esperienza utente è reattiva per adattarsi alle dimensioni dello schermo del dispositivo mobile (escluse le UI di personalizzazione). 

### <a name="accessibility-improvements-and-bug-fixes"></a>Miglioramenti dell'accessibilità e correzioni di bug 

Come parte di WCAG (linee guida per l'accessibilità dei contenuti Web), le esperienze del sito Web Video Indexer sono allineate al livello C, come parte degli standard di accessibilità Microsoft. Sono stati risolti diversi bug e miglioramenti correlati alla navigazione tramite tastiera, all'accesso a livello di codice e all'utilità per la lettura dello schermo. 

## <a name="july-2020"></a>Luglio 2020

### <a name="ga-for-multi-language-identification"></a>Ga per l'identificazione in più lingue

L'identificazione in più lingue viene spostata dall'anteprima alla versione a livello di codice disponibile a livello di codice e pronta per l'uso produttivo.

Non vi è alcun impatto sui prezzi correlato alla transizione "Anteprima a ga".

### <a name="video-indexer-website-improvements"></a>Video Indexer dei siti Web

#### <a name="adjustments-in-the-video-gallery"></a>Modifiche nella raccolta video

È stata aggiunta una nuova barra di ricerca per la ricerca di deep insights con funzionalità di filtro aggiuntive. Sono stati migliorati anche i risultati della ricerca.

Nuova visualizzazione elenco con la possibilità di ordinare e gestire l'archivio video con più file.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Nuovo pannello per una facile selezione e configurazione

È stato aggiunto il pannello laterale per una facile selezione e la configurazione utente, consentendo la creazione e la condivisione di account semplici e veloci, nonché l'impostazione della configurazione.

Il pannello laterale viene usato anche per le preferenze utente e la Guida.

## <a name="june-2020"></a>Giugno 2020

### <a name="search-by-topics"></a>Cercare in base agli argomenti

È ora possibile usare l'API di ricerca per cercare video con argomenti specifici (solo API).

Gli argomenti vengono aggiunti come parte di `textScope` (parametro facoltativo). Per [informazioni dettagliate,](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos) vedere API.  

### <a name="labels-enhancement"></a>Miglioramento delle etichette

Il tagger di etichette è stato aggiornato e ora include più etichette visive che possono essere identificate.

## <a name="may-2020"></a>Maggio 2020

### <a name="video-indexer-deployed-in-the-east-us"></a>Video Indexer distribuito negli Stati Uniti orientali

È ora possibile creare un Video Indexer account a pagamento nell'area Stati Uniti orientali.
 
### <a name="video-indexer-url"></a>Video Indexer URL

Video Indexer endpoint regionali sono stati tutti unificati per iniziare solo con www. Non è necessario alcun elemento azione.

D'ora in avanti, si www.videoindexer.ai se si tratta di incorporare widget o accedere Video Indexer applicazioni Web.

Anche wus.videoindexer.ai verrà reindirizzato a www. Altre informazioni sono disponibili in [Incorporare Video Indexer widget nelle app.](video-indexer-embed-widgets.md)

## <a name="april-2020"></a>Aprile 2020

### <a name="new-widget-parameters-capabilities"></a>Nuove funzionalità dei parametri del widget

Il widget **Informazioni** dettagliate include nuovi `language` parametri: e `control` .

Il widget **Player** include un nuovo `locale` parametro. Entrambi `locale` i parametri e `language` controllano la lingua del giocatore.

Per altre informazioni, vedere la sezione Tipi [di widget.](video-indexer-embed-widgets.md#widget-types) 

### <a name="new-player-skin"></a>Nuova interfaccia del lettore

Una nuova interfaccia del lettore avviata con una progettazione aggiornata.

### <a name="prepare-for-upcoming-changes"></a>Preparare le modifiche future

* Attualmente, le API seguenti restituiscono un oggetto account:

    * [Create-Paid-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Get-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Get-Accounts-Authorization](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Get-Accounts-With-Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    L'oggetto Account ha un campo che punta alla posizione del Video Indexer `Url` [sito Web](https://www.videoindexer.ai/).
Per gli account a pagamento `Url` il campo punta attualmente a un URL interno anziché al sito Web pubblico.
Nelle prossime settimane verrà modificato e verrà restituito l'URL Video Indexer [sito Web](https://www.videoindexer.ai/) per tutti gli account (versione di valutazione e pagamento).

    Non usare gli URL interni, è consigliabile usare le [API Video Indexer pubbliche](https://api-portal.videoindexer.ai/).
* Se si incorporano URL Video Indexer nelle applicazioni e gli URL non puntano al sito Web [di Video Indexer](https://www.videoindexer.ai/) o all'endpoint API Video Indexer ( ), ma a un endpoint a livello di zona (ad esempio, ), rigenerare gli `https://api.videoindexer.ai` `https://wus2.videoindexer.ai` URL.

   A tale scopo, è possibile:

    * Sostituzione dell'URL con un URL che punta alle API del widget Video Indexer (ad esempio, il [widget insights)](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget)
    * Uso del Video Indexer per generare un nuovo URL incorporato:
         
         Premere **Riproduci** per accedere alla pagina del video **&lt; / &gt;** > fare clic sul pulsante Incorpora > copiare l'URL nell'applicazione:
   
    Gli URL di area non sono supportati e verranno bloccati nelle prossime settimane.

## <a name="january-2020"></a>Gennaio 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Supporto linguistico personalizzato per lingue aggiuntive

Video Indexer ora supporta modelli linguistici personalizzati per `ar-SY` `en-UK` , e `en-AU` (solo API).
 
### <a name="delete-account-timeframe-action-update"></a>Eliminare l'aggiornamento dell'azione dell'intervallo di tempo dell'account

L'azione Elimina account ora elimina l'account entro 90 giorni anziché 48 ore.
 
### <a name="new-video-indexer-github-repository"></a>Nuovo Video Indexer repository GitHub

È ora disponibile Video Indexer github con progetti diversi, guide introduttive ed esempi di codice: https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Aggiornamento di Swagger

Video Indexer le **autenticazioni e le** **operazioni** unificate in un'unica Video Indexer [specifica OpenAPI (swagger).](https://api-portal.videoindexer.ai/api-details#api=Operations&operation) Gli sviluppatori possono trovare le API in [portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Dicembre 2019

### <a name="update-transcript-with-the-new-api"></a>Aggiornare la trascrizione con la nuova API

Aggiornare una sezione specifica nella trascrizione usando [l'API Update-Video-Index.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index)

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Correggere la configurazione dell'account dal portale Video Indexer dati

È ora possibile aggiornare la configurazione della connessione di Servizi multimediali per consentire l'auto-assistenza in caso di problemi come: 

* risorsa Servizi multimediali di Azure non corretta
* modifiche della password
* Le risorse di Servizi multimediali sono state spostate tra sottoscrizioni  

Per correggere la configurazione dell'account, nel portale Video Indexer passare a Impostazioni > scheda Account (come proprietario).

### <a name="configure-the-custom-vision-account"></a>Configurare l'account visione personalizzata

Configurare l'account visione personalizzata sugli account a pagamento usando il portale Video Indexer (in precedenza era supportato solo dall'API). A tale scopo, accedere al portale Video Indexer modelli, scegliere Personalizzazione modello > caratteri animati > Configura. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scene, riprese e fotogrammi chiave: ora in un unico riquadro informazioni dettagliate

Scene, scene e fotogrammi chiave vengono ora uniti in un'unica panoramica per semplificare l'uso e la navigazione. Quando si seleziona la scena desiderata, è possibile visualizzare le riprese e i fotogrammi chiave di cui è costituita. 

### <a name="notification-about-a-long-video-name"></a>Notifica di un nome video lungo

Quando il nome di un video è più lungo di 80 caratteri, Video Indexer un errore descrittivo durante il caricamento.

### <a name="streaming-endpoint-is-disabled-notification"></a>Notifica dell'endpoint di streaming disabilitato

Quando l'endpoint di streaming è disabilitato, Video Indexer un errore descrittivo nella pagina del lettore.

### <a name="error-handling-improvement"></a>Miglioramento della gestione degli errori

Il codice di stato 409 verrà ora restituito dalle API [Re-Index Video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) e [Update Video Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) nel caso in cui un video sia attivamente indicizzato, per evitare l'override accidentale delle modifiche di re-indicizzazione correnti.

## <a name="november-2019"></a>Novembre 2019
 
* Supporto per i modelli linguistici personalizzati in coreano

    Video Indexer supporta ora modelli linguistici personalizzati in coreano ( `ko-KR` ) sia nell'API che nel portale. 
* Nuove lingue supportate per la sintesi vocale (STT)

    Video Indexer API supportano ora STT in arabo levantine (ar-SY), dialetto inglese Regno Unito (en-GB) e dialetto inglese australiano (en-AU).
    
    Per il caricamento video, zh-HANS è stato sostituito con zh-CN, entrambi sono supportati, ma zh-CN è consigliato e più accurato.
    
## <a name="october-2019"></a>Ottobre 2019
 
* Cercare caratteri animati nella raccolta

    Quando si indicizzano i caratteri animati, è ora possibile cercarli nella galea video dell'account. Per altre informazioni, vedere [Riconoscimento di caratteri animati.](animated-characters-recognition.md)

## <a name="september-2019"></a>Settembre 2019
 
Più progressi annunciati a IBC 2019:
 
* Riconoscimento di caratteri animati (anteprima pubblica)

    Possibilità di rilevare i caratteri di riconoscimento degli annunci di gruppo nel contenuto animato tramite l'integrazione con la visione personalizzata. Per altre informazioni, vedere [Rilevamento di caratteri animati](animated-characters-recognition.md).
* Identificazione in più lingue (anteprima pubblica)

    Rilevare i segmenti in più lingue nella traccia audio e creare una trascrizione multilingue basata su di essi. Supporto iniziale: inglese, spagnolo, tedesco e francese. Per altre informazioni, vedere [Identificare e trascrivere automaticamente contenuto multilingue](multi-language-identification-transcription.md).
* Estrazione di entità denominate per People e Location

    Estrae marchi, posizioni e persone dal parlato e dal testo visivo tramite l'elaborazione del linguaggio naturale (NLP).
* Classificazione del tipo di ripresa editoriale

    Assegnazione di tag a foto con tipi editoriali, ad esempio primo livello, di medie dimensioni, a due foto, all'interno, all'esterno e così via. Per altre informazioni, vedere [Rilevamento del tipo di ripresa editoriale.](scenes-shots-keyframes.md#editorial-shot-type-detection)
* Miglioramento dell'inferenza degli argomenti : ora copre il livello 2
    
    Il modello di inferenza dell'argomento supporta ora una granularità più approfondita della tassonomia IPTC. Per informazioni dettagliate, vedere [Servizi multimediali di Azure'innovazione basata sull'intelligenza artificiale.](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)

## <a name="august-2019"></a>Agosto 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer distribuito in Regno Unito meridionale

È ora possibile creare un account Video Indexer'account a pagamento nell'area Regno Unito meridionale.

### <a name="new-editorial-shot-type-insights-available"></a>Nuove informazioni dettagliate sul tipo di pubblicazione editoriale disponibili

I nuovi tag aggiunti agli screenshot video forniscono "tipi di foto" editoriali per identificarli con frasi editoriali comuni usate nel flusso di lavoro di creazione del contenuto, ad esempio: primo piano, primo piano, ampio, medio, due foto, esterno, interno, viso sinistro e viso destro (disponibile nel codice JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Estrazione di nuove entità Persone e Località disponibili

Video Indexer identifica le località e le persone denominate tramite l'elaborazione del linguaggio naturale (NLP, Natural Language Processing) dall'OCR e dalla trascrizione del video. Video Indexer usa l'algoritmo di Machine Learning per riconoscere quando località specifiche (ad esempio, la Egle Tower) o persone (ad esempio John Doe) vengono chiamate in un video.

### <a name="keyframes-extraction-in-native-resolution"></a>Estrazione di fotogrammi chiave nella risoluzione nativa

I fotogrammi chiave estratti Video Indexer sono disponibili nella risoluzione originale del video.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Ga per il training di modelli di viso personalizzati da immagini

Training dei visi dalle immagini spostate dalla modalità di anteprima alla disponibilità a livello di disponibilità (disponibile tramite API e nel portale).

> [!NOTE]
> Non vi è alcun impatto sui prezzi correlato alla transizione "Anteprima a ga".

### <a name="hide-gallery-toggle-option"></a>Nascondi l'opzione di attivazione/disattivazione della raccolta

L'utente può scegliere di nascondere la scheda della raccolta dal portale (in modo simile a nascondere la scheda degli esempi).
 
### <a name="maximum-url-size-increased"></a>Dimensioni massime url aumentate

Supporto per la stringa di query URL 4096 (anziché 2048) per l'indicizzazione di un video.
 
### <a name="support-for-multi-lingual-projects"></a>Supporto per progetti multilingue

I progetti possono ora essere creati in base ai video indicizzati in lingue diverse (solo API).

## <a name="july-2019"></a>Luglio 2019

### <a name="editor-as-a-widget"></a>Editor come widget

Il Video Indexer editor di intelligenza artificiale è ora disponibile come widget da incorporare nelle applicazioni dei clienti.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aggiornare il modello linguistico personalizzato dal file di sottotitoli codificati dal portale

I clienti possono fornire formati di file VTT, SRT e TTML come input per i modelli linguistici nella pagina di personalizzazione del portale.

## <a name="june-2019"></a>Giugno 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer distribuito in Giappone orientale

È ora possibile creare un account Video Indexer a pagamento nell'area Giappone orientale.

### <a name="create-and-repair-account-api-preview"></a>API per la creazione e il ripristino dell'account (anteprima)

Aggiunta di una nuova API che consente di aggiornare la chiave o [l'endpoint di connessione del Servizio multimediale di Azure.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)

### <a name="improve-error-handling-on-upload"></a>Migliorare la gestione degli errori durante il caricamento 

Viene restituito un messaggio descrittivo in caso di configurazione errata dell'account Servizi multimediali di Azure sottostante.

### <a name="player-timeline-keyframes-preview"></a>Anteprima dei fotogrammi chiave della sequenza temporale del lettore 

È ora possibile visualizzare un'anteprima dell'immagine per ogni volta nella sequenza temporale del lettore.

### <a name="editor-semi-select"></a>Semi-selezione dell'editor

È ora possibile visualizzare un'anteprima di tutte le informazioni dettagliate selezionate in seguito alla scelta di un intervallo di tempo di informazioni dettagliate specifico nell'editor.

## <a name="may-2019"></a>Maggio 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aggiornare il modello linguistico personalizzato dal file di sottotitoli codificati

[Creare un modello linguistico](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) personalizzato e le API Aggiorna modelli linguistici personalizzati ora supportano i formati di file VTT, SRT e TTML come input per i modelli linguistici. [](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model)

Quando si chiama [l'API Di aggiornamento trascrizione video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript), la trascrizione viene aggiunta automaticamente. Anche il modello di training associato al video viene aggiornato automaticamente. Per informazioni su come personalizzare ed eseguire il training dei modelli linguistici, vedere [Personalizzare un modello linguistico con Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nuovi formati di trascrizione per il download: TXT e CSV

Oltre al formato di sottotitoli codificati già supportato (SRT, VTT e TTML), Video Indexer ora supporta il download della trascrizione nei formati TXT e CSV.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica](video-indexer-overview.md)
