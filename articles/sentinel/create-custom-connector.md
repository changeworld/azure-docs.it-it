---
title: Risorse per la creazione Azure Sentinel connettori personalizzati | Microsoft Docs
description: Informazioni sulle risorse disponibili per la creazione di connettori personalizzati per Azure Sentinel. I metodi includono l'agente e l'API di Log Analytics, Logstash, App per la logica, PowerShell e Funzioni di Azure.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: a1aaf89624f8d0ab48692629d859f3c1bdb4ba67
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738901"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Risorse per la creazione Azure Sentinel connettori personalizzati

Azure Sentinel offre un'ampia gamma di connettori predefiniti per i servizi di [Azure](connect-data-sources.md)e soluzioni esterne e supporta anche l'inserimento di dati da alcune origini senza un connettore dedicato.

Se non è possibile connettere l'origine dati a Azure Sentinel una delle soluzioni esistenti disponibili, provare a creare un connettore di origine dati personalizzato.

Per un elenco completo dei connettori supportati, vedere il post di blog [Azure Sentinel: The connectors grand (CEF, Syslog, Direct, Agent, Custom e altro](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) ancora).

## <a name="compare-custom-connector-methods"></a>Confrontare i metodi dei connettori personalizzati

Nella tabella seguente vengono confrontati i dettagli essenziali su ogni metodo per la creazione di connettori personalizzati descritti in questo articolo. Selezionare i collegamenti nella tabella per altri dettagli su ogni metodo.

