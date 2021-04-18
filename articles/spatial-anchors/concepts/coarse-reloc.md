---
title: Rilocalizzazione grossolana
description: Informazioni su come e quando usare la rilocalizzazione grosso modo. La rilocalizzazione grosso modo consente di trovare ancoraggi nelle vicinanze.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: d2737f58fa95d1aa45d9952e8b501c1b9be4d1b0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600353"
---
# <a name="coarse-relocalization"></a>Rilocalizzazione grossolana

La rilocalizzazione grosso modo è una funzionalità che consente la localizzazione su larga scala fornendo una risposta approssimativa ma rapida a queste domande: 
- *Dove si trova il dispositivo?* 
- *Quale contenuto è necessario osservare?* 
 
La risposta non è precisa. È in questo formato: *si è vicini a questi ancoraggi. Provare a individuarne uno.*

La rilocalizzazione grosso modo funziona contrassegnando gli ancoraggi con varie letture del sensore sul dispositivo che verranno usate in seguito per l'esecuzione di query rapide. Per gli scenari esterni, i dati del sensore sono in genere la posizione GPS (Global Positioning System) del dispositivo. Quando il GPS non è disponibile o inaffidabile, ad esempio quando si è all'interno, i dati del sensore sono costituiti dai punti di accesso Wi-Fi e dai fari Bluetooth in gamma. I dati del sensore raccolti contribuiscono a mantenere un indice spaziale usato da Ancoraggi nello stato di Azure per determinare rapidamente quali ancoraggi sono vicini al dispositivo.

## <a name="when-to-use-coarse-relocalization"></a>Quando usare la rilocalizzazione grosso modo

Se si prevede di gestire più di 35 ancoraggi spaziali in uno spazio più grande di un campo da tennis, probabilmente si trarrà vantaggio dall'indicizzazione spaziale di rilocalizzazione grossossa.

La ricerca rapida degli ancoraggi abilitati dalla rilocalizzazione grosso modo è progettata per semplificare lo sviluppo di applicazioni supportate da raccolte su scala globale di milioni di ancoraggi con distribuzione geografica. La complessità dell'indicizzazione spaziale è tutta nascosta, quindi è possibile concentrarsi sulla logica dell'applicazione. Tutto il lavoro difficile viene eseguito dietro le quinte da Ancoraggi nello stato di Azure.

## <a name="using-coarse-relocalization"></a>Uso della rilocalizzazione grosso modo

Ecco il flusso di lavoro tipico per creare ed eseguire query su Ancoraggi nello spaziali di Azure con una rilocalizzazione grosso modo grosso modo:
1.  Creare e configurare un provider di impronte digitali del sensore per raccogliere i dati del sensore desiderati.
2.  Avviare una sessione di Ancoraggi nello stato di Azure e creare gli ancoraggi. Poiché l'impronta digitale del sensore è abilitata, gli ancoraggi vengono indicizzati dal punto di vista spaziale tramite rilocalizzazione grossosa.
3.  Eseguire query su ancoraggi intorno usando la rilocalizzazione grosso modo tramite i criteri di ricerca dedicati nella sessione Ancoraggi nello spazio.

