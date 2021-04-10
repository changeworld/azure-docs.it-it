---
title: Domande frequenti su Kubernetes di Azure Arc abilitate
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Questo articolo contiene un elenco di domande frequenti relative ad Azure Arc Enabled Kubernetes
keywords: Kubernetes, Arc, Azure, contenitori, configurazione, GitOps, domande frequenti
ms.openlocfilehash: 84368cc63bd9aaf1df4fb281395b47a6e886cb7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025850"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Domande frequenti-Azure Arc abilitato Kubernetes

Questo articolo descrive le domande frequenti su Azure Arc abilitato Kubernetes.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Qual è la differenza tra Kubernetes con abilitazione di Azure Arc e il servizio Azure Kubernetes?

AKS è l'offerta Kubernetes gestita da Azure. AKS semplifica la distribuzione di un cluster Kubernetes gestito in Azure grazie all'offload di gran parte della complessità e del sovraccarico operativo in Azure. Poiché i master Kubernetes sono gestiti da Azure, è possibile gestire e gestire solo i nodi dell'agente.

Azure Arc Enabled Kubernetes consente di estendere le funzionalità di gestione di Azure, ad esempio monitoraggio di Azure e criteri di Azure, connettendo i cluster Kubernetes ad Azure. Si gestisce il cluster Kubernetes sottostante.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>È necessario connettere i cluster AKS in esecuzione in Azure ad Azure Arc?

No. Tutte le funzionalità Kubernetes di Azure Arc abilitate, tra cui monitoraggio di Azure e criteri di Azure (Gatekeeper), sono disponibili in AKS (una risorsa nativa in Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>È necessario connettere il cluster AKS-HCI e I cluster Kubernetes nell'hub Azure Stack e Azure Stack Edge ad Azure Arc?

Sì, la connessione del cluster AKS-HCI o dei cluster Kubernetes in Azure Stack Edge o nell'hub Azure Stack ad Azure Arc fornisce cluster con rappresentazione di risorse in Azure Resource Manager. Questa rappresentazione della risorsa estende funzionalità come la configurazione del cluster, monitoraggio di Azure e criteri di Azure (Gatekeeper) per i cluster Kubernetes connessi.

Se il cluster Kubernetes abilitato per Azure Arc si trova su Azure Stack Edge, AKS in Azure Stack HCI (>= aggiornamento di aprile 2021) o AKS in Windows Server 2019 datacenter (>= aggiornamento di aprile 2021), la configurazione di Kubernetes è inclusa gratuitamente.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Come risolvere le risorse Kubernetes abilitate per Azure Arc scadute?

Il certificato di identità del servizio gestita (MSI) associato al Kubernetes abilitato per Azure Arc ha una finestra di scadenza di 90 giorni. Una volta scaduto il certificato, la risorsa viene considerata `Expired` e tutte le funzionalità, ad esempio la configurazione, il monitoraggio e i criteri, smettono di funzionare in questo cluster. Per far funzionare di nuovo il cluster Kubernetes con Azure Arc:

1. Elimina la risorsa e gli agenti Kubernetes abilitati per Azure Arc nel cluster. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Ricreare la risorsa Kubernetes abilitata per Azure Arc distribuendo gli agenti nel cluster.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` eliminerà anche le configurazioni all'inizio del cluster. Dopo `az connectedk8s connect` l'esecuzione, ricreare le configurazioni nel cluster, manualmente o usando criteri di Azure.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Se si usano già pipeline di integrazione continua/recapito continuo, è possibile continuare a usare le configurazioni e le Kubernetes abilitate per Azure Arc?

Sì, è comunque possibile usare le configurazioni in un cluster che riceve le distribuzioni tramite una pipeline CI/CD. Rispetto alle pipeline CI/CD tradizionali, le configurazioni presentano due vantaggi aggiuntivi:

**Riconciliazione della deviazione**

La pipeline CI/CD applica le modifiche solo una volta durante l'esecuzione della pipeline. Tuttavia, l'operatore GitOps nel cluster esegue continuamente il polling del repository Git per recuperare lo stato desiderato delle risorse Kubernetes nel cluster. Se l'operatore GitOps rileva lo stato desiderato delle risorse in modo che sia diverso dallo stato effettivo delle risorse nel cluster, questa deviazione viene risolta.

**Applica GitOps su larga scala**

Le pipeline CI/CD sono utili per le distribuzioni basate su eventi nel cluster Kubernetes (ad esempio, un push in un repository git). Tuttavia, se si vuole distribuire la stessa configurazione in tutti i cluster Kubernetes, è necessario configurare manualmente le credenziali di ogni cluster Kubernetes per la pipeline CI/CD. 

Per Azure Arc Enabled Kubernetes, poiché Azure Resource Manager gestisce le configurazioni, è possibile automatizzare la creazione della stessa configurazione in tutte le risorse Kubernetes abilitate per Azure ARC usando criteri di Azure, nell'ambito di una sottoscrizione o di un gruppo di risorse. Questa funzionalità è applicabile anche alle risorse Kubernetes abilitate per Azure Arc create dopo l'assegnazione dei criteri.

Questa funzionalità applica le configurazioni di base (ad esempio i criteri di rete, le associazioni di ruolo e i criteri di sicurezza Pod) nell'intero inventario cluster Kubernetes per soddisfare i requisiti di conformità e governance.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare la Guida introduttiva per [connettere un cluster Kubernetes ad Azure Arc](./quickstart-connect-cluster.md).
* Si dispone già di un cluster Kubernetes connesso ad Azure Arc? [Creare configurazioni nel cluster Kubernetes abilitato per l'arco](./tutorial-use-gitops-connected-cluster.md).
* Informazioni su come [usare i criteri di Azure per applicare configurazioni su larga scala](./use-azure-policy.md).