---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: kriation
title: Architettura di riferimento del cloud Spring di Azure
ms.author: akaleshian
ms.service: spring-cloud
description: Questa architettura di riferimento è una base che usa un tipico hub aziendale e una progettazione spoke per l'uso di Azure Spring cloud.
ms.openlocfilehash: 74183ca2decf8487e5c41cf36d5784538021077f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604169"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Architettura di riferimento del cloud Spring di Azure

Questa architettura di riferimento è una base che usa un tipico hub aziendale e una progettazione spoke per l'uso di Azure Spring cloud. Nella progettazione Azure Spring cloud viene distribuito in un singolo spoke che dipende dai servizi condivisi ospitati nell'hub. L'architettura è costituita da componenti che consentono di ottenere i principi nel [Framework Microsoft Azure Well-Architected][16].

Per un'implementazione di questa architettura, vedere il repository dell' [architettura di riferimento del cloud Spring di Azure][10] su GitHub.

Le opzioni di distribuzione per questa architettura includono Azure Resource Manager (ARM), bonifica e l'interfaccia della riga di comando di Azure. Gli elementi in questo repository forniscono una base che è possibile personalizzare per l'ambiente in uso. È possibile raggruppare risorse come il firewall di Azure o il gateway applicazione in sottoscrizioni o gruppi di risorse diversi. Questo raggruppamento consente di rendere separate le diverse funzioni, ad esempio l'infrastruttura IT, la sicurezza, i team delle applicazioni aziendali e così via.

## <a name="planning-the-address-space"></a>Pianificazione dello spazio degli indirizzi

Azure Spring cloud richiede due subnet dedicate:

* Runtime del servizio
* Applicazioni Spring boot

Ognuna di queste subnet richiede un cluster dedicato. Più cluster non possono condividere le stesse subnet. La dimensione minima di ogni subnet è/28. Il numero di istanze dell'applicazione che Azure Spring cloud può supportare varia in base alle dimensioni della subnet. I requisiti dettagliati della rete virtuale (VNET) sono disponibili nella sezione [requisiti della rete][11] virtuale della pagina relativa alla distribuzione del [cloud Spring di Azure in una rete virtuale][17].

> [!WARNING]
> Le dimensioni della subnet selezionate non possono sovrapporsi allo spazio degli indirizzi della VNET esistente e non devono sovrapporsi ad alcun intervallo di indirizzi di subnet con peering o in locale.

## <a name="use-cases"></a>Casi d'uso

Tra gli usi tipici di questa architettura sono inclusi:

* Applicazioni interne distribuite in ambienti cloud ibridi
* Applicazioni rivolte all'esterno

Questi casi d'uso sono simili, ad eccezione delle regole di sicurezza e del traffico di rete. Questa architettura è progettata per supportare le sfumature di ciascuna di esse.

## <a name="private-applications"></a>Applicazioni private

Nell'elenco seguente vengono descritti i requisiti dell'infrastruttura per le applicazioni private. Questi requisiti sono tipici negli ambienti altamente regolamentati.

* Nessun uscita diretta alla rete Internet pubblica, ad eccezione del traffico del piano di controllo.
* Il traffico in uscita deve viaggiare attraverso un'appliance virtuale di rete centrale (NVA), ad esempio un firewall di Azure.
* I dati inattivi devono essere crittografati.
* È necessario crittografare i dati in transito.
* È possibile usare le pipeline di distribuzione di DevOps (ad esempio, Azure DevOps) e richiedere la connettività di rete al cloud Spring di Azure.
* L'approccio di sicurezza zero trust Microsoft richiede l'archiviazione di segreti, certificati e credenziali in un insieme di credenziali protetto. Il servizio consigliato è Azure Key Vault.
* I record DNS (Application host Domain Name Service) devono essere archiviati in Azure DNS privato.
* La risoluzione dei nomi degli host in locale e nel cloud deve essere bidirezionale.
* È necessario applicare l'aderenza ad almeno un benchmark di sicurezza.
* Le dipendenze dei servizi di Azure devono comunicare tramite endpoint di servizio o collegamento privato.
* I gruppi di risorse gestiti dalla distribuzione del cloud Spring di Azure non devono essere modificati.
* Le subnet gestite dalla distribuzione cloud di Azure Spring non devono essere modificate.
* Una subnet deve avere una sola istanza di Azure Spring cloud.
* Se il [server di configurazione cloud di Azure Spring][8] viene usato per caricare le proprietà di configurazione da un repository, il repository deve essere privato.

