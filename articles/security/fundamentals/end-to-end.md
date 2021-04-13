---
title: Sicurezza end-to-end in Azure | Microsoft Docs
description: Questo articolo fornisce una mappa dei servizi di Azure che consentono di proteggere e proteggere le risorse cloud e rilevare ed esaminare le minacce.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: 3ea3c2bcb878dbd8a712e6076dda09853f55e297
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310343"
---
# <a name="end-to-end-security-in-azure"></a>Sicurezza end-to-end in Azure
Uno dei motivi migliori per usare Azure per le proprie applicazioni e i propri servizi consiste nella possibilità di sfruttare una vasta gamma di strumenti e funzionalità per la sicurezza. Questi strumenti e queste funzionalità consentono di creare soluzioni sicure sulla piattaforma Azure protetta. Microsoft Azure garantisce la riservatezza, l'integrità e la disponibilità dei dati dei clienti, assicurando anche al tempo stesso una rendicontazione trasparente.

Nel diagramma e nella documentazione seguenti vengono presentati i servizi di sicurezza in Azure. Questi servizi di sicurezza consentono di soddisfare le esigenze di sicurezza dell'azienda e di proteggere gli utenti, i dispositivi, le risorse, i dati e le applicazioni nel cloud.

## <a name="microsoft-security-services-map"></a>Mappa dei servizi di sicurezza Microsoft

La mappa servizi di sicurezza organizza i servizi in base alle risorse protette (colonna). Il diagramma raggruppa inoltre i servizi nelle seguenti categorie (riga):

- Proteggi e proteggi-servizi che ti permettono di implementare una strategia di difesa approfondita a più livelli, per l'identità, gli host, le reti e i dati. Questa raccolta di servizi e funzionalità di sicurezza consente di comprendere e migliorare il comportamento di sicurezza nell'ambiente Azure.
- Rilevare le minacce: servizi che identificano le attività sospette e facilitano la mitigazione della minaccia.
- Esaminare e rispondere: servizi che effettuano il pull dei dati di registrazione in modo da poter valutare un'attività sospetta e rispondere.

Il diagramma include il programma di benchmark di sicurezza di Azure, una raccolta di raccomandazioni di sicurezza a elevato utilizzo che è possibile usare per proteggere i servizi usati in Azure.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Diagramma che illustra i servizi di sicurezza end-to-end in Azure." border="false":::

## <a name="security-controls-and-baselines"></a>Controlli di sicurezza e linee di base
Il programma di [benchmark di sicurezza di Azure](../benchmarks/introduction.md) include una raccolta di raccomandazioni sulla sicurezza a elevato utilizzo che è possibile usare per proteggere i servizi usati in Azure:

- Controlli di sicurezza: questi consigli sono generalmente applicabili nei servizi tenant di Azure e Azure. Ogni raccomandazione identifica un elenco di stakeholder che in genere sono coinvolti nella pianificazione, nell'approvazione o nell'implementazione del benchmark.
- Baseline del servizio: questi applicano i controlli ai singoli servizi di Azure per fornire consigli sulla configurazione di sicurezza del servizio.

## <a name="secure-and-protect"></a>Sicurezza e protezione

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="Diagramma che illustra i servizi di Azure che consentono di proteggere e proteggere le risorse cloud." border="false":::

