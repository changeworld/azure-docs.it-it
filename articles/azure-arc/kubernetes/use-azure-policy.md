---
title: Usare Criteri di Azure per applicare le configurazioni del cluster su larga scala (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usare Criteri di Azure per applicare le configurazioni del cluster su larga scala
keywords: Kubernetes, Arc, Azure, K8s, contenitori
ms.openlocfilehash: 23cd42458c396afd31741c648d713934250a4112
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587801"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Usare Criteri di Azure per applicare le configurazioni del cluster su larga scala (anteprima)

## <a name="overview"></a>Panoramica

È possibile usare i criteri di Azure per applicare una delle risorse seguenti per `Microsoft.KubernetesConfiguration/sourceControlConfigurations` applicare specifici:
*  `Microsoft.Kubernetes/connectedclusters` risorse.
* Risorsa abilitata per GitOps `Microsoft.ContainerService/managedClusters` . 

Per usare criteri di Azure, selezionare una definizione dei criteri esistente e creare un'assegnazione di criteri. Quando si crea l'assegnazione dei criteri:
1. Impostare l'ambito per l'assegnazione.
    * L'ambito sarà un gruppo di risorse o una sottoscrizione di Azure. 
2. Impostare i parametri per l'oggetto `sourceControlConfiguration` che verrà creato. 

Una volta creata l'assegnazione, il motore di criteri di Azure identifica tutte le `connectedCluster` risorse o che `managedCluster` si trovano all'interno dell'ambito e applica `sourceControlConfiguration` a ciascuna di esse.

È possibile abilitare più repository Git come origini di verità per ogni cluster usando più assegnazioni di criteri. Ogni assegnazione di criteri viene configurata per l'uso di un repository git diverso; ad esempio, un repository per l'operatore IT/cluster centrale e altri repository per i team di applicazioni.

## <a name="prerequisite"></a>Prerequisito

Verificare di disporre `Microsoft.Authorization/policyAssignments/write` delle autorizzazioni per l'ambito (sottoscrizione o gruppo di risorse) in cui verrà creata l'assegnazione dei criteri.

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

1. Nella portale di Azure passare a **criterio**.
1. Nella sezione **creazione e modifica** della barra laterale selezionare **definizioni**.
1. Nella categoria "Kubernetes" scegliere il criterio predefinito "Distribuisci GitOps nel cluster Kubernetes". 
1. Fare clic su **assegna**.
1. Impostare l' **ambito** sul gruppo di gestione, sulla sottoscrizione o sul gruppo di risorse a cui si applica l'assegnazione dei criteri.
    * Se si desidera escludere risorse dall'ambito dei criteri, impostare **esclusioni**.
1. Assegnare all'assegnazione dei criteri un **nome** e una **Descrizione** facilmente identificabili.
1. Verificare che l' **applicazione dei criteri** sia impostata su **abilitato**.
1. Selezionare **Avanti**.
1. Impostare i valori dei parametri da utilizzare durante la creazione di `sourceControlConfiguration` .
1. Selezionare **Avanti**.
1. Abilitare **Crea un'attività di correzione**.
1. Verificare che l'opzione **Crea un'identità gestita** sia selezionata e che l'identità disponga delle autorizzazioni di **collaboratore** . 
    * Per altre informazioni, vedere l'articolo [creare una guida introduttiva](../../governance/policy/assign-policy-portal.md) per l'assegnazione di criteri e la [correzione di risorse non conformi con Azure Policy](../../governance/policy/how-to/remediate-resources.md).
1. Selezionare **Rivedi e crea**.

Dopo aver creato l'assegnazione dei criteri, il `sourceControlConfiguration` verrà applicato per le risorse seguenti situate all'interno dell'ambito dell'assegnazione:
* Nuove `connectedCluster` risorse.
* Nuove `managedCluster` risorse con gli agenti GitOps installati. 

Per i cluster esistenti, sarà necessario eseguire manualmente un'attività di correzione. Questa attività richiede in genere da 10 a 20 minuti per rendere effettive le assegnazioni di criteri.

## <a name="verify-a-policy-assignment"></a>Verificare un'assegnazione di criteri

1. Nella portale di Azure passare a una delle `connectedCluster` risorse.
1. Nella sezione **Impostazioni** della barra laterale selezionare **criteri**. 
    * L'UX del cluster AKS non è ancora implementato.
    * Nell'elenco dei criteri dovrebbe essere visualizzata l'assegnazione di criteri creata in precedenza con lo **stato di conformità** impostato come *conforme*.
1. Nella sezione **Impostazioni** della barra laterale selezionare **Configurazioni**.
    * Nell'elenco delle configurazioni dovrebbe essere visualizzato l'oggetto `sourceControlConfiguration` creato dall'assegnazione dei criteri.
1. Usare `kubectl` per interrogare il cluster. 
    * Verranno visualizzati lo spazio dei nomi e gli artefatti creati da `sourceControlConfiguration` .
    * Entro 5 minuti dovrebbero essere visibile nel cluster gli artefatti descritti nei manifesti nel repository Git configurato.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare Monitoraggio di Azure per i contenitori con cluster Kubernetes abilitati per Arc](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md)
