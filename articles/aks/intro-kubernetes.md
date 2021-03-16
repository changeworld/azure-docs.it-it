---
title: Introduzione al servizio Azure Kubernetes
description: Informazioni sulle funzioni e i vantaggi del servizio Azure Kubernetes nella distribuzione e gestione delle applicazioni basate su contenitore in Azure.
services: container-service
ms.topic: overview
ms.date: 02/24/2021
ms.custom: mvc
ms.openlocfilehash: bb4adac1f59370959830f418d27bc27f9aaf63d2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493017"
---
# <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

Azure Kubernetes Service (AKS) semplifica la distribuzione di un cluster Kubernetes gestito in Azure tramite l'offload del sovraccarico operativo in Azure. Come servizio ospitato Kubernetes, Azure gestisce attività critiche, come il monitoraggio dell'integrità e la manutenzione. Poiché i master Kubernetes sono gestiti da Azure, è possibile gestire e gestire solo i nodi dell'agente. Quindi, AKS è gratuito; si paga solo per i nodi dell'agente nei cluster, non per i master.  

È possibile creare un cluster AKS usando:
* [L’interfaccia della riga di comando di Azure](kubernetes-walkthrough.md)
* [Il portale di Azure](kubernetes-walkthrough-portal.md)
* [Azure PowerShell](kubernetes-walkthrough-powershell.md)
* Uso di opzioni di distribuzione basate su modelli, ad esempio [modelli Azure Resource Manager](kubernetes-walkthrough-rm-template.md) e bonifica 

Quando si distribuisce un cluster servizio Azure Kubernetes, il master Kubernetes e tutti i nodi vengono distribuiti e configurati automaticamente. Durante il processo di distribuzione, è possibile configurare l'integrazione di rete, Azure Active Directory (Azure AD) avanzata, il monitoraggio e altre funzionalità. 

Per altre informazioni sulle nozioni di base di Kubernetes, vedere [Concetti di base di Kubernetes per il servizio Azure Kubernetes][concepts-clusters-workloads].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]
> AKS supporta inoltre i contenitori di Windows Server.

## <a name="access-security-and-monitoring"></a>Accesso, sicurezza e monitoraggio

Per migliorare la sicurezza e la gestione, AKS consente di eseguire l'integrazione con Azure AD per:
* Usare il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC). 
* Monitorare l'integrità del cluster e delle risorse.

### <a name="identity-and-security-management"></a>Gestione delle identità e della sicurezza

#### <a name="kubernetes-rbac"></a>Controllo degli accessi in base al ruolo di Kubernetes

Per limitare l'accesso alle risorse cluster, AKS supporta [KUBERNETES RBAC][kubernetes-rbac]. Kubernetes RBAC controlla l'accesso e le autorizzazioni per le risorse e gli spazi dei nomi Kubernetes.  

#### <a name="azure-ad"></a>Azure AD

È possibile configurare un cluster AKS per l'integrazione con Azure AD. Con l'integrazione di Azure AD, è possibile configurare l'accesso Kubernetes in base all'identità esistente e all'appartenenza al gruppo. Gli utenti e i gruppi di Azure AD esistenti possono essere forniti con un'esperienza di accesso integrata e l'accesso alle risorse di AKS.  

Per altre informazioni sull'identità, vedere [Opzioni di accesso e identità per il servizio Azure Kubernetes][concepts-identity].

Per proteggere i cluster del servizio Azure Kubernetes, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Registrazione e monitoraggio integrati

Monitoraggio di Azure per l'integrità del contenitore raccoglie le metriche delle prestazioni di memoria e processore da contenitori, nodi e controller all'interno del cluster AKS e delle applicazioni distribuite. È possibile esaminare i log dei contenitori e [i log master di Kubernetes][aks-master-logs], che sono:
* Archiviato in un'area di lavoro di Azure Log Analytics.
* Disponibile tramite l'portale di Azure, l'interfaccia della riga di comando di Azure o un endpoint REST.

