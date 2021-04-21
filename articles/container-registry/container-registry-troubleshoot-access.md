---
title: Risolvere i problemi di rete con il Registro di sistema
description: Sintomi, cause e risoluzione dei problemi comuni durante l'accesso a un Registro Azure Container in una rete virtuale o dietro un firewall
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: dc2110405713791d11fb438565fc091da9c9dd5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780753"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Risolvere i problemi di rete con il Registro di sistema

Questo articolo consente di risolvere i problemi che possono verificarsi quando si accede a un Registro Azure Container in una rete virtuale o dietro un firewall o un server proxy. 

## <a name="symptoms"></a>Sintomi

Può includere uno o più degli elementi seguenti:

* Non è possibile eseguire il push o il pull delle immagini e viene visualizzato l'errore `dial tcp: lookup myregistry.azurecr.io`
* Non è possibile eseguire il push o il pull delle immagini e viene visualizzato l'errore `Client.Timeout exceeded while awaiting headers`
* Non è possibile eseguire il push o il pull di immagini e viene visualizzato un errore dell'interfaccia della riga di comando di Azure `Could not connect to the registry login server`
* Non è possibile eseguire il pull di immagini dal registro servizio Azure Kubernetes o un altro servizio di Azure
* Non è possibile accedere a un registro dietro un proxy HTTPS e viene visualizzato `Error response from daemon: login attempt failed with status: 403 Forbidden` l'errore o `Error response from daemon: Get <registry>: proxyconnect tcp: EOF Login failed`
* Non è possibile configurare le impostazioni della rete virtuale e viene visualizzato l'errore `Failed to save firewall and virtual network settings for container registry`
* Non è possibile accedere o visualizzare le impostazioni del Registro di sistema in portale di Azure o gestire il Registro di sistema usando l'interfaccia della riga di comando di Azure
* Impossibile aggiungere o modificare le impostazioni di rete virtuale o le regole di accesso pubblico
* ACR Tasks non è in grado di eseguire il push o il pull di immagini
* Centro sicurezza di Azure possibile analizzare le immagini nel Registro di sistema o i risultati dell'analisi non vengono visualizzati in Centro sicurezza di Azure
* Viene visualizzato un `host is not reachable` errore quando si tenta di accedere a un registro configurato con un endpoint privato.

## <a name="causes"></a>Cause

