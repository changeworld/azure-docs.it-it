---
title: Esercitazione per C# sull'ordinamento dei risultati
titleSuffix: Azure Cognitive Search
description: Questa esercitazione per C# illustra come ordinare i risultati della ricerca. Si basa su un progetto di hotel precedente, applicando l'ordine in base a proprietà primaria e proprietà secondaria, e include un profilo di assegnazione di punteggi per aggiungere i criteri di aumento priorità.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1f8100dd6340383eadec5d10b7f23db59ba0ebdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786386"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Esercitazione: Ordinare i risultati della ricerca con .NET SDK

In questa serie di esercitazioni i risultati sono stati restituiti e visualizzati in un [ordine predefinito](index-add-scoring-profiles.md#what-is-default-scoring). In questa esercitazione si aggiungeranno i criteri di ordinamento primario e secondario. In alternativa all'ordinamento basato su valori numerici, nell'esempio finale viene illustrato come classificare i risultati in base a un profilo di punteggio personalizzato. Verrà anche illustrata in dettaglio la visualizzazione dei _tipi complessi_.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Ordinare i risultati in base a un'unica proprietà
> * Ordinare i risultati in base a più proprietà
> * Ordinare i risultati in base a un profilo di punteggio

## <a name="overview"></a>Panoramica

Questa esercitazione estende il progetto di scorrimento infinto creato nell'esercitazione [Aggiungere la paginazione ai risultati della ricerca](tutorial-csharp-paging.md).

Una versione completa del codice di questa esercitazione si trova nel progetto seguente:

* [5-order-results (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Prerequisiti

* Soluzione [2b-add-infinite-scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll). Questo progetto può essere una versione personalizzata, completata nell'esercitazione precedente, oppure una copia di GitHub.

L'esercitazione è stata aggiornata per usare il pacchetto [Azure.Search.Documents (versione 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Per una versione precedente di .NET SDK, vedere l'[esempio di codice Microsoft.Azure.Search (versione 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="order-results-based-on-one-property"></a>Ordinare i risultati in base a un'unica proprietà

Quando si ordinano i risultati in base a una proprietà, ad esempio la valutazione di un hotel, non basta che i risultati siano ordinati, ma bisogna anche avere la conferma che l'ordine sia corretto. Aggiungendo il campo rating ai risultati è possibile verificare che i risultati siano ordinati correttamente.

In questo esercizio si aggiungeranno anche altre informazioni alla visualizzazione dei risultati per ogni hotel, ovvero la tariffa più bassa e quella più alta per camera.

Per abilitare l'ordinamento, non è necessario modificare alcun modello. Solo la visualizzazione e il controller richiedono aggiornamenti. Per iniziare, aprire il controller home.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Aggiungere la proprietà OrderBy ai parametri di ricerca

1. In HomeController. cs aggiungere l'opzione **OrderBy** e includere la proprietà rating con un ordinamento decrescente. Nel metodo **Index(SearchData model)** aggiungere la riga seguente ai parametri di ricerca.

    ```cs
    options.OrderBy.Add("Rating desc");
    ```

    >[!Note]
    > L'ordine predefinito è crescente, sebbene sia possibile aggiungerlo `asc` alla proprietà per renderlo chiaro. L'ordine decrescente viene specificato aggiungendo `desc` .

1. A questo punto, eseguire l'app e immettere un qualsiasi termine di ricerca comune. I risultati possono o meno essere visualizzati nell'ordine corretto, dal momento che né lo sviluppatore né l'utente possono verificarli facilmente.

1. Bisogna quindi chiarire che i risultati sono ordinati in base alla valutazione. Sostituire innanzitutto le classi **box1** e **box2** nel file hotels.css con le classi seguenti, completamente nuove e necessarie per questa esercitazione.

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > I browser in genere memorizzano i file CSS nella cache. Può quindi capitare che venga usato un file CSS obsoleto e che le modifiche apportate vengano ignorate. Una soluzione valida per ovviare a questo problema consiste nell'aggiungere al collegamento una stringa di query con un parametro version. Ad esempio:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Aggiornare il numero di versione se si ritiene che il browser usi un file CSS obsoleto.

1. Aggiungere la proprietà **rating** al parametro **Select** , nel metodo **index (modello SearchData)** in modo che i risultati includano i tre campi seguenti:

    ```cs
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Rating");
    ```

1. Aprire la visualizzazione (index.cshtml) e sostituire il ciclo di rendering ( **&lt;!-- Show the hotel data. --&gt;** ) con il codice seguente.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var ratingText = $"Rating: {result[i].Document.Rating}";

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. La valutazione deve essere disponibile sia nella prima pagina visualizzata che nelle pagine successive, che vengono chiamate tramite lo scorrimento infinito. Nella seconda di queste due situazioni, è necessario aggiornare sia l'azione **Next** nel controller che la funzione **scrolled** nella visualizzazione. Iniziando con il controller, modificare il metodo **Next** sostituendolo con il codice seguente. Questo codice consente di creare e comunicare il testo della valutazione.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
    
            string fullDescription = result.Document.Description;
    
            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. A questo punto, aggiornare la funzione **scrolled** nella visualizzazione in modo da visualizzare il testo della valutazione.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 3) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Eseguire di nuovo l'app. Cercare un termine comune, ad esempio "wifi", e verificare che i risultati vengano visualizzati in ordine decrescente in base alla valutazione dell'albergo.

    ![Ordinamento basato sulla valutazione](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Si noterà che la valutazione è identica per diversi alberghi, di conseguenza anche in questo caso vengono visualizzati nell'ordine arbitrario in cui sono stati trovati i dati.

    Prima di vedere come aggiungere un secondo livello di ordinamento, è necessario aggiungere il codice per visualizzare le tariffe delle camere. Questo codice viene aggiunto non solo per illustrare l'estrazione dei dati da un _tipo complesso_, ma anche per esaminare l'ordinamento dei risultati in base al prezzo (probabilmente il più economico per primo).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Aggiungere le tariffe delle camere alla visualizzazione

1. Aggiungere proprietà con le tariffe più alte e più basse al modello Hotel.cs.

    ```cs
    // Room rate range
    public double cheapest { get; set; }
    public double expensive { get; set; }
    ```

1. Calcolare le tariffe delle camere alla fine dell'azione **Index(SearchData model)** , nel controller home. Aggiungere i calcoli dopo l'archiviazione dei dati temporanei.

    ```cs
    // Ensure TempData is stored for the next call.
    TempData["page"] = page;
    TempData["searchfor"] = model.searchText;

    // Calculate the room rate ranges.
    await foreach (var result in model.resultList.GetResultsAsync())
    {
        var cheapest = 0d;
        var expensive = 0d;

        foreach (var room in result.Document.Rooms)
        {
            var rate = room.BaseRate;
            if (rate < cheapest || cheapest == 0)
            {
                cheapest = (double)rate;
            }
            if (rate > expensive)
            {
                expensive = (double)rate;
            }
        }
        model.resultList.Results[n].Document.cheapest = cheapest;
        model.resultList.Results[n].Document.expensive = expensive;
    }
    ```

1. Aggiungere la proprietà **Rooms** al parametro **Select** nel metodo dell'azione **Index(SearchData model)** del controller.

    ```cs
    options.Select.Add("Rooms");
    ```

1. Modificare il ciclo di rendering nella visualizzazione in modo da visualizzare le tariffe per la prima pagina di risultati.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Modificare il metodo **Next** nel controller home per comunicare le tariffe per le pagine di risultati successive.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";

            string fullDescription = result.Document.Description;

            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(rateText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Aggiornare la funzione **scrolled** nella visualizzazione, per gestire il testo delle tariffe delle camere.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 4) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Eseguire l'app e verificare che le tariffe delle camere siano visualizzate.

    ![Visualizzazione delle tariffe delle camere](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

La proprietà **OrderBy** dei parametri di ricerca non accetterà una voce come **Rooms.BaseRate** per fornire la tariffa più bassa, neanche se le camere fossero già ordinate in base alla tariffa. In questo caso, le camere non sono ordinate in base alla tariffa. Per visualizzare gli alberghi nel set di dati di esempio, ordinati in base alla tariffa della camera, è necessario ordinare i risultati nel controller home e inviarli alla visualizzazione nell'ordine desiderato.

## <a name="order-results-based-on-multiple-values"></a>Ordinare i risultati in base a più valori

La questione è ora come distinguere tra alberghi con la stessa valutazione. Un approccio potrebbe essere un ordinamento secondario basato sull'ultima volta che l'hotel è stato rinnovato, in modo che gli alberghi rinnovati più di recente vengano visualizzati più in alto nei risultati.

1. Per aggiungere un secondo livello di ordinamento, aggiungere **LastRenovationDate** ai risultati della ricerca e a **OrderBy** nel metodo **index (modello SearchData)** .

    ```cs
    options.Select.Add("LastRenovationDate");

    options.OrderBy.Add("LastRenovationDate desc");
    ```

    >[!Tip]
    > Nell'elenco **OrderBy** è possibile aggiungere un numero qualsiasi di proprietà. Nel caso in cui gli alberghi abbiano la stessa valutazione e siano stati ristrutturati nello stesso periodo, è possibile aggiungere una terza proprietà per distinguerli ulteriormente.

1. Anche in questo caso, per essere sicuri che l'ordinamento sia corretto, è necessario che la data di ristrutturazione sia inclusa nella visualizzazione. Per una cosa come un rinnovo, probabilmente solo l'anno è sufficiente. Modificare il ciclo di rendering nella visualizzazione sostituendolo con il codice seguente.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var lastRenovatedText = $"Last renovated: { result[i].Document.LastRenovationDate.Value.Year}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Modificare il metodo **Next** nel controller home per trasferire il componente dell'anno della data dell'ultima ristrutturazione.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var ratingText = $"Rating: {result.Document.Rating}";
                var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
                var lastRenovatedText = $"Last renovated: {result.Document.LastRenovationDate.Value.Year}";

                string fullDescription = result.Document.Description;

                // Add strings to the list.
                nextHotels.Add(result.Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(fullDescription);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Modificare la funzione **scrolled** nella visualizzazione in modo da visualizzare il testo relativo alla ristrutturazione.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 5) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Eseguire l'app. Cercare un termine comune, come "pool" o "view", quindi verificare che gli alberghi con la stessa valutazione siano ora elencati in ordine decrescente in base alla data di ristrutturazione.

    ![Ordinamento in base alla data di ristrutturazione](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="order-results-based-on-a-scoring-profile"></a>Ordinare i risultati in base a un profilo di punteggio

Gli esempi forniti nell'esercitazione illustrano finora come ordinare in base a valori numerici (classificazione e data di rinnovo), fornendo una sequenza _esatta_ di ordinamento. Tuttavia, con alcune ricerche e alcuni dati non è semplice eseguire un confronto tra due elementi dati. Per le query di ricerca full-text, ricerca cognitiva include il concetto di _rango_. I _profili di Punteggio_ possono essere specificati per influenzare il modo in cui i risultati sono classificati, fornendo confronti più complessi e qualitativi.

I [profili di Punteggio](index-add-scoring-profiles.md) sono definiti nello schema dell'indice. Per i dati degli alberghi sono stati configurati diversi profili di punteggio. Verrà ora esaminato come definire un profilo di punteggio e provare a scrivere codice per eseguire ricerche basate sui profili.

### <a name="how-scoring-profiles-are-defined"></a>Modalità di definizione dei profili di punteggio

I profili di punteggio sono definiti in un indice di ricerca in fase di progettazione. L'indice degli Alberghi di sola lettura ospitato da Microsoft dispone di tre profili di punteggio. In questa sezione vengono esaminati i profili di punteggio e viene illustrato come utilizzare nel codice.

1. Di seguito è riportato il profilo di Punteggio predefinito per il set di dati degli alberghi, utilizzato quando non si specifica alcun parametro **OrderBy** o **ScoringProfile** . Questo profilo consente di incrementare il _punteggio_ di un albergo se il testo cercato è presente nel nome, nella descrizione o nell'elenco di tag (servizi) di un albergo. Notare come i pesi del punteggio favoriscono determinati campi. Se il testo da cercare è presente in un altro campo, non elencato di seguito, avrà peso pari a 1. Ovviamente, più elevato è il punteggio, maggiore sarà la visualizzazione di un risultato.

     ```cs
    {
        "name": "boostByField",
        "text": {
            "weights": {
                "Tags": 3,
                "HotelName": 2,
                "Description": 1.5,
                "Description_fr": 1.5,
            }
        }
    }
    ```

1. Il profilo di Punteggio alternativo seguente aumenta significativamente il punteggio se un parametro fornito include uno o più elenchi di tag (che vengono chiamati "servizi"). L'aspetto fondamentale di questo profilo è che è _necessario_ specificare un parametro che contiene testo. Se il parametro è vuoto o non viene specificato, verrà generato un errore.

    ```cs
    {
        "name":"boostAmenities",
        "functions":[
            {
            "fieldName":"Tags",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":null,
            "distance":null,
            "tag":{
                "tagsParameter":"amenities"
            },
            "type":"tag",
            "boost":5
            }
        ],
        "functionAggregation":0
    },
    ```

1. In questo terzo profilo, la classificazione degli hotel offre un aumento significativo del punteggio. Anche la data dell'ultima ristrutturazione contribuisce al punteggio, ma solo se risale a meno di 730 giorni (2 anni) dalla data corrente.

    ```cs
    {
        "name":"renovatedAndHighlyRated",
        "functions":[
            {
            "fieldName":"Rating",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":{
                "boostingRangeStart":0,
                "boostingRangeEnd":5,
                "constantBoostBeyondRange":false
            },
            "distance":null,
            "tag":null,
            "type":"magnitude",
            "boost":20
            },
            {
            "fieldName":"LastRenovationDate",
            "freshness":{
                "boostingDuration":"P730D"
            },
            "interpolation":"quadratic",
            "magnitude":null,
            "distance":null,
            "tag":null,
            "type":"freshness",
            "boost":10
            }
        ],
        "functionAggregation":0
    }
    ```

    A questo punto, è possibile vedere se questi profili funzionano come previsto.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Aggiungere codice alla visualizzazione per confrontare i profili

1. Aprire il file index.cshtml e sostituire la sezione &lt;body&gt; con il codice seguente.

    ```html
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. Aprire il file SearchData.cs e sostituire la classe **SearchData** con il codice seguente.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. Aprire il file hotels.css e aggiungere le classi HTML seguenti.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Aggiungere codice al controller per specificare un profilo di punteggio

1. Aprire il file del controller home. Aggiungere l'istruzione **using** seguente per facilitare la creazione di elenchi.

    ```cs
    using System.Linq;
    ```

1. Per questo esempio è necessario che la chiamata iniziale a **Index** non si limiti a restituire la visualizzazione iniziale. Il metodo ora cerca fino a 20 servizi da includere nella visualizzazione.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchOptions options = new SearchOptions();
            // Search for up to 20 amenities.
            options.Facets.Add("Tags,count:20");

            SearchResults<Hotel> searchResult = await _searchClient.SearchAsync<Hotel>("*", options);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. Sono necessari due metodi privati, uno per salvare i facet nell'archivio temporaneo e l'altro per ripristinarli dall'archivio temporaneo e popolare un modello.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. È necessario impostare i parametri **OrderBy** e **ScoringProfile** secondo necessità. Sostituire il metodo **Index(SearchData model)** esistente con il codice seguente.

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Recover the facet text, and the facet check box settings.
                RecoverFacets(model, true);

                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First search with text. 
                // Recover the facet text, but ignore the check box settings, and use the current model settings.
                RecoverFacets(model, false);

                // First call. Check for valid text input, and valid scoring profile.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                if (model.scoring == null)
                {
                    model.scoring = "Default";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true,
            };
            // Select the data properties to be returned.
            options.Select.Add("HotelName");
            options.Select.Add("Description");
            options.Select.Add("Tags");
            options.Select.Add("Rooms");
            options.Select.Add("Rating");
            options.Select.Add("LastRenovationDate");

            List<string> parameters = new List<string>();
            // Set the ordering based on the user's radio button selection.
            switch (model.scoring)
            {
                case "RatingRenovation":
                    // Set the ordering/scoring parameters.
                    options.OrderBy.Add("Rating desc");
                    options.OrderBy.Add("LastRenovationDate desc");
                    break;

                case "boostAmenities":
                    {
                        options.ScoringProfile = model.scoring;

                        // Create a string list of amenities that have been clicked.
                        for (int a = 0; a < model.facetOn.Length; a++)
                        {
                            if (model.facetOn[a])
                            {
                                parameters.Add(model.facetText[a]);
                            }
                        }

                        if (parameters.Count > 0)
                        {
                            options.ScoringParameters.Add($"amenities-{ string.Join(',', parameters)}");
                        }
                        else
                        {
                            // No amenities selected, so set profile back to default.
                            options.ScoringProfile = "";
                        }
                    }
                    break;

                case "renovatedAndHighlyRated":
                    options.ScoringProfile = model.scoring;
                    break;

                default:
                    break;
            }

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options);

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
            TempData["scoring"] = model.scoring;
            SaveFacets(model, true);

            // Calculate the room rate ranges.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var cheapest = 0d;
                var expensive = 0d;

                foreach (var room in result.Document.Rooms)
                {
                    var rate = room.BaseRate;
                    if (rate < cheapest || cheapest == 0)
                    {
                        cheapest = (double)rate;
                    }
                    if (rate > expensive)
                    {
                        expensive = (double)rate;
                    }
                }

                result.Document.cheapest = cheapest;
                result.Document.expensive = expensive;
            }
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Leggere attentamente i commenti relativi a ognuna delle selezioni di **switch**.

1. Non è necessario apportare modifiche all'azione **Next** se è stato completato il codice aggiuntivo per la sezione precedente sull'ordinamento basato su più proprietà.

### <a name="run-and-test-the-app"></a>Eseguire e testare l'app

1. Eseguire l'app. La visualizzazione includerà un set completo di servizi.

1. Per l'ordinamento, se si seleziona "By numerical Rating" verrà applicato l'ordinamento numerico già implementato in questa esercitazione, in cui la data di ristrutturazione consente di decidere tra alberghi con la stessa valutazione.

   ![Ordinamento di "beach" basato sulla valutazione](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Provare ora il profilo "By amenities". Effettuare varie selezioni di servizi e verificare che gli alberghi con questi servizi occupino una posizione di rilievo nell'elenco risultati.

   ![Ordinamento di "beach" basato sul profilo](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Provare il profilo "By Renovated date/Rating profile" per verificare se i risultati sono quelli previsti. Solo agli alberghi ristrutturati di recente dovrebbe essere assegnata una priorità _freshness_.

### <a name="resources"></a>Risorse

Per altre informazioni, vedere l'articolo seguente [Aggiungere profili di punteggio a un indice di Ricerca cognitiva di Azure](./index-add-scoring-profiles.md).

## <a name="takeaways"></a>Risultati

Tenere conto delle considerazioni seguenti riguardo a questo progetto:

* Gli utenti si aspettano che i risultati della ricerca siano ordinati e che quelli più pertinenti siano visualizzati per primi.
* I dati devono essere strutturati in modo da semplificare l'ordinamento. Non è stato possibile ordinare facilmente prima in base alla tariffa più bassa perché i dati non sono strutturati per consentire l'ordinamento senza codice aggiuntivo.
* Possono esistere più livelli di ordinamento, per distinguere tra i risultati che hanno lo stesso valore a un livello di ordinamento superiore.
* Per alcuni risultati viene applicato naturalmente l'ordine crescente, ad esempio la distanza da un punto, mentre per altri l'ordine decrescente, ad esempio la valutazione degli ospiti.
* È possibile definire profili di punteggio quando i confronti numerici per un set di dati non sono disponibili o abbastanza efficaci. L'assegnazione di un punteggio a ogni risultato consente di ordinare e visualizzare i risultati in modo intelligente.

## <a name="next-steps"></a>Passaggi successivi

Questa serie di esercitazioni per C# è stata completata. A questo punto sono state acquisite conoscenze preziose sulle API di Ricerca cognitiva di Azure.

Per altre informazioni di riferimento ed esercitazioni, provare a esplorare [Microsoft Learn](/learn/browse/?products=azure) o a completare le altre esercitazioni disponibili nella [documentazione di Ricerca cognitiva di Azure](./index.yml).