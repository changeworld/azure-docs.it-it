---
title: Panoramica della messaggistica del bus di servizio di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica generale del bus di servizio di Azure, un broker di messaggi completamente gestito per l'integrazione aziendale.
ms.topic: overview
ms.date: 02/16/2021
ms.openlocfilehash: 897729b9748d69ad3c6de507e800dbb3a1a3619c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570474"
---
# <a name="what-is-azure-service-bus"></a>Cos'è il bus di servizio di Azure?
Il bus di servizio di Microsoft Azure è un broker di messaggi aziendale completamente gestito, con code di messaggi e argomenti di pubblicazione-sottoscrizione. Il bus di servizio viene usato per disaccoppiare le applicazioni dai servizi, offrendo i vantaggi seguenti:

- Bilanciamento del carico di lavoro tra ruoli di lavoro in competizione
- Routing e trasferimento di dati in modo sicuro e controllo tra limiti di servizi e applicazioni
- Coordinamento delle operazioni transazionali che richiedono un livello elevato di affidabilità 

## <a name="overview"></a>Panoramica
I dati vengono trasferiti tra applicazioni e servizi diversi usando i *messaggi*. Un messaggio è un contenitore decorato con metadati che contiene dati. I dati possono essere qualsiasi tipo di informazioni, tra cui dati strutturati codificati con formati comuni come ad esempio JSON, XML, Apache Avro e testo normale.

Alcuni scenari di messaggistica comuni sono:

* *Messaggistica*. Trasferimento di dati aziendali, ad esempio gli ordini di vendita o di acquisto, i giornali di registrazione o i movimenti delle scorte.
* *Separazione delle applicazioni*. Miglioramento dell'affidabilità e della scalabilità di applicazioni e servizi. Il producer e il consumer non devono necessariamente essere online o immediatamente disponibili allo stesso tempo. Il [carico viene livellato](/azure/architecture/patterns/queue-based-load-leveling) in modo tale che i picchi del traffico non sovraccarichino un servizio. 
* *Bilanciamento del carico*. Consente a più [consumer in competizione](/azure/architecture/patterns/competing-consumers) di leggere da una coda contemporaneamente, ottenendo ognuno la proprietà esclusiva di specifici messaggi. 
* *Argomenti e sottoscrizioni*. Abilitano 1:*n* relazioni tra [autori di pubblicazioni e sottoscrittori](/azure/architecture/patterns/publisher-subscriber), consentendo ai sottoscrittori di selezionare specifici messaggi in un flusso di messaggi pubblicati.
* *Transazioni*. Consentono di eseguire diverse operazioni, tutte nell'ambito della transazione atomica. Ad esempio, nell'ambito di una transazione è possibile eseguire le operazioni seguenti.  

    1. Ottenere un messaggio da una coda.
    2. Pubblicare i risultati dell'elaborazione in una o più code diverse.
    3. Spostare il messaggio di input dalla coda originale. 
    
    I risultati diventano visibili ai consumer downstream solo quando l'operazione riesce, ad esempio quando il messaggio di input viene gestito correttamente, consentendo la semantica dell'elaborazione di tipo "once-only". Questo modello di transazioni rappresenta una solida base per il modello di [transazioni di compensazione](/azure/architecture/patterns/compensating-transaction) nel contesto più ampio della soluzione. 
* *Sessioni di messaggistica*. Implementano un coordinamento su larga scala di flussi di lavoro e trasferimenti multiplex che richiedono un ordinamento rigoroso dei messaggi o il differimento degli stessi.

Se si ha familiarità con altri broker di messaggi, come Apache ActiveMQ, i concetti del bus di servizio sono simili a quelli già noti. Poiché un bus di servizio è un'offerta PaaS (platform-as-a-service), la principale differenza è che non bisogna preoccuparsi delle azioni seguenti. Azure le gestisce automaticamente. 

- Inserimento di log e gestione dello spazio su disco
- Gestione dei backup
- Applicazione continua di patch a sistemi operativi o prodotti
- Errori hardware 
- Failover in un computer di riserva

## <a name="compliance-with-standards-and-protocols"></a>Conformità a standard e protocolli

