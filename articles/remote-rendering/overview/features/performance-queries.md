---
title: Query sulle prestazioni lato server
description: Come eseguire query sulle prestazioni lato server con chiamate API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 30b8104a9596f0b32f731c507b513b204f5d1acd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594096"
---
# <a name="server-side-performance-queries"></a>Query sulle prestazioni lato server

Ottenere prestazioni di rendering ottimali sul server è essenziale per avere frequenze dei fotogrammi stabili e offrire agli utenti un'esperienza soddisfacente. È importante monitorare con attenzione le caratteristiche delle prestazioni sul server e ottimizzare laddove necessario. È possibile eseguire query sui dati sulle prestazioni usando funzioni API dedicate.

L'elemento di maggior impatto riguardo alle prestazioni di rendering è rappresentato dai dati di input del modello. È possibile modificare i dati di input come descritto in [Configurare la conversione di modelli](../../how-tos/conversion/configure-model-conversion.md).

Anche le prestazioni delle applicazioni lato client possono costituire un collo di bottiglia. Per un'analisi approfondita delle prestazioni lato client, è consigliabile usare [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>Sequenza temporale client/server

Prima di approfondire i dettagli relativi ai diversi valori di latenza, è opportuno esaminare i punti di sincronizzazione tra client e server nella sequenza temporale:

![Sequenza temporale della pipeline](./media/server-client-timeline.png)

L'illustrazione indica che:

* viene avviata una *stima della posa* dal client con una frequenza dei fotogrammi costante di 60 Hz (ogni 16,6 ms)
* il server quindi avvia il rendering in base alla posa
* il server restituisce l'immagine video codificata
* il client decodifica l'immagine, esegue alcune operazioni di CPU e GPU sulla stessa e poi la visualizza

## <a name="frame-statistics-queries"></a>Query sulle statistiche dei fotogrammi

Le statistiche dei fotogrammi offrono alcune informazioni di alto livello per l'ultimo fotogramma, ad esempio la latenza. I dati specificati nella struttura `FrameStatistics` vengono misurati sul lato client, quindi l'API è una chiamata sincrona:

