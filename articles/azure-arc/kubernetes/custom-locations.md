---
title: Percorsi personalizzati in Azure Arc abilitato Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions
description: Usare i percorsi personalizzati per distribuire i servizi PaaS di Azure nei cluster Kubernetes abilitati per Azure Arc
ms.openlocfilehash: ddda6420acd7126cb46b043f5c1bce67758342bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451156"
---
# <a name="custom-locations-on-azure-arc-enabled-kubernetes"></a>Percorsi personalizzati in Azure Arc abilitato Kubernetes

Come estensione della località di Azure, i *percorsi personalizzati* consentono agli amministratori tenant di usare i cluster Kubernetes abilitati per Azure Arc come percorsi di destinazione per la distribuzione di istanze dei servizi di Azure. Gli esempi di risorse di Azure includono Azure Arc abilitato Istanza gestita SQL e la scalabilità di PostgreSQL abilitata per Azure Arc.

Analogamente alle località di Azure, gli utenti finali all'interno del tenant con accesso a posizioni personalizzate possono distribuire le risorse con il calcolo privato della propria azienda.

Una panoramica concettuale di questa funzionalità è disponibile in [percorsi personalizzati-articolo Kubernetes abilitato per Azure Arc](conceptual-custom-locations.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Installare o aggiornare l'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli) della riga di comando di Azure alla versione >= 2.16.0.

- `connectedk8s` (Version >= 1.1.0), `k8s-extension` (version >= 0.2.0) e `customlocation` (Version >= 0.1.0) estensioni dell'interfaccia della riga di comando di Azure. Installare le estensioni dell'interfaccia della riga di comando di Azure eseguendo i comandi seguenti:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Se le `connectedk8s` `k8s-extension` estensioni, ed `customlocation` sono già installate, è possibile aggiornarle alla versione più recente usando il comando seguente:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- La registrazione del provider è stata completata per `Microsoft.ExtendedLocation` .
    1. Immettere i comandi seguenti:
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. Monitorare il processo di registrazione. La registrazione può richiedere fino a 10 minuti.
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

>[!NOTE]
>**Aree supportate per percorsi personalizzati:**
>* Stati Uniti orientali
>* Europa occidentale

## <a name="enable-custom-locations-on-cluster"></a>Abilita percorsi personalizzati nel cluster

Per abilitare questa funzionalità nel cluster, eseguire il comando seguente:

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

> [!NOTE]
> 1. La funzionalità percorsi personalizzati dipende dalla funzionalità di connessione del cluster. È quindi necessario abilitare entrambe le funzionalità per consentire il funzionamento dei percorsi personalizzati.
> 2. `az connectedk8s enable-features` deve essere eseguito in un computer in cui il `kubeconfig` file fa riferimento al cluster in cui devono essere abilitate le funzionalità.

## <a name="create-custom-location"></a>Crea percorso personalizzato

1. Creare un cluster Kubernetes abilitato per Azure Arc.
    - Se non è ancora stato connesso un cluster, usare la [Guida introduttiva](quickstart-connect-cluster.md).
    - [Aggiornare gli agenti](agent-upgrade.md#manually-upgrade-agents) alla versione >= 1.1.0.

1. Distribuire l'estensione del cluster del servizio di Azure di cui si desidera ottenere l'istanza nel percorso personalizzato:

    ```azurecli
    az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
    ```

    > [!NOTE]
    > Il proxy in uscita senza autenticazione e proxy in uscita con l'autenticazione di base è supportato dall'estensione del cluster di servizi dati abilitati per Arc. Il proxy in uscita che prevede certificati attendibili non è al momento supportato.

1. Ottenere l'identificatore Azure Resource Manager del cluster Kubernetes abilitato per Azure Arc, a cui si fa riferimento nei passaggi successivi come `connectedClusterId` :

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Ottenere l'identificatore Azure Resource Manager dell'estensione del cluster distribuita nella parte superiore del cluster Kubernetes abilitato per Azure Arc, a cui si fa riferimento nei passaggi successivi come `extensionId` :

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Creare un percorso personalizzato facendo riferimento al cluster Kubernetes abilitato per Azure Arc e all'estensione:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> Connettersi in modo sicuro al cluster tramite la [connessione cluster](cluster-connect.md)