| Servizio | Descrizione |
|------|--------|
| [Centro sicurezza di Azure](../../security-center/security-center-introduction.md)| Sistema unificato di gestione della sicurezza dell'infrastruttura che rafforza il comportamento di sicurezza dei data center e offre protezione avanzata dalle minacce nei carichi di lavoro ibridi nel cloud, sia che si trovino in Azure, sia in locale. |
| **&nbsp; & &nbsp; Gestione dell'accesso alle &nbsp; identità** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| Il servizio di gestione delle identità e degli accessi basato sul cloud di Microsoft.  |
|  | L' [accesso condizionale](../../active-directory/conditional-access/overview.md) è lo strumento usato da Azure ad per riunire i segnali di identità, prendere decisioni e applicare i criteri dell'organizzazione. |
|  | [Servizi di dominio](../../active-directory-domain-services/overview.md) è lo strumento usato da Azure ad per fornire servizi di dominio gestiti, ad esempio aggiunta a un dominio, criteri di gruppo, LDAP (Lightweight Directory Access Protocol) e autenticazione Kerberos/NTLM. |
|  | [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) è un servizio di Azure ad che consente di gestire, controllare e monitorare l'accesso a risorse importanti nell'organizzazione. |
|  | L'autenticazione a più [fattori](../../active-directory/authentication/concept-mfa-howitworks.md) è lo strumento usato da Azure ad per proteggere l'accesso ai dati e alle applicazioni richiedendo una seconda forma di autenticazione. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) | Strumento che consente alle organizzazioni di automatizzare il rilevamento e la correzione dei rischi basati sull'identità, analizzare i rischi usando i dati nel portale ed esportare i dati di rilevamento dei rischi in utilità di terze parti per un'ulteriore analisi. |
| **Rete dell'infrastruttura &nbsp; & &nbsp;** |  |
| [Gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | Un gateway di rete virtuale usato per inviare il traffico crittografato tra una rete virtuale di Azure e una posizione locale tramite la rete Internet pubblica e per inviare il traffico crittografato tra le reti virtuali di Azure tramite la rete Microsoft. |
| [Protezione DDoS di Azure Standard](../../ddos-protection/ddos-protection-overview.md) | Fornisce funzionalità avanzate di mitigazione DDoS per la difesa da attacchi DDoS. Viene ottimizzata automaticamente per aiutare a proteggere le risorse di Azure specifiche in una rete virtuale. |
| [Frontdoor di Azure](../../frontdoor/front-door-overview.md) | Un punto di ingresso globale e scalabile che usa la rete Microsoft globale perimetrale per creare applicazioni Web veloci, sicure e scalabili. |
| [Firewall di Azure](../../firewall/overview.md) | Servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti. |
| [Azure Key Vault](../../key-vault/general/overview.md) | Archivio di segreti protetti per token, password, certificati, chiavi API e altri segreti. Key Vault possibile utilizzare anche per creare e controllare le chiavi di crittografia utilizzate per crittografare i dati. |
| [Key Vault HSM gestito (anteprima)](../../key-vault/managed-hsm/overview.md) | Un servizio cloud completamente gestito, a disponibilità elevata, a tenant singolo e conforme agli standard che consente di proteggere le chiavi crittografiche per le applicazioni cloud, usando FIPS 140-2 livello 3 convalidato HSM. |
| [Collegamento privato di Azure](../../private-link/private-link-overview.md) | Consente di accedere ai servizi PaaS di Azure, ad esempio archiviazione di Azure e database SQL, e ai servizi di proprietà/partner del cliente ospitati in Azure tramite un endpoint privato nella rete virtuale. |
| [Gateway applicazione di Azure](../../application-gateway/overview.md) | Un servizio di bilanciamento del carico del traffico Web avanzato che consente di gestire il traffico per le applicazioni Web. Il gateway applicazione consente di prendere decisioni relative al routing basate su altri attributi di una richiesta HTTP, ad esempio il percorso dell'URI o le intestazioni host. |
| [Bus di servizio di Azure](../../service-bus-messaging/service-bus-messaging-overview.md) | Un broker di messaggi Enterprise completamente gestito con code di messaggi e argomenti di pubblicazione-sottoscrizione. Il bus di servizio viene usato per separare le applicazioni e i servizi tra loro. |
| [Web application firewall](../../web-application-firewall/overview.md) | Offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. WAF può essere distribuito con applicazione Azure gateway e il front-end di Azure. |
| **Applicazione & dati** |  |
| [Backup di Azure](../../backup/backup-overview.md) | Fornisce soluzioni semplici, sicure ed economicamente convenienti per eseguire il backup dei dati e recuperarli dal cloud Microsoft Azure. |
| [crittografia del servizio di archiviazione di Azure](../../storage/common/storage-service-encryption.md) | Crittografa automaticamente i dati prima che vengano archiviati e li decrittografa automaticamente al momento del recupero. |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) | Soluzione basata sul cloud che consente alle organizzazioni di individuare, classificare e proteggere documenti e messaggi di posta elettronica applicando etichette ai contenuti. |
| [Gestione API](../../api-management/api-management-key-concepts.md) | Un modo per creare gateway API coerenti e moderni per i servizi back-end esistenti. |
| [Confidential computing di Azure](../../confidential-computing/overview.md) | Consente di isolare i dati sensibili durante l'elaborazione nel cloud. |
| [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops) | I progetti di sviluppo traggono vantaggio da più livelli di tecnologie di sicurezza e governance, procedure operative e criteri di conformità se archiviati in Azure DevOps. |
| **Accesso dei clienti** |  |
| [Azure AD identità esterne](../../active-directory/external-identities/compare-with-b2c.md) | Con le identità esterne in Azure AD, è possibile consentire a persone esterne all'organizzazione di accedere ad app e risorse aziendali, permettendo loro di eseguire l'accesso con l'identità che preferiscono. |
|  | È possibile condividere le app e le risorse con gli utenti esterni tramite [Azure ad collaborazione B2B](../../active-directory/external-identities/what-is-b2b.md) . |
|  | [Azure ad B2C](../../active-directory-b2c/overview.md) ti permette di supportare milioni di utenti e miliardi di autenticazioni al giorno, monitorando e gestendo automaticamente le minacce, ad esempio attacchi di tipo Denial of Service, spray con password o attacchi di forza bruta. |

