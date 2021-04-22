---
title: Analizzare log e metriche in Azure Spring Cloud | Microsoft Docs
description: Informazioni su come analizzare i dati di diagnostica in Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6472ba7dd055de97a1855211f21fd0c75eea814f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874118"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analizzare log e metriche con le impostazioni di diagnostica

**Questo articolo si applica a:** ✔️ Java ✔️ C#

Usando la funzionalità di diagnostica di Azure Spring Cloud, è possibile analizzare log e metriche con uno dei servizi seguenti:

* Usare Azure Log Analytics, in cui i dati vengono scritti in Archiviazione di Azure. Si verifica un ritardo durante l'esportazione dei log in Log Analytics.
* Salvare i log in un account di archiviazione per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni).
* Trasmettere i log all'hub eventi per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata.

Scegliere la categoria di log e la categoria di metriche da monitorare.

> [!TIP]
> Vuoi solo trasmettere i log? Vedere questo comando [dell'interfaccia della riga di comando di Azure](/cli/azure/spring-cloud/app#az_spring_cloud_app_logs).

## <a name="logs"></a>Log

|Registro | Descrizione |
|----|----|
| **ApplicationConsole** | Log della console di tutte le applicazioni dei clienti. |
| **SystemLogs** | Attualmente, solo [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) log in questa categoria. |

## <a name="metrics"></a>Metriche

Per un elenco completo delle metriche, vedere Spring Cloud [Metrics](./spring-cloud-concept-metrics.md#user-metrics-options).

Per iniziare, abilitare uno di questi servizi per ricevere i dati. Per informazioni sulla configurazione di Log Analytics, vedere [Introduzione a Log Analytics in Monitoraggio di Azure](../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Configurare le impostazioni di diagnostica

1. Nel portale di Azure passare all'istanza Azure Spring Cloud.
1. Selezionare **l'opzione impostazioni** di diagnostica e quindi selezionare **Aggiungi impostazione di diagnostica.**
1. Immettere un nome per l'impostazione e quindi scegliere dove inviare i log. È possibile selezionare qualsiasi combinazione delle tre opzioni seguenti:
    * **Archivia in un account di archiviazione**
    * **Streaming in un hub eventi**
    * **Invia a Log Analytics**

1. Scegliere la categoria di log e la categoria di metriche da monitorare e quindi specificare il tempo di conservazione (in giorni). Il tempo di conservazione si applica solo all'account di archiviazione.
1. Selezionare **Salva**.

> [!NOTE]
> 1. Potrebbe esserci un divario fino a 15 minuti tra la generazione dei log o delle metriche e la loro visualizzazione nell'account di archiviazione, nell'hub eventi o in Log Analytics.
> 1. Se l'Azure Spring Cloud viene eliminata o spostata, l'operazione non verrà propagata alle risorse delle **impostazioni di** diagnostica. Le **risorse delle impostazioni** di diagnostica devono essere eliminate manualmente prima dell'operazione sull'elemento padre, ad esempio l'Azure Spring Cloud istanza. In caso contrario, se viene effettuato il provisioning di una nuova istanza di Azure Spring Cloud con lo stesso  ID risorsa di quella eliminata o se l'istanza di Azure Spring Cloud viene spostata di nuovo, le risorse delle impostazioni di diagnostica precedenti continuano a estenderla.

## <a name="view-the-logs-and-metrics"></a>Visualizzare i log e le metriche
Esistono diversi metodi per visualizzare i log e le metriche, come descritto nelle intestazioni seguenti.

### <a name="use-the-logs-blade"></a>Usare il pannello Log

1. Nel portale di Azure passare all'istanza Azure Spring Cloud.
1. Per aprire il **riquadro Ricerca** log, selezionare **Log**.
1. Nella casella **di ricerca** Tabelle
   * Per visualizzare i log, immettere una query semplice, ad esempio:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Per visualizzare le metriche, immettere una query semplice, ad esempio:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Per visualizzare i risultati della ricerca, selezionare **Esegui.**

### <a name="use-log-analytics"></a>Usare Log Analytics

1. Nel riquadro portale di Azure fare clic su Log Analytics nel **riquadro sinistro.**
1. Selezionare l'area di lavoro Log Analytics scelta quando sono state aggiunte le impostazioni di diagnostica.
1. Per aprire il **riquadro Ricerca** log, selezionare **Log**.
1. Nella casella **di ricerca** Tabelle
   * Per visualizzare i log, immettere una query semplice, ad esempio:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * Per visualizzare le metriche, immettere una query semplice, ad esempio:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Per visualizzare i risultati della ricerca, selezionare **Esegui.**
1. È possibile eseguire ricerche nei log dell'applicazione o dell'istanza specifica impostando una condizione di filtro:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` fa distinzione tra maiuscole e minuscole, `=~` ma non lo è.

Per altre informazioni sul linguaggio di query usato in Log Analytics, vedere Monitoraggio di Azure [query di log.](/azure/data-explorer/kusto/query/) Per eseguire query su tutti i log di Log Analytics da un client centralizzato, vedere [Esplora dati di Azure](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

### <a name="use-your-storage-account"></a>Usare l'account di archiviazione

1. Nel riquadro portale di Azure trovare Account **di archiviazione nel** pannello di spostamento a sinistra o nella casella di ricerca.
1. Selezionare l'account di archiviazione scelto quando sono state aggiunte le impostazioni di diagnostica.
1. Per aprire il **riquadro Contenitore** BLOB, **selezionare BLOB.**
1. Per esaminare i log applicazioni, cercare un contenitore denominato **insights-logs-applicationconsole.**
1. Per esaminare le metriche dell'applicazione, cercare un contenitore **denominato insights-metrics-pt1m.**

Per altre informazioni sull'invio di informazioni di diagnostica a un account di archiviazione, vedere [Archiviare](../storage/common/storage-introduction.md)e visualizzare i dati di diagnostica in Archiviazione di Azure .

### <a name="use-your-event-hub"></a>Usare l'hub eventi

1. Nel riquadro portale di Azure trovare **Hub** eventi nel pannello di spostamento a sinistra o nella casella di ricerca.

1. Cercare e selezionare l'hub eventi scelto quando sono state aggiunte le impostazioni di diagnostica.
1. Per aprire il **riquadro Elenco hub** eventi, selezionare Hub **eventi.**
1. Per esaminare i log applicazioni, cercare un hub eventi denominato **insights-logs-applicationconsole.**
1. Per esaminare le metriche dell'applicazione, cercare un hub eventi denominato **insights-metrics-pt1m.**

Per altre informazioni sull'invio di informazioni di diagnostica a un hub eventi, vedere Streaming Diagnostica di Azure dati nel percorso critico [tramite Hub eventi.](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)

## <a name="analyze-the-logs"></a>Analizzare i log

Azure Log Analytics è in esecuzione con un motore Kusto per poter eseguire query nei log per l'analisi. Per una rapida introduzione all'esecuzione di query sui log con Kusto, vedere [l'esercitazione su Log Analytics.](../azure-monitor/logs/log-analytics-tutorial.md)

I log applicazioni forniscono informazioni critiche e log dettagliati sull'integrità, sulle prestazioni e altro ancora dell'applicazione. Nelle sezioni successive vengono descritte alcune semplici query che consentono di comprendere gli stati correnti e passati dell'applicazione.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Mostra log applicazioni da Azure Spring Cloud

Per esaminare un elenco dei log applicazioni Azure Spring Cloud, ordinati in base all'ora con i log più recenti visualizzati per primi, eseguire la query seguente:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Mostra le voci di log contenenti errori o eccezioni

Per esaminare le voci di log non ordinate che citano un errore o un'eccezione, eseguire la query seguente:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Usare questa query per trovare errori o modificare i termini della query per trovare codici di errore o eccezioni specifici.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Visualizzare il numero di errori ed eccezioni segnalati dall'applicazione nell'ultima ora

Per creare un grafico a torta che visualizza il numero di errori ed eccezioni registrati dall'applicazione nell'ultima ora, eseguire la query seguente:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Altre informazioni sull'esecuzione di query sui log applicazioni

Monitoraggio di Azure offre supporto completo per l'esecuzione di query nei log applicazioni tramite Log Analytics. Per altre informazioni su questo servizio, vedere [Introduzione alle query di log in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md). Per altre informazioni sulla compilazione di query per analizzare i log applicazioni, vedere [Panoramica delle query di log in Monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md).

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Come convertire le analisi dello stack Java su più righe in un'unica riga?

Esiste una soluzione alternativa per convertire le analisi dello stack su più righe in una singola riga. È possibile modificare l'output del log Java per riformattare i messaggi di analisi dello stack, sostituendo i caratteri di nuova riga con un token. Se si usa la libreria Logback Java, è possibile riformattare i messaggi di analisi dello stack aggiungendo `%replace(%ex){'[\r\n]+', '\\n'}%nopex` come segue:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
È quindi possibile sostituire di nuovo il token con caratteri di nuova riga in Log Analytics, come indicato di seguito:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Potrebbe essere possibile usare la stessa strategia per altre librerie di log Java.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Distribuire la prima applicazione Azure Spring Cloud](spring-cloud-quickstart.md)