Per altre informazioni, vedere [Monitorare l'integrità dei contenitori del servizio Azure Kubernetes][container-health].

## <a name="clusters-and-nodes"></a>Cluster e nodi

I nodi AKS vengono eseguiti in macchine virtuali (VM) di Azure. Con i nodi AKS è possibile connettere lo spazio di archiviazione ai nodi e ai pod, aggiornare i componenti del cluster e usare le GPU. Il servizio Azure Kubernetes supporta i cluster Kubernetes che eseguono più pool di nodi per supportare sistemi operativi misti e contenitori Windows Server.  

Per altre informazioni sulle funzionalità del cluster Kubernetes, dei nodi e dei pool di nodi, vedere [concetti di base di Kubernetes per AKS][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Ridimensionamento dei nodi del cluster e dei pod

Con la modifica della richiesta di risorse, il numero di nodi del cluster o di pod che eseguono i servizi viene ridimensionato automaticamente. È possibile modificare sia il ridimensionamento automatico del Pod orizzontale che il ridimensionamento automatico del cluster per adattarsi alle richieste ed eseguire solo le risorse necessarie.

Per altre informazioni, vedere [Ridimensionare un cluster del servizio Azure Kubernetes][aks-scale].

### <a name="cluster-node-upgrades"></a>Aggiornamenti dei nodi del cluster

AKS offre più versioni di Kubernetes. Quando le nuove versioni diventano disponibili nel servizio contenitore di Azure, è possibile aggiornare il cluster usando il portale di Azure o l'interfaccia della riga di comando di Azure. Durante il processo di aggiornamento, i nodi vengono accuratamente contrassegnati come non pianificabili e svuotati per ridurre al minimo le interruzioni nelle applicazioni in esecuzione.  

Per altre informazioni sulle versioni del ciclo di vita, vedere [Versioni di Kubernetes supportate nel servizio Azure Kubernetes][aks-supported versions]. Per la procedura di aggiornamento, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Nodi abilitati per GPU

AKS supporta la creazione di pool di nodi abilitati per GPU. Azure fornisce attualmente una o più macchine virtuali abilitate per la GPU. Le macchine virtuali abilitate per la GPU sono progettate per carichi di lavoro a elevato utilizzo di calcolo, a elevato utilizzo di grafica e di visualizzazione.

Per altre informazioni, vedere [Uso di GPU nel servizio Azure Kubernetes][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Nodi di confidential computing (anteprima pubblica)

AKS supporta la creazione di pool di nodi di calcolo riservati basati su Intel SGX (VM DCSv2). I nodi di elaborazione riservati consentono l'esecuzione di contenitori in un ambiente di esecuzione attendibile basato su hardware (enclave). L'isolamento tra i contenitori, combinati con l'integrità del codice tramite l'attestazione, può essere utile per la strategia di sicurezza del contenitore di difesa in profondità. I nodi di elaborazione riservati supportano sia i contenitori riservati (app Docker esistenti) che i contenitori compatibili con l'enclave.

Per altre informazioni, vedere [nodi di elaborazione riservati su AKS][conf-com-node].

### <a name="storage-volume-support"></a>Supporto di volumi di archiviazione

Per supportare i carichi di lavoro dell'applicazione, è possibile montare volumi di archiviazione statici o dinamici per i dati persistenti. A seconda del numero di Pod connessi che dovrebbero condividere i volumi di archiviazione, è possibile usare lo spazio di archiviazione supportato da uno dei seguenti:
* Dischi di Azure per l'accesso a un singolo POD o 
* File di Azure per più accessi Pod simultanei.

Per altre informazioni, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

Capire come usare i volumi persistenti dinamici con [Dischi di Azure][azure-disk] o [File di Azure][azure-files].

## <a name="virtual-networks-and-ingress"></a>Reti virtuali e ingresso

Un cluster servizio Azure Kubernetes può essere distribuito in una rete virtuale esistente. In questa configurazione, a ogni pod del cluster viene assegnato un indirizzo IP nella rete virtuale e può comunicare direttamente con:
* Altri pod nel cluster 
* Altri nodi nella rete virtuale. 

I pod possono anche connettersi ad altri servizi in una rete virtuale con peering e alle reti locali tramite connessioni VPN ExpressRoute o da sito a sito (S2S).  

Per altre informazioni, vedere [Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Ingresso con routing di applicazioni HTTP

Il componente aggiuntivo routing applicazione HTTP consente di accedere facilmente alle applicazioni distribuite nel cluster AKS. Quando abilitata, la soluzione Routing di applicazioni HTTP configura un controller di ingresso nel cluster servizio Azure Kubernetes.  

Quando vengono distribuite le applicazioni, i nomi DNS accessibili pubblicamente sono configurati automaticamente. Il routing dell'applicazione HTTP configura una zona DNS e la integra con il cluster AKS. È quindi possibile distribuire le risorse Kubernetes in ingresso come di consueto.  

Per iniziare a usare il traffico in ingresso, vedere [Routing di applicazioni HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integrazione di strumenti di sviluppo

Kubernetes dispone di un ricco ecosistema di strumenti di sviluppo e gestione che funzionano senza interruzioni con AKS. Questi strumenti includono Helm e l'estensione Kubernetes per Visual Studio Code.   

Azure offre diversi strumenti che consentono di semplificare Kubernetes, ad esempio Azure Dev Spaces e DevOps Starter.  

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Azure Dev Spaces offre un'esperienza di sviluppo Kubernetes rapida e iterativa per i team. Con una configurazione minima è possibile eseguire i contenitori ed effettuarne il debug direttamente in servizio Azure Kubernetes. Per iniziare, vedere[Azure Dev Spaces][azure-dev-spaces].

### <a name="devops-starter"></a>Starter DevOps

DevOps Starter fornisce una soluzione semplice per trasferire in Azure il codice esistente e i repository git. DevOps starter automatico:
* Crea risorse di Azure (ad esempio AKS); 
* Configura una pipeline di rilascio in Azure DevOps Services che include una pipeline di compilazione per CI; 
* Configura una pipeline di versione per CD; e 
* Genera una risorsa applicazione Azure Insights per il monitoraggio. 

Per altre informazioni, vedere [DevOps Starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Supporto per le immagini Docker e il registro contenitori privato

servizio Azure Kubernetes supporta il formato di immagine Docker. Per l'archiviazione privata delle immagini Docker è possibile integrare servizio Azure Kubernetes con Registro Azure Container.

Per creare un archivio immagini privato, vedere [container Registry di Azure][acr-docs].

## <a name="kubernetes-certification"></a>Certificazione Kubernetes

AKS è stato CNCFmente certificato come Kubernetes conforme.

## <a name="regulatory-compliance"></a>Conformità alle normative

AKS è conforme a SOC, ISO, PCI DSS e HIPAA. Per altre informazioni, vedere [Panoramica della conformità di Microsoft Azure][compliance-doc].

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione e la gestione di AKS con l'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Distribuire un cluster AKS con l'interfaccia della riga di comando][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://azure.microsoft.com/en-us/overview/trusted-cloud/compliance/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: ./view-control-plane-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
