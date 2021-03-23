---
title: Procedure consigliate per l'SDK Web di Azure Maps | Mappe Microsoft Azure
description: Suggerimenti & trucchi per ottimizzare l'uso di Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ba351053c876c31d945ec7e4127a5caebd6a71ce
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878041"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Procedure consigliate per l'SDK Web di Azure Maps

Questo documento è incentrato sulle procedure consigliate per Azure Maps Web SDK. Tuttavia, molte delle procedure consigliate e delle ottimizzazioni descritte possono essere applicate a tutti gli altri SDK di Azure maps.

Azure Maps Web SDK offre un'area di disegno potente per il rendering di set di dati spaziali di grandi dimensioni in molti modi diversi. In alcuni casi, esistono diversi modi per eseguire il rendering dei dati nello stesso modo, ma a seconda delle dimensioni del set di dati e della funzionalità desiderata, un metodo può offrire prestazioni migliori rispetto ad altri. In questo articolo vengono illustrate le procedure consigliate e i suggerimenti e i trucchi per ottimizzare le prestazioni e creare un'esperienza utente uniforme.

In genere, quando si desidera migliorare le prestazioni della mappa, cercare i modi per ridurre il numero di livelli e di origini e la complessità dei set di dati e degli stili di rendering utilizzati.

## <a name="security-basics"></a>Nozioni fondamentali sulla sicurezza

La parte più importante dell'applicazione è la sua sicurezza. Se l'applicazione non è protetta, un pirata informatico può rovinare qualsiasi applicazione, indipendentemente dalla qualità dell'esperienza utente. Di seguito sono riportati alcuni suggerimenti per proteggere l'applicazione Azure maps. Quando si usa Azure, assicurarsi di acquisire familiarità con gli strumenti di sicurezza disponibili. Vedere questo documento per un' [Introduzione alla sicurezza di Azure](https://docs.microsoft.com/azure/security/fundamentals/overview).