Il protocollo di collegamento principale per il bus di servizio è [AMQP (Advanced Messaging Queueing Protocol) 1.0](service-bus-amqp-overview.md), uno standard ISO/IEC aperto. Consente ai clienti di scrivere applicazioni compatibili con il bus di servizio e con broker locali come ActiveMQ o RabbitMQ. La [guida del protocollo AMQP](service-bus-amqp-protocol-guide.md) fornisce informazioni dettagliate nel caso si decida di creare tale astrazione.

Il [bus di servizio Premium](service-bus-premium-messaging.md) è pienamente conforme all'API [Java Message Service (JMS) 2.0](how-to-use-java-message-service-20.md) di Java/Jakarta EE. E il bus di servizio Standard supporta il subset JMS 1.1 incentrato sulle code. JMS è un'astrazione comune per i broker di messaggi e si integra con molte applicazioni e diversi framework, tra cui il framework Spring ampiamente diffuso. Per passare al bus di servizio di Azure da altri broker, è sufficiente ricreare la topologia di code e argomenti, quindi cambiare le dipendenze del provider client e la configurazione. Per un esempio, vedere la [guida alla migrazione di ActiveMQ](migrate-jms-activemq-to-servicebus.md).

## <a name="concepts-and-terminology"></a>Concetti e terminologia 
Questa sessione descrive i concetti e la terminologia del bus di servizio.

### <a name="namespaces"></a>Spazi dei nomi
Uno spazio dei nomi è un contenitore per tutti i componenti di messaggistica. Più code e argomenti possono essere presenti in un unico spazio dei nomi e gli spazi dei nomi vengono spesso usati come contenitori di applicazioni. 

Uno spazio dei nomi può essere paragonato a un "server" nella terminologia di altri broker, ma i concetti non sono direttamente equivalenti. Lo spazio dei nomi di un bus di servizio rappresenta una specifica quota di capacità di un cluster di grandi dimensioni costituito da decine di macchine virtuali tutte attive. Facoltativamente può estendersi in tre [zone di disponibilità di Azure](../availability-zones/az-overview.md). Si ottengono così tutti i vantaggi di disponibilità e affidabilità associati all'esecuzione del broker di messaggi su scala enorme. Inoltre, non bisogna preoccuparsi delle complessità sottostanti. Il bus di servizio è un servizio di messaggistica "serverless".

### <a name="queues"></a>Code
I messaggi vengono inviati e ricevuti dalle *code*. Le code consentono di archiviare i messaggi fino a quando l'applicazione di destinazione è disponibile a riceverli ed elaborarli.

![Coda](./media/service-bus-messaging-overview/about-service-bus-queue.png)

I messaggi nelle code vengono ordinati e vi viene aggiunto un timestamp all'arrivo. Una volta accettato dal broker, il messaggio viene sempre conservato permanentemente in uno spazio di archiviazione a tripla ridondanza, suddiviso tra zone di disponibilità se lo spazio dei nomi è abilitato per le zone. Il bus di servizio non lascia mai i messaggi in memoria o nello spazio di archiviazione volatile dopo che sono stati segnalati al client e accettati.

I messaggi vengono recapitati in modalità *pull*, ovvero solo quando è richiesto. A differenza del modello di polling attivo di alcune altre code cloud, l'operazione pull può essere di lunga durata e viene completata solo quando un messaggio è disponibile. 

### <a name="topics"></a>Argomenti

È anche possibile usare gli *argomenti* per inviare e ricevere i messaggi. Mentre una coda viene spesso usata per la comunicazione da punto a punto, gli argomenti sono utili negli scenari di pubblicazione/sottoscrizione.

