---
title: Prerequisiti | Modalità di connessione diretta
description: Prerequisiti per distribuire il controller dati in modalità di connessione diretta.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716313"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>Distribuire il controller dati - modalità di connessione diretta (prerequisiti)

Questo articolo descrive come preparare la distribuzione di un controller di dati per servizi dati abilitati per Azure Arc in modalità di connessione diretta.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

In un riepilogo di alto livello, i prerequisiti includono:

1. Installare gli strumenti
1. Aggiungere estensioni
1. Creare l'entità servizio e configurare i ruoli per le metriche
1. Connettere un cluster Kubernetes ad Azure usando Azure Arc Kubernetes abilitato

Dopo aver completato questi prerequisiti, è possibile distribuire Azure Arc [controller dati | Modalità di connessione diretta](deploy-data-controller-direct-mode.md).

Le sezioni rimanenti di questo articolo identificano i prerequisiti.

## <a name="install-tools"></a>Installare gli strumenti

- Helm versione 3.3+[(installazione](https://helm.sh/docs/intro/install/))
- Interfaccia della riga di comando di Azure[(installare](/sql/azdata/install/deploy-install-azdata))

## <a name="add-extensions-for-azure-cli"></a>Aggiungere estensioni per l'interfaccia della riga di comando di Azure

Sono inoltre necessarie le estensioni az seguenti:
- Estensione dell'interfaccia della riga di comando di Azure `k8s-extension` (0.2.0)
- Interfaccia della riga di comando di Azure `customlocation` (0.1.0)

`az`L'esempio e le relative estensioni dell'interfaccia della riga di comando sono:

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>Creare un'entità servizio e configurare i ruoli per le metriche

Seguire i passaggi descritti nell'articolo [Caricare](upload-metrics-and-logs-to-azure-monitor.md) le metriche e creare un'entità servizio e concedere i ruoli come descritto nell'articolo. 

Le informazioni SPN ClientID, TenantID e Client Secret saranno necessarie quando si [distribuisce](deploy-data-controller-direct-mode.md)Azure Arc controller dati . 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Connettere un cluster Kubernetes ad Azure usando Azure Arc Kubernetes abilitato

Per completare questa attività, seguire la procedura descritta in [Connettere un cluster Kubernetes esistente ad Azure Arc.](../kubernetes/quickstart-connect-cluster.md)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato questi prerequisiti, è possibile distribuire Azure Arc [controller dati | Modalità di connessione diretta](deploy-data-controller-direct-mode.md).
