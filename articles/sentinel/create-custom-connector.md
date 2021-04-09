---
title: Risorse per la creazione di connettori personalizzati di Azure Sentinel | Microsoft Docs
description: Informazioni sulle risorse disponibili per la creazione di connettori personalizzati per Sentinel di Azure. I metodi includono l'agente Log Analytics e l'API, logstash, le app per la logica, PowerShell e funzioni di Azure.
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
ms.openlocfilehash: 25f83088bdc55dbafe7ccf0ff06b0c6595c9ea71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724354"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Risorse per la creazione di connettori personalizzati di Azure Sentinel

Azure Sentinel offre un'ampia gamma di [connettori predefiniti per i servizi di Azure e le soluzioni esterne](connect-data-sources.md)e supporta anche l'inserimento di dati da alcune origini senza un connettore dedicato.

Se non è possibile connettere l'origine dati ad Azure Sentinel usando una delle soluzioni esistenti disponibili, provare a creare il proprio connettore origine dati.

Per un elenco completo dei connettori supportati, vedere il post di Blog su [Sentinel di Azure: connettori Grand (CEF, syslog, Direct, Agent, Custom e altro)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) .

## <a name="compare-custom-connector-methods"></a>Confrontare i metodi del connettore personalizzato

Nella tabella seguente vengono confrontati i dettagli essenziali su ciascun metodo per la creazione di connettori personalizzati descritti in questo articolo. Per ulteriori informazioni su ogni metodo, selezionare i collegamenti nella tabella.

