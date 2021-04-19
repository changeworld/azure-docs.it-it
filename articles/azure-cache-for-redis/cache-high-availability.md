---
title: Disponibilità elevata per cache di Azure per Redis
description: Informazioni sulle cache di Azure per Redis e sulle opzioni di disponibilità elevata
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 6c44c87221442797f063877385ac5eb7f8585850
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719098"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Disponibilità elevata per cache di Azure per Redis

cache di Azure per Redis ha disponibilità elevata incorporata. L'obiettivo dell'architettura a disponibilità elevata è garantire il funzionamento dell'istanza redis gestita anche quando le macchine virtuali sottostanti sono influenzate da interruzioni pianificate o non pianificate. Offre percentuali molto più elevate di quelle raggiungibili ospitando Redis in una singola macchina virtuale.

cache di Azure per Redis la disponibilità elevata usando più macchine virtuali, denominate *nodi*, per una cache. Configura questi nodi in modo che la replica dei dati e il failover avvengano in modo coordinato. Orchestra anche le operazioni di manutenzione, ad esempio l'applicazione di patch al software Redis. Nei livelli Standard, Premium ed Enterprise sono disponibili diverse opzioni di disponibilità elevata:

| Opzione | Descrizione | Disponibilità | Standard | Premium | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Replica standard](#standard-replication)| Configurazione replicata a doppio nodo in un singolo data center con failover automatico | 99,9% (vedere [i dettagli)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Ridondanza della zona](#zone-redundancy) | Configurazione replicata a più nodi tra le AZ, con failover automatico | Fino al 99,99% (vedere [i dettagli)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|Anteprima|✔|
| [Replica geografica](#geo-replication) | Istanze della cache collegate in due aree, con failover controllato dall'utente | Fino al 99,999% (vedere [i dettagli)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|Anteprima|

## <a name="standard-replication"></a>Replica standard

Un cache di Azure per Redis nel livello Standard o Premium viene eseguito in una coppia di server Redis per impostazione predefinita. I due server sono ospitati in macchine virtuali dedicate. Redis open source consente a un solo server di gestire le richieste di scrittura dei dati. Questo server è il *nodo primario,* mentre l'altra *replica*. Dopo aver effettuato il provisioning dei nodi del server, cache di Azure per Redis assegna loro i ruoli primario e di replica. Il nodo primario è in genere responsabile della manutenzione delle richieste di scrittura e lettura dai client Redis. In un'operazione di scrittura, esegue il commit di una nuova chiave e di un aggiornamento della chiave nella memoria interna e risponde immediatamente al client. Inoltra l'operazione alla replica in modo asincrono.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Configurazione della replica dei dati":::
   
>[!NOTE]
>In genere, un client Redis comunica con il nodo primario in una cache Redis per tutte le richieste di lettura e scrittura. Alcuni client Redis possono essere configurati per la lettura dal nodo di replica.
>
>

Se il nodo primario in una cache Redis non è disponibile, la replica verrà alzata di livello per diventare automaticamente il nuovo nodo primario. Questo processo è detto *failover.* La replica attenderà un tempo sufficientemente lungo prima di assumere il controllo nel caso in cui il nodo primario venga ripristinato rapidamente. Quando si verifica un failover, cache di Azure per Redis esegue il provisioning di una nuova macchina virtuale e la aggiunge alla cache come nodo di replica. La replica esegue una sincronizzazione completa dei dati con il database primario in modo che abbia un'altra copia dei dati della cache.

Un nodo primario può uscire dal servizio come parte di un'attività di manutenzione pianificata, ad esempio il software Redis o l'aggiornamento del sistema operativo. Può anche smettere di funzionare a causa di eventi non pianificati, ad esempio errori nell'hardware, nel software o nella rete sottostanti. [Failover e applicazione di patch cache di Azure per Redis](cache-failover.md) una spiegazione dettagliata dei tipi di failover Redis. Un cache di Azure per Redis verrà eseguito tramite molti failover nel corso della sua durata. L'architettura a disponibilità elevata è progettata per rendere queste modifiche all'interno di una cache il più possibile trasparenti per i client.

>[!NOTE]
>Gli elementi seguenti sono disponibili come anteprima.
>
>

Inoltre, cache di Azure per Redis nodi di replica aggiuntivi nel livello Premium. Una [cache a più repliche](cache-how-to-multi-replicas.md) può essere configurata con un massimo di tre nodi di replica. La disponibilità di più repliche migliora in genere la resilienza a causa dei nodi aggiuntivi che esercitino il backup del database primario. Anche con più repliche, un'cache di Azure per Redis può comunque essere gravemente influenzata da un'interruzione a livello di data center o AZ. È possibile aumentare la disponibilità della cache usando più repliche in combinazione con la [ridondanza della zona.](#zone-redundancy)

## <a name="zone-redundancy"></a>Ridondanza della zona

cache di Azure per Redis le configurazioni con ridondanza della zona nei livelli Premium ed Enterprise. Una [cache con ridondanza](cache-how-to-zone-redundancy.md) della zona può posizionare i nodi tra [zone di disponibilità di Azure](../availability-zones/az-overview.md) nella stessa area. Elimina l'interruzione del data center o dell'AZ come singolo punto di errore e aumenta la disponibilità complessiva della cache.

### <a name="premium-tier"></a>Livello Premium

>[!NOTE]
>Questa opzione è disponibile come anteprima.
>
>

Il diagramma seguente illustra la configurazione con ridondanza della zona per il livello Premium:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Configurazione della ridondanza della zona":::
   
cache di Azure per Redis distribuisce i nodi in una cache con ridondanza della zona in modo round robin sulle AZ selezionate. Determina anche quale nodo fungerà inizialmente da nodo primario.

Una cache con ridondanza della zona fornisce il failover automatico. Quando il nodo primario corrente non è disponibile, una delle repliche avrà il controllo. L'applicazione potrebbe avere un tempo di risposta della cache superiore se il nuovo nodo primario si trova in una zona AZ diversa. Le AZ sono separate geograficamente. Il passaggio da una rete AZ a un'altra modifica la distanza fisica tra la posizione in cui sono ospitate l'applicazione e la cache. Questa modifica influisce sulle latenze di rete round trip dall'applicazione alla cache. La latenza aggiuntiva dovrebbe rientrare in un intervallo accettabile per la maggior parte delle applicazioni. È consigliabile testare l'applicazione per assicurarsi che funzioni bene con una cache con ridondanza della zona.

### <a name="enterprise-tiers"></a>Livelli Enterprise

Una cache in uno dei due livelli Enterprise viene eseguita in un cluster Redis Enterprise. Richiede sempre un numero dispari di nodi server per formare un quorum. Per impostazione predefinita, è costituito da tre nodi, ognuno ospitato in una macchina virtuale dedicata. Una cache aziendale ha due nodi dati di dimensioni *uguali e* un nodo quorum *più piccolo.* Una Enterprise Flash cache ha tre nodi dati di dimensioni uguali. Il cluster Enterprise divide internamente i dati Redis in partizioni. Ogni partizione ha una *replica primaria* e almeno una *replica*. Ogni nodo dati contiene una o più partizioni. Il cluster Enterprise garantisce che le repliche primarie e di qualsiasi partizione non siano mai presenti nello stesso nodo dati. Le partizioni replicano i dati in modo asincrono dalle repliche primarie alle repliche corrispondenti.

Quando un nodo dati non è più disponibile o si verifica una suddivisione di rete, viene eseguito un failover simile a quello descritto in [Replica standard.](#standard-replication) Il cluster Enterprise usa un modello basato su quorum per determinare quali nodi ancora esistenti parteciperanno a un nuovo quorum. Alza inoltre di livello le partizioni di replica all'interno di questi nodi a primarie in base alle esigenze.

## <a name="geo-replication"></a>Replica geografica

[La replica geografica](cache-how-to-geo-replication.md) è un meccanismo per collegare due o più istanze di cache di Azure per Redis, che in genere si estendono su due aree di Azure. 

### <a name="premium-tier"></a>Livello Premium

>[!NOTE]
>La replica geografica nel livello Premium è progettata principalmente per il ripristino di emergenza.
>
>

Due istanze di cache di livello Premium possono essere connesse tramite [la replica geografica,](cache-how-to-geo-replication.md) in modo da poter eseguire il backup dei dati della cache in un'area diversa. Una volta collegato, un'istanza viene designata come cache collegata primaria e l'altra come cache collegata secondaria. Solo la cache primaria accetta richieste di lettura e scrittura. I dati scritti nella cache primaria vengono replicati nella cache secondaria. Un'applicazione accede alla cache tramite endpoint separati per le cache primaria e secondaria. L'applicazione deve inviare tutte le richieste di scrittura alla cache primaria quando viene distribuita in più aree di Azure. Può leggere dalla cache primaria o secondaria. In generale, si vuole che le istanze di calcolo dell'applicazione leggono dalle cache più vicine per ridurre la latenza. Il trasferimento dei dati tra le due istanze della cache è protetto da TLS.

La replica geografica non fornisce il failover automatico a causa di problemi di aggiunta del tempo di roundtrip di rete tra aree se il resto dell'applicazione rimane nell'area primaria. È necessario gestire e avviare il failover scollegando la cache secondaria. Verrà alzata di livello la nuova istanza primaria.

### <a name="enterprise-tiers"></a>Livelli Enterprise

>[!NOTE]
>Questa opzione è disponibile come anteprima.
>
>

I livelli Enterprise supportano una forma più avanzata di replica geografica, denominata [replica geografica attiva.](cache-how-to-active-geo-replication.md) Sfruttando i tipi di dati replicati senza conflitti, il software Redis Enterprise supporta le scritture in più istanze della cache e, se necessario, si occupa dell'unione delle modifiche e della risoluzione dei conflitti. È possibile aggiungere due o più istanze della cache di livello Enterprise in aree di Azure diverse per formare una cache con replica geografica attiva. Un'applicazione che usa tale cache può leggere e scrivere nelle istanze della cache con distribuzione geografica tramite gli endpoint corrispondenti. Deve usare il valore più vicino a ogni istanza di calcolo, che offre la latenza più bassa. L'applicazione deve anche monitorare le istanze della cache e passare a un'altra area se una delle istanze diventa non disponibile. Per altre informazioni sul funzionamento della replica geografica attiva, vedere [Active-Active Geo-Distriubtion (CRDTs-Based)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come configurare cache di Azure per Redis a disponibilità elevata.

* [cache di Azure per Redis di servizio Premium](cache-overview.md#service-tiers)
* [Aggiungere repliche a cache di Azure per Redis](cache-how-to-multi-replicas.md)
* [Abilitare la ridondanza della zona per cache di Azure per Redis](cache-how-to-zone-redundancy.md)
* [Configurare la replica geografica per cache di Azure per Redis](cache-how-to-geo-replication.md)
