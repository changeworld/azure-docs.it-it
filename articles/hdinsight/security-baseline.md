---
title: Baseline della sicurezza di Azure per HDInsight
description: La linea di base di sicurezza HDInsight fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf2360bda55735aa8ef4258da5ae47f673f4d71b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738923"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Baseline della sicurezza di Azure per HDInsight

Questa linea di base di sicurezza applica le indicazioni della [versione 1,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview-v1.md) a HDInsight. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a HDInsight. I **controlli** non applicabili a HDInsight sono stati esclusi.

 
Per informazioni sul modo in cui HDInsight esegue il mapping completo al benchmark di sicurezza di Azure, vedere il [file di mapping di base della sicurezza HDInsight](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: la sicurezza perimetrale in Azure HDInsight viene eseguita tramite reti virtuali. Un amministratore dell'organizzazione può creare un cluster all'interno di una rete virtuale e usare un gruppo di sicurezza di rete (NSG) per limitare l'accesso alla rete virtuale. Solo gli indirizzi IP consentiti nelle regole del gruppo di sicurezza di rete in ingresso saranno in grado di comunicare con il cluster HDInsight di Azure. Questa configurazione offre sicurezza perimetrale. Tutti i cluster distribuiti in una rete virtuale avranno anche un endpoint privato che viene risolto in un indirizzo IP privato all'interno della rete virtuale per l'accesso HTTP privato ai gateway del cluster.

Per ridurre il rischio di perdita di dati tramite exfiltration, limitare il traffico di rete in uscita per i cluster Azure HDInsight usando il firewall di Azure.

- [Come distribuire Azure HDInsight all'interno di una rete virtuale e proteggere con un gruppo di sicurezza di rete](hdinsight-create-virtual-network.md)

- [Come limitare il traffico in uscita per i cluster HDInsight di Azure con il firewall di Azure](hdinsight-restrict-outbound-traffic.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: usare il Centro sicurezza di Azure e correggere i consigli sulla protezione della rete per la rete virtuale, la subnet e il gruppo di sicurezza di rete usato per proteggere il cluster HDInsight di Azure. Abilitare i log di flusso del gruppo di sicurezza di rete (NSG) e inviare i log in un account di archiviazione di Azure al controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro di Azure Log Analytics e usare Azure Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Azure Analisi del traffico sono la possibilità di visualizzare le attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate di rete.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Azure Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: per le protezioni da attacchi DDoS, abilitare la protezione standard DDoS di Azure nella rete virtuale in cui è distribuita la HDInsight di Azure. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi noti o non usati.

- [Come configurare la protezione DDoS](/azure/virtual-network/manage-ddos-protection)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](/azure/security-center/security-center-alerts-service-layer)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: abilitare i log dei flussi del gruppo di sicurezza di rete (NSG) per il NSG collegato alla subnet usata per proteggere il cluster HDInsight di Azure. Registrare i log dei flussi di NSG in un account di archiviazione di Azure per generare record di flusso. Se necessario per l'analisi dell'attività anomala, abilitare l'acquisizione di pacchetti di Azure Network Watcher.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: è possibile soddisfare il requisito ID di controllo di sicurezza di Azure 1,1; Distribuire il cluster HDInsight di Azure in una rete virtuale e proteggerlo con un gruppo di sicurezza di rete (NSG).

Sono disponibili diverse dipendenze per Azure HDInsight che richiedono il traffico in ingresso. Il traffico di gestione in ingresso non può essere inviato attraverso un dispositivo firewall. Gli indirizzi di origine per il traffico di gestione necessario sono noti e pubblicati. Creare regole del gruppo di sicurezza di rete con queste informazioni per consentire il traffico solo da percorsi attendibili, proteggendo il traffico in ingresso verso i cluster.

Per ridurre il rischio di perdita di dati tramite exfiltration, limitare il traffico di rete in uscita per i cluster Azure HDInsight usando il firewall di Azure.

- [Come distribuire HDInsight in una rete virtuale e proteggere con un gruppo di sicurezza di rete](hdinsight-create-virtual-network.md)

- [Informazioni sulle dipendenze di HDInsight e sull'utilizzo del firewall](hdinsight-restrict-outbound-traffic.md)

- [Indirizzi IP di gestione di HDInsight](hdinsight-management-ip-addresses.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete (NSG) collegati alla subnet in cui è distribuito il cluster Azure HDInsight. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio per Azure HDInsight](/azure/virtual-network/security-overview#service-tags)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete correlate al cluster HDInsight di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. HDInsight" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete del cluster Azure HDInsight.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controlli RBAC di Azure e criteri, in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come visualizzare gli alias dei criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag per il gruppo di sicurezza di rete (gruppi) e altre risorse correlate alla sicurezza di rete e al flusso del traffico associati al cluster HDInsight di Azure. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle distribuzioni HDInsight di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log-view)

- [Come creare avvisi in Monitoraggio di Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: è possibile caricare il cluster HDInsight di Azure in monitoraggio di Azure per aggregare i dati di sicurezza generati dal cluster. Utilizzare query personalizzate per rilevare e rispondere alle minacce nell'ambiente. 

- [Come eseguire l'onboarding di un cluster HDInsight di Azure in monitoraggio di Azure](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Come creare query personalizzate per un cluster Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare monitoraggio di Azure per il cluster HDInsight, indirizzarlo a un'area di lavoro log Analytics. Questa operazione registrerà le informazioni rilevanti sul cluster e le metriche del sistema operativo per tutti i nodi del cluster HDInsight di Azure.

- [Come abilitare la registrazione per il cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Come eseguire query nei log di HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida**: caricare il cluster Azure HDInsight in monitoraggio di Azure. Verificare che l'area di lavoro Log Analytics utilizzata includa il periodo di conservazione del log impostato in base alle normative di conformità dell'organizzazione.

- [Come eseguire l'onboarding di un cluster HDInsight di Azure in monitoraggio di Azure](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Come configurare il periodo di conservazione dell'area di lavoro Log Analytics](/azure/azure-monitor/platform/manage-cost-storage)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: caricare il cluster Azure HDInsight in monitoraggio di Azure. Assicurarsi che l'area di lavoro di Azure Log Analytics utilizzata disponga del periodo di memorizzazione del log impostato in base alle normative di conformità dell'organizzazione.

- [Come eseguire l'onboarding di un cluster HDInsight di Azure in monitoraggio di Azure](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Come configurare il periodo di conservazione dell'area di lavoro Log Analytics](/azure/azure-monitor/platform/manage-cost-storage)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: usare query dell'area di lavoro di Azure log Analytics per eseguire query nei log di Azure HDInsight:

- [Come creare query personalizzate per i cluster HDInsight di Azure](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: usare l'area di lavoro di Azure log Analytics per il monitoraggio e l'invio di avvisi sulle attività anomale nei registri di sicurezza e negli eventi correlati al cluster HDInsight di Azure.

- [Come gestire gli avvisi nel centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come inviare un avviso sui dati del log di log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: Azure HDInsight viene fornita con clamscan preinstallato e abilitato per le immagini del nodo del cluster, tuttavia è necessario gestire il software e aggregare/monitorare manualmente i log prodotti da clamscan.

- [Informazioni su clamscan](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: implementare una soluzione di terze parti per la registrazione DNS.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: configurare manualmente la registrazione della console in base ai singoli nodi.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: mantenere il record dell'account amministrativo locale creato durante il provisioning del cluster del cluster HDInsight di Azure e di qualsiasi altro account creato dall'utente. Inoltre, se viene utilizzata l'integrazione Azure Active Directory (Azure AD), Azure AD dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e pertanto possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

Inoltre, è possibile usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni**: durante il provisioning di un cluster, Azure richiede la creazione di nuove password per il portale Web e l'accesso Secure Shell (SSH). Non sono disponibili password predefinite da modificare, ma è possibile specificare password diverse per l'accesso SSH e del portale Web.

- [Come impostare le password durante il provisioning di un cluster Azure HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: integrare l'autenticazione per il cluster Azure HDInsight con Azure Active Directory (Azure ad). Creare criteri e procedure per l'utilizzo di account amministrativi dedicati.

Inoltre, è possibile usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

- [Come integrare l'autenticazione HDInsight di Azure con Azure AD](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: usare Azure HDInsight ID Broker per accedere ai cluster Enterprise Security Package (ESP) usando l'autenticazione a più fattori, senza fornire alcuna password. Se è già stato effettuato l'accesso ad altri servizi di Azure, ad esempio la portale di Azure, è possibile accedere al cluster Azure HDInsight con un'esperienza di Single Sign-On (SSO).

- [Come abilitare Azure HDInsight ID Broker](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure. I cluster HDInsight di Azure con la Enterprise Security Package configurata possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali di dominio per l'autenticazione con i cluster ed eseguire Big Data processi. Quando si esegue l'autenticazione con l'autenticazione a più fattori abilitata, agli utenti verrà richiesto di fornire un secondo fattore di autenticazione.

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare le workstation Paw (Privileged Access workstation) con l'autenticazione a più fattori configurata per accedere e configurare i cluster HDInsight di Azure e le risorse correlate.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: i cluster HDInsight di Azure con il Enterprise Security Package configurato possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali di dominio per l'autenticazione. È possibile utilizzare i report di sicurezza Azure Active Directory (Azure AD) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente Azure AD. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Come monitorare l'identità degli utenti e l'attività di accesso nel centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: i cluster HDInsight di Azure con il Enterprise Security Package configurato possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali di dominio per l'autenticazione. Usare i percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

I cluster HDInsight di Azure con Enterprise Security Package (ESP) configurati possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali di dominio per l'autenticazione con i cluster.

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Come configurare Enterprise Security Package con Azure AD Domain Services in Azure HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Linee guida**: usare l'autenticazione Azure Active Directory (Azure ad) con il cluster HDInsight di Azure. Azure AD fornisce i log per individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: usare i log di accesso e di controllo di Azure Active Directory (Azure ad) per monitorare i tentativi di accesso agli account disattivati; Questi log possono essere integrati in qualsiasi strumento di SIEM/monitoraggio di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente, inviando i log di controllo e i log di accesso a un'area di lavoro di Azure Log Analytics. Configurare gli avvisi desiderati nell'area di lavoro di Azure Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Linee guida**: i cluster Azure HDInsight con Enterprise Security Package (ESP) configurati possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali di dominio per l'autenticazione con i cluster. Usare Azure Active Directory (Azure AD) rilevamento del rischio e la funzionalità di protezione delle identità per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. Inoltre, è possibile inserire dati in Sentinel di Azure per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non disponibile; Customer Lockbox non ancora supportata per Azure HDInsight.

- [Elenco dei servizi Customer Lockbox supportati](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag sulle risorse correlate alle distribuzioni HDInsight di Azure per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. I cluster HDInsight di Azure e tutti gli account di archiviazione associati devono essere separati da rete virtuale/subnet, contrassegnati in modo appropriato e protetti in un gruppo di sicurezza di rete (NSG) o in un firewall di Azure. I dati del cluster devono essere contenuti in un account di archiviazione di Azure protetto o Azure Data Lake Storage (Gen1 o Gen2).

- [Scegliere le opzioni di archiviazione per il cluster Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)

- [Come proteggere Azure Data Lake Storage](../data-lake-store/data-lake-store-security-overview.md)

- [Come proteggere gli account di archiviazione di Azure](/azure/storage/common/storage-security-guide)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: per i cluster HDInsight di Azure che archiviano o elaborano informazioni riservate, contrassegnare il cluster e le risorse correlate come sensibili usando i tag. Per ridurre il rischio di perdita di dati tramite exfiltration, limitare il traffico di rete in uscita per i cluster Azure HDInsight usando il firewall di Azure.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Come limitare il traffico in uscita per i cluster HDInsight di Azure con il firewall di Azure](hdinsight-restrict-outbound-traffic.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono al cluster HDInsight o agli archivi dati cluster di Azure (account di archiviazione di Azure o Azure Data Lake Storage Gen1/Gen2) siano in grado di negoziare TLS 1,2 o versione successiva. Microsoft Azure risorse negozieranno TLS 1,2 per impostazione predefinita. 

- [Informazioni sulla crittografia Azure Data Lake Storage in transito](../data-lake-store/data-lake-store-security-overview.md)

- [Informazioni sulla crittografia dell'account di archiviazione di Azure in transito](../storage/blobs/security-recommendations.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per le risorse di calcolo o di archiviazione di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse 

**Linee guida**: con Azure HDInsight Enterprise Security Package (ESP), è possibile usare Apache Ranger per creare e gestire i criteri granulari di controllo degli accessi e di offuscamento dei dati per i dati archiviati in file, cartelle, database, tabelle e righe/colonne. L'amministratore di Hadoop può configurare il controllo degli accessi in base al ruolo (RBAC) per proteggere Apache Hive, HBase, Kafka e Spark usando questi plug-in Apache Ranger.

La configurazione dei criteri RBAC con Apache Ranger consente di associare le autorizzazioni a un ruolo nell'organizzazione. Con questo livello di astrazione risulta ancor più semplice assicurarsi che le persone dispongano solo delle autorizzazioni necessarie per svolgere le proprie responsabilità lavorative.

- [Enterprise Security Package configurazioni con servizi di dominio Azure Active Directory (Azure AD) in HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Panoramica della sicurezza aziendale in Azure HDInsight](domain-joined/hdinsight-security-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: per i cluster HDInsight di Azure che archiviano o elaborano informazioni riservate, contrassegnare il cluster e le risorse correlate come sensibili usando i tag. Le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per le risorse di archiviazione o di calcolo di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: se si usa il database SQL di Azure per archiviare i metadati di Apache hive e Apache oozie, assicurarsi che i dati SQL rimangano sempre crittografati. Per gli account di archiviazione di Azure e Data Lake Storage (Gen1 o Gen2), è consigliabile consentire a Microsoft di gestire le chiavi di crittografia, ma è possibile gestire le proprie chiavi.

- [Come gestire le chiavi di crittografia per gli account di archiviazione di Azure](/azure/storage/common/storage-encryption-keys-portal)

- [Come creare Azure Data Lake Storage usando chiavi di crittografia gestite dal cliente](../data-lake-store/data-lake-store-get-started-portal.md)

- [Informazioni sulla crittografia per il database SQL di Azure](/azure/sql-database/sql-database-technical-overview#data-encryption)

- [Come configurare Transparent Data Encryption per il database SQL usando chiavi gestite dal cliente](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: configurare le impostazioni di diagnostica per gli account di archiviazione di Azure associati ai cluster HDInsight di Azure per monitorare e registrare tutte le operazioni CRUD sui dati del cluster. Abilitare il controllo per tutti gli account di archiviazione o gli archivi di Data Lake associati al cluster HDInsight di Azure.

- [Come abilitare la registrazione o il controllo aggiuntivo per un account di archiviazione di Azure](/azure/storage/common/storage-monitor-storage-account)

- [Come abilitare la registrazione o il controllo aggiuntivi per Azure Data Lake Storage](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: Implementare una soluzione di gestione delle vulnerabilità di terze parti.

Facoltativamente, se si dispone di un Rapid7, Qualys o qualsiasi altra sottoscrizione della piattaforma di gestione delle vulnerabilità, è possibile usare le azioni script per installare gli agenti di valutazione della vulnerabilità nei nodi del cluster HDInsight di Azure e gestire i nodi tramite il rispettivo portale.

- [Come installare l'agente Rapid7 manualmente](https://insightvm.help.rapid7.com/docs/install)

- [Come installare l'agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [Come usare le azioni script](hdinsight-hadoop-customize-cluster-linux.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida**: gli aggiornamenti automatici del sistema sono stati abilitati per le immagini dei nodi del cluster, tuttavia è necessario riavviare periodicamente i nodi del cluster per assicurarsi che gli aggiornamenti vengano applicati.

Microsoft per gestire e aggiornare le immagini del nodo di Azure HDInsight di base.

- [Come configurare la pianificazione dell'applicazione di patch del sistema operativo per i cluster HDInsight](hdinsight-os-patching.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire la soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Linee guida**: usare azioni script o altri meccanismi per applicare patch ai cluster HDInsight di Azure. I cluster appena creati avranno sempre gli ultimi aggiornamenti disponibili, incluse le patch di sicurezza più recenti.

- [Come configurare la pianificazione dell'applicazione di patch del sistema operativo per i cluster Azure HDInsight basati su Linux](hdinsight-os-patching.md)

- [Come usare le azioni script](hdinsight-hadoop-customize-cluster-linux.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: implementare una soluzione di gestione delle vulnerabilità di terze parti che abbia la possibilità di confrontare le analisi delle vulnerabilità nel tempo. Se si dispone di una sottoscrizione di Rapid7 o Qualys, è possibile usare il portale del fornitore per visualizzare e confrontare le analisi delle vulnerabilità back-to-back.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: usare un programma comune di assegnazione dei punteggi di rischio, ad esempio un sistema comune di valutazione delle vulnerabilità, oppure le valutazioni di rischio predefinite fornite dallo strumento di analisi di terze parti.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via), inclusi i cluster HDInsight di Azure, all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Azure Resource Graph, è consigliabile creare e usare Azure Resource Manager risorse in futuro.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestione](/azure/governance/management-groups/create)

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: definire l'elenco delle risorse di Azure approvate e il software approvato per le risorse di calcolo

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: implementare una soluzione di terze parti per monitorare i nodi del cluster per le applicazioni software non approvate.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via), inclusi i cluster HDInsight di Azure, all'interno delle sottoscrizioni.  Se individuate, rimuovere eventuali risorse di Azure non approvate. Per i nodi del cluster Azure HDInsight, implementare una soluzione di terze parti per rimuovere o avvisare il software non approvato.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: per i nodi del cluster HDInsight di Azure, implementare una soluzione di terze parti per impedire l'esecuzione di software non autorizzato.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Per altre informazioni, vedere i riferimenti seguenti:

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida**: per i nodi del cluster HDInsight di Azure, implementare una soluzione di terze parti per impedire l'esecuzione di tipi di file non autorizzati.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. HDInsight" per creare criteri personalizzati per controllare o applicare la configurazione di rete del cluster HDInsight.

- [Come visualizzare gli alias dei criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Linee guida**: immagini del sistema operativo Azure HDInsight gestite e gestite da Microsoft. Cliente responsabile dell'implementazione di configurazioni sicure per il sistema operativo dei nodi del cluster.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare i criteri di Azure [deny] e [Deploy if not exist] per applicare impostazioni sicure per i cluster HDInsight di Azure e le risorse correlate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Linee guida**: immagini del sistema operativo Azure HDInsight gestite e gestite da Microsoft. Cliente responsabile dell'implementazione della configurazione dello stato a livello di sistema operativo.

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Esercitazione su git Azure Repos](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos/index)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. HDInsight" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Linee guida**: implementare una soluzione di terze parti per mantenere lo stato desiderato per i sistemi operativi dei nodi del cluster.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. HDInsight" per creare criteri personalizzati per controllare o applicare la configurazione del cluster HDInsight.

- [Come visualizzare gli alias dei criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: implementare una soluzione di terze parti per monitorare lo stato dei sistemi operativi del nodo del cluster.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: Azure HDInsight include il supporto Bring your own key (BYOK) per Apache Kafka. Questa funzionalità consente di essere proprietari delle chiavi usate per crittografare i dati inattivi e di gestirle.

Tutti i dischi gestiti in Azure HDInsight sono protetti con Azure crittografia del servizio di archiviazione (SSE). Per impostazione predefinita, i dati su tali dischi vengono crittografati usando chiavi gestite da Microsoft. Se si Abilita BYOK, è necessario specificare la chiave di crittografia per l'uso e la gestione da parte di Azure HDInsight usando Azure Key Vault.

Key Vault possono anche essere usati con le distribuzioni HDInsight di Azure per gestire le chiavi per l'archiviazione del cluster (account di archiviazione di Azure e Azure Data Lake Storage)

- [Come importare la propria chiave per Apache Kafka in Azure HDInsight](/azure/hdinsight/kafka/apache-kafka-byok)

- [Come gestire le chiavi di crittografia per gli account di archiviazione di Azure](/azure/storage/common/storage-encryption-keys-portal)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: è possibile usare le identità gestite in Azure HDInsight per consentire ai cluster di accedere ai servizi di dominio Azure Active Directory (Azure ad), accedere Azure Key Vault o accedere ai file Azure Data Lake storage Gen2.

- [Informazioni sulle identità gestite con Azure HDInsight](hdinsight-managed-identities.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Linee guida**: se si usa un codice correlato alla distribuzione di Azure HDInsight, è possibile implementare Credential scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usare il software antimalware gestito centralmente

**Linee guida**: Azure HDInsight viene fornita con clamscan preinstallato e abilitato per le immagini del nodo del cluster, tuttavia è necessario gestire il software e aggregare/monitorare manualmente i log prodotti da clamscan.

- [Informazioni su clamscan per Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, ma non viene eseguito sui contenuti del cliente.

Eseguire la pre-analisi di tutti i file caricati in tutte le risorse di Azure correlate alla distribuzione del cluster HDInsight di Azure, ad esempio Data Lake Storage, archiviazione BLOB e così via. Microsoft non è in grado di accedere ai dati dei clienti in tali istanze.

- [Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../security/fundamentals/antimalware.md)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Linee guida**: Azure HDInsight viene fornita con clamscan pre-installato e abilitato per le immagini del nodo del cluster. Clamscan eseguirà automaticamente gli aggiornamenti del motore e delle definizioni, tuttavia l'aggregazione e la gestione dei log dovranno essere eseguite manualmente.

- [Informazioni su clamscan per Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: quando si usa un account di archiviazione di Azure per l'archivio dati del cluster HDInsight, scegliere l'opzione di ridondanza appropriata (con ridondanza locale, ZRS, GRS, RA-GRS).  Quando si usa un database SQL di Azure per l'archivio dati del cluster HDInsight di Azure, configurare la replica geografica attiva.

- [Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure](../storage/common/storage-redundancy.md)

- [Come configurare la ridondanza per il database SQL di Azure](/azure/sql-database/sql-database-active-geo-replication)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: quando si usa un account di archiviazione di Azure per l'archivio dati del cluster HDInsight di Azure, scegliere l'opzione di ridondanza appropriata (con ridondanza locale, ZRS, GRS, RA-GRS). Se si usa Azure Key Vault per qualsiasi parte della distribuzione di Azure HDInsight, assicurarsi che venga eseguito il backup delle chiavi.

- [Scegliere le opzioni di archiviazione per il cluster Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)

- [Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure](../storage/common/storage-redundancy.md)

- [Come eseguire il backup di chiavi di Key Vault in Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: se Azure Key Vault viene usato con la distribuzione di Azure HDInsight, testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

- [Come importare la propria chiave per Apache Kafka in Azure HDInsight](/azure/hdinsight/kafka/apache-kafka-byok)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: se Azure Key Vault viene usato con la distribuzione di Azure HDInsight, abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

- [Come abilitare l'eliminazione temporanea Azure Key Vault](/azure/key-vault/key-vault-ovw-soft-delete)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Guida**: sviluppare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi della gestione e della gestione degli eventi imprevisti dal rilevamento alla revisione post-evento imprevisto. 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Usare la guida alla gestione degli eventi imprevisti di sicurezza del computer NIST per semplificare la creazione del piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza assegna un livello di gravità agli avvisi, in modo da consentire la priorità dell'ordine in cui si partecipa a ogni avviso, in modo che, quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft. 

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilità**: Condiviso

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
