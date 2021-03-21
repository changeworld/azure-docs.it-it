---
title: Rilocalizzazione grossolana
description: Informazioni su come e quando usare la rilocalizzazione approssimativa. La rilocalizzazione grossolana consente di trovare ancoraggi più vicini.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656176"
---
# <a name="coarse-relocalization"></a>Rilocalizzazione grossolana

La rilocalizzazione grossolana è una funzionalità che consente la localizzazione su larga scala fornendo una risposta approssimativa ma rapida a queste domande: 
- *Dove si trova ora il dispositivo?* 
- *Quali contenuti è opportuno osservare?* 
 
La risposta non è precisa. È in questo formato: *si è vicini a questi ancoraggi. Provare a individuarne uno*.

La rilocalizzazione approssimativa funziona contrassegnando gli ancoraggi con varie letture di sensori sul dispositivo che vengono usate in seguito per eseguire query veloci. Per gli scenari esterni, i dati del sensore sono in genere la posizione GPS (Global Positioning System) del dispositivo. Quando il GPS non è disponibile o non è affidabile, ad esempio quando si è indoor, i dati del sensore sono costituiti dai punti di accesso Wi-Fi e dai beacon Bluetooth nell'intervallo. I dati dei sensori raccolti contribuiscono alla gestione di un indice spaziale usato dagli ancoraggi spaziali di Azure per determinare rapidamente quali ancoraggi sono vicini al dispositivo.

## <a name="when-to-use-coarse-relocalization"></a>Quando usare la rilocalizzazione approssimativa

Se si prevede di gestire più di 35 ancoraggi spaziali in uno spazio di dimensioni maggiori rispetto a un tribunale, probabilmente si trarrà vantaggio dall'indicizzazione spaziale per la rilocalizzazione grossolana.

La ricerca rapida di ancoraggi abilitati dalla rilocalizzazione grossolana è progettata per semplificare lo sviluppo di applicazioni supportate da raccolte su scala mondiale di, ad affermare milioni di ancoraggi con distribuzione geografica. La complessità dell'indicizzazione spaziale è nascosta, quindi è possibile concentrarsi sulla logica dell'applicazione. Tutto il lavoro difficile viene eseguito dietro le quinte dagli ancoraggi spaziali di Azure.

## <a name="using-coarse-relocalization"></a>Uso della rilocalizzazione approssimativa

Di seguito è riportato il flusso di lavoro tipico per creare ed eseguire query su ancoraggi spaziali di Azure con una rilocalizzazione grossolana:
1.  Creare e configurare un provider di impronte digitali dei sensori per raccogliere i dati del sensore desiderati.
2.  Avviare una sessione di ancoraggi spaziali di Azure e creare gli ancoraggi. Poiché l'impronta digitale dei sensori è abilitata, gli ancoraggi vengono indicizzati a livello spaziale in base alla rilocalizzazione grossolana.
3.  Eseguire una query sugli ancoraggi circostanti usando la rilocalizzazione grossolana tramite i criteri di ricerca dedicati nella sessione di ancoraggi spaziali.

