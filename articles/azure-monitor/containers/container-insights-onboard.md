---
title: Abilitare Le informazioni dettagliate sui contenitori | Microsoft Docs
description: Questo articolo descrive come abilitare e configurare informazioni dettagliate sui contenitori in modo da comprendere le prestazioni del contenitore e quali problemi correlati alle prestazioni sono stati identificati.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: e0544232f40e93cce0705fff6814d29697a96218
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782118"
---
# <a name="enable-container-insights"></a>Abilitare le informazioni dettagliate sui contenitori

Questo articolo offre una panoramica delle opzioni disponibili per la configurazione di Informazioni dettagliate sui contenitori per monitorare le prestazioni dei carichi di lavoro distribuiti in ambienti Kubernetes e ospitati in:

- [Servizio Azure Kubernetes](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versioni 3.x e 4.x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versione 4.x  
- Un [cluster Kubernetes abilitato](../../azure-arc/kubernetes/overview.md) per Arc

È anche possibile monitorare le prestazioni dei carichi di lavoro distribuiti in cluster Kubernetes auto-gestiti ospitati in:
- Azure, usando il motore del servizio [AzureKs](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) o in locale, usando il motore del servizio Web AKS.

È possibile abilitare Informazioni dettagliate sul contenitore per una nuova distribuzione o per una o più distribuzioni esistenti di Kubernetes usando uno dei metodi supportati seguenti:

- Portale di Azure
- Azure PowerShell
- Interfaccia della riga di comando di Azure
- [Terraform e servizio AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di aver soddisfatto i requisiti seguenti:

> [!IMPORTANT]
> L'agente Linux in contenitori di Log Analytics (pod del set di repliche) effettua chiamate API a tutti i nodi di Windows sulla porta sicura Kubelet (10250) all'interno del cluster per raccogliere le metriche correlate alle prestazioni dei nodi e dei contenitori. La porta sicura Kubelet (:10250) deve essere aperta nella rete virtuale del cluster per il funzionamento sia in ingresso che in uscita per il funzionamento della raccolta di metriche correlate alle prestazioni del nodo Windows e del contenitore.
>
> Se si dispone di un cluster Kubernetes con nodi Windows, esaminare e configurare il gruppo di sicurezza di rete e i criteri di rete per assicurarsi che la porta sicura Kubelet (:10250) sia aperta sia in ingresso che in uscita nella rete virtuale del cluster.


- Si dispone di un'area di lavoro Log Analytics.

   Informazioni dettagliate sui contenitori supporta un'area di lavoro Log Analytics nelle aree elencate in [Prodotti disponibili per area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

   È possibile creare un'area di lavoro quando si abilita il monitoraggio per il nuovo cluster del servizio AzureKs oppure è possibile consentire all'esperienza di onboarding di creare un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster del servizio AzureKs. 
   
   Se si sceglie di creare l'area di lavoro manualmente, è possibile crearla tramite: 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Il portale di Azure](../logs/quick-create-workspace.md) 
   
   Per un elenco delle coppie di mapping supportate da usare per l'area di lavoro predefinita, vedere [Mapping delle aree per Informazioni dettagliate sui contenitori.](container-insights-region-mapping.md)

- Si è membri del gruppo di collaboratori *di Log Analytics* per abilitare il monitoraggio dei contenitori. Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../logs/manage-access.md).

- L'utente è membro del gruppo [ *Proprietario nella*](../../role-based-access-control/built-in-roles.md#owner) risorsa cluster del servizio Web Diaks.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Per visualizzare i dati di monitoraggio, è necessario avere il ruolo di lettore [*di Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro Log Analytics, configurato con Informazioni dettagliate sul contenitore.

- Le metriche prometheus non vengono raccolte per impostazione predefinita. Prima di [configurare l'agente](container-insights-prometheus-integration.md) per la raccolta delle metriche, è importante esaminare la documentazione di [Prometheus](https://prometheus.io/) per comprendere quali dati possono essere raccolti e quali metodi sono supportati.
- È possibile collegare un cluster del servizio Azure Azure AD a un'area di lavoro Log Analytics in una sottoscrizione di Azure diversa Azure AD tenant. Questa operazione non può attualmente essere eseguita con il portale di Azure, ma può essere eseguita con l'interfaccia della riga di comando di Azure o Resource Manager modello.

## <a name="supported-configurations"></a>Configurazioni supportate

Container Insights supporta ufficialmente le configurazioni seguenti:

- Ambienti: Azure Red Hat OpenShift, Kubernetes in locale e il motore del servizio Azure Kubernetes in Azure e Azure Stack. Per altre informazioni, vedere il [motore del Azure Stack AKS.](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
- Le versioni di Kubernetes e i criteri di supporto sono uguali a quelli [supportati in servizio Azure Kubernetes (AKS).](../../aks/supported-kubernetes-versions.md) 

## <a name="network-firewall-requirements"></a>Requisiti del firewall di rete

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall necessarie per consentire all'agente in contenitori di comunicare con Informazioni dettagliate sul contenitore. Tutto il traffico di rete dall'agente è in uscita verso Monitoraggio di Azure.

|Risorsa dell'agente|Porta |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

Nella tabella seguente sono elencate le informazioni di configurazione del proxy e del firewall per Azure China (21Vianet):

|Risorsa dell'agente|Porta |Descrizione | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Inserimento dati |
| `*.oms.opinsights.azure.cn` | 443 | Onboarding di OMS |
| `dc.services.visualstudio.com` | 443 | Per i dati di telemetria dell'agente che usa cloud pubblico di Azure Application Insights |

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall per Azure US Government:

|Risorsa dell'agente|Porta |Descrizione | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Inserimento dati |
| `*.oms.opinsights.azure.us` | 443 | Onboarding di OMS |
| `dc.services.visualstudio.com` | 443 | Per i dati di telemetria dell'agente che usa Cloud pubblico di Azure Application Insights |

## <a name="components"></a>Componenti

La possibilità di monitorare le prestazioni si basa su un agente di Log Analytics in contenitori per Linux appositamente sviluppato per informazioni dettagliate sui contenitori. L'agente specializzato raccoglie i dati su prestazioni ed eventi da tutti i nodi del cluster e viene distribuito e registrato automaticamente con l'area di lavoro Log Analytics specificata durante la distribuzione. 

La versione dell'agente è microsoft/oms:rsid04202018 o versione successiva ed è rappresentata da una data nel formato seguente: *mmddyyyy*.

>[!NOTE]
>Con la disponibilità generale del supporto di Windows Server per il servizio AKS, in un cluster del servizio Web Dicking con nodi di Windows Server è installato un agente di anteprima come pod daemonset in ogni singolo nodo del server Windows per raccogliere i log e inoltrarlo a Log Analytics. Per le metriche delle prestazioni, un nodo Linux distribuito automaticamente nel cluster come parte della distribuzione standard raccoglie e inoltra i dati a Monitoraggio di Azure per conto di tutti i nodi Windows nel cluster.

Quando viene rilasciata una nuova versione dell'agente, viene aggiornato automaticamente nei cluster Kubernetes gestiti ospitati in servizio Azure Kubernetes (AKS). Per tenere traccia delle versioni rilasciate, vedere [annunci sulla versione dell'agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Se è già stato distribuito un cluster del servizio AzureKs, è stato abilitato il monitoraggio usando l'interfaccia della riga di comando di Azure o un modello Azure Resource Manager fornito, come illustrato più avanti in questo articolo. Non è possibile usare per `kubectl` aggiornare, eliminare, ridistribuire o distribuire l'agente.
>
> Il modello deve essere distribuito nello stesso gruppo di risorse del cluster.

Per abilitare Informazioni dettagliate sul contenitore, usare uno dei metodi descritti nella tabella seguente:

| Stato distribuzione | Metodo | Descrizione |
|------------------|--------|-------------|
| Nuovo cluster Kubernetes | [Creare un cluster del servizio AzureKs usando l'interfaccia della riga di comando di Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| È possibile abilitare il monitoraggio per un nuovo cluster del servizio AzureKs creato tramite l'interfaccia della riga di comando di Azure. |
| | [Creare un cluster del servizio Web Disassodato con Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| È possibile abilitare il monitoraggio per un nuovo cluster del servizio AKS creato usando lo strumento open source Terraform. |
| | [Creare un cluster OpenShift usando un modello Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | È possibile abilitare il monitoraggio per un nuovo cluster OpenShift creato usando un modello di Azure Resource Manager preconfigurato. |
| | [Creare un cluster OpenShift usando l'interfaccia della riga di comando di Azure](/cli/azure/openshift#az_openshift_create) | È possibile abilitare il monitoraggio quando si distribuisce un nuovo cluster OpenShift usando l'interfaccia della riga di comando di Azure. |
| Cluster Kubernetes esistente | [Abilitare il monitoraggio di un cluster del servizio AzureKs usando l'interfaccia della riga di comando di Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | È possibile abilitare il monitoraggio per un cluster del servizio AzureKs già distribuito usando l'interfaccia della riga di comando di Azure. |
| |[Abilitare per il cluster del servizio Web Diaks con Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | È possibile abilitare il monitoraggio per un cluster del servizio Web Diaks già distribuito usando lo strumento open source Terraform. |
| | [Abilitare per il cluster del servizio Web Di Monitoraggio di Azure](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| È possibile abilitare il monitoraggio per uno o più cluster del servizio Web Diaks già distribuiti dalla pagina multi-cluster in Monitoraggio di Azure. |
| | [Abilitare dal cluster del servizio Web Disassoce](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| È possibile abilitare il monitoraggio direttamente da un cluster del servizio Web Del servizio Portale di Azure. |
| | [Abilitare per il cluster del servizio Web Diaks usando un modello Azure Resource Manager servizio](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| È possibile abilitare il monitoraggio per un cluster del servizio Web Diaks usando un modello di Azure Resource Manager preconfigurato. |
| | [Abilitare per il cluster Kubernetes ibrido](container-insights-hybrid-setup.md) | È possibile abilitare il monitoraggio per il motore del servizio Kubernetes ospitato in Azure Stack o per un cluster Kubernetes ospitato in locale. |
| | [Abilitare per il cluster Kubernetes abilitato per Arc.](container-insights-enable-arc-enabled-clusters.md) | È possibile abilitare il monitoraggio per i cluster Kubernetes ospitati all'esterno di Azure e abilitati con Azure Arc. |
| | [Abilitare per il cluster OpenShift usando un modello Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | È possibile abilitare il monitoraggio per un cluster OpenShift esistente usando un modello di Azure Resource Manager preconfigurato. |
| | [Abilitare per il cluster OpenShift da Monitoraggio di Azure](container-insights-azure-redhat-setup.md#from-the-azure-portal) | È possibile abilitare il monitoraggio per uno o più cluster OpenShift già distribuiti dalla pagina multicluster in Monitoraggio di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver abilitato il monitoraggio, è possibile iniziare ad analizzare le prestazioni dei cluster Kubernetes ospitati in servizio Azure Kubernetes (AKS), Azure Stack o in un altro ambiente. Per informazioni su come usare Le informazioni dettagliate sui contenitori, vedere [Visualizzare le prestazioni del cluster Kubernetes.](container-insights-analyze.md)