Nell'elenco seguente sono illustrati i componenti che costituiscono la progettazione:

* Rete locale
  * Domain Name Service (DNS)
  * Gateway
* Sottoscrizione Hub
  * Subnet del firewall di Azure
  * Subnet del gateway applicazione
  * Subnet dei servizi condivisi
* Sottoscrizione connessa
  * Peer rete virtuale
  * Subnet di Azure Bastion

L'elenco seguente descrive i servizi di Azure in questa architettura di riferimento:

* [Azure Spring cloud][1]: un servizio gestito progettato e ottimizzato in modo specifico per le applicazioni Spring boot basate su Java e. Applicazioni [Steeltoe][9] basate su rete.

* [Azure Key Vault][2]: un servizio di gestione delle credenziali basato su hardware con una stretta integrazione con i servizi di identità Microsoft e le risorse di calcolo.

* [Monitoraggio di Azure][3]: una suite completa di servizi di monitoraggio per le applicazioni che distribuiscono sia in Azure che in locale.

* [Centro sicurezza di Azure][4]: un sistema unificato per la gestione della sicurezza e la protezione dalle minacce per i carichi di lavoro in locale, più cloud e Azure.

* [Azure Pipelines][5]: un servizio di integrazione continua/recapito continuo (ci/CD) completo che può distribuire automaticamente le app Spring boot aggiornate in Azure Spring cloud.

Il diagramma seguente rappresenta una progettazione di hub e spoke ben progettata che soddisfa i requisiti indicati in precedenza:

