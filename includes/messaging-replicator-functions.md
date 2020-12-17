---
title: includere file
description: includere file
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bc6b7553d240de05404d24f828a5f7db14772f93
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657512"
---
## <a name="what-is-a-replication-task"></a>Che cos'è un'attività di replica?

Un'attività di replica riceve gli eventi da un'origine e li inoltra a una destinazione.
La maggior parte delle attività di replica inoltrerà gli eventi senza modifiche e al massimo eseguirà il mapping tra le strutture dei metadati se i protocolli di origine e di destinazione sono diversi. 

Le attività di replica sono in genere senza stato, vale a dire che non condividono lo stato o altri effetti collaterali in esecuzioni sequenziali o parallele di un'attività. Questo vale anche per l'invio in batch e il concatenamento, che possono essere implementati entrambi sopra lo stato esistente di un flusso. 

Ciò rende le attività di replica diverse dalle attività di aggregazione, che sono in genere con stato, e sono il dominio di Framework e servizi di analisi come [analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction.md).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Applicazioni e attività di replica in funzioni di Azure

In funzioni di Azure, un'attività di replica viene implementata usando un [trigger](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings.md) che acquisisce uno o più messaggi di input da un'origine configurata e un' [associazione di output](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings.md#binding-direction) che trasmette i messaggi copiati dall'origine a una destinazione configurata. 