```cs
void QueryFrameData(RenderingSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<RenderingSession> session)
{
    FrameStatistics frameStatistics;
    if (session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

L'oggetto `FrameStatistics` recuperato include i membri seguenti:

| Membro | Spiegazione |
|:-|:-|
| LatencyPoseToReceive | Latenza dalla stima della posa della fotocamera nel dispositivo client al momento in cui è completamente disponibile un fotogramma del server per l'applicazione client. Questo valore include round trip della rete, tempo di rendering del server, decodifica video e compensazione dell'instabilità. Vedere l'**intervallo 1 nell'illustrazione precedente.**|
| LatencyReceiveToPresent | Latenza dalla disponibilità di un fotogramma ricevuto in remoto al momento in cui l'app client chiama PresentFrame sulla CPU. Vedere l'**intervallo 2 nell'illustrazione precedente.**|
| LatencyPresentToDisplay  | Latenza dalla presentazione di un fotogramma sulla CPU fino all'attivazione del display. Questo valore include tempo GPU client, qualsiasi buffer dei fotogrammi eseguito dal sistema operativo, riproiezione hardware e tempo di analisi del display dipendente dal dispositivo. Vedere l'**intervallo 3 nell'illustrazione precedente.**|
| TimeSinceLastPresent | Intervallo di tempo tra le chiamate successive a PresentFrame sulla CPU. I valori superiori alla durata della visualizzazione, ad esempio 16,6 ms in un dispositivo client a 60 Hz, indicano i problemi causati dall'applicazione client che non termina in tempo il carico di lavoro della CPU.|
| VideoFramesReceived | Numero di fotogrammi ricevuti dal server nell'ultimo secondo. |
| VideoFrameReusedCount | Numero di fotogrammi ricevuti nell'ultimo secondo che sono stati usati più volte nel dispositivo. I valori diversi da zero indicano che i fotogrammi devono essere riutilizzati e riproiettati a causa di un'instabilità della rete o di un tempo di rendering eccessivo del server. |
| VideoFramesSkipped | Numero di fotogrammi ricevuti nell'ultimo secondo che sono stati decodificati, ma non visualizzati sul display perché è arrivato un fotogramma più recente. I valori diversi da zero indicano che l'instabilità della rete ha causato il ritardo di più fotogrammi che poi sono arrivati nel dispositivo client tutti insieme. |
| VideoFramesDiscarded | Molto simile a **VideoFramesSkipped**, ma il motivo per cui viene scartato è il fatto che un frame è arrivato in quel momento che non può essere neanche correlato con le altre pose in sospeso. Se si verifica questo problema, si verifica un conflitto di rete grave.|
| VideoFrameMinDelta | Quantità minima di tempo tra due fotogrammi consecutivi in arrivo nell'ultimo secondo. Insieme a VideoFrameMaxDelta, questo intervallo indica un'instabilità causata dal codec di rete o video. |
| VideoFrameMaxDelta | Quantità massima di tempo tra due fotogrammi consecutivi in arrivo nell'ultimo secondo. Insieme a VideoFrameMinDelta, questo intervallo indica un'instabilità causata dal codec di rete o video. |

La somma di tutti i valori di latenza è in genere molto maggiore rispetto alla durata dei fotogrammi disponibili a 60 Hz. Questo è accettabile, poiché vengono distribuiti più fotogrammi in parallelo e le nuove richieste di fotogrammi vengono avviate alla frequenza dei fotogrammi desiderata, come illustrato nella figura. Tuttavia, se la latenza diventa troppo grande, influisce sulla qualità della [riproiezione con ritardo della fase](../../overview/features/late-stage-reprojection.md) e può compromettere l'esperienza complessiva.

`VideoFramesReceived`, `VideoFrameReusedCount` e `VideoFramesDiscarded` possono essere usati per misurare le prestazioni della rete e del server. Una combinazione di un `VideoFramesReceived` valore basso e un `VideoFrameReusedCount` valore elevato può indicare una congestione della rete o una riduzione delle prestazioni del server. Un valore `VideoFramesDiscarded` elevato indica anche la congestione della rete.

Infine, `TimeSinceLastPresent`, `VideoFrameMinDelta` e `VideoFrameMaxDelta` danno un'idea della varianza dei fotogrammi video in arrivo e delle chiamate locali presenti. Una varianza elevata indica una frequenza dei fotogrammi instabile.

Nessuno dei valori precedenti offre un'indicazione chiara della latenza di rete pura (le frecce rosse nell'illustrazione), perché l'ora esatta in cui il server è occupato con il rendering deve essere sottratto dal valore di round trip `LatencyPoseToReceive`. La parte lato server della latenza complessiva è costituita da informazioni non disponibili per il client. Tuttavia, il paragrafo successivo spiega come questo valore viene approssimato attraverso un input aggiuntivo dal server ed esposto usando il valore `NetworkLatency`.

## <a name="performance-assessment-queries"></a>Query di valutazione delle prestazioni

Le *query di valutazione delle prestazioni* offrono informazioni più dettagliate sul carico di lavoro di CPU e GPU sul server. Poiché i dati vengono richiesti dal server, l'esecuzione di query su uno snapshot delle prestazioni segue il consueto modello asincrono:

```cs
async void QueryPerformanceAssessment(RenderingSession session)
{
    try
    {
        PerformanceAssessment result = await session.Connection.QueryServerPerformanceAssessmentAsync();
        // do something with result...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<RenderingSession> session)
{
    session->Connection()->QueryServerPerformanceAssessmentAsync([](Status status, PerformanceAssessment result) {
        if (status == Status::OK)
        {
            // do something with result...
        }
    });
}
```

Contrariamente all'oggetto `FrameStatistics`, l'oggetto `PerformanceAssessment` contiene informazioni sul lato server:

| Membro | Spiegazione |
|:-|:-|
| TimeCPU | Tempo medio CPU server per fotogramma in millisecondi |
| TimeGPU | Tempo medio GPU server per fotogramma in millisecondi |
| UtilizationCPU | Utilizzo CPU totale nel server in percentuale |
| UtilizationGPU | Utilizzo GPU totale nel server in percentuale |
| MemoryCPU | Utilizzo totale della memoria principale nel server in percentuale |
| MemoryGPU | Utilizzo totale della memoria video dedicata in percentuale della GPU del server |
| NetworkLatency | Latenza media di rete approssimativa del round trip in millisecondi. Nell'illustrazione precedente questo valore corrisponde alla somma delle frecce rosse. Il valore viene calcolato sottraendo il tempo effettivo di rendering del server dal valore `LatencyPoseToReceive` di `FrameStatistics`. Anche se questa approssimazione non è precisa, offre alcune indicazioni sulla latenza di rete, isolata dai valori di latenza calcolati sul client. |
| PolygonsRendered | Numero di triangoli sottoposti a rendering in un fotogramma. Questo numero include anche i triangoli che vengono selezionati in seguito durante il rendering. Ciò significa che questo numero non varia molto tra le diverse posizioni della fotocamera, ma le prestazioni possono variare drasticamente, in base alla frequenza di selezione dei triangoli.|

Per facilitare la valutazione dei valori, ogni parte viene classificata in base alla qualità, ad esempio **Great**, **Good**, **Mediocre** o **Bad**.
Questa metrica di valutazione offre un'indicazione approssimativa dell'integrità del server, ma non deve essere considerata come un valore assoluto. Si supponga, ad esempio, che venga visualizzato il punteggio "mediocre" per il tempo GPU. Il tempo è considerato mediocre perché si avvicina al limite del budget complessivo previsto per la durata dei fotogrammi. In questo caso, tuttavia, potrebbe essere comunque un valore valido, perché si sta eseguendo il rendering di un modello complesso.

## <a name="statistics-debug-output"></a>Output del debug delle statistiche

La classe `ServiceStatistics` è una classe C# che esegue il wrapping delle statistiche dei fotogrammi e delle query sulla valutazione delle prestazioni e offre una comoda funzionalità per restituire le statistiche come valori aggregati o come stringa predefinita. Il codice seguente rappresenta il modo più semplice per visualizzare le statistiche sul lato server nell'applicazione client.

```cs
ServiceStatistics _stats = null;

void OnConnect()
{
    _stats = new ServiceStatistics();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

Il codice precedente popola l'etichetta di testo con il testo seguente:

![Output stringa ArrServiceStats](./media/arr-service-stats.png)

L'API `GetStatsString` formatta una stringa di tutti i valori, ma ogni singolo valore può anche essere sottoposto a query a livello di codice dall'istanza di `ServiceStatistics`.

Sono inoltre disponibili varianti dei membri che aggregano i valori nel tempo. Vedere i membri con suffisso `*Avg`, `*Max` o `*Total`. Il membro `FramesUsedForAverage` indica il numero di fotogrammi usati per questa aggregazione.

## <a name="api-documentation"></a>Documentazione dell'API

* [C# RenderingConnection. QueryServerPerformanceAssessmentAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.queryserverperformanceassessmentasync)
* [C++ RenderingConnection:: QueryServerPerformanceAssessmentAsync ()](/cpp/api/remote-rendering/renderingconnection#queryserverperformanceassessmentasync)

## <a name="next-steps"></a>Passaggi successivi

* [Creare analisi delle prestazioni](../../how-tos/performance-tracing.md)
* [Configurare la conversione di modelli](../../how-tos/conversion/configure-model-conversion.md)