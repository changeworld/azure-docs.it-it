---
title: Baseline della sicurezza di Azure per analisi di flusso
description: La linea di base di sicurezza di analisi di flusso fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 88e27b839e3f8baf9a1df8a6a6d936aecd602b23
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285115"
---
# <a name="azure-security-baseline-for-stream-analytics"></a>Baseline della sicurezza di Azure per analisi di flusso

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview-v1.md) a analisi di flusso. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a analisi di flusso. 

> [!NOTE]
> I **controlli** non applicabili a analisi di flusso o per i quali la responsabilità è Microsoft sono stati esclusi. Per informazioni sul mapping completo di analisi di flusso al benchmark di sicurezza di Azure, vedere il **[file di mapping della linea di base di analisi di flusso completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/stream-analytics-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: analisi di flusso di Azure non supporta l'uso dei gruppi di sicurezza di rete (NSG) e del firewall di Azure.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: analisi di flusso di Azure non supporta l'uso di reti virtuali e subnet.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche 

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: usare il Centro sicurezza di Azure Threat Protection per rilevare e inviare avvisi sulle comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

- [Protezione dalle minacce per il livello di servizio di Azure nel centro sicurezza di Azure](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: analisi di flusso di Azure non usa i gruppi di sicurezza di rete e i log di flusso per Azure Key Vault non vengono acquisiti.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Indicazioni**: usare il Centro sicurezza di Azure Threat Protection per rilevare operazioni insolite o potenzialmente dannose nell'ambiente della sottoscrizione di Azure.

- [Protezione dalle minacce per il livello di servizio di Azure nel centro sicurezza di Azure](../security-center/azure-defender.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: analisi di flusso di Azure non supporta l'uso di reti virtuali e di regole di rete.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete 

**Linee guida**: analisi di flusso di Azure non supporta l'uso di reti virtuali e di dispositivi di rete.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico 

**Linee guida**: analisi di flusso di Azure non supporta l'uso di reti virtuali e di regole di configurazione del traffico.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche 

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche per le risorse di analisi di flusso. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata 

**Linee guida**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza, ad esempio eventi di controllo e richieste. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare l'archiviazione di Azure accountyfor a lungo termine o archiviazione, facoltativamente con funzionalità di sicurezza come l'archiviazione non modificabile e la conservazione applicata. 

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica nell'analisi di flusso di Azure per l'accesso ai log amministrativi, di sicurezza e di diagnostica. È anche possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log alla stessa area di lavoro Log Analytics o all'account di archiviazione.

- [Analisi di flusso di Azure fornisce i log di diagnostica e i dati delle attività da rivedere](stream-analytics-job-diagnostic-logs.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. StreamAnalytics**:

[!INCLUDE [Resource Policy for Microsoft.StreamAnalytics 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.streamanalytics-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza 

**Linee guida**: quando si archiviano i registri eventi di sicurezza nell'account di archiviazione di Azure o in log Analytics area di lavoro, è possibile impostare i criteri di conservazione in base ai requisiti dell'organizzazione. 

- [Analisi di flusso di Azure fornisce i log di diagnostica e i dati delle attività da rivedere](stream-analytics-job-diagnostic-logs.md)

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

- [Modificare il periodo di conservazione dei dati in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati per le risorse di analisi di flusso. Usare l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Per ulteriori informazioni sull'area di lavoro Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: abilitare le impostazioni di diagnostica per l'analisi di flusso e inviare i log a un'area di lavoro log Analytics. Eseguire l'onboarding dell'area di lavoro Log Analytics in Azure Sentinel perché fornisce una soluzione SOAR (Security Orchestration Automated Response). In questo modo è possibile creare playbook (soluzioni automatizzate) e usarli per risolvere i problemi di sicurezza. 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Come inviare un avviso sui dati del log di log Analytics](../azure-monitor/alerts/tutorial-response.md)  

- [Analisi di flusso di Azure fornisce i log di diagnostica e i dati delle attività da rivedere](stream-analytics-job-diagnostic-logs.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware 

**Linee guida**: non applicabile; Analisi di flusso non elabora o produce log correlati all'anti-malware.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS 

**Indicazioni**: la soluzione Azure analisi DNS (anteprima) in monitoraggio di Azure raccoglie informazioni dettagliate sull'infrastruttura DNS in merito a sicurezza, prestazioni e operazioni. Attualmente non supporta analisi di flusso di Azure, ma è possibile usare una soluzione di registrazione DNS di terze parti. 

- [Raccogliere informazioni dettagliate sull'infrastruttura DNS con la soluzione DNS Analytics (anteprima)](../azure-monitor/insights/dns-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando 

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi 

**Linee guida**: Azure Active Directory (Azure ad) include ruoli predefiniti che devono essere assegnati in modo esplicito. È possibile eseguire query sui ruoli per individuare l'appartenenza. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile 

**Linee guida**: analisi di flusso non ha il concetto di password predefinite perché l'autenticazione viene fornita con Azure Active Directory (Azure ad) e protetta dal controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire il servizio. A seconda dei servizi di flusso di inserimento e di output, è necessario ruotare le credenziali configurate nei processi.

- [Ruotare le credenziali di accesso per gli input e gli output di un processo di analisi di flusso](stream-analytics-login-credentials-inputs-outputs.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati 

**Indicazioni**: creare un piano di sicurezza per la gestione delle identità e i ruoli, seguendo le procedure consigliate, incluso il principio dell'accesso con privilegi minimi per i ruoli di amministratore. Usare Azure Privileged Identity Management (PIM) per fornire l'accesso con privilegi JIT per Azure Active Directory (Azure AD) e le risorse di Azure. Usare gli avvisi e la cronologia di controllo di Azure PIM per monitorare l'attività degli account amministrativi. Utilizzare Azure AD report di sicurezza per identificare gli account amministrativi che potrebbero essere stati compromessi.

- [Scopri di più](/azure/active-directory/privileged-identity-management/)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: laddove possibile, utilizzare Azure Active Directory (Azure ad) SSO anziché configurare credenziali autonome per servizio. Implementare le raccomandazioni per l'accesso alle identità del Centro sicurezza di Azure &amp; .

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure per proteggere le risorse di analisi di flusso.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Indicazioni**: usare le workstation Paw (Privileged Access workstation) con l'autenticazione a più fattori configurata per accedere e configurare le risorse di analisi di flusso.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare i report di sicurezza Azure Active Directory (Azure ad) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: usare percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche. 

- [Come configurare le località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD fornisce il controllo degli accessi in base al ruolo (RBAC di Azure) per un controllo con granularità fine sull'accesso di un client alle risorse di analisi di flusso.

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: esaminare i registri di Azure Active Directory (Azure ad) per individuare gli account obsoleti che possono includere quelli con i ruoli amministrativi dell'account di archiviazione. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente deve essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.

- [Informazioni sulla creazione di report Azure AD](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: abilitare le impostazioni di diagnostica per analisi di flusso di Azure e Azure Active Directory (Azure ad), inviando tutti i log a un'area di lavoro log Analytics. Configurare gli avvisi desiderati, ad esempio i tentativi di accesso ai segreti disabilitati, all'interno Log Analytics.

- [Integrare log di Azure AD con i log di monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Linee guida**: usare le funzionalità di protezione delle identità e dei rischi di Azure Active Directory (Azure ad) per configurare risposte automatiche per rilevare azioni sospette correlate alle risorse di analisi di flusso. È necessario abilitare le risposte automatiche tramite Sentinel di Azure per implementare le risposte di sicurezza dell'organizzazione.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non applicabile; Customer Lockbox non è supportato per analisi di flusso di Azure.

- [Servizi e scenari supportati in disponibilità generale](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per facilitare il rilevamento delle risorse di analisi di flusso che archiviano o elaborano informazioni riservate. 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: isolare i processi di analisi di flusso inserendo gli input, gli output e gli account di archiviazione nella stessa sottoscrizione. È possibile limitare l'analisi di flusso per controllare il livello di accesso alle risorse di analisi di flusso richieste dalle applicazioni e dagli ambienti aziendali. È possibile controllare l'accesso a analisi di flusso di Azure tramite Azure Active Directory (Azure AD) RBAC.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Informazioni sugli input per Analisi di flusso di Azure](stream-analytics-add-inputs.md)

- [Informazioni sugli output di Analisi di flusso di Azure](stream-analytics-define-outputs.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate 

**Indicazioni**: le funzionalità di prevenzione della perdita dei dati non sono ancora disponibili per le risorse di analisi di flusso di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

- [Come proteggere gli account di archiviazione di Azure](../storage/blobs/security-recommendations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: le funzionalità di identificazione dei dati non sono ancora disponibili per le risorse di analisi di flusso di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità. 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare la modalità di interazione degli utenti con il servizio.

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: analisi di flusso non archivia i dati in ingresso perché tutte le operazioni di elaborazione vengono eseguite in memoria.  Tutti i dati privati, incluse le query e le funzioni che devono essere mantenuti da analisi di flusso, vengono archiviati nell'account di archiviazione configurato.  Usare chiavi gestite dal cliente (CMK) per crittografare i dati di output inattivi negli account di archiviazione. Anche senza CMK, analisi di flusso usa automaticamente gli standard di crittografia migliori all'interno dell'infrastruttura per crittografare e proteggere i dati.

- [Protezione dei dati in analisi di flusso di Azure](data-protection.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione di risorse di analisi di flusso di Azure.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: seguire le raccomandazioni del Centro sicurezza di Azure per la protezione delle risorse di analisi di flusso di Azure.

Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano analisi di flusso di Azure.

- [Informazioni sulle raccomandazioni del Centro sicurezza di Azure](../security-center/recommendations-reference.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire la soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure. 

- [Informazioni sul punteggio sicuro del Centro sicurezza di Azure](../security-center/secure-score-security-controls.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di analisi di flusso di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare le risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. StreamAnalytics" per creare criteri personalizzati per controllare o applicare la configurazione di analisi di flusso di Azure. È anche possibile usare le definizioni di criteri predefinite correlate all'analisi di flusso di Azure, ad esempio:

-I log di diagnostica in analisi di flusso di Azure devono essere abilitati

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Definizioni di criteri predefiniti di Criteri di Azure](../governance/policy/samples/built-in-policies.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure Repos per archiviare e gestire in modo sicuro il codice, inclusi criteri personalizzati di Azure, modelli di Azure Resource Manager, script di configurazione dello stato desiderato, funzioni definite dall'utente e query. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Azure AD se integrati con TFS.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. StreamAnalytics" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. StreamAnalytics" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le risorse di analisi di flusso di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: i dettagli della connessione delle risorse di input o di output, usati dal processo di analisi di flusso, vengono archiviati nell'account di archiviazione configurato. Crittografare l'account di archiviazione per proteggere tutti i dati.  Inoltre, ruotare regolarmente le credenziali per l'input o l'output di un processo di analisi di flusso.

- [Protezione dei dati in analisi di flusso di Azure](data-protection.md)

- [Ruotare le credenziali di accesso per input e output in un processo di Analisi di flusso](stream-analytics-login-credentials-inputs-outputs.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: autenticazione dell'identità gestita per l'output consente ai processi di analisi di flusso di accedere direttamente al servizio, tra cui Power bi, account di archiviazione, anziché usare una stringa di connessione.

- [Autenticare analisi di flusso per Azure Data Lake Storage Gen1 usando identità gestite](stream-analytics-managed-identities-adls.md)

- [Usare l'identità gestita per autenticare il processo di analisi di flusso di Azure nell'output di archiviazione BLOB di Azure](blob-output-managed-identity.md)

- [Usare l'identità gestita per autenticare il processo di analisi di flusso di Azure per Power BI](powerbi-output-managed-identity.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usare il software antimalware gestito centralmente

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio analisi di flusso di Azure, ma non viene eseguito sui contenuti del cliente.

Pre-analisi di tutti i contenuti caricati nelle risorse di Azure, ad esempio servizio app, analisi di flusso, archiviazione BLOB e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: assicurarsi che le firme e il software antimalware siano aggiornati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: in base al tipo di servizio di output selezionato. è possibile eseguire backup automatici dei dati di output in base alle linee guida consigliate per il servizio di output.  I dati interni, incluse le funzioni definite dall'utente, le query e gli snapshot dei dati, vengono archiviati nell'account di archiviazione configurato di cui è possibile eseguire il backup a intervalli regolari.

I dati nell'account di archiviazione Microsoft Azure vengono sempre replicati automaticamente per assicurare durabilità e disponibilità elevata. Archiviazione di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. È possibile scegliere di replicare i dati nello stesso data center, tra data center di zona nella stessa area o tra aree separate geograficamente. 

È anche possibile usare la funzionalità di gestione del ciclo di vita per eseguire il backup dei dati nel livello archivio.  Inoltre, abilitare l'eliminazione temporanea per i backup archiviati nell'account di archiviazione.

- [Protezione dei dati in analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [Informazioni sulla ridondanza di archiviazione di Azure e sui contratti di Service-Level](../storage/common/storage-redundancy.md)

- [Gestire il ciclo di vita di Archiviazione BLOB di Azure](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: i dati interni, incluse le funzioni definite dall'utente, le query e gli snapshot dei dati, vengono archiviati nell'account di archiviazione configurato di cui è possibile eseguire il backup a intervalli regolari.

Per eseguire il backup dei dati dai servizi supportati dall'account di archiviazione, sono disponibili diversi metodi, tra cui l'uso di azcopy o di strumenti di terze parti. L'archiviazione non modificabile per BLOB di Azure consente agli utenti di archiviare oggetti dati critici per l'azienda nello stato WORM (Write Once, Read Many). Questo stato rende i dati non cancellabili e non modificabili per un intervallo di tempo specificato dall'utente.

- [Protezione dei dati in analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [Introduzione ad AzCopy](../storage/common/storage-use-azcopy-v10.md)

- [Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB](../storage/blobs/storage-blob-immutability-policies-manage.md)

È possibile eseguire il backup delle chiavi gestite/fornite dal cliente all'interno Azure Key Vault usando l'interfaccia della riga di comando di Azure o PowerShell.

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: eseguire periodicamente il ripristino dei dati di backup per verificare l'integrità dei dati.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: i backup di analisi di flusso archiviati in archiviazione di Azure supportano la crittografia per impostazione predefinita e non possono essere disattivati.  È consigliabile gestire i backup come dati sensibili e applicare i controlli di accesso e protezione dei dati rilevanti come parte di questa linea di base.  

- [Protezione dei dati in analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [Autorizzazione dell'accesso ai dati in archiviazione di Azure](../storage/common/storage-auth.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sulla metrica utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso. 

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Indicazioni**: seguire le regole Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
