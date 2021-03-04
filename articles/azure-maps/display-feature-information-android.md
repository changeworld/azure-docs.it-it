---
title: Visualizzare le informazioni sulle funzionalità nelle mappe Android | Mappe Microsoft Azure
description: Informazioni su come visualizzare le informazioni quando gli utenti interagiscono con le funzionalità della mappa. Usare il Android SDK mappe di Azure per visualizzare i messaggi di tipo avviso popup e altri tipi di messaggi.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 4e84bd821d53048b134db635c7ec541db74fbf11
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047715"
---
# <a name="display-feature-information"></a>Visualizzare informazioni sulle funzionalità

I dati spaziali vengono spesso rappresentati tramite punti, linee e poligoni. A questi dati sono spesso associate informazioni sui metadati. Ad esempio, un punto può rappresentare la posizione di un ristorante e i metadati relativi a tale ristorante possono essere il nome, l'indirizzo e il tipo di cibo che serve. Questi metadati possono essere aggiunti come proprietà di un GeoJSON `Feature` . Il codice seguente crea una funzionalità di punto semplice con una `title` proprietà con valore "Hello World!"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

Vedere la documentazione [creare un'origine dati](create-data-source-android-sdk.md) per i modi in cui creare e aggiungere dati alla mappa.

Quando un utente interagisce con una funzionalità sulla mappa, gli eventi possono essere usati per rispondere a tali azioni. Uno scenario comune consiste nel visualizzare un messaggio composto dalle proprietà dei metadati di una funzionalità con cui l'utente ha eseguito l'interazione. L' `OnFeatureClick` evento è l'evento principale utilizzato per rilevare quando l'utente ha toccato una funzionalità sulla mappa. Esiste anche un `OnLongFeatureClick` evento. Quando si aggiunge l' `OnFeatureClick` evento alla mappa, può essere limitato a un singolo livello passando l'ID di un livello per limitarlo a. Se non viene passato alcun ID livello, toccando qualsiasi funzionalità sulla mappa, indipendentemente dal livello in cui si trova, questo evento verrà generato. Il codice seguente crea un livello di simboli per il rendering dei dati del punto sulla mappa, quindi aggiunge un `OnFeatureClick` evento e lo limita a questo livello di simbolo.

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Visualizza un messaggio di avviso popup

Un messaggio di avviso popup è uno dei modi più semplici per visualizzare le informazioni all'utente ed è disponibile in tutte le versioni di Android. Non supporta alcun tipo di input dell'utente e viene visualizzato solo per un breve periodo di tempo. Se si desidera consentire all'utente di conoscere rapidamente gli elementi che hanno toccato, un messaggio di avviso popup potrebbe essere un'opzione appropriata. Nel codice seguente viene illustrato come è possibile utilizzare un messaggio di avviso popup con l' `OnFeatureClick` evento.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![Animazione di una funzionalità toccata e visualizzazione di un messaggio popup](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Oltre ai messaggi di avviso popup, esistono molti altri modi per presentare le proprietà dei metadati di una funzionalità, ad esempio:

- [Widget snackbar](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` fornire feedback leggero su un'operazione. Visualizzano un breve messaggio nella parte inferiore della schermata in dispositivi mobili e in basso a sinistra nei dispositivi più grandi. `Snackbars` vengono visualizzati sopra tutti gli altri elementi sullo schermo e ne può essere visualizzato solo uno alla volta.
- Finestre di [dialogo](https://developer.android.com/guide/topics/ui/dialogs) : una finestra di dialogo è una piccola finestra in cui viene richiesto all'utente di prendere una decisione o immettere informazioni aggiuntive. Una finestra di dialogo non riempie lo schermo e viene in genere usata per gli eventi modali che richiedono agli utenti di eseguire un'azione prima di poter continuare.
- Aggiungere un [frammento](https://developer.android.com/guide/components/fragments) all'attività corrente.
- Passare a un'altra attività o a una vista.

## <a name="display-a-popup"></a>Visualizzazione di un popup

Azure Maps Android SDK fornisce una `Popup` classe che semplifica la creazione di elementi di annotazione dell'interfaccia utente ancorati a una posizione sulla mappa. Per i popup è necessario passare una visualizzazione con un layout relativo nell' `content` opzione del popup. Di seguito è riportato un esempio di layout semplice in cui viene visualizzato il testo scuro sopra a uno sfondo.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

Supponendo che il layout precedente venga archiviato in un file denominato `popup_text.xml` nella `res -> layout` cartella di un'app, il codice seguente crea un popup, lo aggiunge alla mappa. Quando si fa clic su una funzionalità, la `title` proprietà viene visualizzata utilizzando il `popup_text.xml` layout, con il centro inferiore del layout ancorato alla posizione specificata sulla mappa.

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Access the text view within the custom view and set the text to the title property of the feature.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText(props.get("title").getAsString());

    //Get the coordinates from the clicked feature and create a position object.
    List<Double> c = ((Point)(f.geometry())).coordinates();
    Position pos = new Position(c.get(0), c.get(1));

    //Set the options on the popup.
    popup.setOptions(
        //Set the popups position.
        position(pos),

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),

        //Set the content of the popup.
        content(customView)

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    );

    //Open the popup.
    popup.open();
});

```

L'acquisizione di schermate seguente mostra i popup visualizzati quando si fa clic sulle funzionalità e si rimane ancorati alla posizione specificata sulla mappa mentre viene spostata.

![Animazione di un popup visualizzato e della mappa spostata con il popup ancorato a una posizione sulla mappa](./media/display-feature-information-android/android-popup.gif)

## <a name="next-steps"></a>Passaggi successivi

Per aggiungere altri dati alla mappa:

> [!div class="nextstepaction"]
> [Reagire per eseguire il mapping degli eventi](android-map-events.md)

> [!div class="nextstepaction"]
> [Creare un'origine dati](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](how-to-add-shapes-to-android-map.md)
