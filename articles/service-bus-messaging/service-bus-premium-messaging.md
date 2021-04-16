---
title: bus di servizio di Azure premium e standard
description: Questo articolo descrive i livelli Standard e Premium di bus di servizio di Azure. Confronta questi livelli e fornisce differenze tecniche.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: b7117da6a959181704dd136c6d5be5ab62edef55
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389486"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Livelli di messaggistica Standard e Premium del bus di servizio

La messaggistica del bus di servizio, che include entità come code e argomenti, unisce funzionalità di messaggistica aziendale a una semantica di pubblicazione-sottoscrizione completa a livello di cloud. La messaggistica del bus di servizio viene usata come backbone di comunicazione per molte soluzioni cloud avanzate.

Il *livello Premium* di messaggistica del bus di servizio risolve le richieste dei clienti comuni relative a scalabilità, prestazioni e disponibilità per le applicazioni cruciali. Per gli scenari di produzione è consigliabile usare il livello Premium. Anche se i set di funzionalità sono quasi identici, questi due livelli di messaggistica del bus di servizio sono progettati per gestire diversi casi d'uso.

Nella tabella seguente sono evidenziate alcune differenze generali.

| Premium | Standard |
| --- | --- |
| Velocità effettiva elevata |Velocità effettiva variabile |
| Prestazioni prevedibili |Latenza variabile |
| Prezzi fissi |Prezzi variabili in base all'uso |
| Possibilità di aumentare o ridurre le prestazioni del carico di lavoro |N/D |
| Dimensioni del messaggio fino a 1 MB. Questo limite potrebbe essere aumentato in futuro. Per gli ultimi aggiornamenti importanti del servizio, vedere il [blog Messaggistica in Azure](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog). |Dimensione messaggi fino a 256 KB |

La **messaggistica di livello Premium del bus di servizio** garantisce l'isolamento delle risorse a livello di CPU e di memoria in modo che ogni carico di lavoro del cliente venga eseguito in isolamento. Questo contenitore di risorse è detto unità *di messaggistica*. Ad ogni spazio dei nomi Premium viene allocata almeno un'unità di messaggistica. È possibile acquistare 1, 2, 4, 8 o 16 unità di messaggistica per ogni spazio dei nomi Premium del bus di servizio. Un singolo carico di lavoro o un'entità può estendersi su più unità di messaggistica e il numero di unità di messaggistica può essere modificato in base alle diverse modalità. Ne risultano prestazioni prevedibili e ripetibili per la soluzione basata sul bus di servizio.

Non solo le prestazioni sono più prevedibili e disponibili, ma anche più veloci. Con la messaggistica Premium, le prestazioni massime sono più veloci rispetto al livello Standard.

> [!NOTE]
> Il limite di dimensioni del batch per la messaggistica Premium è di 1 MB.

## <a name="premium-messaging-technical-differences"></a>Differenze tecniche della messaggistica Premium

Le sezioni seguenti illustrano alcune differenze tra i livelli di messaggistica Standard e Premium.

### <a name="partitioned-queues-and-topics"></a>Code e argomenti partizionati

Code e argomenti partizionati non sono supportati in Messaggistica Premium. Per altre informazioni sul partizionamento, vedere [Code e argomenti partizionati](service-bus-partitioning.md).

### <a name="express-entities"></a>Entità Express

Poiché la messaggistica Premium viene eseguita in un ambiente di run-time isolato, le entità rapide non sono supportate negli spazi dei nomi Premium. Un'entità rapida mantiene temporaneamente un messaggio in memoria prima di scriverlo nell'archiviazione persistente. Se si dispone di codice in esecuzione nella messaggistica Standard e si vuole eseguire il port nel livello Premium, assicurarsi che la funzionalità di entità express sia disabilitata.

## <a name="premium-messaging-resource-usage"></a>Utilizzo delle risorse di Messaggistica Premium
In generale, qualsiasi operazione su un'entità può causare l'utilizzo della CPU e della memoria. Ecco alcune di queste operazioni: 