| Trigger  | Output |
|----------|--------|
| [Trigger di hub eventi di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-trigger?tabs=csharp) | [Associazione di output di hub eventi di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-output?tabs=csharp) |
| [Trigger del bus di servizio di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-trigger?tabs=csharp) | [Binding di output del bus di servizio di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-output?tabs=csharp)
| [Trigger dell'hub Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-trigger?tabs=csharp) | [Binding di output dell'hub Internet Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-output?tabs=csharp)
| [Trigger griglia di eventi di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger?tabs=csharp) | [Associazione di output di griglia di eventi di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-output?tabs=csharp)
| [Trigger Archiviazione code di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp) | [Binding di output di archiviazione code di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-output?tabs=csharp)
| [Trigger Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) | [Binding di output Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
| [Trigger RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) | [Binding di output RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Binding di output di hub di notifica di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-notification-hubs)
||[Binding di output del servizio Azure SignalR](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service-output?tabs=csharp)
||[Binding di output SendGrid di Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid?tabs=csharp)

Le attività di replica vengono distribuite come nell'applicazione di replica tramite gli stessi metodi di distribuzione di qualsiasi altra applicazione di funzioni di Azure. È possibile configurare più attività nella stessa applicazione. 

Con funzioni di Azure Premium, più applicazioni di replica possono condividere lo stesso pool di risorse sottostante, denominato piano di servizio app. Ciò significa che è possibile collocare facilmente le attività di replica scritte in .NET con le attività di replica scritte in Java, ad esempio. Questa operazione è importante se si desidera sfruttare librerie specifiche, ad esempio Apache Camel, che sono disponibili solo per Java e se si tratta dell'opzione migliore per un determinato percorso di integrazione, anche se in genere si preferisce un linguaggio e un runtime diversi per le altre attività di replica. 

Ogni volta che è disponibile, è consigliabile preferire i trigger orientati al batch rispetto ai trigger che forniscono singoli eventi o messaggi ed è sempre necessario ottenere l'evento completo o la struttura dei messaggi anziché basarsi sulle [espressioni di associazione dei parametri](https://docs.microsoft.com/azure/azure-functions/functions-bindings-expressions-patterns)della funzione di Azure.

Il nome della funzione deve riflettere la coppia di origine e destinazione a cui ci si connette ed è necessario anteporre i riferimenti alle stringhe di connessione o ad altri elementi di configurazione nei file di configurazione dell'applicazione con tale nome. 

### <a name="data-and-metadata-mapping"></a>Mapping dei dati e dei metadati

Una volta decisa una coppia di trigger di input e di associazione di output, sarà necessario eseguire alcuni mapping tra i diversi tipi di evento o di messaggio, a meno che il tipo di trigger e l'output non siano uguali.

Per le semplici attività di replica che consentono di copiare messaggi tra hub eventi e bus di servizio, non è necessario scrivere codice personalizzato, ma è possibile appoggiarsi a una [libreria di utilità fornita](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) con gli esempi di replica.

### <a name="retry-policy"></a>Criterio di ripetizione dei tentativi

Per evitare la perdita di dati durante l'evento di disponibilità su entrambi i lati di una funzione di replica, è necessario configurare i criteri di ripetizione dei tentativi in modo che siano affidabili. Per configurare i criteri di ripetizione dei tentativi, vedere la [documentazione di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-error-pages.md) . 

Le impostazioni dei criteri scelti per i progetti di esempio nel [repository di esempio](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) configurano una strategia backoff esponenziale con intervalli di tentativi da 5 secondi a 15 minuti con tentativi infiniti per evitare la perdita di dati. 

Per il bus di servizio, vedere la sezione ["uso del supporto per tentativi nella parte superiore della resilienza dei trigger"](https://docs.microsoft.com/azure/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) per comprendere l'interazione dei trigger e il numero massimo di recapiti definiti per la coda.

### <a name="setting-up-a-replication-application-host"></a>Configurazione di un host di applicazioni di replica

Un'applicazione di replica è un host di esecuzione per una o più attività di replica. 

Si tratta di un'applicazione di funzioni di Azure configurata per essere eseguita nel piano a consumo o (scelta consigliata) in un piano Premium di funzioni di Azure. Tutte le applicazioni di replica devono essere eseguite in un' [identità gestita di sistema o assegnata dall'utente](https://docs.microsoft.com/azure/app-service/overview-managed-identity.md). 

I modelli di Azure Resource Manager collegati (ARM) creano e configurano un'applicazione di replica con:

* un account di archiviazione di Azure per tenere traccia dello stato di avanzamento della replica e dei log,
* un'identità gestita assegnata dal sistema e 
* Monitoraggio di Azure e integrazione Application Insights per il monitoraggio.

Le applicazioni di replica che devono accedere a hub eventi associati a una rete virtuale di Azure (VNet) devono usare il piano Premium di funzioni di Azure e devono essere configurate per connettersi allo stesso VNet, che è anche una delle opzioni disponibili.


|       | Distribuire | Visualizzazione  
|-------|------------------|--------------|---------------|
| **Piano a consumo di Funzioni di Azure** | [![Distribuzione in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![Visualizzare](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Piano Premium di funzioni di Azure** |[![Distribuzione in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Visualizzare](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **Piano Premium di funzioni di Azure con VNet** | [![Distribuzione in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Visualizzare](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Esempi

Il [repository Samples](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) contiene diversi esempi di attività di replica che copiano eventi tra gli hub eventi e/o tra entità del bus di servizio.

Per la copia di un evento tra hub eventi, usare un trigger di hub eventi con un'associazione di output di hub eventi:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Per la copia di messaggi tra entità del bus di servizio, è possibile usare il trigger e l'associazione di output del bus di servizio:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

I metodi helper possono semplificare la replica tra hub eventi e il bus di servizio:

| Source (Sorgente)      | Destinazione      | Punto di ingresso 
|-------------|-------------|------------------------------------------------------------------------
| Hub eventi   | Hub eventi   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Hub eventi   | Bus di servizio | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Bus di servizio | Hub eventi   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Bus di servizio | Bus di servizio | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>Monitoraggio

Per informazioni su come è possibile monitorare l'app di replica, vedere la [sezione monitoraggio](https://docs.microsoft.com/azure/azure-functions/configure-monitoring) della documentazione di funzioni di Azure.

Uno strumento visivo particolarmente utile per il monitoraggio delle attività di replica è la Application Insights [mappa delle applicazioni](https://docs.microsoft.com/azure/azure-monitor/app/app-map), che viene generata automaticamente dalle informazioni di monitoraggio acquisite e consente di esplorare l'affidabilità e le prestazioni dei trasferimenti di origine e destinazione dell'attività di replica.

Per informazioni dettagliate diagnostiche immediate, è possibile usare lo strumento del portale di [metriche in tempo reale](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) , che fornisce una visualizzazione a bassa latenza dei dettagli del log.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuzioni di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies.md)
* [Diagnostica di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-diagnostics.md)
* [Opzioni di rete di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options.md)
* [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview.md)