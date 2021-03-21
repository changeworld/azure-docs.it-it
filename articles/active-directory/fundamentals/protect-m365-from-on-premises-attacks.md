---
title: Protezione Microsoft 365 da attacchi locali
description: Il materiale sussidiario su come garantire un attacco locale non influisce Microsoft 365.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d548f4d792d8980e2aa5040b09530eaf7868c4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609907"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Protezione Microsoft 365 da attacchi locali

Molti clienti connettono le proprie reti aziendali private a Microsoft 365 per trarre vantaggio da utenti, dispositivi e applicazioni. Tuttavia, queste reti private possono essere compromesse in molti modi ben documentati. Poiché Microsoft 365 funge da sistema nervoso per molte organizzazioni, è fondamentale proteggerlo dall'infrastruttura locale compromessa.

Questo articolo illustra come configurare i sistemi per proteggere l'ambiente cloud Microsoft 365 da compromessi locali. Si concentra principalmente su: 

- Impostazioni di configurazione del tenant Azure Active Directory (Azure AD).
- In che modo Azure AD tenant possono essere connessi in modo sicuro ai sistemi locali.
- I compromessi necessari per gestire i sistemi in modo da proteggere i sistemi cloud da compromessi locali.

È consigliabile implementare queste linee guida per proteggere l'ambiente cloud Microsoft 365.
> [!NOTE]
> Questo articolo è stato pubblicato inizialmente come post di Blog. È stata spostata nella posizione corrente per la longevità e la manutenzione.
>
> Per creare una versione offline di questo articolo, usare la funzionalità di stampa in PDF del browser. Controllare di frequente gli aggiornamenti.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Vettori di minacce primari da ambienti locali compromessi


L'ambiente cloud Microsoft 365 trae vantaggio da un'infrastruttura completa di monitoraggio e sicurezza. Con l'apprendimento automatico e l'intelligenza umana, Microsoft 365 esamina il traffico in tutto il mondo. Consente di rilevare rapidamente gli attacchi e di riconfigurare quasi in tempo reale. 

Nelle distribuzioni ibride che connettono l'infrastruttura locale a Microsoft 365, molte organizzazioni delegano l'attendibilità ai componenti locali per l'autenticazione critica e le decisioni di gestione dello stato degli oggetti directory.
Sfortunatamente, se l'ambiente locale è compromesso, le relazioni di trust diventano un'opportunità per gli utenti malintenzionati di compromettere l'ambiente Microsoft 365.

I due vettori di minaccia principali sono le *relazioni di trust di Federazione* e la sincronizzazione degli *account.* Entrambi i vettori possono concedere a un utente malintenzionato l'accesso amministrativo al cloud.

* Le **relazioni di trust federative**, ad esempio l'autenticazione SAML, vengono usate per eseguire l'autenticazione Microsoft 365 tramite l'infrastruttura di identità locale. Se un certificato per la firma di token SAML viene compromesso, la federazione consente a chiunque disponga di tale certificato di rappresentare qualsiasi utente nel cloud. *Quando possibile, è consigliabile disabilitare le relazioni di trust federativa per l'autenticazione Microsoft 365.*

* La **sincronizzazione degli account** può essere usata per modificare gli utenti con privilegi (incluse le relative credenziali) o i gruppi con privilegi amministrativi in Microsoft 365. *Si consiglia di assicurarsi che gli oggetti sincronizzati non contengano privilegi oltre a un utente in Microsoft 365,* direttamente o tramite l'inclusione in ruoli o gruppi attendibili. Assicurarsi che questi oggetti non dispongano di un'assegnazione diretta o annidata in ruoli o gruppi cloud attendibili.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Protezione Microsoft 365 dalla compromissione locale


Per risolvere i vettori di minacce descritti in precedenza, è consigliabile rispettare i principi illustrati nel diagramma seguente:

![Architettura di riferimento per la protezione di Microsoft 365.](media/protect-m365/protect-m365-principles.png)

1. **Isolare completamente gli account amministratore Microsoft 365.** Devono essere:

    * Mastered in Azure AD.

     * Autenticazione eseguita tramite l'autenticazione a più fattori.

     *  Protetto da Azure AD accesso condizionale.

     *  Accessibili solo tramite workstation gestite da Azure.

    Questi account amministratore sono account con restrizioni sull'utilizzo. *Nessun account locale deve avere privilegi amministrativi in Microsoft 365.* 

    Per ulteriori informazioni, vedere la [Panoramica dei ruoli di amministratore Microsoft 365](/microsoft-365/admin/add-users/about-admin-roles). Vedere anche [ruoli per Microsoft 365 in Azure ad](../roles/m365-workload-docs.md).

