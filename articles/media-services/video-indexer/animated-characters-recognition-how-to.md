---
title: Rilevamento di caratteri animati con Video Indexer come
titleSuffix: Azure Media Services
description: Questo articolo illustra come usare il rilevamento dei caratteri animati con Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: e80699a043d4c18a1bc7ba75ce58c6972fae0fad
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531558"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Usare il rilevamento dei caratteri animati (anteprima) con il portale e l'API 

Servizi multimediali di Azure Video Indexer supporta il rilevamento, il raggruppamento e il riconoscimento dei caratteri nel contenuto animato, questa funzionalità è disponibile tramite il portale di Azure e tramite l'API. Esaminare [questo argomento di panoramica.](animated-characters-recognition.md)

Questo articolo illustra come usare il rilevamento dei caratteri animati con il portale di Azure e l'API Video Indexer animata.

## <a name="use-the-animated-character-detection-with-portal"></a>Usare il rilevamento dei caratteri animati con il portale 

Negli account di valutazione l'integrazione Visione personalizzata è gestita da Video Indexer, è possibile iniziare a creare e usare il modello di caratteri animati. Se si usa l'account di valutazione, è possibile ignorare la sezione seguente ("Connettere l'account Visione personalizzata account").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Connettere l'account Visione personalizzata (solo account a pagamento)

Se si possiede un Video Indexer a pagamento, è necessario prima connettere un account Visione personalizzata account. Se non si ha già un account Visione personalizzata, crearne uno. Per altre informazioni, [vedere](../../cognitive-services/custom-vision-service/overview.md)Visione personalizzata .

> [!NOTE]
> Entrambi gli account devono essere nella stessa area. L Visione personalizzata integra non è attualmente supportata nell'area Del Giappone.

Gli account a pagamento che hanno accesso al Visione personalizzata possono visualizzare i modelli e le immagini con tag. Altre informazioni sul [miglioramento del classificatore in Visione personalizzata](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). 

Si noti che il training del modello deve essere eseguito solo tramite Video Indexer e non tramite il Visione personalizzata sito Web. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Connettere un account Visione personalizzata'API 

Seguire questa procedura per connettersi Visione personalizzata account a Video Indexer o per modificare l'account Visione personalizzata attualmente connesso a Video Indexer:

