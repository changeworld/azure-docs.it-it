---
title: Cluster gestiti di Service Fabric (anteprima)
description: I cluster gestiti di Service Fabric sono un'evoluzione del modello di risorse cluster di Azure Service Fabric che semplifica la gestione della distribuzione e del cluster.
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 271852214097ee96ba6b10de7a94904981cd8ef8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041230"
---
# <a name="service-fabric-managed-clusters-preview"></a>Cluster gestiti di Service Fabric (anteprima)

I cluster gestiti di Service Fabric sono un'evoluzione del modello di risorse cluster di Azure Service Fabric che semplifica l'esperienza di gestione della distribuzione e dei cluster.

Il modello ARM (Azure Resource Manager) per i cluster di Service Fabric tradizionali richiede la definizione di una risorsa cluster insieme a una serie di risorse di supporto, che devono tutte essere "collegate" correttamente (durante la distribuzione e per tutto il ciclo di vita del cluster) affinché il cluster e i servizi funzionino correttamente. Al contrario, il modello di incapsulamento per i cluster gestiti di Service Fabric è costituito da una singola risorsa *cluster gestito di Service Fabric*. Tutte le risorse sottostanti relative al cluster sono astratte e gestite da Azure per conto dell'utente.

**Modello di cluster tradizionale di Service Fabric**
![Modello di cluster tradizionale di Service Fabric][sf-composition]

**Modello di cluster gestito di Service Fabric**
![Modello cluster incapsulato di Service Fabric][sf-encapsulation]

In termini di dimensioni e complessità, il modello ARM per un cluster gestito di Service Fabric è costituito da circa 100 righe di codice JSON, rispetto al numero approssimativo di 1000 righe che sono necessarie per definire un tipico cluster di Service Fabric:

| Risorse di Service Fabric | Risorse del cluster gestito di Service Fabric |
|----------|-----------|
| Cluster di Service Fabric | Cluster gestito di Service Fabric |
| Set di scalabilità di macchine virtuali | |
| Bilanciamento del carico | |
| Indirizzo IP pubblico | |
| Account di archiviazione | |
| Rete virtuale | |

I cluster gestiti di Service Fabric offrono numerosi vantaggi rispetto ai cluster tradizionali:

**Distribuzione e gestione del cluster semplificate**
- Distribuzione e gestione di una singola risorsa di Azure
- Gestione e rotazione automatica dei certificati
- Operazioni di ridimensionamento semplificate

**Prevenzione dagli errori operativi**
- Prevenzione da errori di corrispondenza della configurazione con le risorse sottostanti
- Blocco delle operazioni non sicure (ad esempio l'eliminazione di un nodo di inizializzazione)

**Procedure consigliate predefinite**
- Impostazioni consigliate per affidabilità e durabilità

Non sono previsti costi aggiuntivi per i cluster gestiti di Service Fabric oltre al costo delle risorse sottostanti necessarie per il cluster.

## <a name="service-fabric-managed-cluster-skus"></a>SKU dei cluster gestiti di Service Fabric

I cluster gestiti di Service Fabric sono disponibili negli SKU Basic e Standard.

| Feature | Basic | Standard |
| ------- | ----- | -------- |
| Risorsa di rete (SKU per [Load Balancer](../load-balancer/skus.md), [IP pubblico](../virtual-network/public-ip-addresses.md)) | Basic | Standard |
| Numero minimo di nodi (istanza VM) | 3 | 5 |
| Numero massimo di nodi per tipo | 100 | 100 |
| Numero massimo di tipi di nodo | 1 | 20 |
| Aggiunta/Rimozione di tipi di nodo | No | Sì |
| Ridondanza della zona | No | Sì |

## <a name="whats-new-for-service-fabric-managed-clusters"></a>Novità per i cluster gestiti Service Fabric

Le funzionalità più recenti per l'anteprima dei cluster gestiti Service Fabric includono il supporto per:

* [Distribuzione di applicazioni con modelli ARM](how-to-managed-cluster-app-deployment-template.md)
* [Aggiornamenti automatici del sistema operativo](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* [Crittografia del disco](how-to-enable-managed-cluster-disk-encryption.md)
* [Applicazione delle regole di NSG](how-to-managed-cluster-networking.md)

Le funzionalità da aggiungere nelle prossime versioni includono:

* Distribuzione di applicazioni con Visual Studio
* Supporto delle identità gestite
* Zone di disponibilità
* Proxy inverso
* Scalabilità automatica

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i cluster Service Fabric gestiti, provare la Guida introduttiva:

> [!div class="nextstepaction"]
> [Creare un cluster gestito di Service Fabric (anteprima)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png