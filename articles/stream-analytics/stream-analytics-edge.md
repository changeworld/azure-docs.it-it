---
title: Analisi di flusso di Azure in IoT Edge
description: Creare processi Edge in Analisi di flusso di Azure e distribuirli in dispositivi che eseguono Azure IoT Edge.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012615"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Analisi di flusso di Azure in IoT Edge
 
Analisi di flusso di Azure in IoT Edge offre agli sviluppatori la possibilità di distribuire Intelligence analitica quasi in tempo reale più vicino ai dispositivi Internet, in modo che possano sbloccare l'intero valore dei dati generati dal dispositivo. Analisi di flusso di Azure è progettato per offrire bassa latenza, resilienza, uso efficiente della larghezza di banda e conformità. Le aziende possono distribuire la logica di controllo vicina alle operazioni industriali e integrare l'analisi di Big Data eseguita nel cloud.

L'Analisi di flusso di Azure su IoT Edge viene eseguita all'interno del framework di [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). Una volta creato il processo in analisi di flusso, è possibile distribuirlo e gestirlo tramite l'hub Internet.

## <a name="common-scenarios"></a>Scenari comuni

Questa sezione descrive gli scenari comuni per l'analisi di flusso in IoT Edge. Il diagramma seguente mostra il flusso di dati tra i dispositivi Internet e il cloud di Azure.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="Diagramma di alto livello di IoT Edge":::

### <a name="low-latency-command-and-control"></a>Comando e controllo a bassa latenza

I sistemi di sicurezza di produzione devono rispondere ai dati operativi con latenza estremamente bassa. Con analisi di flusso in IoT Edge, è possibile analizzare i dati dei sensori quasi in tempo reale ed eseguire comandi quando si rilevano anomalie per arrestare un computer o attivare gli avvisi.

### <a name="limited-connectivity-to-the-cloud"></a>Connettività limitata al cloud

per i sistemi mission-critical, come le attrezzature per le attività minerarie in remoto, le imbarcazioni connesse o le trivellazioni offshore, è fondamentale analizzare e reagire ai dati, anche quando la connettività cloud è intermittente. Con l'analisi di flusso, la logica di streaming viene eseguita indipendentemente dalla connettività di rete ed è possibile scegliere ciò che si invia al cloud per un'ulteriore elaborazione o archiviazione.

### <a name="limited-bandwidth"></a>Larghezza di banda limitata

il volume dei dati generati dai motori a reazione o dalle automobili connesse può essere talmente elevato che i dati devono essere filtrati o pre-elaborati prima di essere inviati al cloud. Con analisi di flusso è possibile filtrare o aggregare i dati che devono essere inviati al cloud.

### <a name="compliance"></a>Conformità

la conformità alle normative potrebbe richiedere che alcuni dati siano resi anonimi o aggregati in locale prima di essere inviati al cloud.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Processi Edge in Analisi di flusso di Azure

I processi Edge di analisi di flusso vengono eseguiti nei contenitori distribuiti nei [dispositivi Azure IOT Edge](../iot-edge/about-iot-edge.md). I processi Edge sono composti da due parti:

* Una parte del cloud responsabile della definizione del processo: gli utenti definiscono input, output, query e altre impostazioni, ad esempio gli eventi non in ordine, nel cloud.

* Un modulo in esecuzione nei dispositivi IoT. Il modulo contiene il motore di analisi di flusso e riceve la definizione del processo dal cloud. 

Analisi di flusso usa l'hub Internet per distribuire i processi Edge nei dispositivi. Per ulteriori informazioni, vedere [IOT Edge Deployment](../iot-edge/module-deployment-monitoring.md).

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Processo Edge di Analisi di flusso di Azure":::

## <a name="edge-job-limitations"></a>Limitazioni del processo Edge

L'obiettivo è di ottenere una parità tra i processi IoT Edge e i processi cloud. La maggior parte delle funzionalità del linguaggio di query SQL è supportata sia per Edge che per il cloud. Tuttavia, le funzionalità seguenti non sono supportate per i processi Edge:
* Funzioni definite dall'utente in JavaScript. Le funzioni definite dall'utente sono disponibili in [C# per i processi IoT Edge](./stream-analytics-edge-csharp-udf.md) (anteprima).
* Aggregazioni definite dall'utente.
* Funzioni di Azure Machine Learning.
* Formato AVRO per l'input/output. In questo momento sono supportati solo CSV e JSON.
* Operatori SQL seguenti:
    * PARTITION BY
    * GetMetadataPropertyValue
* Criteri di arrivo in ritardo

### <a name="runtime-and-hardware-requirements"></a>Requisiti hardware e di runtime
Per eseguire analisi di flusso in IoT Edge, è necessario che i dispositivi possano eseguire [Azure IOT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Analisi di flusso e Azure IoT Edge usano contenitori **Docker** per offrire una soluzione portabile in esecuzione su più sistemi operativi host (Windows, Linux).

Analisi di flusso in IoT Edge viene reso disponibile come immagini Windows e Linux, in esecuzione su architetture x86-64 o ARM (Advanced RISC Machines). 


## <a name="input-and-output"></a>Input e output

I processi Edge di analisi di flusso possono ottenere input e output da altri moduli in esecuzione nei dispositivi IoT Edge. Per la connessione da e a moduli specifici, è possibile impostare la configurazione di routing in fase di distribuzione. Altre informazioni sono disponibili nella [documentazione relativa alla composizione di un modulo IoT Edge](../iot-edge/module-composition.md).

Per input e output sono supportati i formati CSV e JSON.

Per ogni flusso di input e di output creato nel processo di analisi di flusso, viene creato un endpoint corrispondente nel modulo distribuito. Questi endpoint possono essere usati nelle route della distribuzione.

I tipi di input del flusso supportati sono:
* Hub di Edge
* Hub eventi
* Hub IoT

I tipi di output del flusso supportati sono:
* Hub di Edge
* Database SQL
* Hub eventi
* Archiviazione BLOB/ADLS Gen2

L'input di riferimento supporta il tipo di file di riferimento. Altri output possono essere ottenuti tramite un downstream del processo cloud. Ad esempio, un processo di Analisi di flusso ospitato in Edge invia l'output all'hub Edge, che può quindi inviare l'output all'hub IoT. È possibile usare un secondo processo di analisi di flusso di Azure ospitato nel cloud con input dall'hub e output per Power BI o un altro tipo di output.

## <a name="license-and-third-party-notices"></a>Licenza e comunicazioni di terze parti
* [Licenza di Analisi di flusso di Azure in IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Comunicazioni di terze parti per Analisi di flusso di Azure in IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informazioni sull'immagine del modulo Analisi di flusso di Azure 

Le informazioni sulla versione sono state aggiornate per l'ultima volta il 2020-09-21:

- Immagine: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - immagine di base: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - piattaforma:
      - architettura: amd64
      - sistema operativo: linux
 
- Immagine: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - immagine di base: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - piattaforma:
      - architettura: arm
      - sistema operativo: linux
 
- Immagine: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - immagine di base: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - piattaforma:
      - architettura: arm64
      - sistema operativo: linux
      
      
## <a name="get-help"></a>Ottenere aiuto
Per un'ulteriore assistenza, provare la [pagina di domande e risposte Microsoft per Analisi di flusso di Azure](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni su Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Esercitazione sull'analisi di flusso in IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Sviluppare processi Edge di Analisi di flusso usando gli strumenti di Visual Studio](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Implementare CI/CD per Analisi di flusso usando le API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
