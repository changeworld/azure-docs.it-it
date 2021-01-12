---
title: Metriche in Monitoraggio di Azure - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come usare il monitoraggio di Azure per monitorare Hub eventi di Azure
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5b055c02783c40d844d1c6306bbb71cb23d602f2
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118796"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metriche di Hub eventi di Azure in Monitoraggio di Azure

Le metriche di hub eventi forniscono lo stato delle risorse di hub eventi nella sottoscrizione di Azure. Grazie a un set completo di dati delle metriche è possibile valutare l'integrità generale delle risorse degli hub eventi, non solo a livello di spazio dei nomi, ma anche a livello di entità. Queste statistiche possono rivelarsi importanti perché consentono di monitorare lo stato degli hub eventi. Le metriche consentono anche di risolvere i problemi senza dover contattare il supporto di Azure.

Monitoraggio di Azure offre interfacce utente unificate per il monitoraggio di diversi servizi di Azure. Per altre informazioni, vedere [Panoramica sul monitoraggio in Microsoft Azure](../azure-monitor/overview.md) e l'esempio che descrive come [recuperare le metriche di Monitoraggio di Azure con .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) in GitHub.

## <a name="access-metrics"></a>Accedere alle metriche

Monitoraggio di Azure offre molti modi per accedere alle metriche. È possibile accedere alle metriche dal [portale di Azure](https://portal.azure.com) o usare le API di Monitoraggio di Azure (REST e .NET) e soluzioni di analisi come Log Analytics e Hub eventi. Per altre informazioni, consultare [Monitoraggio dei dati raccolti da Monitoraggio di Azure](../azure-monitor/platform/data-platform.md).

Le metriche sono abilitate per impostazione predefinita ed è possibile accedere ai 30 giorni di dati più recenti. Se è necessario conservare i dati per un periodo di tempo più lungo, è possibile archiviare i dati relativi alle metriche in un account di archiviazione di Azure. Questo approccio viene configurato nelle [Impostazioni di diagnostica](../azure-monitor/platform/diagnostic-settings.md) in Monitoraggio di Azure.


## <a name="access-metrics-in-the-portal"></a>Accedere alle metriche nel portale

È possibile monitorare le metriche nel tempo nel [portale di Azure](https://portal.azure.com). L'esempio seguente illustra come visualizzare le richieste completate e le richieste in ingresso a livello di account:

![Visualizzare le metriche riuscite][1]

È anche possibile accedere alle metriche direttamente tramite lo spazio dei nomi. A tale scopo, selezionare lo spazio dei nomi e fare clic su **Metriche**. Per visualizzare le metriche filtrate in base all'ambito dell'hub eventi, selezionare l'hub eventi e quindi fare clic su **Metriche**.

Per le metriche che supportano le dimensioni, è necessario filtrare specificando il valore di dimensione da usare come illustrato nell'esempio seguente:

![Filtrare per valore di dimensione][2]

## <a name="billing"></a>Fatturazione

L'uso delle metriche in Monitoraggio di Azure è attualmente gratuito. Se tuttavia si usano soluzioni aggiuntive per inserire i dati delle metriche, è possibile che la fatturazione venga effettuata da tali soluzioni. Ad esempio, la fatturazione viene effettuata da Archiviazione di Azure se i dati relativi alle metriche vengono archiviati in un account di Archiviazione di Azure. Viene anche addebitato da Azure se i dati di metrica vengono trasmessi ai log di monitoraggio di Azure per l'analisi avanzata.

Le metriche seguenti offrono una panoramica dell'integrità del servizio. 

> [!NOTE]
> Numerose metriche risultano attualmente deprecate perché hanno assunto un nome diverso. Ciò potrebbe rendere necessario l'aggiornamento dei riferimenti. Le metriche contrassegnate con la parola "deprecata" non saranno supportate in futuro.

Tutti i valori delle metriche vengono inviati a Monitoraggio di Azure ogni minuto. La granularità temporale definisce l'intervallo di tempo per il quale vengono presentati i valori delle metriche. L'intervallo di tempo supportato per tutte le metriche di tutti gli hub eventi è 1 minuto.

## <a name="azure-event-hubs-metrics"></a>Metriche di hub eventi di Azure
Per un elenco delle metriche supportate dal servizio, vedere [Hub eventi di Azure](../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)

## <a name="azure-monitor-integration-with-siem-tools"></a>Integrazione di monitoraggio di Azure con gli strumenti SIEM
Il routing dei dati di monitoraggio (log attività, log di diagnostica e così via) a un hub eventi con monitoraggio di Azure consente di integrare facilmente gli strumenti di gestione di informazioni ed eventi di sicurezza (SIEM). Per ulteriori informazioni, vedere gli articoli e i post di Blog seguenti:

- [Trasmettere i dati di monitoraggio di Azure a un hub eventi per il consumo da parte di uno strumento esterno](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Introduzione a Integrazione log di Azure](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Usare Monitoraggio di Azure per l'integrazione con strumenti SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

Nello scenario in cui uno strumento SIEM usa i dati di log da un hub eventi, se non viene visualizzato alcun messaggio in arrivo o se si visualizzano messaggi in arrivo ma nessun messaggio in uscita nel grafico delle metriche, attenersi alla procedura seguente:

- Se non sono presenti **messaggi in ingresso**, significa che il servizio monitoraggio di Azure non sta muovendo i log di controllo/diagnostica nell'hub eventi. In questo scenario, aprire un ticket di supporto con il team di monitoraggio di Azure. 
- Se sono presenti messaggi in ingresso, ma **nessun messaggio in uscita**, significa che l'applicazione Siem non sta leggendo i messaggi. Contattare il provider SIEM per determinare se la configurazione dell'hub eventi è corretta.


## <a name="next-steps"></a>Passaggi successivi

* Vedere [Panoramica sul monitoraggio in Microsoft Azure](../azure-monitor/overview.md).
* Esempio che descrive come [recuperare le metriche di Monitoraggio di Azure con .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) in GitHub. 

Per altre informazioni su Hub eventi, vedere i collegamenti seguenti:

- Iniziare con un'esercitazione di Hub eventi
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
