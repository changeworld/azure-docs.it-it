---
title: Profilatura della memoria nelle app Python in funzioni di Azure
description: Informazioni su come profilare l'utilizzo della memoria per le app Python e identificare il collo di bottiglia.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: 26f9040016f9eae7e82a85c589d673c90e542f47
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060313"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Profilare l'utilizzo della memoria delle app Python in funzioni di Azure

Durante lo sviluppo o dopo la distribuzione del progetto di app per le funzioni Python locale in Azure, è consigliabile analizzare i potenziali colli di bottiglia della memoria nelle funzioni. Questi colli di bottiglia possono ridurre le prestazioni delle funzioni e causare errori. L'istruzione seguente illustra come usare il pacchetto Python del [Profiler della memoria](https://pypi.org/project/memory-profiler) , che fornisce l'analisi del consumo di memoria riga per riga delle funzioni durante l'esecuzione.

> [!NOTE]
> La profilatura della memoria è destinata solo all'analisi del footprint di memoria nell'ambiente di sviluppo. Non applicare il profiler della memoria nelle app per le funzioni di produzione.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a sviluppare un'app per le funzioni Python, è necessario soddisfare i requisiti seguenti:

* [Python 3.6. x o versione successiva](https://www.python.org/downloads/release/python-374/). Per verificare l'elenco completo delle versioni di Python supportate in funzioni di Azure, visitare la [Guida per sviluppatori di Python](functions-reference-python.md#python-version).

* [Azure Functions Core Tools](functions-run-local.md#v2) versione 3.x.

* [Visual Studio Code](https://code.visualstudio.com/) installato in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>Processo di profilatura della memoria

1. Nella requirements.txt aggiungere `memory-profiler` per assicurarsi che il pacchetto venga incluso nella distribuzione. Se si sta sviluppando nel computer locale, potrebbe essere necessario [attivare un ambiente virtuale Python](create-first-function-cli-python.md#create-venv) ed eseguire una risoluzione del pacchetto in base a `pip install -r requirements.txt` .

2. Nello script di funzione (in genere \_ \_ init \_ \_ . py) aggiungere le righe seguenti sopra la `main()` funzione. In questo modo, il logger radice segnala i nomi dei logger figlio, in modo che i log di profilatura della memoria siano distinguibili dal prefisso `memory_profiler_logs` .

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=memory_logger)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>Esempio

Di seguito è riportato un esempio di esecuzione della profilatura della memoria su un trigger HTTP asincrono e sincrono, denominati rispettivamente "HttpTriggerAsync" e "HttpTriggerSync". Viene compilata un'app per le funzioni Python che invia le richieste GET solo ai home page Microsoft.

### <a name="create-a-python-function-app"></a>Creare un'app per le funzioni Python

Un'app per le funzioni Python deve seguire la [struttura di cartelle](functions-reference-python.md#folder-structure)specificata in funzioni di Azure. Per eseguire l'impalcatura del progetto, è consigliabile usare il Azure Functions Core Tools eseguendo i comandi seguenti:

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>Aggiornare il contenuto del file

Il requirements.txt definisce i pacchetti che verranno usati nel progetto. Oltre ad Azure Functions SDK e Memory-Profiler, vengono introdotti `aiohttp` per le richieste HTTP asincrone e `requests` per le chiamate http sincrone.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

È anche necessario riscrivere il trigger HTTP asincrono `HttpTriggerAsync/__init__.py` e configurare il profiler della memoria, il formato del logger radice e il binding di streaming del logger.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

Per il trigger HTTP sincrono, fare riferimento alla `HttpTriggerSync/__init__.py` sezione di codice seguente:

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>Profilare l'app per le funzioni Python nell'ambiente di sviluppo locale

Dopo aver apportato tutte le modifiche precedenti, è necessario eseguire altri passaggi per inizializzare un ambiente virtuale Python per il runtime di funzioni di Azure.

1. Aprire Windows PowerShell o qualsiasi shell Linux come si preferisce.
2. Creare un ambiente virtuale Python `py -m venv .venv` in Windows o `python3 -m venv .venv` in Linux.
3. Attivare l'ambiente virutal di Python con `.venv\Scripts\Activate.ps1` in Windows PowerShell o `source .venv/bin/activate` nella shell Linux.
4. Ripristinare le dipendenze di Python con `pip install requirements.txt`
5. Avviare il runtime di funzioni di Azure localmente con Azure Functions Core Tools `func host start`
6. Inviare una richiesta GET a `https://localhost:7071/api/HttpTriggerAsync` o `https://localhost:7071/api/HttpTriggerSync` .
7. Verrà visualizzato un report di profilatura della memoria simile alla sezione seguente in Azure Functions Core Tools.

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo Python in funzioni di Azure, vedere le risorse seguenti:

* [Guida per sviluppatori Python per Funzioni di Azure](functions-reference-python.md)
* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
