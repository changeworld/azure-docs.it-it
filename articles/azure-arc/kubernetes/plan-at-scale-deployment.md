---
title: Come pianificare e distribuire Kubernetes di Azure Arc abilitato
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Onboarding di un elevato numero di cluster in Azure Arc abilitato Kubernetes per la gestione della configurazione
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315490"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Pianificare e distribuire Azure Arc abilitato Kubernetes

La distribuzione di un servizio di infrastruttura IT o di un'applicazione aziendale è una sfida per qualsiasi azienda. Per evitare sorprese o costi non pianificati, è necessario pianificarlo accuratamente per assicurarsi di essere il più pronto possibile. Tale piano dovrebbe identificare i criteri di progettazione e distribuzione che devono essere soddisfatti per completare le attività.

Affinché la distribuzione continui a funzionare senza problemi, il piano deve stabilire una chiara comprensione di:

* Ruoli e responsabilità.
* Inventario di tutti i cluster Kubernetes
* Soddisfare i requisiti di rete.
* Il set di competenze e il training necessari per consentire una corretta distribuzione e una gestione continua.
* Criteri di accettazione e il modo in cui si tiene traccia dell'esito positivo.
* Strumenti o metodi da usare per automatizzare le distribuzioni.
* Sono stati identificati i rischi e i piani di mitigazione per evitare ritardi e rotture.
* Come evitare l'interferenza durante la distribuzione.
* Qual è il percorso di escalation quando si verifica un problema significativo?

