---
title: Domande frequenti sui servizi meteorologici di Microsoft Azure Maps (anteprima)
description: Risposte alle domande frequenti sui dati e sulle funzionalità di servizi meteorologici di Azure Maps (anteprima).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9c8e971b4fda313ffede58455dd6d057d6848ce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678130"
---
# <a name="azure-maps-weather-services-preview-frequently-asked-questions-faq"></a>Domande frequenti su servizi meteorologici di Azure Maps (anteprima)

> [!IMPORTANT]
> I servizi Meteo di Mappe di Azure sono attualmente disponibili in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo risponde a domande comuni sui dati e sulle funzionalità di [servizi meteorologici](/rest/api/maps/weather) di Azure maps. Vengono trattati i seguenti argomenti:

* Origini dati e modelli di dati
* Code coverage e disponibilità di servizi meteo
* Frequenza di aggiornamento dei dati
* Sviluppo con Azure Maps SDK
* Opzioni per la visualizzazione dei dati meteorologici, inclusa l'integrazione di Microsoft Power BI

## <a name="data-sources-and-data-models"></a>Origini dati e modelli di dati

**In che modo Azure esegue il mapping dei dati meteorologici di origine?**

Azure Maps è stato creato con la collaborazione di partner tecnologici per la mobilità e la posizione di qualità elevata, tra cui AccuWeather, che fornisce i dati meteo sottostanti. Per leggere l'annuncio della collaborazione di Azure Maps con AccuWeather, vedere [pioggia o lucentezza: i servizi meteo di Azure Maps porteranno informazioni approfondite sull'azienda](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/).

AccuWeather offre informazioni ambientali e meteorologiche in tempo reale in tutto il mondo, in gran parte a causa delle loro partnership con numerose agenzie meteorologiche governative nazionali e altre disposizioni proprietarie. Di seguito è riportato un elenco di informazioni di base.

* Osservazioni sulla superficie globale disponibili pubblicamente dalle agenzie governative
* Set di impostazioni proprietarie per l'osservazione della superficie da enti pubblici e società private
* Dati radar ad alta risoluzione per più di 40 paesi/aree geografiche
* Dati fulmine globali in tempo reale migliori
* Avvisi meteorologici emessi dal governo per più di 60 paesi/aree geografiche e territori
* Dati satellite da satelliti meteorologici geostazionari che coprono l'intero mondo
* Oltre 150 modelli di previsione numerici, tra cui la modellazione interna, proprietaria, i modelli governativi come il sistema di previsione globale (GFS) degli Stati Uniti e i modelli downscaling univoci offerti dalle società private
* Osservazioni sulla qualità dell'aria
* Osservazioni dei reparti del trasporto

Decine di migliaia di osservazioni sulla superficie, insieme ad altri dati, sono incorporati per creare e influenzare le condizioni correnti rese disponibili agli utenti. Sono inclusi non solo i set di impostazioni di tipo standard disponibili gratuitamente, ma anche osservazioni univoche ottenute da servizi meteorologici nazionali in molti paesi o aree geografiche, tra cui India, Brasile, Canada e altri input proprietari. Questi set di dati univoci aumentano la risoluzione spaziale e temporale dei dati della condizione corrente per gli utenti. 

Questi set di dati vengono esaminati in tempo reale per l'accuratezza del sistema di previsione digitale, che usa gli algoritmi di intelligenza artificiale proprietari di AccuWeather per modificare continuamente le previsioni, garantendo sempre l'integrazione dei dati più recenti e ottimizzando così la precisione continua.

**Quali modelli creano i dati delle previsioni meteorologiche?**

Per formulare previsioni globali vengono usati numerosi sistemi di linee guida sulle previsioni meteorologiche. Oltre 150 modelli di previsione numerici vengono utilizzati ogni giorno, sia i set di impostazioni esterni sia quelli interni. Sono inclusi i modelli governativi come il centro europeo ECMWF e il sistema di previsione globale degli Stati Uniti (GFS). Inoltre, AccuWeather incorpora modelli proprietari ad alta risoluzione che downscaling previsioni a posizioni specifiche e a domini regionali strategici per prevedere il meteo con maggiore precisione. Gli algoritmi di fusione e ponderazione univoci di AccuWeather sono stati sviluppati negli ultimi decenni. Questi algoritmi usano in modo ottimale i numerosi input di previsione per fornire previsioni estremamente accurate.

