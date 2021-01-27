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
ms.openlocfilehash: 9bc641d680d927c44814f6814ebf6a6dde958c9e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901006"
---
Funzioni di Azure consente di creare attività di replica solo di configurazione che si basano su un punto di ingresso predefinito. Gli [esempi di replica basati sulla configurazione per funzioni di Azure](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) illustrano come sfruttare gli [Helper predefiniti](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) nel codice o evitare di gestire completamente il codice e usare semplicemente la configurazione.

## <a name="create-a-replication-task"></a>Creare un'attività di replica
Per creare un'attività di replica di questo tipo, creare prima di tutto una nuova cartella sotto la cartella del progetto. Il nome della nuova cartella è il nome della funzione, ad esempio `europeToAsia` o `telemetry` . Il nome non ha correlazione diretta con le entità di messaggistica in uso e serve solo per identificarle. È possibile creare dozzine di funzioni nello stesso progetto.

Successivamente, creare un `function.json` file nella cartella. Il file configura la funzione. Iniziare con il contenuto seguente:

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

In tale file è necessario completare tre passaggi di configurazione che dipendono da quali entità si desidera connettere:

1. [Configurare la direzione di input](#configure-the-input-direction)
2. [Configurare la direzione di output](#configure-the-output-direction)
3. [Configurare il punto di ingresso](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>Configurare la direzione di input

#### <a name="event-hub-input"></a>Input dell'hub eventi

Se si desidera ricevere eventi da un hub eventi, aggiungere le informazioni di configurazione alla sezione superiore all'interno di "binding" che imposta

* **Type** : tipo "eventHubTrigger".
* **Connection (connessione** ): nome del valore delle impostazioni dell'app per la stringa di connessione dell'hub eventi. 
* **eventHubName** : nome dell'hub eventi nello spazio dei nomi identificato dalla stringa di connessione.
* **gruppo Consumer** : nome del gruppo di consumer. Si noti che il nome è racchiuso tra segni di percentuale e che quindi fa riferimento anche a un valore di impostazioni dell'app.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Input della coda del bus di servizio

Se si desidera ricevere eventi da una coda del bus di servizio, aggiungere le informazioni di configurazione alla sezione superiore all'interno di "binding" che imposta

* **Type** : tipo "serviceBusTrigger".
* **Connection (connessione** ): nome del valore delle impostazioni dell'app per la stringa di connessione del bus di servizio.
* **QueueName** : nome della coda del bus di servizio nello spazio dei nomi identificato dalla stringa di connessione.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Input argomento del bus di servizio

Se si desidera ricevere eventi da un argomento del bus di servizio, aggiungere le informazioni di configurazione alla sezione superiore all'interno di "binding" che imposta

* **Type** : tipo "serviceBusTrigger".
* **Connection (connessione** ): nome del valore delle impostazioni dell'app per la stringa di connessione del bus di servizio. Questo valore deve essere `{FunctionName}-source-connection` se si desidera utilizzare gli script forniti.
* **topicName** : nome dell'argomento del bus di servizio nello spazio dei nomi identificato dalla stringa di connessione.
* **subscriptionname** : nome della sottoscrizione del bus di servizio nell'argomento specificato nello spazio dei nomi identificato dalla stringa di connessione.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>Configurare la direzione di output

#### <a name="event-hub-output"></a>Output dell'hub eventi

Se si vuole inviare eventi a un hub eventi, aggiungere le informazioni di configurazione alla sezione inferiore all'interno di "binding" che imposta

* **Type** : tipo "eventHub".
* **Connection (connessione** ): nome del valore delle impostazioni dell'app per la stringa di connessione dell'hub eventi. 
* **eventHubName** : nome dell'hub eventi nello spazio dei nomi identificato dalla stringa di connessione.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Output coda del bus di servizio

Se si vuole inviare gli eventi a una coda del bus di servizio, aggiungere le informazioni di configurazione alla sezione inferiore all'interno di "binding" che imposta

* **digitare** : tipo "serviceBus".
* **Connection (connessione** ): nome del valore delle impostazioni dell'app per la stringa di connessione del bus di servizio. 
* **QueueName** : nome della coda del bus di servizio nello spazio dei nomi identificato dalla stringa di connessione.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Output argomento del bus di servizio

Se si vuole inviare eventi a un argomento del bus di servizio, aggiungere le informazioni di configurazione alla sezione inferiore all'interno di "binding" che imposta

* **digitare** : tipo "serviceBus".
* **Connection (connessione** ): nome del valore delle impostazioni dell'app per la stringa di connessione del bus di servizio. 
* **topicName** : nome dell'argomento del bus di servizio nello spazio dei nomi identificato dalla stringa di connessione.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Configurare il punto di ingresso

La configurazione del punto di ingresso sceglie una delle attività di replica standard. Se si modifica il `Azure.Messaging.Replication` progetto, è anche possibile aggiungere attività e farvi riferimento qui. Ad esempio:

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

La tabella seguente contiene i valori corretti per le combinazioni di origini e destinazioni:

| Source (Sorgente)      | Destinazione      | Punto di ingresso 
|-------------|-------------|------------------------------------------------------------------------
| Hub eventi   | Hub eventi   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Hub eventi   | Bus di servizio | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Bus di servizio | Hub eventi   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Bus di servizio | Bus di servizio | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Criterio di ripetizione dei tentativi

Per configurare i criteri di ripetizione dei tentativi, vedere la [documentazione di funzioni di Azure](../articles/azure-functions/functions-bindings-error-pages.md) . Le impostazioni dei criteri scelti in tutti i progetti in questo repository configurano una strategia backoff esponenziale con intervalli di tentativi da 5 secondi a 5 minuti con tentativi infiniti per evitare la perdita di dati.

Per il bus di servizio, vedere la sezione ["uso del supporto per tentativi nella parte superiore della resilienza dei trigger"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) per comprendere l'interazione dei trigger e il numero massimo di recapiti definiti per la coda.

### <a name="build-deploy-and-configure"></a>Compilazione, distribuzione e configurazione

Mentre si sta concentrando sulla configurazione, le attività richiedono comunque la compilazione di un'applicazione distribuibile e la configurazione degli host di funzioni di Azure in modo che disponga di tutte le informazioni necessarie per la connessione agli endpoint specificati. 

Questa operazione viene illustrata insieme agli script riutilizzabili negli [esempi di replica basati sulla configurazione per funzioni di Azure](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config).