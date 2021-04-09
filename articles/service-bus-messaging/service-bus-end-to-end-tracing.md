---
title: Diagnostica e analisi end-to-end di Azure Service Bus | Microsoft Docs
description: Panoramica della diagnostica del client del bus di servizio e della traccia end-to-end (client tramite tutti i servizi interessati nell'elaborazione).
ms.topic: article
ms.date: 02/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 19b284aceb83fbbc2bcf662b2b58941e6a5b36f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539214"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Correlazione e analisi distribuita tramite la messaggistica del bus di servizio

Uno dei problemi più comuni nello sviluppo di microservizi è la possibilità di tracciare l'operazione da un client tramite tutti i servizi interessati all'elaborazione. Si tratta di una caratteristica utile per il debug, l'analisi delle prestazioni, i test A/B e altri comuni scenari di diagnostica.
Una parte di questo problema consiste nel tenere traccia degli elementi di lavoro logici. Tale operazione include i risultati e la latenza dell'elaborazione dei messaggi e le chiamate di dipendenza esterne. Un'altra parte è costituita dalla correlazione di questi eventi di diagnostica oltre i limiti del processo.

Quando un producer invia un messaggio tramite una coda, tale operazione avviene in genere nell'ambito di un'altra operazione logica, avviata da un altro client o servizio. La stessa operazione viene portata avanti dal consumer dopo che ha ricevuto un messaggio. Sia il producer che il consumer (e altri servizi che elaborano l'operazione) generano presumibilmente eventi di telemetria per analizzare il flusso e il risultato dell'operazione. Per correlare tali eventi e l'operazione di analisi end-to-end, ogni servizio che restituisce dati di telemetria deve contrassegnare tutti gli eventi con un contesto di analisi.

La messaggistica del bus di servizio di Microsoft Azure include proprietà di payload definite che producer e consumer devono usare per passare tale contesto di analisi.
Il protocollo si basa sul [protocollo di correlazione HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

# <a name="azuremessagingservicebus-sdk-latest"></a>[Azure. Messaging. ServiceBus SDK (versione più recente)](#tab/net-standard-sdk-2)
| Nome proprietà        | Descrizione                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Identificatore univoco di una chiamata esterna alla coda effettuata dal producer. Per la logica, le considerazioni e il formato, vedere [Request-Id in HTTP protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) (Request-Id nel protocollo HTTP). |

## <a name="service-bus-net-client-autotracing"></a>Tracciatura utente .NET del bus di servizio
La `ServiceBusProcessor` classe del [client del bus di servizio di messaggistica di Azure per .NET](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) fornisce punti di strumentazione di traccia che possono essere collegati da sistemi di traccia o parte del codice client. La strumentazione consente di verificare tutte le chiamate al servizio di messaggistica del bus di servizio dal lato client. Se l'elaborazione dei messaggi viene eseguita utilizzando di (modello [ `ProcessMessageAsync` del gestore di `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) messaggi), anche l'elaborazione del messaggio viene instrumentata.

### <a name="tracking-with-azure-application-insights"></a>Verifica con Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) offre funzionalità avanzate di monitoraggio delle prestazioni, tra cui la verifica automatica delle richieste e delle dipendenze.

A seconda del tipo di progetto, installare Application Insights SDK:
- [ASP.NET](../azure-monitor/app/asp-net.md): installare la versione 2.5-beta2 o una versione successiva
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md): installare la versione 2.2.0-beta2 o una versione successiva.
Questi collegamenti forniscono informazioni dettagliate su come installare l'SDK, creare risorse e, se necessario, configurare l'SDK. Per le applicazioni non ASP.NET, vedere l'articolo [Azure Application Insights for Console Applications](../azure-monitor/app/console.md) (Azure Application Insights per applicazioni console).

Se si utilizza (modello [ `ProcessMessageAsync` di `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) gestione messaggi) per elaborare i messaggi, anche l'elaborazione del messaggio viene instrumentata. Tutte le chiamate del bus di servizio eseguite dal servizio vengono automaticamente rilevate e correlate con altri elementi di telemetria. In caso contrario vedere l'esempio seguente per la verifica manuale delle operazioni di elaborazione dei messaggi.

#### <a name="trace-message-processing"></a>Analizzare l'elaborazione dei messaggi

```csharp
async Task ProcessAsync(ProcessMessageEventArgs args)
{
    ServiceBusReceivedMessage message = args.Message;
    if (message.ApplicationProperties.TryGetValue("Diagnostic-Id", out var objectId) && objectId is string diagnosticId)
    {
        var activity = new Activity("ServiceBusProcessor.ProcessMessage");
        activity.SetParentId(diagnosticId);
        // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
        using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
        {
            telemetryClient.TrackTrace("Received message");
            try 
            {
            // process message
            }
            catch (Exception ex)
            {
                telemetryClient.TrackException(ex);
                operation.Telemetry.Success = false;
                throw;
            }

            telemetryClient.TrackTrace("Done");
        }
    }
}
```

In questo esempio, i dati di telemetria delle richieste vengono segnalati per ogni messaggio elaborato, con un timestamp, la durata e il risultato (esito positivo). La telemetria include anche un set di proprietà di correlazione. Alle eccezioni e alle analisi nidificate restituite durante l'elaborazione dei messaggi vengono inoltre assegnate proprietà di correlazione in modo che vengano rappresentate come elementi figlio di `RequestTelemetry`.

Se si effettuano chiamate a componenti esterni supportati durante l'elaborazione del messaggio, vengono anche monitorati e correlati automaticamente. Per informazioni sulla verifica e la correlazione manuale, vedere [Verifica delle operazioni personalizzate con Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md).

Se si esegue un codice esterno oltre a Application Insights SDK, prevedere una **durata** più lunga quando si visualizzano i log di Application Insights. 

![Durata più lunga nel log Application Insights](./media/service-bus-end-to-end-tracing/longer-duration.png)

Non significa che si è verificato un ritardo nella ricezione del messaggio. In questo scenario, il messaggio è già stato ricevuto perché il messaggio viene passato come parametro al codice SDK. Il tag **nome** nei log di Application Insights (**processo**) indica che il messaggio è ora elaborato dal codice di elaborazione dell'evento esterno. Questo problema non è correlato ad Azure. Queste metriche si riferiscono invece all'efficienza del codice esterno, dato che il messaggio è già stato ricevuto dal bus di servizio. 

### <a name="tracking-without-tracing-system"></a>Verifica senza sistema di analisi
Se il sistema di traccia non supporta il rilevamento automatico delle chiamate del bus di servizio, è possibile che si stia cercando di aggiungere tale supporto in un sistema di traccia o nell'applicazione. Questa sezione illustra gli eventi di diagnostica inviati dal client .NET del bus di servizio.  

Per instrumentare il client .NET del bus di servizio, si usano le primitive di analisi .NET [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` funge da un contesto di analisi mentre `DiagnosticSource` è un meccanismo di notifica. 

Se non è presente alcun listener per gli eventi DiagnosticSource, la strumentazione è disattivata, mantenendo i costi di strumentazione zero. DiagnosticSource cede il controllo completo al listener:
- il listener controlla le origini e gli eventi di cui rimanere in ascolto
- il listener controlla il campionamento e la frequenza degli eventi
- gli eventi vengono inviati con un payload che fornisce il contesto completo, in modo che sia possibile accedere e modificare l'oggetto Message durante l'evento

Prima di procedere con l'implementazione, è consigliabile leggere il [manuale dell'utente di DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

Nel codice seguente verrà creato un listener per eventi del bus di servizio nell'app ASP.NET Core che scrive i log con Microsoft.Extension.Logger.
Il listener usa la libreria [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) per eseguire la sottoscrizione a DiagnosticSource. È comunque possibile eseguire facilmente la sottoscrizione a DiagnosticSource anche senza la libreria.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Azure.Messaging.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Azure.Messaging.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

In questo esempio il listener registra durata, risultato, identificatore univoco e ora di inizio di ogni operazione del bus di servizio.

### <a name="events"></a>Eventi
Tutti gli eventi avranno le proprietà seguenti conformi alla specifica di telemetria aperta: https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/trace/api.md .

- `message_bus.destination` -percorso coda/argomento/sottoscrizione
- `peer.address` : spazio dei nomi completo
- `kind` : Producer, consumer o client. Producer viene utilizzato per l'invio di messaggi, consumer durante la ricezione e il client durante la risoluzione.
- `component` – `servicebus`

Tutti gli eventi hanno anche proprietà' Entity ' è endpoint ', che vengono omesse nella tabella seguente
  * `string Entity` : Nome dell'entità (coda, argomento e così via).
  * `Uri Endpoint`: URL dell'endpoint del bus di servizio

### <a name="instrumented-operations"></a>Operazioni instrumentate
Ecco l'elenco completo delle operazioni instrumentate:

| Nome operazione | API verificata |
| -------------- | ----------- | 
| ServiceBusSender. Send | ServiceBusSender.SendMessageAsync<br/>ServiceBusSender.SendMessagesAsync |
| ServiceBusSender. Schedule | ServiceBusSender. ScheduleMessageAsync<br/>ServiceBusSender.ScheduleMessagesAsync | 
| ServiceBusSender. Cancel | ServiceBusSender.CancelScheduledMessageAsync<br/>ServiceBusSender.CancelScheduledMessagesAsync |
| ServiceBusReceiver. Receive | ServiceBusReceiver.ReceiveMessageAsync<br/>ServiceBusReceiver.ReceiveMessagesAsync |
| ServiceBusReceiver.ReceiveDeferred | ServiceBusReceiver.ReceiveDeferredMessagesAsync |
| ServiceBusReceiver. Peek | ServiceBusReceiver.PeekMessageAsync<br/>ServiceBusReceiver.PeekMessagesAsync |
| ServiceBusReceiver. Abandon | ServiceBusReceiver.AbandonMessagesAsync |
| ServiceBusReceiver. complete | ServiceBusReceiver.CompleteMessagesAsync |
| ServiceBusReceiver. DeadLetter | ServiceBusReceiver.DeadLetterMessagesAsync |
| ServiceBusReceiver. rinvia |  ServiceBusReceiver.DeferMessagesAsync |
| ServiceBusReceiver.RenewMessageLock | ServiceBusReceiver.RenewMessageLockAsync |
| ServiceBusSessionReceiver.RenewSessionLock | ServiceBusSessionReceiver.RenewSessionLockAsync |
| ServiceBusSessionReceiver. getsessionstate | ServiceBusSessionReceiver.GetSessionStateAsync |
| ServiceBusSessionReceiver. SessionState | ServiceBusSessionReceiver.SetSessionStateAsync |
| ServiceBusProcessor. ProcessMessage | Callback del processore impostato in ServiceBusProcessor. Proprietà ProcessMessageAsync |
| ServiceBusSessionProcessor.ProcessSessionMessage | Callback del processore impostato in ServiceBusSessionProcessor. Proprietà ProcessMessageAsync |

### <a name="filtering-and-sampling"></a>Filtro e campionamento

In alcuni casi è consigliabile registrare solo parte degli eventi per ridurre il consumo dello spazio di archiviazione o il sovraccarico delle prestazioni. È possibile registrare solo gli eventi di tipo 'Stop' (come nell'esempio precedente) o una percentuale campione degli eventi. 
A questo scopo, è possibile usare `DiagnosticSource` con il predicato `IsEnabled`. Per altre informazioni, vedere [Context-Based Filtering in DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering) (Filtro in base al contesto in DiagnosticSource).

Per ridurre al minimo l'impatto sulle prestazioni, è possibile chiamare `IsEnabled` più volte per una singola operazione.

`IsEnabled` viene chiamato nella sequenza seguente:

1. `IsEnabled(<OperationName>, string entity, null)` ad esempio, `IsEnabled("ServiceBusSender.Send", "MyQueue1")`. Si noti che non è presente alcun ' Start ' o ' stop ' alla fine. Usarlo per escludere determinate operazioni o code. Se il metodo di callback restituisce `false` , gli eventi per l'operazione non vengono inviati.

   * Per le operazioni di tipo 'Process' e 'ProcessSession' viene ricevuto anche il callback `IsEnabled(<OperationName>, string entity, Activity activity)`. Usarlo per filtrare gli eventi in base alle proprietà `activity.Id` o Tags.
  
2. `IsEnabled(<OperationName>.Start)` ad esempio, `IsEnabled("ServiceBusSender.Send.Start")`. Controlla se deve essere attivato l'evento 'Start'. Il risultato interessa solo l'evento ' Start ', ma altre strumentazioni non dipendono da esso.

Non esiste alcun `IsEnabled` per l'evento ' stop '.

Se il risultato di alcune operazioni è un'eccezione, viene chiamato `IsEnabled("ServiceBusSender.Send.Exception")`. È possibile eseguire la sottoscrizione solo di eventi 'Exception' e impedire il resto della strumentazione. In questo caso sarà comunque necessario gestire tali eccezioni. Poiché l'altra strumentazione è disabilitata, non è previsto che il contesto di traccia scorra con i messaggi dal consumer al producer.

È possibile usare `IsEnabled` anche per implementare strategie di campionamento. Il campionamento basato su `Activity.Id` o `Activity.RootId` garantisce un campionamento coerente in tutti i pneumatici (purché venga propagato dal sistema di traccia o dal codice).

In presenza di più `DiagnosticSource` listener per la stessa origine, è sufficiente che un solo listener accetti l'evento, quindi non esiste alcuna garanzia che `IsEnabled` venga chiamato.



# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

| Nome proprietà        | Descrizione                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Identificatore univoco di una chiamata esterna alla coda effettuata dal producer. Per la logica, le considerazioni e il formato, vedere [Request-Id in HTTP protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) (Request-Id nel protocollo HTTP). |
|  Correlation-Context | Contesto dell'operazione, che viene propagato in tutti i servizi coinvolti nell'elaborazione dell'operazione. Per altre informazioni, vedere [Correlation-Context in HTTP protocol](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) (Correlation-Context nel protocollo HTTP). |

## <a name="service-bus-net-client-autotracing"></a>Tracciatura utente .NET del bus di servizio

A partire dalla versione 3.0.0, il [client del bus di servizio di Microsoft Azure per .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) fornisce punti di strumentazione di analisi a cui possono agganciarsi sistemi di analisi o parti del codice client.
La strumentazione consente di verificare tutte le chiamate al servizio di messaggistica del bus di servizio dal lato client. Se per l'elaborazione dei messaggi si usa il [criterio del gestore di messaggi](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), anche tale operazione viene instrumentata.

### <a name="tracking-with-azure-application-insights"></a>Verifica con Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) offre funzionalità avanzate di monitoraggio delle prestazioni, tra cui la verifica automatica delle richieste e delle dipendenze.

A seconda del tipo di progetto, installare Application Insights SDK:
- [ASP.NET](../azure-monitor/app/asp-net.md): installare la versione 2.5-beta2 o una versione successiva
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md): installare la versione 2.2.0-beta2 o una versione successiva.
Questi collegamenti forniscono informazioni dettagliate su come installare l'SDK, creare risorse e, se necessario, configurare l'SDK. Per le applicazioni non ASP.NET, vedere l'articolo [Azure Application Insights for Console Applications](../azure-monitor/app/console.md) (Azure Application Insights per applicazioni console).

Se si usa il [modello di gestione](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) messaggi per elaborare i messaggi, l'operazione è terminata: tutte le chiamate del bus di servizio eseguite dal servizio vengono automaticamente rilevate e correlate con altri elementi di telemetria. In caso contrario vedere l'esempio seguente per la verifica manuale delle operazioni di elaborazione dei messaggi.

#### <a name="trace-message-processing"></a>Analizzare l'elaborazione dei messaggi

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

In questo esempio, per ogni messaggio elaborato viene restituito `RequestTelemetry`, che include un timestamp, la durata e il risultato (esito positivo). La telemetria include anche un set di proprietà di correlazione.
Alle eccezioni e alle analisi nidificate restituite durante l'elaborazione dei messaggi vengono inoltre assegnate proprietà di correlazione in modo che vengano rappresentate come elementi figlio di `RequestTelemetry`.

Se si effettuano chiamate a componenti esterni supportati durante l'elaborazione del messaggio, vengono anche monitorati e correlati automaticamente. Per informazioni sulla verifica e la correlazione manuale, vedere [Verifica delle operazioni personalizzate con Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md).

Se si esegue un codice esterno oltre a Application Insights SDK, prevedere una **durata** più lunga quando si visualizzano i log di Application Insights. 

![Durata più lunga nel log Application Insights](./media/service-bus-end-to-end-tracing/longer-duration.png)

Non significa che si è verificato un ritardo nella ricezione del messaggio. In questo scenario, il messaggio è già stato ricevuto perché il messaggio viene passato come parametro al codice SDK. Il tag **nome** nei log di Application Insights (**processo**) indica che il messaggio è ora elaborato dal codice di elaborazione dell'evento esterno. Questo problema non è correlato ad Azure. Queste metriche si riferiscono invece all'efficienza del codice esterno, dato che il messaggio è già stato ricevuto dal bus di servizio. Vedere [questo file su GitHub](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) per vedere dove viene generato e assegnato il tag del **processo** dopo la ricezione del messaggio dal bus di servizio. 

### <a name="tracking-without-tracing-system"></a>Verifica senza sistema di analisi
Se il sistema di traccia non supporta il rilevamento automatico delle chiamate del bus di servizio, è possibile che si stia cercando di aggiungere tale supporto in un sistema di traccia o nell'applicazione. Questa sezione illustra gli eventi di diagnostica inviati dal client .NET del bus di servizio.  

Per instrumentare il client .NET del bus di servizio, si usano le primitive di analisi .NET [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` funge da un contesto di analisi mentre `DiagnosticSource` è un meccanismo di notifica. 

Se non è presente alcun listener per gli eventi DiagnosticSource, la strumentazione è disattivata, mantenendo i costi di strumentazione zero. DiagnosticSource cede il controllo completo al listener:
- il listener controlla le origini e gli eventi di cui rimanere in ascolto
- il listener controlla il campionamento e la frequenza degli eventi
- gli eventi vengono inviati con un payload che fornisce il contesto completo, in modo che sia possibile accedere e modificare l'oggetto Message durante l'evento

Prima di procedere con l'implementazione, è consigliabile leggere il [manuale dell'utente di DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

Nel codice seguente verrà creato un listener per eventi del bus di servizio nell'app ASP.NET Core che scrive i log con Microsoft.Extension.Logger.
Il listener usa la libreria [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) per eseguire la sottoscrizione a DiagnosticSource. È comunque possibile eseguire facilmente la sottoscrizione a DiagnosticSource anche senza la libreria.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

In questo esempio il listener registra durata, risultato, identificatore univoco e ora di inizio di ogni operazione del bus di servizio.

#### <a name="events"></a>Eventi

Per ogni operazione vengono inviati due eventi, ovvero 'Start' e 'Stop'. Probabilmente si è interessati solo agli eventi "Stop". Forniscono il risultato dell'operazione e l'ora di inizio e la durata come proprietà dell'attività.

Il payload dell'evento fornisce a un listener con il contesto dell'operazione, replicando il valore restituito e i parametri in arrivo dell'API. Il payload dell'evento 'Stop' contiene tutte le proprietà del payload dell'evento 'Start', di conseguenza è possibile ignorare completamente l'evento 'Start'.

Tutti gli eventi hanno anche proprietà' Entity ' è endpoint ', che vengono omesse nella tabella seguente
  * `string Entity`: nome dell'entità (coda, argomento e così via)
  * `Uri Endpoint`: URL dell'endpoint del bus di servizio

Per ogni evento 'Stop' esiste una proprietà `Status` con l'operazione asincrona `TaskStatus` con cui è stata completata. Per semplicità anche tale proprietà non figura nella tabella seguente.

Ecco l'elenco completo delle operazioni instrumentate:

| Nome operazione | API verificata | Proprietà del payload specifiche|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages`: elenco dei messaggi da inviare |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message`: messaggio in fase di elaborazione<br/>`DateTimeOffset ScheduleEnqueueTimeUtc`: differenza di ora dei messaggi pianificati<br/>`long SequenceNumber`: numero di sequenza del messaggio pianificato (payload dell'evento 'Stop') |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber`: numero di sequenza del messaggio da annullare | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount`: numero massimo di messaggi che è possibile ricevere.<br/>`IList<Message> Messages`: elenco dei messaggi ricevuti (payload dell'evento 'Stop') |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber`: punto di partenza da cui sfogliare un batch di messaggi.<br/>`int RequestedMessageCount`: numero massimo di messaggi da recuperare.<br/>`IList<Message> Messages`: elenco dei messaggi ricevuti (payload dell'evento 'Stop') |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers`: elenco contenente i numeri di sequenza da ricevere.<br/>`IList<Message> Messages`: elenco dei messaggi ricevuti (payload dell'evento 'Stop') |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens`: elenco contenente i token di blocco dei messaggi corrispondenti da completare.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken`: token di blocco del messaggio corrispondente da abbandonare. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken`: token di blocco del messaggio corrispondente da rinviare. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken`: token di blocco del messaggio corrispondente da inserire nella coda di messaggi non recapitabili. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken`: token di blocco del messaggio corrispondente da inserire per cui rinnovare il blocco.<br/>`DateTime LockedUntilUtc`: nuova data e ora di scadenza del token di blocco in formato UTC. (payload dell'evento 'Stop')|
| Microsoft.Azure.ServiceBus.Process | Funzione lambda del gestore di messaggi fornita in [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message`: messaggio in fase di elaborazione. |
| Microsoft.Azure.ServiceBus.ProcessSession | Funzione lambda del gestore della sessione di messaggi fornita in [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message`: messaggio in fase di elaborazione.<br/>`IMessageSession Session`: sessione in fase di elaborazione |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule`: descrizione della regola che specifica la regola da aggiungere. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`: nome della regola da rimuovere. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`: tutte le regole associate alla sottoscrizione. (solo payload di 'Stop') |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId`: elemento sessionId presente nei messaggi. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId`: elemento sessionId presente nei messaggi.<br/>`byte [] State`: stato della sessione (payload dell'evento 'Stop') |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId`: elemento sessionId presente nei messaggi.<br/>`byte [] State`: stato della sessione |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId`: elemento sessionId presente nei messaggi. |
| Microsoft.Azure.ServiceBus.Exception | qualsiasi API instrumentata| `Exception Exception`: istanza di eccezione |

In ogni evento è possibile accedere a `Activity.Current` che contiene il contesto dell'operazione

#### <a name="logging-more-properties"></a>Registrazione di altre proprietà

`Activity.Current` fornisce il contesto dettagliato dell'operazione corrente e i relativi elementi padre. Per ulteriori informazioni, vedere la [documentazione delle attività](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md).
La strumentazione del bus di servizio fornisce ulteriori informazioni in `Activity.Current.Tags` , che vengono mantenute `MessageId` e `SessionId` ogni volta che sono disponibili.

Anche le attività che verificano gli eventi 'Receive', 'Peek' e 'ReceiveDeferred' possono contenere il tag `RelatedTo`. Tale tag contiene un elenco distinto di elementi `Diagnostic-Id` di messaggi che sono stati ricevuti come risultato.
Tale operazione può causare la ricezione di numerosi messaggi non correlati. Inoltre, `Diagnostic-Id` non è noto quando l'operazione viene avviata, quindi le operazioni di ' ricezione ' potrebbero essere correlate alle operazioni ' Process ' solo con questo tag. Il tag è utile quando si analizzano problemi di prestazioni per verificare il tempo impiegato per la ricezione del messaggio.

Un modo efficiente per registrare i tag consiste nell'eseguire l'iterazione su di essi. Ecco come aggiungere i tag all'esempio precedente 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtro e campionamento

In alcuni casi è consigliabile registrare solo parte degli eventi per ridurre il consumo dello spazio di archiviazione o il sovraccarico delle prestazioni. È possibile registrare solo gli eventi di tipo 'Stop' (come nell'esempio precedente) o una percentuale campione degli eventi. 
A questo scopo, è possibile usare `DiagnosticSource` con il predicato `IsEnabled`. Per altre informazioni, vedere [Context-Based Filtering in DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering) (Filtro in base al contesto in DiagnosticSource).

Per ridurre al minimo l'impatto sulle prestazioni, è possibile chiamare `IsEnabled` più volte per una singola operazione.

`IsEnabled` viene chiamato nella sequenza seguente:

1. `IsEnabled(<OperationName>, string entity, null)` ad esempio, `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Si noti che non è presente alcun ' Start ' o ' stop ' alla fine. Usarlo per escludere determinate operazioni o code. Se il metodo di callback restituisce `false` , gli eventi per l'operazione non vengono inviati

   * Per le operazioni di tipo 'Process' e 'ProcessSession' viene ricevuto anche il callback `IsEnabled(<OperationName>, string entity, Activity activity)`. Usarlo per filtrare gli eventi in base alle proprietà `activity.Id` o Tags.
  
2. `IsEnabled(<OperationName>.Start)` ad esempio, `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Controlla se deve essere attivato l'evento 'Start'. Il risultato interessa solo l'evento ' Start ', ma altre strumentazioni non dipendono da esso.

Non esiste alcun `IsEnabled` per l'evento ' stop '.

Se il risultato di alcune operazioni è un'eccezione, viene chiamato `IsEnabled("Microsoft.Azure.ServiceBus.Exception")`. È possibile eseguire la sottoscrizione solo di eventi 'Exception' e impedire il resto della strumentazione. In questo caso sarà comunque necessario gestire tali eccezioni. Poiché l'altra strumentazione è disabilitata, non è previsto che il contesto di traccia scorra con i messaggi dal consumer al producer.

È possibile usare `IsEnabled` anche per implementare strategie di campionamento. Il campionamento basato su `Activity.Id` o `Activity.RootId` garantisce un campionamento coerente in tutti i pneumatici (purché venga propagato dal sistema di traccia o dal codice).

In presenza di più `DiagnosticSource` listener per la stessa origine, è sufficiente che un solo listener accetti l'evento, quindi non esiste alcuna garanzia che `IsEnabled` venga chiamato.

---

## <a name="next-steps"></a>Passaggi successivi

* [Correlazione di dati di telemetria in Application Insights](../azure-monitor/app/correlation.md)
* [Impostare Application Insights: tenere traccia delle dipendenze](../azure-monitor/app/asp-net-dependencies.md) per verificare se REST, SQL o altre risorse esterne causano rallentamenti.
* [Verifica delle operazioni personalizzate con Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)