1. **Gestire i dispositivi da Microsoft 365.** Usare Azure AD join e la gestione di dispositivi mobili (MDM) basata sul cloud per eliminare le dipendenze dall'infrastruttura di gestione dei dispositivi locale. Queste dipendenze possono compromettere i controlli dispositivo e sicurezza.

1. **Assicurarsi che nessun account locale disponga di privilegi elevati per Microsoft 365.**
    Alcuni account accedono ad applicazioni locali che richiedono l'autenticazione NTLM, LDAP o Kerberos. Questi account devono trovarsi nell'infrastruttura di identità locale dell'organizzazione. Assicurarsi che questi account, inclusi gli account del servizio, non siano inclusi in gruppi o ruoli cloud con privilegi. Assicurarsi inoltre che le modifiche apportate a questi account non possano influire sull'integrità dell'ambiente cloud. Il software locale con privilegi non deve essere in grado di influenzare Microsoft 365 account o ruoli con privilegi.

1. **Usare Azure ad autenticazione cloud** per eliminare le dipendenze dalle credenziali locali. Usare sempre l'autenticazione avanzata, ad esempio Windows Hello, FIDO, Microsoft Authenticator o Azure AD l'autenticazione a più fattori.

## <a name="specific-security-recommendations"></a>Raccomandazioni specifiche sulla sicurezza


Le sezioni seguenti forniscono indicazioni specifiche sull'implementazione dei principi descritti in precedenza.

### <a name="isolate-privileged-identities"></a>Isolare le identità con privilegi


In Azure AD, gli utenti con ruoli con privilegi, ad esempio amministratori, rappresentano la radice di attendibilità per creare e gestire il resto dell'ambiente. Implementare le procedure seguenti per ridurre al minimo gli effetti di un compromesso.

* Usare account solo cloud per Azure AD e Microsoft 365 ruoli con privilegi.

