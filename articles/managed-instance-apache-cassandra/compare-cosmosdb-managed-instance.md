---
title: Differenze tra Istanza gestita di Azure per Apache Cassandra e Azure Cosmos DB API Cassandra
description: Informazioni sulle differenze tra Istanza gestita di Azure per Apache Cassandra e API Cassandra in Azure Cosmos DB. Vengono inoltre illustrati i vantaggi di ognuno di questi servizi e quando sceglierli.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748760"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Differenze tra Istanza gestita di Azure per Apache Cassandra (anteprima) e Azure Cosmos DB API Cassandra 

In questo articolo vengono illustrate le differenze tra Istanza gestita di Azure per Apache Cassandra e i API Cassandra in Azure Cosmos DB. Questo articolo fornisce consigli su come scegliere tra i due servizi o quando ospitare un ambiente Apache Cassandra personalizzato.

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-differences"></a>Differenze principali

Azure Istanza gestita per Apache Cassandra fornisce operazioni automatiche di distribuzione, ridimensionamento e operazioni per gestire l'integrità del nodo per le istanze Open Source di Apache Cassandra in Azure. Offre inoltre la possibilità di scalare in orizzontale la capacità dei cluster Apache Cassandra ospitati in locale o nel cloud. Aumenta la scalabilità orizzontale aggiungendo i Data Center di Cassandra gestiti all'anello del cluster esistente.

Il [API Cassandra](../cosmos-db/cassandra-introduction.md) in Azure Cosmos DB è un livello di compatibilità rispetto al servizio di database nativo del cloud distribuito a livello globale di Microsoft [Azure Cosmos DB](../cosmos-db/index.yml). La combinazione di questi servizi in Azure offre una serie continua di scelte per gli utenti di Apache Cassandra in ambienti cloud ibridi complessi.

## <a name="how-to-choose"></a>Come scegliere?

La tabella seguente illustra gli scenari comuni, i requisiti del carico di lavoro e le aspirazioni in cui ognuno di questi approcci di distribuzione è adatto:

| |Apache Cassandra self-hosted in locale o in Azure | Azure Istanza gestita per Apache Cassandra | API Cassandra di Azure Cosmos DB |
|---------|---------|---------|---------|
|**Tipo di distribuzione**| Si dispone di una distribuzione di Apache Cassandra altamente personalizzata con patch o spie personalizzate. | Si dispone di una distribuzione di Apache Cassandra standard Open Source senza codice personalizzato. | Il contenuto di una piattaforma non è Apache Cassandra sottostante, ma è conforme a tutti i driver client open source a livello di [protocollo wire](../cosmos-db/cassandra-support.md) . |
| **Overhead operativo**| Sono disponibili esperti Cassandra che possono distribuire, configurare e gestire i cluster.  | Si vuole ridurre il sovraccarico operativo per l'integrità del nodo Apache Cassandra, mantenendo comunque il controllo sulle configurazioni a livello di piattaforma, ad esempio la replica e la coerenza. | Si vuole eliminare il sovraccarico operativo usando un database di piattaforma distribuita come servizio completamente gestito nel cloud. |
| **Requisiti del sistema operativo**| Si ha la necessità di mantenere le immagini del sistema operativo della macchina virtuale personalizzata o dorata. | È possibile usare le immagini Vanilla ma si vuole avere il controllo su SKU, memoria, dischi e IOPS. | Si vuole che il provisioning della capacità venga semplificato ed espresso come una singola metrica normalizzata, con una relazione uno-a-uno con la velocità effettiva, ad esempio le [unità richiesta](../cosmos-db/request-units.md) in Azure Cosmos DB. |
| **Modello di determinazione prezzi**| Si desidera utilizzare il software di gestione, ad esempio gli strumenti di Datastax, e sono soddisfatti i costi di licenza. | Si preferisce il prezzo di licenze open source pure e i prezzi basati su istanze di VM. | Si vuole usare i prezzi nativi del cloud, che includono la [scalabilità](../cosmos-db/manage-scale-cassandra.md#use-autoscale) automatica e le offerte senza [Server](../cosmos-db/serverless.md) . |
| **Analisi**| Si vuole avere il controllo completo sul provisioning delle pipeline analitiche indipendentemente dal sovraccarico per crearle e gestirle. | Si desidera utilizzare servizi analitici basati su cloud come Azure Databricks. | Si vuole avere un'analisi transazionale ibrida quasi in tempo reale incorporata nella piattaforma con il [collegamento sinapsi di Azure per Cosmos DB](../cosmos-db/synapse-link.md). |
| **Modello di carico di lavoro**| Il carico di lavoro è piuttosto stabile e non è necessario ridimensionare spesso i nodi nel cluster. | Il carico di lavoro è volatile ed è necessario poter aumentare o ridurre i nodi in un data center o aggiungere o rimuovere facilmente i Data Center. | Il carico di lavoro è spesso volatile ed è necessario essere in grado di aumentare o ridurre rapidamente e in un volume significativo. |
| **Contratti di servizio**| Si è soddisfatti dei processi per gestire i contratti di sistema in coerenza, velocità effettiva, disponibilità e ripristino di emergenza. | Si è soddisfatti dei processi per la gestione dei contratti di sicurezza in coerenza, velocità effettiva e disponibilità, ma è necessario assistenza per i backup. | Si desiderano contratti di prestazioni completi per coerenza, velocità effettiva, disponibilità e ripristino di emergenza. |

## <a name="next-steps"></a>Passaggi successivi

Inizia a usare una delle guide introduttive seguenti:

* [Creare un cluster di istanze gestite dalla portale di Azure](create-cluster-portal.md)