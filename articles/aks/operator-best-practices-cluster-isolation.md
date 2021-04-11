---
title: Procedure consigliate per l'isolamento del cluster
titleSuffix: Azure Kubernetes Service
description: Procedure consigliate per l'operatore del cluster per l'isolamento nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105171"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Procedure consigliate per l'isolamento cluster nel servizio Azure Kubernetes (AKS)

Quando si gestiscono i cluster nel servizio Azure Kubernetes (AKS), spesso è necessario isolare i team e i carichi di lavoro. Il servizio Azure Kubernetes offre flessibilità nella modalità di esecuzione dei cluster multi-tenant e di isolamento delle risorse. Per massimizzare l'investimento in Kubernetes, è necessario comprendere e implementare le funzionalità AKS di multi-tenant e di isolamento.

Questo articolo sulle procedure consigliate è incentrato sull'isolamento per gli operatori del cluster. In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Pianificare cluster multi-tenant e la separazione delle risorse
> * Usare l'isolamento logico o fisico nei cluster del servizio Azure Kubernetes

## <a name="design-clusters-for-multi-tenancy"></a>Progettare cluster per il multi-tenancy

Kubernetes consente di isolare logicamente i team e i carichi di lavoro nello stesso cluster. L'obiettivo è fornire il minor numero di privilegi, con l'ambito delle risorse necessarie per ogni team. Uno [spazio dei nomi][k8s-namespaces] Kubernetes crea un limite di isolamento logico. Le funzionalità e le considerazioni aggiuntive di Kubernetes per l'isolamento e la multi-tenant includono le aree seguenti:

### <a name="scheduling"></a>Pianificazione

La *pianificazione* usa le funzionalità di base, ad esempio le quote delle risorse e i budget per l'interferenza pod. Per altre informazioni su queste funzionalità, vedere [Procedure consigliate per le funzionalità di base dell'utilità di pianificazione in servizio Azure Kubernetes][aks-best-practices-scheduler].

Le funzionalità più avanzate di utilità di pianificazione includono:
* Macchie e tolleranze
* Selettori di nodo
* Affinità di nodi e pod o anti-affinità. 

Per altre informazioni su queste funzionalità, vedere [Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione in servizio Azure Kubernetes][aks-best-practices-advanced-scheduler].

### <a name="networking"></a>Rete

La *rete* usa i criteri di rete per controllare il flusso del traffico all'interno e all'esterno dei pod.

### <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Usi *di autenticazione e autorizzazione* :
* Controllo degli accessi in base al ruolo
* Integrazione di Azure Active Directory (AD)
* Identità Pod
* Segreti in Azure Key Vault 

Per altre informazioni su queste funzionalità, vedere [Procedure consigliate per l'autenticazione e l'autorizzazione in servizio Azure Kubernetes][aks-best-practices-identity].

### <a name="containers"></a>Contenitori
I *contenitori* includono:
* Il componente aggiuntivo criteri di Azure per AKS per applicare la sicurezza pod.
* Uso dei contesti di sicurezza pod.
* Analisi di immagini e Runtime per le vulnerabilità. 
* Uso di app blind o Seccomp (Secure Computing) per limitare l'accesso del contenitore al nodo sottostante.

## <a name="logically-isolate-clusters"></a>Isolare i cluster in modo logico

> **Indicazioni sulle procedure consigliate**
>
> Separare i team e i progetti usando l' *isolamento logico*. Ridurre al minimo il numero di cluster AKS fisici distribuiti per isolare i team o le applicazioni.

Con l'isolamento logico, un singolo cluster servizio Azure Kubernetes può essere usato per più carichi di lavoro, team o ambienti. Gli [spazi dei nomi][k8s-namespaces] di Kubernetes costituiscono il limite di isolamento logico per i carichi di lavoro e le risorse.

![Isolamento logico di un cluster Kubernetes in servizio Azure Kubernetes](media/operator-best-practices-cluster-isolation/logical-isolation.png)

La separazione logica dei cluster in genere fornisce una densità di Pod superiore rispetto ai cluster fisicamente isolati, con una capacità di calcolo meno eccessiva che si trova in inattività nel cluster. Se combinato con il ridimensionamento automatico del cluster Kubernetes, è possibile aumentare o ridurre il numero dei nodi in base alle esigenze. Questo approccio alle procedure consigliate per la scalabilità automatica riduce al minimo i costi eseguendo solo il numero di nodi necessari.

Attualmente, gli ambienti Kubernetes non sono completamente sicuri per l'utilizzo di multi-tenant ostili. In un ambiente multi-tenant, più tenant lavorano su un'infrastruttura condivisa comune. Se tutti i tenant non possono essere considerati attendibili, sarà necessaria una pianificazione aggiuntiva per impedire ai tenant di influenzare la sicurezza e il servizio di altri.

Funzionalità di sicurezza aggiuntive, come i *criteri di sicurezza Pod* o Kubernetes RBAC per i nodi, bloccano efficacemente gli exploit. Per una sicurezza reale quando si eseguono carichi di lavoro multi-tenant ostili, è consigliabile considerare attendibile solo un hypervisor. Il dominio di sicurezza per Kubernetes diventa l'intero cluster, non un singolo nodo. 

Per questi tipi di carichi di lavoro multi-tenant ostili è consigliabile usare cluster fisicamente isolati.

## <a name="physically-isolate-clusters"></a>Isolare i cluster in modo fisico

> **Indicazioni sulle procedure consigliate** 
>
> Ridurre al minimo l'utilizzo dell'isolamento fisico per ogni singola distribuzione del team o dell'applicazione. Usare invece l'isolamento *logico*, come descritto nella sezione precedente.

Separare fisicamente i cluster AKS è un approccio comune all'isolamento del cluster. In questo modello di isolamento ai team o ai carichi di lavoro viene assegnato il proprio cluster servizio Azure Kubernetes. Sebbene l'isolamento fisico possa apparire come il modo più semplice per isolare i carichi di lavoro o i team, viene aggiunto un sovraccarico finanziario e di gestione. A questo punto, è necessario gestire questi più cluster e fornire singolarmente le autorizzazioni di accesso e assegnazione. Verranno addebitati anche i costi per ogni singolo nodo.

![Isolamento fisico di singoli cluster Kubernetes in servizio Azure Kubernetes](media/operator-best-practices-cluster-isolation/physical-isolation.png)

I cluster separati fisicamente hanno in genere una bassa densità del pod. Poiché ogni team o carico di lavoro ha il proprio cluster AKS, il cluster viene spesso sottoposti a provisioning con le risorse di calcolo. Spesso viene pianificato un numero ridotto di Pod su tali nodi. Non è possibile usare la capacità del nodo non reclamato per le applicazioni o i servizi in fase di sviluppo da altri team. Queste risorse in eccesso contribuiscono ai costi aggiuntivi nei cluster separati fisicamente.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato l'isolamento cluster. Per altre informazioni sulle operazioni cluster in servizio Azure Kubernetes, vedere le procedure consigliate seguenti:

* [Funzionalità di base dell'utilità di pianificazione di Kubernetes][aks-best-practices-scheduler]
* [Funzionalità avanzate dell'utilità di pianificazione di Kubernetes][aks-best-practices-advanced-scheduler]
* [Autenticazione e autorizzazione][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