* Distribuire i [dispositivi con accesso con privilegi](/security/compass/privileged-access-devices#device-roles-and-profiles) per l'accesso con privilegi per gestire Microsoft 365 e Azure ad.

*  Distribuire [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) per l'accesso JIT (just-in-Time) a tutti gli account umani con ruoli con privilegi. Richiedere l'autenticazione avanzata per attivare i ruoli.

* Fornire ruoli amministrativi che consentano i [privilegi minimi necessari per eseguire le attività necessarie](../roles/delegate-by-task.md).

* Per abilitare un'esperienza di assegnazione di ruolo avanzata che includa contemporaneamente delega e più ruoli, è consigliabile utilizzare Azure AD gruppi di sicurezza o gruppi di Microsoft 365. Questi gruppi sono chiamati collettivamente *cloud groups*. Abilitare anche il [controllo degli accessi in base al ruolo](../roles/groups-assign-role.md). È possibile utilizzare le [unità amministrative](../roles/administrative-units.md) per limitare l'ambito dei ruoli a una parte dell'organizzazione.

* Distribuire gli [account di accesso di emergenza](../roles/security-emergency-access.md). *Non* usare insiemi di credenziali delle password locali per archiviare le credenziali.

Per altre informazioni, vedere [protezione dell'accesso con privilegi](/security/compass/overview). Vedere anche [procedure di accesso protetto per gli amministratori in Azure ad](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Usa autenticazione cloud 

Le credenziali sono un vettore di attacco primario. Implementare le procedure seguenti per rendere più sicure le credenziali:

* [Distribuire l'autenticazione con password](../authentication/howto-authentication-passwordless-deployment.md). Ridurre il più possibile l'utilizzo delle password tramite la distribuzione di credenziali con password. Queste credenziali vengono gestite e convalidate in modo nativo nel cloud. Scegliere tra i metodi di autenticazione seguenti:

   * [Windows Hello for business](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [App Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [Chiavi di sicurezza FIDO2](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Distribuire l'autenticazione](../authentication/howto-mfa-getstarted.md)a più fattori. Eseguire il provisioning [di più credenziali complesse utilizzando Azure ad autenticazione a più fattori](../fundamentals/resilience-in-credentials.md). In questo modo, l'accesso alle risorse cloud richiede una credenziale gestita in Azure AD oltre a una password locale che può essere modificata. Per altre informazioni, vedere [creare una strategia di gestione del controllo di accesso resiliente usando Azure ad](./resilience-overview.md).

### <a name="limitations-and-tradeoffs"></a>Limitazioni e compromessi

* La gestione delle password dell'account ibrido richiede componenti ibridi, ad esempio agenti di protezione delle password e agenti di writeback delle password. Se l'infrastruttura locale è compromessa, gli utenti malintenzionati possono controllare i computer in cui si trovano questi agenti. Questa vulnerabilità non comprometterà l'infrastruttura cloud. Tuttavia, gli account cloud non proteggono questi componenti da compromessi locali.

*  Gli account locali sincronizzati da Active Directory sono contrassegnati in modo da non scadere mai nel Azure AD. Questa impostazione viene in genere attenuata in base alle impostazioni della password Active Directory locali. Tuttavia, se l'istanza locale di Active Directory è compromessa e la sincronizzazione è disabilitata, è necessario impostare l'opzione [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) per forzare le modifiche delle password.

## <a name="provision-user-access-from-the-cloud"></a>Eseguire il provisioning dell'accesso utente dal cloud

Il *provisioning* si riferisce alla creazione di account utente e gruppi in applicazioni o provider di identità.

![Diagramma dell'architettura di provisioning.](media/protect-m365/protect-m365-provision.png)

Si consiglia di usare i metodi di provisioning seguenti:

* Effettuare il provisioning **da app HR cloud a Azure ad**: questo provisioning consente di isolare una compromissione locale senza compromettere il ciclo di join-Mover-Leaver dalle app HR cloud a Azure ad.

* **Applicazioni cloud**: laddove possibile, distribuire [Azure ad il provisioning delle app](../app-provisioning/user-provisioning.md) in contrapposizione alle soluzioni di provisioning locali. Questo metodo protegge alcune delle app SaaS (software-as-a-Service) interessate da profili hacker dannosi in violazioni locali. 

* **Identità esterne**: usare [Azure ad collaborazione B2B](../external-identities/what-is-b2b.md).
    Questo metodo riduce la dipendenza da account locali per la collaborazione esterna con partner, clienti e fornitori. Valutare con attenzione qualsiasi federazione diretta con altri provider di identità. È consigliabile limitare gli account Guest B2B nei modi seguenti:

   *  Limitare l'accesso Guest ai gruppi di esplorazione e ad altre proprietà della directory. Usare le impostazioni di collaborazione esterna per limitare la capacità dei guest di leggere i gruppi di cui non si è membri. 

    *   Blocca l'accesso al portale di Azure. È possibile eseguire rare eccezioni necessarie.  Creare un criterio di accesso condizionale che includa tutti i guest e gli utenti esterni. [Implementare quindi un criterio per bloccare l'accesso](../../role-based-access-control/conditional-access-azure-management.md). 

* **Foreste disconnesse**: usare [Azure ad il provisioning cloud](../cloud-sync/what-is-cloud-sync.md). Questo metodo consente di connettersi a foreste disconnesse, eliminando la necessità di stabilire la connettività o i trust tra foreste, che possono ampliare l'effetto di una violazione locale. 
 
### <a name="limitations-and-tradeoffs"></a>Limitazioni e compromessi

Quando viene usato per eseguire il provisioning di account ibridi, il sistema Azure-AD-from-cloud-HR si basa su una sincronizzazione locale per completare il flusso di dati da Active Directory a Azure AD. Se la sincronizzazione viene interrotta, i nuovi record dei dipendenti non saranno disponibili in Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Usare i gruppi di cloud per la collaborazione e l'accesso

I gruppi di cloud consentono di separare la collaborazione e l'accesso dall'infrastruttura locale.

* **Collaborazione**: usare gruppi di Microsoft 365 e Microsoft teams per la collaborazione moderna. Rimuovere le autorizzazioni dalle liste di distribuzione locali e [aggiornare le liste di distribuzione per Microsoft 365 gruppi in Outlook](/office365/admin/manage/upgrade-distribution-lists).

* **Accesso**: usare gruppi di sicurezza o gruppi di Microsoft 365 Azure ad per autorizzare l'accesso alle applicazioni in Azure ad.
* **Licenze di office 365**: usare le licenze basate sui gruppi per eseguire il provisioning in Office 365 usando gruppi solo cloud. Questo metodo separa il controllo dell'appartenenza al gruppo dall'infrastruttura locale.

I proprietari dei gruppi usati per l'accesso devono essere considerati identità con privilegi per evitare l'acquisizione di membri in una compromissione locale.
Un'acquisizione potrebbe includere la manipolazione diretta dell'appartenenza al gruppo in locale o la manipolazione di attributi locali che possono influenzare l'appartenenza dinamica ai gruppi in Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Gestire dispositivi dal cloud


Usare Azure AD funzionalità per gestire in modo sicuro i dispositivi.

-   **Usare le workstation Windows 10**: [distribuire Azure ad dispositivi aggiunti](../devices/azureadjoin-plan.md) con criteri MDM. Abilitare [Windows Autopilot](/mem/autopilot/windows-autopilot) per un'esperienza di provisioning completamente automatizzata.

    -   Deprecare i computer che eseguono Windows 8.1 e versioni precedenti.

    -   Non distribuire computer del sistema operativo server come workstation.

    -   Usare [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) come origine dell'autorità per tutti i carichi di lavoro di gestione dei dispositivi.

-   [**Distribuire i dispositivi di accesso con privilegi**](/security/compass/privileged-access-devices#device-roles-and-profiles): usare l'accesso con privilegi per gestire Microsoft 365 e Azure ad.

## <a name="workloads-applications-and-resources"></a>Carichi di lavoro, applicazioni e risorse 

-   **Sistemi SSO (Single Sign-on) locali** 

    Deprecare qualsiasi infrastruttura di gestione di accesso Web e di Federazione locale. Configurare le applicazioni per l'utilizzo di Azure AD.  

-   **Applicazioni SaaS e line-of-business (LOB) che supportano i protocolli di autenticazione moderni** 

    [Utilizzare Azure ad per SSO](../manage-apps/what-is-single-sign-on.md). Maggiore è il numero di app configurate per l'uso Azure AD per l'autenticazione, minore è il rischio in una compromissione locale.


* **Applicazioni legacy** 

   * È possibile abilitare l'autenticazione, l'autorizzazione e l'accesso remoto alle applicazioni legacy che non supportano l'autenticazione moderna. Usare [Azure ad proxy di applicazione](../manage-apps/application-proxy.md). È anche possibile abilitarli tramite una soluzione di rete o controller per la distribuzione di applicazioni usando le [integrazioni sicure dei partner di accesso ibrido](../manage-apps/secure-hybrid-access.md).   

   * Scegliere un fornitore VPN che supporti l'autenticazione moderna. Integrare l'autenticazione con Azure AD. In una compromissione locale è possibile usare Azure AD per disabilitare o bloccare l'accesso disabilitando la VPN.

*  **Server applicazioni e carichi di lavoro**

   * Applicazioni o risorse per cui è possibile eseguire la migrazione dei server richiesti nell'infrastruttura distribuita come servizio (IaaS) di Azure. Usare [Azure ad Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) per separare il trust e la dipendenza dalle istanze locali di Active Directory. Per eseguire questa separazione, assicurarsi che le reti virtuali usate per Azure AD DS non dispongano di una connessione alle reti aziendali.

   * Seguire le istruzioni per la suddivisione in [livelli delle credenziali](/security/compass/privileged-access-access-model#ADATM_BM). I server applicazioni sono in genere considerati asset di primo livello.

## <a name="conditional-access-policies"></a>Criteri di accesso condizionale

Usare Azure AD l'accesso condizionale per interpretare i segnali e usarli per prendere decisioni di autenticazione. Per altre informazioni, vedere il [piano di distribuzione dell'accesso condizionale](../conditional-access/plan-conditional-access.md).

* Usare l'accesso condizionale per [bloccare i protocolli di autenticazione legacy](../conditional-access/howto-conditional-access-policy-block-legacy.md) laddove possibile. Inoltre, disabilitare i protocolli di autenticazione legacy a livello di applicazione usando una configurazione specifica dell'applicazione.

   Per ulteriori informazioni, vedere [protocolli di autenticazione legacy](../fundamentals/auth-sync-overview.md). In alternativa, vedere dettagli specifici per [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) e [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant).

* Implementare le configurazioni consigliate [per l'identità e l'accesso ai dispositivi](/microsoft-365/security/office-365-security/identity-access-policies).

* Se si usa una versione di Azure AD che non include l'accesso condizionale, assicurarsi di usare le [impostazioni predefinite di sicurezza di Azure ad](../fundamentals/concept-fundamentals-security-defaults.md).

   Per ulteriori informazioni sulle licenze per le funzionalità Azure AD, vedere la [Guida ai prezzi Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitor"></a>Monitoraggio 

Dopo aver configurato l'ambiente per proteggere la Microsoft 365 da un compromesso locale, monitorare in modo [proattivo](../reports-monitoring/overview-monitoring.md) l'ambiente.
### <a name="scenarios-to-monitor"></a>Scenari da monitorare

Monitorare gli scenari principali seguenti, oltre a eventuali scenari specifici dell'organizzazione. Ad esempio, è necessario monitorare in modo proattivo l'accesso alle applicazioni e alle risorse cruciali per l'azienda.

* **Attività sospetta** 

    Monitora tutti [gli eventi di rischio Azure ad](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) per attività sospette. [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md) è integrato in modo nativo con il Centro sicurezza di Azure.

    Definire i percorsi di rete [denominati](../reports-monitoring/quickstart-configure-named-locations.md) per evitare rilevamenti fastidiosi sui segnali basati sulla posizione. 
*  **Avvisi offrono dati (User and Entity Behavioral Analytics)** 

    Usare offrono dati per ottenere informazioni dettagliate sul rilevamento delle anomalie.
    * Microsoft Cloud App Security (MCAS) fornisce [offrono dati nel cloud](/cloud-app-security/tutorial-ueba).

    * È possibile [integrare offrono dati locali da Azure Advanced Threat Protection (ATP)](/defender-for-identity/install-step2). MCAS legge i segnali dal Azure AD Identity Protection. 

* **Attività degli account di accesso di emergenza** 

    Monitorare qualsiasi accesso che usa [account di accesso di emergenza](../roles/security-emergency-access.md). Creare avvisi per le indagini. Questo monitoraggio deve includere: 

   * Accessi. 

   * Gestione delle credenziali. 

   * Eventuali aggiornamenti sulle appartenenze ai gruppi. 

   * Assegnazioni di applicazioni. 
* **Attività del ruolo con privilegi**

    Configurare ed esaminare gli [avvisi di sicurezza generati da Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts).
    Consente di monitorare l'assegnazione diretta dei ruoli con privilegi all'esterno di PIM generando avvisi ogni volta che un utente viene assegnato direttamente.

* **Azure AD configurazioni a livello di tenant**

    Qualsiasi modifica alle configurazioni a livello di tenant dovrebbe generare avvisi nel sistema. Queste modifiche includono ma non sono limitate a:

  *  Domini personalizzati aggiornati.  

  * Azure AD le modifiche B2B a allowlists e elenchi dei blocchi.

  * Azure AD le modifiche B2B ai provider di identità consentiti (provider di identità SAML attraverso la Federazione diretta o gli accessi di social networking).  

  * Modifiche ai criteri di accesso condizionale o di rischio. 

* **Oggetti applicazione e oggetti entità servizio**
    
   * Nuove applicazioni o entità servizio che potrebbero richiedere criteri di accesso condizionale. 

   * Credenziali aggiunte alle entità servizio.
   * Attività di consenso dell'applicazione. 

* **Ruoli personalizzati**
   * Aggiornamenti delle definizioni di ruolo personalizzate. 

   * Ruoli personalizzati appena creati. 

### <a name="log-management"></a>Gestione log

Definire una strategia di archiviazione e di conservazione dei log, la progettazione e l'implementazione per facilitare un set di strumenti coerente. Ad esempio, è possibile prendere in considerazione sistemi SIEM (Security Information and Event Management) come Sentinel di Azure, query comuni e playbook di analisi e forensi.

* **Log Azure ad**: inserire i log e i segnali generati seguendo le procedure consigliate per le impostazioni, ad esempio la diagnostica, la conservazione dei log e l'inserimento di Siem. 

    La strategia di log deve includere i registri di Azure AD seguenti:
   * Attività di accesso 

   * Log di controllo 

   * Eventi di rischio 

    Azure AD fornisce l' [integrazione di monitoraggio di Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) per il log attività di accesso e i log di controllo. Gli eventi di rischio possono essere inseriti tramite l' [API Microsoft Graph](/graph/api/resources/identityriskevent). È possibile [trasmettere i log Azure ad ai log di monitoraggio di Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Log di sicurezza del sistema operativo dell'infrastruttura ibrida**: tutti i log del sistema operativo dell'infrastruttura di identità ibrida devono essere archiviati e monitorati attentamente come un sistema di livello 0, a causa delle implicazioni della superficie di attacco. Includere gli elementi seguenti: 

   *  Azure AD Connect. Per monitorare la sincronizzazione delle identità, è necessario distribuire [Azure ad Connect Health](../hybrid/whatis-azure-ad-connect.md) .

   *  Agenti proxy di applicazione 


   * Agenti di writeback delle password 

   * Computer gateway di protezione con password  

   * Server dei criteri di rete (NPSs) con l'estensione RADIUS per l'autenticazione a più fattori Azure AD 

## <a name="next-steps"></a>Passaggi successivi
* [Rafforzare la resilienza per la gestione delle identità e degli accessi tramite Azure AD](resilience-overview.md)

* [Proteggere l'accesso esterno alle risorse](secure-external-access-resources.md) 
* [Integra tutte le tue app con Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)