---
title: Azure Load Balancer e zone di disponibilità
titleSuffix: Azure Load Balancer
description: Con questo percorso di apprendimento, inizia a usare Load Balancer Standard e zone di disponibilità di Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 3c18b6d8dc44762649a9c07b88af348a18888fb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699114"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer e zone di disponibilità

Azure Load Balancer supporta scenari di zone di disponibilità. È possibile usare Load Balancer Standard per aumentare la disponibilità in tutto lo scenario allineando le risorse con e la distribuzione tra le zone.  Esaminare questo documento per comprendere questi concetti e le linee guida per la progettazione di scenari fondamentali

Un Load Balancer può essere con **ridondanza della zona, zona** o **non di zona**. Per configurare le proprietà correlate alla zona (sopra indicate) per il servizio di bilanciamento del carico, selezionare il tipo appropriato di front-end necessario.

## <a name="zone-redundant"></a>Con ridondanza della zona

In un'area con zone di disponibilità, una Load Balancer Standard può essere con ridondanza della zona. Questo traffico viene servito da un singolo indirizzo IP.

Un singolo indirizzo IP front-end sopravviverà in caso di errore della zona. L'IP front-end può essere usato per raggiungere tutti i membri del pool back-end (non interessati), a prescindere dalla zona. Una o più zone di disponibilità possono avere esito negativo e il percorso dei dati rimane integro fino a quando una zona dell'area rimane integra.

L'indirizzo IP del front-end viene servito simultaneamente da più distribuzioni di infrastrutture indipendenti in più zone di disponibilità. Eventuali tentativi o ristabilimenti riusciranno in altre zone non interessate dall'errore della zona.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="NAT di rete virtuale">
</p>

*Figura: bilanciamento del carico con ridondanza della zona*

## <a name="zonal"></a>Zona

È possibile scegliere di disporre di un front-end garantito per una singola zona, nota come *zona*.  Questo scenario indica che qualsiasi flusso in ingresso o in uscita viene servito da una singola zona in un'area.  La durata del front-end è legata all'integrità della zona.  Il percorso dati non è interessato dagli errori in zone diverse da quelle in cui è stato garantito. È possibile usare front-end di zona per esporre un indirizzo IP per zona di disponibilità.  

Inoltre, è supportato l'uso diretto dei front-end di zona per gli endpoint con carico bilanciato all'interno di ogni zona. È possibile usare questa configurazione per esporre gli endpoint con carico bilanciato per zona per monitorare singolarmente ogni zona. Per gli endpoint pubblici, è possibile integrarli con un prodotto di bilanciamento del carico DNS come [Gestione traffico](../traffic-manager/traffic-manager-overview.md) e usare un singolo nome DNS.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="NAT di rete virtuale">
</p>

*Figura: bilanciamento del carico di zona*

Per un front-end di Load Balancer pubblico, aggiungere un parametro **Zones** all'indirizzo IP pubblico. Alla configurazione IP front-end usata dalla rispettiva regola viene fatto riferimento a questo indirizzo IP pubblico.

Per un front-end di bilanciamento del carico interno, aggiungere un parametro **Zones** alla configurazione IP del servizio di bilanciamento del carico interno. Un front-end di zona garantisce un indirizzo IP in una subnet a una zona specifica.

## <a name="design-considerations"></a><a name="design"></a> Considerazioni sulla progettazione

Ora che si conoscono le proprietà correlate alla zona per Load Balancer Standard, le considerazioni di progettazione seguenti potrebbero essere utili per la progettazione per la disponibilità elevata.

### <a name="tolerance-to-zone-failure"></a>Tolleranza ad errore zona

- Una Load Balancer con **ridondanza della zona** può gestire una risorsa di zona in qualsiasi area con un indirizzo IP.  L'IP può sopravvivere a uno o più errori di zona purché almeno una zona rimanga integro all'interno dell'area.
- Un front-end di **zona** è una riduzione del servizio in una singola zona e condivide il destino con la rispettiva zona. Se la zona in cui si trova la distribuzione diventa inattiva, la distribuzione non riuscirà a sopravvivere.

Si consiglia di usare Load Balancer con ridondanza della zona per i carichi di lavoro di produzione.

### <a name="control-vs-data-plane-implications"></a>Controllare le implicazioni del piano dati di Visual Studio

La ridondanza della zona non implica il piano dati o il piano di controllo hitless. I flussi con ridondanza della zona possono usare qualsiasi zona e i flussi useranno tutte le zone integre in un'area. In un errore di zona non sono interessati flussi di traffico che usano zone integre.

I flussi di traffico che usano una zona al momento dell'errore della zona possono essere interessati, ma le applicazioni possono essere ripristinate. Il traffico continua nelle zone integre all'interno dell'area durante la ritrasmissione quando Azure è convergente intorno all'errore della zona.

Esaminare i [modelli di progettazione cloud di Azure](/azure/architecture/patterns/) per migliorare la resilienza dell'applicazione in scenari di errore.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [zone di disponibilità](../availability-zones/az-overview.md)
- Altre informazioni su [Load Balancer standard](./load-balancer-overview.md)
- Informazioni su come [bilanciare il carico delle macchine virtuali all'interno di una zona usando una Load Balancer standard di zona](./quickstart-load-balancer-standard-public-cli.md)
- Informazioni su come [bilanciare il carico delle macchine virtuali tra zone usando un Load Balancer standard con ridondanza della zona](./quickstart-load-balancer-standard-public-cli.md)
- Informazioni sui [modelli di progettazione cloud di Azure](/azure/architecture/patterns/) per migliorare la resilienza dell'applicazione in scenari di errore.
