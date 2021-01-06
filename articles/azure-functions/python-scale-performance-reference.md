---
title: Migliorare le prestazioni della velocità effettiva delle app Python in funzioni di Azure
description: Informazioni su come sviluppare app di funzioni di Azure con Python altamente performanti e con scalabilità in carico.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: baa362f029678f266f154df912a9178a6626667d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935870"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Migliorare le prestazioni della velocità effettiva delle app Python in funzioni di Azure

Quando si sviluppa per funzioni di Azure con Python, è necessario comprendere come funzionano le funzioni e in che modo le prestazioni influiscono sul modo in cui l'app per le funzioni viene ridimensionata. La necessità è più importante quando si progettano app a prestazioni elevate. I fattori principali da considerare durante la progettazione, la scrittura e la configurazione delle app per le funzioni sono la scalabilità orizzontale e le configurazioni delle prestazioni della velocità effettiva.

## <a name="horizontal-scaling"></a>Scalabilità orizzontale
Per impostazione predefinita, Funzioni di Azure monitora automaticamente il carico dell'applicazione e crea istanze host aggiuntive per Python, se necessario. Funzioni di Azure usa soglie predefinite per diversi tipi di trigger per decidere quando aggiungere istanze, ad esempio l'età dei messaggi e le dimensioni della coda per QueueTrigger. Queste soglie non sono configurabili dall'utente. Per altre informazioni, vedere [scalabilità guidata dagli eventi in funzioni di Azure](event-driven-scaling.md).

## <a name="improving-throughput-performance"></a>Miglioramento delle prestazioni della velocità effettiva

Le configurazioni predefinite sono adatte alla maggior parte delle applicazioni di funzioni di Azure. Tuttavia, è possibile migliorare le prestazioni della velocità effettiva delle applicazioni usando le configurazioni basate sul profilo del carico di lavoro. Il primo passaggio consiste nel comprendere il tipo di carico di lavoro in esecuzione.

|| Carico di lavoro associato a I/O | Carico di lavoro associato alla CPU |
|--| -- | -- |
|Caratteristiche delle app per le funzioni| <ul><li>L'app deve gestire molte chiamate simultanee.</li> <li> L'app elabora un numero elevato di eventi di I/O, ad esempio chiamate di rete e lettura/scrittura su disco.</li> </ul>| <ul><li>L'app esegue calcoli a esecuzione prolungata, ad esempio il ridimensionamento delle immagini.</li> <li>L'app esegue la trasformazione dei dati.</li> </ul> |
|Esempi| <ul><li>API Web</li><ul> | <ul><li>Elaborazione dati</li><li> Inferenza di Machine Learning</li><ul>|

 
Poiché i carichi di lavoro di funzioni reali sono in genere una combinazione di I/O e di CPU, è consigliabile profilare l'app in carichi di produzione realistici.


### <a name="performance-specific-configurations"></a>Configurazioni specifiche delle prestazioni

Dopo aver compreso il profilo del carico di lavoro dell'app per le funzioni, di seguito sono riportate le configurazioni che è possibile usare per migliorare le prestazioni della velocità effettiva delle funzioni.

