---
title: Panoramica di Kubernetes con abilitazione di Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Questo articolo offre una panoramica di Kubernetes con abilitazione di per Azure Arc.
keywords: Kubernetes, Arc, Azure, contenitori
ms.custom: references_regions
ms.openlocfilehash: 3d96c8c8764db89501da6fb9c498f0a3d20461af
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652531"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Che cos'è Kubernetes con abilitazione di Azure Arc?

Con Azure Arc abilitato Kubernetes, è possibile associare e configurare cluster Kubernetes che si trovano all'interno o all'esterno di Azure. Quando si connette un cluster Kubernetes ad Azure Arc, viene:
* Vengono visualizzati nel portale di Azure con un ID Azure Resource Manager e un'identità gestita. 
* Essere collegato alle sottoscrizioni standard di Azure.
* Essere posizionati in un gruppo di risorse.
* Ricevere i tag come qualsiasi altra risorsa di Azure. 

Per connettere un cluster Kubernetes ad Azure, l'amministratore del cluster deve distribuire gli agenti. Questi agenti:
* Eseguire nello `azure-arc` spazio dei nomi Kubernetes come distribuzioni Kubernetes standard.
* Gestire la connettività ad Azure.
* Raccolta di metriche e log di Azure Arc.
* Controllare le richieste di configurazione. 

Kubernetes con abilitazione di Azure Arc supporta il protocollo SSL standard di settore per proteggere i dati in transito. Questi dati vengono archiviati crittografati e inattivi in un database di Azure Cosmos DB per garantire la riservatezza dei dati.
 
## <a name="supported-kubernetes-distributions"></a>Distribuzioni di Kubernetes supportate

Azure Arc Enabled Kubernetes funziona con qualsiasi cluster Kubernetes certificato CNCF (cloud native Computing Foundation), ad esempio:
* Motore AKS in Azure
* AKS-motore nell'hub Azure Stack
* GKE
* EKS
* VMware vSphere

Le funzionalità Kubernetes di Azure Arc abilitate sono state testate dal team Arc sulle distribuzioni seguenti:
* RedHat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* Azure Kubernetes Engine
* Motore del servizio Azure Kubernetes nell'hub di Azure Stack
* Servizio Azure Kubernetes in Azure Stack HCI
* Azure del provider API Cluster

## <a name="supported-scenarios"></a>Scenari supportati 

Kubernetes con abilitazione di Azure Arc supporta gli scenari seguenti: 

* Connessione di Kubernetes in esecuzione all'esterno di Azure per l'inventario, il raggruppamento e l'assegnazione di tag.

* Distribuire le applicazioni e applicare la configurazione tramite la gestione della configurazione basata su GitOps. 

* Visualizza e monitora i cluster con monitoraggio di Azure per i contenitori. 

* Applicare criteri usando criteri di Azure per Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Aree supportate 

Kubernetes con abilitazione di Azure Arc è attualmente supportato in queste aree: 

* Stati Uniti orientali 
* Europa occidentale

## <a name="next-steps"></a>Passaggi successivi

* [Connettere un cluster](./connect-cluster.md)
