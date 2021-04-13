---
title: Ribilanciare le partizioni-iperscala (CITUS)-database di Azure per PostgreSQL
description: Distribuisci uniformemente le partizioni tra i server per ottenere prestazioni migliori
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 63322fac4c6ad5b705deedcd8a80466ddd803814
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305702"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Ribilanciare le partizioni nel gruppo di server CITUS (iperscale)

Per sfruttare i vantaggi dei nuovi nodi aggiunti è necessario ribilanciare le partizioni [delle tabelle](concepts-hyperscale-distributed-data.md#shards)distribuite, ovvero trasferire alcune partizioni dai nodi esistenti a quelle nuove.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>Determinare se il gruppo di server deve essere ribilanciato

Il portale di Azure può indicare se i dati vengono distribuiti equamente tra i nodi del ruolo di lavoro in un gruppo di server. Per visualizzarlo, passare alla pagina di **ribilanciamento** delle partizioni nel menu **Gestione gruppi di server** . Se i dati sono sbilanciati tra i thread di lavoro, si noterà che il **ribilanciamento dei messaggi è consigliato**, insieme a un elenco delle dimensioni di ciascun nodo.

Se i dati sono già bilanciati, si noterà che il **ribilanciamento dei messaggi in questo momento non è consigliato**.

## <a name="run-the-shard-rebalancer"></a>Eseguire il ribilanciamento della partizione

Per avviare il ribilanciamento delle partizioni, è necessario connettersi al nodo coordinatore del gruppo di server ed eseguire la [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) funzione SQL sulle tabelle distribuite. La funzione ribilancia [tutte le tabelle nel gruppo di](concepts-hyperscale-colocation.md) condivisione della tabella denominato nell'argomento. Pertanto non è necessario chiamare la funzione per ogni tabella distribuita, ma è sufficiente chiamarla in una tabella rappresentativa di ogni gruppo di condivisione percorso.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>Monitorare lo stato di ribilanciamento

Per controllare il ribilanciamento dopo l'avvio, tornare alla portale di Azure. Aprire la pagina del **ribilanciamento** delle partizioni in **Gestione gruppi di server**. Verrà visualizzato il **ribilanciamento** dei messaggi in corso insieme a due tabelle.

La prima tabella mostra il numero di partizioni che si spostano all'interno o all'esterno di un nodo, ad esempio, "6 di 24 spostate in". La seconda tabella mostra lo stato di avanzamento per ogni tabella di database: nome, numero di partizioni interessato, dimensioni dei dati interessate e stato di ribilanciamento.

Selezionare il pulsante **Aggiorna** per aggiornare la pagina. Al termine del ribilanciamento, **il ribilanciamento non è consigliato in questo momento**.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulle [Opzioni relative alle prestazioni](concepts-hyperscale-configuration-options.md)del gruppo di server.
- [Ridimensionare un gruppo di server](howto-hyperscale-scale-grow.md)
- Vedere il materiale di riferimento [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)