Lo scopo di questo articolo è assicurarsi di essere preparati per una corretta distribuzione di Azure Arc abilitato Kubernetes tra più cluster di produzione nell'ambiente in uso.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Kubernetes esistente. Se non si dispone di un cluster, è possibile crearne uno usando una di queste opzioni:
    - [Kubernetes in Docker (KIND)](https://kind.sigs.k8s.io/)
    - Creare un cluster Kubernetes con Docker per [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) o [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    - Cluster Kubernetes self-Managed con l' [API cluster](https://cluster-api.sigs.k8s.io/user/quick-start.html)

* I computer dispongono di connettività dalla rete locale o da un altro ambiente cloud alle risorse in Azure, direttamente o tramite un server proxy. Ulteriori informazioni sono disponibili in [prerequisiti di rete](quickstart-connect-cluster.md#meet-network-requirements).

* Un `kubeconfig` file che punta al cluster che si vuole connettere ad Azure Arc.
* Autorizzazioni ' Read ' è Write ' per l'utente o l'entità servizio che crea il tipo di risorsa Kubernetes abilitato per Azure Arc `Microsoft.Kubernetes/connectedClusters` .

## <a name="pilot"></a>Fase pilota

Prima di eseguire la distribuzione in tutti i cluster di produzione, iniziare valutando questo processo di distribuzione prima di adottarlo in modo esteso nell'ambiente in uso. Per un progetto pilota, identificare un campionamento rappresentativo di cluster che non sono cruciali per le aziende in grado di condurre le attività aziendali. È necessario assicurarsi di concedere tempo sufficiente per eseguire il progetto pilota e valutarne l'effetto: è consigliabile circa 30 giorni.

Definire un piano formale che descriva l'ambito e i dettagli del progetto pilota. Il piano di esempio seguente dovrebbe essere utile per iniziare.

* **Obiettivi** : descrive i driver aziendali e tecnici che hanno portato alla decisione che un progetto pilota è necessario.
* **Criteri di selezione** : specifica i criteri usati per selezionare gli aspetti della soluzione che verranno illustrati tramite un progetto pilota.
* **Ambito** : vengono illustrati i componenti della soluzione, la pianificazione prevista, la durata del progetto pilota e il numero di cluster di destinazione.
* **Criteri e metriche di successo** : definire i criteri di successo del pilota e misure specifiche usate per determinare il livello di successo.
* **Piano di formazione** : descrive il piano per i tecnici del sistema di formazione, gli amministratori e così via, che non hanno familiarità con Azure e i servizi IT durante la fase pilota.
* **Piano di transizione** : descrive la strategia e i criteri usati per guidare la transizione dal progetto pilota alla produzione.
* **Rollback** : descrive le procedure per eseguire il rollback di un progetto pilota allo stato di pre-distribuzione.
* **Rischi** : elenca tutti i rischi identificati per condurre il progetto pilota e associato alla distribuzione di produzione.

## <a name="phase-1-build-a-foundation"></a>Fase 1: creare una base

In questa fase, i tecnici o gli amministratori di sistema eseguono le attività principali, ad esempio la creazione di gruppi di risorse, tag e assegnazioni di ruolo, in modo che le risorse Kubernetes abilitate per Azure Arc possano quindi essere create e gestite.

|Attività |Dettaglio |Duration |
|-----|-------|---------|
| [Creare un gruppo di risorse](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Un gruppo di risorse dedicato per includere solo le risorse Kubernetes abilitate per Azure Arc e centralizzare la gestione e il monitoraggio di queste risorse. | Un'ora |
| Applicare i [tag](../../azure-resource-manager/management/tag-resources.md) per organizzare i computer. | Valutare e sviluppare una [strategia di assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/)allineata. Questo consente di ridurre la complessità della gestione delle risorse Kubernetes abilitate per Azure Arc e di semplificare le decisioni di gestione. | Un giorno |
| Identificare le [configurazioni](tutorial-use-gitops-connected-cluster.md) per GitOps | Identificare le configurazioni dell'applicazione o della linea di base, ad esempio `PodSecurityPolicy` , `NetworkPolicy` che si desidera distribuire nei cluster | Un giorno |
| [Sviluppare un piano di governance dei criteri di Azure](../../governance/policy/overview.md) | Determinare come verrà implementata la governance dei cluster Kubernetes abilitati per Azure Arc nell'ambito della sottoscrizione o del gruppo di risorse con criteri di Azure. | Un giorno |
| Configurare il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) | Sviluppare un piano di accesso per identificare gli utenti che dispongono di autorizzazioni di lettura/scrittura/tutte per i cluster | Un giorno |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>Fase 2: distribuire Azure Arc abilitato Kubernetes

In questa fase i cluster Kubernetes vengono connessi ad Azure:

|Attività |Dettaglio |Duration |
|-----|-------|---------|
| [Connettere il primo cluster Kubernetes ad Azure Arc](quickstart-connect-cluster.md) | Come parte della connessione del primo cluster ad Azure Arc, configurare l'ambiente di onboarding con tutti gli strumenti necessari, ad esempio l'interfaccia della riga di comando di Azure, Helm ed Extension per l'interfaccia della riga di comando di `connectedk8s` Azure. | 15 minuti |
| [Creare un'entità servizio](create-onboarding-service-principal.md) | Creare un'entità servizio per connettere i cluster Kubernetes in modo non interattivo usando l'interfaccia della riga di comando di Azure o PowerShell. | Un'ora |


## <a name="phase-3-manage-and-operate"></a>Fase 3: gestione e funzionamento

In questa fase vengono distribuite le applicazioni e le configurazioni di base nei cluster Kubernetes.

|Attività |Dettaglio |Duration |
|-----|-------|---------|
|[Creare configurazioni](tutorial-use-gitops-connected-cluster.md) nei cluster | Creare configurazioni per la distribuzione delle applicazioni nella risorsa Kubernetes abilitata per Azure Arc. | 15 minuti |
|[Usare i criteri di Azure](use-azure-policy.md) per l'applicazione su larga scala delle configurazioni | Creare assegnazioni di criteri per automatizzare la distribuzione delle configurazioni di base in tutti i cluster in un ambito di sottoscrizione o di gruppo di risorse. | 15 minuti |
| [Aggiornare gli agenti di Azure Arc](agent-upgrade.md) | Se è stato disabilitato l'aggiornamento automatico degli agenti nei cluster, aggiornare manualmente gli agenti alla versione più recente per assicurarsi di avere le correzioni di bug e sicurezza più recenti. | 15 minuti |

## <a name="next-steps"></a>Passaggi successivi

* Usare la Guida introduttiva per [connettere un cluster Kubernetes ad Azure Arc](./quickstart-connect-cluster.md).
* [Creare configurazioni](./tutorial-use-gitops-connected-cluster.md) nel cluster Kubernetes abilitato per Azure Arc.
* [Usare i criteri di Azure per applicare configurazioni su larga scala](./use-azure-policy.md).