> [!IMPORTANT]
> Azure Maps fornisce due metodi di autenticazione.
> * Autenticazione basata su chiavi di sottoscrizione
> * Azure Active Directory l'autenticazione usa Azure Active Directory in tutte le applicazioni di produzione.
> L'autenticazione basata su chiavi di sottoscrizione è semplice e la maggior parte delle piattaforme di mapping USA come metodo leggero per misurare l'uso della piattaforma per finalità di fatturazione. Tuttavia, non si tratta di una forma sicura di autenticazione e deve essere usata solo localmente durante lo sviluppo di app. Alcune piattaforme offrono la possibilità di limitare gli indirizzi IP e/o il referrer HTTP nelle richieste. queste informazioni possono tuttavia essere facilmente falsificate. Se si usano le chiavi di sottoscrizione, assicurarsi di [ruotarle regolarmente](how-to-manage-authentication.md#manage-and-rotate-shared-keys).
> Azure Active Directory è un servizio di identità aziendale con una vasta gamma di funzionalità e impostazioni di sicurezza per tutti i tipi di scenari applicativi. Microsoft consiglia di usare Azure Active Directory per l'autenticazione per tutte le applicazioni di produzione che usano Azure maps.
> Per altre informazioni sulla [gestione dell'autenticazione in mappe di Azure](how-to-manage-authentication.md) , vedere questo documento.

### <a name="secure-your-private-data"></a>Proteggere i dati privati

Quando i dati vengono aggiunti agli SDK mappa interattiva di Azure Maps, ne viene eseguito il rendering localmente sul dispositivo dell'utente finale e non vengono mai inviati a Internet per qualsiasi motivo.

Se l'applicazione carica dati che non devono essere accessibili pubblicamente, assicurarsi che i dati vengano archiviati in un luogo sicuro, che si acceda in modo sicuro e che l'applicazione stessa sia bloccata e disponibile solo per gli utenti desiderati. Se uno di questi passaggi viene ignorato, un utente non autorizzato può accedere a questi dati. Azure Active Directory possibile bloccarlo.

Vedere questa esercitazione sull' [aggiunta dell'autenticazione all'app Web in esecuzione nel servizio app Azure](https://docs.microsoft.com/azure/app-service/scenario-secure-app-authentication-app-service)

### <a name="use-the-latest-versions-of-azure-maps"></a>Usare le versioni più recenti di Azure Maps

Azure Maps SDK consente di eseguire periodicamente test di sicurezza insieme alle eventuali librerie di dipendenza esterne che possono essere usate dagli SDK. Eventuali problemi di sicurezza noti vengono risolti in modo tempestivo e rilasciati nell'ambiente di produzione. Se l'applicazione fa riferimento alla versione principale più recente della versione ospitata di Azure Maps Web SDK, riceverà automaticamente tutti gli aggiornamenti della versione secondaria che includeranno correzioni correlate alla sicurezza.

Se si esegue l'hosting automatico di Azure Maps Web SDK tramite il modulo NPM, assicurarsi di usare il simbolo di accento circonflesso (^) in combinazione con il numero di versione del pacchetto NPM di Azure Maps nel `package.json` file in modo che punti sempre alla versione secondaria più recente.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>Ottimizza caricamento mappa iniziale

Quando viene caricata una pagina Web, una delle prime operazioni da eseguire è iniziare a eseguire il rendering appena possibile, in modo che l'utente non stia guardando una schermata vuota.

### <a name="watch-the-maps-ready-event"></a>Guarda l'evento Maps Ready

Analogamente, quando la mappa viene caricata inizialmente spesso è preferibile caricare i dati nel minor tempo possibile, in modo che l'utente non stia analizzando una mappa vuota. Poiché la mappa carica le risorse in modo asincrono, è necessario attendere che la mappa sia pronta per l'interazione con prima di provare a eseguire il rendering dei propri dati. Ci sono due eventi che è possibile attendere, un `load` evento e un `ready` evento. L'evento Load viene attivato dopo che la mappa ha completato il caricamento della visualizzazione della mappa iniziale e il caricamento di ogni tessera mappa. L'evento ready viene attivato quando le risorse minime della mappa devono iniziare a interagire con la mappa. L'evento ready può spesso essere attivato a metà del tempo dell'evento Load, consentendo così di iniziare a caricare i dati nella mappa prima.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Caricamento lazy Azure Maps Web SDK

Se la mappa non è necessaria immediatamente, il caricamento lazy di Azure Maps Web SDK viene eseguito fino a quando non è necessario. Questa operazione ritarderà il caricamento dei file JavaScript e CSS usati da Azure Maps Web SDK fino a quando non sarà necessario. Uno scenario comune in cui si verifica questa situazione si verifica quando la mappa viene caricata in una scheda o in un pannello a comparsa che non viene visualizzato al caricamento della pagina.
L'esempio di codice seguente illustra come ritardare il caricamento di Azure Maps Web SDK fino a quando non viene premuto un pulsante.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Caricamento lazy della mappa" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la pagina relativa al <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>caricamento lazy della mappa</a> da mappe di Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Aggiungere un segnaposto per la mappa

Se il caricamento della mappa richiede tempo a causa di limitazioni di rete o di altre priorità all'interno dell'applicazione, è consigliabile aggiungere una piccola immagine di sfondo alla mappa `div` come segnaposto per la mappa. Verrà riempito il void della mappa mentre è in corso il `div` caricamento.

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Imposta lo stile iniziale della mappa e le opzioni della fotocamera durante l'inizializzazione

Spesso le app vogliono caricare la mappa in un percorso o uno stile specifico. Talvolta gli sviluppatori restano in attesa fino a quando la mappa non è stata caricata (o non è in attesa dell' `ready` evento), quindi si utilizzano le `setCemer` `setStyle` funzioni o della mappa. Questa operazione richiederà spesso più tempo per passare alla visualizzazione mappa iniziale desiderata, perché molte risorse vengono caricate per impostazione predefinita prima che vengano caricate le risorse necessarie per la vista mappa desiderata. Un approccio migliore consiste nel passare le opzioni della fotocamera e dello stile della mappa desiderate nella mappa al momento dell'inizializzazione.

## <a name="optimize-data-sources"></a>Ottimizzare le origini dati

Web SDK presenta due origini dati,

* **Origine GeoJSON**: nota come `DataSource` classe, gestisce i dati di posizione non elaborati in formato GeoJSON localmente. Ideale per set di dati di piccole e medie dimensioni (a partire da centinaia di migliaia di funzionalità).
* **Origine del riquadro vettoriale**: Nota alla `VectorTileSource` classe, carica i dati formattati come riquadri vettoriali per la vista mappa corrente, in base al sistema di affiancamento mappe. Ideale per set di dati di grandi dimensioni (milioni o miliardi di funzionalità).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Usare soluzioni basate su Tile per set di impostazioni di grandi dimensioni

Se si usano set di dati di dimensioni maggiori che contengono milioni di funzionalità, il modo consigliato per ottenere prestazioni ottimali consiste nell'esporre i dati usando una soluzione sul lato server, ad esempio il servizio vettoriale o il riquadro immagine raster.  
I riquadri vettoriali sono ottimizzati per caricare solo i dati visualizzati con le geometrie ritagliate nell'area di interesse del riquadro e generalizzate in base alla risoluzione della mappa per il livello di zoom del riquadro.

La [piattaforma Azure Maps Creator](creator-indoor-maps.md) consente di recuperare i dati in formato tessera vettoriale. Altri formati di dati possono usare strumenti come [Tippecanoe](https://github.com/mapbox/tippecanoe) o uno dei numerosi elenchi di [risorse disponibili in questa pagina](https://github.com/mapbox/awesome-vector-tiles).

È anche possibile creare un servizio personalizzato che esegue il rendering dei set di dati come riquadri immagine raster sul lato server e caricare i dati usando la classe TileLayer nell'SDK della mappa. Ciò garantisce prestazioni eccezionali poiché la mappa deve caricare e gestire solo alcune dozzine di immagini. Tuttavia, esistono alcune limitazioni all'uso di riquadri raster poiché i dati non elaborati non sono disponibili localmente. Un servizio secondario è spesso necessario per potenziare qualsiasi tipo di esperienza di interazione, ad esempio individuare la forma su cui un utente ha fatto clic. Inoltre, la dimensione del file di un riquadro raster è spesso più grande di un riquadro vettoriale compresso che contiene le geometrie ottimizzate e con livello di zoom.

Altre informazioni sulle origini dati sono disponibili nel documento [creare un'origine dati](create-data-source-web-sdk.md) .

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Combinare più set di dati in un'unica origine del riquadro vettoriale

Minore è il numero di origini dati che la mappa deve gestire, più velocemente può elaborare tutte le funzionalità da visualizzare. In particolare, quando si tratta di origini affiancate, la combinazione di due origini dei riquadri vettoriali consente di ridurre il numero di richieste HTTP per recuperare i riquadri a metà e la quantità totale di dati potrebbe essere leggermente inferiore perché è presente una sola intestazione di file.

La combinazione di più set di dati in una singola origine del riquadro vettoriale può essere eseguita usando uno strumento come [Tippecanoe](https://github.com/mapbox/tippecanoe). I set di dati possono essere combinati in una singola raccolta di funzionalità o separati in livelli distinti all'interno del riquadro vettoriale noto come livelli di origine. Quando si connette un'origine del riquadro vettoriale a un livello di rendering, è necessario specificare il livello di origine che contiene i dati di cui si vuole eseguire il rendering con il livello.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Ridurre il numero di aggiornamenti dell'area di disegno a causa di aggiornamenti dei dati

Esistono diversi modi in `DataSource` cui è possibile aggiungere o aggiornare i dati in una classe. Di seguito sono elencati i diversi metodi e alcune considerazioni per garantire prestazioni ottimali.

* La funzione origini dati `add` può essere utilizzata per aggiungere una o più funzionalità a un'origine dati. Ogni volta che questa funzione viene chiamata, attiverà un aggiornamento dell'area di disegno della mappa. Se si aggiungono molte funzionalità, combinarle in una matrice o in una raccolta di funzionalità e passarle una sola volta, anziché eseguire il ciclo su un set di dati e chiamare questa funzione per ogni funzionalità.
* La funzione origini dati `setShapes` può essere utilizzata per sovrascrivere tutte le forme in un'origine dati. Dietro le quinte, combina le origini dati `clear` e le `add` funzioni insieme ed esegue un singolo aggiornamento Canvas mappa anziché due, che è molto più veloce. Assicurarsi di utilizzare questo valore quando si desidera aggiornare tutti i dati in un'origine dati.
* La funzione origini dati `importDataFromUrl` può essere usata per caricare un file GeoJSON tramite un URL in un'origine dati. Una volta scaricati, i dati vengono passati alla funzione origini dati `add` . Se il file GeoJSON è ospitato in un dominio diverso, assicurarsi che l'altro dominio supporti le richieste tra domini (CORs). Se non si considera la copia dei dati in un file locale nel dominio o nella creazione di un servizio proxy con CORs abilitato. Se il file è di grandi dimensioni, è consigliabile convertirlo in un'origine del riquadro vettoriale.
* Se viene eseguito il wrapper delle funzionalità con la `Shape` classe, le `addProperty` `setCoordinates` funzioni, e `setProperties` della forma attivano tutti un aggiornamento nell'origine dati e un aggiornamento dell'area di disegno della mappa. Tutte le funzionalità restituite dalle origini dati `getShapes` e dalle `getShapeById` funzioni vengono incapsulate automaticamente con la `Shape` classe. Se si vogliono aggiornare più forme, è più veloce convertirle in JSON usando la funzione origini dati `toJson` , modificando il GeoJSON e quindi passando questi dati nella funzione origini dati `setShapes` .

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Evitare di chiamare la funzione Clear per le origini dati inutilmente

La chiamata della funzione Clear della `DataSource` classe causa l'aggiornamento di un'area di disegno della mappa. Se la `clear` funzione viene chiamata più volte in una riga, è possibile che si verifichi un ritardo durante l'attesa di ogni aggiornamento da parte della mappa.

Uno scenario comune in cui questo viene spesso visualizzato nelle applicazioni è quando un'app cancella l'origine dati, Scarica nuovi dati, Cancella nuovamente l'origine dati, quindi aggiunge i nuovi dati all'origine dati. A seconda dell'esperienza utente desiderata, le alternative seguenti sono migliori.

* Cancellare i dati prima di scaricare i nuovi dati, quindi passare i nuovi dati nelle origini dati `add` o nella `setShapes` funzione. Se si tratta dell'unico set di dati sulla mappa, la mappa sarà vuota durante il download dei nuovi dati.
* Scaricare i nuovi dati, quindi passarli alla funzione origini dati `setShapes` . Tutti i dati della mappa vengono sostituiti.

### <a name="remove-unused-features-and-properties"></a>Rimuovere le funzionalità e le proprietà inutilizzate

Se il set di dati contiene funzionalità che non verranno usate nell'app, rimuoverle. Analogamente, rimuovere tutte le proprietà delle funzionalità non necessarie. Ciò comporta diversi vantaggi:

* Riduce la quantità di dati da scaricare.
* Riduce il numero di funzionalità che devono essere in ciclo durante il rendering dei dati.
* Può a volte semplificare o rimuovere le espressioni e i filtri basati sui dati, il che significa meno elaborazione necessaria al momento del rendering.

Quando le funzionalità di includono molte proprietà o contenuto, è molto più efficiente limitare gli elementi che vengono aggiunti all'origine dati solo a quelli necessari per il rendering e avere un metodo o un servizio separato per recuperare la proprietà o il contenuto aggiuntivo quando necessario. Ad esempio, se si dispone di una mappa semplice che Visualizza i percorsi su una mappa quando si fa clic su una serie di contenuti dettagliati. Se si desidera utilizzare lo stile basato sui dati per personalizzare la modalità di rendering dei percorsi sulla mappa, caricare solo le proprietà necessarie nell'origine dati. Quando si desidera visualizzare il contenuto dettagliato, utilizzare l'ID della funzionalità per recuperare il contenuto aggiuntivo separatamente. Se il contenuto viene archiviato sul lato server, è possibile utilizzare un servizio per recuperarlo in modo asincrono, riducendo drasticamente la quantità di dati che devono essere scaricati quando la mappa viene inizialmente caricata.

Inoltre, la riduzione del numero di cifre significative nelle coordinate delle funzionalità può ridurre significativamente le dimensioni dei dati. Non è insolito che le coordinate contengano 12 o più posizioni decimali; Tuttavia, sei cifre decimali hanno un'accuratezza pari a circa 0,1 metri, che è spesso più precisa della posizione rappresentata dalla coordinata (sono consigliate sei cifre decimali quando si usano dati di località ridotti, ad esempio layout di compilazione interni). La presenza di più di sei cifre decimali probabilmente non farà alcuna differenza nel modo in cui viene eseguito il rendering dei dati e richiederà solo all'utente di scaricare più dati senza alcun vantaggio aggiuntivo.

Di seguito è riportato un elenco di [strumenti utili per l'uso dei dati GeoJSON](https://github.com/tmcw/awesome-geojson).

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>Usare un'origine dati separata per modificare rapidamente i dati

A volte è necessario aggiornare rapidamente i dati sulla mappa per elementi come la visualizzazione di aggiornamenti dinamici dei dati di streaming o l'animazione delle funzionalità. Quando un'origine dati viene aggiornata, il motore di rendering esegue il ciclo e Visualizza tutte le funzionalità nell'origine dati. La separazione dei dati statici da dati in rapida evoluzione in origini dati diverse può ridurre significativamente il numero di funzionalità di cui viene eseguito il rendering a ogni aggiornamento nell'origine dati e migliorare le prestazioni complessive.

Se si usano i riquadri vettoriali con dati dinamici, un modo semplice per supportare gli aggiornamenti consiste nell'usare l' `expires` intestazione della risposta. Per impostazione predefinita, quando la data viene ricaricata automaticamente, i riquadri vengono ricaricati automaticamente da qualsiasi origine o livello sezione raster `expires` . Il flusso del traffico e i riquadri degli eventi imprevisti nella mappa usano questa funzionalità per garantire la visualizzazione dei dati di traffico in tempo reale aggiornati sulla mappa. Questa funzionalità può essere disabilitata impostando l' `refreshExpiredTiles` opzione del servizio Maps su `false` .

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>Modificare le opzioni di buffer e tolleranza nelle origini dati GeoJSON

La `DataSource` classe converte i dati di posizione RAW in riquadri vettoriali locali per il rendering immediato. Questi riquadri vettoriali locali ritagliano i dati non elaborati nei limiti dell'area del riquadro con un bit di buffer per garantire il rendering uniforme tra i riquadri. Minore è l' `buffer` opzione, il minor numero di dati sovrapposti viene archiviato nei riquadri vettoriali locali e le prestazioni migliori, tuttavia, maggiore è la modifica degli artefatti di rendering che si verificano. Provare a modificare questa opzione per ottenere la combinazione corretta di prestazioni con elementi di rendering minimi.

La `DataSource` classe dispone inoltre di un' `tolerance` opzione che viene utilizzata con l'algoritmo di semplificazione Douglas-Peucker quando si riduce la risoluzione delle geometrie a scopo di rendering. L'aumento di questo valore di tolleranza ridurrà la risoluzione delle geometrie e a sua volta migliorerà le prestazioni. Modificare questa opzione per ottenere la combinazione corretta di risoluzione geometrica e prestazioni per il set di dati.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>Impostare l'opzione di zoom massimo delle origini dati GeoJSON

La `DataSource` classe converte i dati di posizione RAW in riquadri vettoriali locali per il rendering immediato. Per impostazione predefinita, questa operazione viene eseguita fino al livello di zoom 18, a quel punto, quando si esegue lo zoom avanti, vengono campionati i dati dei riquadri generati per il livello di zoom 18. Questo comportamento è adatto per la maggior parte dei set di dati che devono avere una risoluzione elevata quando si esegue lo zoom avanti a questi livelli. Tuttavia, quando si usano set di dati con maggiore probabilità di essere visualizzati quando si esegue lo zoom avanti, ad esempio quando si visualizzano i poligoni di stato o provincia, l'impostazione dell' `minZoom` opzione dell'origine dati su un valore inferiore, ad esempio, `12` ridurrà il calcolo della quantità, la generazione di riquadri locali che si verifica e la memoria usata dall'origine dati e aumenterà le prestazioni.

### <a name="minimize-geojson-response"></a>Ridurre la risposta GeoJSON

Quando si caricano i dati GeoJSON da un server tramite un servizio o caricando un file flat, assicurarsi che i dati siano ridotti al minimo per rimuovere i caratteri di spazio non necessari che rendono le dimensioni del download maggiori del necessario.

### <a name="access-raw-geojson-using-a-url"></a>Accedere a GeoJSON non elaborato usando un URL

È possibile archiviare gli oggetti GeoJSON inline all'interno di JavaScript, tuttavia in questo modo verrà usata una grande quantità di memoria perché le copie di tale oggetto verranno archiviate nella variabile creata per questo oggetto e nell'istanza dell'origine dati, che la gestisce in un Web worker separato. Esporre GeoJSON all'app usando invece un URL e l'origine dati caricherà un'unica copia di dati direttamente nel Web Worker delle origini dati.  

## <a name="optimize-rendering-layers"></a>Ottimizzare i livelli di rendering

Azure Maps fornisce diversi livelli per il rendering dei dati su una mappa. Sono disponibili numerose ottimizzazioni che consentono di personalizzare questi livelli in uno scenario in cui migliorare le prestazioni e l'esperienza utente complessiva.

### <a name="create-layers-once-and-reuse-them"></a>Creare i livelli una volta e riutilizzarli

Azure Maps Web SDK si è deciso di guidare i dati. I dati vengono inseriti in origini dati, che vengono quindi connessi ai livelli di rendering. Se si desidera modificare i dati sulla mappa, aggiornare i dati nell'origine dati o modificare le opzioni di stile di un livello. Questa operazione è spesso molto più veloce rispetto alla rimozione e alla ricreazione di livelli ogni volta che viene apportata una modifica.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Prendere in considerazione il livello Bubble rispetto al livello dei simboli

Il livello Bubble esegue il rendering dei punti come cerchi sulla mappa e può avere facilmente lo stile del raggio e del colore usando un'espressione basata sui dati. Poiché il cerchio è una forma semplice per disegnare WebGL, il motore di rendering sarà in grado di eseguire il rendering di questi più velocemente rispetto a un livello di simbolo, che deve caricare ed eseguire il rendering di un'immagine. La differenza di prestazioni di questi due livelli di rendering è evidente quando si esegue il rendering di decine di migliaia di punti.

### <a name="use-html-markers-and-popups-sparingly"></a>Usare i marcatori HTML e i popup sporadicamente

A differenza della maggior parte dei livelli nel controllo Web Maps di Azure che usano WebGL per il rendering, i marcatori HTML e i popup usano gli elementi DOM tradizionali per il rendering. Di conseguenza, più marcatori e popup HTML hanno aggiunto una pagina, maggiore è il numero di elementi DOM. Le prestazioni possono peggiorare dopo l'aggiunta di alcune centinaia di marcatori o popup HTML. Per i set di dati di dimensioni maggiori, è consigliabile eseguire il clustering dei dati o usare un simbolo o un livello Bubble. Per i popup, una strategia comune consiste nel creare un singolo popup e riutilizzarlo aggiornando il contenuto e la posizione, come illustrato nell'esempio seguente:

<br/>

<iframe height='500' scrolling='no' title='Riutilizzo di popup con più segnaposto' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pins</a> (Riutilizzo di popup con più segnaposto) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Detto ciò, se si dispone solo di pochi punti di cui eseguire il rendering sulla mappa, è possibile che si preferisca la semplicità dei marcatori HTML. Inoltre, è possibile rendere facilmente trascinabili i marcatori HTML, se necessario.

### <a name="combine-layers"></a>Combinare i livelli

La mappa è in grado di eseguire il rendering di centinaia di livelli, tuttavia, maggiore è il numero di livelli, maggiore è il tempo necessario per il rendering di una scena. Una strategia per ridurre il numero di livelli consiste nel combinare i livelli con stili simili o con stili [basati sui dati](data-driven-style-expressions-web-sdk.md).

Si consideri, ad esempio, un set di dati in cui tutte le funzionalità hanno una `isHealthy` proprietà con valore `true` o `false` . Se si crea un livello a bolle che esegue il rendering di bolle colorate diverse in base a questa proprietà, esistono diversi modi per eseguire questa operazione, come indicato di seguito, dal meno efficiente al più efficiente.

* Suddividere i dati in due origini dati in base al `isHealthy` valore e associare un livello a bolle con un'opzione di colore hardcoded a ogni origine dati.
* Inserire tutti i dati in una singola origine dati e creare due livelli Bubble con un'opzione di colore hardcoded e un filtro in base alla `isHealthy` Proprietà. 
* Inserire tutti i dati in una singola origine dati, creare un singolo livello a bolle con un' `case` espressione di stile per l'opzione Color basata sulla `isHealthy` Proprietà. Ecco un esempio di codice che dimostra questa funzionalità.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Crea animazioni a livelli di simboli uniformi

Per impostazione predefinita, il rilevamento dei conflitti è abilitato per i livelli dei simboli. Questo rilevamento dei conflitti mira a garantire che non si sovrappongano due simboli. L'icona e le opzioni di testo di un livello simboli hanno due opzioni,

* `allowOverlap` : specifica se il simbolo sarà visibile se è in conflitto con altri simboli.
* `ignorePlacement` : specifica se gli altri simboli possono entrare in conflitto con il simbolo.

Entrambe le opzioni sono impostate su per `false` impostazione predefinita. Quando si aggiunge un'animazione a un simbolo, i calcoli di rilevamento dei conflitti vengono eseguiti in ogni frame dell'animazione, che può rallentare l'animazione e renderlo meno fluido. Per smussare l'animazione, impostare queste opzioni su `true` .

Nell'esempio di codice seguente viene illustrata una procedura semplice per animare un livello di simboli.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Animazione del livello simboli" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere l' <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>animazione del livello simboli</a> penna di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="specify-zoom-level-range"></a>Specifica intervallo livelli di zoom

Se i dati soddisfano uno dei criteri seguenti, assicurarsi di specificare il livello di zoom minimo e massimo del livello, in modo che il motore di rendering possa ignorarlo al di fuori dell'intervallo del livello di zoom.

* Se i dati provengono da un'origine del riquadro vettoriale, spesso i livelli di origine per tipi di dati diversi sono disponibili solo tramite un intervallo di livelli di zoom.
* Se si usa un livello sezione che non include riquadri per tutti i livelli di zoom da 0 a 24 e si vuole che venga eseguito il rendering solo ai livelli con riquadri, non provare a compilare riquadri mancanti con riquadri da altri livelli di zoom.
* Se si vuole solo eseguire il rendering di un livello con determinati livelli di zoom.
Tutti i livelli hanno `minZoom` un' `maxZoom` opzione e in cui verrà eseguito il rendering del livello quando tra questi livelli di zoom in base a questa logica ` maxZoom > zoom >= minZoom` .

**Esempio**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Specificare i limiti del livello sezione e l'intervallo di zoom di origine

Per impostazione predefinita, i livelli del riquadro caricherà i riquadri nell'intero globo. Tuttavia, se il servizio affiancato dispone solo di riquadri per una determinata area, la mappa tenterà di caricare i riquadri al di fuori di quest'area. Quando si verifica questa situazione, viene effettuata una richiesta per ogni sezione e si attende una risposta che può bloccare altre richieste eseguite dalla mappa e quindi rallentare il rendering degli altri livelli. Se si specificano i limiti di un livello sezione, nella mappa verranno richiesti solo i riquadri che si trovano all'interno del rettangolo. Inoltre, se il livello sezione è disponibile solo tra determinati livelli di zoom, specificare lo zoom di origine min e Max per lo stesso motivo.

**Esempio**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Usa uno stile mappa vuoto quando il mapping di base non è visibile

Se un livello viene sovrapposto alla mappa che coprirà completamente la mappa di base, provare a impostare lo stile della mappa su `blank` o in `blank_accessible` modo che non venga eseguito il rendering della mappa di base. Uno scenario comune per eseguire questa operazione è quando la sovrapposizione di un riquadro globo completo in non ha un'area opaca o trasparente sopra la mappa di base.

### <a name="smoothly-animate-image-or-tile-layers"></a>Animare senza problemi livelli di immagine o affiancati

Se si desidera aggiungere un'animazione a una serie di livelli di immagine o di sezione sulla mappa. Spesso è più veloce creare un livello per ogni immagine o livello sezione e modificare l'opacità anziché aggiornare l'origine di un singolo livello in ogni frame di animazione. Per nascondere un livello, impostare l'opacità su zero e visualizzare un nuovo livello impostando la relativa opacità su un valore maggiore di zero è molto più veloce dell'aggiornamento dell'origine nel livello. In alternativa, la visibilità dei livelli può essere attivata/disattivata, ma assicurarsi di impostare la durata di dissolvenza del livello su zero. in caso contrario, il livello verrà animato al momento della visualizzazione, che genererà un effetto sfarfallio perché il livello precedente sarebbe stato nascosto prima che il nuovo livello fosse visibile.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>Modifica della logica di rilevamento collisione del livello simboli

Il livello dei simboli dispone di due opzioni disponibili per l'icona e il testo chiamati `allowOverlap` e `ignorePlacement` . Queste due opzioni specificano se l'icona o il testo di un simbolo può sovrapporsi o essere sovrapposti. Quando questi vengono impostati su `false` , il livello dei simboli eseguirà calcoli durante il rendering di ogni punto per verificare se è in conflitto con qualsiasi altro simbolo già sottoposto a rendering nel livello e, in tal caso, non eseguirà il rendering del simbolo in conflitto. Si tratta di una soluzione ideale per ridurre il disordine sulla mappa e ridurre il numero di oggetti di cui è stato eseguito il rendering. Impostando queste opzioni su `false` , questa logica di rilevamento dei conflitti verrà ignorata e verrà eseguito il rendering di tutti i simboli sulla mappa. Modificare questa opzione per ottenere la combinazione ottimale di prestazioni e esperienza utente.

### <a name="cluster-large-point-data-sets"></a>Set di dati del punto di grandi dimensioni del cluster

Quando si utilizzano set di punti dati di grandi dimensioni, è possibile che, quando viene eseguito il rendering a determinati livelli di zoom, molti dei punti si sovrappongano e siano visibili solo parzialmente. Il clustering è il processo di raggruppamento di punti che sono vicini e che li rappresentano come un singolo punto cluster. Quando l'utente esegue lo zoom della mappa in, i cluster si suddividono nei singoli punti. Questo può ridurre significativamente la quantità di dati di cui è necessario eseguire il rendering, rendere la mappa meno disordinata e migliorare le prestazioni. La `DataSource` classe dispone di opzioni per il clustering dei dati localmente. Inoltre, molti strumenti che generano riquadri vettoriali hanno opzioni di clustering.

Inoltre, aumentare le dimensioni del raggio del cluster per migliorare le prestazioni. Maggiore è il raggio del cluster, minore è il numero di punti cluster per tenere traccia e il rendering.
Per altre informazioni, vedere il documento relativo ai [dati del punto di clustering](clustering-point-data-web-sdk.md)

### <a name="use-weighted-clustered-heat-maps"></a>Usare mappe termiche in cluster ponderate

Il livello mappa termica può eseguire facilmente il rendering di decine di migliaia di punti dati. Per i set di dati di dimensioni maggiori, è consigliabile abilitare il clustering nell'origine dati e utilizzare un raggio di cluster di piccole dimensioni e utilizzare la proprietà Clusters `point_count` come peso per la mappa altezza. Quando la dimensione del raggio del cluster è limitata a pochi pixel, nella mappa termica sottoposta a rendering sarà presente una piccola differenza visiva. L'uso di un raggio di cluster più grande migliora ulteriormente le prestazioni, ma può ridurre la risoluzione della mappa termica sottoposta a rendering.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

Per altre informazioni, vedere il [clustering e le mappe termiche di questo documento](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer)

### <a name="keep-image-resources-small"></a>Mantieni risorse immagine ridotte

Le immagini possono essere aggiunte allo sprite dell'immagine Maps per il rendering delle icone in un livello di simboli o di modelli in un livello poligono. Tenere le immagini ridotte per ridurre al minimo la quantità di dati da scaricare e la quantità di spazio che essi sono presenti nello sprite dell'immagine maps. Quando si usa un livello di simboli che ridimensiona l'icona usando l' `size` opzione, usare un'immagine che corrisponde alla dimensione massima che il piano deve visualizzare sulla mappa e non più grande. In questo modo viene eseguito il rendering dell'icona con risoluzione elevata, riducendo al minimo le risorse utilizzate. Inoltre, SVG può essere usato anche come formato di file più piccolo per le immagini icona semplici.

## <a name="optimize-expressions"></a>Ottimizza espressioni

Le [espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md) offrono una grande flessibilità e potenza per filtrare e applicare uno stile ai dati sulla mappa. È possibile ottimizzare le espressioni in diversi modi. Ecco alcuni suggerimenti.

### <a name="reduce-the-complexity-of-filters"></a>Ridurre la complessità dei filtri

Filtra il ciclo su tutti i dati in un'origine dati e controlla se ogni filtro corrisponde alla logica nel filtro. Se i filtri diventano complessi, ciò può causare problemi di prestazioni. Di seguito sono riportate alcune possibili strategie per risolvere questo problema.

* Se si usano i riquadri vettoriali, suddividere i dati in livelli di origine diversi.
* Se si usa la `DataSource` classe, suddividere i dati in origini dati separate. Provare a bilanciare il numero di origini dati con la complessità del filtro. Troppe origini dati possono causare problemi di prestazioni, quindi potrebbe essere necessario eseguire alcuni test per scoprire cosa funziona meglio per lo scenario.
* Quando si utilizza un filtro complesso su un livello, è consigliabile utilizzare più livelli con espressioni di stile per ridurre la complessità del filtro. Evitare di creare un gruppo di livelli con stili hardcoded quando le espressioni di stile possono essere utilizzate come un numero elevato di livelli possono causare problemi di prestazioni.

### <a name="make-sure-expressions-dont-produce-errors"></a>Assicurarsi che le espressioni non producano errori

Le espressioni vengono spesso utilizzate per generare codice per eseguire calcoli o operazioni logiche in fase di rendering. Proprio come il codice nel resto dell'applicazione, assicurarsi che i calcoli e la logica abbiano senso e non siano soggetti a errori. Gli errori nelle espressioni causeranno problemi durante la valutazione dell'espressione, con conseguente riduzione delle prestazioni e dei problemi di rendering.

Un errore comune da tenere presente è avere un'espressione che si basa su una proprietà della funzionalità che potrebbe non esistere in tutte le funzionalità. Il codice seguente, ad esempio, usa un'espressione per impostare la proprietà Color di un livello Bubble sulla `myColor` proprietà di una funzionalità.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

Il codice precedente funzionerà correttamente se tutte le funzionalità nell'origine dati hanno una `myColor` proprietà e il valore di tale proprietà è un colore. Questo potrebbe non essere un problema se si ha il controllo completo dei dati nell'origine dati e si sa che tutte le funzionalità avranno un colore valido in una `myColor` Proprietà. Ciò premesso, per rendere questo codice sicuro dagli errori, un' `case` espressione può essere utilizzata con l' `has` espressione per verificare che la funzionalità disponga della `myColor` Proprietà. In tal caso, è `to-color` possibile utilizzare l'espressione Type per tentare di convertire il valore della proprietà in un colore. Se il colore non è valido, è possibile usare un colore di fallback. Il codice seguente illustra come eseguire questa operazione e imposta il colore di fallback su verde.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Ordinare le espressioni booleane dal più specifico al meno specifico

Quando si utilizzano espressioni booleane che contengono più test condizionali, ordinare i test condizionali dal più specifico al meno specifico. In questo modo, la prima condizione dovrebbe ridurre la quantità di dati su cui deve essere testata la seconda condizione, riducendo in tal modo il numero totale di test condizionali che è necessario eseguire.

### <a name="simplify-expressions"></a>Semplificare le espressioni

Le espressioni possono essere potenti e talvolta complesse. Più semplice è un'espressione, più veloce sarà la valutazione. Se, ad esempio, è necessario un semplice confronto, un'espressione come `['==', ['get', 'category'], 'restaurant']` sarebbe migliore rispetto all'utilizzo di un'espressione di corrispondenza come `['match', ['get', 'category'], 'restaurant', true, false]` . In questo caso, se la proprietà sottoposta a verifica è un valore booleano, un' `get` espressione sarà ancora più semplice `['get','isRestaurant']` .

## <a name="web-sdk-troubleshooting"></a>Risoluzione dei problemi di SDK Web

Di seguito sono riportati alcuni suggerimenti per eseguire il debug di alcuni problemi comuni riscontrati durante lo sviluppo con Azure Maps Web SDK.

**Perché la mappa non viene visualizzata quando si carica il controllo Web?**

Eseguire le operazioni seguenti:

* Assicurarsi di aver aggiunto le opzioni di autenticazione aggiunte alla mappa. Se questa operazione non viene aggiunta, la mappa verrà caricata con un'area di disegno vuota perché non può accedere ai dati della mappa di base senza autenticazione e 401 errori verranno visualizzati nella scheda rete degli strumenti di sviluppo del browser.
* Assicurarsi di disporre di una connessione Internet.
* Verificare la presenza di errori degli strumenti di sviluppo del browser nella console di. Alcuni errori potrebbero causare il rendering della mappa. Eseguire il debug dell'applicazione.
* Assicurarsi di usare un [browser supportato](supported-browsers.md).

**Tutti i miei dati vengono visualizzati sull'altro lato del mondo, cosa succede?**
Le coordinate, dette anche posizioni, in Azure Maps SDK sono allineate al formato standard del settore geospaziale di `[longitude, latitude]` . Questo stesso formato è anche il modo in cui le coordinate sono definite nello schema GeoJSON. i dati principali formattati usati in Azure Maps SDK. Se i dati vengono visualizzati sul lato opposto del mondo, è molto probabile che i valori di longitudine e latitudine siano invertiti nelle informazioni sulle coordinate/posizioni.

**Perché i marcatori HTML appaiono nella posizione sbagliata del controllo Web?**

Elementi da verificare:

* Se si usa il contenuto personalizzato per il marcatore, verificare `anchor` che le `pixelOffset` Opzioni e siano corrette. Per impostazione predefinita, il centro inferiore del contenuto è allineato con la posizione sulla mappa.
* Verificare che il file CSS per mappe di Azure sia stato caricato.
* Esaminare l'elemento DOM del marcatore HTML per verificare se un CSS dall'app è stato aggiunto al marcatore e influisce sulla relativa posizione.

**Perché le icone o il testo nel livello dei simboli appaiono nella posizione sbagliata?**
Verificare che le `anchor` Opzioni e `offset` siano configurate correttamente per allinearle alla parte dell'immagine o al testo che si desidera siano allineate con la coordinata sulla mappa.
Se il simbolo non è più presente quando la mappa viene ruotata, selezionare l' `rotationAlignment` opzione. Per impostazione predefinita, i simboli verranno ruotati con il viewport Maps in modo che risultino verticalmente all'utente. Tuttavia, a seconda dello scenario, potrebbe essere opportuno bloccare il simbolo all'orientamento della mappa. Per `rotationAlignment` eseguire questa operazione, impostare l'opzione su `’map’` .
Se il simbolo non è più presente quando la mappa è inclinata o inclinata, selezionare l' `pitchAlignment` opzione. Per impostazione predefinita, i simboli vengono mantenuti in verticale con il viewport Maps quando la mappa è inclinata o inclinata. Tuttavia, a seconda dello scenario, potrebbe essere opportuno bloccare il simbolo al passo della mappa. Per `pitchAlignment` eseguire questa operazione, impostare l'opzione su `’map’` .

**Perché non viene visualizzato alcun dato sulla mappa?**

Elementi da verificare:

* Verificare la presenza di errori nella console degli strumenti di sviluppo del browser.
* Verificare che l'origine dati sia stata creata e aggiunta alla mappa e che l'origine dati sia stata connessa a un livello di rendering aggiunto anche alla mappa.
* Aggiungere punti di interruzioni nel codice ed eseguire un'istruzione alla volta per assicurarsi che i dati vengano aggiunti all'origine dati e che l'origine dati e i livelli vengano aggiunti alla mappa senza errori.
* Provare a rimuovere le espressioni basate sui dati dal livello di rendering. È possibile che uno di essi possa riscontrare un errore che causa il problema.

**È possibile usare Azure Maps Web SDK in un iframe sandbox?**

Sì. Si noti che [Safari presenta un bug](https://bugs.webkit.org/show_bug.cgi?id=170075) che impedisce agli iframe in modalità sandbox di eseguire Web Worker, requisito di Azure Maps Web SDK. La soluzione consiste nell'aggiungere il `"allow-same-origin"` tag alla proprietà sandbox dell'iframe.

## <a name="get-support"></a>Supporto

Di seguito sono riportati i diversi modi per ottenere supporto per le mappe di Azure in base al problema.

**Ricerca per categorie segnalare un problema di dati o un problema relativo a un indirizzo?**

Azure Maps offre uno strumento per il feedback dei dati in cui è possibile segnalare e rilevare i problemi relativi ai dati. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) Ogni problema inviato genera un URL univoco che è possibile usare per tenere traccia dello stato di avanzamento del problema relativo ai dati. Il tempo necessario per risolvere un problema di dati varia a seconda del tipo di problema e della facilità con cui è possibile verificare che la modifica sia corretta. Una volta risolto il problema, il servizio di rendering visualizzerà l'aggiornamento nell'aggiornamento settimanale, mentre altri servizi, ad esempio la geocodifica e il routing, visualizzeranno l'aggiornamento nell'aggiornamento mensile. In questo [documento](how-to-use-feedback-tool.md)vengono fornite istruzioni dettagliate su come segnalare un problema di dati.

**Ricerca per categorie segnalare un bug in un servizio o in un'API?**

https://azure.com/support

**Dove posso ottenere assistenza tecnica per le mappe di Azure?**

Se correlato all'oggetto visivo Maps di Azure in Power BI: https://powerbi.microsoft.com/support/ per tutti gli altri servizi di Azure Maps: https://azure.com/support o i forum per sviluppatori: https://docs.microsoft.com/answers/topics/azure-maps.html

**Ricerca per categorie effettuare una richiesta di funzionalità?**

Eseguire una richiesta di funzionalità nel sito vocale dell'utente: https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti su come migliorare l'esperienza utente nell'applicazione, vedere gli articoli seguenti.

> [!div class="nextstepaction"]
> [Rendere accessibile l'applicazione](map-accessibility.md)

Scopri di più sulla terminologia usata dalle mappe di Azure e dal settore geospaziale.

> [!div class="nextstepaction"]
> [Glossario di Mappe di Azure](glossary.md)