## <a name="detect-threats"></a>Rilevare le minacce

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="Diagramma che mostra i servizi di Azure che rilevano le minacce." border="false":::

| Servizio | Descrizione |
|------|--------|
| [Azure Defender](../../security-center/azure-defender.md) | Offre funzionalità avanzate, intelligenti, per la protezione delle risorse e dei carichi di lavoro di Azure e ibrido. Il dashboard di Azure Defender nel centro sicurezza offre visibilità e controllo delle funzionalità di protezione del carico di lavoro cloud per l'ambiente in uso. |
| [Azure Sentinel](../../sentinel/overview.md) | Una soluzione scalabile, nativa del cloud, per la gestione degli eventi di sicurezza (SIEM) e per la risposta automatica delle orchestrazioni di sicurezza (SOAR). Sentinel offre funzionalità intelligenti di analisi della sicurezza e Intelligence per le minacce nell'azienda, offrendo un'unica soluzione per il rilevamento degli avvisi, la visibilità delle minacce, la ricerca proattiva e la risposta alle minacce. |
| **&nbsp; & &nbsp; Gestione dell'accesso alle &nbsp; identità** |  |
| [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/defender/microsoft-365-defender) | Una suite di difesa aziendale pre e post-violazione unificata che coordina in modo nativo il rilevamento, la prevenzione, l'analisi e la risposta tra endpoint, identità, posta elettronica e applicazioni per offrire protezione integrata da attacchi sofisticati. |
|  | [Microsoft Defender for endpoint](https://docs.microsoft.com/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint.md) è una piattaforma di sicurezza degli endpoint aziendali progettata per aiutare le reti aziendali a prevenire, rilevare, analizzare e rispondere a minacce avanzate. |
|  | [Microsoft Defender per l'identità](https://docs.microsoft.com/defender-for-identity/what-is) è una soluzione di sicurezza basata sul cloud che sfrutta i segnali di Active Directory locali per identificare, rilevare e analizzare minacce avanzate, identità compromesse e azioni Insider dannose dirette alla tua organizzazione. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | Invia due tipi di messaggi di posta elettronica di notifica automatici per semplificare la gestione dei rischi e dei rilevamenti dei rischi utente: utenti a rischio messaggi di posta elettronica e messaggi di posta elettronica del digest |
| **Infrastruttura & rete** |  |
| [Azure Defender per IoT](../../defender-for-iot/overview.md) | Una soluzione di sicurezza unificata per identificare i dispositivi, le vulnerabilità e le minacce. Consente di proteggere l'intero ambiente di Internet delle cose, se è necessario proteggere i dispositivi Internet o gli Internet esistenti o creare una sicurezza in nuove innovazioni. |
| [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) | Fornisce strumenti per il monitoraggio, la diagnosi, la visualizzazione delle metriche e l'abilitazione o disabilitazione dei log per le risorse in una rete virtuale di Azure. Network Watcher è progettato per monitorare e ripristinare l'integrità della rete dei prodotti IaaS che includono macchine virtuali, reti virtuali, gateway applicazione e servizi di bilanciamento del carico. |
| [Registrazione di controllo criteri di Azure](../../governance/policy/overview.md) | Consente di applicare gli standard aziendali e di valutare la conformità su larga scala. Criteri di Azure usa i log attività, che vengono abilitati automaticamente per includere origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili. |
| **Applicazione & dati** |  |
| [Azure Defender per registri contenitori](../../security-center/defender-for-container-registries-introduction.md) | Include uno scanner di vulnerabilità per analizzare le immagini nei registri di Azure Container Registry basati su Azure Resource Manager e fornire visibilità più approfondita sulle vulnerabilità delle immagini. |
| [Azure Defender per Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) | Fornisce la protezione dalle minacce a livello di cluster monitorando i servizi gestiti da AKS tramite i log recuperati dal servizio Azure Kubernetes (AKS). |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | Un broker CASB (cloud Access Security Broker) che opera su più cloud. Offre ampia visibilità, il controllo sui dati durante il trasferimento e strumenti di analisi avanzati per identificare e contrastare minacce informatiche per tutti i servizi cloud. |

## <a name="investigate-and-respond"></a>Analizzare e rispondere

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="Diagramma che illustra i servizi di Azure che consentono di analizzare e rispondere alle minacce." border="false":::

| Servizio | Descrizione |
|------|--------|
| [Azure Sentinel](../../sentinel/hunting.md) | Potente strumento di ricerca e query per cercare le minacce alla sicurezza nelle origini dati dell'organizzazione. |
| [&nbsp; &nbsp; Log &nbsp; e metriche di monitoraggio di Azure &nbsp;](../../azure-monitor/overview.md) | Offre una soluzione completa per la raccolta, l'analisi e la gestione dei dati di telemetria dagli ambienti cloud e locali. Monitoraggio di Azure [raccoglie e aggrega i dati](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) da un'ampia gamma di origini in una piattaforma dati comune in cui possono essere usati per l'analisi, la visualizzazione e gli avvisi. |
| **&nbsp; & &nbsp; Gestione dell'accesso alle &nbsp; identità** |  |
| [&nbsp; &nbsp; Report &nbsp; e monitoraggio di Azure ad &nbsp;](https://docs.microsoft.com/azure/active-directory/reports-monitoring/) | [Azure ad report](../../active-directory/reports-monitoring/overview-reports.md) forniscono una visualizzazione completa delle attività nell'ambiente in uso. |
|  | [Azure ad monitoraggio](../../active-directory/reports-monitoring/overview-monitoring.md) consente di instradare i log attività Azure ad a endpoint diversi.|
| [Cronologia di controllo di Azure AD PIM](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | Mostra tutte le assegnazioni di ruolo e le attivazioni negli ultimi 30 giorni per tutti i ruoli con privilegi. |
| **Applicazione & dati** |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate) | Fornisce strumenti per ottenere una conoscenza più approfondita di ciò che accade nell'ambiente cloud. |

## <a name="next-steps"></a>Passaggi successivi

- Comprendere la [responsabilità condivisa nel cloud](shared-responsibility.md).

- Informazioni sulle [Opzioni di isolamento nel cloud di Azure per](isolation-choices.md) gli utenti malintenzionati e non dannosi.