- Operazioni di gestione, ad esempio operazioni CRUD (Creazione, Recupero, Aggiornamento ed Eliminazione) su code, argomenti e sottoscrizioni.
- Operazioni di runtime (invio e ricezione di messaggi)
- Monitoraggio di operazioni e avvisi

L'utilizzo aggiuntivo di CPU e memoria non ha tuttavia un prezzo aggiuntivo. Per il livello di messaggistica Premium, è presente un singolo prezzo per l'unità messaggi.

L'utilizzo della CPU e della memoria viene monitorato e visualizzato per i motivi seguenti: 

- Fornire trasparenza nelle risorse interne del sistema
- Comprendere la capacità delle risorse acquistate.
- Pianificazione della capacità che consente di decidere di aumentare/ridurre le prestazioni.

## <a name="messaging-unit---how-many-are-needed"></a>Unità di messaggistica: quanti sono necessari?

Quando si esegue il provisioning bus di servizio di Azure spazio dei nomi Premium, è necessario specificare il numero di unità di messaggistica allocate. Queste unità di messaggistica sono risorse dedicate allocate allo spazio dei nomi.

Il numero di unità di messaggistica allocate  allo spazio dei nomi Premium del bus di servizio può essere modificato dinamicamente in base alla modifica (aumento o diminuzione) nei carichi di lavoro.

Quando si decide il numero di unità di messaggistica per l'architettura, è necessario prendere in considerazione diversi fattori:

- Iniziare con ***1 o 2 unità di messaggistica*** allocate allo spazio dei nomi.
- Studiare le metriche di utilizzo della CPU all'interno [delle metriche di utilizzo delle risorse](service-bus-metrics-azure-monitor.md#resource-usage-metrics) per lo spazio dei nomi.
    - Se l'utilizzo della CPU è * inferiore al **20%,** è possibile *_aumentare_* il numero di unità di messaggistica allocate allo spazio dei nomi.
    - Se l'utilizzo della CPU è * superiore al **70%** _, l'applicazione trarrà vantaggio da _ *_aumento_** il numero di unità di messaggistica allocate allo spazio dei nomi.

Per informazioni su come configurare uno spazio dei nomi del bus di servizio per ridimensionare automaticamente (aumentare o ridurre le unità di messaggistica), vedere [Aggiornare automaticamente le](automate-update-messaging-units.md)unità di messaggistica .

> [!NOTE]
> **Il** ridimensionamento delle risorse allocate allo spazio dei nomi può essere preventivo o reattivo.
>
>  * **Preemptive: se** è previsto un carico di lavoro aggiuntivo (a causa della stagionalità o delle tendenze), è possibile procedere all'allocazione di più unità di messaggistica allo spazio dei nomi prima che i carichi di lavoro possano essere raggiunto.
>
>  * **Reattivo:** se vengono identificati carichi di lavoro aggiuntivi tramite lo studio delle metriche di utilizzo delle risorse, è possibile allocare risorse aggiuntive allo spazio dei nomi per incorporare l'aumento della domanda.
>
> I contatori di fatturazione per il bus di servizio sono orari. In caso di aumento delle risorse, si paga solo per le risorse aggiuntive per le ore in cui sono state usate.
>

## <a name="get-started-with-premium-messaging"></a>Introduzione alla messaggistica Premium

Iniziare a usare la messaggistica Premium è estremamente semplice e il processo è simile a quello della messaggistica Standard. Iniziare [creando uno spazio dei nomi](service-bus-create-namespace-portal.md) nel [portale di Azure](https://portal.azure.com). Assicurarsi di selezionare **Premium** in **Piano tariffario.** Fai clic su **Visualizza i dettagli sui prezzi completi** per vedere altre informazioni su ogni livello.

![create-premium-namespace][create-premium-namespace]

È anche possibile creare [spazi dei nomi Premium usando i modelli di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedi i collegamenti seguenti:

- [Aggiornare automaticamente le unità di messaggistica](automate-update-messaging-units.md).
- [Introducing bus di servizio di Azure Premium Messaging (Introduzione alla messaggistica Premium) (post di blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introducing Azure Service Bus Premium Messaging](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Introduzione alla messaggistica Premium del bus di servizio di Azure, Channel9)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
