---
title: Programma di convalida Kubernetes abilitato per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Descrive il programma di convalida dell'arco per le distribuzioni Kubernetes
keywords: Kubernetes, Arc, Azure, K8s, convalida
ms.openlocfilehash: 819df906add6275997e01fab310fe8dd57a87b51
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121372"
---
# <a name="azure-arc-validation-program"></a>Programma di convalida di Azure Arc

Azure Arc Enabled Kubernetes funziona con tutti i cluster Kubernetes certificati CNCF (cloud native Computing Foundation). Il team di Azure Arc ha anche lavorato con i principali provider di servizi Kubernetes di settore per convalidare Azure Arc abilitato Kubernetes con le relative distribuzioni Kubernetes. Verranno convalidate le versioni principali e secondarie future delle distribuzioni Kubernetes rilasciate da questi provider per la compatibilità con Azure Arc abilitato Kubernetes.

## <a name="validated-distributions"></a>Distribuzioni convalidate

Le distribuzioni Kubernetes e i provider di infrastruttura forniti da Microsoft seguenti hanno superato i test di conformità per Azure Arc Enabled Kubernetes:

| Provider di distribuzione e infrastruttura | Versione |
| ---------------------------------------- | ------- |
| Provider API cluster in Azure            | Versione di rilascio: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12); Versione di Kubernetes: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| Servizio Azure Kubernetes in Azure Stack HCI                   | Versione di rilascio: [aggiornamento 2020 dicembre](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012); Versione di Kubernetes: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

I provider seguenti e le distribuzioni Kubernetes corrispondenti hanno superato i test di conformità per Azure Arc Enabled Kubernetes:

| Nome provider | Nome distribuzione | Versione |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4,5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4,6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4,7](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Griglia Kubernetes Tanz](https://tanzu.vmware.com/kubernetes-grid) | Versione di Kubernetes: v 1.17.5 |
| Canonical    | [Kubernetes con charme](https://ubuntu.com/kubernetes) | [1,19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| Ranch di SUSE      | [Motore Kubernetes Rancher](https://rancher.com/products/rke/) | Versione dell'interfaccia della riga di comando RKE: [v 1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4); Versioni di Kubernetes: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | Versione 2.2.1 |

Il team di Azure Arc ha inoltre eseguito i test di conformità e gli scenari Kubernetes abilitati per Azure Arc convalidati sui seguenti provider di cloud pubblici:

| Nome del provider di cloud pubblico | Nome distribuzione | Versione |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | Servizio Kubernetes elastico (EKS) | v 1.18.9  |
| Google Cloud Platform      | Google Kubernetes Engine (GKE) | v 1.17.15 |

## <a name="scenarios-validated"></a>Scenari convalidati

I test di conformità eseguiti come parte della convalida Kubernetes di Azure Arc abilitata coprono gli scenari seguenti:

1. Connettere i cluster Kubernetes ad Azure Arc: 
    * Distribuire il grafico Helm dell'agente Kubernetes abilitato per Azure Arc nel cluster.
    * Configurare il certificato identità del sistema gestito (MSI) nel cluster.
    * Gli agenti inviano metadati del cluster ad Azure.

2. Configurazione: 
    * Crea la configurazione nella parte superiore della risorsa Kubernetes abilitata per Azure Arc.
    * [Flux](https://docs.fluxcd.io/), necessario per la configurazione del flusso di lavoro GitOps, viene distribuito nel cluster.
    * Flux estrae i manifesti e i grafici Helm dal repository git demo e li distribuisce nel cluster.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come connettere un cluster ad Azure Arc.
> [!div class="nextstepaction"]
> [Connettere un cluster ad Azure Arc](./quickstart-connect-cluster.md)
