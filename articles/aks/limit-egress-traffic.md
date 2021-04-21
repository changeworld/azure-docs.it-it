---
title: Limitare il traffico in uscita nel servizio Azure Kubernetes
description: Informazioni sulle porte e sugli indirizzi necessari per controllare il traffico in uscita nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 39c0877b96a3e8c6c716c1ab9ae7ba11575990a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765594"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controllare il traffico in uscita per i nodi del cluster nel servizio Azure Kubernetes

Questo articolo fornisce i dettagli necessari che consentono di proteggere il traffico in uscita dal servizio Servizio Azure Kubernetes (AKS). Contiene i requisiti del cluster per una distribuzione del servizio Web Di base del servizio Web Di seguito sono riportati i requisiti aggiuntivi per i componenti aggiuntivi e le funzionalità facoltativi. [Alla fine verrà fornito un esempio di come configurare questi requisiti con Firewall di Azure](#restrict-egress-traffic-using-azure-firewall). Tuttavia, è possibile applicare queste informazioni a qualsiasi metodo di restrizione in uscita o appliance.

## <a name="background"></a>Sfondo

I cluster del servizio Web Diaks vengono distribuiti in una rete virtuale. Questa rete può essere gestita (creata dal servizio Servizio Web di controllo di accesso) o personalizzata (preconfigurato in precedenza dall'utente). In entrambi i casi, il cluster **ha** dipendenze in uscita da servizi esterni a tale rete virtuale (il servizio non ha dipendenze in ingresso).

A scopi di gestione e operativi, i nodi in un cluster del servizio Azure Kubernetes devono poter accedere a porte e nomi di dominio completi (FQDN) specifici. Questi endpoint sono necessari per la comunicazione dei nodi con il server API o per scaricare e installare i componenti principali del cluster Kubernetes e gli aggiornamenti della sicurezza dei nodi. Ad esempio, il cluster deve eseguire il pull delle immagini del contenitore di sistema di base da Microsoft Container Registry (MCR).

Le dipendenze in uscita del servizio Web AKS sono quasi completamente definite con nomi fqdn, che non hanno indirizzi statici dietro di esse. La mancanza di indirizzi statici significa che i gruppi di sicurezza di rete non possono essere usati per bloccare il traffico in uscita da un cluster del servizio Web Diaks. 

Per impostazione predefinita, i cluster del servizio Azure Kubernetes hanno accesso a Internet in uscita senza restrizioni. Questo livello di accesso alla rete consente a nodi e servizi in esecuzione di accedere alle risorse esterne in base alle esigenze. Se si vuole limitare il traffico in uscita, è necessario rendere accessibile un numero limitato di porte e indirizzi per mantenere l'integrità delle attività di manutenzione del cluster. La soluzione più semplice per proteggere gli indirizzi in uscita consiste nell'usare un dispositivo firewall in grado di controllare il traffico in uscita in base ai nomi di dominio. Firewall di Azure, ad esempio, può limitare il traffico HTTP e HTTPS in uscita in base al nome di dominio completo della destinazione. È anche possibile configurare il firewall preferito e le regole di sicurezza per consentire le porte e gli indirizzi necessari.

> [!IMPORTANT]
> Questo documento illustra solo come bloccare il traffico in uscita dalla subnet del servizio Azure Kubernetes. Per impostazione predefinita, il servizio AKS non ha requisiti per l'ingresso.  Il **blocco del traffico interno della subnet** tramite gruppi di sicurezza di rete (NSG) e firewall non è supportato. Per controllare e bloccare il traffico all'interno del cluster, usare [**_Criteri di rete._**][network-policy]

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Regole di rete in uscita obbligatorie e FQDN per i cluster del servizio Web AKS

Le regole di rete e FQDN/applicazione seguenti sono necessarie per un cluster del servizio Web Disassodato. È possibile usarle se si vuole configurare una soluzione diversa da Firewall di Azure.

* Le dipendenze degli indirizzi IP sono per il traffico non HTTP/S (traffico sia TCP che UDP)
* Gli endpoint HTTP/HTTPS con nome di dominio completo possono essere posizionati nel dispositivo firewall.
* Gli endpoint HTTP/HTTPS con caratteri jolly sono dipendenze che possono variare in base al cluster del servizio Web del servizio Web di Servizio Controllo di accesso in base a diversi qualificatori.
* Il servizio AKS usa un controller di ammissione per inserire il nome di dominio completo come variabile di ambiente per tutte le distribuzioni in kube-system e gatekeeper-system, in modo che tutte le comunicazioni di sistema tra i nodi e il server API usino l'FQDN del server API e non l'IP del server API. 
* Se si dispone di un'app o di una soluzione  che deve comunicare con il server API, è necessario aggiungere una regola di rete aggiuntiva per consentire la comunicazione TCP alla porta *443 dell'INDIRIZZO IP* del server API.
* In rari casi, se è presente un'operazione di manutenzione, l'INDIRIZZO IP del server API potrebbe cambiare. Le operazioni di manutenzione pianificata che possono modificare l'IP del server API vengono sempre comunicate in anticipo.


### <a name="azure-global-required-network-rules"></a>Regole di rete richieste globali di Azure

Le regole di rete necessarie e le dipendenze degli indirizzi IP sono:

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [CIDR regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Per la comunicazione protetta con tunneling tra i nodi e il piano di controllo. Questa operazione non è necessaria per [i cluster privati](private-clusters.md)|
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [CIDR regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Per la comunicazione protetta con tunneling tra i nodi e il piano di controllo. Questa operazione non è necessaria per [i cluster privati](private-clusters.md) |
| **`*:123`** o **`ntp.ubuntu.com:123`** (se si usano Firewall di Azure di rete)  | UDP      | 123     | Obbligatorio per la sincronizzazione dell'ora NTP (Network Time Protocol) nei nodi Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se si usano server DNS personalizzati, è necessario assicurarsi che siano accessibili dai nodi del cluster. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Obbligatorio se si eseguono pod/distribuzioni che accedono al server API, tali pod/distribuzioni userebbero l'INDIRIZZO IP dell'API. Questa operazione non è necessaria per [i cluster privati](private-clusters.md)  |

### <a name="azure-global-required-fqdn--application-rules"></a>Fqdn/regole dell'applicazione richiesti a livello globale di Azure 

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

| FQDN di destinazione                 | Porta            | Uso      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Obbligatorio per la comunicazione < server API > nodo. Sostituire *\<location\>* con l'area in cui è distribuito il cluster del servizio Web Diaks. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Obbligatorio per accedere alle immagini in Microsoft Container Registry (MCR). Questo registro contiene immagini/grafici di prima parte ,ad esempio coreDNS e così via. Queste immagini sono necessarie per la creazione e il funzionamento corretti del cluster, incluse le operazioni di scalabilità e aggiornamento.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Obbligatorio per l'archiviazione MCR supportata dalla rete per la distribuzione di contenuti (CDN) di Azure. |
| **`management.azure.com`**       | **`HTTPS:443`** | Obbligatorio per le operazioni Kubernetes nell'API di Azure. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Obbligatorio per Azure Active Directory autenticazione. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Questo indirizzo è il repository di pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Questo indirizzo è per il repository necessario per scaricare e installare i file binari necessari, ad esempio kubenet e Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure China (21Vianet) regole di rete necessarie

Le regole di rete necessarie e le dipendenze degli indirizzi IP sono:

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Or* <br/> [CIDR regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Per la comunicazione protetta con tunneling tra i nodi e il piano di controllo. |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [CIDR regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Per la comunicazione protetta con tunneling tra i nodi e il piano di controllo. |
| **`*:22`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Or* <br/> [CIDR regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Or* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | Per la comunicazione protetta con tunneling tra i nodi e il piano di controllo. |
| **`*:123`** o **`ntp.ubuntu.com:123`** (se si usano Firewall di Azure di rete)  | UDP      | 123     | Obbligatorio per la sincronizzazione dell'ora NTP (Network Time Protocol) nei nodi Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se si usano server DNS personalizzati, è necessario assicurarsi che siano accessibili dai nodi del cluster. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Obbligatorio se si eseguono pod/distribuzioni che accedono al server API, tali pod/distribuzioni userebbero l'INDIRIZZO IP dell'API.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China (21Vianet) fqdn/regole dell'applicazione necessarie

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

| FQDN di destinazione                               | Porta            | Uso      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Obbligatorio per la comunicazione < server API > nodo. Sostituire *\<location\>* con l'area in cui viene distribuito il cluster del servizio Servizio Web Disatteso. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Obbligatorio per la comunicazione < server API > nodo. Sostituire *\<location\>* con l'area in cui viene distribuito il cluster del servizio Servizio Web Disatteso. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Obbligatorio per accedere alle immagini in Microsoft Container Registry (MCR). Questo registro contiene immagini/grafici di prima parte,ad esempio coreDNS e così via. Queste immagini sono necessarie per la creazione e il funzionamento corretti del cluster, incluse le operazioni di scalabilità e aggiornamento. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Obbligatorio per l'archiviazione MCR supportata dalla rete per la distribuzione di contenuti (CDN) di Azure. |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Obbligatorio per le operazioni Kubernetes nell'API di Azure. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Obbligatorio per Azure Active Directory autenticazione. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Questo indirizzo è il repository di pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Questo indirizzo è per il repository necessario per scaricare e installare i file binari necessari, ad esempio kubenet e Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Regole di rete necessarie per Azure US Government

Le regole di rete necessarie e le dipendenze degli indirizzi IP sono:

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [CIDR regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Per la comunicazione protetta con tunneling tra i nodi e il piano di controllo. |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [CIDR regionali](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Per la comunicazione protetta con tunneling tra i nodi e il piano di controllo. |
| **`*:123`** o **`ntp.ubuntu.com:123`** (se si usano regole Firewall di Azure di rete)  | UDP      | 123     | Obbligatorio per la sincronizzazione dell'ora NTP (Network Time Protocol) nei nodi Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se si usano server DNS personalizzati, è necessario assicurarsi che siano accessibili dai nodi del cluster. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Obbligatorio se si eseguono pod/distribuzioni che accedono al server API, tali pod/distribuzioni userebbero l'INDIRIZZO IP dell'API.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Fqdn/regole dell'applicazione obbligatorio per Azure US Government 

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

| FQDN di destinazione                                        | Porta            | Uso      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Obbligatorio per la comunicazione < server API > nodo. Sostituire *\<location\>* con l'area in cui è distribuito il cluster del servizio Web Diaks.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Obbligatorio per accedere alle immagini in Microsoft Container Registry (MCR). Questo registro contiene immagini/grafici di prima parte,ad esempio coreDNS e così via. Queste immagini sono necessarie per la creazione e il funzionamento corretti del cluster, incluse le operazioni di scalabilità e aggiornamento. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Obbligatorio per l'archiviazione MCR supportata dalla rete per la distribuzione di contenuti (CDN) di Azure. |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Obbligatorio per le operazioni Kubernetes nell'API di Azure. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Obbligatorio per Azure Active Directory autenticazione. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Questo indirizzo è il repository di pacchetti Microsoft usato per le operazioni *apt-get* memorizzate nella cache.  I pacchetti di esempio includono Moby, PowerShell e l'interfaccia della riga di comando di Azure. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Questo indirizzo è per il repository necessario per installare i file binari richiesti, come Kubenet e Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Fqdn/regole dell'applicazione facoltative consigliate per i cluster del servizio Web Disatteso

Le regole fqdn/applicazione seguenti sono facoltative, ma consigliate per i cluster del servizio Web Diaks:

| FQDN di destinazione                                                               | Porta          | Uso      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Questo indirizzo consente ai nodi del cluster Linux di scaricare le patch di sicurezza e gli aggiornamenti necessari. |

Se si sceglie di bloccare o non consentire questi FQDN, i nodi riceveranno gli aggiornamenti del sistema operativo solo quando si esegue un aggiornamento dell'immagine del nodo o [](node-image-upgrade.md) [dell'aggiornamento del cluster.](upgrade-cluster.md)

## <a name="gpu-enabled-aks-clusters"></a>Cluster del servizio AKS abilitati per GPU

### <a name="required-fqdn--application-rules"></a>Fqdn/regole dell'applicazione obbligatorie

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con la GPU abilitata:

| FQDN di destinazione                        | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Questo indirizzo viene usato per l'installazione e il funzionamento corretti dei driver nei nodi basati su GPU. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Questo indirizzo viene usato per l'installazione e il funzionamento corretti dei driver nei nodi basati su GPU. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Questo indirizzo viene usato per l'installazione e il funzionamento corretti dei driver nei nodi basati su GPU. |

## <a name="windows-server-based-node-pools"></a>Pool di nodi basati su Windows Server 

### <a name="required-fqdn--application-rules"></a>Fqdn/regole dell'applicazione obbligatorie

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per l'uso di pool di nodi basati su Windows Server:

| FQDN di destinazione                                                           | Porta      | Uso      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Per installare file binari correlati a Windows |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Per installare file binari correlati a Windows |

## <a name="aks-addons-and-integrations"></a>Componenti aggiuntivi e integrazioni del servizio AKS

### <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori

Esistono due opzioni per fornire l'accesso Monitoraggio di Azure per i contenitori. È possibile consentire Monitoraggio di Azure [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)  o fornire l'accesso al nome di dominio completo o alle regole dell'applicazione necessarie.

#### <a name="required-network-rules"></a>Regole di rete necessarie

Le regole delle applicazioni e FQDN seguenti sono obbligatorie:

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Questo endpoint viene usato per inviare i dati e i log delle metriche Monitoraggio di Azure e Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Fqdn/regole dell'applicazione obbligatorie

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con Monitoraggio di Azure per i contenitori abilitato:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Questo endpoint viene usato per le metriche e il monitoraggio dei dati di telemetria Monitoraggio di Azure. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Questo endpoint viene usato dal Monitoraggio di Azure per l'inserimento di dati di log analytics. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Questo endpoint viene usato da omsagent, che viene usato per autenticare il servizio log analytics. |
| *.monitoring.azure.com | **`HTTPS:443`** | Questo endpoint viene usato per inviare i dati delle metriche Monitoraggio di Azure. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Aggiornare la configurazione del firewall o della sicurezza per consentire il traffico di rete da e verso tutti i seguenti FQDN e Azure Dev Spaces [di infrastruttura.][dev-spaces-service-tags]

#### <a name="required-network-rules"></a>Regole di rete necessarie

| Endpoint di destinazione                                                             | Protocollo | Porta    | Uso  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Questo endpoint viene usato per inviare i dati e i log delle metriche Monitoraggio di Azure e Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Fqdn/regole dell'applicazione obbligatorie 

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con Azure Dev Spaces abilitato:

| Nome di dominio completo                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull di immagini di Alpine Linux e di altre immagini di Azure Dev Spaces |
| `gcr.io` | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull di immagini Helm/Tiller |
| `storage.googleapis.com` | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull di immagini Helm/Tiller |


### <a name="azure-policy"></a>Criteri di Azure

#### <a name="required-fqdn--application-rules"></a>Fqdn/regole dell'applicazione obbligatorie 

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con il servizio Criteri di Azure abilitato.

| Nome di dominio completo                                          | Porta      | Uso      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull dei criteri Kubernetes e per segnalare lo stato di conformità del cluster al servizio criteri. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull degli elementi gatekeeper dei criteri predefiniti. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Questo indirizzo viene usato per il corretto funzionamento di Criteri di Azure  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull dei criteri predefiniti da GitHub per assicurare il corretto funzionamento di Criteri di Azure |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Componente aggiuntivo di Criteri di Azure che invia i dati di telemetria all'endpoint di Application Insights. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China (21Vianet) fqdn/regole dell'applicazione obbligatorie 

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con il servizio Criteri di Azure abilitato.

| Nome di dominio completo                                          | Porta      | Uso      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull dei criteri Kubernetes e per segnalare lo stato di conformità del cluster al servizio criteri. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull degli elementi gatekeeper dei criteri predefiniti. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Regole dell'applicazione/FQDN obbligatorio per il governo degli Stati Uniti di Azure

Le regole delle applicazioni e FQDN seguenti sono obbligatorie per i cluster del servizio Azure Kubernetes con il servizio Criteri di Azure abilitato.

| Nome di dominio completo                                          | Porta      | Uso      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull dei criteri Kubernetes e per segnalare lo stato di conformità del cluster al servizio criteri. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Questo indirizzo viene usato per eseguire il pull degli artefatti gatekeeper dei criteri predefiniti. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Limitare il traffico in uscita tramite firewall di Azure

Firewall di Azure fornisce un tag FQDN servizio Azure Kubernetes ( `AzureKubernetesService` ) per semplificare questa configurazione. 

> [!NOTE]
> Il tag FQDN contiene tutti i nomi fqdn elencati in precedenza e viene mantenuto aggiornato automaticamente.
>
> È consigliabile avere almeno 20 IP front-end nel Firewall di Azure per gli scenari di produzione per evitare problemi di esaurimento delle porte SNAT.

Di seguito è riportata un'architettura di esempio della distribuzione:

![Topologia bloccata](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* In ingresso pubblico è forzato il flusso attraverso i filtri del firewall
  * I nodi dell'agente del servizio Web Diaks sono isolati in una subnet dedicata.
  * [Firewall di Azure](../firewall/overview.md) viene distribuito nella propria subnet.
  * Una regola DNAT converte l'indirizzo IP pubblico del firewall nell'indirizzo IP front-end di LB.
* Le richieste in uscita iniziano dai nodi agente Firewall di Azure ip interno usando [una route definita dall'utente](egress-outboundtype.md)
  * Le richieste provenienti dai nodi agente del servizio Azure Kubernetes seguono una route definita dall'utente che è stata inserita nella subnet in cui è stato distribuito il cluster del servizio Azure Kubernetes.
  * Uscite di Firewall di Azure dalla rete virtuale da un front-end IP pubblico
  * L'accesso alla rete Internet pubblica o ad altri servizi di Azure viene trasmesso da e verso l'indirizzo IP front-end del firewall
  * Facoltativamente, l'accesso al piano di controllo del servizio AKS è protetto dagli intervalli IP autorizzati del [server API,](./api-server-authorized-ip-ranges.md)che include l'indirizzo IP front-end pubblico del firewall.
* Traffico interno
  * Facoltativamente, in alternativa o in aggiunta [a](load-balancer-standard.md) un Load Balancer pubblico, è possibile usare un [Load Balancer](internal-lb.md) interno per il traffico interno, che è possibile isolare anche nella propria subnet.


I passaggi seguenti usano il tag FQDN di Firewall di Azure per limitare il traffico in uscita dal cluster del servizio Web Diaks e forniscono un esempio di come configurare il traffico in ingresso pubblico tramite `AzureKubernetesService` il firewall.


### <a name="set-configuration-via-environment-variables"></a>Impostare la configurazione tramite variabili di ambiente

Definire un set di variabili di ambiente da usare durante la creazione di risorse.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Creare una rete virtuale con più subnet

Effettuare il provisioning di una rete virtuale con due subnet separate, una per il cluster e una per il firewall. Facoltativamente, è anche possibile crearne uno per l'ingresso interno del servizio.

![Topologia di rete vuota](media/limit-egress-traffic/empty-network.png)

Creare un gruppo di risorse che conterrà tutte le risorse.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Creare una rete virtuale con due subnet per ospitare il cluster del servizio Web Disassociato di Firewall di Azure. Ognuna avrà una propria subnet. Si inizierà con la rete del servizio Azure Kubernetes.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Creare e configurare un'Firewall di Azure con una UDR

È necessario configurare le regole in ingresso e in uscita di Firewall di Azure. Lo scopo principale del firewall è consentire alle organizzazioni di configurare regole granulari per il traffico in ingresso e in uscita all'interno e all'esterno del cluster del servizio AKS.

![Firewall e route definita dall'utente](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Se il cluster o l'applicazione crea un numero elevato di connessioni in uscita indirizzate allo stesso subset o a un subset ridotto di destinazioni, potrebbero essere necessari più indirizzi IP front-end del firewall per evitare il numero massimo di porte per ip front-end.
> Per altre informazioni su come creare un firewall di Azure con più indirizzi IP, vedere [ **qui**](../firewall/quick-create-multiple-ip-template.md)

Creare una risorsa IP pubblico dello SKU standard che verrà usata come indirizzo Firewall di Azure front-end.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registrare l'estensione dell'interfaccia della riga di comando di anteprima per creare un'istanza di Firewall di Azure.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
È ora possibile assegnare l'indirizzo IP creato in precedenza al front-end del firewall.

> [!NOTE]
> La configurazione dell'indirizzo IP pubblico per Firewall di Azure potrebbe richiedere alcuni minuti.
> Per sfruttare il nome fqdn nelle regole di rete è necessario che il proxy DNS sia abilitato. Se abilitato, il firewall rimane in ascolto sulla porta 53 e inoltra le richieste DNS al server DNS specificato in precedenza. In questo modo il firewall sarà in grado di convertire automaticamente il nome di dominio completo.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Dopo aver eseguito il comando precedente, salvare l'indirizzo IP del front-end del firewall per eseguire la configurazione in un secondo momento.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Se si usa l'accesso sicuro al server DELL'API del servizio Web Diaks con intervalli di indirizzi [IP](./api-server-authorized-ip-ranges.md)autorizzati, è necessario aggiungere l'indirizzo IP pubblico del firewall nell'intervallo di indirizzi IP autorizzati.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Creare una route definita dall'utente con un hop a Firewall di Azure

Azure effettua il routing automatico del traffico tra subnet di Azure, reti virtuali e reti locali. Per modificare il routing predefinito di Azure è necessario creare una tabella di route.

Creare una tabella di route vuota da associare a una determinata subnet. Firewall di Azure creato in precedenza verrà impostato come hop successivo nella tabella di route. A ogni subnet può essere associata una o nessuna tabella di route.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Vedere la [documentazione relativa alle tabelle di route della rete virtuale](../virtual-network/virtual-networks-udr-overview.md#user-defined) per informazioni su come eseguire l'override delle route di sistema predefinite di Azure o su come aggiungere altre route alla tabella di route di una subnet.

### <a name="adding-firewall-rules"></a>Aggiunta di regole del firewall

Di seguito sono riportate tre regole di rete che è possibile usare per configurare nel firewall. Potrebbe essere necessario adattare queste regole in base alla distribuzione. La prima regola consente l'accesso alla porta 9000 tramite TCP. La seconda regola consente l'accesso alle porte 1194 e 123 tramite UDP (se si esegue la distribuzione in Azure China (21Vianet), potrebbero essere necessarie [più .](#azure-china-21vianet-required-network-rules) Entrambe queste regole consentiranno solo il traffico destinato al CIDR dell'area di Azure in uso, in questo caso Stati Uniti orientali. Infine, si aggiungerà una terza regola di rete che apre la porta 123 al nome di dominio completo tramite UDP (l'aggiunta di un nome di dominio completo come regola di rete è una delle funzionalità specifiche di Firewall di Azure e sarà necessario adattarla quando si usano `ntp.ubuntu.com` le proprie opzioni).

Dopo aver impostato le regole di rete, si aggiungerà anche una regola dell'applicazione usando che copre tutti i nomi fqdn necessari accessibili tramite la porta `AzureKubernetesService` TCP 443 e la porta 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Per altre informazioni sul servizio Firewall di Azure, vedere la [documentazione di Firewall di Azure](../firewall/overview.md).

### <a name="associate-the-route-table-to-aks"></a>Associare la tabella di route al servizio Azure Kubernetes

Per associare il cluster al firewall, la subnet dedicata per la subnet del cluster deve fare riferimento alla tabella di route creata in precedenza. È possibile eseguire l'associazione inviando un comando alla rete virtuale che contiene sia il cluster che il firewall per aggiornare la tabella di route della subnet del cluster.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Distribuire il servizio Azure Kubernetes con il tipo in uscita con route definita dall'utente nella rete esistente

È ora possibile distribuire un cluster del servizio Web Disassoce nella rete virtuale esistente. Si userà [ `userDefinedRouting` ](egress-outboundtype.md)anche il tipo in uscita , questa funzionalità garantisce che qualsiasi traffico in uscita verrà forzato attraverso il firewall e non saranno presenti altri percorsi di uscita (per impostazione predefinita è possibile usare il tipo Load Balancer in uscita).

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Creare un'entità servizio con accesso per effettuare il provisioning all'interno della rete virtuale esistente

Un'identità del cluster (identità gestita o entità servizio) viene usata dal servizio Web Del servizio Web per creare le risorse del cluster. Un'entità servizio che viene passata in fase di creazione viene usata per creare risorse del servizio AzureKs sottostanti, ad esempio risorse di archiviazione, indirizzi IP e servizi di bilanciamento del carico usati dal servizio Azure Service Pack. È anche possibile usare un'identità [gestita.](use-managed-identity.md) Se non sono state concesse le autorizzazioni appropriate riportate di seguito, non sarà possibile effettuare il provisioning del cluster del servizio Web Del servizio AKS.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

A questo punto, sostituire `APPID` e `PASSWORD` nell'esempio seguente con i valori di ID app e password dell'entità servizio generati automaticamente dall'output del comando precedente. Verrà fatto riferimento all'ID risorsa della rete virtuale per concedere le autorizzazioni all'entità servizio in modo che il servizio AKS possa distribuirla.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

È possibile controllare le autorizzazioni dettagliate necessarie [qui.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)

> [!NOTE]
> Se si usa il plug-in di rete kubenet, è necessario assegnare all'entità servizio o all'identità gestita le autorizzazioni per la tabella di route creata in precedenza, poiché kubenet richiede una tabella di route per aggiungere regole di routing neccesary. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Distribuire il servizio Azure Kubernetes

Infine, è possibile distribuire il cluster del servizio AKS nella subnet esistente dedicata per il cluster. Per definire la subnet di destinazione in cui eseguire la distribuzione, si usa la variabile di ambiente `$SUBNETID`. La variabile `$SUBNETID` non è stata definita nei passaggi precedenti. Per impostare il valore per l'ID subnet, è possibile usare il comando seguente:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Si definirà il tipo in uscita per usare la replica definita dall'utente già esistente nella subnet. Questa configurazione consentirà al servizio AzureKs di ignorare la configurazione e il provisioning IP per il servizio di bilanciamento del carico.

> [!IMPORTANT]
> Per altre informazioni sul tipo di UDR in uscita, incluse le limitazioni, vedere [**Egress outbound type UDR**](egress-outboundtype.md#limitations).


> [!TIP]
> È possibile aggiungere funzionalità aggiuntive alla distribuzione del cluster, ad esempio [**il cluster privato**](private-clusters.md). 
>
> È possibile aggiungere la funzionalità servizio AKS per gli intervalli IP autorizzati del [**server API**](api-server-authorized-ip-ranges.md) per limitare l'accesso del server API solo all'endpoint pubblico del firewall. La funzionalità degli intervalli IP autorizzati è denotata nel diagramma come facoltativa. Quando si abilita la funzionalità degli intervalli IP autorizzati per limitare l'accesso al server API, gli strumenti di sviluppo devono usare un JumpBox dalla rete virtuale del firewall oppure è necessario aggiungere tutti gli endpoint sviluppatore all'intervallo di indirizzi IP autorizzati.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>Consentire agli sviluppatori di accedere al server API

Se nel passaggio precedente sono stati usati intervalli IP autorizzati per il cluster, è necessario aggiungere gli indirizzi IP degli strumenti di sviluppo all'elenco di intervalli IP approvati del cluster del servizio Gateway Gateway per accedere al server API da questo punto. Un'altra opzione consiste nel configurare un JumpBox con gli strumenti necessari all'interno di una subnet separata nella rete virtuale di Firewall.

Aggiungere un altro indirizzo IP agli intervalli approvati con il comando seguente

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Usare il comando [az servizio Kubernetes get-credentials][az-aks-get-credentials] per configurare la connessione al `kubectl` cluster Kubernetes appena creato. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Distribuire un servizio pubblico
È ora possibile iniziare a esporre i servizi e distribuire le applicazioni in questo cluster. In questo esempio verrà esposto un servizio pubblico, ma è anche possibile scegliere di esporre un servizio interno tramite il servizio [di bilanciamento del carico interno.](internal-lb.md)

![DNAT del servizio pubblico](media/limit-egress-traffic/aks-create-svc.png)

Distribuire l'applicazione Azure Voting copiando il codice YAML seguente in un file denominato `example.yaml`.

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

Per distribuire il servizio, eseguire:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Aggiungere una regola DNAT a Firewall di Azure

> [!IMPORTANT]
> Quando si usa Firewall di Azure per limitare il traffico in uscita e creare una route definita dall'utente (UDR) per forzare tutto il traffico in uscita, assicurarsi di creare una regola DNAT appropriata nel firewall per consentire correttamente il traffico in ingresso. L'uso del servizio Firewall di Azure con una route definita dall'utente interrompe la configurazione in ingresso a causa del routing asimmetrico. Questo problema si verifica se la subnet del servizio Azure Kubernetes ha una route predefinita verso l'indirizzo IP privato del firewall, ma si sta usando un servizio di bilanciamento del carico pubblico in ingresso o del servizio Kubernetes di tipo LoadBalancer. In questo caso, il traffico del servizio di bilanciamento del carico in ingresso viene ricevuto tramite l'indirizzo IP pubblico, ma il percorso di ritorno passa attraverso l'indirizzo IP privato del firewall. Poiché il firewall è con stato, elimina il pacchetto di ritorno perché il firewall non è a conoscenza del fatto che è stata stabilita una sessione. Per informazioni su come integrare Firewall di Azure con il servizio di bilanciamento del carico in ingresso o del servizio, vedere [Integrare Firewall di Azure con Azure Load Balancer Standard](../firewall/integrate-lb.md).


Per configurare la connettività in ingresso, è necessario scrivere una regola DNAT in Firewall di Azure. Per testare la connettività al cluster, viene definita una regola per l'indirizzo IP pubblico front-end del firewall da instradare all'indirizzo IP interno esposto dal servizio interno.

L'indirizzo di destinazione può essere personalizzato perché è la porta del firewall a cui accedere. L'indirizzo convertito deve essere l'indirizzo IP del bilanciamento del carico interno. La porta convertita deve essere la porta esposta per il servizio Kubernetes.

È necessario specificare l'indirizzo IP interno assegnato al servizio di bilanciamento del carico creato dal servizio Kubernetes. Per recuperare l'indirizzo, eseguire:

```bash
kubectl get services
```

L'indirizzo IP necessario sarà indicato nella colonna EXTERNAL-IP e sarà simile a quello illustrato di seguito.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Ottenere l'indirizzo IP del servizio eseguendo:
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Aggiungere la regola NAT eseguendo:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Convalidare la connettività

In un browser accedere all'indirizzo IP front-end di Firewall di Azure per convalidare la connettività.

Dovrebbe essere visualizzata l'app di voto del servizio Diaks. In questo esempio l'indirizzo IP pubblico del firewall era `52.253.228.132` .


![Screenshot che mostra l'app di voto A K S con i pulsanti Per gatti, Cani e Reimposta e totali.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse di Azure, eliminare il gruppo di risorse del servizio Azure Kubernetes.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati appresi quali porte e indirizzi consentire se si vuole limitare il traffico in uscita per il cluster. Si è anche visto come proteggere il traffico in uscita usando Firewall di Azure. 

Se necessario, è possibile generalizzare i passaggi precedenti per inoltrare il traffico alla soluzione di uscita preferita, seguendo la documentazione [relativa al tipo in `userDefinedRoute` uscita](egress-outboundtype.md).

Se si vuole limitare il modo in cui i pod comunicano tra se stessi e East-West del traffico all'interno del cluster, vedere Proteggere il traffico tra i pod usando i criteri di rete nel servizio Contenitore [di AKS.][network-policy]

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
