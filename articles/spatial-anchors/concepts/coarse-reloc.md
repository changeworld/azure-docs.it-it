---
title: Rilocalizzazione grossolana
description: Informazioni sull'uso della rilocalizzazione grossolana per trovare gli ancoraggi più vicini.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: fc04242e61c748d7ae52e61e40206ba338a1b6aa
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071138"
---
# <a name="coarse-relocalization"></a>Rilocalizzazione grossolana

La rilocalizzazione grossolana è una funzionalità che consente la localizzazione su larga scala fornendo una risposta approssimativa ma rapida alla domanda: *dove si trova il mio dispositivo/quale contenuto è opportuno osservare?* La risposta non è precisa, ma è nel formato: *si è vicini a questi ancoraggi; provare a individuarne uno*.

La rilocalizzazione approssimativa funziona contrassegnando gli ancoraggi con varie letture di sensori sul dispositivo che vengono usate in seguito per eseguire query veloci. Per gli scenari esterni, i dati del sensore sono in genere la posizione GPS (Global Positioning System) del dispositivo. Quando il GPS non è disponibile o non è affidabile (ad esempio, in ingresso), i dati del sensore sono costituiti dai punti di accesso Wi-Fi e dai beacon Bluetooth nell'intervallo. I dati dei sensori raccolti contribuiscono alla gestione di un indice spaziale usato dagli ancoraggi spaziali di Azure per determinare rapidamente quali ancoraggi sono in prossimità del dispositivo.

## <a name="when-to-use-coarse-relocalization"></a>Quando usare la rilocalizzazione approssimativa

Se si prevede di gestire più di 35 ancoraggi spaziali in uno spazio maggiore rispetto a un tribunale, è probabile che si tragga vantaggio dall'indicizzazione spaziale per la rilocalizzazione grossolana.

La ricerca rapida di ancoraggi abilitati dalla rilocalizzazione grossolana è progettata per semplificare lo sviluppo di applicazioni supportate da raccolte su scala globale di ancoraggi (ad affermare milioni di ancoraggi con distribuzione geografica). La complessità dell'indicizzazione spaziale è completamente nascosta, consentendo di concentrarsi sulla logica dell'applicazione. Tutto il lifting di ancoraggio viene eseguito automaticamente dietro le quinte dagli ancoraggi spaziali di Azure.

## <a name="using-coarse-relocalization"></a>Uso della rilocalizzazione approssimativa

Il flusso di lavoro tipico per creare ed eseguire query sugli ancoraggi spaziali di Azure con la rilocalizzazione grossolana è:
1.  Creare e configurare un provider di impronte digitali del sensore per raccogliere i dati dei sensori scelti.
2.  Avviare una sessione di ancoraggio spaziale di Azure e creare ancoraggi. Poiché l'impronta digitale dei sensori è abilitata, gli ancoraggi vengono indicizzati a livello spaziale in base alla rilocalizzazione grossolana.
3.  Eseguire una query sugli ancoraggi circostanti usando la rilocalizzazione grossolana, usando i criteri di ricerca dedicati nella sessione di ancoraggio spaziale di Azure.