È possibile fare riferimento a una di queste esercitazioni per configurare la rilocalizzazione grossosa nell'applicazione:
* [Rilocalizzazione grossolana in Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Rilocalizzazione grossolana in Objective-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Rilocalizzazione grossolana in Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Rilocalizzazione grossolana in Java](../how-tos/set-up-coarse-reloc-java.md)
* [Rilocalizzazione grossolana in C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Rilocalizzazione grossolana in C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensori e piattaforme

### <a name="platform-availability"></a>Disponibilità della piattaforma

È possibile inviare questi tipi di dati del sensore al servizio di ancoraggio:

* Posizione GPS: latitudine, longitudine, altitudine
* Livello di segnale dei Wi-Fi di accesso nell'intervallo
* Signal strength of Bluetooth beacons in range

Questa tabella riepiloga la disponibilità dei dati dei sensori nelle piattaforme supportate e fornisce informazioni di cui tenere conto:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | N.<sup>1</sup>  | Sì<sup>2</sup> | Sì<sup>3</sup> |
| **Wi-Fi**        | Sì<sup>4</sup> | Sì<sup>5</sup> | No  |
| **BLE beacons** | Sì<sup>6</sup> | Sì<sup>6</sup> | Sì<sup>6</sup>|


<sup>1</sup> Un dispositivo GPS esterno può essere associato a HoloLens. Contattare [il supporto](../spatial-anchor-support.md) tecnico se si è disposti a usare HoloLens con un tracciatore GPS.<br/>
<sup>2</sup> Supportato tramite le API [LocationManager][3] (GPS e NETWORK).<br/>
<sup>3</sup> Supportato tramite le API [CLLocationManager.][4]<br/>
<sup>4</sup> Supportato a una frequenza di circa un'analisi ogni 3 secondi. <br/>
<sup>5</sup> A partire dal livello API 28, le Wi-Fi vengono limitate a quattro chiamate ogni 2 minuti. A partire da Android 10, è possibile disabilitare questa limitazione dal menu **Impostazioni sviluppatore.** Per altre informazioni, vedere la [documentazione di Android.][5]<br/>
<sup>6</sup> Limitato a [Eddystone][1] [e iBeacon][2].

### <a name="which-sensor-to-enable"></a>Sensore da abilitare

La scelta del sensore dipende dall'applicazione che si sta sviluppando e dalla piattaforma.
Questo diagramma fornisce un punto di partenza per determinare quale combinazione di sensori è possibile abilitare, a seconda dello scenario di localizzazione:

![Diagramma che mostra i sensori abilitati per vari scenari.](media/coarse-relocalization-enabling-sensors.png)

Le sezioni seguenti forniscono informazioni più dettagliate sui vantaggi e sulle limitazioni di ogni tipo di sensore.

### <a name="gps"></a>GPS

GPS è l'opzione go-to per gli scenari esterni.
Quando si usa il GPS nell'applicazione, tenere presente che le letture fornite dall'hardware sono in genere:

* Frequenza asincrona e bassa (minore di 1 Hz).
* Inaffidabile/rumoroso (in media, deviazione standard di 7 m).

In generale, sia il sistema operativo del dispositivo che gli ancoraggi spaziali esereranno alcune operazioni di filtro ed estrapolazione del segnale GPS non elaborato nel tentativo di attenuare questi problemi. Questa elaborazione aggiuntiva richiede tempo per la convergenza, quindi, per ottenere risultati ottimali, è consigliabile provare a:

* Creare un provider di impronte digitali del sensore il prima possibile nell'applicazione.
* Mantenere attivo il provider di impronte digitali del sensore tra più sessioni.
* Condividere il provider di impronte digitali del sensore tra più sessioni.

I dispositivi GPS di livello consumer sono in genere imprecisi. Uno studio di [Zandenbergen e Barbeau (2011)][6] segnala che l'accuratezza mediana dei telefoni cellulari con GPS assistito (A-GPS) è di circa 7 metri. Si tratta di un valore piuttosto grande da ignorare. Per considerare questi errori di misurazione, il servizio considera gli ancoraggi come distribuzioni di probabilità nello spazio GPS. Un ancoraggio è quindi l'area di spazio che più probabilmente (con un'attendibilità superiore al 95%) contiene la posizione GPS vera e sconosciuta.

Lo stesso ragionamento si applica quando si esegue una query tramite GPS. Il dispositivo è rappresentato come un'altra area di confidenza spaziale intorno alla posizione GPS vera e sconosciuta. L'individuazione degli ancoraggi nelle vicinanze si  traduce nel trovare gli ancoraggi con aree di attendibilità abbastanza vicine all'area di attendibilità del dispositivo, come illustrato di seguito:

![Diagramma che illustra la ricerca di candidati di ancoraggio tramite GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

In HoloLens e Android la Wi-Fi del segnale può essere un buon modo per abilitare la rilocalizzazione grossole dell'interno.
Il vantaggio è la potenziale disponibilità immediata dei punti Wi-Fi (comuni negli spazi degli uffici e nei negozi, ad esempio) senza alcuna configurazione aggiuntiva.

> [!NOTE]
> iOS non fornisce un'API per la lettura Wi-Fi del segnale, quindi non può essere usato per la rilocalizzazione grossolata abilitata tramite Wi-Fi.

Quando si usano Wi-Fi nell'applicazione, tenere presente che le letture fornite dall'hardware sono in genere:

* Frequenza asincrona e bassa (minore di 0,1 Hz).
* Potenzialmente limitato a livello di sistema operativo.
* Inaffidabile/rumoroso (deviazione standard media di 3 dBm).

Gli ancoraggi nello stato spaziale proveranno a creare una mappa filtrata di Wi-Fi del segnale durante una sessione nel tentativo di attenuare questi problemi. Per ottenere risultati ottimali, provare a:

* Creare la sessione prima di posizionare il primo ancoraggio.
* Mantenere la sessione attiva il più a lungo possibile. Ovvero, creare tutti gli ancoraggi ed eseguire query in un'unica sessione.

### <a name="bluetooth-beacons"></a>Bluetooth beacons
<a name="beaconsDetails"></a>

Un'attenta distribuzione dei fari Bluetooth è una buona soluzione per scenari di rilocalizzazione grossolano di interni su larga scala, in cui il GPS è assente o impreciso. È anche l'unico metodo interno supportato in tutte e tre le piattaforme.

I beacon sono in genere dispositivi versatili in cui è possibile configurare tutti gli elementi, inclusi gli UUID e gli indirizzi MAC. Ancoraggi nello stato di Azure prevede che i fari siano identificati in modo univoco dagli UUID. Se non si garantisce questa univocità, si otterrà probabilmente risultati non corretti. Per risultati migliori:

* Assegnare UUID univoci ai fari.
* Distribuire i fari in modo da coprire lo spazio in modo uniforme e in modo che almeno tre fari siano raggiungibili da qualsiasi punto dello spazio.
* Passare l'elenco di UUID di beacon univoci al provider di impronte digitali del sensore.

I segnali radio come quelli del Bluetooth sono interessati da ostacoli e possono interferire con altri segnali radio. Può quindi essere difficile indovinare se lo spazio è coperto in modo uniforme. Per garantire una migliore esperienza del cliente, è consigliabile testare manualmente la copertura dei fari. È possibile eseguire un test aggirando lo spazio con i dispositivi candidati e un'applicazione che mostra Bluetooth in gamma. Durante il test della copertura, assicurarsi di poter raggiungere almeno tre fari da qualsiasi posizione strategica nel proprio spazio. La presenza di troppi fari può comportare un'interferenza maggiore tra di essi e non migliora necessariamente l'accuratezza della rilocalizzazione grossosca.

I fari Bluetooth coprono in genere 80 metri se non sono presenti ostacoli nello spazio.
Pertanto, per uno spazio senza ostacoli di grandi dimensioni, è possibile distribuire i fari in un modello di griglia ogni 40 metri.

Un faro che sta scaricando la batteria influirà sui risultati, quindi assicurarsi di monitorare periodicamente la distribuzione per le batterie basse o non caricate.

Ancoraggi nello stato di Azure tiene traccia solo dei fari Bluetooth presenti nell'elenco UUID di prossimità dei fari noti. Ma i fari dannosi programmati per avere UUID consentiti possono influire negativamente sulla qualità del servizio. Si otterrà quindi i risultati migliori in spazi curati in cui è possibile controllare la distribuzione dei beacon.

### <a name="sensor-accuracy"></a>Accuratezza del sensore

L'accuratezza del segnale GPS, sia durante la creazione dell'ancoraggio che durante le query, influisce in modo significativo sul set di ancoraggi restituiti. Al contrario, le query basate su Wi-Fi/beacon considereranno tutti gli ancoraggi che hanno almeno un punto di accesso/beacon in comune con la query. In questo senso, il risultato di una query basata su Wi-Fi/beacon è determinato principalmente dall'intervallo fisico dei punti di accesso/dei beacon e dalle ostruzione ambientale.
Questa tabella stima lo spazio di ricerca previsto per ogni tipo di sensore:

| Sensore      | Raggio dello spazio di ricerca (approssimativo) | Dettagli |
|-------------|:-------:|---------|
| **GPS**         | Da 20 m a 30 m | Determinato dall'incertezza GPS, tra gli altri fattori. I numeri segnalati sono stimati per l'accuratezza GPS mediana dei telefoni cellulari con GPS A: 7 metri. |
| **Wi-Fi**        | Da 50 m a 100 m | Determinato dall'intervallo dei punti di accesso wireless. Dipende da frequenza, forza di trasmissione, ostruzione fisica, interferenza e così via. |
| **BLE beacons** |  70 m | Determinato dall'intervallo del beacon. Dipende da frequenza, potenza di trasmissione, ostruzione fisica, interferenza e così via. |

<!-- Reference links in article -->
[1]: https://developer.estimote.com/eddystone/
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
