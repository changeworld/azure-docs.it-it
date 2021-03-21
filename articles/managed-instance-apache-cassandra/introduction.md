---
title: Introduzione ad Azure Istanza gestita per Apache Cassandra
description: Informazioni su Azure Istanza gestita per Apache Cassandra. Questo servizio gestisce la distribuzione e il ridimensionamento di istanze Open source native di Apache Cassandra in Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748676"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>Che cos'è Azure Istanza gestita per Apache Cassandra? (Anteprima)

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il servizio Istanza gestita di Azure per Apache Cassandra fornisce operazioni automatiche di distribuzione e scalabilità per i Data Center di Apache Cassandra gestiti e open source. Questo servizio ti aiuta ad accelerare gli scenari ibridi e a ridurre la manutenzione continuativa. Con [Azure Cosmos DB API Cassandra](../cosmos-db/cassandra-introduction.md) alla sua versione generale, avrà funzionalità complete di integrazione e spostamento dei dati.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Azure Istanza gestita per Apache Cassandra è un servizio gestito per Apache Cassandra." border="false":::

## <a name="key-benefits"></a>Vantaggi principali

### <a name="hybrid-deployments"></a>Distribuzioni ibride

È possibile usare questo servizio per inserire facilmente le istanze gestite dei data center Apache Cassandra, che vengono distribuite automaticamente come set di scalabilità di macchine virtuali in una rete virtuale di Azure nuova o esistente. Questi Data Center possono essere aggiunti all'anello Apache Cassandra esistente in esecuzione in locale tramite [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) in Azure o in un altro ambiente cloud.

- **Distribuzione semplificata:** Una volta stabilita la distribuzione della connettività ibrida, è facile tramite il protocollo gossip.
- **Metriche ospitate:** Le metriche sono ospitate in [Prometheus](https://prometheus.io/docs/introduction/overview/) per monitorare l'attività nel cluster.

### <a name="simplified-scaling"></a>Ridimensionamento semplificato

Nell'istanza gestita, la scalabilità verticale e verticale dei nodi in un Data Center è completamente gestita. Si immette il numero di nodi necessari e l'agente di orchestrazione del ridimensionamento si occupa di stabilire la propria operazione nell'anello Cassandra.

### <a name="managed-and-cost-effective"></a>Gestione ed economicamente convenienti

Il servizio fornisce operazioni di gestione per le seguenti attività di Apache Cassandra comuni:

- Effettuare il provisioning di un cluster
- Effettuare il provisioning di un Data Center
- Ridimensionare un Data Center
- Eliminare un Data Center
- Avvia un'azione di ripristino su uno spazio.
- Modificare la configurazione di un Data Center
- Backup di installazione

Il modello di determinazione dei prezzi è flessibile, su richiesta e basato su istanze e non prevede costi di licenza. Questo modello di determinazione dei prezzi consente di adattarsi alle esigenze specifiche del carico di lavoro. È possibile scegliere il numero di core, lo SKU della macchina virtuale, le dimensioni della memoria e le dimensioni dello spazio su disco necessarie.

## <a name="next-steps"></a>Passaggi successivi

Inizia a usare una delle guide introduttive seguenti:

* [Creare un cluster di istanze gestite dalla portale di Azure](create-cluster-portal.md)
* [Distribuire un cluster Apache Spark gestito con Azure Databricks](deploy-cluster-databricks.md)
* [Gestire Azure Istanza gestita per le risorse Apache Cassandra usando l'interfaccia della riga di comando di Azure](manage-resources-cli.md)
