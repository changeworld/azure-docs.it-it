---
title: Baseline di sicurezza di Azure per Key Vault
description: La Key Vault di sicurezza fornisce indicazioni procedurali e risorse per l'implementazione delle raccomandazioni sulla sicurezza specificate in Azure Security Benchmark.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1f56de94df4fd5d4dd154ae8485edb9eed88364c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753345"
---
# <a name="azure-security-baseline-for-key-vault"></a>Baseline di sicurezza di Azure per Key Vault

Questa baseline di sicurezza applica le indicazioni di [Azure Security Benchmark versione 1.0](../../security/benchmarks/overview-v1.md) a Key Vault. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto è raggruppato in base ai controlli **di** sicurezza definiti da Azure Security Benchmark e alle linee guida correlate applicabili a Key Vault. **I** controlli non applicabili Key Vault, o per i quali la responsabilità è di Microsoft, sono stati esclusi.

Per informazioni sul Key Vault completamente mappato a Azure Security Benchmark, vedere il file di mapping completo Key Vault [baseline di sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno delle reti virtuali

**Linee** guida: integrare Azure Key Vault con collegamento privato di Azure. collegamento privato di Azure Service consente di accedere a Servizi di Azure (ad esempio, Azure Key Vault) e ai servizi clienti/partner ospitati in Azure tramite un endpoint privato nella rete virtuale.

Un endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

- [Come integrare Key Vault con collegamento privato di Azure](/azure/key-vault/private-link-service)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee** guida: usare Centro sicurezza di Azure e seguire le raccomandazioni sulla protezione di rete per proteggere le Key Vault configurate in Azure. 

- [Per altre informazioni sulla sicurezza di rete fornita da Centro sicurezza di Azure](../../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni:** abilitare Protezione DDoS di Azure Standard nelle reti virtuali di Azure associate alle istanze di Key Vault per la protezione da attacchi Denial of Service distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

 
- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](/azure/virtual-network/manage-ddos-protection)

- [Rilevamento delle minacce per il livello di servizio di Azure in Centro sicurezza di Azure](/azure/security-center/security-center-alerts-service-layer)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** questo requisito può essere soddisfatto configurando Advanced Threat Protection (ATP) per Azure Key Vault. ATP offre un livello aggiuntivo di intelligence sulla sicurezza. Questo strumento rileva tentativi potenzialmente dannosi di accesso o exploit Azure Key Vault account.

Quando Centro sicurezza di Azure rileva un'attività anomala, vengono visualizzati avvisi. Invia anche un messaggio di posta elettronica all'amministratore della sottoscrizione con informazioni dettagliate sulle attività sospette e raccomandazioni su come analizzare e correggere le minacce identificate.

- [Configurare Advanced Threat Protection per Azure Key Vault](/azure/security-center/advanced-threat-protection-key-vault)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni:** per le risorse che devono accedere alle istanze di Azure Key Vault, usare i tag di servizio di Azure per l'Azure Key Vault per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nelle Firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Panoramica dei tag del servizio di Azure](../../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni:** definire e implementare configurazioni di sicurezza standard per le risorse di rete associate alle istanze Azure Key Vault con Criteri di Azure. Usare Criteri di Azure alias negli spazi dei nomi "Microsoft.KeyVault" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze Azure Key Vault. È anche possibile usare le definizioni di criteri predefiniti correlate Azure Key Vault, ad esempio:
- Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale

Per altre informazioni, vedere i riferimenti seguenti:

- [Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

- [Esempi di Criteri di Azure](/azure/governance/policy/samples)

- [Definire e assegnare un progetto nel portale](../../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni:** usare i tag per le risorse correlate alla sicurezza di rete e al flusso del traffico per le istanze Azure Key Vault per fornire metadati e organizzazione logica.

Usare le definizioni Criteri di Azure di tag incorporate correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse siano create con tag e per notificare le risorse senza tag.

È possibile usare Azure PowerShell'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze Azure Key Vault rete. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log-view)

- [Creare, visualizzare e gestire gli avvisi del log attività usando Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Azure Security Benchmark: Logging and Monitoring](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni:** inserire log tramite Monitoraggio di Azure aggregare i dati di sicurezza generati da Azure Key Vault. In Monitoraggio di Azure usare l'area di lavoro Azure Log Analytics per eseguire query ed eseguire analisi e usare gli account Archiviazione di Azure per l'archiviazione a lungo termine/di archiviazione. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [Azure Key Vault registrazione](/azure/key-vault/key-vault-logging)

- [Guida introduttiva: Come eseguire l'on-board Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni:** abilitare le impostazioni di diagnostica nelle istanze Azure Key Vault per l'accesso ai log di controllo, sicurezza e diagnostica. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

- [Azure Key Vault registrazione](/azure/key-vault/key-vault-logging)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni:** nell'Monitoraggio di Azure, per l'area di lavoro Log Analytics usata per contenere i log Azure Key Vault, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

- [Cambiare il periodo di conservazione dei dati](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Indicazioni:** analizzare e monitorare i log per individuare comportamenti anomali ed esaminare regolarmente i risultati Azure Key Vault risorse protette. Usare Monitoraggio di Azure'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati di log. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [On-board Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Introduzione a Log Analytics in Monitoraggio di Azure](/azure/azure-monitor/log-query/get-started-portal)

- [Introduzione alle query di log in Monitoraggio di Azure](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anoma le

**Indicazioni:** in Centro sicurezza di Azure abilitare Advanced Threat Protection (ATP) per Key Vault. Abilitare le impostazioni di diagnostica in Azure Key Vault e inviare i log a un'area di lavoro Log Analytics. Eseguire l'onboarding dell'area di lavoro Log Analytics in Azure Sentinel perché fornisce una soluzione risposta automatica di orchestrazione della sicurezza (SOAR). In questo modo è possibile creare playbook (soluzioni automatizzate) e usarle per risolvere i problemi di sicurezza.

- [Avvio rapido: Caricare dati in Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-managing-and-responding-alerts.md)

- [Rispondere agli eventi con gli avvisi di Monitoraggio di Azure](/azure/azure-monitor/learn/tutorial-response)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Azure Security Benchmark: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni:** mantenere un inventario delle applicazioni registrate Azure Active Directory (Azure AD), nonché di tutti gli account utente che hanno accesso a chiavi, segreti e certificati Azure Key Vault. È possibile usare il portale di Azure o PowerShell per eseguire query e riconciliare l Key Vault accesso. Per visualizzare l'accesso in PowerShell, usare il comando seguente:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Properties.AccessPolicies

- [Registrazione di un'applicazione con Azure AD](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Proteggere l'accesso a un insieme di credenziali delle chiavi](security-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni:** creare procedure operative standard per l'uso di account amministrativi dedicati che hanno accesso alle istanze Azure Key Vault servizio. Usare Centro sicurezza di Azure gestione delle identità e degli accessi (attualmente in anteprima) per monitorare il numero di account amministrativi attivi.

- [Monitorare identità e accesso (anteprima)](../../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni:** usare un'entità servizio di Azure in combinazione con AppId, TenantID e ClientSecret per autenticare facilmente l'applicazione e recuperare il token che verrà usato per accedere ai segreti Azure Key Vault sicurezza.

- [Autenticazione da servizio a servizio per Azure Key Vault tramite .NET](/azure/key-vault/service-to-service-authentication)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti Azure Active Directory basato su autenticazione

**Linee** guida: abilitare l'autenticazione Azure Active Directory (Azure AD) a più fattori e seguire le raccomandazioni Centro sicurezza di Azure Identity and Access Management (attualmente in anteprima) per proteggere le risorse abilitate per Hub eventi.

- [Pianificare una Azure AD multi-factor authentication](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitorare identità e accesso (anteprima)](../../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Usare workstation protette gestite da Azure per attività amministrative

**Linee** guida: usare una workstation PAW (Privileged Access Workstation) con Azure AD autenticazione a più fattori configurata per accedere e configurare le Key Vault abilitate.

- [Workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Pianificazione di una distribuzione di autenticazione a più fattori basata Azure AD cloud](../../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrare e avvisare attività sospette da account amministrativi

**Indicazioni:** usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare Azure AD di rischio per visualizzare avvisi e report sul comportamento degli utenti a rischio. Per la registrazione aggiuntiva, inviare Centro sicurezza di Azure avvisi di rilevamento dei rischi Monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando gruppi di azioni.

Abilitare Advanced Threat Protection (ATP) per Azure Key Vault generare avvisi per attività sospette.

- [Distribuire Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Configurare Advanced Threat Protection per Azure Key Vault (anteprima)](/azure/security-center/advanced-threat-protection-key-vault)

- [Avvisi per Azure Key Vault (anteprima)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Azure AD dei rischi](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Creare e gestire gruppi di azione nel portale di Azure](/azure/azure-monitor/platform/action-groups)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni:** configurare la condizione della posizione dei criteri di accesso condizionale e gestire le località denominate. Con le località denominate è possibile creare raggruppamenti logici di intervalli di indirizzi IP o paesi e aree geografiche. È possibile limitare l'accesso alle risorse sensibili, ad esempio i segreti Key Vault, alle posizioni denominate configurate.

- [Qual è la condizione della posizione nell'Azure Active Directory condizionale Azure AD(Azure AD)?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni:** usare Azure Active Directory (Azure AD) come sistema centrale di autenticazione e autorizzazione per le risorse di Azure, ad esempio Key Vault. Ciò consente al controllo degli accessi in base al ruolo di Azure di eseguire l'amministrazione delle risorse sensibili.

- [Creare un nuovo tenant in Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni:** esaminare i Azure Active Directory (Azure AD) per individuare gli account non obsoleti con Azure Key Vault amministrativi. È anche possibile usare le Azure AD di accesso per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere alle Azure Key Vault e le assegnazioni di ruolo. L'accesso degli utenti deve essere verificato regolarmente, ad esempio ogni 90 giorni, per assicurarsi che solo gli utenti con diritti di accesso continuino.

- [Azure AD di report e monitoraggio](/azure/active-directory/reports-monitoring/)

- [Informazioni sulle verifiche di accesso di Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni:** abilitare le impostazioni di diagnostica per Azure Key Vault e Azure Active Directory (Azure AD), inviando tutti i log a un'area di lavoro Log Analytics. Configurare gli avvisi desiderati, ad esempio i tentativi di accesso ai segreti disabilitati, in Log Analytics.

- [Integrare Azure AD log con Monitoraggio di Azure log](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Migrazione dalla soluzione Key Vault precedente](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Deviazione del comportamento di avviso per l'accesso all'account

**Indicazioni:** usare Azure Active Directory funzionalità di identity protection e rilevamento dei rischi di Azure Active Directory (Azure AD) per configurare risposte automatiche alle azioni sospette rilevate correlate alle Azure Key Vault protette. È consigliabile abilitare le risposte automatizzate tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

- [Report di accesso rischioso nel portale Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Procedura: Configurare e abilitare i criteri di rischio](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni:** usare i tag per tenere traccia delle risorse di Azure che archiviano o elaborano informazioni riservate Azure Key Vault abilitate. 

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni:** è possibile proteggere l'accesso Azure Key Vault usando gli endpoint del servizio di rete virtuale configurati per limitare l'accesso a subnet specifiche.

Dopo aver attivato le regole del firewall, è possibile eseguire Azure Key Vault operazioni del piano dati solo quando la richiesta ha origine da subnet consentite o intervalli di indirizzi IP. Questo vale anche per Azure Key Vault'accesso nel portale di Azure. Anche se è possibile passare a un insieme di credenziali delle chiavi dal portale di Azure, potrebbe non essere possibile elencare chiavi, segreti o certificati se il computer client non è in elenco consentito. Ciò influisce anche sulla selezione Azure Key Vault e altri servizi di Azure. È possibile visualizzare elenchi di Insiemi di credenziali delle chiavi, ma non elencare le chiavi, se le regole del firewall impediscono al computer client di eseguire questa operazione.

- [Configurare reti virtuali e firewall di Azure Key Vault](/azure/key-vault/key-vault-network-security)

- [Endpoint servizio di rete virtuale per Azure Key Vault](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee** guida: tutti i dati archiviati Azure Key Vault dati sono considerati sensibili. Usare Azure Key Vault di accesso al piano dati per controllare l'accesso Azure Key Vault segreti. È anche possibile usare Key Vault firewall incorporato di per controllare l'accesso a livello di rete. Per monitorare l'accesso Azure Key Vault, abilitare Key Vault di diagnostica e inviare i log a un account Archiviazione di Azure o all'area di lavoro Log Analytics.

- [Proteggere l'accesso a un insieme di credenziali delle chiavi](security-overview.md)

- [Configurare reti virtuali e firewall di Azure Key Vault](/azure/key-vault/key-vault-network-security)

- [Azure Key Vault registrazione](/azure/key-vault/key-vault-logging)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse

**Indicazioni:** proteggere l'accesso al piano dati e di gestione delle Azure Key Vault istanze.

- [Proteggere l'accesso a un insieme di credenziali delle chiavi](security-overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni:** usare la soluzione Azure Key Vault Analytics in Monitoraggio di Azure esaminare i Azure Key Vault eventi di controllo.

- [Azure Key Vault Analytics in Monitoraggio di Azure](/azure/azure-monitor/insights/azure-key-vault)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Vulnerability Management ( Azure Security Benchmark: Gestione delle vulnerabilità).](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni:** usare le classificazioni di rischio predefinite (Punteggio di sicurezza) fornite da Centro sicurezza di Azure.

- [Migliorare il punteggio di sicurezza in Centro sicurezza di Azure](/azure/security-center/security-center-secure-score)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Azure Security Benchmark: Inventory and Asset Management](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usare una soluzione di individuazione automatica degli asset

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le istanze Azure Key Vault) all'interno della sottoscrizione. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Eseguire la prima query di Resource Graph con Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Ottenere le sottoscrizioni a cui l'account corrente può accedere](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni:** applicare tag alle Azure Key Vault fornendo metadati per organizzarle in modo logico in una tassonomia.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni:** usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, dove appropriato, per organizzare e tenere traccia Azure Key Vault e le risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Creare una sottoscrizione di Azure aggiuntiva](/azure/billing/billing-create-subscription)

- [Creare gruppi di gestione per la gestione e l'organizzazione delle risorse](/azure/governance/management-groups/create)

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee** guida: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri incorporate seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

- [Avvio rapido: Eseguire la prima query di Resource Graph con Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee** guida: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri incorporate seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Per altre informazioni, vedere i riferimenti seguenti:

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

- [Esempi di Criteri di Azure](/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure. Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente a sicurezza elevata, ad esempio quelle con Key Vault configurazione.

- [Gestire l'accesso alla gestione di Azure con accesso condizionale](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Azure Security Benchmark: Secure Configuration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.KeyVault" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze Azure Key Vault personalizzate. È anche possibile usare definizioni di Criteri di Azure per Azure Key Vault, ad esempio:

- Gli oggetti Key Vault devono essere recuperabili

- Distribuisci le impostazioni di diagnostica per Key Vault nell'area di lavoro Log Analytics

- I log di diagnostica in Key Vault devono essere abilitati

- Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale

- Distribuisci le impostazioni di diagnostica per Key Vault nell'hub eventi

- Usare le raccomandazioni Centro sicurezza di Azure come baseline di configurazione sicura per le istanze Azure Key Vault sicurezza.

Per altre informazioni, vedere i riferimenti seguenti:

- [Come visualizzare gli alias Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee** guida: usare Criteri di Azure [deny] e [distribuisci se non esiste] per applicare impostazioni sicure tra le Azure Key Vault abilitate per la distribuzione. 

- [Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

  
- [Informazioni sugli effetti di Criteri di Azure](../../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni:** se si usano definizioni Criteri di Azure personalizzate per le Azure Key Vault abilitate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni:** usare Criteri di Azure alias nello spazio dei nomi "Microsoft.KeyVault" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per le risorse di Azure

**Indicazioni:** usare Centro sicurezza di Azure per eseguire analisi di base per le Azure Key Vault protette.

- [Come correggere le raccomandazioni in Centro sicurezza di Azure](../../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni:** usare l'identità del servizio gestita in combinazione Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud. Assicurarsi che l Azure Key Vault'eliminazione soft sia abilitata.

- [Eseguire l'integrazione con identità gestite di Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Creare un insieme di credenziali delle chiavi](quick-create-portal.md)

- [Eseguire l'autenticazione a Key Vault](authentication.md)

- [Assegnare un criterio Key Vault di accesso](assign-access-policy-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure:** [Azure Security Benchmark è](/azure/governance/policy/samples/azure-security-benchmark) [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [un piano Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni:** usare l'identità del servizio gestita in combinazione Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

  

- [Come eseguire l'integrazione con identità gestite di Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Come creare un Key Vault](quick-create-portal.md)    

- [Come eseguire l'autenticazione Key Vault](authentication.md)

- [Assegnare un criterio Key Vault di accesso](assign-access-policy-portal.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.  
  
- [ Come configurare lo scanner di credenziali](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Azure Security Benchmark: Malware Defense](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Azure Key Vault), ma non viene eseguito sul contenuto del cliente.

Eseguire la pre-analisi di qualsiasi contenuto caricato o inviato a risorse di Azure non di calcolo, ad esempio Azure Key Vault. Microsoft non può accedere ai dati in queste istanze.

- [Informazioni Microsoft Antimalware per Servizi cloud di Azure e macchine virtuali](../../security/fundamentals/antimalware.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Data Recovery](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatizzati regolari

**Linee** guida: assicurarsi di eseguire backup automatizzati regolari Key Vault certificati, chiavi, account di archiviazione gestiti e segreti, con i comandi di PowerShell seguenti:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Facoltativamente, è possibile archiviare i Key Vault backup all'interno Backup di Azure.

- [Come eseguire il backup Key Vault certificati](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Come eseguire il backup Key Vault chiavi](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Come eseguire il backup Key Vault account di archiviazione gestiti](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Come eseguire il backup Key Vault segreti](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Come abilitare Backup di Azure](/azure/backup)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup di sistema completi ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** eseguire backup dei certificati, delle chiavi, degli account di archiviazione gestiti e dei segreti Key Vault, con i comandi di PowerShell seguenti:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Facoltativamente, è possibile archiviare i Key Vault backup all'interno Backup di Azure.

- [Come eseguire il backup Key Vault certificati](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Come eseguire il backup Key Vault chiavi](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Come eseguire il backup Key Vault account di archiviazione gestiti](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Come eseguire il backup Key Vault segreti](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Come abilitare la Backup di Azure](/azure/backup)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** eseguire periodicamente il ripristino dei dati Key Vault certificati, chiavi, account di archiviazione gestiti e segreti con i comandi di PowerShell seguenti:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Per altre informazioni, vedere i riferimenti seguenti:

- [Come ripristinare i Key Vault certificati](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Come ripristinare Key Vault chiavi](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Come ripristinare Key Vault account di archiviazione gestiti](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Come ripristinare i Key Vault segreti](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni:** assicurarsi che l'eliminazione soft sia abilitata per Azure Key Vault. L'eliminazione soft consente il ripristino degli insiemi di credenziali delle chiavi e degli oggetti dell'insieme di credenziali eliminati, ad esempio chiavi, segreti e certificati. 

- [Come usare l Azure Key Vault'eliminazione soft di Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) è [l'iniziativa](/azure/security-center/security-center-recommendations)dei criteri predefinita per il Centro sicurezza ed è alla base delle raccomandazioni del Centro sicurezza. Le Criteri di Azure relative a questo controllo vengono abilitate automaticamente dal Centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere [Azure Defender](/azure/security-center/azure-defender) piano per i servizi correlati.

**Criteri di Azure definizioni incorporate - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto. Questi processi devono concentrarsi sulla protezione dei sistemi sensibili, ad esempio quelli che usano Key Vault segreti.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../../security-center/security-center-planning-and-operations-guide.md)   

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [Il cliente può anche sfruttare la Guida alla gestione degli eventi imprevisti per la sicurezza informatica di NIST per facilitare la creazione del proprio piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sulla metrica usata per emettere l'avviso, nonché sul livello di attendibilità che dietro l'attività era presente una finalità dannosa che ha generato l'avviso. Contrassegnare anche chiaramente le sottoscrizioni, ad esempio produzione, non prod) e creare un sistema di denominazione per identificare e classificare in modo chiaro le risorse di Azure, in particolare quelle che elaborano dati sensibili, ad esempio Azure Key Vault segreti.

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio**: Nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee** guida: eseguire esercizi per testare le funzionalità di risposta agli eventi imprevisti dei sistemi in base a una cadenza regolare per proteggere le istanze Azure Key Vault e le risorse correlate. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni:** esportare gli avvisi Centro sicurezza di Azure e le raccomandazioni usando la funzionalità esportazione continua per identificare i rischi per Azure Key Vault abilitate per le risorse. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo.  È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. 

 

- [Come configurare l'esportazione continua](../../security-center/continuous-export.md) 

  

- [Come trasmettere gli avvisi in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità Automazione del flusso di lavoro in Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" per gli avvisi di sicurezza e le raccomandazioni per proteggere le Azure Key Vault protette da cloud. 

 

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere Azure Security Benchmark: Penetration Tests (Benchmark di sicurezza di [Azure: test di penetrazione) ed Red Team Exercises (Esercizi del Red Team).](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Eseguire regolarmente test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza

**Indicazioni:** seguire le regole di engagement per i test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Centro sicurezza di Azure monitoraggio:** Nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
