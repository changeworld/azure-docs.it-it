---
title: Abilitare informazioni dettagliate sul contenitore | Microsoft Docs
description: Questo articolo descrive come abilitare e configurare le informazioni dettagliate sul contenitore in modo che sia possibile comprendere il modo in cui il contenitore sta eseguendo e quali sono stati identificati i problemi relativi alle prestazioni.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 58797221fa3380e4f7533a710e2f8dc658cb676c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708357"
---
# <a name="enable-container-insights"></a>Abilita informazioni dettagliate sul contenitore

Questo articolo offre una panoramica delle opzioni disponibili per la configurazione di informazioni dettagliate sul contenitore per monitorare le prestazioni dei carichi di lavoro distribuiti negli ambienti Kubernetes e ospitati in:

- [Servizio Azure Kubernetes](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versioni 3. x e 4. x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versione 4. x  
- Un [cluster Kubernetes abilitato per Arc](../../azure-arc/kubernetes/overview.md)

È anche possibile monitorare le prestazioni dei carichi di lavoro distribuiti nei cluster Kubernetes autogestiti ospitati in:
- Azure, usando il [motore AKS](https://github.com/Azure/aks-engine)
- [Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) o in locale usando il motore AKS.

È possibile abilitare il contenitore Insights per una nuova distribuzione o per una o più distribuzioni esistenti di Kubernetes usando uno dei metodi supportati seguenti:

- Portale di Azure
- Azure PowerShell
- Interfaccia della riga di comando di Azure
- [Bonifica e AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che siano soddisfatti i requisiti seguenti:

> [!IMPORTANT]
> Log Analytics agente Linux in contenitori (REPLICASET Pod) effettua chiamate API a tutti i nodi Windows nella porta protetta Kubelet (10250) all'interno del cluster per raccogliere le metriche relative alle prestazioni dei nodi e dei contenitori. La porta protetta Kubelet (: 10250) deve essere aperta nella rete virtuale del cluster sia per il funzionamento della raccolta delle metriche in ingresso che in uscita per il nodo Windows e per le prestazioni dei contenitori.
>
> Se si dispone di un cluster Kubernetes con nodi di Windows, esaminare e configurare il gruppo di sicurezza di rete e i criteri di rete per assicurarsi che la porta sicura Kubelet (: 10250) sia aperta per le connessioni in ingresso e in uscita nella rete virtuale del cluster.


- Si dispone di un'area di lavoro Log Analytics.

   Il contenitore Insights supporta un'area di lavoro Log Analytics nelle aree elencate nei [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

   È possibile creare un'area di lavoro quando si Abilita il monitoraggio per il nuovo cluster AKS oppure è possibile consentire all'esperienza di onboarding di creare un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster AKS. 
   
   Se si sceglie di creare manualmente l'area di lavoro, è possibile crearla tramite: 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Il portale di Azure](../logs/quick-create-workspace.md) 
   
   Per un elenco delle coppie di mapping supportate da usare per l'area di lavoro predefinita, vedere [mapping di aree per informazioni dettagliate sul contenitore](container-insights-region-mapping.md).

- L'utente è membro del gruppo *log Analytics Contributor* per abilitare il monitoraggio dei contenitori. Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../logs/manage-access.md).

- Si è un membro del gruppo [ *owner*](../../role-based-access-control/built-in-roles.md#owner) sulla risorsa cluster AKS.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Per visualizzare i dati di monitoraggio, è necessario avere [*log Analytics ruolo Reader*](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro log Analytics, configurato con informazioni dettagliate sul contenitore.

- Le metriche Prometeo non vengono raccolte per impostazione predefinita. Prima di [configurare l'agente](container-insights-prometheus-integration.md) per raccogliere le metriche, è importante esaminare la [documentazione di Prometeo](https://prometheus.io/) per comprendere quali dati possono essere frammentati e quali metodi sono supportati.

## <a name="supported-configurations"></a>Configurazioni supportate

Il contenitore Insights supporta ufficialmente le configurazioni seguenti:

- Ambienti: Azure Red Hat OpenShift, Kubernetes locale e il motore AKS in Azure e Azure Stack. Per ulteriori informazioni, vedere [il motore AKS in Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
- Le versioni di Kubernetes e i criteri di supporto sono identici [a quelli supportati in Azure Kubernetes Service (AKS)](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Requisiti del firewall di rete

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall necessarie per la comunicazione dell'agente in contenitori con informazioni dettagliate sul contenitore. Tutto il traffico di rete dall'agente è in uscita in monitoraggio di Azure.

|Risorsa dell'agente|Porta |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall per Azure Cina 21Vianet:

|Risorsa dell'agente|Porta |Descrizione | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Inserimento di dati |
| `*.oms.opinsights.azure.cn` | 443 | Onboarding di OMS |
| `dc.services.visualstudio.com` | 443 | Per la telemetria dell'agente che usa il cloud pubblico di Azure Application Insights |

La tabella seguente elenca le informazioni di configurazione del proxy e del firewall per il governo degli Stati Uniti di Azure:

|Risorsa dell'agente|Porta |Descrizione | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Inserimento di dati |
| `*.oms.opinsights.azure.us` | 443 | Onboarding di OMS |
| `dc.services.visualstudio.com` | 443 | Per la telemetria dell'agente che usa il cloud pubblico di Azure Application Insights |

## <a name="components"></a>Componenti

La possibilità di monitorare le prestazioni si basa su un agente Log Analytics in contenitori per Linux, sviluppato in modo specifico per informazioni sul contenitore. L'agente specializzato raccoglie i dati su prestazioni ed eventi da tutti i nodi del cluster e viene distribuito e registrato automaticamente con l'area di lavoro Log Analytics specificata durante la distribuzione. 

La versione dell'agente è Microsoft/OMS: ciprod04202018 o versione successiva ed è rappresentata da una data nel formato seguente: *MMGGAAAA*.

>[!NOTE]
>Con la disponibilità generale del supporto di Windows Server per AKS, un cluster AKS con nodi di Windows Server dispone di un agente di anteprima installato come Pod daemonset in ogni singolo nodo di Windows Server per raccogliere i log e inviarli a Log Analytics. Per le metriche delle prestazioni, un nodo Linux distribuito automaticamente nel cluster come parte della distribuzione standard raccoglie e invia i dati a monitoraggio di Azure per conto di tutti i nodi Windows del cluster.

Quando viene rilasciata una nuova versione dell'agente, questa viene aggiornata automaticamente nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes (AKS). Per tenere traccia delle versioni rilasciate, vedere [annunci sulla versione degli agenti](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Se è già stato distribuito un cluster AKS, è stato abilitato il monitoraggio usando l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager fornito, come illustrato più avanti in questo articolo. Non è possibile utilizzare `kubectl` per aggiornare, eliminare, ridistribuire o distribuire l'agente.
>
> Il modello deve essere distribuito nello stesso gruppo di risorse del cluster.

Per abilitare il contenitore Insights, usare uno dei metodi descritti nella tabella seguente:

| Stato distribuzione | Metodo | Descrizione |
|------------------|--------|-------------|
| Nuovo cluster Kubernetes | [Creare un cluster AKS usando l'interfaccia della riga di comando di Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| È possibile abilitare il monitoraggio per un nuovo cluster AKS creato con l'interfaccia della riga di comando di Azure. |
| | [Creare un cluster AKS usando la bonifica](container-insights-enable-new-cluster.md#enable-using-terraform)| È possibile abilitare il monitoraggio per un nuovo cluster AKS creato tramite lo strumento open source bonifica. |
| | [Creare un cluster OpenShift usando un modello di Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | È possibile abilitare il monitoraggio per un nuovo cluster OpenShift creato usando un modello di Azure Resource Manager preconfigurato. |
| | [Creare un cluster OpenShift usando l'interfaccia della riga di comando di Azure](/cli/azure/openshift#az-openshift-create) | È possibile abilitare il monitoraggio quando si distribuisce un nuovo cluster OpenShift usando l'interfaccia della riga di comando di Azure. |
| Cluster Kubernetes esistente | [Abilitare il monitoraggio di un cluster AKS usando l'interfaccia della riga di comando di Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | È possibile abilitare il monitoraggio per un cluster AKS già distribuito usando l'interfaccia della riga di comando di Azure. |
| |[Abilitare per il cluster AKS usando la bonifica](container-insights-enable-existing-clusters.md#enable-using-terraform) | È possibile abilitare il monitoraggio per un cluster AKS già distribuito usando lo strumento open source bonifica. |
| | [Abilitare per il cluster AKS da monitoraggio di Azure](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| È possibile abilitare il monitoraggio per uno o più cluster AKS già distribuiti dalla pagina multicluster in monitoraggio di Azure. |
| | [Abilita dal cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| È possibile abilitare il monitoraggio direttamente da un cluster AKS nel portale di Azure. |
| | [Abilitare per il cluster AKS usando un modello di Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| È possibile abilitare il monitoraggio per un cluster AKS usando un modello di Azure Resource Manager preconfigurato. |
| | [Abilita per il cluster Kubernetes ibrido](container-insights-hybrid-setup.md) | È possibile abilitare il monitoraggio per il motore AKS ospitato in Azure Stack o per un cluster Kubernetes ospitato in locale. |
| | [Abilitare per il cluster Kubernetes abilitato per Arc](container-insights-enable-arc-enabled-clusters.md). | È possibile abilitare il monitoraggio per i cluster Kubernetes ospitati all'esterno di Azure e abilitati con Azure Arc. |
| | [Abilitare per il cluster OpenShift usando un modello di Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | È possibile abilitare il monitoraggio per un cluster OpenShift esistente usando un modello di Azure Resource Manager preconfigurato. |
| | [Abilitare per il cluster OpenShift da monitoraggio di Azure](container-insights-azure-redhat-setup.md#from-the-azure-portal) | È possibile abilitare il monitoraggio per uno o più cluster OpenShift già distribuiti dalla pagina multicluster in monitoraggio di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato abilitato il monitoraggio, è possibile iniziare ad analizzare le prestazioni dei cluster Kubernetes ospitati nel servizio Azure Kubernetes (AKS), Azure Stack o in un altro ambiente. Per informazioni su come usare il contenitore Insights, vedere [visualizzare le prestazioni del cluster Kubernetes](container-insights-analyze.md).