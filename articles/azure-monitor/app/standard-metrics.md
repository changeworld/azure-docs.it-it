---
title: Metriche standard di applicazione Azure Insights | Microsoft Docs
description: Questo articolo elenca applicazione Azure metriche di Insights con le aggregazioni e le dimensioni supportate.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 8da719f399c0c49efd478f05a0114d85233c880f
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732629"
---
# <a name="application-insights-standard-metrics"></a>Metrica Application Insights standard

Le metriche standard sono pre-aggregate durante la raccolta, hanno prestazioni migliori in fase di query. Questo li rende la scelta migliore per i dashboard e gli avvisi in tempo reale.

## <a name="availability-metrics"></a>Metriche di disponibilità

Le metriche nella categoria di disponibilità consentono di visualizzare l'integrità dell'applicazione Web come osservato dai punti di tutto il mondo. [Configurare i test di disponibilità](../app/monitor-web-app-availability.md) per iniziare a usare qualsiasi metrica da questa categoria.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Disponibilità (availabilityResults/availabilityPercentage)
La metrica di *disponibilità* Mostra la percentuale delle esecuzioni dei test Web che non hanno rilevato alcun problema. Il valore più basso possibile è 0, che indica che tutte le esecuzioni dei test Web non sono riuscite. Il valore 100 indica che tutte le esecuzioni dei test Web hanno superato i criteri di convalida.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|---|---|---|
|Percentuale|Media|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>Durata del test di disponibilità (availabilityResults/Duration)

La metrica *durata test di disponibilità* indica il tempo impiegato per l'esecuzione del test Web. Per i [test Web](../app/availability-multistep.md)in più passaggi, la metrica riflette il tempo di esecuzione totale di tutti i passaggi.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|---|---|---|
|Millisecondi|Media, min, max|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>Test di disponibilità (availabilityResults/count)

La metrica dei *test di disponibilità* riflette il conteggio delle esecuzioni dei test Web da parte di monitoraggio di Azure.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|---|---|---|
|Conteggio|Conteggio|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>Metriche del browser

Le metriche del browser vengono raccolte dal Application Insights JavaScript SDK dai browser degli utenti finali reali. Forniscono informazioni eccezionali sull'esperienza degli utenti con l'app Web. Le metriche del browser non vengono in genere campionate, il che significa che forniscono una maggiore precisione dei numeri di utilizzo rispetto alle metriche lato server, che potrebbero essere inclinate in base al campionamento.

> [!NOTE]
> Per raccogliere le metriche del browser, l'applicazione deve essere instrumentata con [Application Insights JavaScript SDK](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tempo di caricamento pagina del browser (browserTimings/totalDuration)

|Unità di misura|Aggregazioni supportate| Dimensioni supportate|
|---|---|---|
|Millisecondi|Media, min, max|nessuno|

### <a name="client-processing-time-browsertimingprocessingduration"></a>Tempo di elaborazione client (browserTiming/processingDuration)

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Millisecondi|Media, min, max|nessuno|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Tempo di connessione alla rete di caricamento della pagina (browserTimings/networkDuration)

|Unità di misura|Aggregazioni supportate| Dimensioni supportate|
|---|---|---|
|Millisecondi|Media, min, max|nessuno|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Ricezione del tempo di risposta (browserTimings/receiveDuration)

|Unità di misura|Aggregazioni supportate| Dimensioni supportate|
|---|---|---|
|Millisecondi|Media, min, max|nessuno|

### <a name="send-request-time-browsertimingssendduration"></a>Tempo di invio richiesta (browserTimings/sendDuration)

|Unità di misura|Aggregazioni supportate| Dimensioni supportate|
|---|---|---|
|Millisecondi|Media, min, max|nessuno|

## <a name="failure-metrics"></a>Metriche errori

Le metriche negli **errori** presentano problemi relativi all'elaborazione delle richieste, alle chiamate alle dipendenze e alle eccezioni generate.

### <a name="browser-exceptions-exceptionsbrowser"></a>Eccezioni del browser (eccezioni/browser)

Questa metrica riflette il numero di eccezioni generate dal codice dell'applicazione in esecuzione nel browser. Nella metrica vengono incluse solo le eccezioni rilevate con una ```trackException()``` chiamata API Application Insights.

|Unità di misura|Aggregazioni supportate | Dimensioni supportate|
|---|---|---|---|
| Conteggio | Conteggio | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>Errori delle chiamate di dipendenza (dipendenze/non riuscite)

Numero di chiamate di dipendenza non riuscite.

|Unità di misura|Aggregazioni supportate | Dimensioni supportate |
|---|---|---|---|
|Conteggio|Conteggio| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>Eccezioni (eccezioni/conteggio)

Ogni volta che si registra un'eccezione a Application Insights, viene [rilevata una chiamata al metodo trackexception ()](../app/api-custom-events-metrics.md#trackexception) dell'SDK. La metrica eccezioni Mostra il numero di eccezioni registrate.

