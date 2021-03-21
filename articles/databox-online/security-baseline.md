---
title: Baseline della sicurezza di Azure per Azure Stack Edge
description: La linea di base di sicurezza di Azure Stack Edge fornisce linee guida e risorse per la procedura di implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 460fd66ed4651248639334caa55eb8facbce866d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452374"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Baseline della sicurezza di Azure per Azure Stack Edge

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 2,0](../security/benchmarks/overview.md) a Microsoft Azure stack Edge. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a Azure stack Edge. I **controlli** non applicabili a Azure stack Edge sono stati esclusi.

Per informazioni sul modo in cui Azure Stack Edge è completamente mappato al benchmark di sicurezza di Azure, vedere il [file di mapping di base sicurezza Azure stack Edge completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementare la sicurezza per il traffico interno

**Indicazioni**: i clienti distribuiscono un dispositivo Microsoft, fisico Azure stack Edge fornito da Microsoft nella propria rete privata per l'accesso interno e dispongono di opzioni per proteggerlo ulteriormente. Ad esempio, il dispositivo Azure Stack Edge è accessibile tramite la rete interna del cliente e richiede un indirizzo IP configurato dal cliente. Una password di accesso viene inoltre scelta dal cliente per accedere all'interfaccia utente del dispositivo. 

Il traffico interno è ulteriormente protetto da:

- Transport Layer Security (TLS) versione 1,2 è necessaria per la gestione portale di Azure e SDK del dispositivo Azure Stack Edge.

- Qualsiasi accesso client al dispositivo avviene tramite l'interfaccia utente Web locale usando TLS 1,2 standard come protocollo di sicurezza predefinito.

- Solo un dispositivo Azure Stack Edge Pro autorizzato è autorizzato a partecipare al servizio Azure Stack Edge creato dal cliente nella sottoscrizione di Azure.

Ulteriori informazioni sono disponibili in collegamenti a cui si fa riferimento.
 
- [Configurare TLS 1,2 nei client Windows che accedono al dispositivo GPU Pro Azure Stack Edge](azure-stack-edge-j-series-configure-tls-settings.md)

- [Guida introduttiva: Introduzione a Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-quickstart.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: connettere le reti private

**Linee guida**: i clienti possono scegliere una rete privata virtuale (VPN) da punto a sito per connettere un dispositivo Azure stack Edge dalla propria rete privata locale alla rete di Azure. VPN fornisce un secondo livello di crittografia per i dati in movimento rispetto alla sicurezza a livello di trasporto dal dispositivo del cliente ad Azure. 

I clienti possono configurare una rete privata virtuale nel dispositivo Azure Stack Edge tramite il portale di Azure o tramite il Azure PowerShell.

- [Configurare la VPN di Azure tramite Azure PowerShell script per Azure Stack Edge Pro R e Azure Stack Edge Mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Configurare TLS 1,2 nei client Windows che accedono al dispositivo GPU Pro Azure Stack Edge](azure-stack-edge-j-series-configure-tls-settings.md)

- [Esercitazione: Configurare i certificati per il dispositivo Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Stabilire l'accesso alla rete privato ai servizi di Azure

**Linee guida**: i clienti possono scegliere una rete privata virtuale (VPN) da punto a sito per connettere un dispositivo Azure stack Edge dalla propria rete privata locale alla rete di Azure. VPN fornisce un secondo livello di crittografia per i dati in movimento rispetto alla sicurezza a livello di trasporto dal dispositivo del cliente ad Azure. 

- [Configurare la VPN di Azure tramite Azure PowerShell script per Azure Stack Edge Pro R e Azure Stack Edge Mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Configurare TLS 1,2 nei client Windows che accedono al dispositivo GPU Pro Azure Stack Edge](azure-stack-edge-j-series-configure-tls-settings.md)

- [Esercitazione: Configurare i certificati per il dispositivo Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteggere le applicazioni e i servizi da attacchi di rete esterni

**Indicazioni**: il dispositivo Azure stack Edge incorpora le funzionalità standard di protezione della rete di Windows Server, che non sono configurabili dai clienti.

I clienti possono scegliere di proteggere la propria rete privata connessa con Azure Stack dispositivo perimetrale da attacchi esterni usando un'appliance virtuale di rete, ad esempio un firewall con protezioni DDoS (Distributed Denial of Service) avanzate.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: distribuire sistemi di rilevamento intrusione/prevenzione intrusioni (IDS/IP)

**Linee guida**: gli endpoint usati dal dispositivo Azure stack Edge sono tutti gestiti da Microsoft. I clienti sono responsabili di eventuali controlli aggiuntivi che vogliono distribuire nei sistemi locali.

Il dispositivo Azure Stack Edge usa le proprie funzionalità di rilevamento delle intrusioni per proteggere il servizio. 

- [Informazioni sulla sicurezza di Azure Stack Edge](azure-stack-edge-security.md)

- [Informazioni sulle porte per Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md)

- [Ottenere i log di rilevamento delle intrusioni hardware e software](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gestire le identità dell'applicazione in modo sicuro e automatico

**Linee guida**: tutti i dispositivi Azure stack Edge hanno automaticamente un'identità gestita assegnata dal sistema in Azure Active Directory (Azure ad). Attualmente, l'identità gestita viene usata per la gestione cloud delle macchine virtuali ospitate in Azure Stack Edge.

I dispositivi Azure Stack Edge vengono avviati in uno stato bloccato per l'accesso locale. Per l'account amministratore del dispositivo locale, sarà necessario connettersi al dispositivo tramite l'interfaccia utente Web locale o l'interfaccia di PowerShell e impostare una password complessa. Archiviare e gestire le credenziali di amministratore del dispositivo in un luogo sicuro, ad esempio un Azure Key Vault e ruotare la password di amministratore in base agli standard aziendali.

- [Proteggi dispositivo Azure Stack Edge tramite password](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Usare l'accesso Single Sign-On (SSO) di Azure per accedere alle applicazioni

**Indicazioni**: Single Sign-on non è supportato per i dispositivi endpoint Azure stack Edge. Tuttavia, è possibile scegliere di abilitare Single Sign-On basate su Azure Active Directory standard (Azure AD) per proteggere l'accesso alle risorse cloud di Azure.

- [Informazioni sull'accesso Single Sign-on dell'applicazione con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Usare i controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory

**Linee guida**: l'autenticazione a più fattori è un controllo di autenticazione avanzato, ma una funzionalità di consenso esplicito per gli utenti del servizio Azure stack Edge. Può essere usato per gli scenari supportati, ad esempio la gestione Edge dei dispositivi Azure Stack Edge nel portale di Azure. Si noti che l'accesso locale ai dispositivi Azure Stack Edge non supporta l'autenticazione a più fattori.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorare e segnalare le anomalie degli account

**Linee guida**: abilitare monitoraggio di Azure per raccogliere i log del dispositivo o del contenitore per il servizio Azure stack Edge. Monitorare i contenitori, ad esempio quelli che eseguono più applicazioni di calcolo nel cluster Kubernetes del dispositivo.

Inoltre, un pacchetto per il supporto, che include i log di controllo, può essere raccolto nell'interfaccia utente Web locale del dispositivo Azure Stack Edge. Si noti che il pacchetto per il supporto non è disponibile nel portale di Azure.
 
- [Abilitare monitoraggio di Azure sul dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [Raccolta di un pacchetto per il supporto](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Limitare l'accesso alle risorse di Azure in base alle condizioni

**Linee guida**: l'accesso condizionale Azure Active Directory (Azure ad) è una funzionalità di consenso esplicito per l'autenticazione al servizio Azure stack Edge. Azure Stack Edge service è una risorsa della portale di Azure che consente di gestire un dispositivo Azure Stack Edge Pro in posizioni geografiche diverse. 

- [Che cos'è l'accesso condizionale](../active-directory/conditional-access/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminare l'esposizione non intenzionale delle credenziali

**Linee guida**: seguire le procedure consigliate per proteggere le credenziali, ad esempio:

- Chiave di attivazione usata per attivare il dispositivo con la risorsa Azure Stack Edge in Azure.
- Credenziali di accesso per accedere al dispositivo Azure Stack Edge.
- File di chiave che possono semplificare il ripristino del dispositivo Azure Stack Edge.
- Chiave di crittografia del canale

Ruotare e sincronizzare regolarmente le chiavi dell'account di archiviazione per proteggere l'account di archiviazione da utenti non autorizzati.

- [Sicurezza e protezione dei dati di Azure Stack Edge Pro](azure-stack-edge-security.md)

- [Sincronizzare le chiavi di archiviazione](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Limitare l'accesso amministrativo ai sistemi business-critical

**Indicazioni**: la soluzione Azure stack Edge dispone di diversi componenti con controlli di accesso sicuri per limitare l'accesso ai dispositivi aziendali critici. Per configurare e gestire il dispositivo, l'organizzazione dovrà avere un Contratto Enterprise (EA) o un provider di soluzioni cloud (CSP) o una sottoscrizione di Microsoft Azure Sponsorship: 

Il servizio Azure Stack Edge è protetto dalle funzionalità di sicurezza di Azure come servizio di gestione ospitato in Azure. È possibile ottenere la chiave di crittografia per la risorsa nelle proprietà del dispositivo per qualsiasi operazione di gestione Software Development Kit, ma è possibile visualizzare la chiave di crittografia solo se si dispone delle autorizzazioni appropriate per la API Graph di risorse.

Il dispositivo Azure Stack Edge Pro è un dispositivo locale che consente di trasformare i dati in locale e quindi di inviarli ad Azure. Il dispositivo:

- richiede una chiave di attivazione per accedere al servizio Azure Stack Edge.
- è sempre protetto da una password del dispositivo.
- con avvio protetto abilitato.

- è un dispositivo bloccato. Il controller di gestione battiscopa (BMC) e il BIOS del dispositivo sono protetti da password. Il BIOS è protetto da accesso utente limitato.
- esegue Windows Defender Device Guard. Device Guard consente di eseguire solo applicazioni attendibili definite nei criteri di integrità del codice.

Seguire le procedure consigliate per proteggere tutte le credenziali e i segreti usati per accedere a Azure Stack Edge. 

- [Procedure consigliate per le password](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Esaminare e riconciliare regolarmente gli accessi utente

**Indicazioni**: Azure stack Edge ha un utente denominato "EdgeUser" che può configurare il dispositivo. Include anche Azure Resource Manager utente "EdgeArmUser" per le funzionalità di Azure Resource Manager locali nel dispositivo. 

Attenersi alle procedure consigliate per proteggere:

- Credenziali usate per accedere al dispositivo locale

- Credenziali di condivisione SMB.

- Accesso ai sistemi client che sono stati configurati per l'utilizzo delle condivisioni NFS.

- Chiavi dell'account di archiviazione locale usate per accedere agli account di archiviazione locali quando si usa l'API REST BLOB.

Informazioni aggiuntive sono disponibili nel collegamento a cui si fa riferimento.

- [Informazioni sulla sicurezza per i dispositivi Azure Stack Edge](azure-stack-edge-security.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Usare le workstation con accesso con privilegi

**Linee guida**: le workstation protette e isolate sono di importanza fondamentale per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Usare workstation utente altamente sicure con o senza il Bastion di Azure per le attività amministrative. Usare Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP) e Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. 

Le workstation protette possono essere gestite centralmente per applicare una configurazione sicura che include l'autenticazione avanzata, le baseline software e hardware, l'accesso logico e di rete limitato.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Distribuire una workstation con accesso con privilegi](/security/compass/privileged-access-deployment)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteggere i dati sensibili

**Linee guida**: Azure stack Edge considera tutti i dati interagito come sensibili solo con gli utenti autorizzati che hanno accesso a questi dati. È consigliabile seguire le procedure consigliate per proteggere le credenziali usate per accedere al servizio Azure Stack Edge.

- [Sicurezza e protezione dei dati di Azure Stack Edge Pro](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Crittografare le informazioni sensibili in transito

**Linee guida**: Azure stack Edge USA canali protetti per i dati in corso. Si tratta di:

- Il protocollo TLS 1,2 standard viene usato per i dati che si spostano tra il dispositivo e il cloud di Azure. Non esiste alcun fallback a TLS 1,1 e versioni precedenti. La comunicazione dell'agente verrà bloccata se TLS 1,2 non è supportato. Il protocollo TLS 1,2 è necessario anche per la gestione di portale di Azure e Software Development Kit (SDK).

- Quando i client accedono al dispositivo tramite l'interfaccia utente Web locale di un browser, viene usato standard TLS 1,2 come protocollo di sicurezza predefinito

La procedura consigliata consiste nel configurare il browser per l'uso di TLS 1,2. Usare SMB 3,0 con la crittografia per proteggere i dati durante la copia dai server di dati.

- [Procedure consigliate per la protezione dei dati in corso](azure-stack-edge-security.md#protect-data-in-flight)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Indicazioni**: assicurarsi che i team responsabili della sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure, in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda di come sono strutturate le responsabilità del team responsabile della sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabilità di un team addetto alla sicurezza centrale o di un team locale. Fatta questa premessa, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in una posizione centralizzata all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Si noti che potrebbero essere necessarie autorizzazioni aggiuntive per ottenere la visibilità dei carichi di lavoro e dei servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: solo gli utenti autorizzati, ad esempio, "EdgeArmUser" possono accedere alle API del dispositivo Azure stack Edge tramite la Azure Resource Manager locale. Le password degli account utente possono essere gestite solo in portale di Azure. 

- [Impostare la password di Azure Resource Manager](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: usare solo le applicazioni approvate nelle risorse di calcolo

**Linee guida**: è possibile importare le proprie applicazioni per l'esecuzione in tutte le macchine virtuali create localmente. Usare gli script di PowerShell per creare macchine virtuali di calcolo locali sul dispositivo stack Edge. Si consiglia di importare solo le applicazioni attendibili per l'esecuzione nelle macchine virtuali locali. 

- [Come controllare l'esecuzione di script di PowerShell nell'ambiente Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: abilitazione del rilevamento delle minacce per le risorse di Azure

**Linee guida**: Azure stack Edge non offre funzionalità di rilevamento delle minacce. Tuttavia, i clienti possono raccogliere i log di controllo in un pacchetto di supporto per l'analisi e il rilevamento delle minacce offline. 

- [Raccolta del pacchetto di supporto per il dispositivo Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Abilitare la registrazione per le attività di rete di Azure

**Linee guida**: Azure stack Edge dispone di log di controllo di rete abilitati come parte del pacchetto di supporto scaricabile. Questi log possono essere analizzati per implementare un monitoraggio in tempo quasi reale per i dispositivi Azure Stack Edge.

- [Raccolta di un pacchetto per il supporto di Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Abilitare la registrazione per le risorse di Azure

**Linee guida**: il monitoraggio in tempo reale con i log non è attualmente supportato per Azure stack Edge. È disponibile una funzionalità per la raccolta di un pacchetto per il supporto che consente di analizzare i vari log inclusi, ad esempio firewall, software, intrusione hardware e registri eventi di sistema per il dispositivo Azure Stack Edge Pro. Si noti che l'intrusione software o i log del firewall predefiniti vengono raccolti per il traffico in ingresso e in uscita.

- [Raccogliere un pacchetto per il supporto per Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizzare la gestione e l'analisi dei log di sicurezza

**Linee guida**: il monitoraggio in tempo reale con i log non è attualmente supportato per Azure stack Edge. Tuttavia, è possibile raccogliere un pacchetto per il supporto che consente di analizzare i vari log inclusi.  Il pacchetto per il supporto è compresso e viene scaricato nel percorso desiderato. Decomprimere il pacchetto e visualizzare i file di registro di sistema contenuti. È anche possibile inviare questi log in uno strumento di gestione degli eventi di informazioni di sicurezza o in un'altra posizione di archiviazione centrale per l'analisi.

- [Raccogliere un pacchetto per il supporto per Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurare la conservazione dell'archiviazione dei log

**Linee guida**: non è possibile modificare il periodo di memorizzazione dei log nel dispositivo Azure stack Edge. I log meno recenti vengono eliminati in base alle esigenze. È possibile raccogliere i pacchetti di supporto dal dispositivo a intervalli periodici per qualsiasi requisito per conservare i log per un periodo di tempo più lungo. 

- [Raccogliere un pacchetto per il supporto per Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: usare le origini di sincronizzazione dell'ora approvate

**Linee guida**: Azure stack Edge USA Time.Windows.com, un server del provider di tempo di rete da Microsoft.  Azure Stack Edge consente inoltre ai clienti di configurare il server del protocollo del tempo di rete scelto.

- [Configurare le impostazioni dell'ora del dispositivo Azure Stack Edge](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

**Linee guida**: assicurarsi che il piano di risposta dell'evento imprevisto aziendale includa i processi 

- per rispondere agli eventi imprevisti della sicurezza

- che sono stati aggiornati per il cloud di Azure
 
- vengono regolarmente usati per garantire la conformità

Si consiglia di implementare la sicurezza utilizzando processi standardizzati di risposta agli eventi imprevisti nell'ambiente aziendale.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparazione: configurare la notifica dell'evento imprevisto

**Linee guida**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Le informazioni di contatto consentono a Microsoft di contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: rilevamento e analisi: creare eventi imprevisti in base a avvisi di alta qualità 

**Linee guida**: assicurarsi di disporre di un processo per creare avvisi di alta qualità e per misurarne la qualità. In questo modo è possibile apprendere le lezioni dagli eventi imprevisti passati e assegnare la priorità agli avvisi per gli analisti, in modo da evitare inutili tempi di elaborazione degli avvisi falsi positivi. Crea avvisi di alta qualità in base alle tue esperienze, da interventi precedenti, origini della community convalidate e strumenti progettati per generare e pulire gli avvisi con la registrazione e la correlazione per diverse origini degli avvisi. 

Il Centro sicurezza di Azure offre avvisi di alta qualità in molte risorse di Azure. È possibile usare il connettore dati del Centro sicurezza per trasmettere gli avvisi ad Azure Sentinel. Creare regole di avviso avanzate con Azure Sentinel per generare eventi imprevisti automatici per un'indagine. 

Esporta gli avvisi e le raccomandazioni del Centro sicurezza in Azure Sentinel con la funzionalità di esportazione per identificare i rischi per le risorse di Azure. Si consiglia di creare un processo per esportare avvisi e consigli in modo automatico per la sicurezza continua.

- [Come configurare l'esportazione](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Rilevamento e analisi: esaminare un evento imprevisto

**Linee guida**: assicurarsi che gli analisti possano eseguire query e utilizzare origini dati diverse per creare una visualizzazione completa dell'attività che si è verificata durante l'analisi di potenziali eventi imprevisti. È necessario raccogliere diversi tipi di log per tenere traccia delle attività di un potenziale utente malintenzionato attraverso la catena di Kill per evitare punti ciechi.  Assicurarsi inoltre che le informazioni dettagliate e le informazioni vengano acquisite per riferimento cronologico.  

Le origini dati per l'analisi includono le origini di registrazione centralizzate che sono già state raccolte dai servizi inclusi nell'ambito e dai sistemi in esecuzione, ma possono includere anche:

- Dati di rete: usare i log dei flussi dei gruppi di sicurezza di rete, Azure Network Watcher e Monitoraggio di Azure per acquisire i log dei flussi di rete e altre informazioni di analisi. 

- Snapshot dei sistemi in esecuzione: 

    - Usare la funzionalità snapshot macchina virtuale di Azure per creare uno snapshot del disco del sistema in esecuzione. 

    - Scegliere la funzionalità di dump della memoria nativa del sistema operativo per creare uno snapshot della memoria del sistema in esecuzione.

    - Usare la funzionalità snapshot dei servizi di Azure o una funzionalità software di terze parti per creare snapshot dei sistemi in esecuzione.

Azure Sentinel fornisce analisi approfondite dei dati in qualsiasi origine di log e un portale di gestione dei casi per gestire l'intero ciclo di vita degli eventi imprevisti. Le informazioni di intelligence durante un'analisi possono essere associate a un evento imprevisto a scopo di rilevamento e creazione di report. 

- [Creare uno snapshot del disco di un computer Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Creare uno snapshot del disco di un computer Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Raccolta delle informazioni di diagnostica e del dump della memoria da parte del supporto tecnico di Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Esaminare gli eventi imprevisti con Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Rilevamento e analisi: assegnare la priorità agli eventi imprevisti

**Linee guida**: offrire agli analisti il contesto relativo agli eventi imprevisti da esaminare per primi in base alla gravità dell'avviso e alla sensibilità degli asset. Usare il livello di gravità assegnato a ogni avviso dal centro sicurezza di Azure per definire la priorità degli avvisi da analizzare per primi. Il livello di gravità è basato sul grado di attendibilità del Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di attendibilità con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha generato l'avviso.

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: stabilire configurazioni sicure per le risorse di calcolo

**Linee guida**: Azure stack Edge offre supporto per creare configurazioni sicure per le macchine virtuali locali create dai clienti. Si consiglia vivamente di utilizzare le linee guida fornite da Microsoft per stabilire le basi di riferimento per la sicurezza durante la creazione di macchine virtuali locali.

- [Scaricare le linee di base di sicurezza incluse in Security Compliance Toolkit](/windows/security/threat-protection/windows-security-baselines)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: supportare le configurazioni sicure per le risorse di calcolo

**Linee guida**: Azure stack Edge non offre alcun supporto per supportare configurazioni sicure per le macchine virtuali locali create dai clienti. Si consiglia vivamente ai clienti di usare i toolkit di conformità della sicurezza (SCT) per supportare le configurazioni sicure per le risorse di calcolo.

Il sistema operativo host e le macchine virtuali gestite da Azure Stack Edge gestiscono le proprie configurazioni di sicurezza. 

- [Elementi di base della sicurezza di Windows](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: archiviare in modo sicuro immagini del sistema operativo e del contenitore personalizzate

**Linee guida**: i sistemi operativi host e le macchine virtuali gestite da Azure stack Edge vengono archiviati in modo sicuro. 

I clienti possono importare le proprie immagini di macchine virtuali e contenitori e sono responsabili della gestione sicura.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>7\. Correggere le vulnerabilità del software in modo rapido e automatico

**Linee guida**: Azure stack Edge fornisce aggiornamenti regolari delle patch e avvisi ai clienti quando tali aggiornamenti sono disponibili per correggere le vulnerabilità. È responsabilità del cliente mantenerne aggiornati i dispositivi e le macchine virtuali (creati da essi) con le patch più recenti.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

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

**Linee guida**: Azure stack Edge non supporta direttamente il rilevamento e la risposta degli endpoint. Tuttavia, è possibile raccogliere un pacchetto per il supporto e recuperare i log di controllo. Questi log possono quindi essere analizzati per verificare la presenza di attività anomale. 

- [Raccolta del pacchetto di supporto per il dispositivo Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: utilizzo del software antimalware moderno gestito centralmente

**Indicazioni**: Azure stack Edge usa il controllo delle applicazioni di Windows Defender (WDAC) con un criterio di integrità del codice (ci) rigoroso che consente l'esecuzione solo di applicazioni e script predeterminati. È abilitato anche l'anti-malware per la protezione in tempo reale di Windows Defender. Il cliente può scegliere di eseguire l'anti-malware nelle macchine virtuali di calcolo create per eseguire localmente sul dispositivo Azure Stack Edge.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="backup-and-recovery"></a>Backup e ripristino

*Per altre informazioni, vedere [Azure Security Benchmark: Backup e ripristino](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: garantire backup automatici regolari

**Linee guida**: i backup periodici del dispositivo Azure stack Edge sono consigliati e possono essere eseguiti con backup di Azure e altre soluzioni di protezione dei dati di terze parti per proteggere i dati contenuti nelle macchine virtuali distribuite nel dispositivo. 

Le soluzioni di protezione dei dati di terze parti, ad esempio Cohesity, CommVault e Veritas, possono anche fornire una soluzione di backup per i dati nelle condivisioni SMB o NFS locali. 

Ulteriori informazioni sono disponibili in collegamenti a cui si fa riferimento.

- [Preparare un errore del dispositivo](azure-stack-edge-gpu-prepare-device-failure.md)

- [Proteggere file e cartelle nelle VM](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2: crittografare i dati di backup

**Linee guida**: assicurarsi che i backup siano protetti dagli attacchi. Questa operazione dovrebbe includere la crittografia dei backup per evitare la perdita di riservatezza.  Per ulteriori informazioni, fare riferimento alla soluzione di backup preferita per i dettagli.

- [Proteggere i dati nelle condivisioni locali perimetrali](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [Proteggere file e cartelle in macchine virtuali](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Convalidare tutti i backup che includono chiavi gestite dal cliente

**Linee guida**: eseguire periodicamente il ripristino dei dati dei backup. 

- [Procedure di ripristino per Azure Stack Edge](azure-stack-edge-gpu-recover-device-failure.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Attenuare il rischio di chiavi perse

**Linee guida**: assicurarsi che tutti i backup Azure stack Edge, incluse le chiavi gestite dal cliente, siano protetti secondo le procedure consigliate dell'organizzazione. Il dispositivo Azure Stack Edge è associato a un account di archiviazione di Azure, usato come repository di destinazione per i dati in Azure. 

L'accesso all'account di archiviazione di Azure è controllato dalle sottoscrizioni di Azure e dalle chiavi di accesso alle archiviazione a 2 512 bit associate a tale account di archiviazione. Una delle chiavi di accesso viene usata a scopo di autenticazione quando il dispositivo Azure Stack Edge accede all'account di archiviazione. L'altra chiave viene mantenuta in riservata per la rotazione periodica della chiave. Assicurarsi che vengano usate le procedure di sicurezza consigliate per la rotazione della chiave.

- [Proteggere i dati dei dispositivi Azure Stack Edge negli account di archiviazione di Azure](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

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

-   Strategia di controllo degli accessi obbligatoria in base alla classificazione dei dati dell'organizzazione

-   Uso delle funzionalità di protezione dei dati native di Azure e di terze parti

-   Requisiti di crittografia dei dati per i casi d'uso in transito e inattivi

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

- [Azure Security Benchmark - Sicurezza di rete](../security/benchmarks/index.yml)

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

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