## <a name="weather-services-preview-coverage-and-availability"></a>Code coverage e disponibilità di servizi meteorologici (anteprima)

**Quale tipo di copertura è possibile prevedere per paesi/aree geografiche diverse?**

La copertura del servizio meteo varia in base al paese. Tutte le funzionalità non sono disponibili in ogni paese/area geografica. Per ulteriori informazioni, vedere la [documentazione relativa al code coverage](./weather-coverage.md).

## <a name="data-update-frequency"></a>Frequenza di aggiornamento dei dati

**Con quale frequenza vengono aggiornati i dati delle condizioni correnti?**

I dati delle condizioni correnti vengono aggiornati approssimativamente almeno una volta all'ora, ma possono essere aggiornati più spesso con condizioni che cambiano rapidamente, ad esempio modifiche di temperatura elevata, modifiche alle condizioni del cielo, modifiche di precipitazione e così via. La maggior parte delle stazioni di osservazione nel mondo segnala diverse volte all'ora quando le condizioni cambiano. Tuttavia, alcune aree continueranno a essere aggiornate solo una volta, due volte o quattro volte all'ora a intervalli pianificati.  

Mappe di Azure memorizza nella cache i dati delle condizioni correnti per un massimo di 10 minuti, per consentire di acquisire la frequenza di aggiornamento quasi in tempo reale dei dati quando si verifica. Per visualizzare la scadenza della risposta memorizzata nella cache ed evitare la visualizzazione di dati obsoleti, è possibile sfruttare le informazioni di intestazione Expires nell'intestazione HTTP della risposta API Maps di Azure.

**Con quale frequenza vengono aggiornati i dati giornalieri e relativi alle previsioni orarie?**

I dati giornalieri e relativi alle previsioni orarie vengono aggiornati più volte al giorno, perché vengono ricevute le osservazioni aggiornate.  Se ad esempio una temperatura elevata o bassa prevista viene superata, i dati delle previsioni si adatteranno al successivo ciclo di aggiornamento. Questa situazione può verificarsi a intervalli diversi, ma in genere si verifica entro un'ora. Molte condizioni meteorologiche improvvise possono causare una modifica dei dati di previsione. Ad esempio, in un pomeriggio estivo caldo, un thunderstorm isolato può improvvisamente emergere, con una copertura e una pioggia abbondanti nel cloud. Il Storm isolato può effettivamente eliminare la temperatura di 10 gradi. Questo nuovo valore di temperatura influirà sulle previsioni orarie e giornaliere per il resto del giorno e, di conseguenza, verrà aggiornato nei set di impostazioni.

Le API previsioni delle mappe di Azure vengono memorizzate nella cache per un massimo di 30 minuti. Per visualizzare la scadenza della risposta memorizzata nella cache ed evitare la visualizzazione di dati obsoleti, è possibile sfruttare le informazioni di intestazione Expires nell'intestazione HTTP della risposta API Maps di Azure. Si consiglia di eseguire l'aggiornamento in base a un caso d'uso specifico del prodotto e all'interfaccia utente (interfaccia utente).

## <a name="developing-with-azure-maps-sdks"></a>Sviluppo con Azure Maps SDK

**Azure Maps Web SDK supporta in modo nativo l'integrazione di servizi meteorologici (anteprima)?**

Azure Maps Web SDK fornisce un modulo dei servizi. Il modulo Services è una libreria helper che semplifica l'uso dei servizi REST di Maps di Azure nelle applicazioni Web o Node.js. utilizzando JavaScript o TypeScript. Per iniziare, vedere la [documentazione](./how-to-use-services-module.md).

