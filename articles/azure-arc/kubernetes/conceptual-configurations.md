---
title: Configurazioni e GitOps-Azure Arc abilitato Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Questo articolo fornisce una panoramica concettuale di GitOps e delle funzionalità di configurazione di Azure Arc Enabled Kubernetes.
keywords: Kubernetes, Arc, Azure, contenitori, configurazione, GitOps
ms.openlocfilehash: f8fe1522eee4cc855ae1f396d9c98323114a25ce
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652548"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Configurazioni e GitOps con Azure Arc abilitato Kubernetes

In relazione a Kubernetes, GitOps è la procedura per dichiarare lo stato desiderato delle configurazioni del cluster Kubernetes (distribuzioni, spazi dei nomi e così via) in un repository git. Questa dichiarazione è seguita da una distribuzione di polling e basata su pull di queste configurazioni cluster tramite un operatore. Il repository Git può contenere:
* Manifesti di formato YAML che descrivono le risorse Kubernetes valide, inclusi spazi dei nomi, ConfigMaps, distribuzioni, gli elementi daemonset e così via.
* Grafici Helm per la distribuzione di applicazioni.

[Flux](https://docs.fluxcd.io/), uno strumento open source noto nello spazio GitOps, può essere distribuito nel cluster Kubernetes per semplificare il flusso di configurazioni da un repository git a un cluster Kubernetes. Flux supporta la distribuzione del relativo operatore in entrambi gli ambiti del cluster e dello spazio dei nomi. Un operatore Flux distribuito con ambito spazio dei nomi può distribuire solo oggetti Kubernetes all'interno di tale spazio dei nomi specifico. La possibilità di scegliere tra l'ambito del cluster o dello spazio dei nomi consente di ottenere modelli di distribuzione multi-tenant nello stesso cluster Kubernetes.

## <a name="configurations"></a>Configurazioni

[![Architettura ](./media/conceptual-configurations.png) delle configurazioni](./media/conceptual-configurations.png#lightbox)

La connessione tra il cluster e un repository Git viene creata come `Microsoft.KubernetesConfiguration/sourceControlConfigurations` risorsa di estensione nella parte superiore della risorsa Kubernetes abilitata per Azure Arc (rappresentata da `Microsoft.Kubernetes/connectedClusters` ) in Azure Resource Manager. 

Le `sourceControlConfiguration` proprietà delle risorse vengono usate per distribuire un operatore Flux nel cluster con i parametri appropriati, ad esempio il repository git dal quale eseguire il pull dei manifesti e l'intervallo di polling in cui eseguirne il pull. I `sourceControlConfiguration` dati vengono archiviati crittografati e inattivi in un database di Azure Cosmos DB per garantire la riservatezza dei dati.

Il `config-agent` in esecuzione nel cluster è responsabile di:
* Rilevamento `sourceControlConfiguration` delle risorse di estensione nuove o aggiornate nella risorsa Kubernetes abilitata per Azure Arc.
* Distribuzione di un operatore Flux per il controllo dell'archivio git `sourceControlConfiguration` .
* Applicazione degli aggiornamenti apportati a qualsiasi `sourceControlConfiguration` . 

È possibile creare più risorse con ambito spazio dei nomi nello `sourceControlConfiguration` stesso cluster Kubernetes abilitato per Azure Arc per ottenere il multi-tenant.

> [!NOTE]
> * `config-agent` monitora continuamente le risorse di estensione nuove o aggiornate `sourceControlConfiguration` disponibili nella risorsa Kubernetes abilitata per Azure Arc. Gli agenti richiedono pertanto una connettività coerente per eseguire il pull delle proprietà di stato desiderate al cluster. Se gli agenti non sono in grado di connettersi ad Azure, lo stato desiderato non viene applicato al cluster.
> * Gli input sensibili dei clienti come la chiave privata, il contenuto di host noti, il nome utente HTTPS e il token o la password vengono archiviati per un massimo di 48 ore nei servizi Kubernetes abilitati per Azure Arc. Se si usano input sensibili per le configurazioni, portare online i cluster il più regolarmente possibile.

## <a name="apply-configurations-at-scale"></a>Applica configurazioni su larga scala

Poiché Azure Resource Manager gestisce le configurazioni, è possibile automatizzare la creazione della stessa configurazione in tutte le risorse Kubernetes abilitate per Azure ARC usando criteri di Azure, nell'ambito di una sottoscrizione o di un gruppo di risorse. 

Questa applicazione su larga scala garantisce una configurazione di base comune (che contiene configurazioni come ClusterRoleBindings, RoleBindings e NetworkPolicy) può essere applicata a un'intera flotta o a un inventario dei cluster Kubernetes abilitati per Azure Arc.

## <a name="next-steps"></a>Passaggi successivi

* [Connettere un cluster ad Azure Arc](./connect-cluster.md)
* [Creare configurazioni nel cluster Kubernetes abilitato per l'arco](./use-gitops-connected-cluster.md)
* [Usare i criteri di Azure per applicare configurazioni su larga scala](./use-azure-policy.md)