* [Asincrona](#async)
* [Ruolo di lavoro per più lingue](#use-multiple-language-worker-processes)
* [Numero massimo di ruoli di lavoro in un processo di lavoro](#set-up-max-workers-within-a-language-worker-process)
* [Ciclo di eventi](#managing-event-loop)
* [Scalabilità verticale](#vertical-scaling)



#### <a name="async"></a>Async

Poiché [Python è un runtime a thread singolo](https://wiki.python.org/moin/GlobalInterpreterLock), un'istanza host per Python può elaborare una sola chiamata di funzione alla volta per impostazione predefinita. Per le applicazioni che elaborano un numero elevato di eventi di I/o e/o è associato a I/o, è possibile migliorare significativamente le prestazioni eseguendo funzioni in modo asincrono.

Per eseguire una funzione in modo asincrono, usare l'istruzione `async def`, che esegue la funzione direttamente con [asyncio](https://docs.python.org/3/library/asyncio.html):

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Di seguito è riportato un esempio di una funzione con trigger HTTP che usa il client http [aiohttp](https://pypi.org/project/aiohttp/) :

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


Una funzione senza la `async` parola chiave viene eseguita automaticamente in un pool di thread ThreadPoolExecutor:

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

Per ottenere il massimo vantaggio delle funzioni in esecuzione in modo asincrono, è necessario implementare anche la libreria e l'operazione di I/O usata nel codice. L'utilizzo di operazioni di I/O sincrone in funzioni definite come asincrone **può compromettere** le prestazioni complessive. Se per le librerie in uso non è stata implementata la versione asincrona, è comunque possibile trarre vantaggio dall'esecuzione del codice in modo asincrono [gestendo il ciclo di eventi](#managing-event-loop) nell'app. 

Di seguito sono riportati alcuni esempi di librerie client che hanno implementato il modello asincrono:
- [aiohttp](https://pypi.org/project/aiohttp/) -client/server http per asyncio 
- [Flussi](https://docs.python.org/3/library/asyncio-stream.html) di primitive API per async/await pronti per l'uso con la connessione di rete
- [Coda Janus](https://pypi.org/project/janus/) -coda thread-safe Asyncio per Python
- [pyzmq](https://pypi.org/project/pyzmq/) -binding Python per ZeroMQ
 
##### <a name="understanding-async-in-python-worker"></a>Informazioni su Async in Python Worker

Quando si definisce davanti `async` a una firma della funzione, Python contrassegna la funzione come coroutine. Quando si chiama la coroutine, può essere pianificata come attività in un ciclo di eventi. Quando si chiama `await` in una funzione asincrona, viene registrata una continuazione nel ciclo di eventi e il ciclo di eventi consente di elaborare l'attività successiva durante il tempo di attesa.

Nel ruolo di lavoro Python, il thread di lavoro condivide il ciclo di eventi con la funzione del cliente `async` ed è in grado di gestire più richieste simultaneamente. Si consiglia vivamente ai clienti di usare le librerie compatibili con asyncio, ad esempio [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/). L'uso di queste raccomandazioni aumenterà significativamente la velocità effettiva della funzione rispetto alle librerie implementate in modo sincrono.

> [!NOTE]
>  Se la funzione viene dichiarata come `async` senza alcun `await` oggetto all'interno della relativa implementazione, le prestazioni della funzione verranno gravemente influenzate poiché il ciclo di eventi verrà bloccato che impedisce al ruolo di lavoro di Python di gestire le richieste simultanee.

#### <a name="use-multiple-language-worker-processes"></a>Usare più processi di lavoro del linguaggio

Per impostazione predefinita, ogni istanza host di Funzioni include un singolo processo di lavoro del linguaggio. È possibile aumentare il numero di processi di lavoro per host (fino a 10) usando l'impostazione [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) dell'applicazione. Funzioni di Azure prova quindi a distribuire uniformemente le chiamate di funzioni simultanee tra questi processi di lavoro.

Per le app associate alla CPU, è necessario impostare il numero di Language worker in modo che sia uguale o superiore al numero di core disponibili per ogni app per le funzioni. Per altre informazioni, vedere [SKU di istanze disponibili](functions-premium-plan.md#available-instance-skus). 

Le app con binding i/O possono anche trarre vantaggio dall'aumento del numero di processi di lavoro oltre il numero di core disponibili. Tenere presente che l'impostazione del numero di ruoli di lavoro troppo elevata può influisca sulle prestazioni complessive a causa del numero maggiore di cambi di contesto richiesti. 

FUNCTIONS_WORKER_PROCESS_COUNT si applica a ogni host creato da Funzioni quando le istanze dell'applicazione vengono aumentate per soddisfare la domanda.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>Configurare il numero massimo di ruoli di lavoro in un processo di lavoro in linguaggio

Come indicato nella [sezione](#understanding-async-in-python-worker)async, il ruolo di lavoro del linguaggio Python considera le funzioni e le [coroutine](https://docs.python.org/3/library/asyncio-task.html#coroutines) in modo diverso. Una coroutine viene eseguita nello stesso ciclo di eventi in cui viene eseguito il processo di lavoro della lingua. D'altra parte, una chiamata di funzione viene eseguita all'interno di un [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor), gestito dal Language Worker, come thread.

È possibile impostare il valore di numero massimo di thread di lavoro consentiti per l'esecuzione di funzioni di sincronizzazione usando l'impostazione [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) applicazione. Questo valore imposta l' `max_worker` argomento dell'oggetto ThreadPoolExecutor, che consente a Python di usare un pool di al massimo `max_worker` thread per eseguire chiamate in modo asincrono. `PYTHON_THREADPOOL_THREAD_COUNT`Si applica a ogni ruolo di lavoro creato dall'host di funzioni e Python decide quando creare un nuovo thread o riutilizzare il thread inattivo esistente. Per le versioni precedenti di Python (ovvero,, `3.8` `3.7` e `3.6` ), il `max_worker` valore è impostato su 1. Per la versione di Python `3.9` , `max_worker` è impostato su `None` .

Per le app associate alla CPU, è consigliabile lasciare l'impostazione su un numero basso, a partire da 1 e aumentando quando si sperimenta il carico di lavoro. Questo suggerimento consiste nel ridurre il tempo impiegato per i cambi di contesto e consentire il completamento delle attività con associazione alla CPU.

Per le app con binding I/O, è possibile ottenere miglioramenti sostanziali aumentando il numero di thread che lavorano a ogni chiamata. si consiglia di iniziare con il valore predefinito di Python, ovvero il numero di Core + 4 e quindi modificare in base ai valori di velocità effettiva visualizzati.

Per le app per i carichi di lavoro di combinazione, è necessario bilanciare entrambe `FUNCTIONS_WORKER_PROCESS_COUNT` `PYTHON_THREADPOOL_THREAD_COUNT` le configurazioni e per massimizzare la velocità effettiva. Per comprendere il modo in cui le app per le funzioni passano la maggior parte del tempo, è consigliabile profilarle e impostare i valori in base al comportamento presente. Vedere anche questa [sezione](#use-multiple-language-worker-processes) per informazioni sulle impostazioni dell'applicazione FUNCTIONS_WORKER_PROCESS_COUNT.

> [!NOTE]
>  Sebbene queste raccomandazioni siano valide per le funzioni attivate sia HTTP che non HTTP, potrebbe essere necessario modificare altre configurazioni specifiche del trigger per le funzioni non HTTP attivate per ottenere le prestazioni previste dalle app per le funzioni. Per altre informazioni, fare riferimento a questo [articolo](functions-best-practices.md).


#### <a name="managing-event-loop"></a>Gestione del ciclo di eventi

È consigliabile usare librerie di terze parti compatibili con asyncio. Se nessuna delle librerie di terze parti soddisfa le proprie esigenze, è anche possibile gestire i cicli di eventi in funzioni di Azure. La gestione dei cicli di eventi offre una maggiore flessibilità nella gestione delle risorse di calcolo e consente anche di eseguire il wrapping delle librerie I/O sincrone in coroutine.

Sono disponibili molti documenti ufficiali Python utili per discutere le [coroutine e le attività](https://docs.python.org/3/library/asyncio-task.html) e il [ciclo di eventi](https://docs.python.org/3.8/library/asyncio-eventloop.html) usando la libreria **asyncio** incorporata.

Usare la libreria di [richieste](https://github.com/psf/requests) seguente come esempio, questo frammento di codice usa la libreria **asyncio** per eseguire il wrapping del `requests.get()` metodo in una coroutine, eseguendo più richieste Web a SAMPLE_URL simultaneamente.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>Scalabilità verticale
Per altre unità di elaborazione in particolare per le operazioni associate alla CPU, è possibile eseguire l'aggiornamento al piano Premium con specifiche più elevate. Con unità di elaborazione più elevate, è possibile modificare il numero di processi di lavoro in base al numero di core disponibili e ottenere un grado di parallelismo superiore. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo Python in funzioni di Azure, vedere le risorse seguenti:

* [Guida per sviluppatori Python per Funzioni di Azure](functions-reference-python.md)
* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)