È possibile fare riferimento a una di queste esercitazioni per configurare la rilocalizzazione grossolana nell'applicazione:
* [Rilocalizzazione grossolana in Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Rilocalizzazione grossolana in Objective-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Rilocalizzazione grossolana in Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Rilocalizzazione grossolana in Java](../how-tos/set-up-coarse-reloc-java.md)
* [Rilocalizzazione grossolana in C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Rilocalizzazione grossolana in C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensori e piattaforme

### <a name="platform-availability"></a>Disponibilità piattaforma

È possibile inviare questi tipi di dati del sensore al servizio di ancoraggio:

* Posizione GPS: Latitudine, Longitudine, altitudine
* Attendibilità del segnale dei punti di accesso Wi-Fi nell'intervallo
* Potenza del segnale dei beacon Bluetooth nell'intervallo

In questa tabella viene riepilogata la disponibilità dei dati dei sensori sulle piattaforme supportate e vengono fornite informazioni che è necessario conoscere:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Nessun<sup>1</sup>  | Sì<sup>2</sup> | Sì<sup>3</sup> |
| **Wi-Fi**        | Sì<sup>4</sup> | Sì<sup>5</sup> | No  |
| **Beacon BLE** | Sì<sup>6</sup> | Sì<sup>6</sup> | Sì<sup>6</sup>|


<sup>1</sup> un dispositivo GPS esterno può essere associato a HoloLens. Se si vuole usare HoloLens con un tracker GPS, contattare [il supporto tecnico](../spatial-anchor-support.md) .<br/>
<sup>2</sup> supportato tramite le API [LOCATIONMANAGER][3] (sia GPS che Network).<br/>
<sup>3</sup> supportato tramite le API [CLLocationManager][4] .<br/>
<sup>4</sup> supportato con una frequenza di circa un'analisi ogni 3 secondi. <br/>
<sup>5</sup> a partire dal livello API 28, Wi-Fi analisi sono limitate a quattro chiamate ogni 2 minuti. A partire da Android 10, è possibile disabilitare questa limitazione dal menu **Impostazioni sviluppatore** . Per ulteriori informazioni, vedere la [documentazione di Android][5].<br/>
<sup>6</sup> limitato a [Eddystone][1] e [Impedisci iBeacon][2].

### <a name="which-sensor-to-enable"></a>Sensore da abilitare

La scelta del sensore varia a seconda dell'applicazione che si sta sviluppando e della piattaforma.
Questo diagramma costituisce un punto di partenza per determinare la combinazione di sensori che è possibile abilitare, a seconda dello scenario di localizzazione:

![Diagramma che mostra i sensori abilitati per diversi scenari.](media/coarse-relocalization-enabling-sensors.png)

Le sezioni seguenti forniscono informazioni più dettagliate sui vantaggi e le limitazioni di ogni tipo di sensore.

### <a name="gps"></a>GPS

Il GPS è l'opzione ideale per gli scenari esterni.
Quando si usa il GPS nell'applicazione, tenere presente che le letture fornite dall'hardware sono in genere:

* Asincrona e bassa frequenza (minore di 1 Hz).
* Inaffidabile/rumoroso (in media, deviazione standard 7-m).

In generale, il sistema operativo del dispositivo e gli ancoraggi spaziali eseguiranno un filtro e un'estrapolazione del segnale GPS non elaborato nel tentativo di attenuare questi problemi. Questa elaborazione aggiuntiva richiede tempo per la convergenza, quindi, per ottenere risultati ottimali, è consigliabile provare a:

* Creare un provider di impronte digitali del sensore il prima possibile nell'applicazione.
* Mantieni attivo il provider di impronte digitali del sensore tra più sessioni.
* Condividere il provider di impronte digitali del sensore tra più sessioni.

I dispositivi GPS di livello consumer sono in genere imprecisi. Uno studio di [Zandenbergen e Barbeau (2011)][6] segnala che l'accuratezza mediana dei telefoni cellulari che hanno assistito con GPS (A-GPS) è circa 7 metri. Questo è un valore piuttosto elevato da ignorare. Per tenere conto di questi errori di misurazione, il servizio considera gli ancoraggi come distribuzioni di probabilità nello spazio GPS. Un ancoraggio è quindi l'area di spazio più probabile (con una confidenza superiore al 95%) che contiene la propria posizione GPS sconosciuta.

Lo stesso ragionamento si applica quando si esegue una query tramite GPS. Il dispositivo è rappresentato come un'altra area di confidenza spaziale attorno alla sua vera e propria posizione GPS sconosciuta. L'individuazione degli ancoraggi vicini si traduce per individuare gli ancoraggi con aree di confidenza *sufficientemente vicine* all'area di confidenza del dispositivo, come illustrato di seguito:

![Diagramma che illustra la ricerca di candidati di ancoraggio usando GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

In HoloLens e Android Wi-Fi livello di attendibilità del segnale può essere un modo efficace per abilitare la rilocalizzazione grossolana interna.
Il vantaggio è la potenziale disponibilità immediata di punti di accesso Wi-Fi (comune negli spazi di Office e nei centri commerciali, ad esempio) senza che sia necessaria alcuna configurazione aggiuntiva.

> [!NOTE]
> iOS non fornisce un'API per la lettura Wi-Fi la forza del segnale, quindi non può essere usata per la rilocalizzazione grossolana abilitata tramite Wi-Fi.

Quando si usa Wi-Fi nell'applicazione, tenere presente che le letture fornite dall'hardware sono in genere:

* Asincrona e bassa frequenza (inferiore a 0,1 Hz).
* Potenzialmente limitato a livello di sistema operativo.
* Inaffidabile/rumoroso (in media, deviazione standard a 3 dBm).

Gli ancoraggi spaziali proveranno a creare una mappa filtrata di Wi-Fi la forza del segnale durante una sessione nel tentativo di attenuare questi problemi. Per ottenere risultati ottimali, provare a:

* Creare correttamente la sessione prima di inserire il primo ancoraggio.
* Mantieni la sessione attiva per il periodo di tempo più lungo possibile. Ovvero creare tutti gli ancoraggi e le query in un'unica sessione.

### <a name="bluetooth-beacons"></a>Beacon Bluetooth
<a name="beaconsDetails"></a>

Una distribuzione attenta dei beacon Bluetooth è una buona soluzione per scenari di ridistribuzione grossolani interni su larga scala, in cui il GPS è assente o non accurato. È anche l'unico metodo interno supportato su tutte e tre le piattaforme.

I beacon sono in genere dispositivi versatili in cui è possibile configurare tutti gli elementi, inclusi UUID e indirizzi MAC. Gli ancoraggi spaziali di Azure prevedono che i beacon siano identificati in modo univoco dai rispettivi UUID. Se non si garantisce l'univocità, è probabile che si ottengano risultati errati. Per risultati migliori:

* Assegnare UUID univoci ai beacon.
* Distribuire i beacon in un modo che copra lo spazio in modo uniforme e che almeno tre Beacon siano raggiungibili da qualsiasi punto nello spazio.
* Passare l'elenco degli UUID del Beacon univoco al provider di impronte digitali del sensore.

I segnali radio come quelli del Bluetooth sono interessati da ostacoli e possono interferire con altri segnali radio. Quindi, può essere difficile indovinare se lo spazio è coperto in modo uniforme. Per garantire un'esperienza migliore per i clienti, è consigliabile testare manualmente la copertura dei beacon. Per eseguire un test, è possibile aggirare lo spazio con i dispositivi candidati e un'applicazione che mostra il Bluetooth nell'intervallo. Durante il test della copertura, verificare che sia possibile raggiungere almeno tre Beacon da qualsiasi posizione strategica nello spazio. La presenza di un numero eccessivo di Beacon può comportare una maggiore interferenza tra di essi e non necessariamente migliorare l'accuratezza della rilocalizzazione grossolana.

I beacon Bluetooth in genere coprono 80 metri se non sono presenti ostacoli nello spazio.
Quindi, per uno spazio senza ostacoli di grandi dimensioni, è possibile distribuire i beacon in un modello di griglia ogni 40 metri.

Un faro che sta esaurendo la batteria influirà sui risultati, quindi assicurarsi di monitorare periodicamente la distribuzione per le batterie a bassa o non caricate.

Gli ancoraggi spaziali di Azure rileveranno solo i beacon Bluetooth presenti nell'elenco UUID di prossimità di Beacon noto. Tuttavia, i beacon dannosi programmati per avere UUID allowlisted possono influire negativamente sulla qualità del servizio. Quindi, otterrai i migliori risultati negli spazi curati in cui è possibile controllare la distribuzione di Beacon.

### <a name="sensor-accuracy"></a>Accuratezza del sensore

L'accuratezza del segnale GPS, sia durante la creazione dell'ancoraggio che durante le query, ha un'influenza significativa sul set di ancoraggi restituiti. Al contrario, le query basate su Wi-Fi/Beacon considereranno tutti gli ancoraggi con almeno un punto di accesso/Beacon in comune con la query. In questo senso, il risultato di una query basata su Wi-Fi/Beacon è determinato principalmente dall'intervallo fisico dei punti di accesso/Beacon e dalle ostruzioni ambientali.
Questa tabella stima lo spazio di ricerca previsto per ogni tipo di sensore:

| Sensore      | Ricerca-raggio dello spazio (approssimativa) | Dettagli |
|-------------|:-------:|---------|
| **GPS**         | da 20 a 30 m | Determinato dall'incertezza GPS, tra gli altri fattori. I numeri segnalati sono stimati per l'accuratezza GPS mediana dei telefoni cellulari con A-GPS: 7 contatori. |
| **Wi-Fi**        | 50 m a 100 m | Determinato dall'intervallo dei punti di accesso wireless. Dipende dalla frequenza, dalla potenza del trasmettitore, dalle ostruzioni fisiche, dall'interferenza e così via. |
| **Beacon BLE** |  70 m | Determinato dall'intervallo del Beacon. Dipende dalla frequenza, dalla potenza di trasmissione, dagli ostacoli fisici, dall'interferenza e così via. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