|Descrizione del metodo  |Funzionalità | Senza server    |Complessità  |
|---------|---------|---------|---------|
|**[Agente di Log Analytics](#connect-with-the-log-analytics-agent)** <br>Ideale per la raccolta di file da origini IaaS e locali   | Solo raccolta di file  |   No      |Basso         |
|**[Logstash](#connect-with-logstash)** <br>Ideale per le origini IaaS e locali, qualsiasi origine per cui è disponibile un plug-in e organizzazioni che hanno già familiarità con Logstash  | I plug-in disponibili, oltre al plug-in personalizzato, offrono una notevole flessibilità.   |   No; richiede l'esecuzione di una macchina virtuale o di un cluster di macchine virtuali           |   Basso; supporta molti scenari con i plug-in      |
|**[App per la logica](#connect-with-logic-apps)** <br>Costo elevato; evitare dati con volumi elevati <br>Ideale per le origini cloud a volume ridotto  | La programmazione senza codice consente una flessibilità limitata, senza supporto per l'implementazione di algoritmi.<br><br> Se nessuna azione disponibile supporta già i requisiti, la creazione di un'azione personalizzata può aggiungere complessità.    |    Sì         |   Basso; sviluppo semplice e senza codice      |
|**[PowerShell](#connect-with-powershell)** <br>Ideale per la prototipazione e il caricamento periodico di file | Supporto diretto per la raccolta di file. <br><br>PowerShell può essere usato per raccogliere più origini, ma richiederà la codifica e la configurazione dello script come servizio.      |No               |  Basso       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>Ideale per gli ISV che implementano l'integrazione e per requisiti di raccolta univoci   | Supporta tutte le funzionalità disponibili con il codice.  | Dipende dall'implementazione           |     Alto    |
|**[Funzioni di Azure](#connect-with-azure-functions)** Ideale per origini cloud con volumi elevati e per requisiti di raccolta univoci  | Supporta tutte le funzionalità disponibili con il codice.  |  Sì             |     Alto; richiede conoscenze di programmazione    |
|     |         |                |

> [!TIP]
> Per i confronti dell'uso di App per la logica Funzioni di Azure per lo stesso connettore, vedere:
> 
> - [Inserire i log di Fastly Web Application Firewall Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel community GitHub): [Connettore](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)per Connettore app  |  [funzione di Azure](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Connettersi con l'agente di Log Analytics

Se l'origine dati recapita eventi nei file, è consigliabile usare l'agente di Log Analytics Monitoraggio di Azure per creare il connettore personalizzato.

- Per altre informazioni, vedere [Raccolta di log personalizzati in Monitoraggio di Azure](../azure-monitor/agents/data-sources-custom-logs.md).

- Per un esempio di questo metodo, vedere Raccolta di origini dati JSON personalizzate con l'agente [di Log Analytics per Linux in Monitoraggio di Azure](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Connettersi con Logstash

Se si ha familiarità con [Logstash,](https://www.elastic.co/logstash)è possibile usare Logstash con il [plug-in di output Logstash per](connect-logstash.md) Azure Sentinel creare il connettore personalizzato.

Con il plug-Azure Sentinel di output Logstash è possibile usare qualsiasi plug-in di input e filtro di Logstash e configurare Azure Sentinel come output per una pipeline Logstash. Logstash include un'ampia libreria di plug-in che consentono l'input da varie origini, ad esempio Hub eventi, Apache Kafka, file, database e servizi cloud. Usare i plug-in di filtro per analizzare gli eventi, filtrare gli eventi non necessari, offuscare i valori e altro ancora.

Per esempi di uso di Logstash come connettore personalizzato, vedere:

- [Ricerca di TTTP di violazione di capitale 1 nei log di AWS Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (blog)
- [Guida all'implementazione Azure Sentinel Radware](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Per esempi di utili plug-in Logstash, vedere:

- [Plug-in di input cloudwatch](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Hub eventi di Azure plug-in](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Plug-in di input di Google Cloud Storage](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub plug-in di input](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash consente anche la raccolta di dati ridimensionati tramite un cluster. Per altre informazioni, vedere Uso di una macchina virtuale [Logstash](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)con carico bilanciato su larga scala.
>

## <a name="connect-with-logic-apps"></a>Connettersi con App per la logica

Usare [un'app per la logica](../logic-apps/index.yml) di Azure per creare un connettore personalizzato senza server per Azure Sentinel.

> [!NOTE]
> Anche se la creazione di connettori serverless con App per la logica può risultare utile, l'uso di App per la logica per i connettori può essere dis costoso per grandi volumi di dati.
>
> È consigliabile usare questo metodo solo per le origini dati con volumi contenuti o per arricchire i caricamenti dei dati.
>

1. **Usare uno dei trigger seguenti per avviare App** per la logica:

    |Trigger  |Descrizione  |
    |---------|---------|
    |**Un'attività ricorrente**     |   Ad esempio, pianificare l'app per la logica per recuperare regolarmente i dati da file, database o API esterne specifici. <br>Per altre informazioni, vedere [Creare, pianificare ed eseguire attività e flussi di](../connectors/connectors-native-recurrence.md)lavoro ricorrenti in App per la logica di Azure .      |
    |**Attivazione su richiesta**     | Eseguire l'app per la logica su richiesta per la raccolta e il test manuali dei dati. <br>Per altre informazioni, vedere  [Chiamare, attivare o annidare](../logic-apps/logic-apps-http-endpoint.md)app per la logica usando endpoint HTTPS.        |
    |**Endpoint HTTP/S**     |  Consigliato per lo streaming e se il sistema di origine può avviare il trasferimento dei dati. <br>Per altre informazioni, vedere [Chiamare gli endpoint del servizio tramite HTTP o HTTP.](../connectors/connectors-native-http.md)       |
    |     |         |

1. **Usare uno dei connettori dell'app per la logica che leggono le informazioni per ottenere gli eventi**. Ad esempio:

    - [Connettersi a un'API REST](/connectors/custom-connectors/)
    - [Connettersi a SQL Server](/connectors/sql/)
    - [Connettersi a un file system](/connectors/filesystem/)

    > [!TIP]
    > I connettori personalizzati per API REST, SQL Server e file system supportano anche il recupero di dati da origini dati locali. Per altre informazioni, vedere la documentazione relativa [all'installazione del gateway dati](/connectors/filesystem/) locale.
    >

1. **Preparare le informazioni da recuperare.**

    Ad esempio, usare l'azione [analizza JSON](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) per accedere alle proprietà nel contenuto JSON, consentendo di selezionare tali proprietà dall'elenco di contenuto dinamico quando si specificano input per l'app per la logica.

    Per altre informazioni, vedere [Eseguire operazioni sui dati in App per la logica di Azure](../logic-apps/logic-apps-perform-data-operations.md).

1. **Scrivere i dati in Log Analytics**.

    Per altre informazioni, vedere la documentazione [dell'agente di raccolta dati di Azure Log Analytics.](/connectors/azureloganalyticsdatacollector/)

Per esempi di come creare un connettore personalizzato per Azure Sentinel app per la logica, vedere:

- [Creare una pipeline di dati con l'API dell'Agente di raccolta dati](/connectors/azureloganalyticsdatacollector/)
- [Palo Alto Prisma Logic Connettore app un webhook (Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) community GitHub)
- [Proteggere le chiamate di Microsoft Teams con l'attivazione pianificata](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- [Inserimento di indicatori di minaccia OTX di Alvault](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) in Azure Sentinel (blog)

## <a name="connect-with-powershell"></a>Connettersi con PowerShell

Lo [script di PowerShell Upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) consente di usare PowerShell per trasmettere eventi o informazioni di contesto Azure Sentinel dalla riga di comando. Questo flusso crea in modo efficace un connettore personalizzato tra l'origine dati e Azure Sentinel.

Ad esempio, lo script seguente carica un file CSV in Azure Sentinel:

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

Lo [script di PowerShell Upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) usa i parametri seguenti:

|Parametro  |Descrizione  |
|---------|---------|
|**Id area di lavoro**     |   ID Azure Sentinel'area di lavoro, in cui verranno archiviati i dati.  [Trovare l'ID e la chiave dell'area di lavoro.](#find-your-workspace-id-and-key)  |
|**WorkspaceKey**     |   Chiave primaria o secondaria per l'Azure Sentinel di lavoro in cui verranno archiviati i dati. [Trovare l'ID e la chiave dell'area di lavoro.](#find-your-workspace-id-and-key)  |
|**LogTypeName**     |    Nome della tabella di log personalizzata in cui si desidera archiviare i dati. Un suffisso **_CL** verrà aggiunto automaticamente alla fine del nome della tabella.  |
|**AddComputerName**     |   Quando questo parametro esiste, lo script aggiunge il nome del computer corrente a ogni record di log, in un campo denominato **Computer**.      |
|**TaggedAzureResourceId**     | Quando questo parametro esiste, lo script associa tutti i record di log caricati alla risorsa di Azure specificata. <br><br>Questa associazione abilita i record di log caricati per le query del contesto delle risorse ed è conforme al controllo degli accessi in base al ruolo incentrato sulle risorse.       |
|**AdditionalDataTaggingName**     |      Quando questo parametro esiste, lo script aggiunge un altro campo a ogni record di log, con il nome configurato e il valore configurato per il **parametro AdditionalDataTaggingValue.** <br><br>In questo caso, **AdditionalDataTaggingValue** non deve essere vuoto. |
|**AdditionalDataTaggingValue**     |   Quando questo parametro esiste, lo script aggiunge un altro campo a ogni record di log, con il valore configurato e il nome del campo configurato per il **parametro AdditionalDataTaggingName.** <br><br>Se il **parametro AdditionalDataTaggingName** è vuoto, ma è configurato un valore, il nome del campo predefinito è **DataTagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Trovare l'ID e la chiave dell'area di lavoro

Trovare i dettagli per i parametri **WorkspaceID** e **WorkspaceKey** in Azure Sentinel:

1. In Azure Sentinel selezionare **Impostazioni** a sinistra e quindi selezionare la scheda **Impostazioni area di** lavoro.

1. In **Introduzione a Log Analytics**  >  **1 Connettere un'origine dati** selezionare Gestione agenti Windows e **Linux.**

1. Trovare l'ID dell'area di lavoro, la chiave primaria e la chiave secondaria nelle **schede dei server Windows.**
## <a name="connect-with-the-log-analytics-api"></a>Connettersi con l'API Log Analytics

È possibile trasmettere gli eventi Azure Sentinel usando l'API agente di raccolta dati di Log Analytics per chiamare direttamente un endpoint RESTful.

Anche se la chiamata diretta di un endpoint RESTful richiede una maggiore programmazione, offre anche maggiore flessibilità.

Per altre informazioni, vedere l'API dell'agente di raccolta dati di [Log Analytics,](../azure-monitor/logs/data-collector-api.md)in particolare gli esempi seguenti:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Connettersi con Funzioni di Azure

Usare Funzioni di Azure con un'API RESTful e vari linguaggi di codifica, ad esempio [PowerShell](../azure-functions/functions-reference-powershell.md), per creare un connettore personalizzato senza server.

Per esempi di questo metodo, vedere:

- [Connettere l'endpoint cloud VMware Carbon Black Standard Azure Sentinel con la funzione di Azure](connect-vmware-carbon-black.md)
- [Connettere l'istanza di Okta Single Sign-On a Azure Sentinel con la funzione di Azure](connect-okta-single-sign-on.md)
- [Connettere il tap proofpoint alla Azure Sentinel con La funzione di Azure](connect-proofpoint-tap.md)
- [Connettere la macchina virtuale Qualys a Azure Sentinel funzione di Azure](connect-qualys-vm.md)
- [Inserimento di XML, CSV o altri formati di dati](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Monitoraggio di Zoom con Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Distribuire un'app per le funzioni per ottenere i dati API Gestione office 365 in Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Azure Sentinel community GitHub)

## <a name="parse-your-custom-connector-data"></a>Analizzare i dati del connettore personalizzato

È possibile usare la tecnica di analisi incorporata del connettore personalizzato per estrarre le informazioni rilevanti e popolare i campi pertinenti in Azure Sentinel.

Ad esempio:

- **Se è stato usato Logstash,** usare il plug-in [di filtro Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) per analizzare i dati.
- **Se è stata usata una funzione di Azure,** analizzare i dati con il codice. Per altre informazioni, vedere [Parser.](normalization.md#parsers)

Azure Sentinel supporta l'analisi in fase di query. L'analisi in fase di query consente di eseguire il push dei dati nel formato originale e quindi di analizzarlo su richiesta, quando necessario.

L'analisi in fase di query significa anche che non è necessario conoscere in anticipo la struttura esatta dei dati, quando si crea il connettore personalizzato o anche le informazioni che è necessario estrarre. Analizzare invece i dati in qualsiasi momento, anche durante un'analisi.

> [!NOTE]
> L'aggiornamento del parser si applica anche ai dati già inseriti in Azure Sentinel.
> 
## <a name="next-steps"></a>Passaggi successivi

Usare i dati inseriti in Azure Sentinel proteggere l'ambiente con uno dei processi seguenti:

- [Ottenere visibilità sugli avvisi](quickstart-get-visibility.md)
- [Visualizzare e monitorare i dati](tutorial-monitor-your-data.md)
- [Eseguire indagini sugli eventi imprevisti](tutorial-investigate-cases.md)
- [Rilevare le minacce](tutorial-detect-threats-built-in.md)
- [Automatizzare la prevenzione delle minacce](tutorial-respond-threats-playbook.md)
- [Cercare le minacce](hunting.md)
