---
title: Dimensioni iniziali del gruppo di server-iperscala (CITUS)-database di Azure per PostgreSQL
description: Selezionare le dimensioni iniziali corrette per il caso d'uso
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8b87cfc8276d13ccc7e12a4901489ea0b1e770a5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012507"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Selezionare le dimensioni iniziali per il gruppo di server CITUS (iperscale)

Le dimensioni di un gruppo di server, sia il numero di nodi che la capacità hardware, sono [facili da modificare](howto-hyperscale-scale-grow.md). Tuttavia, è comunque necessario scegliere una dimensione iniziale per un nuovo gruppo di server. Ecco alcuni suggerimenti per una scelta ragionevole.

## <a name="use-cases"></a>Casi d'uso

Iperscale (CITUS) viene usato di frequente nei modi seguenti.

### <a name="multi-tenant-saas"></a>SaaS multi-tenant

Quando si esegue la migrazione a iperscale (CITUS) da un'istanza di database PostgreSQL a nodo singolo esistente, scegliere un cluster in cui il numero di Vcore e RAM del ruolo di lavoro in totale sia uguale a quello dell'istanza originale. In questi scenari sono stati rilevati miglioramenti delle prestazioni da 2 a 3 volte perché il partizionamento orizzontale migliora l'utilizzo delle risorse, consentendo indici più piccoli e così via.

Il numero di vCore è in realtà l'unica decisione. L'allocazione di RAM è attualmente determinata in base al numero di vCore, come descritto nella pagina [Opzioni di configurazione di iperscala (CITUS)](concepts-hyperscale-configuration-options.md) .
Il nodo coordinatore non richiede la stessa quantità di RAM dei thread di lavoro, ma non è possibile scegliere RAM e Vcore in modo indipendente.

### <a name="real-time-analytics"></a>Analisi in tempo reale

Totale VCore: quando i dati di lavoro si adattano alla RAM, è possibile prevedere un miglioramento delle prestazioni lineare su iperscala (CITUS) proporzionale al numero di core del ruolo di lavoro. Per determinare il numero corretto di Vcore per le proprie esigenze, prendere in considerazione la latenza corrente per le query nel database a nodo singolo e la latenza necessaria in iperscala (CITUS). Dividere la latenza corrente per la latenza desiderata e arrotondare il risultato.

RAM del ruolo di lavoro: il caso migliore è fornire memoria sufficiente per la maggior parte delle working set si adatta alla memoria. Il tipo di query usate dall'applicazione influisce sui requisiti di memoria. È possibile eseguire EXPLAIN ANALYZe su una query per determinare la quantità di memoria richiesta. Tenere presente che Vcore e RAM vengono ridimensionati insieme come descritto nell'articolo [Opzioni di configurazione dell'iperscalabilità (CITUS)](concepts-hyperscale-configuration-options.md) .

## <a name="choosing-a-hyperscale-citus-tier"></a>Scelta di un livello iperscalabile (CITUS)

> [!IMPORTANT]
> Il livello Basic con iperscalabilità (CITUS) è attualmente in anteprima.  Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
>
> È possibile visualizzare un elenco completo di altre nuove funzionalità in [Anteprima funzionalità per l'iperscalabilità (CITUS)](hyperscale-preview-features.md).

Le sezioni precedenti forniscono un'idea del numero di Vcore e della quantità di RAM necessaria per ogni caso di utilizzo. Queste richieste possono essere soddisfatte tramite una scelta tra due livelli di iperscalabilità (CITUS), ovvero il livello Basic e il livello standard.

Il livello Basic utilizza un solo nodo di database per eseguire l'elaborazione, mentre il livello standard consente un numero maggiore di nodi. I livelli sono altrimenti identici e offrono le stesse funzionalità. In alcuni casi, il vcore e lo spazio su disco di un singolo nodo possono essere ridimensionati in modo da essere sufficienti e, in altri casi, richiede la collaborazione di più nodi.

Per un confronto tra i livelli, vedere la pagina concetti di [base del livello](concepts-hyperscale-tiers.md) .

## <a name="next-steps"></a>Passaggi successivi

- [Dimensionare un gruppo di server](howto-hyperscale-scale-grow.md)
- Ulteriori informazioni sulle [Opzioni relative alle prestazioni](concepts-hyperscale-configuration-options.md)del gruppo di server.
