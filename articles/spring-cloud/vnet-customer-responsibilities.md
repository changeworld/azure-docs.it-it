---
title: Responsabilità dei clienti che eseguono Azure Spring Cloud nella rete virtuale
description: Questo articolo descrive le responsabilità dei clienti che eseguono Azure Spring Cloud nella rete virtuale.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java
ms.openlocfilehash: a6b444092ec4e3588564a3f902b49c4ed3dc5fe5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376784"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Responsabilità del cliente per l'Azure Spring Cloud nella rete virtuale
Questo documento include specifiche per l'uso Azure Spring Cloud in una rete virtuale.

Quando Azure Spring Cloud viene distribuito nella rete virtuale, ha dipendenze in uscita dai servizi all'esterno della rete virtuale. Per motivi di gestione e operativi, Azure Spring Cloud accedere a determinate porte e nomi di dominio completi (FQDN). Questi endpoint sono necessari per comunicare con il piano Azure Spring Cloud di gestione e per scaricare e installare i componenti principali del cluster Kubernetes e gli aggiornamenti della sicurezza.

Per impostazione predefinita, Azure Spring Cloud accesso Internet in uscita (in uscita) senza restrizioni. Questo livello di accesso alla rete consente alle applicazioni eseguite di accedere alle risorse esterne in base alle esigenze. Se si vuole limitare il traffico in uscita, un numero limitato di porte e indirizzi deve essere accessibile per le attività di manutenzione. La soluzione più semplice per proteggere gli indirizzi in uscita è l'uso di un dispositivo firewall in grado di controllare il traffico in uscita in base ai nomi di dominio. Firewall di Azure, ad esempio, può limitare il traffico HTTP e HTTPS in uscita in base al nome di dominio completo della destinazione. È anche possibile configurare il firewall preferito e le regole di sicurezza per consentire le porte e gli indirizzi necessari.

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud requisiti delle risorse 

Di seguito è riportato un elenco dei requisiti delle risorse per Azure Spring Cloud servizi. Come requisito generale, non è consigliabile modificare i gruppi di risorse creati Azure Spring Cloud e le risorse di rete sottostanti.
- Non modificare i gruppi di risorse creati e di proprietà Azure Spring Cloud.
  - Per impostazione predefinita, questi gruppi di risorse sono denominati ap-svc-rt_[SERVICE-INSTANCE-NAME]_[REGION]* e ap_[SERVICE-INSTANCE-NAME]_[REGION]*.
- Non modificare le subnet usate da Azure Spring Cloud.
- Non creare più istanze del Azure Spring Cloud nella stessa subnet.
- Quando si usa un firewall per controllare il *traffico,* non bloccare il traffico in uscita seguente Azure Spring Cloud componenti che operano, gestiscono e supportano l'istanza del servizio.

## <a name="azure-spring-cloud-network-requirements"></a>Azure Spring Cloud di rete

  | Endpoint di destinazione | Porta | Uso | Nota |
  |------|------|------|------|
  | *:1194 *o* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:1194 | UDP:1194 | Gestione del cluster Kubernetes sottostante. | |
  | *:443 *o* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:443 | TCP:443 | Azure Spring Cloud Service Management. | Le informazioni dell'istanza del servizio "requiredTraffics" potrebbero essere note nel payload della risorsa, nella sezione "networkProfile". |
  | *:9000 *o* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:9000 | TCP:9000 | Gestione del cluster Kubernetes sottostante. |
  | *:123 *o* ntp.ubuntu.com:123 | UDP:123 | Sincronizzazione dell'ora NTP nei nodi Linux. | |
  | *.azure.io:443 Or  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureContainerRegistry:443 | TCP:443 | Registro Azure Container. | Può essere sostituito abilitando *Registro Azure Container* [endpoint di servizio nella rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *.core.windows.net:443 e *.core.windows.net:445 *o* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Storage:443 e Storage:445 | TCP:443, TCP:445 | Archiviazione file di Azure | Può essere sostituito abilitando *Archiviazione di Azure* [endpoint di servizio nella rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *.servicebus.windows.net:443 o  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - EventHub:443 | TCP:443 | Hub eventi di Azure. | Può essere sostituito abilitando *Hub eventi di Azure* [endpoint di servizio nella rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md). |
  

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Azure Spring Cloud fqdn/regole dell'applicazione

Firewall di Azure il tag FQDN **AzureKubernetesService** per semplificare le configurazioni seguenti:

  | FQDN di destinazione | Porta | Uso |
  |------|------|------|
  | *.azmk8s.io | HTTPS:443 | Gestione del cluster Kubernetes sottostante. |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | Microsoft Container Registry (MCR). |
  | *.cdn.mscr.io | HTTPS:443 | Archiviazione MCR supportata dal Rete CDN di Azure. |
  | *.data.mcr.microsoft.com | HTTPS:443 | Archiviazione MCR supportata dal Rete CDN di Azure. |
  | <i>management.azure.com</i> | HTTPS:443 | Gestione del cluster Kubernetes sottostante. |
  | <i>*login.microsoftonline.com</i> | HTTPS:443 | Azure Active Directory autenticazione. |
  | <i>*login.microsoft.com</i> | HTTPS:443 | Azure Active Directory autenticazione. |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Repository di pacchetti Microsoft. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | Repository necessario per installare i file binari necessari, ad esempio kubenet e Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS:80 | Percorsi della catena di certificati Microsoft necessari. |
  | *crl.microsoft.com* | HTTPS:80 | Percorsi della catena di certificati Microsoft necessari. |
  | *crl3.digicert.com* | HTTPS:80 | Percorsi della catena di certificati SSL di terze parti. |
  
## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>Azure Spring Cloud FQDN facoltativo per la gestione delle prestazioni delle applicazioni di terze parti

Firewall di Azure fornisce il tag FQDN **AzureKubernetesService** per semplificare le configurazioni seguenti:

  | FQDN di destinazione | Porta | Uso                                                          |
  | ---------------- | ---- | ------------------------------------------------------------ |
  | collector*.newrelic.com | TCP:443/80 | Le reti necessarie per New Relic agenti APM dall'area degli Stati Uniti, vedere anche [APM Agents Networks](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
  | collector*.eu01.nr-data.net | TCP:443/80 | Le reti necessarie per New Relic agenti APM dall'area dell'Unione Europea, vedere anche APM Agents Networks ( [Reti degli agenti APM).](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents) |

## <a name="see-also"></a>Vedere anche
* [Accedere all'applicazione in una rete privata](access-app-virtual-network.md)
* [Esporre le app usando il gateway applicazione e Firewall di Azure](expose-apps-gateway-azure-firewall.md)
