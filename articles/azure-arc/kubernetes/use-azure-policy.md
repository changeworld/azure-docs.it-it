---
title: Usare Criteri di Azure per applicare le configurazioni del cluster su larga scala
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usare Criteri di Azure per applicare le configurazioni del cluster su larga scala
keywords: Kubernetes, Arc, Azure, K8s, contenitori
ms.openlocfilehash: 7f85050666c383ba49730bd88ce1f26d55607e7a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652148"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>Usare Criteri di Azure per applicare le configurazioni del cluster su larga scala

## <a name="overview"></a>Panoramica

È possibile usare i criteri di Azure per applicare le configurazioni ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` tipo di risorsa) su larga scala nei cluster Kubernetes abilitati per Azure Arc ( `Microsoft.Kubernetes/connectedclusters` ).

Per usare criteri di Azure, selezionare una definizione dei criteri esistente e creare un'assegnazione di criteri. Quando si crea l'assegnazione dei criteri:
1. Impostare l'ambito per l'assegnazione.
    * L'ambito sarà un gruppo di risorse o una sottoscrizione di Azure. 
2. Impostare i parametri per la configurazione che verrà creata. 

Una volta creata l'assegnazione, il motore di criteri di Azure identifica tutti i cluster Kubernetes abilitati per Azure Arc presenti nell'ambito e applica la configurazione a ogni cluster.

È possibile creare più configurazioni, ognuna delle quali punta a un repository git diverso, usando più assegnazioni di criteri. Ad esempio, un repository per l'operatore IT/cluster centrale e altri repository per i team di applicazioni.

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

Dopo aver creato l'assegnazione dei criteri, la configurazione viene applicata ai nuovi cluster Kubernetes abilitati per Azure Arc creati nell'ambito dell'assegnazione dei criteri.

Per i cluster esistenti, è necessario eseguire manualmente un'attività di correzione. Questa attività richiede in genere da 10 a 20 minuti per rendere effettive le assegnazioni di criteri.

## <a name="verify-a-policy-assignment"></a>Verificare un'assegnazione di criteri

1. Nella portale di Azure passare a uno dei cluster Kubernetes abilitati per Azure Arc.
1. Nella sezione **Impostazioni** della barra laterale selezionare **criteri**. 
    * Nell'elenco dei criteri dovrebbe essere visualizzata l'assegnazione di criteri creata in precedenza con lo **stato di conformità** impostato come *conforme*.
1. Nella sezione **Impostazioni** della barra laterale selezionare **Configurazioni**.
    * Nell'elenco delle configurazioni dovrebbe essere visualizzata la configurazione creata dall'assegnazione dei criteri.
1. Usare `kubectl` per interrogare il cluster. 
    * Verranno visualizzati lo spazio dei nomi e gli artefatti creati dalle risorse di configurazione.
    * Entro 5 minuti (presupponendo che il cluster disponga di connettività di rete ad Azure), verranno visualizzati gli oggetti descritti dai manifesti nel repository git, che verranno creati nel cluster.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare Monitoraggio di Azure per i contenitori con cluster Kubernetes abilitati per Arc](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md)
