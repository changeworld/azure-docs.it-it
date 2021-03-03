---
title: Guida per gli sviluppatori di JMS 2,0 per il bus di servizio
description: Come usare l'API Java Message Service (JMS) 2,0 per comunicare con il bus di servizio di Azure
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726955"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Guida per gli sviluppatori di JMS 2,0 per il bus di servizio

Questa guida contiene informazioni dettagliate per facilitare la comunicazione con il bus di servizio di Azure tramite l'API Java Message Service (JMS) 2,0.

Gli sviluppatori Java, se non si ha familiarità con il bus di servizio di Azure, si consiglia di leggere gli articoli seguenti.

| Guida introduttiva | Concetti |
|----------------|-------|
| <ul> <li> [Informazioni sul bus di servizio di Azure](service-bus-messaging-overview.md) </li> <li> [Code, argomenti e sottoscrizioni](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Bus di servizio di Azure-livello Premium](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Modello di programmazione JMS (Java Message Service)

Il modello di programmazione API del servizio messaggi Java è illustrato di seguito: 

> [!NOTE]
>
>Il **livello Premium del bus di servizio di Azure** supporta JMS 1,1 e JMS 2,0.
> <br> <br>
> **Bus di servizio di Azure:** il livello standard supporta la funzionalità limitata di JMS 1,1. Per ulteriori informazioni, consultare la presente [documentazione](service-bus-java-how-to-use-jms-api-amqp.md).
>

# <a name="jms-20-programming-model"></a>[Modello di programmazione JMS 2,0](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="Diagramma che illustra il modello di programmazione JMS 2,0." border="false":::

# <a name="jms-11-programming-model"></a>[Modello di programmazione JMS 1,1](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="Diagramma che illustra il modello di programmazione JMS 1,1." border="false":::

---

## <a name="jms---building-blocks"></a>JMS-blocchi predefiniti

I blocchi predefiniti seguenti sono disponibili per comunicare con l'applicazione JMS.

> [!NOTE]
> La guida seguente è stata adattata dall' [esercitazione Oracle Java EE 6 per Java Message Service (JMS)](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
>
> Per una migliore comprensione di Java Message Service (JMS), è consigliabile fare riferimento a questa esercitazione.
>

### <a name="connection-factory"></a>Factory di connessione
L'oggetto connection factory viene usato dal client per connettersi al provider JMS. La factory di connessione incapsula un set di parametri di configurazione della connessione definiti dall'amministratore.

Ogni factory di connessione è un'istanza di `ConnectionFactory` , `QueueConnectionFactory` o `TopicConnectionFactory` Interface.

Per semplificare la connessione al bus di servizio di Azure, queste interfacce vengono implementate `ServiceBusJmsConnectionFactory` `ServiceBusJmsQueueConnectionFactory` rispettivamente tramite e `ServiceBusJmsTopicConnectionFactory` . È possibile creare un'istanza della factory di connessione con i parametri seguenti: 
   * Stringa di connessione: la stringa di connessione per lo spazio dei nomi del livello Premium del bus di servizio di Azure.
   * Contenitore di proprietà ServiceBusJmsConnectionFactorySettings che contiene
      * connectionIdleTimeoutMS-timeout della connessione inattiva in millisecondi.
      * traceFrames-flag booleano per la raccolta di frame di traccia AMQP per il debug.
      * *altri parametri di configurazione*

La factory può essere creata come indicato di seguito. La stringa di connessione è un parametro obbligatorio, ma le proprietà aggiuntive sono facoltative.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> Le applicazioni Java che usano l'API JMS 2,0 devono connettersi al bus di servizio di Azure usando solo la stringa di connessione. Attualmente, l'autenticazione per i client JMS è supportata solo usando la stringa di connessione.
>
> L'autenticazione supportata da Azure Active Directory (AAD) non è attualmente supportata.
>

### <a name="jms-destination"></a>Destinazione JMS

Una destinazione è l'oggetto utilizzato da un client per specificare la destinazione dei messaggi che produce e l'origine dei messaggi utilizzati.

Le destinazioni sono mappate alle entità nel bus di servizio di Azure: code (negli scenari Point-to-Point) e argomenti (in scenari di pubblicazione-sub).

### <a name="connections"></a>Connessioni

Una connessione incapsula una connessione virtuale con un provider JMS. Con il bus di servizio di Azure, rappresenta una connessione con stato tra l'applicazione e il bus di servizio di Azure tramite AMQP.

Viene creata una connessione dalla factory di connessione come illustrato di seguito.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>Sessioni

Una sessione è un contesto a thread singolo per la produzione e l'utilizzo di messaggi. Può essere utilizzato per creare messaggi, Producer e consumer di messaggi, ma fornisce anche un contesto transazionale per consentire il raggruppamento di invii e ricevimenti in un'unità di lavoro atomica.

È possibile creare una sessione dall'oggetto connessione, come illustrato di seguito.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>Modalità sessione

Una sessione può essere creata con una qualsiasi delle modalità indicate di seguito.

| Modalità sessione | Comportamento |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | La sessione riconosce automaticamente la ricezione di un messaggio da parte di un client quando la sessione viene restituita correttamente da una chiamata a Receive o quando il listener del messaggio chiamato dalla sessione per elaborare il messaggio viene restituito correttamente.|
|**Session.CLIENT_ACKNOWLEDGE** |Il client riconosce un messaggio utilizzato chiamando il metodo di riconoscimento del messaggio.|
|**Session.DUPS_OK_ACKNOWLEDGE**|Questa modalità di riconoscimento indica alla sessione di confermare in modo differito il recapito dei messaggi.| 
|**Session.SESSION_TRANSACTED**|Questo valore può essere passato come argomento al metodo createSession (int sessionMode) nell'oggetto Connection per specificare che la sessione deve usare una transazione locale.| 

Quando non viene specificata la modalità di sessione, per impostazione predefinita viene selezionato il **Session.AUTO_ACKNOWLEDGE** .

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext è definito come parte della specifica JMS 2,0.
>

JMSContext combina la funzionalità fornita dall'oggetto Connection e Session. È possibile crearlo dall'oggetto Connection Factory.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>Modalità JMSContext

Analogamente all'oggetto **Session** , è possibile creare JMSContext con le stesse modalità di riconoscimento indicate in [modalità sessione](#session-modes).

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

Quando la modalità non viene specificata, per impostazione predefinita viene selezionato il **JMSContext.AUTO_ACKNOWLEDGE** .

### <a name="jms-message-producers"></a>Produttori di messaggi JMS

Un producer di messaggi è un oggetto creato mediante una JMSContext o una sessione e utilizzato per l'invio di messaggi a una destinazione.

È possibile crearlo come oggetto autonomo come indicato di seguito: 

```java
JMSProducer producer = context.createProducer();
```

o creato in fase di esecuzione quando è necessario inviare un messaggio.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>Consumer di messaggi JMS

Un consumer di messaggi è un oggetto creato da un JMSContext o una sessione e utilizzato per la ricezione di messaggi inviati a una destinazione. È possibile crearlo come illustrato di seguito:

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>Ricevute sincrone tramite il metodo Receive ()

Il consumer di messaggi fornisce un modo sincrono per ricevere messaggi dalla destinazione tramite il `receive()` metodo.

Se non viene specificato alcun argomento o timeout oppure viene specificato il timeout ' 0', il consumer si blocca a tempo indefinito, a meno che non arrivi il messaggio o la connessione venga interruppe (a seconda del valore precedente).

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

Quando viene specificato un argomento positivo diverso da zero, il consumer si blocca fino alla scadenza del timer.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>Ricezione asincrona con listener di messaggi JMS

Un listener di messaggi è un oggetto utilizzato per la gestione asincrona dei messaggi in una destinazione. Implementa l' `MessageListener` interfaccia che contiene il `onMessage` metodo in cui deve risiedere la logica di business specifica.

È necessario creare un'istanza di un oggetto listener di messaggi e registrarlo in base a un consumer di messaggi specifico utilizzando il `setMessageListener` metodo.

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>Utilizzo da argomenti

[I consumer di messaggi JMS](#jms-message-consumers) vengono creati a fronte di una [destinazione](#jms-destination) che può essere una coda o un argomento.

I consumer nelle code sono semplicemente oggetti lato client che risiedono nel contesto della sessione (e connessione) tra l'applicazione client e il bus di servizio di Azure.

Gli utenti degli argomenti, tuttavia, hanno 2 parti: 
   * **Oggetto lato client** che risiede nel contesto della sessione (o JMSContext), e.
   * Una **sottoscrizione** che rappresenta un'entità nel bus di servizio di Azure.

Le sottoscrizioni sono documentate [qui](java-message-service-20-entities.md#java-message-service-jms-subscriptions) e possono essere una delle seguenti: 
   * Sottoscrizioni durevoli condivise
   * Sottoscrizioni non durevoli condivise
   * Sottoscrizioni durevoli non condivise
   * Sottoscrizioni non durevoli non condivise

### <a name="jms-queue-browsers"></a>Browser coda JMS

L'API JMS fornisce un `QueueBrowser` oggetto che consente all'applicazione di esplorare i messaggi nella coda e visualizzare i valori di intestazione per ogni messaggio.

Un browser della coda può essere creato usando JMSContext, come indicato di seguito.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> L'API JMS non fornisce un'API per esplorare un argomento.
>
> Questo è dovuto al fatto che l'argomento stesso non archivia i messaggi. Non appena il messaggio viene inviato all'argomento, viene inoltrato alle sottoscrizioni appropriate.
>

### <a name="jms-message-selectors"></a>Selettori di messaggi JMS

I selettori di messaggi possono essere utilizzati ricevendo applicazioni per filtrare i messaggi ricevuti. Con i selettori di messaggi, l'applicazione ricevente esegue l'offload del lavoro di filtro dei messaggi al provider JMS (in questo caso, il bus di servizio di Azure) invece di assumere tale responsabilità.

I selettori possono essere utilizzati durante la creazione di uno dei consumer seguenti: 
   * Sottoscrizione durevole condivisa
   * Sottoscrizione durevole non condivisa
   * Sottoscrizione non durevole condivisa
   * Sottoscrizione non durevole non condivisa
   * Browser coda


## <a name="summary"></a>Riepilogo

Questa guida per gli sviluppatori ha illustrato il modo in cui le applicazioni client Java che usano Java Message Service (JMS) possono connettersi con il bus di servizio di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul bus di servizio di Azure e dettagli sulle entità JMS (Java Message Service), consultare i collegamenti seguenti: 
* [Bus di servizio: code, argomenti e sottoscrizioni](service-bus-queues-topics-subscriptions.md)
* [Bus di servizio-entità del servizio messaggi Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Supporto per il protocollo AMQP 1.0 nel bus di servizio di Azure](service-bus-amqp-overview.md)
* [Guida per sviluppatori di AMQP 1.0 per il bus di servizio](service-bus-amqp-dotnet.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [API del servizio messaggi Java (documento Oracle esterno)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Informazioni su come eseguire la migrazione da ActiveMQ a Service Bus](migrate-jms-activemq-to-servicebus.md)