![Diagramma dell'architettura di riferimento per applicazioni private](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Applicazioni pubbliche

Nell'elenco seguente vengono descritti i requisiti dell'infrastruttura per le applicazioni pubbliche. Questi requisiti sono tipici negli ambienti altamente regolamentati.

* Il traffico in ingresso deve essere gestito da almeno un gateway applicazione o un front-end di Azure.
* È necessario abilitare la protezione DDoS di Azure standard.
* Nessun uscita diretta alla rete Internet pubblica, ad eccezione del traffico del piano di controllo.
* Il traffico in uscita deve attraversare un'appliance virtuale di rete centrale (NVA), ad esempio un firewall di Azure.
* I dati inattivi devono essere crittografati.
* È necessario crittografare i dati in transito.
* È possibile usare le pipeline di distribuzione di DevOps (ad esempio, Azure DevOps) e richiedere la connettività di rete al cloud Spring di Azure.
* L'approccio di sicurezza zero trust Microsoft richiede l'archiviazione di segreti, certificati e credenziali in un insieme di credenziali protetto. Il servizio consigliato è Azure Key Vault.
* I record DNS dell'host applicazioni devono essere archiviati in DNS privato di Azure.
* Gli indirizzi instradabili Internet devono essere archiviati nel DNS pubblico di Azure.
* La risoluzione dei nomi degli host in locale e nel cloud deve essere bidirezionale.
* È necessario applicare l'aderenza ad almeno un benchmark di sicurezza.
* Le dipendenze dei servizi di Azure devono comunicare tramite endpoint di servizio o collegamento privato.
* I gruppi di risorse gestiti dalla distribuzione del cloud Spring di Azure non devono essere modificati.
* Le subnet gestite dalla distribuzione cloud di Azure Spring non devono essere modificate.
* Una subnet deve avere una sola istanza di Azure Spring cloud.

Nell'elenco seguente sono illustrati i componenti che costituiscono la progettazione:

* Rete locale
  * Domain Name Service (DNS)
  * Gateway
* Sottoscrizione Hub
  * Subnet del firewall di Azure
  * Subnet del gateway applicazione
  * Subnet dei servizi condivisi
* Sottoscrizione connessa
  * Peer rete virtuale
  * Subnet di Azure Bastion

L'elenco seguente descrive i servizi di Azure in questa architettura di riferimento:

* [Azure Spring cloud][1]: un servizio gestito progettato e ottimizzato in modo specifico per le applicazioni Spring boot basate su Java e. Applicazioni [Steeltoe][9] basate su rete.

* [Azure Key Vault][2]: un servizio di gestione delle credenziali basato su hardware con una stretta integrazione con i servizi di identità Microsoft e le risorse di calcolo.

* [Monitoraggio di Azure][3]: una suite completa di servizi di monitoraggio per le applicazioni che distribuiscono sia in Azure che in locale.

* [Centro sicurezza di Azure][4]: un sistema unificato per la gestione della sicurezza e la protezione dalle minacce per i carichi di lavoro in locale, più cloud e Azure.

* [Azure Pipelines][5]: un servizio di integrazione continua/recapito continuo (ci/CD) completo che può distribuire automaticamente le app Spring boot aggiornate in Azure Spring cloud.

* [Gateway applicazione Azure][6]: un servizio di bilanciamento del carico responsabile del traffico delle applicazioni con offload Transport Layer Security (TLS) operativo al livello 7.

* [Applicazione Azure firewall][7]: funzionalità di applicazione Azure gateway che offre una protezione centralizzata delle applicazioni da exploit e vulnerabilità comuni.

Il diagramma seguente rappresenta una progettazione di hub e spoke ben progettata che soddisfa i requisiti indicati in precedenza:

![Diagramma dell'architettura di riferimento per le applicazioni pubbliche](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Connettività locale di Azure Spring cloud

Le applicazioni in esecuzione in Azure Spring cloud possono comunicare con varie risorse di Azure, locali ed esterne. Con la progettazione hub e spoke, le applicazioni possono instradare il traffico all'esterno o alla rete locale usando Express route o VPN (Virtual Private Network) da sito a sito.

## <a name="azure-well-architected-framework-considerations"></a>Considerazioni su Azure Well-Architected Framework

Il [Framework di Well-Architected di Azure][16] è un set di principi guida da seguire per stabilire una solida infrastruttura di base. Il Framework contiene le seguenti categorie: ottimizzazione dei costi, eccellenza operativa, efficienza delle prestazioni, affidabilità e sicurezza.

### <a name="cost-optimization"></a>Ottimizzazione dei costi

A causa della natura della progettazione del sistema distribuito, l'espansione dell'infrastruttura è una realtà. Questa realtà comporta costi imprevisti e incontrollabili. Azure Spring cloud è stato creato usando i componenti che si ridimensionano per poter soddisfare la domanda e ottimizzare i costi. Il nucleo di questa architettura è il servizio Azure Kubernetes (AKS). Il servizio è progettato per ridurre la complessità e il sovraccarico operativo della gestione di Kubernetes, che include efficienza nel costo operativo del cluster.

È possibile distribuire applicazioni e tipi di applicazione diversi in una singola istanza del cloud Spring di Azure. Il servizio supporta la scalabilità automatica delle applicazioni attivate da metriche o pianificazioni che possono migliorare l'utilizzo e l'efficienza dei costi.

È anche possibile usare Application Insights e monitoraggio di Azure per ridurre i costi operativi. Con la visibilità fornita dalla soluzione di registrazione completa, è possibile implementare l'automazione per ridimensionare i componenti del sistema in tempo reale. È anche possibile analizzare i dati di log per rivelare inefficienze nel codice dell'applicazione che è possibile risolvere per migliorare i costi e le prestazioni complessivi del sistema.

### <a name="operational-excellence"></a>Eccellenza operativa

Azure Spring cloud affronta più aspetti dell'eccellenza operativa. È possibile combinare questi aspetti per assicurarsi che il servizio venga eseguito in modo efficiente negli ambienti di produzione, come descritto nell'elenco seguente:

* È possibile utilizzare Azure Pipelines per garantire che le distribuzioni siano affidabili e coerenti, evitando al tempo stesso errori umani.
* È possibile usare monitoraggio di Azure e Application Insights per archiviare i dati di log e di telemetria.
  È possibile valutare i dati di log e delle metriche raccolti per garantire l'integrità e le prestazioni delle applicazioni. APM (Application Performance Monitoring) è completamente integrato nel servizio tramite un agente Java. Questo agente offre visibilità su tutte le applicazioni e le dipendenze distribuite senza richiedere codice aggiuntivo. Per altre informazioni, vedere il post di Blog [su come monitorare con facilità le applicazioni e le dipendenze in Azure Spring cloud][15].
* È possibile usare il Centro sicurezza di Azure per garantire che le applicazioni mantengano la sicurezza fornendo una piattaforma per analizzare e valutare i dati forniti.
* Il servizio supporta diversi modelli di distribuzione. Per altre informazioni, vedere [Configurare un ambiente di staging in Azure Spring Cloud][14].

### <a name="reliability"></a>Affidabilità

Azure Spring cloud è progettato con AKS come componente fondamentale. Sebbene AKS fornisca un livello di resilienza tramite il clustering, questa architettura di riferimento incorpora i servizi e le considerazioni sull'architettura per aumentare la disponibilità dell'applicazione in caso di errore del componente.

Basandosi sulla progettazione di un hub e spoke ben definito, la base di questa architettura garantisce che sia possibile distribuirla in più aree. Per il caso d'uso dell'applicazione privata, l'architettura usa DNS privato di Azure per garantire la disponibilità continua durante un errore geografico. Per il caso d'uso dell'applicazione pubblica, il gateway di Azure e applicazione Azure gateway garantiscono la disponibilità.

### <a name="security"></a>Sicurezza

La sicurezza di questa architettura viene risolta in base alla relativa adesione ai controlli e ai benchmark definiti dal settore. I controlli in questa architettura sono della [matrice di controllo cloud][19] (CCM) di [Cloud Security Alliance][18] (CSA) e del benchmark di [Microsoft Azure Foundation][20] (MAFB) di [Center for Internet Security][21] (CIS). Nei controlli applicati, il focus è sui principi di progettazione della sicurezza primari della governance, della rete e della sicurezza delle applicazioni. È responsabilità dell'utente gestire i principi di progettazione di identità, gestione dell'accesso e archiviazione in relazione all'infrastruttura di destinazione.

#### <a name="governance"></a>Governance

L'aspetto principale della governance che questa architettura risolve è la separazione tramite l'isolamento delle risorse di rete. In CCM, DCS-08 consiglia il controllo in ingresso e in uscita per il Data Center. Per soddisfare il controllo, l'architettura usa una progettazione hub e spoke usando gruppi di sicurezza di rete (gruppi) per filtrare il traffico est-ovest tra le risorse. L'architettura filtra anche il traffico tra i servizi centrali nell'hub e le risorse nel spoke. L'architettura usa un'istanza del firewall di Azure per gestire il traffico tra Internet e le risorse all'interno dell'architettura.

L'elenco seguente illustra il controllo che risolve la sicurezza del Data Center in questo riferimento:

| ID controllo CCM CSA | Dominio di controllo CCM CSA |
| :----------------- | :----------------------|
| CONTROLLER DI DOMINIO-08 | Immissione di persone non autorizzate per la sicurezza del Data Center |

#### <a name="network"></a>Rete

La progettazione di rete che supporta questa architettura deriva dal modello di hub e spoke tradizionale. Questa decisione garantisce che l'isolamento rete sia un costrutto di base. Il controllo CCM IV-06 suggerisce che il traffico tra le reti e le macchine virtuali sia limitato e monitorato tra ambienti attendibili e non attendibili. Questa architettura adotta il controllo mediante l'implementazione di gruppi per il traffico East-West e il firewall di Azure per il traffico nord-sud. Il controllo CCM IPY-04 consiglia all'infrastruttura di usare protocolli di rete protetti per lo scambio di dati tra i servizi. I servizi di Azure che supportano questa architettura usano tutti protocolli di sicurezza standard, ad esempio TLS per HTTP e SQL.

L'elenco seguente mostra i controlli CCM che indirizzano la sicurezza di rete in questo riferimento:

| ID controllo CCM CSA | Dominio di controllo CCM CSA |
| :----------------- | :----------------------|
| IPY-04             | Protocolli di rete      |
| IV-06             | Sicurezza di rete       |

L'implementazione della rete è ulteriormente protetta definendo i controlli di MAFB. I controlli assicurano che il traffico nell'ambiente sia limitato dalla rete Internet pubblica.

L'elenco seguente mostra i controlli CIS che indirizzano la sicurezza di rete in questo riferimento:

| ID controllo CIS | Descrizione del controllo CIS |
| :------------- | :---------------------- |
| 6.2 | Assicurarsi che l'accesso SSH sia limitato da Internet. |
| 6.3 | Assicurarsi che nessun database SQL consenta il traffico in ingresso 0.0.0.0/0 (qualsiasi IP). |
| 6.5 | Assicurarsi che Network Watcher sia ' Enabled '. |
| 6.6 | Verificare che il traffico in ingresso con UDP sia limitato da Internet. |

Azure Spring cloud richiede il traffico di gestione per l'uscita da Azure quando viene distribuito in un ambiente protetto. A tale scopo, è necessario consentire la rete e le regole dell'applicazione elencate in [responsabilità del cliente per l'esecuzione di Azure Spring cloud in VNET](./spring-cloud-vnet-customer-responsibilities.md).

#### <a name="application-security"></a>Sicurezza dell'applicazione

Questa entità di progettazione copre i componenti fondamentali dell'identità, la protezione dei dati, la gestione delle chiavi e la configurazione dell'applicazione. Per impostazione predefinita, un'applicazione distribuita nel cloud Spring di Azure viene eseguita con i privilegi minimi necessari per funzionare. Il set di controlli di autorizzazione è direttamente correlato alla protezione dei dati quando si usa il servizio. La gestione delle chiavi rafforza questo approccio a più livelli di protezione delle applicazioni.

L'elenco seguente mostra i controlli CCM che indirizzano la gestione delle chiavi in questo riferimento:

| ID controllo CCM CSA | Dominio di controllo CCM CSA |
| :----------------- | :--------------------- |
| EKM-01 | Diritti di crittografia e gestione delle chiavi |
| EKM-02 | Generazione della chiave di crittografia e gestione delle chiavi |
| EKM-03 | Protezione dei dati sensibili per la crittografia e la gestione delle chiavi |
| EKM-04 | Archiviazione e accesso per la crittografia e la gestione delle chiavi |

Per la gestione delle chiavi e per l'utilizzo di protocolli di crittografia per proteggere i dati sensibili, da CCM, EKM-02 e EKM-03. EKM-01 suggerisce che tutte le chiavi crittografiche hanno proprietari identificabili, in modo che possano essere gestite. EKM-04 consiglia l'uso di algoritmi standard.

L'elenco seguente mostra i controlli CIS che indirizzano la gestione delle chiavi in questo riferimento:

| ID controllo CIS | Descrizione del controllo CIS |
| :------------- | :---------------------- |
| 8.1 | Verificare che la data di scadenza sia impostata su tutte le chiavi. |
| 8.2 | Verificare che la data di scadenza sia impostata su tutti i segreti. |
| 8.4 | Verificare che l'insieme di credenziali delle chiavi sia reversibile. |

I controlli CIS 8,1 e 8,2 raccomandano che le date di scadenza siano impostate per le credenziali per garantire che la rotazione venga applicata. Il controllo CIS 8,4 garantisce che il contenuto dell'insieme di credenziali delle chiavi possa essere ripristinato per mantenere la continuità aziendale.

Gli aspetti della sicurezza dell'applicazione impostano una base per l'uso di questa architettura di riferimento per supportare un carico di lavoro Spring in Azure.

## <a name="next-steps"></a>Passaggi successivi

Esplora questa architettura di riferimento tramite le distribuzioni ARM, di bonifica e dell'interfaccia della riga di comando di Azure disponibili nel repository dell' [architettura di riferimento del cloud Spring di Azure][10] .

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./spring-cloud-tutorial-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./spring-cloud-howto-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/