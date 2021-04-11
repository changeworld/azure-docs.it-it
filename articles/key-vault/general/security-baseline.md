---
title: Baseline della sicurezza di Azure per Key Vault
description: La linea di base di sicurezza Key Vault fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 305fdd3a0b8e0c876924c6e1f090424e67571af0
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968706"
---
# <a name="azure-security-baseline-for-key-vault"></a>Baseline della sicurezza di Azure per Key Vault

Questa linea di base di sicurezza applica le indicazioni della [versione 1,0 del benchmark di sicurezza di Azure](../../security/benchmarks/overview-v1.md) a Key Vault. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a Key Vault. I **controlli** non applicabili a Key Vault o per i quali la responsabilità è Microsoft, sono stati esclusi.

Per informazioni su come Key Vault viene eseguito il mapping completo al benchmark di sicurezza di Azure, vedere il [file di mapping di base Key Vault sicurezza completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: integrare Azure Key Vault con il collegamento privato di Azure. Il servizio di collegamento privato di Azure consente di accedere ai servizi di Azure (ad esempio Azure Key Vault) e ai servizi cliente/partner ospitati in Azure tramite un endpoint privato nella rete virtuale.

Un endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

- [Come integrare Key Vault con collegamento privato di Azure](/azure/key-vault/private-link-service)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Vault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse configurate Key Vault in Azure. 

- [Per altre informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare la protezione DDoS di Azure standard nelle reti virtuali di Azure associate alle istanze di Key Vault per la protezione da attacchi Denial of Service distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

 
- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](/azure/virtual-network/manage-ddos-protection)

- [Rilevamento delle minacce per il livello di servizio di Azure nel centro sicurezza di Azure](/azure/security-center/security-center-alerts-service-layer)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: questo requisito può essere soddisfatto configurando Advanced Threat Protection (ATP) per Azure Key Vault. ATP fornisce un ulteriore livello di intelligence per la sicurezza. Questo strumento rileva tentativi potenzialmente dannosi di accesso o exploit degli account Azure Key Vault.

Quando il Centro sicurezza di Azure rileva attività anomale, Visualizza gli avvisi. Invia inoltre un e-mail all'amministratore della sottoscrizione con i dettagli dell'attività sospetta e consigli su come analizzare e correggere le minacce identificate.

- [Configurare Advanced Threat Protection per Azure Key Vault](/azure/security-center/advanced-threat-protection-key-vault)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: per le risorse che richiedono l'accesso alle istanze di Azure Key Vault, usare i tag dei servizi di Azure per la Azure Key Vault per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Panoramica dei tag del servizio di Azure](../../virtual-network/service-tags-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate alle istanze di Azure Key Vault con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. codevault" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze del Azure Key Vault. È anche possibile usare le definizioni di criteri predefinite correlate Azure Key Vault, ad esempio:
- Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale

Per altre informazioni, vedere i riferimenti seguenti:

- [Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

- [Esempi di Criteri di Azure](/azure/governance/policy/samples)

- [Definire e assegnare un progetto nel portale](../../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag per le risorse correlate alla sicurezza di rete e al flusso del traffico per le istanze di Azure Key Vault per fornire i metadati e l'organizzazione logica.

Usare le definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di Azure Key Vault. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log-view)

- [Creare, visualizzare e gestire gli avvisi del log attività usando Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati da Azure Key Vault. In monitoraggio di Azure usare l'area di lavoro di Azure Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archivio. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [Registrazione Azure Key Vault](/azure/key-vault/key-vault-logging)

- [Guida introduttiva: How to Board Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica nelle istanze di Azure Key Vault per l'accesso ai log di controllo, sicurezza e diagnostica. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

- [Registrazione Azure Key Vault](/azure/key-vault/key-vault-logging)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Vault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: all'interno di monitoraggio di Azure, per l'area di lavoro log Analytics usata per conservare i log Azure Key Vault, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

- [Cambiare il periodo di conservazione dei dati](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati per le risorse protette con Azure Key Vault. Usare l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [Sentinella di Azure a bordo](../../sentinel/quickstart-onboard.md)

- [Introduzione a Log Analytics in Monitoraggio di Azure](/azure/azure-monitor/log-query/get-started-portal)

- [Introduzione alle query su log in Monitoraggio di Azure](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: nel centro sicurezza di Azure abilitare Advanced Threat Protection (ATP) per Key Vault. Abilitare le impostazioni di diagnostica in Azure Key Vault e inviare i log a un'area di lavoro di Log Analytics. Eseguire l'onboarding dell'area di lavoro Log Analytics in Azure Sentinel perché fornisce una soluzione risposta automatica di orchestrazione della sicurezza (SOAR). In questo modo è possibile creare playbook (soluzioni automatizzate) e usarle per risolvere i problemi di sicurezza.

- [Avvio rapido: Caricare dati in Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-managing-and-responding-alerts.md)

- [Rispondere agli eventi con gli avvisi di Monitoraggio di Azure](/azure/azure-monitor/learn/tutorial-response)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: gestire un inventario delle applicazioni registrate da Azure Active Directory (Azure ad), nonché di tutti gli account utente che hanno accesso alle chiavi Azure Key Vault, i segreti e i certificati. È possibile utilizzare il portale di Azure o PowerShell per eseguire una query e riconciliare l'accesso Key Vault. Per visualizzare l'accesso in PowerShell, usare il comando seguente:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Properties. AccessPolicies

- [Registrazione di un'applicazione con Azure AD](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Proteggere l'accesso a un insieme di credenziali delle chiavi](secure-your-key-vault.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: creare procedure operative standard per l'uso di account amministrativi dedicati che possono accedere alle istanze di Azure Key Vault. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure (attualmente in anteprima) per monitorare il numero di account amministrativi attivi.

- [Monitorare identità e accesso (anteprima)](../../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: usare un'entità servizio di Azure insieme a AppID, TenantId e ClientSecret per autenticare l'applicazione e recuperare il token che verrà usato per accedere ai segreti Azure Key Vault.

- [Autenticazione da servizio a servizio a Azure Key Vault con .NET](/azure/key-vault/service-to-service-authentication)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure (attualmente in anteprima) per proteggere le risorse abilitate per hub eventi.

- [Pianificare una distribuzione di Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitorare identità e accesso (anteprima)](../../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Indicazioni**: usare una workstation con accesso con privilegi (Paw) con Azure ad autenticazione a più fattori configurata per accedere e configurare le risorse abilitate per Key Vault.

- [Workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Pianificazione di una distribuzione di autenticazione a più fattori Azure AD basata sul cloud](../../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente. Usare Azure AD i rilevamenti dei rischi per visualizzare gli avvisi e i report sul comportamento utente rischioso. Per la registrazione aggiuntiva, inviare avvisi di rilevamento dei rischi del Centro sicurezza di Azure in monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

Abilitare Advanced Threat Protection (ATP) per Azure Key Vault generare avvisi per attività sospette.

- [Distribuire Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Configurare Advanced Threat Protection per Azure Key Vault (anteprima)](/azure/security-center/advanced-threat-protection-key-vault)

- [Avvisi per Azure Key Vault (anteprima)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Rilevamento del rischio Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Creare e gestire gruppi di azione nel portale di Azure](/azure/azure-monitor/platform/action-groups)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: configurare la condizione del percorso di un criterio di accesso condizionale e gestire le località denominate. Con le località denominate, è possibile creare raggruppamenti logici di intervalli di indirizzi IP o paesi e aree geografiche. È possibile limitare l'accesso alle risorse sensibili, ad esempio i segreti di Key Vault, alle località denominate configurate.

- [Qual è la condizione di posizione nell'accesso condizionale Azure Active Directory (Azure AD)?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale per le risorse di Azure, ad esempio Key Vault. Questo consente il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per amministrare risorse sensibili.

- [Creare un nuovo tenant in Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: esaminare i registri Azure Active Directory (Azure ad) per individuare gli account obsoleti con Azure Key Vault ruoli amministrativi. Inoltre, è possibile utilizzare le verifiche di accesso Azure AD per gestire in modo efficiente l'appartenenza ai gruppi, accedere alle applicazioni aziendali che possono essere utilizzate per accedere Azure Key Vault e assegnazioni di ruolo. L'accesso utente deve essere esaminato a intervalli regolari, ad esempio ogni 90 giorni, per garantire che solo gli utenti giusti abbiano accesso continuo.

- [Documentazione di Azure AD report e monitoraggio](/azure/active-directory/reports-monitoring/)

- [Informazioni sulle verifiche di accesso di Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: abilitare le impostazioni di diagnostica per Azure Key Vault e Azure Active Directory (Azure ad), inviando tutti i log a un'area di lavoro log Analytics. Configurare gli avvisi desiderati, ad esempio i tentativi di accesso ai segreti disabilitati, all'interno Log Analytics.

- [Integrare log di Azure AD con i log di monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Migrazione dalla soluzione Key Vault precedente](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Linee guida**: usare le funzionalità di protezione delle identità e rilevamento dei rischi di Azure Active Directory (Azure ad) per configurare risposte automatiche per rilevare azioni sospette correlate alle risorse Azure Key Vault protette. È necessario abilitare le risposte automatiche tramite Sentinel di Azure per implementare le risposte di sicurezza dell'organizzazione.

- [Report degli accessi a rischio nel portale di Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Procedura: configurare e abilitare i criteri di rischio](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate sulle risorse abilitate per Azure Key Vault. 

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: è possibile proteggere l'accesso ai Azure Key Vault usando gli endpoint di servizio della rete virtuale configurati per limitare l'accesso a subnet specifiche.

Dopo aver applicato le regole del firewall, è possibile eseguire Azure Key Vault operazioni del piano dati solo quando la richiesta proviene da subnet o intervalli di indirizzi IP consentiti. Questo vale anche per l'accesso Azure Key Vault nell'portale di Azure. Sebbene sia possibile passare a un insieme di credenziali delle chiavi dalla portale di Azure, potrebbe non essere possibile elencare chiavi, segreti o certificati se il computer client non è presente nell'elenco dei consentiti. Ciò influiscono anche sul selettore Azure Key Vault e altri servizi di Azure. È possibile visualizzare elenchi di insiemi di credenziali delle chiavi, ma non di elencare le chiavi, se le regole del firewall impediscono il computer client.

- [Configurare reti virtuali e firewall di Azure Key Vault](/azure/key-vault/key-vault-network-security)

- [Endpoint servizio di rete virtuale per Azure Key Vault](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: tutti i dati archiviati all'interno di Azure Key Vault sono considerati sensibili. Usare i controlli di accesso del piano dati Azure Key Vault per controllare l'accesso ai segreti Azure Key Vault. È anche possibile usare il firewall integrato di Key Vault per controllare l'accesso a livello di rete. Per monitorare l'accesso ai Azure Key Vault, abilitare Key Vault impostazioni di diagnostica e inviare i log a un account di archiviazione di Azure o a Log Analytics area di lavoro.

- [Proteggere l'accesso a un insieme di credenziali delle chiavi](secure-your-key-vault.md)

- [Configurare reti virtuali e firewall di Azure Key Vault](/azure/key-vault/key-vault-network-security)

- [Registrazione Azure Key Vault](/azure/key-vault/key-vault-logging)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usare RBAC di Azure per gestire l'accesso alle risorse

**Linee guida**: accesso sicuro alla gestione e al piano dati delle istanze del Azure Key Vault.

- [Proteggere l'accesso a un insieme di credenziali delle chiavi](secure-your-key-vault.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare la soluzione Azure Key Vault Analytics in monitoraggio di Azure per esaminare Azure Key Vault log eventi di controllo.

- [Soluzione Azure Key Vault Analytics in monitoraggio di Azure](/azure/azure-monitor/insights/azure-key-vault)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure.

- [Migliorare il Punteggio sicuro nel centro sicurezza di Azure](/azure/security-center/security-center-secure-score)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le istanze Azure Key Vault) nella sottoscrizione. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Eseguire la prima query di Resource Graph con Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Ottenere le sottoscrizioni a cui l'account corrente può accedere](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Controllo degli accessi in base al ruolo di Azure (RBAC)](../../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare i tag alle risorse Azure Key Vault assegnare i metadati per organizzarli in modo logico in una tassonomia.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle istanze Azure Key Vault e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Creare una sottoscrizione di Azure aggiuntiva](/azure/billing/billing-create-subscription)

- [Creare gruppi di gestione per la gestione e l'organizzazione delle risorse](/azure/governance/management-groups/create)

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

- [Avvio rapido: Eseguire la prima query di Resource Graph con Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Per altre informazioni, vedere i riferimenti seguenti:

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

- [Esempi di Criteri di Azure](/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure. Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente di sicurezza elevato, ad esempio quelle con Key Vault configurazione.

- [Gestire l'accesso alla gestione di Azure con accesso condizionale](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Vault" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze del Azure Key Vault. È anche possibile usare le definizioni di criteri di Azure predefinite per Azure Key Vault, ad esempio:

- Gli oggetti Key Vault devono essere recuperabili

- Distribuisci le impostazioni di diagnostica per Key Vault nell'area di lavoro Log Analytics

- I log di diagnostica in Key Vault devono essere abilitati

- Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale

- Distribuisci le impostazioni di diagnostica per Key Vault nell'hub eventi

- Usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le istanze di Azure Key Vault.

Per altre informazioni, vedere i riferimenti seguenti:

- [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Deploy if not exist] per applicare impostazioni sicure tra le risorse abilitate per Azure Key Vault. 

- [Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

  
- [Informazioni sugli effetti di Criteri di Azure](../../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni personalizzate di criteri di Azure per le risorse abilitate per la Azure Key Vault, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. insieme di credenziali" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse protette da Azure Key Vault.

- [Come correggere le raccomandazioni nel centro sicurezza di Azure](../../security-center/security-center-remediate-recommendations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud. Assicurarsi che Azure Key Vault l'eliminazione temporanea sia abilitata.

- [Eseguire l'integrazione con le identità gestite di Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Creare un insieme di credenziali delle chiavi](quick-create-portal.md)

- [Eseguire l'autenticazione a Key Vault](authentication.md)

- [Assegnare un criterio di accesso Key Vault](assign-access-policy-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Vault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

  

- [Come eseguire l'integrazione con le identità gestite di Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Come creare una Key Vault](quick-create-portal.md)    

- [Come eseguire l'autenticazione a Key Vault](authentication.md)

- [Assegnare un criterio di accesso Key Vault](assign-access-policy-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.  
  
- [ Come configurare Credential scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Azure Key Vault), tuttavia, non viene eseguito sui contenuti del cliente.

Pre-analizza i contenuti caricati o inviati a risorse di Azure non di calcolo, ad esempio Azure Key Vault. Microsoft non è in grado di accedere ai dati in tali istanze.

- [Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../../security/fundamentals/antimalware.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: garantire backup automatici regolari dei Key Vault certificati, delle chiavi, degli account di archiviazione gestiti e dei segreti, con i comandi di PowerShell seguenti:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Facoltativamente, è possibile archiviare i backup del Key Vault all'interno di backup di Azure.

- [Come eseguire il backup di certificati Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Come eseguire il backup di chiavi di Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Come eseguire il backup di Key Vault account di archiviazione gestiti](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Come eseguire il backup di Key Vault segreti](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Come abilitare backup di Azure](/azure/backup)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: eseguire backup dei certificati Key Vault, delle chiavi, degli account di archiviazione gestiti e dei segreti, con i comandi di PowerShell seguenti:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Facoltativamente, è possibile archiviare i backup del Key Vault all'interno di backup di Azure.

- [Come eseguire il backup di certificati Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Come eseguire il backup di chiavi di Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Come eseguire il backup di Key Vault account di archiviazione gestiti](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Come eseguire il backup di Key Vault segreti](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Come abilitare backup di Azure](/azure/backup)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: eseguire periodicamente il ripristino dei dati di Key Vault certificati, chiavi, account di archiviazione gestiti e segreti con i seguenti comandi di PowerShell:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Per altre informazioni, vedere i riferimenti seguenti:

- [Come ripristinare Key Vault certificati](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Come ripristinare chiavi di Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Come ripristinare Key Vault account di archiviazione gestiti](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Come ripristinare Key Vault segreti](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: assicurarsi che l'eliminazione temporanea sia abilitata per Azure Key Vault. L'eliminazione temporanea consente di recuperare gli insiemi di credenziali delle chiavi e gli oggetti dell'insieme di credenziali eliminati, ad esempio chiavi, segreti e certificati. 

- [Come usare l'eliminazione temporanea di Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. Vault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto. Questi processi devono concentrarsi sulla protezione di sistemi sensibili, ad esempio quelli che usano Key Vault segreti.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../../security-center/security-center-planning-and-operations-guide.md)   

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sulla metrica utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso. Contrassegnare anche chiaramente le sottoscrizioni, ad esempio produzione, non prod) e creazione di un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure, in particolare quelle che elaborano dati sensibili, ad esempio Azure Key Vault segreti.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le istanze di Azure Key Vault e le risorse correlate. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse abilitate per Azure Key Vault. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo.  È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. 

 

- [Come configurare l'esportazione continua](../../security-center/continuous-export.md) 

  

- [Come trasmettere gli avvisi in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse protette da Azure Key Vault. 

 

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
