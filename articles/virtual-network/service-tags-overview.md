---
title: Panoramica dei tag del servizio di Azure
titlesuffix: Azure Virtual Network
description: Informazioni sui tag del servizio. I tag del servizio consentono di ridurre la complessità della creazione delle regole di sicurezza.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/14/2021
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 4a18e0f257ac8d7b7d981d53249cf29f2f8f38bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790150"
---
# <a name="virtual-network-service-tags"></a>Tag del servizio di rete virtuale
<a name="network-service-tags"></a>

Un tag del servizio rappresenta un gruppo di prefissi di indirizzi IP di un determinato servizio di Azure. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag in base alla modifica degli indirizzi, riducendo la complessità degli aggiornamenti frequenti alle regole di sicurezza di rete.

È possibile usare i tag di servizio per definire i controlli di accesso alla rete nei [gruppi di sicurezza](./network-security-groups-overview.md#security-rules) di rete o [Firewall di Azure](../firewall/service-tags.md). Usare i tag del servizio anziché indirizzi IP specifici quando si creano regole di sicurezza. Specificando il nome del tag del servizio, ad  esempio  **ApiManagement**, nel campo di origine o destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. 

> [!NOTE] 
> A partire da marzo 2021, è anche possibile usare i tag di servizio al posto di intervalli IP espliciti nelle [route definite dall'utente.](./virtual-networks-udr-overview.md) Questa funzionalità è attualmente in anteprima pubblica. 

È possibile usare i tag del servizio per ottenere l'isolamento rete e proteggere le risorse di Azure da Internet in generale durante l'accesso ai servizi di Azure con endpoint pubblici. Creare regole del gruppo di sicurezza di rete in ingresso/uscita per negare il traffico da/verso **Internet** e consentire il traffico da/verso **AzureCloud** o altri [tag del servizio](#available-service-tags) disponibili dei servizi di Azure specifici.

![Isolamento di rete dei servizi di Azure tramite tag di servizio](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Tag del servizio disponibili
La tabella seguente include tutti i tag del servizio disponibili per l'uso nelle regole del [gruppo di sicurezza di rete](./network-security-groups-overview.md#security-rules).

Le colonne indicano se il tag:

- È adatto alle regole che coprono il traffico in ingresso o in uscita.
- Supporta l'[ambito](https://azure.microsoft.com/regions) regionale.
- È utilizzabile nelle regole del [Firewall di Azure](../firewall/service-tags.md).

Per impostazione predefinita, i tag del servizio riflettono gli intervalli per l'intero cloud. Alcuni tag del servizio consentono un controllo più granulare limitando gli intervalli IP corrispondenti a un'area specificata. Ad esempio, il tag di servizio **Archiviazione** rappresenta Archiviazione di Azure per l'intero cloud, ma **storage.Westus** restringe l'intervallo solo agli intervalli di indirizzi IP di archiviazione dall'area WestUS. La tabella seguente indica se ogni tag del servizio supporta tale ambito regionale.  

| Tag | Scopo | È possibile usarlo in ingresso o in uscita? | Può essere regionale? | È possibile usarlo con Firewall di Azure? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Gruppo di azioni | In ingresso | No | No |
| **Gestione API** | Traffico per distribuzioni dedicate di Gestione API di Azure. <br/><br/>*Nota:* questo tag rappresenta l'endpoint del servizio Gestione API di Azure per il piano di controllo per area. Ciò consente ai clienti di eseguire operazioni di gestione su API, operazioni, criteri e valori denominati nel servizio Gestione API.  | In ingresso | Sì | Sì |
| **ApplicationInsightsAvailability** | Disponibilità di Application Insights. | In ingresso | No | No |
| **AppConfiguration** | Configurazione app | In uscita | No | No |
| **AppService**    | Servizio app di Azure. Questo tag è consigliato per le regole di sicurezza in uscita per le app Web e le app per le funzioni.  | In uscita | Sì | Sì |
| **AppServiceManagement** | Traffico di gestione per le distribuzioni dedicate all'ambiente del servizio app. | Entrambe | No | Sì |
| **AzureActiveDirectory** | Azure Active Directory. | In uscita | No | Sì |
| **AzureActiveDirectoryDomainServices** | Traffico di gestione per le distribuzioni dedicate in Azure Active Directory Domain Services. | Entrambe | No | Sì |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection | In uscita | No | No |
| **AzureAPIForFHIR** | API di Azure per FHIR (Fast Healthcare Interoperability Resources).<br/><br/> *Nota: questo tag non è attualmente configurabile tramite il portale di Azure.*| In uscita | No | No |
| **AzureArcInfrastructure** | Azure Arc server abilitati, Azure Arc kubernetes abilitati e il traffico di Configurazione guest.<br/><br/>*Nota:* Questo tag ha una dipendenza dai tag **AzureActiveDirectory**,**AzureTrafficManager** e **AzureResourceManager.** *Questo tag non è attualmente configurabile tramite il portale di Azure.*| In uscita | No | Sì |
| **AzureBackup** |Backup di Azure.<br/><br/>*Nota:* questo tag presenta una dipendenza dai tag **Storage** e **AzureActiveDirectory**. | In uscita | No | Sì |
| **AzureBotService** | Servizio Azure Bot. | In uscita | No | No |
| **AzureCloud** | Tutti gli [indirizzi IP pubblici dei data center](https://www.microsoft.com/download/details.aspx?id=56519). | In uscita | Sì | Sì |
| **AzureCognitiveSearch** | Ricerca cognitiva di Azure <br/><br/>Questo tag o gli indirizzi IP associati possono essere usati per concedere agli indicizzatori l'accesso sicuro alle origini dati. Per altri dettagli, vedere la [documentazione relativa alla connessione all'indicizzatore.](../search/search-indexer-troubleshooting.md#connection-errors) <br/><br/> *Nota*: l'indirizzo IP del servizio di ricerca non è incluso nell'elenco di intervalli IP per questo tag del servizio ed **è necessario aggiungerlo anche** al firewall IP delle origini dati. | In ingresso | No | No |
| **AzureConnectors** | Questo tag rappresenta gli indirizzi IP usati per i connettori gestiti che effettuano callback webhook in ingresso al servizio App per la logica di Azure e le chiamate in uscita ai rispettivi servizi, ad esempio Archiviazione di Azure o Hub eventi di Azure. | In ingresso/In uscita | Sì | Sì |
| **AzureContainerRegistry** | Registro Azure Container. | In uscita | Sì | Sì |
| **AzureCosmosDB** | Azure Cosmos DB. | In uscita | Sì | Sì |
| **AzureDatabricks** | Azure Databricks. | Entrambe | No | No |
| **AzureDataExplorerManagement** | Gestione Esplora dati di Azure. | In ingresso | No | No |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | In uscita | No | Sì |
| **AzureDevSpaces** | Azure Dev Spaces. | In uscita | No | No |
| **AzureDevOps** | Azure Dev Ops.<br/><br/>*Nota: questo tag non è attualmente configurabile tramite il portale di Azure*| In ingresso | No | Sì |
| **AzureDigitalTwins** | Gemelli digitali di Azure.<br/><br/>*Nota:* Questo tag o gli indirizzi IP coperti da questo tag possono essere usati per limitare l'accesso agli endpoint configurati per le route eventi. *Questo tag non è attualmente configurabile tramite il portale di Azure* | In ingresso | No | Sì |
| **AzureEventGrid** | Griglia di eventi di Azure. | Entrambe | No | No |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Frontdoor di Azure | Entrambe | No | No |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Nota:* questo tag presenta una dipendenza dai tag **AzureActiveDirectory**, **AzureFrontDoor.Frontend** e **AzureFrontDoor.FirstParty**. | In uscita | No | No |
| **AzureIoTHub** | Hub IoT di Azure. | In uscita | No | No |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Nota:* questo tag presenta una dipendenza dal tag  **AzureActiveDirectory**. | In uscita | Sì | Sì |
| **AzureLoadBalancer** | Bilanciamento del carico di infrastruttura di Azure. Viene convertito nell'[indirizzo IP virtuale dell'host](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16) da cui hanno origine i probe di integrità di Azure. Questo include solo il traffico probe, non il traffico reale verso la risorsa back-end. Se non si usa Azure Load Balancer, è possibile ignorare questa regola. | Entrambe | No | No |
| **AzureMachineLearning** | Azure Machine Learning | Entrambe | No | Sì |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon e metriche personalizzate (GiG endpoints).<br/><br/>*Nota:* Per Log Analytics è necessario **anche il** tag Archiviazione. Se vengono usati agenti Linux, è necessario anche il tag **GuestAndHybridManagement.** | In uscita | No | Sì |
| **AzureOpenDatasets** | Set di dati aperti di Azure.<br/><br/>*Nota:* questo tag presenta una dipendenza dai tag **AzureFrontDoor.Frontend** e **Storage**. | In uscita | No | No |
| **AzurePlatformDNS** | Servizio DNS dell'infrastruttura di base (impostazione predefinita).<br/><br>È possibile usare questo tag per disabilitare il DNS predefinito. Prestare attenzione quando si usa questo tag. È consigliabile leggere le [considerazioni sulla piattaforma di Azure](./network-security-groups-overview.md#azure-platform-considerations). È consigliabile inoltre eseguire i test prima di usare questo tag. | In uscita | No | No |
| **AzurePlatformIMDS** | Servizio metadati dell'istanza di Azure, un servizio dell'infrastruttura di base.<br/><br/>È possibile usare questo tag per disabilitare il servizio metadati dell'istanza di Azure predefinito. Prestare attenzione quando si usa questo tag. È consigliabile leggere le [considerazioni sulla piattaforma di Azure](./network-security-groups-overview.md#azure-platform-considerations). È consigliabile inoltre eseguire i test prima di usare questo tag. | In uscita | No | No |
| **AzurePlatformLKM** | Servizio di gestione delle chiavi o delle licenze Windows.<br/><br/>È possibile usare questo tag per disabilitare le impostazioni predefinite per le licenze. Prestare attenzione quando si usa questo tag. È consigliabile leggere le [considerazioni sulla piattaforma di Azure](./network-security-groups-overview.md#azure-platform-considerations).  È consigliabile inoltre eseguire i test prima di usare questo tag. | In uscita | No | No |
| **AzureResourceManager** | Azure Resource Manager. | In uscita | No | No |
| **AzureSignalR** | Servizio Azure SignalR. | In uscita | No | No |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Nota:* questo tag presenta una dipendenza dai tag **AzureActiveDirectory**, **AzureKeyVault**, **EventHub**, **GuestAndHybridManagement** e **Storage**. | In uscita | No | No |
| **AzureTrafficManager** | Indirizzi IP di probe di Gestione traffico di Azure.<br/><br/>Per altre informazioni sugli indirizzi IP di probe di Gestione traffico, vedere [Domande frequenti su Gestione traffico di Azure](../traffic-manager/traffic-manager-faqs.md). | In ingresso | No | Sì |  
| **BatchNodeManagement** | Traffico di gestione per le distribuzioni dedicate in Azure Batch. | Entrambe | No | Sì |
| **CognitiveServicesManagement** | Intervalli di indirizzi per il traffico per Servizi cognitivi di Azure. | Entrambe | No | No |
| **DataFactory**  | Azure Data Factory | Entrambe | No | No |
| **DataFactoryManagement** | Traffico di gestione per Azure Data Factory. | In uscita | No | No |
| **Dynamics365ForMarketingEmail** | Intervalli di indirizzi per il servizio di posta elettronica di marketing di Dynamics 365. | In uscita | Sì | No |
| **EventHub** | Hub eventi di Azure. | In uscita | Sì | Sì |
| **GatewayManager** | Traffico di gestione per le distribuzioni dedicate al Gateway VPN di Azure e al gateway applicazione. | In ingresso | No | No |
| **GuestAndHybridManagement** | Automazione di Azure e configurazione guest. | In uscita | No | Sì |
| **HDInsight** | Azure HDInsight. | In ingresso | Sì | No |
| **Internet** | Spazio di indirizzi IP esterno alla rete virtuale e raggiungibile tramite la rete Internet pubblica.<br/><br/>L'intervallo degli indirizzi include lo [spazio degli IP pubblici appartenenti ad Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Entrambe | No | No |
| **LogicApps** | App per la logica. | Entrambe | No | No |
| **LogicAppsManagement** | Traffico di gestione per App per la logica. | In ingresso | No | No |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | In uscita | No | No |
| **MicrosoftContainerRegistry** | Registro contenitori per le immagini del contenitore Microsoft. <br/><br/>*Nota:* questo tag presenta una dipendenza dal tag **AzureFrontDoor.FirstParty**. | In uscita | Sì | Sì |
| **PowerBI** | PowerBi. *Nota: questo tag non è attualmente configurabile tramite il portale di Azure.* | Entrambe | No | No|
| **PowerQueryOnline** | Power Query online. | Entrambe | No | No |
| **Bus di servizio** | Traffico del bus di servizio di Azure che usa il livello di servizio Premium. | In uscita | Sì | Sì |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Nota:* questo tag rappresenta l'endpoint del servizio Service Fabric per il piano di controllo per l'area. Ciò consente ai clienti di eseguire operazioni di gestione per i propri cluster Service Fabric dalle rispettive reti virtuali (endpoint, ad esempio https:// westus.servicefabric.azure.com) | Entrambe | No | No |
| **Sql** | database SQL di Azure, Database di Azure per MySQL, Database di Azure per PostgreSQL e Azure Synapse Analytics.<br/><br/>*Nota:* il tag rappresenta il servizio, ma non istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Database SQL di Azure, ma non uno specifico server o database SQL. Tale tag non si applica all'istanza gestita di SQL. | In uscita | Sì | Sì |
| **SqlManagement** | Traffico di gestione per le distribuzioni dedicate SQL. | Entrambe | No | Sì |
| **Storage** | Archiviazione di Azure. <br/><br/>*Nota:* il tag rappresenta il servizio, ma non istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Archiviazione di Azure, ma non uno specifico account di archiviazione di Azure. | In uscita | Sì | Sì |
| **StorageSyncService** | Servizio di sincronizzazione archiviazione | Entrambe | No | No |
| **WindowsVirtualDesktop** | Desktop virtuale Windows. | Entrambe | No | Sì |
| **VirtualNetwork** | Spazio di indirizzi della rete virtuale (tutti gli intervalli di indirizzi IP definiti per la rete virtuale), tutti gli spazi di indirizzi locali connessi, reti virtuali con [peering](virtual-network-peering-overview.md), reti virtuali connesse a un [gateway di rete virtuale](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), l'[indirizzo IP virtuale dell'host](./network-security-groups-overview.md#azure-platform-considerations) e i prefissi di indirizzo usati nelle [route definite dall'utente](virtual-networks-udr-overview.md). Questo tag può contenere anche route predefinite. | Entrambe | No | No |

>[!NOTE]
>Nel modello di distribuzione classica (prima di Azure Resource Manager), è supportato un subset dei tag elencati nella tabella precedente. Questi tag hanno un'ortografia diversa:
>
>| Ortografia classica | Tag di Resource Manager equivalente |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> I tag di servizio per i servizi Azure identificano i prefissi di indirizzo dal cloud specifico in uso. Gli intervalli IP sottostanti, ad esempio, che corrispondono al valore del tag **Sql** nel cloud pubblico di Azure, sono diversi dagli intervalli sottostanti nel cloud di Azure Cina.

> [!NOTE]
> Se si implementa un [endpoint servizio di rete virtuale](virtual-network-service-endpoints-overview.md) per un servizio come Archiviazione di Azure o Database SQL di Azure, Azure aggiunge una [route](virtual-networks-udr-overview.md#optional-default-routes) a una subnet di rete virtuale per il servizio. I prefissi di indirizzo nella route sono gli stessi prefissi di indirizzo o intervalli CIDR di quelli del tag di servizio corrispondente.

## <a name="service-tags-on-premises"></a>Tag del servizio in locale  
È possibile ottenere il tag del servizio e le informazioni sull'intervallo correnti da includere nell'ambito delle configurazioni del firewall locale. Tali  informazioni rappresentano l'elenco temporizzato corrente degli intervalli IP che corrispondono a ogni tag del servizio. È possibile ottenere le informazioni a livello di codice o tramite un download di file JSON, come descritto nelle sezioni seguenti.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Usare l'API di individuazione dei tag del servizio (anteprima pubblica)
È possibile recuperare l'elenco corrente dei tag di servizio a livello di codice insieme ai dettagli dell'intervallo di indirizzi IP:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Interfaccia della riga di comando di Azure](/cli/azure/network#az_network_list_service_tags)

> [!NOTE]
> La propagazione dei nuovi dati del tag di servizio nei risultati dell'API richiede fino a 4 settimane. Il numero di modifica nei metadati della risposta verrà incrementato in questo caso. Possono esserci differenze temporanee nei risultati quando vengono specificati valori di posizione diversi. Quando si usano i risultati per creare regole del gruppo di sicurezza di rete, è necessario impostare il parametro location in modo che corrisponda all'area del gruppo di sicurezza di rete. 

> [!NOTE]
> I dati dell'API rappresenteranno i tag che possono essere usati con le regole del gruppo di sicurezza di rete, un subset dei tag attualmente presenti nel file JSON scaricabile. Durante l'anteprima pubblica, non è garantito che i dati rimangano invariati da un aggiornamento a quello successivo. 

### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Individuare i tag del servizio tramite file JSON scaricabili 
È possibile scaricare i file JSON che contengono l'elenco corrente dei tag del servizio insieme ai dettagli dell'intervallo di indirizzi IP. Tali elenchi vengono aggiornati e pubblicati settimanalmente. Le località per ogni cloud sono le seguenti:

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Gov](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure per la Cina](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germania](https://www.microsoft.com/download/details.aspx?id=57064)   

Gli intervalli di indirizzi IP in questi file sono in notazione CIDR. 

> [!NOTE]
>Un subset di queste informazioni è stato pubblicato in file XML per [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Cina](https://www.microsoft.com/download/details.aspx?id=42064) e [Azure Germania](https://www.microsoft.com/download/details.aspx?id=54770). Tali download XML saranno deprecati entro il 30 giugno 2020 e non saranno più disponibili dopo tale data. È consigliabile eseguire la migrazione usando l'API di individuazione o i download di file JSON come descritto nelle sezioni precedenti.

### <a name="tips"></a>Suggerimenti 
- È possibile rilevare gli aggiornamenti da una pubblicazione alla successiva annotando un aumento dei valori *changeNumber* nel file JSON. In ogni sottosezione (ad esempio **storage.Westus**) è presente il valore *changeNumber* che viene incrementato quando si verificano modifiche. Il primo livello del valore *changeNumber* del file viene incrementato quando una delle sottosezioni viene modificata.
- Per esempi di come analizzare le informazioni sui tag del servizio (ad esempio per ottenere tutti gli intervalli di indirizzi per l'archiviazione in WestUS), vedere la documentazione dell'[API di individuazione del tag del servizio in PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag).
- Quando vengono aggiunti nuovi indirizzi IP ai tag del servizio, non verranno usati in Azure per almeno una settimana. In questo modo è possibile aggiornare tutti i sistemi che potrebbero dover tenere traccia degli indirizzi IP associati ai tag del servizio.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [creare un gruppo di sicurezza di rete](tutorial-filter-network-traffic.md).
