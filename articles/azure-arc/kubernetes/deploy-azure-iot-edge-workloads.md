---
title: Distribuire i carichi di lavoro di Azure IoT Edge
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Distribuire i carichi di lavoro di Azure IoT Edge
keywords: Kubernetes, Arc, Azure, K8s, contenitori
ms.openlocfilehash: e77446170e5a6adac995394d66640fd183f453b8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121729"
---
# <a name="deploy-azure-iot-edge-workloads"></a>Distribuire i carichi di lavoro di Azure IoT Edge

## <a name="overview"></a>Panoramica

Azure Arc e Azure IoT Edge sono facilmente complementari alle funzionalità. 

Azure Arc fornisce meccanismi per gli operatori di cluster per la configurazione dei componenti di base di un cluster e per l'applicazione e l'applicazione dei criteri del cluster. 

Azure IoT Edge consente agli operatori di applicazioni di distribuire e gestire in modalità remota i carichi di lavoro su larga scala con pratici inserimenti nel cloud e primitive di comunicazione bidirezionale. 

Il diagramma seguente illustra la relazione tra Azure Arc e Azure IoT Edge:

![Configurazione di IoT Arc](./media/edge-arc.png)

## <a name="pre-requisites"></a>Prerequisiti

* [Registrare un dispositivo IoT Edge](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) e [distribuire il modulo del sensore di temperatura simulato](../../iot-edge/quickstart-linux.md#deploy-a-module). Prendere nota della stringa di connessione del dispositivo per i *valori. YAML* indicata di seguito.

* Usare il [supporto di IoT Edge per Kubernetes](https://aka.ms/edgek8sdoc) per distribuirlo tramite l'operatore Flux di Azure Arc.

* Scaricare il file [*values. YAML*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) per IOT Edge grafico Helm e sostituire il `deviceConnectionString` segnaposto alla fine del file con la stringa di connessione annotata in precedenza. Impostare eventuali altre opzioni di installazione del grafico supportate in base alle esigenze. Creare uno spazio dei nomi per il carico di lavoro IoT Edge e generarvi un segreto:

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  È anche possibile configurare in remoto usando l' [esempio di configurazione del cluster](./tutorial-use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Connettere un cluster

Usare l' `az` estensione dell'interfaccia della riga `connectedk8s` di comando di Azure per connettere un cluster Kubernetes ad Azure Arc:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Creare una configurazione per IoT Edge

Il [repository git di esempio](https://github.com/veyalla/edgearc) punta al grafico Helm IOT Edge e fa riferimento al segreto creato nella sezione prerequisiti.

Usare l' `az` estensione dell'interfaccia della riga `k8s-configuration` di comando di Azure per creare una configurazione che colleghi il cluster connesso al repository git:

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

In pochi minuti dovrebbero essere visualizzati i moduli del carico di lavoro IoT Edge distribuiti nello `iotedge` spazio dei nomi del cluster. 

Visualizzare i `SimulatedTemperatureSensor` Log pod nello spazio dei nomi per visualizzare i valori di esempio generati. È anche possibile vedere i messaggi in arrivo all'hub IoT usando l'[estensione Toolkit dell'hub IoT di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Pulizia

Rimuovere la configurazione utilizzando:

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [usare i criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md).