È possibile fare riferimento all'esercitazione seguente per configurare la rilocalizzazione grossolana nell'applicazione:
* [Rilocalizzazione grossolana in Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Rilocalizzazione grossolana in Objective-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Rilocalizzazione grossolana in Swift](../how-tos/set-up-coarse-reloc-swift.md)
* [Rilocalizzazione grossolana in Java](../how-tos/set-up-coarse-reloc-java.md)
* [Rilocalizzazione grossolana in C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Rilocalizzazione grossolana in C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Sensori e piattaforme

### <a name="platform-availability"></a>Disponibilità piattaforma

I tipi di dati dei sensori che è possibile inviare al servizio di ancoraggio sono:

* Posizione GPS: Latitudine, Longitudine, altitudine.
* Potenza del segnale dei punti di accesso Wi-Fi nell'intervallo.
* Livello di attendibilità dei segnali Bluetooth nell'intervallo.

La tabella seguente riepiloga la disponibilità dei dati dei sensori sulle piattaforme supportate, insieme a eventuali avvertenze specifiche della piattaforma:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Nessun<sup>1</sup>  | Sì<sup>2</sup> | Sì<sup>3</sup> |
| **Wi-Fi**        | Sì<sup>4</sup> | Sì<sup>5</sup> | NO  |
| **Beacon BLE** | Sì<sup>6</sup> | Sì<sup>6</sup> | Sì<sup>6</sup>|


<sup>1</sup> un dispositivo GPS esterno può essere associato a HoloLens. Se si vuole usare HoloLens con un tracker GPS, contattare [il supporto tecnico](../spatial-anchor-support.md) .<br/>
<sup>2</sup> supportato tramite le API [LocationManager][3] (GPS e rete)<br/>
<sup>3</sup> supportati tramite le API [CLLocationManager][4]<br/>
<sup>4</sup> supportato con una frequenza di circa un'analisi ogni 3 secondi <br/>
<sup>5</sup> a partire dal livello API 28, le analisi Wi-Fi sono limitate a 4 chiamate ogni 2 minuti. Da Android 10, la limitazione può essere disabilitata dal menu impostazioni sviluppatore. Per ulteriori informazioni, vedere la [documentazione di Android][5].<br/>
<sup>6</sup> limitato a [Eddystone][1] e [Impedisci iBeacon][2]

### <a name="which-sensor-to-enable"></a>Sensore da abilitare

La scelta del sensore è specifica dell'applicazione che si sta sviluppando e della piattaforma.
Il diagramma seguente fornisce un punto di partenza in cui è possibile abilitare la combinazione di sensori a seconda dello scenario di localizzazione:

![Diagramma della selezione dei sensori abilitati](media/coarse-relocalization-enabling-sensors.png)

Le sezioni seguenti forniscono informazioni dettagliate sui vantaggi e sulle limitazioni per ogni tipo di sensore.

### <a name="gps"></a>GPS

Il GPS è l'opzione ideale per gli scenari esterni.
Quando si usa il GPS nell'applicazione, tenere presente che le letture fornite dall'hardware sono in genere:

* asincrona e bassa frequenza (minore di 1 Hz).
* non affidabile/rumoroso (in media deviazione standard 7-m).

In generale, il sistema operativo del dispositivo e gli ancoraggi spaziali di Azure eseguiranno alcune operazioni di filtro e estrapolazione sul segnale GPS non elaborato nel tentativo di attenuare questi problemi. Questa elaborazione aggiuntiva richiede tempo per la convergenza, quindi, per ottenere risultati ottimali, provare a:

* creare un provider di impronte digitali del sensore il prima possibile nell'applicazione
* Mantieni attivo il provider di impronte digitali del sensore tra più sessioni
* condividere il provider di impronte digitali del sensore tra più sessioni

I dispositivi GPS di livello consumer sono in genere imprecisi. Uno studio di [Zandenbergen e Barbeau (2011)][6] segnala l'accuratezza mediana dei telefoni cellulari con il GPS assistito (a-GPS) a circa 7 metri, un valore piuttosto elevato da ignorare. Per tenere conto di questi errori di misurazione, il servizio considera gli ancoraggi come distribuzioni di probabilità nello spazio GPS. Di conseguenza, un ancoraggio è ora l'area di spazio che più probabilmente, ovvero con una confidenza superiore al 95%, contiene la relativa posizione GPS vera e sconosciuta.

Lo stesso ragionamento viene applicato quando si esegue una query con il GPS. Il dispositivo è rappresentato come un'altra area di confidenza spaziale attorno alla sua vera e propria posizione GPS sconosciuta. L'individuazione degli ancoraggi vicini si traduce nella semplice ricerca di ancoraggi con aree di confidenza *sufficientemente vicine* all'area di confidenza del dispositivo, come illustrato nell'immagine seguente:

![Selezione di candidati di ancoraggio con GPS](media/coarse-reloc-gps-separation-distance.png)

### <a name="wifi"></a>WiFi

In HoloLens e Android, il livello di attendibilità del segnale Wi-Fi può essere una scelta efficace per abilitare la rilocalizzazione grossolana interna.
Il suo vantaggio è la potenziale disponibilità immediata dei punti di accesso Wi-Fi (comune in, ad esempio, gli spazi di Office o i centri commerciali) senza che sia necessaria una configurazione aggiuntiva.

> [!NOTE]
> iOS non fornisce alcuna API per la lettura del livello di attendibilità del segnale Wi-Fi e, di conseguenza, non può essere usato per la rilocalizzazione grossolana abilitata per la

Quando si usa il Wi-Fi nell'applicazione, tenere presente che le letture fornite dall'hardware sono in genere:

* asincrona e bassa frequenza (inferiore a 0,1 Hz).
* potenzialmente limitato a livello di sistema operativo.
* non affidabile/rumoroso (in media, la deviazione standard 3-dBm).

Gli ancoraggi spaziali di Azure tenterà di creare una mappa del livello di attendibilità del segnale Wi-Fi filtrato durante una sessione nel tentativo di attenuare questi problemi. Per ottenere risultati ottimali, provare a:

* creare correttamente la sessione prima di posizionare il primo ancoraggio.
* Mantieni la sessione attiva per il periodo di tempo più lungo possibile, ovvero crea tutti gli ancoraggi e le query in una sessione.

### <a name="bluetooth-beacons"></a>Beacon Bluetooth
<a name="beaconsDetails"></a>

La distribuzione con attenzione di Bluetooth Beacon è una soluzione efficace per scenari di ridistribuzione grossolani interni su larga scala, in cui il GPS è assente o non accurato. È anche l'unico metodo interno supportato su tutte e tre le piattaforme.

I beacon sono in genere dispositivi versatili, in cui è possibile configurare tutti gli elementi, inclusi UUID e indirizzi MAC. Gli ancoraggi spaziali di Azure prevedono che i beacon siano identificati in modo univoco dai rispettivi UUID. Il mancato assicurando che questa unicità provochi probabilmente risultati non corretti. Per ottenere risultati ottimali, è necessario:

* assegnare UUID univoci ai beacon.
* è possibile distribuirli in modo uniforme per lo spazio, in modo che almeno 3 Beacon siano raggiungibili da qualsiasi punto nello spazio.
* passare l'elenco degli UUID del Beacon univoco al provider di impronte digitali del sensore

I segnali radio come il Bluetooth sono interessati da ostacoli e possono interferire con altri segnali radio. Per questi motivi può essere difficile indovinare se lo spazio è coperto in modo uniforme. Per garantire un'esperienza utente migliore, è consigliabile testare manualmente la copertura dei beacon. A tale scopo, è possibile aggirare lo spazio con i dispositivi candidati e un'applicazione che mostra il Bluetooth nell'intervallo. Durante il test della copertura, verificare che sia possibile raggiungere almeno 3 beacon da qualsiasi posizione strategica dello spazio. La configurazione di un numero eccessivo di Beacon può comportare una maggiore interferenza tra di essi e non comporta necessariamente un miglioramento dell'accuratezza della rilocalizzazione.

I beacon Bluetooth in genere hanno una copertura di 80 metri se non sono presenti ostacoli nello spazio.
Ciò significa che per uno spazio senza grandi ostacoli, è possibile distribuire i beacon su un modello di griglia ogni 40 metri.

Un faro che esaurisce la batteria influirà negativamente sui risultati, quindi assicurarsi di monitorare periodicamente la distribuzione per le batterie insufficienti o scariche.

Gli ancoraggi spaziali di Azure rileveranno solo i beacon Bluetooth presenti nell'elenco UUID di prossimità di Beacon noti. I beacon dannosi programmati in modo da consentire gli UUID elencati possono influire negativamente sulla qualità del servizio. Per questo motivo, si otterranno i migliori risultati negli spazi curati in cui è possibile controllare la distribuzione.

### <a name="sensors-accuracy"></a>Accuratezza sensori

L'accuratezza del segnale GPS, sia nella creazione di ancoraggio che durante le query, ha un'influenza notevole sul set di ancoraggi restituiti. Al contrario, le query basate su Wi-Fi/Beacon considereranno tutti gli ancoraggi con almeno un punto di accesso/Beacon in comune con la query. In questo senso, il risultato di una query basata su Wi-Fi/Beacon è determinato principalmente dall'intervallo fisico dei punti di accesso/Beacon e dalle ostruzioni ambientali.
Nella tabella seguente viene stimato lo spazio di ricerca previsto per ogni tipo di sensore:

| Sensore      | Raggio dello spazio di ricerca (circa) | Dettagli |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | Determinato dall'incertezza GPS tra gli altri fattori. I numeri segnalati sono stimati per l'accuratezza GPS mediana dei telefoni cellulari con A-GPS, ovvero 7 contatori. |
| WiFi        | 50 m-100 m | Determinato dall'intervallo dei punti di accesso wireless. Dipende dalla frequenza, dalla potenza del trasmettitore, dalle ostruzioni fisiche, dall'interferenza e così via. |
| Beacon BLE |  70 m | Determinato dall'intervallo del Beacon. Dipende dalla frequenza, dalla potenza di trasmissione, dagli ostacoli fisici, dall'interferenza e così via. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
