---
title: Domande frequenti su Azure Istanza gestita per Apache Cassandra dalla portale di Azure
description: Domande frequenti su Azure Istanza gestita per Apache Cassandra. Questo articolo descrive le domande su quando usare istanze gestite, vantaggi, limiti di velocità effettiva, aree supportate e altri dettagli di configurazione.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748697"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Domande frequenti su Azure Istanza gestita per Apache Cassandra (anteprima)

Questo articolo risponde alle domande frequenti su Azure Istanza gestita per Apache Cassandra. Si apprenderà quando usare le istanze gestite, i relativi vantaggi, i limiti di velocità effettiva, le aree supportate e i dettagli di configurazione.

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="general-faq"></a>Domande frequenti di carattere generale

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Quali sono i vantaggi di Azure Istanza gestita per Apache Cassandra?

Il database Apache Cassandra è la scelta ideale quando è necessaria la scalabilità e la disponibilità elevata senza compromettere le prestazioni. Si tratta di un'ottima piattaforma per i dati cruciali a causa della scalabilità lineare e della tolleranza di errore comprovata per l'hardware o l'infrastruttura cloud. Azure Istanza gestita per Apache Cassandra è un servizio per la gestione di istanze di Data Center Apache Cassandra Open Source distribuiti in Azure.

Può essere usato interamente nel cloud o come parte di un cloud ibrido e di un cluster locale. Questo servizio è un'ottima scelta quando si desiderano la configurazione e il controllo con granularità fine in Apache Cassandra open source, senza alcun sovraccarico di manutenzione.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Perché usare questo servizio anziché Azure Cosmos DB API Cassandra?

Azure Istanza gestita per Apache Cassandra viene fornito dal team di Azure Cosmos DB. Si tratta di un servizio gestito autonomo per la distribuzione, la gestione e il ridimensionamento di cluster e Data Center Apache Cassandra open source. [Azure Cosmos DB API Cassandra](../cosmos-db/cassandra-introduction.md) d'altra parte è una piattaforma distribuita come servizio che fornisce un livello di interoperabilità per il protocollo wire di Apache Cassandra. Se si prevede che la piattaforma si comporti esattamente allo stesso modo di qualsiasi cluster Apache Cassandra, è consigliabile scegliere il servizio istanza gestita. Per altre informazioni, vedere l'articolo [istanza gestita di Azure per Apache Cassandra rispetto Azure Cosmos DB API Cassandra](compare-cosmosdb-managed-instance.md) .

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Azure Istanza gestita per Apache Cassandra dipende da Azure Cosmos DB?

No, non esiste alcuna dipendenza architettonica tra Istanza gestita di Azure per Apache Cassandra e il back-end Azure Cosmos DB. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>È possibile distribuire Istanza gestita di Azure per Apache Cassandra in qualsiasi area?

L'istanza gestita sarà disponibile in un numero limitato di aree durante l'anteprima.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Quali sono i limiti di archiviazione e velocità effettiva di Azure Istanza gestita per Apache Cassandra?

Questi limiti dipendono dagli SKU scelti per le macchine virtuali.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Qual è il costo di Azure Istanza gestita per Apache Cassandra?

Gli addebiti per le istanze gestite sono basati sul costo della VM sottostante, con un markup ridotto. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/).

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>È possibile usare le impostazioni dei file YAML per configurare il comportamento?

Sì, è possibile incorporare le configurazioni dei file YAML come parte di una distribuzione del modello di Azure Resource Manager.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Come è possibile monitorare l'infrastruttura insieme alla velocità effettiva?

Il server [Prometeo](https://prometheus.io/docs/introduction/overview/) è ospitato per monitorare l'attività nel cluster ed espone un endpoint. Questo mantiene 10 minuti o 10 GB di dati (a seconda della soglia raggiunta per primo). Per usare questo monitoraggio, è necessario configurare una [Federazione](https://prometheus.io/docs/prometheus/latest/federation/) e uno strumento dashboard appropriato, ad esempio Grafana.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Azure Istanza gestita per Apache Cassandra fornisce backup completi?

Sì, fornisce backup completi in archiviazione di Azure e viene ripristinato in un nuovo cluster

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Come è possibile eseguire la migrazione dei dati dal cluster Apache Cassandra esistente ad Azure Istanza gestita per Apache Cassandra?

Azure Istanza gestita per Apache Cassandra supporta tutte le funzionalità di Apache Cassandra per la replica e lo streaming dei dati tra data center.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>È possibile associare un cluster Apache Cassandra locale con la Istanza gestita di Azure per Apache Cassandra?

Sì, è possibile configurare un cluster ibrido con rete virtuale di Azure inserito data center distribuiti dal servizio. Istanza gestita Data Center è in grado di comunicare con i data center locali nello stesso anello del cluster.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Dove è possibile inviare commenti e suggerimenti su Azure Istanza gestita per le funzionalità di Apache Cassandra?

Inviare commenti e suggerimenti tramite il [feedback vocale dell'utente](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548) usando la categoria "Apache Cassandra gestito".

Per risolvere un problema relativo all'account, inviare una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.

## <a name="deployment-specific-faq"></a>Domande frequenti specifiche sulla distribuzione

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>L'istanza gestita supporterà i comandi di aggiunta del nodo, stato del cluster e stato del nodo?

Tutti i comandi nodetool di sola *lettura* , ad esempio, sono disponibili tramite l'interfaccia della riga di comando di `status` Azure. Tuttavia, le operazioni, ad esempio l' *aggiunta del nodo* , non sono disponibili perché si gestisce l'integrità dei nodi nell'istanza gestita. In modalità ibrida è possibile connettersi al cluster con *nodetool*. Tuttavia, l'uso di nodetool non è consigliato, in quanto potrebbe destabilizzare il cluster. Potrebbe inoltre invalidare qualsiasi contratto di servizio per il supporto di produzione relativo all'integrità dei data center dell'istanza gestita nel cluster.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Cosa accade con diverse impostazioni per i metadati della tabella?

Le impostazioni per i metadati della tabella, ad esempio Bloom Filter, Caching, Read Repair Chance, gc_grace e Compression memtable_flush_period sono completamente supportate come con qualsiasi ambiente Apache Cassandra self-hosted.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle domande frequenti in altre API, vedere:

* [Creare un cluster di istanze gestite dalla portale di Azure](create-cluster-portal.md)
* [Distribuire un cluster Apache Spark gestito con Azure Databricks](deploy-cluster-databricks.md)
* [Gestire Azure Istanza gestita per le risorse Apache Cassandra usando l'interfaccia della riga di comando di Azure](manage-resources-cli.md)