1. Passare [a](https://www.customvision.ai) www.customvision.ai e accedere.
1. Copiare le chiavi per le risorse Training e Stima:

    > [!NOTE]
    > Per fornire tutte le chiavi è necessario avere due risorse separate in Visione personalizzata, una per il training e una per la stima.
1. Specificare altre informazioni:

    * Endpoint 
    * ID risorsa di previsione
1. Individuare e accedere al [Video Indexer](https://vi.microsoft.com/).
1. Fare clic sul punto interrogativo nell'angolo superiore destro della pagina e scegliere **Riferimento API.**
1. Assicurarsi di aver effettuato la sottoscrizione API Management facendo clic **sulla scheda** Prodotti. Se si dispone di un'API connessa, è possibile continuare con il passaggio successivo. In caso contrario, sottoscrivere. 
1. Nel portale per sviluppatori fare clic su **Completare le informazioni di riferimento sulle API** e passare a **Operazioni**.  
1. Selezionare **Connetti Visione personalizzata account (ANTEPRIMA) e fare** clic su **Prova.**
1. Compilare i campi obbligatori e il token di accesso e fare clic su **Invia.** 

    Per altre informazioni su come ottenere il token Video Indexer [](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)di accesso, visitare il portale per sviluppatori e vedere la [documentazione pertinente.](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)  
1. Dopo che la chiamata ha restituito la risposta 200 OK, l'account è connesso.
1. Per verificare la connessione, passare al [portale Video Indexer](https://vi.microsoft.com/)) :
1. Fare clic sul **pulsante Personalizzazione modello di** contenuto nell'angolo superiore destro.
1. Passare alla scheda **Caratteri animati.**
1. Dopo aver fatto clic su Manage models (Gestisci modelli) in Visione personalizzata, si verrà trasferiti all'account Visione personalizzata appena connesso.

> [!NOTE]
> Attualmente sono supportati solo i modelli creati tramite Video Indexer. I modelli creati tramite Visione personalizzata non saranno disponibili. Inoltre, la procedura consigliata consiste nel modificare i modelli creati tramite Video Indexer solo tramite la piattaforma Video Indexer, poiché le modifiche apportate tramite Visione personalizzata possono causare risultati imprevisti.

### <a name="create-an-animated-characters-model"></a>Creare un modello di caratteri animati

1. Passare al sito Web di [Video Indexer](https://vi.microsoft.com/) ed eseguire l'accesso.
1. Per personalizzare un modello nell'account, selezionare il pulsante **Personalizzazione** modello di contenuto a sinistra della pagina.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Personalizzare il modello di contenuto in Video Indexer":::
1. Passare alla scheda **Caratteri animati** nella sezione personalizzazione del modello.
1. Fare clic su **Aggiungi modello**.
1. Assegnare un nome al modello e fare clic su INVIO per salvare il nome.

> [!NOTE]
> La procedura consigliata consiste nell'avere un modello di visione personalizzata per ogni serie animata. 

### <a name="index-a-video-with-an-animated-model"></a>Indicizzare un video con un modello animato

Per il training iniziale, caricare almeno due video. Ognuno deve essere preferibilmente più lungo di 15 minuti, prima di aspettarsi un buon modello di riconoscimento. Se si hanno episodi più brevi, è consigliabile caricare almeno 30 minuti di contenuto video prima del training. In questo modo sarà possibile unire gruppi che appartengono allo stesso carattere da scene e sfondi diversi e quindi aumentare la probabilità che rilevi il carattere negli episodi seguenti indicizzati. Per eseguire il training di un modello su più video (episodi), è necessario indicizzarli tutti con lo stesso modello di animazione. 

1. Fare clic sul **pulsante Carica.**
1. Scegliere un video da caricare (da un file o da un URL).
1. Fare clic su **Opzioni avanzate**.
1. In **Persone/Caratteri animati scegliere** **Modelli di animazione.**
1. Se si ha un modello, questo verrà scelto automaticamente e, se si dispone di più modelli, è possibile scegliere quello pertinente dal menu a discesa.
1. Fare clic su Carica.
1. Dopo aver indicizzato il video, i caratteri rilevati verranno visualizzati nella **sezione Caratteri** animati nel **riquadro Informazioni** dettagliate.

Prima di contrassegnare ed eseguire il training del modello, tutti i caratteri animati saranno denominati "#X". Dopo il training, anche il modello verrà riconosciuto.

### <a name="customize-the-animated-characters-models"></a>Personalizzare i modelli di caratteri animati

1. Assegnare un nome ai caratteri Video Indexer.

    1. Dopo aver creato il gruppo di caratteri del modello, è consigliabile esaminare questi gruppi in Visione personalizzata. 
    1. Per contrassegnare un carattere animato nel video, passare alla scheda **Informazioni** dettagliate e fare clic sul pulsante Modifica   nell'angolo superiore destro della ****   finestra. 
    1. Nel riquadro **Informazioni** dettagliate fare clic su uno dei caratteri animati rilevati e modificarne i nomi da "Unknown #X" a un nome temporaneo (o al nome assegnato in precedenza   al carattere). 
    1. Dopo aver digitato il nuovo nome, fare clic sull'icona di controllo accanto al nuovo nome. In questo modo il nuovo nome viene salvato nel modello Video Indexer. 
1. Solo account a pagamento: esaminare i gruppi in Visione personalizzata 

    > [!NOTE]
    > Gli account a pagamento che hanno accesso al Visione personalizzata possono visualizzare i modelli e le immagini con tag. Altre informazioni sul [miglioramento del classificatore in Visione personalizzata](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). È importante notare che il training del modello deve essere eseguito solo tramite Video Indexer (come descritto in questo topid) e non tramite il Visione personalizzata web. 

    1. Passare alla pagina **Modelli personalizzati** in Video Indexer e scegliere la scheda **Caratteri animati.** 
    1. Fare clic sul pulsante Modifica per il modello su cui si sta lavorando per gestirlo Visione personalizzata. 
    1. Esaminare ogni gruppo di caratteri: 

        * Se il gruppo contiene immagini non correlate, è consigliabile eliminarle nel Visione personalizzata sito Web. 
        * Se sono presenti immagini che appartengono a un carattere diverso, modificare il tag in queste immagini specifiche facendo clic sull'immagine, aggiungendo il tag corretto ed eliminando il tag errato. 
        * Se il gruppo non è corretto, ovvero contiene principalmente immagini non di caratteri o immagini di più caratteri, è possibile eliminare in nel sito Web di Visione personalizzata o in Video Indexer informazioni dettagliate. 
        * L'algoritmo di raggruppamento a volte suddivide i caratteri in gruppi diversi. È quindi consigliabile assegnare a tutti i gruppi che appartengono allo stesso carattere lo stesso nome (in Video Indexer Insights), che farà apparire immediatamente tutti questi gruppi come in Visione personalizzata sito Web. 
    1. Dopo aver perfezionato il gruppo, assicurarsi che il nome iniziale con cui è stato contrassegnato rifletta il carattere nel gruppo. 
1. Eseguire il training del modello 

    1. Dopo aver completato la modifica di tutti i nomi desiderati, è necessario eseguire il training del modello. 
    1. Dopo il training di un carattere nel modello, verrà riconosciuto nel video successivo indicizzato con tale modello. 
    1. Aprire la pagina di personalizzazione e fare clic sulla **scheda Caratteri animati** e quindi fare clic sul   pulsante **Train** (Training) per eseguire il training del modello. Per mantenere la connessione tra Video 
    
Indicizzatore e modello, non eseguire il training del modello nel sito Web Visione personalizzata (gli account a pagamento hanno accesso Visione personalizzata sito Web), solo in Video Indexer. Dopo il training, qualsiasi video che verrà indicizzato o reindicizzare con tale modello riconoscerà i caratteri con training. 

## <a name="delete-an-animated-character-and-the-model"></a>Eliminare un carattere animato e il modello

1. Eliminare un carattere animato.

    1. Per eliminare un carattere animato nelle informazioni dettagliate del  video, passare alla scheda **Informazioni** dettagliate e fare clic sul pulsante Modifica nell'angolo superiore destro della finestra.
    1. Scegliere il carattere animato e quindi fare clic sul **pulsante Elimina** sotto il nome.

    > [!NOTE]
    > Questa operazione eliminerà le informazioni dettagliate da questo video, ma non influirà sul modello.
1. Eliminare un modello.

    1. Fare clic sul **pulsante Personalizzazione modello di** contenuto nel menu in alto e passare alla scheda Caratteri **animati.**
    1. Fare clic sull'icona con i puntini di sospensione a destra del modello da eliminare e quindi sul pulsante Elimina.
    
    * Account a pagamento: il modello verrà disconnesso Video Indexer e non sarà possibile riconnetterlo.
    * Account di valutazione: il modello verrà eliminato anche dalla visione della dogana. 
    
        > [!NOTE]
        > In un account di valutazione è disponibile un solo modello che è possibile usare. Dopo l'eliminazione, non è possibile eseguire il training di altri modelli.

## <a name="use-the-animated-character-detection-with-api"></a>Usare il rilevamento dei caratteri animati con l'API 

1. Connettere un account Visione personalizzata servizio.

    Se si è proprietari di Video Indexer account a pagamento, è prima necessario connettere un account Visione personalizzata account. <br/>
    Se non si ha già un account Visione personalizzata, crearne uno. Per altre informazioni, vedere [Visione personalizzata](../../cognitive-services/custom-vision-service/overview.md).

    [Connettere l'account Visione personalizzata usando l'API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Connect-Custom-Vision-Account).
1. Creare un modello di caratteri animati.

    Usare l'API [di creazione del modello di animazione.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Animation-Model)
1. Indicizzare o indicizzare nuovamente un video.

    Usare [l'API di re-indicizzazione.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) 
1. Personalizzare i modelli di caratteri animati.

    Usare l'API [del modello di animazione di](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Animation-Model) training.

### <a name="view-the-output"></a>Visualizzare l'output

Vedere i caratteri animati nel file JSON generato.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Limitazioni

* Attualmente, la funzionalità di "identificazione dell'animazione" non è supportata nellEast-Asia area.
* I caratteri che sembrano essere piccoli o molto grandi nel video potrebbero non essere identificati correttamente se la qualità del video è scadente.
* È consigliabile usare un modello per ogni set di caratteri animati, ad esempio per una serie animata.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)