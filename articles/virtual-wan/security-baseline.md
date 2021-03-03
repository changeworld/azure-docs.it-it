---
title: Baseline della sicurezza di Azure per la rete WAN virtuale
description: La linea di base di sicurezza della WAN virtuale fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e4bbc1565cec2c356e916f813a6a334648d82754
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726734"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Baseline della sicurezza di Azure per la rete WAN virtuale

Questa linea di base di sicurezza applica le indicazioni della [versione 2,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview.md) alla rete WAN virtuale Microsoft Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili alla rete WAN virtuale. I **controlli** non applicabili alla rete WAN virtuale sono stati esclusi.

Per informazioni sul mapping completo della rete WAN virtuale al benchmark di sicurezza di Azure, vedere il [file di mapping della linea di base di sicurezza della rete WAN](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementare la sicurezza per il traffico interno

**Linee guida**: Microsoft Azure rete WAN virtuale fornisce funzionalità di routing personalizzate e offre la crittografia per il traffico ExpressRoute. La gestione delle route viene interamente fornita dal router dell'hub virtuale, che consente anche la connettività di transito tra le reti virtuali. La crittografia del traffico ExpressRoute con la rete WAN virtuale offre un transito crittografato tra le reti locali e le reti virtuali di Azure tramite ExpressRoute, senza passare attraverso la rete Internet pubblica o con indirizzi IP pubblici. 

- [Crittografia del traffico ExpressRoute](virtual-wan-about.md#encryption)

- [USA collegamento privato nella rete WAN virtuale](howto-private-link.md)

- [Scenari di routing personalizzati](scenario-any-to-any.md)

- [Documentazione della tabella di route personalizzata](how-to-virtual-hub-routing.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: connettere le reti private 

**Indicazioni**: Microsoft Azure ExpressRoute offre connettività privata alla rete WAN virtuale di Azure. Poiché le connessioni ExpressRoute non passano attraverso la rete Internet pubblica, ExpressRoute offre maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tipiche. È anche possibile usare una rete privata virtuale per connettersi ad Azure tramite una VPN da sito a sito (S2S) o una VPN da punto a sito (P2S).

- [ExpressRoute nella rete WAN virtuale](virtual-wan-expressroute-portal.md)

- [Panoramica della VPN da sito a sito](virtual-wan-site-to-site-portal.md)

- [Panoramica della VPN da punto a sito](virtual-wan-point-to-site-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteggere le applicazioni e i servizi da attacchi di rete esterni

**Linee guida**: la rete WAN virtuale non espone endpoint a reti esterne per cui è necessario che siano protetti con protezioni di rete convenzionali. È possibile proteggere le risorse in reti virtuali spoke (qualsiasi rete virtuale connessa a un hub virtuale) usando i servizi di protezione della rete virtuale. 

Usare il firewall di Azure per proteggere le applicazioni e i servizi da traffico potenzialmente dannoso da Internet e da altre posizioni esterne. 

Scegli protezione DDoS fornita da Azure per proteggere gli asset da attacchi sulle tue reti virtuali di Azure. Usare il Centro sicurezza di Azure per rilevare i rischi di configurazione errata correlati alle risorse correlate alla rete.

- [Documentazione di Azure firewall](../firewall/index.yml)

- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](../ddos-protection/manage-ddos-protection.md) 

- [Raccomandazioni per il Centro sicurezza di Azure](../security-center/recommendations-reference.md#recs-networking)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: distribuire sistemi di rilevamento intrusione/prevenzione intrusioni (IDS/IP)

**Indicazioni**: la rete WAN virtuale è un servizio gestito da Microsoft. Non offre funzionalità di rilevamento delle intrusioni native o di prevenzione delle intrusioni. Tuttavia, sono disponibili funzionalità di sicurezza per la rete WAN virtuale tramite il firewall di Azure per abilitare un punto di controllo unificato dei criteri. È possibile creare un criterio del firewall di Azure e collegare il criterio a un hub WAN virtuale per consentire all'hub WAN virtuale esistente di funzionare come hub virtuale protetto, con le risorse del firewall di Azure necessarie distribuite.

- [Configurare il Firewall di Azure in un hub della rete WAN virtuale](howto-firewall.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: semplificare le regole di sicurezza di rete

**Indicazioni**: semplificare le regole di sicurezza di rete usando i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. I tag di servizio possono essere usati al posto di indirizzi IP specifici durante la creazione di regole di sicurezza. Specificando il nome del tag del servizio nel campo di origine o di destinazione di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

- [Comprendere e usare i gruppi di sicurezza delle applicazioni](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Documentazione di Azure firewall](../firewall/index.yml)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Secure Domain Name Service (DNS)

**Linee guida**: le funzionalità DNS sicure vengono fornite alla rete WAN virtuale con il firewall di Azure. Configurare il firewall di Azure in modo che funga da proxy DNS che diventa un intermediario per le richieste DNS dalle macchine virtuali client a un server DNS. Per le configurazioni del server DNS personalizzato, abilitare il proxy DNS per evitare una mancata corrispondenza della risoluzione DNS e abilitare il filtro dei nomi di dominio completo nelle regole di rete. 

- [Documentazione di Azure firewall](../firewall/index.yml)

- [Impostazioni DNS del firewall di Azure](../firewall/dns-settings.md)

- [Usare il firewall di Azure come server di trasmissione DNS con collegamento privato](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizzare Azure Active Directory come sistema di identità e autenticazione centrale

**Linee guida**: Azure Active Directory (Azure ad) è il servizio di gestione delle identità e degli accessi predefinito per i servizi di Azure. inclusa la rete WAN virtuale. Standardizzare Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in:

- Microsoft Cloud risorse, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, piattaforma distribuita come servizio (PaaS) e applicazioni Software as a Service (SaaS).
- Le risorse dell'organizzazione, ad esempio le applicazioni in Azure o le risorse della rete aziendale

Proteggi Azure AD come priorità alta nella procedura di sicurezza cloud dell'organizzazione. Valuta la tua identità e il tuo comportamento di sicurezza con la funzionalità di Punteggio di sicurezza del Centro sicurezza per misurare la precisione con cui la configurazione corrisponde alle procedure consigliate di Microsoft. Se necessario, implementare le procedure consigliate di Microsoft per migliorare il comportamento di sicurezza.

Azure AD supporta anche le identità esterne, che consentono agli utenti senza account Microsoft di accedere alle applicazioni e alle risorse con l'identità esterna. 

Esaminare le informazioni sull'uso di Azure AD in scenari VPN da punto a sito nei collegamenti a cui si fa riferimento.

- [Creare un tenant di Azure Active Directory (AD) per le connessioni del protocollo OpenVPN P2S](openvpn-azure-ad-tenant-multi-app.md)

- [Configurare l'autenticazione Azure Active Directory per la VPN utente](virtual-wan-point-to-site-azure-ad.md)

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Usare i controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory

**Linee guida**: attualmente, l'autenticazione Azure Active Directory (Azure ad) viene fornita tramite l'integrazione con la VPN da punto a sito WAN virtuale.

Azure Active Directory (Azure AD) è il servizio di gestione delle identità e degli accessi predefinito per i servizi di Azure. Azure AD supporta controlli di autenticazione avanzata con autenticazione a più fattori e metodi avanzati senza password.

Per i controlli di autenticazione avanzata, Azure AD consiglia quanto segue:

- Autenticazione a più fattori: abilitare l'autenticazione a più fattori Azure AD e seguire le raccomandazioni relative alla gestione delle identità e dell'accesso nel centro sicurezza di Azure per le procedure consigliate per la sicurezza. Applicare l'autenticazione a più fattori in tutti, selezionare gli utenti o il livello per utente in base alle condizioni di accesso e ai fattori di rischio

- Autenticazione con password: sono disponibili tre opzioni di autenticazione con password. Tra cui Windows Hello for business, app Microsoft Authenticator e metodi di autenticazione locali come le smart card

Assicurarsi che il livello più alto del metodo di autenticazione avanzata venga usato per gli amministratori e gli utenti con privilegi, seguito da un implementazione di un criterio di autenticazione avanzata per altri utenti.

- [Come abilitare l'autenticazione a più fattori nella VPN P2S per la rete WAN virtuale](openvpn-azure-ad-mfa.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Limitare l'accesso alle risorse di Azure in base alle condizioni

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) per gli utenti VPN (da punto a sito) con l'uso dell'autenticazione Azure ad. Configurare l'autenticazione a più fattori per ogni utente o sfruttare l'autenticazione a più fattori con accesso condizionale. L'accesso condizionale consente un controllo più granulare sulla modalità di promozione di un secondo fattore. Può consentire l'assegnazione dell'autenticazione a più fattori solo alla VPN ed escludere altre applicazioni legate al tenant Azure AD. 

Si noti che Azure AD autenticazione è disponibile solo per i gateway che usano il protocollo OpenVPN e i client che eseguono Windows.

- [Che cos'è l'accesso condizionale](../active-directory/conditional-access/overview.md)

- [Configurare l'autenticazione Azure Active Directory per la VPN utente](virtual-wan-point-to-site-azure-ad.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminare l'esposizione non intenzionale delle credenziali

**Linee guida**: la VPN da sito a sito in WAN virtuale usa chiavi precondivise (PSK) che vengono individuate, create e gestite dal cliente nel Azure Key Vault. Implementare Credential scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

Per GitHub, è possibile usare la funzionalità di analisi dei segreti nativa per identificare le credenziali o altre forme di segreti all'interno del codice.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Analisi dei segreti di GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Limitare l'accesso amministrativo ai sistemi business-critical

**Indicazioni**: la rete WAN virtuale di Azure usa i controlli degli accessi in base al ruolo di Azure (RBAC di Azure) per isolare l'accesso ai sistemi cruciali per l'azienda limitando a quali account viene concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione in cui si trovano.

È inoltre possibile limitare l'accesso ai sistemi di gestione, identità e sicurezza che dispongono di accesso amministrativo all'accesso critico per l'azienda, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema con agenti installati nei sistemi aziendali critici. Gli utenti malintenzionati che compromettono questi sistemi di gestione e sicurezza possono weaponizerli immediatamente per compromettere asset aziendali critici.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access) 

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Crittografare le informazioni sensibili in transito

**Linee guida**: usare una VPN da punto a sito, una VPN da sito a sito e una route Express crittografata con una rete WAN virtuale per i requisiti di connettività. La crittografia VPN protegge i dati in transito da attacchi fuori banda (ad esempio, l'acquisizione del traffico) per assicurarsi che gli utenti malintenzionati non possano leggere o modificare i dati. 

- [VPN da punto a sito](virtual-wan-point-to-site-portal.md)

- [VPN da sito a sito](virtual-wan-site-to-site-portal.md)

- [ExpressRoute crittografati](vpn-over-expressroute.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Indicazioni**: assicurarsi che i team responsabili della sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure, in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda di come sono strutturate le responsabilità del team responsabile della sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabilità di un team addetto alla sicurezza centrale o di un team locale. In questo modo, le informazioni e i rischi per la sicurezza devono sempre essere aggregati a livello centralizzato all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate in modo esteso a un intero tenant (gruppo di gestione radice) o a gruppi di gestione o sottoscrizioni specifiche. 

Nota: potrebbero essere necessarie anche altre autorizzazioni per ottenere visibilità sui carichi di lavoro e i servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Assicurarsi che il team di sicurezza abbia accesso all'inventario degli asset e ai metadati

**Linee guida**: applicare tag alle risorse, ai gruppi di risorse e alle sottoscrizioni di Azure per organizzarle in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione. La rete WAN virtuale di Azure supporta anche distribuzioni di risorse basate su Azure Resource Manager con cui è possibile esportare i modelli di asset. 

- [Guida alle decisioni per la denominazione delle risorse e l'assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Gestione inventario asset del Centro sicurezza di Azure](../security-center/asset-inventory.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Usare solo i servizi di Azure approvati

**Linee guida**: usare monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato. La rete WAN virtuale riunisce numerose funzionalità di rete, sicurezza e routing per offrire una singola interfaccia operativa. I gateway VPN WAN virtuali, i gateway ExpressRoute e il firewall di Azure hanno la registrazione e le metriche disponibili tramite monitoraggio di Azure. 
 

- [Metriche e log WAN virtuali](logs-metrics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: abilitazione del rilevamento delle minacce per le risorse di Azure

**Linee guida**: la VPN da punto a sito con WAN virtuale è integrata con Azure Active Directory (Azure ad). Azure AD fornisce i log utente seguenti che possono essere visualizzati in Azure AD Reporting o integrati con monitoraggio di Azure, Azure Sentinel, SIEM o strumenti di monitoraggio per i casi d'uso più sofisticati di monitoraggio delle minacce e analisi. Si tratta di:

- Accedi: il report di accesso fornisce informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso degli utenti.
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. Esempi di log di controllo includono le modifiche apportate alle risorse all'interno Azure AD, ad esempio, l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- Accesso rischioso: un accesso rischioso è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.
- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Usare il Centro sicurezza di Azure per creare avvisi su determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti, inclusi gli account deprecati nella sottoscrizione. Oltre al monitoraggio dell'igiene di base per la sicurezza, usare il modulo di protezione dalle minacce del Centro sicurezza per raccogliere avvisi di sicurezza più approfonditi dalle singole risorse di calcolo di Azure (macchine virtuali, contenitori, servizio app), risorse dati (database SQL e archiviazione) e livelli di servizio di Azure. In questo modo è possibile avere visibilità sulle anomalie dell'account all'interno delle singole risorse.

- [Report sulle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Abilitare Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Configurare il Firewall di Azure in un hub della rete WAN virtuale](howto-firewall.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

**Linee guida**: la VPN da punto a sito con WAN virtuale è integrata con Azure Active Directory (Azure ad). Azure AD fornisce i log utente seguenti che possono essere visualizzati in Azure AD Reporting o integrati con monitoraggio di Azure, Azure Sentinel, SIEM o strumenti di monitoraggio per i casi d'uso più sofisticati di monitoraggio delle minacce e analisi. Si tratta di:

- Accedi: il report di accesso fornisce informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso degli utenti.
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. Esempi di log di controllo includono le modifiche apportate alle risorse all'interno Azure AD, ad esempio, l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- Accesso rischioso: un accesso rischioso è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.
- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Usare il Centro sicurezza di Azure per creare avvisi su determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti, inclusi gli account deprecati nella sottoscrizione. Oltre al monitoraggio dell'igiene di base per la sicurezza, usare il modulo di protezione dalle minacce del Centro sicurezza per raccogliere avvisi di sicurezza più approfonditi dalle singole risorse di calcolo di Azure (macchine virtuali, contenitori, servizio app), risorse dati (database SQL e archiviazione) e livelli di servizio di Azure. In questo modo è possibile avere visibilità sulle anomalie dell'account all'interno delle singole risorse.

- [Report sulle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Abilitare Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Configurare il Firewall di Azure in un hub della rete WAN virtuale](howto-firewall.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Abilitare la registrazione per le attività di rete di Azure

**Linee guida**: monitorare la rete WAN virtuale di Azure con monitoraggio di Azure. La rete WAN virtuale riunisce numerose funzionalità di rete, sicurezza e routing per offrire una singola interfaccia operativa. I gateway VPN WAN virtuali, i gateway ExpressRoute e il firewall di Azure hanno la registrazione e le metriche disponibili tramite monitoraggio di Azure. Le voci del log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure. È possibile usare i log attività di Azure (precedentemente noti come log operativi e log di controllo) per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure.

Sono disponibili anche diversi log di diagnostica per la rete WAN virtuale e possono essere configurati per la risorsa WAN virtuale con portale di Azure.  È possibile scegliere di inviare a Log Analytics, trasmettere a un hub eventi o semplicemente archiviarli in un account di archiviazione. 
 

- [Metriche e log WAN virtuali](logs-metrics.md)

- [Log e metriche di Firewall di Azure](../firewall/logs-and-metrics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Abilitare la registrazione per le risorse di Azure

**Linee guida**: i log attività di Azure, abilitati automaticamente, contengono tutte le operazioni di scrittura (Put, post, Delete) per le risorse WAN virtuali di Azure, ad eccezione delle operazioni Read (Get). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

Abilitare i log delle risorse di Azure per la rete WAN virtuale. È possibile usare il Centro sicurezza di Azure e i criteri di Azure per abilitare i log delle risorse e la raccolta dei dati di log. Questi log possono essere fondamentali per analizzare in seguito gli eventi imprevisti della sicurezza ed eseguire esercitazioni forensi.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md) 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/essentials/platform-logs-overview.md) 

- [Informazioni sulla raccolta dati del Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizzare la gestione e l'analisi dei log di sicurezza

**Linee guida**: abilitare la registrazione di sicurezza per la rete WAN virtuale con monitoraggio di Azure. La rete WAN virtuale riunisce numerose funzionalità di rete, sicurezza e routing per offrire una singola interfaccia operativa. I gateway VPN WAN virtuali, i gateway ExpressRoute e il firewall di Azure hanno la registrazione e le metriche disponibili tramite monitoraggio di Azure. Le voci del log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure. È possibile usare i log attività di Azure (precedentemente noti come log operativi e log di controllo) per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure. 

Sono disponibili anche diversi log di diagnostica per la rete WAN virtuale e possono essere configurati per la risorsa WAN virtuale con portale di Azure. Inviare a Log Analytics, trasmettere a un hub eventi o semplicemente archiviarli in un account di archiviazione. Inoltre, abilitare e caricare i dati in Sentinel di Azure o in una soluzione di gestione degli eventi e delle informazioni di sicurezza di terze parti. 
 

- [Metriche e log WAN virtuali](logs-metrics.md)

- [Log e metriche di Firewall di Azure](../firewall/logs-and-metrics.md)

La sicurezza della rete WAN virtuale di Azure viene fornita tramite il firewall di Azure. 

- [Documentazione di Azure firewall](../firewall/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurare la conservazione dell'archiviazione dei log

**Linee guida**: configurare la conservazione dei log in base ai requisiti aziendali, normativi e di conformità. In monitoraggio di Azure è possibile impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare archiviazione di Azure, Data Lake o Log Analytics account dell'area di lavoro per l'archiviazione a lungo termine e di archiviazione.

- [Modificare il periodo di conservazione dei dati in Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Esportazione avvisi e raccomandazioni del Centro sicurezza di Azure](../security-center/continuous-export.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

**Linee guida**: assicurarsi che l'organizzazione includa processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato i processi per Azure e li esegua regolarmente per garantire l'idoneità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparazione: configurare la notifica dell'evento imprevisto

**Linee guida**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Le informazioni di contatto consentono a Microsoft di contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>3\. Rilevamento e analisi: creare eventi imprevisti in base agli avvisi di alta qualità

**Indicazioni**: assicurarsi di disporre di un processo per creare avvisi di alta qualità e misurare la qualità degli avvisi. Questo consente di apprendere dagli eventi imprevisti passati e di assegnare priorità agli avvisi a beneficio degli analisti, che eviteranno così di sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, alle origini di community convalidate e a strumenti progettati per generare e pulire gli avvisi fondendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità per molte risorse di Azure. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione per contribuire a individuare i rischi per le risorse di Azure. È possibile esportare avvisi e raccomandazioni manualmente o in modo continuativo.

- [Come configurare l'esportazione](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>4\. Rilevamento e analisi: esaminare un evento imprevisto

**Indicazioni**: assicurarsi che gli analisti possano eseguire query e usare origini dati diverse durante l'analisi di possibili eventi imprevisti, in modo da creare un quadro completo di ciò che è successo. È necessario raccogliere vari log per tenere traccia delle attività di un possibile utente malintenzionato attraverso la kill chain per evitare punti ciechi.  Assicurarsi anche che le informazioni dettagliate e le nozioni apprese vengano acquisite per poter essere sfruttate da altri analisti e per riferimenti cronologici futuri.  

Le origini dati per l'analisi includono le origini di registrazione centralizzate che sono già state raccolte dai servizi inclusi nell'ambito e dai sistemi in esecuzione, ma possono includere anche:

- Dati di rete: usare i log dei flussi dei gruppi di sicurezza di rete, Azure Network Watcher e Monitoraggio di Azure per acquisire i log dei flussi di rete e altre informazioni di analisi. 

- Snapshot dei sistemi in esecuzione: 

    - Usare la funzionalità snapshot macchina virtuale di Azure per creare uno snapshot del disco del sistema in esecuzione. 

    - Usare la funzionalità di dump della memoria nativa del sistema operativo per creare uno snapshot della memoria del sistema in esecuzione.

    - Usare la funzionalità snapshot dei servizi di Azure o la funzionalità del software in uso per creare snapshot dei sistemi in esecuzione.

Azure Sentinel fornisce analisi approfondite dei dati in qualsiasi origine di log e un portale di gestione dei casi per gestire l'intero ciclo di vita degli eventi imprevisti. Le informazioni di intelligence durante un'analisi possono essere associate a un evento imprevisto a scopo di rilevamento e creazione di report. 

- [Creare uno snapshot del disco di un computer Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Creare uno snapshot del disco di un computer Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Raccolta delle informazioni di diagnostica e del dump della memoria da parte del supporto tecnico di Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Esaminare gli eventi imprevisti con Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Rilevamento e analisi: assegnare la priorità agli eventi imprevisti

**Indicazioni**: fornire un contesto agli analisti per consentire loro di capire su quali eventi imprevisti concentrarsi per primi in base al livello di gravità dell'avviso e di sensibilità delle risorse. 

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità del Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di attendibilità con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha generato l'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenimento, eliminazione e ripristino: automatizzare la gestione degli eventi imprevisti

**Linee guida**: automatizzare le attività ripetitive manuali per ridurre il tempo di risposta e il carico sugli analisti. L'esecuzione delle attività manuali richiede più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che un analista può gestire. Le attività manuali rendono inoltre il lavoro degli analisti più faticoso, aumentando il rischio di errori umani che causano ritardi e compromettendo la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. Usare le funzionalità di automazione dei flussi di lavoro nel Centro sicurezza di Azure e in Azure Sentinel per attivare automaticamente le azioni o eseguire un playbook per rispondere agli avvisi di sicurezza in ingresso. Il playbook esegue azioni come l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione dei flussi di lavoro nel Centro sicurezza](../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel Centro sicurezza di Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="posture-and-vulnerability-management"></a>Gestione del comportamento e della vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione del comportamento e della vulnerabilità](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Eseguire una simulazione di attacco regolare

**Linee guida**: come richiesto, eseguire test di penetrazione o attività di red team sulle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza.
Attenersi alle regole di partecipazione dei test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non violino i criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Test di penetrazione in Azure](../security/fundamentals/pen-testing.md)

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="endpoint-security"></a>Sicurezza degli endpoint

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Endpoint Security](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: usare il rilevamento e la risposta degli endpoint (EDR)

**Indicazioni**: ai clienti non è consentito esplicitamente di configurare le impostazioni di rilevamento e risposta degli endpoint. Tuttavia, le macchine virtuali usate nell'offerta WAN virtuale di Azure usano queste funzionalità. Per ulteriori informazioni su queste funzionalità generali, vedere i collegamenti a cui si fa riferimento. 

- [Panoramica di Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Servizio Microsoft Defender ATP per server Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Servizio Microsoft Defender ATP per server non Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [Azure Security Benchmark: Governance e strategia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definire la strategia di gestione degli asset e di protezione dei dati 

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati. Definire la priorità di individuazione, valutazione, protezione e monitoraggio dei dati e dei sistemi business-critical. 

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Standard di classificazione dei dati in base ai rischi aziendali

-   Visibilità dei rischi e dell'inventario degli asset dell'organizzazione della sicurezza 

-   Approvazione dell'organizzazione della sicurezza dei servizi di Azure da usare 

-   Sicurezza degli asset attraverso il ciclo di vita

-   Strategia di controllo degli accessi conforme alla classificazione dei dati aziendali

-   Uso di funzionalità di protezione dei dati native di Azure e di terze parti

-   Requisiti di crittografia dei dati per i casi d'uso di dati in transito e inattivi

-   Standard crittografici appropriati

Per altre informazioni, vedere i riferimenti seguenti:
- [Raccomandazione sull'architettura della sicurezza di Azure - Archiviazione, dati e crittografia](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Nozioni fondamentali sulla sicurezza di Azure - Sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Procedure consigliate per la sicurezza dei dati e la crittografia in Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - Gestione degli asset](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark - Protezione dei dati](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definire la strategia di segmentazione aziendale 

**Linee guida**: definire una strategia a livello aziendale per segmentare l'accesso agli asset tramite una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Bilanciare accuratamente la necessità di separazione di sicurezza con la necessità di abilitare le operazioni giornaliere dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso e i modelli di accesso e autorizzazione dell'applicazione e i controlli dei processi umani.

- [Linee guida sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Linee guida sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allineare la segmentazione della rete alla strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definire la strategia di gestione del comportamento di sicurezza

**Linee guida**: misurare costantemente e attenuare i rischi per i singoli asset e per l'ambiente in cui sono ospitati. Assegnare la priorità agli asset di valore elevato e alle superfici di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e di uscita della rete, endpoint utente e amministratore e così via.

- [Azure Security Benchmark - Gestione del comportamento e della vulnerabilità](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Allineare i ruoli e le responsabilità dell'organizzazione

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per i ruoli e le responsabilità dell'organizzazione di sicurezza. Definire le priorità specificando una chiara responsabilità per le decisioni relative alla sicurezza, informare tutti gli utenti sul modello di responsabilità condivisa e informare i team tecnici sulla tecnologia per la protezione del cloud.

- [Procedura di sicurezza consigliata di Azure 1 - Utenti: informare i team sul percorso di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedura di sicurezza consigliata di Azure 2 - Utenti: informare i team sulla tecnologia di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedura di sicurezza consigliata di Azure 3 - Processo: assegnare la responsabilità per le decisioni sulla sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definire la strategia della sicurezza di rete

**Linee guida**: definire un approccio di sicurezza di rete di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Gestione centralizzata della rete e responsabilità della sicurezza

-   Modello di segmentazione della rete virtuale allineato alla strategia di segmentazione aziendale

-   Strategia di correzione in diversi scenari di minaccia e attacco

-   Perimetro Internet e strategia di ingresso e uscita

-   Cloud ibrido e strategia di interconnettività locale

-   Artefatti di rete aggiornati, ad esempio diagrammi di rete, architettura di rete di riferimento

Per altre informazioni, vedere i riferimenti seguenti:
- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - Sicurezza di rete](../security/benchmarks/security-controls-v2-network-security.md)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definire la strategia di identità e di accesso con privilegi

**Linee guida**: definire gli approcci di identità e di accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Sistema di identità e autenticazione centralizzato e interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione degli utenti con privilegi elevati

-   Monitoraggio e gestione delle attività utente anomale  

-   Verifica dell'identità e dell'accesso utente e processo di riconciliazione

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark - Accesso con privilegi](../security/benchmarks//security-controls-v2-privileged-access.md)

- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definire la strategia di registrazione e di risposta alle minacce

**Indicazioni**: definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Fornire prima di tutto agli analisti avvisi di alta qualità ed esperienze semplici, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (SecOps) 

-   Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata e informazioni di correlazione su minacce, uso di SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse

-   Uso di piattaforme native di Azure e di terze parti per la gestione degli eventi imprevisti, ad esempio la registrazione e il rilevamento delle minacce, l'analisi e la correzione e l'eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività successive all'evento imprevisto, ad esempio apprendimento e conservazione delle prove

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Registrazione e rilevamento delle minacce](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - Risposta agli eventi imprevisti](../security/benchmarks/security-controls-v2-incident-response.md)

- [Procedura di sicurezza consigliata di Azure 4 - Processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework e guida alle decisioni di registrazione e creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Scalabilità, gestione e monitoraggio di Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)