|Descrizione del metodo  |Funzionalità | Senza server    |Complessità  |
|---------|---------|---------|---------|
|**[Agente di Log Analytics](#connect-with-the-log-analytics-agent)** <br>Migliore per la raccolta di file da origini locali e IaaS   | Solo raccolta file  |   No      |Basso         |
|**[Logstash](#connect-with-logstash)** <br>Migliore per le origini locali e IaaS, qualsiasi origine per cui è disponibile un plug-in e le organizzazioni hanno già familiarità con logstash  | I plug-in disponibili, oltre a plug-in personalizzati, offrono una notevole flessibilità.   |   Non richiede l'esecuzione di una macchina virtuale o di un cluster VM           |   Basso supporta molti scenari con i plug-in      |
|**[App per la logica](#connect-with-logic-apps)** <br>Costo elevato; evitare i dati con volumi elevati <br>Migliore per le origini cloud con volumi limitati  | La programmazione senza codice consente una flessibilità limitata, senza il supporto per l'implementazione di algoritmi.<br><br> Se nessuna azione disponibile supporta già i requisiti, la creazione di un'azione personalizzata può aggiungere complessità.    |    Sì         |   Basso sviluppo semplice e non codificato      |
|**[PowerShell](#connect-with-powershell)** <br>Migliore per i prototipi e i caricamenti di file periodici | Supporto diretto per la raccolta di file. <br><br>PowerShell può essere usato per raccogliere più origini, ma richiederà la codifica e la configurazione dello script come servizio.      |No               |  Basso       |
|**[API Log Analytics](#connect-with-the-log-analytics-api)** <br>Ideale per gli ISV che implementano l'integrazione e per i requisiti di raccolta univoci   | Supporta tutte le funzionalità disponibili con il codice.  | Dipende dall'implementazione           |     Alto    |
|**[Funzioni di Azure](#connect-with-azure-functions)** Migliore per le origini cloud con volumi elevati e per i requisiti di raccolta univoci  | Supporta tutte le funzionalità disponibili con il codice.  |  Sì             |     Alta richiede informazioni di programmazione    |
|     |         |                |

> [!TIP]
> Per i confronti dell'uso di app per la logica e funzioni di Azure per lo stesso connettore, vedere:
> 
> - [Inserire rapidamente i log del Web Application Firewall in Sentinel di Azure](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (community di GitHub di Azure Sentinel): connettore della funzione di Azure del [connettore app](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)per la logica  |  [](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Connettersi con l'agente di Log Analytics

Se l'origine dati recapita eventi nei file, è consigliabile usare l'agente di monitoraggio di Azure Log Analytics per creare il connettore personalizzato.

- Per altre informazioni, vedere [raccolta di log personalizzati in monitoraggio di Azure](../azure-monitor/agents/data-sources-custom-logs.md).

- Per un esempio di questo metodo, vedere [raccolta di origini dati JSON personalizzate con l'agente di log Analytics per Linux in monitoraggio di Azure](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Connettersi con logstash

Se si ha familiarità con [logstash](https://www.elastic.co/logstash), è possibile usare logstash con il [plug-in di output logstash per Azure Sentinel](connect-logstash.md) per creare il connettore personalizzato.

Con il plug-in di output logstash di Azure Sentinel, è possibile usare tutti i plug-in di input e di filtro di Logstash e configurare Sentinel di Azure come output per una pipeline logstash. Logstash dispone di un'ampia libreria di plug-in che consentono l'input da varie origini, ad esempio hub eventi, Apache Kafka, file, database e servizi cloud. Usare i plug-in di filtro per analizzare gli eventi, filtrare gli eventi non necessari, offuscare i valori e altro ancora.

Per esempi relativi all'uso di logstash come connettore personalizzato, vedere:

- [Ricerca di una violazione della capitale a TTP nei log AWS con Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (Blog)
- [Guida all'implementazione di Radware di Azure Sentinel](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Per esempi di plug-in di logstash utili, vedere:

- [Plug-in di input CloudWATCH](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Plug-in hub eventi di Azure](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Plug-in di input di Google Cloud Storage](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Plug-in di input Google_pubsub](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash Abilita anche la raccolta di dati con scalabilità usando un cluster. Per altre informazioni, vedere [uso di una VM logstash con bilanciamento del carico su larga scala](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854).
>

## <a name="connect-with-logic-apps"></a>Connettersi con app per la logica

Usare un' [app](../logic-apps/index.yml) per la logica di Azure per creare un connettore personalizzato senza server per Sentinel di Azure.

> [!NOTE]
> Durante la creazione di connettori senza server con app per la logica può essere utile usare app per la logica per i connettori potrebbe essere costoso per grandi volumi di dati.
>
> È consigliabile usare questo metodo solo per le origini dati con volumi limitati o per arricchire i caricamenti di dati.
>

1. **Usare uno dei trigger seguenti per avviare le app per la logica**:

    |Trigger  |Descrizione  |
    |---------|---------|
    |**Attività ricorrente**     |   Ad esempio, pianificare l'app per la logica per recuperare regolarmente i dati da file, database o API esterne specifici. <br>Per altre informazioni, vedere [creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti in app per la logica di Azure](../connectors/connectors-native-recurrence.md).      |
    |**Attivazione su richiesta**     | Eseguire l'app per la logica su richiesta per la raccolta e il test manuali dei dati. <br>Per altre informazioni, vedere  [chiamare, attivare o annidare app per la logica usando endpoint HTTPS](../logic-apps/logic-apps-http-endpoint.md).        |
    |**Endpoint HTTP/S**     |  Consigliato per lo streaming e se il sistema di origine può avviare il trasferimento dei dati. <br>Per altre informazioni, vedere [chiamare gli endpoint di servizio su http o HTTPS](../connectors/connectors-native-http.md).       |
    |     |         |

1. **Usare qualsiasi connettore di app per la logica che legga le informazioni per ottenere gli eventi**. Ad esempio:

    - [Connettersi a un'API REST](/connectors/custom-connectors/)
    - [Connettersi a SQL Server](/connectors/sql/)
    - [Connettersi a un file system](/connectors/filesystem/)

    > [!TIP]
    > I connettori personalizzati per le API REST, i server SQL e i file System supportano anche il recupero di dati da origini dati locali. Per altre informazioni, vedere [installare la documentazione del gateway dati locale](/connectors/filesystem/) .
    >

1. **Preparare le informazioni che si desidera recuperare**.

    Ad esempio, usare l' [azione analizza JSON](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) per accedere alle proprietà nel contenuto JSON, consentendo di selezionare tali proprietà dall'elenco di contenuto dinamico quando si specificano gli input per l'app per la logica.

    Per altre informazioni, vedere [eseguire operazioni sui dati in app per la logica di Azure](../logic-apps/logic-apps-perform-data-operations.md).

1. **Scrivere i dati in log Analytics**.

    Per ulteriori informazioni, vedere la documentazione relativa all' [agente di raccolta dati di Azure log Analytics](/connectors/azureloganalyticsdatacollector/) .

Per esempi di come è possibile creare un connettore personalizzato per Azure Sentinel usando app per la logica, vedere:

- [Creare una pipeline di dati con l'API dell'Agente di raccolta dati](/connectors/azureloganalyticsdatacollector/)
- [Connettore di app per la logica palo alto Prisma usando un webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (community di GitHub di Azure Sentinel)
- [Proteggi le chiamate a Microsoft teams con l'attivazione pianificata](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (Blog)
- Inserimento di [indicatori di minaccia ALIENVAULT OTX in Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (Blog)
- [Invio dei log di PROOFPOINT Tap ad Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (Blog)


## <a name="connect-with-powershell"></a>Connettersi con PowerShell

Lo [script di PowerShell upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) consente di usare PowerShell per trasmettere gli eventi o le informazioni di contesto ad Azure Sentinel dalla riga di comando. Questo flusso crea efficacemente un connettore personalizzato tra l'origine dati e la sentinella di Azure.

Ad esempio, lo script seguente carica un file CSV in Sentinel di Azure:

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

Lo script di [PowerShell upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) usa i parametri seguenti:

|Parametro  |Descrizione  |
|---------|---------|
|**WorkspaceId**     |   ID dell'area di lavoro di Azure Sentinel, in cui verranno archiviati i dati.  [Trovare la chiave e l'ID dell'area di lavoro](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   Chiave primaria o secondaria per l'area di lavoro di Azure Sentinel in cui verranno archiviati i dati. [Trovare la chiave e l'ID dell'area di lavoro](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    Nome della tabella di log personalizzata in cui si desidera archiviare i dati. Un suffisso di **_CL** verrà automaticamente aggiunto alla fine del nome della tabella.  |
|**AddComputerName**     |   Quando questo parametro esiste, lo script aggiunge il nome del computer corrente a tutti i record di log, in un campo denominato **computer**.      |
|**TaggedAzureResourceId**     | Quando questo parametro esiste, lo script associa tutti i record di log caricati con la risorsa di Azure specificata. <br><br>Questa associazione Abilita i record di log caricati per le query del contesto delle risorse e rispetta il controllo degli accessi in base al ruolo incentrato sulle risorse.       |
|**AdditionalDataTaggingName**     |      Quando questo parametro esiste, lo script aggiunge un altro campo a tutti i record di log, con il nome configurato e il valore configurato per il parametro **AdditionalDataTaggingValue** . <br><br>In questo caso, **AdditionalDataTaggingValue** non deve essere vuoto. |
|**AdditionalDataTaggingValue**     |   Quando questo parametro esiste, lo script aggiunge un altro campo a tutti i record di log, con il valore configurato e il nome del campo configurato per il parametro **AdditionalDataTaggingName** . <br><br>Se il parametro **AdditionalDataTaggingName** è vuoto, ma viene configurato un valore, il nome del campo predefinito è **datatagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Trovare la chiave e l'ID dell'area di lavoro

Trovare i dettagli per i parametri **WorkspaceID** e **WorkspaceKey** in Sentinel di Azure:

1. In Sentinel di Azure selezionare **Impostazioni** a sinistra, quindi selezionare la scheda **Impostazioni area di lavoro** .

1. In **Introduzione a log Analytics**  >  **1 Connettere un'origine dati**, selezionare **Gestione agenti Windows e Linux**.

1. Trovare l'ID dell'area di lavoro, la chiave primaria e la chiave secondaria nelle schede dei **server Windows** .
## <a name="connect-with-the-log-analytics-api"></a>Connettersi con l'API Log Analytics

È possibile trasmettere gli eventi a Sentinel di Azure usando l'API dell'agente di raccolta dati Log Analytics per chiamare direttamente un endpoint RESTful.

Quando si chiama un endpoint RESTful direttamente richiede più programmazione, offre anche maggiore flessibilità.

Per ulteriori informazioni, vedere l' [API dell'agente di raccolta dati log Analytics](../azure-monitor/logs/data-collector-api.md), in particolare gli esempi seguenti:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Connettersi con funzioni di Azure

Usare funzioni di Azure insieme a un'API RESTful e a diversi linguaggi di codifica, ad esempio [PowerShell](../azure-functions/functions-reference-powershell.md), per creare un connettore personalizzato senza server.

Per esempi di questo metodo, vedere:

- [Connettere lo standard di endpoint cloud nero di VMware Carbon ad Azure Sentinel con la funzione di Azure](connect-vmware-carbon-black.md)
- [Connettere il Sign-On Single Okta ad Azure Sentinel con la funzione di Azure](connect-okta-single-sign-on.md)
- [Connettere il Proofpoint TAP ad Azure Sentinel con la funzione di Azure](connect-proofpoint-tap.md)
- [Connettere la macchina virtuale Qualys ad Azure Sentinel con la funzione di Azure](connect-qualys-vm.md)
- [Inserimento di XML, CSV o altri formati di dati](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Monitoraggio dello zoom con Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (Blog)
- [Distribuire un app per le funzioni per ottenere i dati dell'API di gestione di Office 365 in Sentinel di Azure](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (community di GitHub di Azure Sentinel)

## <a name="parse-your-custom-connector-data"></a>Analizzare i dati del connettore personalizzato

È possibile usare la tecnica di analisi incorporata del connettore personalizzato per estrarre le informazioni rilevanti e popolare i campi rilevanti in Sentinel di Azure.

Ad esempio:

- **Se è stato usato logstash**, usare il plug-in del filtro [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) per analizzare i dati.
- **Se è stata usata una funzione di Azure**, analizzare i dati con il codice. Per altre informazioni, vedere [parser](normalization.md#parsers).

Azure Sentinel supporta l'analisi in fase di query. L'analisi in fase di query consente di eseguire il push dei dati nel formato originale, quindi di analizzare su richiesta, quando necessario.

L'analisi in fase di query significa anche che non è necessario comprendere in anticipo la struttura esatta dei dati, quando si crea il connettore personalizzato o anche le informazioni che è necessario estrarre. È invece necessario analizzare i dati in qualsiasi momento, anche durante un'indagine.

> [!NOTE]
> L'aggiornamento del parser si applica anche ai dati che sono già stati inseriti in Sentinel di Azure.
> 
## <a name="next-steps"></a>Passaggi successivi

Usare i dati inseriti in Sentinel di Azure per proteggere l'ambiente con uno dei processi seguenti:

- [Ottenere visibilità sugli avvisi](quickstart-get-visibility.md)
- [Visualizza e monitora i dati](tutorial-monitor-your-data.md)
- [Analizzare gli eventi imprevisti](tutorial-investigate-cases.md)
- [Rilevare le minacce](tutorial-detect-threats-built-in.md)
- [Automatizzare la prevenzione delle minacce](tutorial-respond-threats-playbook.md)
- [Cercare le minacce](hunting.md)