|Unità di misura|Aggregazioni supportate | Dimensioni supportate |
|---|---|---|---|
|Conteggio|Conteggio|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>Richieste non riuscite (richieste/non riuscite)

Conteggio delle richieste del server rilevate contrassegnate come *non riuscite*. Per impostazione predefinita, il Application Insights SDK contrassegna automaticamente ogni richiesta del server che ha restituito il codice di risposta HTTP 5xx o 4xx come richiesta non riuscita. È possibile personalizzare questa logica modificando la proprietà  *Success* dell'elemento telemetria della richiesta in un [inizializzatore di telemetria personalizzato](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).


|Unità di misura|Aggregazioni supportate | Dimensioni supportate |
|---|---|---|---|
|Conteggio|Conteggio|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>Eccezioni del server (eccezioni/server)

Questa metrica indica il numero di eccezioni del server.

|Unità di misura|Aggregazioni supportate | Dimensioni supportate |
|---|---|---|---|
|Conteggio|Conteggio|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>Contatori delle prestazioni

Usare le metriche nella categoria **contatori delle prestazioni** per accedere ai [contatori delle prestazioni di sistema raccolti da Application Insights](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Memoria disponibile (performanceCounters/availableMemory)

|Unità di misura|Aggregazioni supportate | Dimensioni supportate |
|---|---|---|---|
|Dipendenti dai dati: megabyte, Gigabyte|Media, Max, min|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>Frequenza delle eccezioni (performanceCounters/exceptionRate)

|Unità di misura|Aggregazioni supportate | Dimensioni supportate |
|---|---|---|---|
| Conteggio | Media, Max, min | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Tempo di esecuzione della richiesta HTTP (performanceCounters/requestExecutionTime)

|Unità di misura|Aggregazioni supportate | Dimensioni supportate |
|---|---|---|---|
| Millisecondi | Media, Max, min | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Frequenza delle richieste HTTP (performanceCounters/requestsPerSecond)

|Unità di misura|Aggregazioni supportate | Dimensioni supportate |
|---|---|---|---|
| Richieste al secondo | Media, Max, min | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Richieste HTTP nella coda dell'applicazione (performanceCounters/requestsInQueue)

|Unità di misura|Aggregazioni supportate | Dimensioni supportate |
|---|---|---|---|
| Conteggio | Media, Max, min | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU processo (performanceCounters/processCpuPercentage)

La metrica Mostra la quantità di capacità totale del processore utilizzata dal processo che ospita l'app monitorata.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Percentuale|Media, Max, min| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Velocità IO processo (performanceCounters/processIOBytesPerSecond)

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Byte al secondo|Media, min, max|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Byte privati processo (performanceCounters/processPrivateBytes)

Quantità di memoria non condivisa allocata dal processo monitorato per i dati.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Byte|Media, min, max|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Tempo processore (performanceCounters/processorCpuPercentage)

Utilizzo della CPU da parte di *tutti* i processi in esecuzione nell'istanza del server monitorata.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Percentuale|Media, min, max|`Cloud role instance` |

>[!NOTE]
> La metrica del tempo del processore non è disponibile per le applicazioni ospitate nei servizi app Azure. Usare la metrica  [Process CPU](#process-cpu-performancecountersprocesscpupercentage) per tenere traccia dell'utilizzo della CPU delle applicazioni Web ospitate nei servizi app.

## <a name="server-metrics"></a>Metriche del server

### <a name="dependency-calls-dependenciescount"></a>Chiamate di dipendenza (dipendenze/conteggio)

Questa metrica è correlata al numero di chiamate alle dipendenze.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
| Conteggio | Conteggio | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>Durata delle dipendenze (dipendenze/durata)

Questa metrica si riferisce alla durata delle chiamate alle dipendenze.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
| Tempo | Media, min, max | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>Frequenza richieste server (richieste/conteggio)

Questa metrica riflette il numero di richieste server in ingresso ricevute dall'applicazione Web.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
| Conteggio | Media | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>Richieste server (richieste/conteggio)

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
| Conteggio | Conteggio | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>Tempo di risposta del server (richieste/durata)

Questa metrica riflette il tempo impiegato dai server per elaborare le richieste in ingresso.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
| Tempo | Media, min, max | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>Metriche di utilizzo

### <a name="page-view-load-time-pageviewsduration"></a>Tempo di caricamento della visualizzazione pagina (pagine visualizzate/durata)

Questa metrica si riferisce alla quantità di tempo impiegato per il caricamento degli eventi di visualizzazione pagina.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
| Tempo | Media, min, max | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>Visualizzazioni pagina (pagine di visualizzazione/conteggio)

Il numero di eventi di visualizzazione della pagina registrati con l'API Application Insights TrackPageView ().

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
| Conteggio | Conteggio | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>Tracce (tracce/conteggio)

Numero di istruzioni di traccia registrate con la chiamata API TrackTrace () Application Insights.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
| Conteggio | Conteggio | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [metriche basate su log e pre-aggregate](./pre-aggregated-metrics-log-metrics.md).
* [Query e definizioni delle metriche basate su log](../platform/app-insights-metrics.md).