* Un firewall client o un proxy impedisce l'accesso - [Soluzione](#configure-client-firewall-access)
* Le regole di accesso alla rete pubblica nel registro impediscono l'accesso - [Soluzione](#configure-public-access-to-registry)
* La configurazione della rete virtuale impedisce l'accesso - [Soluzione](#configure-vnet-access)
* Si tenta di integrare Centro sicurezza di Azure o altri servizi di Azure con un registro con un endpoint privato, un endpoint di servizio o regole di accesso ip pubblico - [soluzione](#configure-service-access)

## <a name="further-diagnosis"></a>Ulteriore diagnosi 

Eseguire il [comando az acr check-health](/cli/azure/acr#az_acr_check_health) per ottenere altre informazioni sull'integrità dell'ambiente del Registro di sistema e, facoltativamente, per accedere a un registro di destinazione. Ad esempio, diagnosticare determinati problemi di connettività di rete o di configurazione. 

Per esempi di comandi, vedere Controllare [l'integrità di](container-registry-check-health.md) un Registro Azure Container. Se vengono segnalati errori, esaminare il riferimento [all'errore](container-registry-health-error-reference.md) e le sezioni seguenti per le soluzioni consigliate.

Se si verificano problemi durante l'uso di un servizio Azure Kubernetes con un Registro di sistema integrato, eseguire il [comando az aks check-acr](/cli/azure/aks#az_aks_check_acr) per verificare che il cluster del servizio AKS possa raggiungere il Registro di sistema.

> [!NOTE]
> Alcuni sintomi di connettività di rete possono verificarsi anche in caso di problemi con l'autenticazione o l'autorizzazione del Registro di sistema. Vedere [Risolvere i problemi di accesso al Registro di sistema](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Possibili soluzioni

### <a name="configure-client-firewall-access"></a>Configurare l'accesso al firewall client

Per accedere a un registro da dietro un firewall client o un server proxy, configurare le regole del firewall per accedere agli endpoint dati e REST pubblici del Registro di sistema. Se [gli endpoint dati dedicati sono](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) abilitati, sono necessarie regole per accedere a:

* Endpoint REST: `<registryname>.azurecr.io`
* Endpoint dati: `<registry-name>.<region>.data.azurecr.io`

Per un registro con replica geografica, configurare l'accesso all'endpoint dati per ogni replica a livello di area.

Dietro un proxy HTTPS, assicurarsi che sia il client Docker che il daemon Docker siano configurati per il comportamento del proxy. Se si modificano le impostazioni proxy per il daemon Docker, assicurarsi di riavviare il daemon. 

I log delle risorse del Registro di sistema nella tabella ContainerRegistryLoginEvents possono aiutare a diagnosticare un tentativo di connessione bloccato.

Collegamenti correlati:

* [Configurare le regole per accedere a un Registro Azure Container dietro un firewall](container-registry-firewall-access-rules.md)
* [Configurazione del proxy HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Replica geograficain Registro Azure Container](container-registry-geo-replication.md)
* [Registro Azure Container log per la valutazione diagnostica e il controllo](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Configurare l'accesso pubblico al registro

Se si accede a un registro tramite Internet, verificare che il registro consenta l'accesso alla rete pubblica dal client. Per impostazione predefinita, un Registro Azure Container consente l'accesso agli endpoint del registro pubblico da tutte le reti. Un registro può limitare l'accesso alle reti selezionate o agli indirizzi IP selezionati. 

Se il Registro di sistema è configurato per una rete virtuale con un endpoint di servizio, la disabilitazione dell'accesso alla rete pubblica disabilita anche l'accesso sull'endpoint di servizio. Se il registro è configurato per una rete virtuale con collegamento privato, le regole di rete IP non si applicano agli endpoint privati del registro. 

Collegamenti correlati:

* [Configurare le regole di rete dell'indirizzo IP pubblico](container-registry-access-selected-networks.md)
* [Connettersi privatamente a un Registro Azure Container usando collegamento privato di Azure](container-registry-private-link.md)
* [Limitare l'accesso a un registro contenitori usando un endpoint di servizio in una rete virtuale di Azure](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Configurare l'accesso alla rete virtuale

Verificare che la rete virtuale sia configurata con un endpoint privato per collegamento privato o un endpoint di servizio (anteprima). Attualmente un endpoint Azure Bastion non è supportato.

Se è configurato un endpoint privato, verificare che DNS risolva l'FQDN pubblico del registro, ad esempio myregistry.azurecr.io *nell'indirizzo* IP privato del registro. Usare un'utilità di rete, ad `dig` esempio o per la ricerca `nslookup` DNS. Assicurarsi che [i record DNS siano configurati per l'FQDN](container-registry-private-link.md#dns-configuration-options) del Registro di sistema e per ogni FQDN dell'endpoint dati.

Esaminare le regole del gruppo di sicurezza di rete e i tag di servizio usati per limitare il traffico da altre risorse della rete al registro. 

Se è configurato un endpoint di servizio per il Registro di sistema, verificare che al Registro di sistema sia stata aggiunta una regola di rete che consenta l'accesso da tale subnet di rete. L'endpoint di servizio supporta solo l'accesso dalle macchine virtuali e dai cluster del servizio AzureKs nella rete.

Se si vuole limitare l'accesso al registro usando una rete virtuale in una sottoscrizione di Azure diversa, assicurarsi di registrare il `Microsoft.ContainerRegistry` provider di risorse in tale sottoscrizione. [Registrare il provider di risorse per](../azure-resource-manager/management/resource-providers-and-types.md) l'Registro Azure Container usando portale di Azure, l'interfaccia della riga di comando di Azure o altri strumenti di Azure.

Se Firewall di Azure o una soluzione simile è configurata nella rete, verificare che il traffico in uscita da altre risorse, ad esempio un cluster del servizio AKS, sia abilitato per raggiungere gli endpoint del Registro di sistema.

Collegamenti correlati:

* [Connettersi privatamente a un registro Azure Container usando collegamento privato di Azure](container-registry-private-link.md)
* [Risolvere i problemi di connettività all'endpoint privato di Azure](../private-link/troubleshoot-private-endpoint-connectivity.md)
* [Limitare l'accesso a un registro contenitori usando un endpoint di servizio in una rete virtuale di Azure](container-registry-vnet.md)
* [Regole di rete in uscita obbligatorie e FQDN per i cluster del servizio Gateway Gateway](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: Debug della risoluzione DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Tag del servizio di rete virtuale](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Configurare l'accesso al servizio

Attualmente, l'accesso a un registro contenitori con restrizioni di rete non è consentito da diversi servizi di Azure:

* Centro sicurezza di Azure possibile eseguire l'analisi della vulnerabilità delle immagini [in](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) un Registro di sistema che limita l'accesso a endpoint privati, subnet selezionate o indirizzi IP. 
* Le risorse di determinati servizi di Azure non sono in grado di accedere a un registro contenitori con restrizioni di rete, tra cui Servizio app di Azure e Istanze di Azure Container.

Se è necessario l'accesso o l'integrazione di questi servizi di Azure con il registro contenitori, rimuovere la restrizione di rete. Ad esempio, rimuovere gli endpoint privati del Registro di sistema o rimuovere o modificare le regole di accesso pubblico del Registro di sistema.

A partire da gennaio 2021, è possibile configurare un registro con restrizioni di rete per consentire [l'accesso](allow-access-trusted-services.md) da servizi attendibili selezionati.

Collegamenti correlati:

* [Registro Azure Container di immagini dal Centro sicurezza](../security-center/defender-for-container-registries-introduction.md)
* Inviare [commenti e suggerimenti](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [Consentire ai servizi attendibili di accedere in modo sicuro a un registro contenitori con restrizioni di rete](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Risoluzione dei problemi avanzata

Se [la raccolta di log delle risorse](container-registry-diagnostics-audit-logs.md) è abilitata nel Registro di sistema, esaminare il log ContainterRegistryLoginEvents. Questo log archivia gli eventi di autenticazione e lo stato, inclusi l'identità in ingresso e l'indirizzo IP. Eseguire una query nel log per gli [errori di autenticazione del Registro di sistema.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Collegamenti correlati:

* [Log per la valutazione e il controllo di diagnostica](container-registry-diagnostics-audit-logs.md)
* [Domande frequenti sul registro contenitori](container-registry-faq.md)
* [Baseline di sicurezza di Azure per Registro Azure Container](security-baseline.md)
* [Procedure consigliate per Registro Azure Container](container-registry-best-practices.md)

## <a name="next-steps"></a>Passaggi successivi

Se il problema persiste, vedere le opzioni seguenti.

* Altri argomenti relativi alla risoluzione dei problemi del Registro di sistema includono:
  * [Risolvere i problemi di accesso al Registro di sistema](container-registry-troubleshoot-login.md) 
  * [Risolvere i problemi relativi alle prestazioni del Registro di sistema](container-registry-troubleshoot-performance.md)
* [Opzioni di supporto della](https://azure.microsoft.com/support/community/) community
* [Domande e risposte Microsoft](/answers/products/)
* [Aprire un ticket di supporto](https://azure.microsoft.com/support/create-ticket/)