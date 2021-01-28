---
title: Responsabilità dei clienti che eseguono Azure Spring cloud in VNET
description: Questo articolo descrive le responsabilità dei clienti che eseguono il cloud Spring di Azure in vnet.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 5ecf9e49887eb584269f724d5199cbfb014351e0
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986854"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Responsabilità del cliente per l'esecuzione di Azure Spring cloud in VNET
Questo documento include le specifiche per l'uso del cloud Spring di Azure in una rete virtuale.

Quando Azure Spring cloud viene distribuito nella rete virtuale, ha dipendenze in uscita dai servizi all'esterno della rete virtuale. A scopo di gestione e operativi, Azure Spring cloud deve accedere a determinate porte e nomi di dominio completi (FQDN). Questi endpoint sono necessari per comunicare con il piano di gestione cloud Spring di Azure e per scaricare e installare i componenti principali del cluster Kubernetes e gli aggiornamenti della sicurezza.

Per impostazione predefinita, Azure Spring cloud ha accesso a Internet in uscita senza restrizioni (in uscita). Questo livello di accesso alla rete consente alle applicazioni eseguite di accedere alle risorse esterne in base alle esigenze. Se si vuole limitare il traffico in uscita, un numero limitato di porte e indirizzi deve essere accessibile per le attività di manutenzione. La soluzione più semplice per proteggere gli indirizzi in uscita è l'uso di un dispositivo firewall che può controllare il traffico in uscita in base ai nomi di dominio. Il firewall di Azure, ad esempio, può limitare il traffico HTTP e HTTPS in uscita in base al nome di dominio completo (FQDN) della destinazione. È anche possibile configurare le regole del firewall e di sicurezza preferite per consentire le porte e gli indirizzi richiesti.

## <a name="azure-spring-cloud-resource-requirements"></a>Requisiti delle risorse del cloud Spring di Azure 

Di seguito è riportato un elenco dei requisiti di risorse per i servizi cloud Spring di Azure. Come requisito generale, non è consigliabile modificare i gruppi di risorse creati da Azure Spring cloud e le risorse di rete sottostanti.
- Non modificare i gruppi di risorse creati e di proprietà del cloud Spring di Azure.
  - Per impostazione predefinita, questi gruppi di risorse vengono denominati come AP-SVC-rt_ [SERVICE-INSTANCE-NAME]_[Region] * e AP_[Service-instance-name] _ [Region] *.
- Non modificare le subnet usate dal cloud Spring di Azure.
- Non creare più di un'istanza del servizio cloud Spring di Azure nella stessa subnet.
- Quando si usa un firewall per controllare il traffico, *non* bloccare il traffico in uscita seguente ai componenti cloud di Azure Spring che operano, gestiscono e supportano l'istanza del servizio.

## <a name="azure-spring-cloud-network-requirements"></a>Requisiti di rete del cloud Spring di Azure

  | Endpoint di destinazione | Porta | Uso | Nota |
  |------|------|------|
  | *: 1194 *o* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud: 1194 | UDP: 1194 | Gestione cluster Kubernetes sottostante. | |
  | *: 443 *o* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud: 443 | TCP: 443 | Gestione dei servizi cloud Spring di Azure. | Le informazioni dell'istanza di servizio "requiredTraffics" possono essere note nel payload della risorsa, nella sezione "networkProfile". |
  | *: 9000 *o* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud: 9000 | TCP: 9000 | Gestione cluster Kubernetes sottostante. |
  | *: 123 *o* NTP.Ubuntu.com:123 | UDP:123 | Sincronizzazione dell'ora NTP nei nodi Linux. | |
  | *. azure.io:443 *o* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureContainerRegistry: 443 | TCP: 443 | Registro Azure Container. | Può essere sostituito abilitando l'endpoint del servizio *container Registry di Azure* [nella rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). |
  | *. core.windows.net:443 e *. core.windows.net:445 *o* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -storage: 443 e archiviazione: 445 | TCP: 443, TCP: 445 | Archiviazione file di Azure | Può essere sostituito abilitando l'endpoint del servizio di *archiviazione di Azure* [nella rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). |
  | *. servicebus.windows.net:443 *o* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -EventHub: 443 | TCP: 443 | Hub eventi di Azure. | Può essere sostituito abilitando l'endpoint del servizio *Hub eventi di Azure* [nella rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>Requisiti per l'FQDN del cloud Spring di Azure/regole dell'applicazione

Il firewall di Azure fornisce un tag di nome di dominio completo (FQDN) **AzureKubernetesService** per semplificare le configurazioni seguenti.

  | FQDN di destinazione | Porta | Uso |
  |------|------|------|
  | *. azmk8s.io | HTTPS:443 | Gestione cluster Kubernetes sottostante. |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | Microsoft Container Registry. |
  | *.cdn.mscr.io | HTTPS:443 | Archiviazione in base al supporto della rete CDN di Azure. |
  | *.data.mcr.microsoft.com | HTTPS:443 | Archiviazione in base al supporto della rete CDN di Azure. |
  | <i>management.azure.com</i> | HTTPS:443 | Gestione cluster Kubernetes sottostante. |
  | <i>login.microsoftonline.com</i> | HTTPS:443 | Autenticazione Azure Active Directory. |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Repository dei pacchetti Microsoft. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | Repository necessario per installare i file binari necessari, ad esempio kubenet e Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS: 80 | Percorsi della catena di certificati Microsoft richiesti. |
  | *crl.microsoft.com* | HTTPS: 80 | Percorsi della catena di certificati Microsoft richiesti. |
  | *crl3.digicert.com* | HTTPS: 80 | Percorsi della catena di certificati SSL di terze parti. |

## <a name="see-also"></a>Vedi anche
* [Accedere all'applicazione in una rete privata](spring-cloud-access-app-virtual-network.md)
* [Esporre app usando il gateway applicazione e il firewall di Azure](spring-cloud-expose-apps-gateway-azure-firewall.md) 