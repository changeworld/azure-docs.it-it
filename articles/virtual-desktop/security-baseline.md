---
title: Baseline della sicurezza di Azure per desktop virtuale Windows
description: La linea di base di sicurezza di desktop virtuali di Windows fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 393495dabb77a5d177c97d37313433bb00ce5a36
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726751"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Baseline della sicurezza di Azure per desktop virtuale Windows

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 2,0](../security/benchmarks/overview.md) al desktop virtuale di Windows. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al desktop virtuale di Windows. I **controlli** non applicabili al desktop virtuale Windows sono stati esclusi.

Il servizio desktop virtuale di Windows include il servizio stesso, Windows 10 Enterprise per SKU virtuali a più sessioni, oltre a FSLogix. Per indicazioni sulla sicurezza relative a FSLogix, vedere la [linea di base di sicurezza per l'archiviazione](../storage/common/security-baseline.md). Il servizio dispone di un agente in esecuzione sulle macchine virtuali, ma poiché le macchine virtuali sono sotto il controllo completo del cliente, seguire le [raccomandazioni di sicurezza per il calcolo](../virtual-machines/windows/security-baseline.md)

Per informazioni sul modo in cui il desktop virtuale di Windows viene mappato completamente al benchmark di sicurezza di Azure, vedere il [file di mapping della linea di base di sicurezza desktop completo di Windows](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementare la sicurezza per il traffico interno

**Linee guida**: è necessario creare o usare una rete virtuale esistente quando si distribuiscono macchine virtuali da registrare in un desktop virtuale di Windows. Assicurarsi che tutte le reti virtuali di Azure seguano un principio di segmentazione aziendale che sia allineato ai rischi aziendali. Tutti i sistemi che potrebbero comportare un rischio maggiore per l'organizzazione devono essere isolati all'interno della propria rete virtuale e sufficientemente protetti con un gruppo di sicurezza di rete o un firewall di Azure.

Usare le funzionalità di protezione avanzata della rete adattiva nel centro sicurezza di Azure per consigliare le configurazioni dei gruppi di sicurezza di rete che limitano le porte e gli indirizzi IP di origine con riferimento alle regole del traffico di rete

In base alle applicazioni e alla strategia di segmentazione aziendale, limitare o consentire il traffico tra le risorse interne basate sulle regole del gruppo di sicurezza di rete. Per applicazioni specifiche ben definite, ad esempio un'applicazione a 3 livelli, può trattarsi di un approccio altamente sicuro "Nega per impostazione predefinita, Consenti per eccezione". Questo potrebbe non essere scalabile se si dispone di molte applicazioni ed endpoint che interagiscono tra loro. È anche possibile usare il firewall di Azure in situazioni in cui la gestione centrale è necessaria per un numero elevato di segmenti o spoke aziendali (in una topologia hub/spoke)

Per i gruppi di sicurezza di rete associati alla macchina virtuale, che fanno parte delle subnet di desktop virtuali Windows, è necessario consentire il traffico in uscita verso endpoint specifici. 

- [Scoprire quali URL sono necessari per consentire l'accesso al desktop virtuale Windows](safe-url-list.md)

- [Protezione avanzata della rete adattiva nel centro sicurezza di Azure](../security-center/security-center-adaptive-network-hardening.md) 

- [Firewall di Azure per desktop virtuale Windows ](../firewall/protect-windows-virtual-desktop.md)

- [Come creare un gruppo di sicurezza di rete con le regole di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Come distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: connettere le reti private

**Linee guida**: usare Azure ExpressRoute o la rete privata virtuale di Azure per creare connessioni private tra i Data Center di Azure e l'infrastruttura locale in un ambiente di condivisione percorso. Le connessioni ExpressRoute non passano attraverso la rete Internet pubblica, offrono maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tipiche. 

Per reti private virtuali da punto a sito e da sito a sito, è possibile connettere i dispositivi o le reti locali a una rete virtuale usando qualsiasi combinazione di opzioni di rete privata virtuale e Azure ExpressRoute.

Usare il peering di rete virtuale per connettere due o più reti virtuali in Azure. Il traffico di rete tra reti virtuali con peering è privato e rimane nella rete backbone di Azure.

- [Quali sono i modelli di connettività ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Panoramica della VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteggere le applicazioni e i servizi da attacchi di rete esterni

**Linee guida**: usare il firewall di Azure per proteggere le applicazioni e i servizi da traffico potenzialmente dannoso da Internet e da altre posizioni esterne. Proteggi le tue risorse di desktop virtuali Windows da attacchi di reti esterne, inclusi attacchi di tipo Denial of Service distribuiti, attacchi specifici dell'applicazione, traffico Internet indesiderato e potenzialmente dannoso. Proteggi le tue risorse da attacchi di tipo Denial of Service distribuiti abilitando la protezione standard DDoS nelle tue reti virtuali di Azure. Usare il Centro sicurezza di Azure per rilevare i rischi di configurazione errata correlati alle risorse correlate alla rete.

Desktop virtuale Windows non è progettato per l'esecuzione di applicazioni Web e non richiede la configurazione di impostazioni aggiuntive o la distribuzione di eventuali servizi di rete aggiuntivi per proteggerli da attacchi di rete esterni destinati ad applicazioni Web.

- [Documentazione di Azure firewall](../firewall/index.yml)

- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](../ddos-protection/manage-ddos-protection.md) 

- [Raccomandazioni per il Centro sicurezza di Azure](../security-center/recommendations-reference.md#networking-recommendations)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: distribuire sistemi di rilevamento intrusione/prevenzione intrusioni (IDS/IP)

**Linee guida**: usare il firewall di Azure con il filtro basato su Intelligence per le minacce per inviare avvisi e, facoltativamente, bloccare il traffico verso e da domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. Quando è richiesta l'ispezione del payload, è possibile distribuire una soluzione di rilevamento o prevenzione delle intrusioni di terze parti da Azure Marketplace. 

Se si dispone di requisiti normativi o di altro genere per l'utilizzo della soluzione di rilevamento o prevenzione delle intrusioni, verificare che sia sempre ottimizzato per fornire avvisi di alta qualità alla soluzione di gestione di informazioni ed eventi di sicurezza (SIEM).

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace include le funzionalità degli ID di terze parti](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Funzionalità di Microsoft Defender ATP EDR](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: semplificare le regole di sicurezza di rete

**Indicazioni**: usare i tag del servizio rete virtuale di Azure per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o in un firewall di Azure configurato per le risorse del desktop virtuale di Windows. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio: WindowsVirtualDesktop) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

- [Ulteriori informazioni sugli elementi trattati dal tag servizio desktop virtuale Windows sono disponibili qui ](safe-url-list.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizzare Azure Active Directory come sistema di identità e autenticazione centrale

**Indicazioni**: desktop virtuale di Windows usa Azure Active Directory (Azure ad) come servizio predefinito di gestione delle identità e degli accessi. È necessario standardizzare Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in:

- Microsoft Cloud risorse, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, PaaS e applicazioni SaaS.

- Risorse dell'organizzazione, come le applicazioni in Azure o le risorse della rete aziendale.

La protezione di Azure AD deve essere una priorità nella procedura di sicurezza del cloud dell'organizzazione. Azure AD offre un punteggio di sicurezza delle identità che consente di valutare il comportamento di sicurezza delle identità rispetto alle procedure consigliate di Microsoft. Usare il punteggio per misurare in che modo la configurazione aderisce alle procedure consigliate e per apportare miglioramenti al comportamento di sicurezza.

Azure AD supporta identità esterne che consentono agli utenti senza account Microsoft di accedere alle applicazioni e alle risorse con la loro identità esterna.

- [Tenancy in Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Usare i provider di identità esterne per l'applicazione](../active-directory/external-identities/identity-providers.md)

- [Che cos'è il punteggio di sicurezza delle identità in Azure AD](../active-directory/fundamentals/identity-secure-score.md)

- [Ruoli specifici necessari per il funzionamento del desktop virtuale di Windows ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gestire le identità dell'applicazione in modo sicuro e automatico

**Indicazioni**: desktop virtuale Windows supporta le identità gestite di Azure per account non umani, ad esempio servizi o automazione. È consigliabile usare la funzionalità identità gestita di Azure anziché creare un account umano più potente per accedere o eseguire le risorse. 

Desktop virtuale di Windows consiglia di usare Azure Active Directory (Azure AD) per creare un'entità servizio con autorizzazioni limitate a livello di risorsa per configurare le entità servizio con le credenziali del certificato e per eseguire il fallback ai segreti client. In entrambi i casi, Azure Key Vault può essere usato in combinazione con le identità gestite di Azure, in modo che l'ambiente di runtime (ad esempio, una funzione di Azure) possa recuperare le credenziali dall'insieme di credenziali delle chiavi.

- [Servizi che supportano le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Entità servizio di Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Creare un'entità servizio con certificati](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Usare Azure Key Vault per la registrazione dell'entità di sicurezza](../key-vault/general/authentication.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Usare l'accesso Single Sign-On (SSO) di Azure per accedere alle applicazioni

**Indicazioni**: desktop virtuale di Windows usa Azure Active Directory (Azure ad) per fornire la gestione delle identità e dell'accesso alle risorse di Azure, alle applicazioni cloud e alle applicazioni locali. Sono incluse le identità aziendali, ad esempio i dipendenti, nonché le identità esterne come partner e fornitori. Ciò consente all'accesso Single Sign-On (SSO) di gestire e proteggere l'accesso ai dati e alle risorse dell'organizzazione in locale e nel cloud. Connetti tutti gli utenti, le applicazioni e i tuoi dispositivi a Azure AD per un accesso sicuro senza problemi con maggiore visibilità e controllo.

- [Informazioni sull'accesso Single Sign-On all'applicazione con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Usare i controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory 

**Indicazioni**: desktop virtuale di Windows usa Azure Active Directory (Azure ad), che supporta i controlli di autenticazione avanzata tramite l'autenticazione a più fattori e metodi avanzati per le password.

- Autenticazione a più fattori: abilitare l'autenticazione a più fattori Azure AD e seguire le raccomandazioni per la gestione delle identità e dell'accesso dal centro sicurezza di Azure per alcune procedure consigliate per la configurazione dell'autenticazione a più fattori. L'autenticazione a più fattori può essere applicata a tutti, selezionare gli utenti o a livello di utente in base alle condizioni di accesso e ai fattori di rischio.

- Autenticazione senza password: sono disponibili tre opzioni di autenticazione senza password: Windows Hello for Business, l'app Microsoft Authenticator e i metodi di autenticazione locali come le smart card.

Desktop virtuale Windows supporta l'autenticazione Legacy basata su password, ad esempio account solo cloud (account utente creati direttamente in Azure) che dispongono di criteri password di base o di account ibridi (account utente di Azure AD locali che seguono i criteri password locali). Quando si usa l'autenticazione basata su password, Azure AD fornisce una funzionalità di protezione delle password che impedisce agli utenti di impostare password facili da indovinare. Microsoft offre un elenco globale delle password escluse che vengono aggiornate in base ai dati di telemetria e i clienti possono ampliare l'elenco in base alle esigenze, ad esempio personalizzazione, riferimenti culturali e così via. Questa protezione con password può essere usata solo per gli account cloud e ibridi.

L'autenticazione basata solo sulle credenziali password è soggetta ai metodi di attacco più diffusi. Per una maggiore sicurezza, utilizzare l'autenticazione avanzata, ad esempio l'autenticazione a più fattori e un criterio di password complessa. Per le applicazioni di terze parti e i servizi del Marketplace che possono avere password predefinite, è necessario modificarli durante l'installazione iniziale del servizio.

Per gli amministratori e gli utenti con privilegi, assicurarsi che venga usato il livello più elevato di metodi di autenticazione avanzata, seguito dall'implementazione dei criteri di autenticazione avanzata appropriati ad altri utenti.

- [Introduzione alle opzioni di autenticazione senza password per Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Criteri password predefiniti di Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Eliminare le password non valide usando la protezione Azure Active Directory password](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorare e segnalare le anomalie degli account

**Linee guida**: desktop virtuale di Windows è integrato con Azure Active Directory (Azure ad) che fornisce le origini dati seguenti:

- Accesso: il report di accesso fornisce informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso degli utenti.

- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

- Accesso rischioso: un accesso rischioso è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.

- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Queste origini dati possono essere integrate con monitoraggio di Azure, Azure Sentinel o un sistema SIEM (Security Information and Event Management) di terze parti. Il Centro sicurezza di Azure può anche inviare avvisi relativi a determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o la presenza di account deprecati nella sottoscrizione. Azure Advanced Threat Protection (ATP) è una soluzione di sicurezza che può usare i segnali di Active Directory per identificare, rilevare ed esaminare minacce avanzate, identità compromesse e azioni di utenti interni malintenzionati.

- [Report delle attività di controllo nell'Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Avvisi nel modulo di protezione dell'intelligence sulle minacce del Centro sicurezza di Azure](../security-center/alerts-reference.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Limitare l'accesso alle risorse di Azure in base alle condizioni

**Linee guida**: desktop virtuale Windows supporta l'accesso condizionale con Azure Active Directory (Azure ad) per un controllo di accesso granulare in base alle condizioni definite dall'utente. Ad esempio, gli accessi utente da determinati intervalli IP potrebbero essere necessari per usare l'autenticazione a più fattori per l'accesso. 

Inoltre, i criteri di gestione delle sessioni di autenticazione granulari possono essere usati anche per diversi casi d'uso.

- [Panoramica dell'accesso condizionale di Azure](../active-directory/conditional-access/overview.md) 

- [Criteri di accesso condizionale comuni](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Informazioni sulla configurazione dell'accesso condizionale specifico per desktop virtuale Windows sono disponibili qui](set-up-mfa.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Limitare l'accesso amministrativo ai sistemi business-critical

**Indicazioni**: desktop virtuale Windows usa il controllo degli accessi in base al ruolo di Azure per isolare l'accesso ai sistemi aziendali critici. Assicurarsi di limitare anche l'accesso ai sistemi di gestione, identità e sicurezza che dispongono di accesso amministrativo all'accesso critico per l'azienda, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema con agenti installati in sistemi aziendali critici. Gli utenti malintenzionati che compromettono questi sistemi di gestione e sicurezza possono potenzialmente weaponizerli immediatamente per compromettere asset aziendali critici.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access) 

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Autorizzazioni di amministratore minime necessarie per gestire il desktop virtuale Windows](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Esaminare e riconciliare regolarmente gli accessi utente

**Linee guida**: desktop virtuale di Windows usa gli account di Azure Active Directory (Azure ad) per gestire le risorse, verificare gli account utente e l'assegnazione di accesso regolarmente per assicurarsi che gli account e il loro accesso siano validi.

Usare le verifiche di accesso di Azure AD per verificare l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Azure AD Reporting può fornire log che consentono di individuare gli account obsoleti.

Inoltre, Azure Privileged Identity Management può anche essere configurato in modo da avvisare quando viene creato un numero eccessivo di account amministratore e identificare gli account amministratore non aggiornati o non configurati correttamente.

Alcuni servizi di Azure supportano utenti e ruoli locali che non sono gestiti tramite Azure AD. Sarà necessario gestire questi utenti separatamente.

- [Ruoli predefiniti per desktop virtuale di Windows](rbac.md)

- [Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurare l'accesso di emergenza in Azure AD

**Indicazioni**: desktop virtuale di Windows usa Azure Active Directory (Azure ad) per gestire le proprie risorse. Per evitare che vengano accidentalmente bloccati dall'organizzazione Azure AD, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare gli account amministrativi normali. Gli account di accesso di emergenza sono in genere account con privilegi elevati e non devono essere assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.

È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano conservate in modo sicuro e siano note solo a utenti autorizzati a usarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizzare la gestione dei diritti 

**Linee guida**: desktop virtuale di Windows è integrato con Azure Active Directory (Azure ad) per gestire le proprie risorse. Usare le funzionalità di gestione dei diritti Azure AD per automatizzare i flussi di lavoro delle richieste di accesso, incluse le assegnazioni di accesso, le revisioni e le scadenze. In altre, sono supportate anche le approvazioni duali o in più fasi.

- [Informazioni sulle verifiche di accesso Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Informazioni sulla gestione dei diritti Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Usare le workstation con accesso con privilegi

**Linee guida**: le workstation protette e isolate sono di importanza fondamentale per la sicurezza dei ruoli sensibili, ad esempio, amministratori, sviluppatori e operatori di servizi critici. Usare workstation utente altamente sicure e/o un bastione di Azure per le attività amministrative. 

Usare Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP) o Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. La workstation protetta può essere gestita centralmente per applicare la configurazione protetta, tra cui l'autenticazione avanzata, le linee di base software e hardware, l'accesso logico e di rete limitato.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Distribuire una workstation con accesso con privilegi](/security/compass/privileged-access-deployment)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>7\. Applicare all'amministrazione il principio dei privilegi minimi 

**Indicazioni**: desktop virtuale di Windows è integrato con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire le proprie risorse. Il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite le assegnazioni di ruoli. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Per alcune risorse sono disponibili ruoli predefiniti, che possono essere inventariati o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure. 

I privilegi assegnati alle risorse con il controllo degli accessi in base al ruolo di Azure devono essere sempre limitati a quelli richiesti dai ruoli. Questo complemento è l'approccio JIT (just-in-Time) di Privileged Identity Management (PIM) con Azure Active Directory (Azure AD) e deve essere esaminato periodicamente.

Inoltre, è possibile utilizzare ruoli predefiniti per allocare le autorizzazioni e creare ruoli personalizzati solo quando necessario.

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) 

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Ruoli predefiniti per desktop virtuale di Windows](rbac.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: scegliere il processo di approvazione per il supporto tecnico Microsoft  

**Linee guida**: in scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, desktop virtuale Windows supporta Customer Lockbox per fornire un'interfaccia per esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti.

- [Informazioni Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Individuare, classificare e assegnare un'etichetta ai dati sensibili

**Linee guida**: individuare, classificare e assegnare etichette ai dati sensibili in modo da poter progettare i controlli appropriati. In questo modo, le informazioni riservate vengono archiviate, elaborate e trasmesse in modo sicuro dai sistemi tecnologici dell'organizzazione.

Usare Azure Information Protection (e lo strumento di analisi associato) per informazioni riservate nei documenti di Office in Azure, in locale, Office 365 e in altri percorsi.

È possibile usare Azure SQL Information Protection per semplificare la classificazione e l'assegnazione di etichette alle informazioni archiviate nei database SQL di Azure.

- [Assegnare tag alle informazioni riservate usando Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Come implementare l'individuazione dati SQL di Azure](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteggere i dati sensibili

**Linee guida**: proteggere i dati sensibili limitando l'accesso tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure), i controlli di accesso basati sulla rete e controlli specifici nei servizi di Azure, ad esempio la crittografia in SQL e altri database.

Per garantire un controllo di accesso coerente, tutti i tipi di controllo di accesso devono essere in linea con la strategia di segmentazione aziendale. Questa strategia deve inoltre tenere conto della posizione dei dati e dei sistemi sensibili o business critical.

Microsoft considera tutti i contenuti del cliente come sensibili e protegge dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato alcuni controlli e funzionalità predefiniti per la protezione dei dati.

- [Controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/overview.md) 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>3\. Rilevare il trasferimento non autorizzato di dati sensibili

**Indicazioni**: eseguire un monitoraggio per rilevare trasferimenti non autorizzati dei dati in posizioni al di fuori della visibilità e del controllo aziendali. Ciò comporta in genere il monitoraggio di attività anomale (trasferimenti insoliti o di grandi quantità di dati) che potrebbero indicare un'esfiltrazione di dati non autorizzata.

Le funzionalità di Advanced Threat Protection (ATP) con archiviazione di Azure e Azure SQL ATP possono avvertire un trasferimento anomalo delle informazioni, indicando che cosa potrebbero essere trasferimenti non autorizzati di informazioni riservate.

Azure Information Protection (AIP) fornisce funzionalità di monitoraggio delle informazioni classificate ed etichettate.

Usare soluzioni di prevenzione della perdita dei dati, ad esempio quelle basate su host, per applicare controlli detective e/o preventive per impedire i dati exfiltration.

- [Abilitare Advanced Threat Protection di Azure SQL](../azure-sql/database/threat-detection-overview.md) 

- [Abilitare Advanced Threat Protection di Archiviazione di Azure](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Indicazioni**: assicurarsi che i team responsabili della sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure, in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda del modo in cui sono strutturate le responsabilità del team di sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabile di un team di sicurezza centrale o di un team locale. Fatta questa premessa, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in una posizione centralizzata all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Potrebbero essere necessarie autorizzazioni aggiuntive per la visibilità in carichi di lavoro e servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Assicurarsi che il team di sicurezza abbia accesso all'inventario degli asset e ai metadati

**Linee guida**: applicare tag alle risorse, ai gruppi di risorse e alle sottoscrizioni di Azure per organizzarle in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

Usare l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni sul software nelle macchine virtuali. Il nome del software, la versione, il server di pubblicazione e l'ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere l'accesso alla data di installazione e ad altre informazioni, abilitare la diagnostica a livello di Guest e portare i registri eventi di Windows in un'area di lavoro Log Analytics.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gestione inventario asset del Centro sicurezza di Azure](../security-center/asset-inventory.md) 

- [Guida alle decisioni per la denominazione delle risorse e l'assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Usare solo i servizi di Azure approvati

**Linee guida**: usare criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. È anche possibile usare Monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general) 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>4\. Garantire la sicurezza della gestione del ciclo di vita delle risorse

**Indicazioni**: Non applicabile. Non è possibile usare desktop virtuale Windows per garantire la sicurezza degli asset in un processo di gestione del ciclo di vita. È responsabilità del cliente mantenere gli attributi e le configurazioni di rete degli asset considerati a elevato effetto. 

È consigliabile che il cliente crei un processo per acquisire l'attributo e le modifiche alla configurazione di rete, misuri l'effetto di modifica e crea le attività di correzione, come applicabile.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: usare solo le applicazioni approvate nelle risorse di calcolo

**Indicazioni**: usare l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni su tutto il software nelle macchine virtuali. Il nome del software, la versione, il server di pubblicazione e l'ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere l'accesso alla data di installazione e ad altre informazioni, abilitare la diagnostica a livello di Guest e portare i registri eventi di Windows in un'area di lavoro Log Analytics.

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: abilitazione del rilevamento delle minacce per le risorse di Azure

**Linee guida**: usare la funzionalità di rilevamento delle minacce incorporata nel centro sicurezza di Azure e abilitare Azure Defender (formalmente Azure Advanced Threat Protection) per le risorse del desktop virtuale di Windows. Azure Defender per desktop virtuale di Windows offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento delle risorse di desktop virtuali Windows.

Inviare i log da desktop virtuale Windows alla soluzione SIEM (Security Information Event Management) che può essere usata per configurare il rilevamento delle minacce personalizzato. Assicurarsi di monitorare diversi tipi di risorse di Azure per potenziali minacce e anomalie. Concentrati su come ottenere avvisi di alta qualità per ridurre i falsi positivi per gli analisti. Gli avvisi possono essere originati da dati di log, agenti o altri dati.

- [Protezione dalle minacce nel Centro sicurezza di Azure](../security-center/azure-defender.md) 

- [Guida di riferimento agli avvisi di sicurezza del Centro sicurezza di Azure](../security-center/alerts-reference.md)

- [Creare regole di analisi personalizzate per rilevare le minacce](../sentinel/tutorial-detect-threats-custom.md) 

- [Intelligence per le minacce informatiche con Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

Materiale sussidiario **: Azure Active Directory**(Azure ad) fornisce i log utente seguenti che possono essere visualizzati in Azure ad Reporting o integrati con monitoraggio di Azure, Azure Sentinel o altri strumenti di gestione di informazioni ed eventi di sicurezza (Siem) o strumenti di monitoraggio per i casi d'uso avanzati di monitoraggio e analisi:

- Accesso: il report di accesso fornisce informazioni sull'utilizzo delle applicazioni gestite e delle attività di accesso degli utenti.

- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

- Accesso rischioso: un accesso rischioso è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.

- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Il Centro sicurezza di Azure può anche inviare avvisi su determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti e account deprecati nella sottoscrizione. Oltre al monitoraggio dell'igiene di base per la sicurezza, il modulo di protezione dalle minacce nel centro sicurezza di Azure può anche raccogliere avvisi di sicurezza più approfonditi dalle singole risorse di calcolo di Azure (macchine virtuali, contenitori, servizio app), risorse dati (database SQL e archiviazione) e livelli di servizio di Azure. Questa funzionalità consente di ottenere visibilità sulle anomalie dell'account all'interno delle singole risorse.

- [Report sulle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Abilitare Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Protezione dalle minacce nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Abilitare la registrazione per le attività di rete di Azure

**Linee guida**: desktop virtuale di Windows non produce né elabora i log di query DNS (Domain Name Service). Tuttavia, le risorse registrate per il servizio possono produrre log dei flussi.

Abilitare e raccogliere i log di risorse e di flusso del gruppo di sicurezza di rete, i log del firewall di Azure e i log del Web Application Firewall (WAF) per l'analisi della sicurezza per supportare le indagini sugli incidenti, la ricerca di minacce e la generazione di È possibile inviare i log di flusso a un'area di lavoro di monitoraggio di Azure Log Analytics e quindi usare Analisi del traffico per fornire informazioni dettagliate.

- [Come abilitare i log dei flussi dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Log e metriche di Firewall di Azure](../firewall/logs-and-metrics.md) 

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md) 

- [Soluzioni di monitoraggio di rete di Azure in Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Abilitare la registrazione per le risorse di Azure

**Linee guida**: i log attività, che vengono abilitati automaticamente, contengono tutte le operazioni di scrittura (Put, post, Delete) per le risorse del desktop virtuale di Windows, ad eccezione delle operazioni di lettura (Get). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md) 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Condiviso

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizzare la gestione e l'analisi dei log di sicurezza

**Linee guida**: centralizzare l'archiviazione e l'analisi di registrazione per abilitare la correlazione. Per ogni origine di log, verificare di avere assegnato un proprietario di dati, le linee guida per l'accesso, il percorso di archiviazione, gli strumenti usati per elaborare e accedere ai dati e i requisiti di conservazione dei dati.

Assicurarsi di integrare i log attività di Azure nella registrazione centrale. Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un sistema SIEM (Security Information Event Management) di terze parti. Molte organizzazioni scelgono di usare Sentinel di Azure per i dati "attivi" usati di frequente e archiviazione di Azure per dati "a freddo" usati con minore frequenza.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md) 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

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

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Rilevamento e analisi: creare eventi imprevisti basati su avvisi di alta qualità

**Linee guida**: assicurarsi di avere un processo per la creazione di avvisi di alta qualità e la misurazione della qualità degli avvisi. Questo consente di apprendere dagli eventi imprevisti passati e di assegnare la priorità agli avvisi per gli analisti, in modo da non sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, a origini della community convalidate e a strumenti progettati per generare e pulire gli avvisi unendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità in molte risorse di Azure. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

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

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. La gravità è basata sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: stabilire configurazioni sicure per le risorse di calcolo

**Linee guida**: usare il Centro sicurezza di Azure e i criteri di Azure per stabilire configurazioni sicure per tutte le risorse di calcolo, tra cui macchine virtuali, contenitori e altro.

È possibile usare immagini del sistema operativo personalizzate o la configurazione dello stato di automazione di Azure per stabilire la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione.

- [Come monitorare le raccomandazioni del Centro sicurezza di Azure](../security-center/security-center-recommendations.md) 

- [Panoramica della configurazione dello stato di automazione di Azure](../automation/automation-dsc-overview.md) 

- [Ambiente di Desktop virtuale Windows](environment-setup.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: supportare le configurazioni sicure per le risorse di calcolo

**Linee guida**: usare il Centro sicurezza di Azure e i criteri di Azure per valutare e correggere regolarmente i rischi di configurazione sulle risorse di calcolo di Azure, tra cui macchine virtuali, contenitori e altro. Inoltre, è possibile usare modelli di Azure Resource Manager, immagini del sistema operativo personalizzate o la configurazione dello stato di automazione di Azure per mantenere la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione. I modelli di macchina virtuale Microsoft combinati con la configurazione dello stato di automazione di Azure possono contribuire alla riunione e alla gestione dei requisiti di sicurezza.

Le immagini di macchine virtuali di Azure Marketplace pubblicate da Microsoft vengono gestite e gestite da Microsoft.

Il Centro sicurezza di Azure può anche analizzare le vulnerabilità nell'immagine del contenitore ed eseguire il monitoraggio continuo della configurazione Docker in contenitori con il benchmark Docker di Center Internet Security. È possibile usare la pagina raccomandazioni del Centro sicurezza di Azure per visualizzare le raccomandazioni e correggere i problemi.

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](../security-center/deploy-vulnerability-assessment-vm.md) 

- [Come creare una macchina virtuale di Azure da un modello ARM](../virtual-machines/windows/ps-template.md) 

- [Panoramica della configurazione dello stato di automazione di Azure](../automation/automation-dsc-overview.md) 

- [Sicurezza dei contenitori nel Centro sicurezza](../security-center/container-security.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: archiviare in modo sicuro immagini del sistema operativo e del contenitore personalizzate

**Indicazioni**: desktop virtuale Windows consente ai clienti di gestire le immagini del sistema operativo. Usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per garantire che solo gli utenti autorizzati possano accedere alle immagini personalizzate. Usare una raccolta di immagini condivise di Azure è possibile condividere le immagini a utenti diversi, entità servizio o gruppi di Active Directory all'interno dell'organizzazione. Archiviare le immagini del contenitore in Azure Container Registry e usare il controllo degli accessi in base al ruolo per garantire l'accesso solo agli utenti autorizzati

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Come configurare RBAC di Azure](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Panoramica di raccolta immagini condivise](../virtual-machines/shared-image-galleries.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: eseguire valutazioni delle vulnerabilità del software

**Linee guida**: desktop virtuale Windows consente di distribuire le proprie macchine virtuali e di registrarle nel servizio, oltre a disporre di un database SQL in esecuzione nell'ambiente.

Desktop virtuale Windows può utilizzare una soluzione di terze parti per l'esecuzione di valutazioni delle vulnerabilità su dispositivi di rete e applicazioni Web. Quando si eseguono scansioni remote, non usare un singolo account amministrativo perpetuo. Si consiglia di implementare la metodologia di provisioning JIT per l'account di analisi. Le credenziali per l'account di analisi devono essere protette, monitorate e utilizzate solo per l'analisi delle vulnerabilità.

Come richiesto, esportare i risultati dell'analisi a intervalli coerenti e confrontare i risultati con le analisi precedenti per verificare che le vulnerabilità siano state corrette.

Seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nelle macchine virtuali di Azure e in SQL Server. Il Centro sicurezza di Azure offre uno scanner di vulnerabilità incorporato per la macchina virtuale, le immagini del contenitore e il database SQL.

Se necessario, esportare l'analisi a intervalli coerenti e confrontare i risultati con le analisi precedenti per verificare che le vulnerabilità siano state corrette. Quando si usano le raccomandazioni sulla gestione delle vulnerabilità suggerite dal centro sicurezza di Azure, è possibile passare al portale della soluzione selezionata per visualizzare i dati cronologici dell'analisi.

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](../security-center/deploy-vulnerability-assessment-vm.md) 

- [Scanner di vulnerabilità integrato per le macchine virtuali](../security-center/deploy-vulnerability-assessment-vm.md) 
- [Valutazione della vulnerabilità di SQL](../azure-sql/database/sql-vulnerability-assessment.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>7\. Correggere le vulnerabilità del software in modo rapido e automatico

**Linee guida**: desktop virtuale Windows non USA o richiede software di terze parti. Tuttavia, desktop virtuale Windows consente di distribuire le proprie macchine virtuali e di registrarle nel servizio.

Usare Gestione aggiornamenti di automazione di Azure o una soluzione di terze parti per assicurarsi che gli aggiornamenti della sicurezza più recenti siano installati nelle macchine virtuali Windows Server. Per le macchine virtuali Windows, verificare che Windows Update sia stato abilitato e impostato per l'aggiornamento automatico.

Usare una soluzione di gestione delle patch di terze parti per il software di terze parti o System Center Updates Publisher per Configuration Manager.

- [Come configurare Gestione aggiornamenti per le macchine virtuali in Azure](../automation/update-management/overview.md) 

- [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](../automation/update-management/manage-updates-for-vm.md)

- [Configurare Microsoft endpoint Configuration Manager per desktop virtuale Windows](configure-automatic-updates.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Eseguire una simulazione di attacco regolare

**Indicazioni**: desktop virtuale di Windows non consente ai clienti di eseguire i propri test di penetrazione sulle risorse del desktop virtuale di Windows.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="endpoint-security"></a>Sicurezza degli endpoint

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Endpoint Security](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: usare il rilevamento e la risposta degli endpoint (EDR)

**Linee guida**: desktop virtuale Windows non fornisce funzionalità specifiche per i processi di rilevamento e risposta degli endpoint. Tuttavia, le risorse registrate per il servizio possono trarre vantaggio dalle funzionalità di rilevamento e risposta degli endpoint. 

Abilitare le funzionalità di rilevamento e risposta degli endpoint per server e client e integrarle con le soluzioni di sicurezza e gestione degli eventi e i processi di sicurezza.

Advanced Threat Protection di Microsoft Defender fornisce funzionalità di rilevamento e risposta degli endpoint, come parte di una piattaforma di sicurezza degli endpoint aziendali per prevenire, rilevare, analizzare e rispondere a minacce avanzate.

- [Panoramica di Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Servizio Microsoft Defender ATP per server Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Servizio Microsoft Defender ATP per server non Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Microsoft Defender ATP per infrastrutture desktop virtuali non permanenti](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: utilizzo del software antimalware moderno gestito centralmente

**Linee guida**: Proteggi le tue risorse di desktop virtuali Windows con una soluzione antimalware moderna e gestita a livello centrale in grado di analizzare in tempo reale e periodico.

Il Centro sicurezza di Azure può identificare automaticamente l'uso di una serie di soluzioni antimalware comuni per le macchine virtuali e segnalare lo stato di esecuzione di Endpoint Protection e creare raccomandazioni.

Microsoft antimalware per servizi cloud di Azure è l'anti-malware predefinito per le macchine virtuali (VM) Windows. È anche possibile usare il rilevamento delle minacce con il Centro sicurezza di Azure per i servizi dati per rilevare il malware caricato negli account di archiviazione di Azure.

- [Come configurare Microsoft antimalware per servizi cloud e macchine virtuali](../security/fundamentals/antimalware.md) 

- [Soluzioni di Endpoint Protection supportate](../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: assicurarsi che il software e le firme anti-malware siano aggiornati

**Linee guida**: assicurarsi che le firme anti-malware vengano aggiornate in modo rapido e coerente.

Seguire le raccomandazioni nel centro sicurezza di Azure: " &amp; app di calcolo" per assicurarsi che tutte le macchine virtuali e/o i contenitori siano aggiornati con le firme più recenti.

Per impostazione predefinita, Microsoft antimalware installerà automaticamente le firme e gli aggiornamenti del motore più recenti.

- [Come distribuire Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../security/fundamentals/antimalware.md) 

- [Valutazione e raccomandazioni di Endpoint Protection nel centro sicurezza di Azure](../security-center/security-center-endpoint-protection.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="backup-and-recovery"></a>Backup e ripristino

*Per altre informazioni, vedere [Azure Security Benchmark: Backup e ripristino](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: garantire backup automatici regolari

**Linee guida**: assicurarsi di eseguire il backup dei sistemi e dei dati per mantenere la continuità aziendale dopo un evento imprevisto. Questo deve essere un materiale sussidiario per tutti gli obiettivi per l'obiettivo del punto di ripristino (RPO) e l'obiettivo del tempo di ripristino (RTO).

Abilitare backup di Azure e configurare l'origine di backup, ad esempio macchine virtuali di Azure, SQL Server, database HANA o condivisioni file, nonché la frequenza e il periodo di memorizzazione desiderati.

Per un livello di ridondanza più elevato, è possibile abilitare l'opzione di archiviazione con ridondanza geografica per replicare i dati di backup in un'area secondaria e per eseguire il ripristino tramite il ripristino tra aree.

- [Continuità aziendale e ripristino di emergenza su scala aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Come abilitare backup di Azure](../backup/index.yml) 

- [Come abilitare il ripristino tra aree geografiche](../backup/backup-azure-arm-restore-vms.md#cross-region-restore) 

- [Come configurare un piano di continuità aziendale e ripristino di emergenza in un desktop virtuale Windows](disaster-recovery.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2: crittografare i dati di backup

**Linee guida**: assicurarsi che i backup siano protetti da attacchi. Questa operazione dovrebbe includere la crittografia dei backup per evitare la perdita di riservatezza.

Per il backup regolare dei servizi di Azure, i dati di backup vengono crittografati automaticamente usando le chiavi gestite dalla piattaforma Azure. È possibile scegliere di crittografare il backup usando la chiave gestita dal cliente. In questo caso, verificare che la chiave gestita dal cliente nell'insieme di credenziali delle chiavi sia presente anche nell'ambito di backup.

Usare il controllo degli accessi in base al ruolo in backup di Azure, Azure Key Vault o altre risorse per proteggere i backup e le chiavi gestite dal cliente. Inoltre, è possibile abilitare le funzionalità di sicurezza avanzate per richiedere l'autenticazione a più fattori prima che i backup possano essere modificati o eliminati.

Panoramica delle funzionalità di sicurezza di backup di Azure/Azure/Backup/Security-Overview 

- [Crittografia dei dati di backup tramite chiavi gestite dal cliente](../backup/encryption-at-rest-with-cmk.md) 

- [Come eseguire il backup di chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

- [Funzionalità di sicurezza per proteggere i backup ibridi dagli attacchi](../backup/backup-azure-security-feature.md#prevent-attacks)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Convalidare tutti i backup che includono chiavi gestite dal cliente

**Linee guida**: si consiglia di convalidare l'integrità dei dati sui supporti di backup a intervalli regolari eseguendo un processo di ripristino dei dati per garantire che il backup funzioni correttamente.

- [Come ripristinare i file dal backup della macchina virtuale di Azure](../backup/backup-azure-restore-files-from-vm.md)

- [Implementazione della sicurezza](../backup/backup-azure-restore-files-from-vm.md#security-implementations)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

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

- [Azure Security Benchmark - Gestione delle identità](../automation/update-management/overview.md)

- [Azure Security Benchmark - Accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definire la strategia di registrazione e di risposta alle minacce

**Linee guida**: definire una strategia di registrazione e di risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Definire le priorità offrendo agli analisti avvisi di alta qualità ed esperienze senza problemi, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

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