**Azure Maps Android SDK supporta in modo nativo l'integrazione di servizi meteorologici (anteprima)?**

Azure Maps Android SDK supporta i livelli di riquadri Mercator, che possono avere la notazione x/y/zoom, la notazione con chiave quad o la notazione del riquadro delimitatore EPSG 3857.

Si prevede di creare un modulo di servizi per Java/Android simile al modulo Web SDK. Il modulo Servizi Android renderà più semplice l'accesso a tutti i servizi di Azure Maps in un'app Java o Android.  

## <a name="data-visualizations"></a>Visualizzazioni di dati  

**Azure Maps Power BI supporto visivo Azure Maps Weather Tiles?**

Sì. Per informazioni su come eseguire la migrazione di riquadri radar e satelliti infrarossi all'oggetto visivo Microsoft Power BI, vedere [aggiungere un livello sezione a Power Bi oggetto visivo](./power-bi-visual-add-tile-layer.md). 

**Ricerca per categorie interpretare i colori usati per i riquadri radar e satellite?**

L'articolo relativo al [concetto meteorologico](./weather-services-concepts.md#radar-and-satellite-imagery-color-scale) di mappe di Azure include una guida che consente di interpretare i colori usati per i riquadri radar e satellite. Questo articolo illustra i campioni di colore e i codici colore ESADECIMALi.
 
**È possibile creare animazioni con riquadro radar e satellite?**

Sì. Oltre al radar in tempo reale e ai riquadri satellite, Azure Maps i clienti possono richiedere riquadri passati e futuri per migliorare le visualizzazioni dei dati con le sovrapposizioni della mappa. Questa operazione può essere eseguita chiamando direttamente [Get Map Tile V2 API](/rest/api/maps/renderv2/getmaptilepreview) o richiedendo riquadri tramite Azure Maps Web SDK. I riquadri radar sono disponibili per un massimo di 1,5 ore in passato e per un massimo di 2 ore in futuro. I riquadri e sono disponibili in intervalli di 5 minuti. I riquadri infrarossi vengono forniti per un massimo di 3 ore in passato e sono disponibili in intervalli di 10 minuti. Per altre informazioni, vedere l' [esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer)di animazione del riquadro meteorologico open source.  

**Sono disponibili icone per le diverse condizioni meteorologiche?**

Sì. [Qui](./weather-services-concepts.md#weather-icons)è possibile trovare le icone e i rispettivi codici. Si noti che solo alcune API del servizio meteo (anteprima), ad esempio  [Get Current Conditions API](/rest/api/maps/weather/getcurrentconditionspreview), restituiscono *iconCode* nella risposta. Per altre informazioni, vedere l' [esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location)Open Source WeatherConditions corrente.

## <a name="next-steps"></a>Passaggi successivi

Se queste domande frequenti non rispondono alla domanda, è possibile contattare Microsoft tramite i seguenti canali (in ordine di escalation):

* Sezione dei commenti di questo articolo.
* [MSFT Q&una pagina per le mappe di Azure](/answers/topics/azure-maps.html).
* Supporto tecnico Microsoft. Per creare una nuova richiesta di supporto, nella [portale di Azure](https://portal.azure.com/)della scheda Guida selezionare il pulsante **Guida** e supporto e quindi selezionare **nuova richiesta di supporto**.
* [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps) per inviare richieste di funzionalità.

Informazioni su come richiedere dati meteorologici in tempo reale e previsione usando i servizi meteo di Azure Maps (anteprima):
> [!div class="nextstepaction"]
> [Richiedi dati meteorologici in tempo reale ](how-to-request-weather-data.md)

Articolo sui concetti relativi ai servizi meteorologici di Azure Maps (anteprima):
> [!div class="nextstepaction"]
> [Concetti dei servizi meteo](weather-coverage.md)

Esplorare la documentazione dell'API servizi meteorologici di Azure Maps (anteprima):

> [!div class="nextstepaction"]
> [Servizi meteo di Mappe di Azure](/rest/api/maps/weather)