![Argomento](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Gli argomenti possono avere più sottoscrizioni indipendenti, che si collegano all'argomento e altrimenti funzionano esattamente come code dal lato destinatario. Un sottoscrittore a un argomento può ricevere una copia di ogni messaggio inviato a tale argomento. Le sottoscrizioni sono entità denominate. Le sottoscrizioni sono durevoli per impostazione predefinita, ma possono essere configurate per scadere e quindi per essere automaticamente eliminate. Tramite l'API JMS, il bus di servizio Premium consente anche di creare sottoscrizioni volatili che esistono per la durata della connessione.

È possibile definire regole per una sottoscrizione. Una regola prevede un *filtro* per definire una condizione per cui il messaggio deve essere copiato nella sottoscrizione e un'*azione* facoltativa che può modificare i metadati del messaggio. Per altre informazioni, vedere [Filtri e azioni per gli argomenti](topic-filters.md). Questa funzionalità è utile negli scenari seguenti:

- Si vuole evitare che una sottoscrizione riceva tutti i messaggi inviati a un argomento.
- Si vogliono contrassegnare i messaggi con metadati aggiuntivi quando attraversano una sottoscrizione.

## <a name="advanced-features"></a>Funzionalità avanzate

Il bus di servizio include anche funzionalità avanzate che consentono di risolvere problemi di messaggistica più complessi. Le sezioni seguenti descrivono alcune di queste funzionalità.

### <a name="message-sessions"></a>Sessioni di messaggistica

Per creare una garanzia FIFO (First-In-First-Out) nel bus di servizio, usare le sessioni. Le sessioni di messaggi consentono la gestione esclusiva e ordinata di sequenze illimitate di messaggi correlati. Per consentire la gestione di sessioni in sistemi a disponibilità elevata e su larga scala, la funzionalità offre anche la possibilità di archiviare lo stato della sessione, per cui le sessioni possono spostarsi tra gestori. Per altre informazioni, vedere [Sessioni di messaggistica: FIFO (First In, First Out)](message-sessions.md).

### <a name="autoforwarding"></a>Inoltro automatico

La funzionalità di inoltro automatico concatena una coda o una sottoscrizione a un'altra coda o a un altro argomento all'interno dello stesso spazio dei nomi. Con questa funzionalità, il bus di servizio sposta automaticamente i messaggi da una coda o da una sottoscrizione a una coda o a un argomento di destinazione. Tutti questi spostamenti vengono eseguiti in modo transazionale. Per altre informazioni, vedere [Concatenamento di entità del bus di servizio con l'inoltro automatico](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Coda di messaggi non recapitabili

Tutte le code e le sottoscrizioni di argomenti del bus di servizio sono associate a una coda di messaggi non recapitabili. Una coda di questo tipo contiene messaggi che soddisfano questi criteri: 

- Non possono essere recapitati correttamente a nessun destinatario.
- Hanno raggiunto il timeout.
- Vengono esplicitamente estromessi dall'applicazione ricevente. 

I messaggi di questa coda vengono contrassegnati con una nota che indica il motivo per cui vi sono stati inseriti. La coda di messaggi non recapitabili prevede un endpoint speciale, ma altrimenti funziona come qualsiasi altra normale coda. Un'applicazione o uno strumento può esplorare una coda di messaggi non recapitabili o rimuovere messaggi dal suo interno. È anche possibile inoltrare automaticamente i messaggi di questa coda. Per altre informazioni, vedere [Panoramica delle code dei messaggi non recapitabili del bus di servizio](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Recapito pianificato

È possibile inviare messaggi a una coda o a un argomento per l'elaborazione ritardata, impostando un'ora in cui il messaggio diventerà disponibile per l'utilizzo. I messaggi pianificati possono anche essere annullati. Per altre informazioni, vedere [Messaggi pianificati](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Differimento di messaggi

Un client di coda o di sottoscrizione può posticipare il recupero di un messaggio a un'ora successiva. È possibile che il messaggio sia stato pubblicato al di fuori di un ordine previsto e che il client voglia aspettare finché non ne riceve un altro. I messaggi differiti rimangono nella coda o nella sottoscrizione e devono essere riattivati esplicitamente usando il numero di sequenza assegnato dal servizio. Per altre informazioni, vedere [Differimento di messaggi](message-deferral.md).

### <a name="batching"></a>Creazione di batch

La creazione di batch sul lato client consente a un client di coda o di sottoscrizione di accumulare una serie di messaggi e di trasferirli tutti insieme. Questa opzione viene in genere scelta per risparmiare larghezza di banda o per aumentare la velocità effettiva. Per altre informazioni, vedere [Invio in batch sul lato client](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transazioni

Una transazione raggruppa due o più operazioni in un *ambito di esecuzione*. Il bus di servizio consente di raggruppare le operazioni rispetto a più entità di messaggistica all'interno dell'ambito di una singola transazione. Un'entità di messaggistica può essere una coda, un argomento o una sottoscrizione. Per altre informazioni, vedere [Panoramica dell'elaborazione delle transazioni del bus di servizio](service-bus-transactions.md).

### <a name="autodelete-on-idle"></a>Eliminazione automatica in caso di inattività
L'eliminazione automatica in caso di inattività consente di specificare un intervallo di inattività trascorso il quale una coda o una sottoscrizione viene automaticamente eliminata. La durata minima è 5 minuti. 

### <a name="duplicate-detection"></a>Rilevamento duplicati
La funzionalità di rilevamento duplicati consente al mittente di rinviare di nuovo lo stesso messaggio e al broker di rimuovere un possibile duplicato. Per altre informazioni, vedere [Rilevamento duplicati](duplicate-detection.md).

### <a name="geo-disaster-recovery"></a>Ripristino di emergenza geografico

Quando in un'area di Azure si verifica un tempo di inattività, la funzionalità di ripristino di emergenza assicura che l'elaborazione dati continui a essere eseguita in un'area o in un data center diverso. La funzionalità mantiene un mirror strutturale di uno spazio dei nomi disponibile nell'area secondaria e consente all'identità dello spazio dei nomi di passare allo spazio dei nomi secondario. I messaggi già pubblicati rimangono nel precedente spazio dei nomi primario per essere recuperati una volta risolto il problema di disponibilità. Per altre informazioni, vedere [Ripristino di emergenza geografico per il bus di servizio di Azure](service-bus-geo-dr.md).

### <a name="security"></a>Security

Il bus di servizio supporta i protocolli standard [AMQP 1.0](service-bus-amqp-overview.md) e [HTTP o REST](/rest/api/servicebus/) e le rispettive funzionalità di sicurezza, tra cui TLS (Transport Layer Security). I client possono essere autorizzati per l'accesso tramite [firma di accesso condiviso](service-bus-sas.md) o con la sicurezza basata sui ruoli di [Azure Active Directory](service-bus-authentication-and-authorization.md). 

Per la protezione da traffico indesiderato, il bus di servizio prevede [funzionalità di sicurezza](network-security.md) come il firewall IP e l'integrazione con reti virtuali. 

## <a name="client-libraries"></a>Librerie client

Le librerie client del bus di servizio pienamente supportate sono disponibili tramite Azure SDK.

- [Bus di servizio di Azure per .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Librerie del bus di servizio di Azure per Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Provider del bus di servizio di Azure per Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Moduli del bus di servizio di Azure per JavaScript e TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Librerie del bus di servizio di Azure per Python](/python/api/overview/azure/servicebus?preserve-view=true)

Il [protocollo primario del bus di servizio di Azure è AMQP 1.0](service-bus-amqp-overview.md) e può essere usato da qualsiasi client di protocollo conforme a AMQP 1.0. Diversi client AMQP open source includono esempi che dimostrano esplicitamente l'interoperabilità con il bus di servizio. Per informazioni su come usare le funzionalità del bus di servizio direttamente con client AMQP 1.0, vedere la [guida del protocollo AMQP 1.0](service-bus-amqp-protocol-guide.md).

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Integrazione

Il bus di servizio si integra completamente con molti servizi di Microsoft e Azure, ad esempio:

* [Griglia di eventi](service-bus-to-event-grid-integration-example.md)
* [App per la logica](../connectors/connectors-create-api-servicebus.md)
* [Funzioni di Azure](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare la messaggistica del bus di servizio, vedere gli articoli seguenti:

* Per confrontare i servizi di messaggistica di Azure, vedere [Confronto dei servizi](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Provare le guide introduttive per [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) o [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Per gestire le risorse del bus di servizio, vedere [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Per altre informazioni sui livelli Standard e Premium e i relativi prezzi, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).
* Per informazioni sulle prestazioni e la latenza per il livello Premium, vedere [Messaggistica Premium](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).