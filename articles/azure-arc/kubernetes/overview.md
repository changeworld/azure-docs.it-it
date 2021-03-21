---
title: Panoramica di Kubernetes con abilitazione di Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Questo articolo offre una panoramica di Kubernetes con abilitazione di per Azure Arc.
keywords: Kubernetes, Arc, Azure, contenitori
ms.custom: references_regions
ms.openlocfilehash: b3296aa1c1d5d463cc7f4cb932b44ad76632e19e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121593"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Che cos'è Kubernetes con abilitazione di Azure Arc?

Con Azure Arc abilitato Kubernetes, è possibile associare e configurare cluster Kubernetes che si trovano all'interno o all'esterno di Azure. Quando si connette un cluster Kubernetes ad Azure Arc, viene:
* Vengono visualizzati nel portale di Azure con un ID Azure Resource Manager e un'identità gestita. 
* Vengono inseriti in una sottoscrizione e un gruppo di risorse di Azure.
* Ricevere i tag come qualsiasi altra risorsa di Azure. 

Per connettere un cluster Kubernetes ad Azure, l'amministratore del cluster deve distribuire gli agenti. Questi agenti:
* Eseguire nello `azure-arc` spazio dei nomi Kubernetes come distribuzioni Kubernetes standard.
* Gestire la connettività ad Azure.
* Raccolta di metriche e log di Azure Arc.
* Controllare le richieste di configurazione. 

Kubernetes con abilitazione di Azure Arc supporta il protocollo SSL standard di settore per proteggere i dati in transito. Inoltre, i dati vengono archiviati crittografati quando sono inattivi in un database di Azure Cosmos DB per garantirne la riservatezza.

## <a name="supported-kubernetes-distributions"></a>Distribuzioni di Kubernetes supportate

Azure Arc Enabled Kubernetes funziona con tutti i cluster Kubernetes certificati CNCF (cloud native Computing Foundation). Il team di Azure Arc ha collaborato con i [principali partner del settore per convalidare la conformità](./validation-program.md) delle distribuzioni Kubernetes con Azure Arc Enabled Kubernetes.

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
* Stati Uniti centro-occidentali
* Stati Uniti centro-meridionali
* Asia sud-orientale
* Regno Unito meridionale
* Stati Uniti occidentali 2
* Australia orientale
* Stati Uniti orientali 2
* Europa settentrionale

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come connettere un cluster ad Azure Arc.
> [!div class="nextstepaction"]
> [Connettere un cluster ad Azure Arc](./quickstart-connect-cluster.md)
