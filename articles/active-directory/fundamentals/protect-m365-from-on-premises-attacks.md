---
title: Protezione Microsoft 365 da attacchi locali
description: Indicazioni su come garantire che un attacco locale non influisca Microsoft 365
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
ms.openlocfilehash: 97893dece068dfdde85159f734095401288231d2
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201349"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Protezione Microsoft 365 da attacchi locali

Molti clienti connettono le proprie reti aziendali private a Microsoft 365 per trarre vantaggio da utenti, dispositivi e applicazioni. Tuttavia, esistono molti modi ben documentati che queste reti private possono essere compromesse. Poiché Microsoft 365 funge da "sistema nervoso" per molte organizzazioni, è fondamentale proteggerlo dall'infrastruttura locale compromessa.

Questo articolo illustra come configurare i sistemi per proteggere l'ambiente cloud Microsoft 365 da compromessi locali. Si incentrano principalmente sulle impostazioni di configurazione di Azure AD tenant, sulle modalità con cui Azure AD tenant possono essere connessi in modo sicuro ai sistemi locali e sui compromessi necessari per gestire i sistemi in modo da proteggere i sistemi cloud da compromessi locali.

È consigliabile implementare queste linee guida per proteggere l'ambiente cloud Microsoft 365.
> [!NOTE]
> Questo articolo è stato pubblicato inizialmente come post di Blog. È stato spostato qui per la longevità e la manutenzione. <br>
Per creare una versione offline di questo articolo, usare la funzionalità di stampa del browser in formato PDF. Controllare di frequente gli aggiornamenti.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Vettori di minacce primari da ambienti locali compromessi


L'ambiente cloud Microsoft 365 trae vantaggio da un'infrastruttura completa di monitoraggio e sicurezza. L'uso dell'apprendimento automatico e dell'intelligenza umana che analizza il traffico mondiale può rilevare rapidamente gli attacchi e consentire la riconfigurazione quasi in tempo reale. Nelle distribuzioni ibride che connettono l'infrastruttura locale a Microsoft 365, molte organizzazioni delegano l'attendibilità ai componenti locali per l'autenticazione critica e le decisioni di gestione dello stato degli oggetti directory.
Sfortunatamente, se l'ambiente locale è compromesso, le relazioni di trust comportano la compromissione dell'ambiente Microsoft 365 per gli utenti malintenzionati.

I due vettori di minaccia principali sono le **relazioni di trust di Federazione** e la sincronizzazione degli **account.** Entrambi i vettori possono concedere a un utente malintenzionato l'accesso amministrativo al cloud.

* Le **relazioni di trust federative**, ad esempio l'autenticazione SAML, vengono usate per eseguire l'autenticazione Microsoft 365 tramite l'infrastruttura di identità locale. Se un certificato di firma del token SAML viene compromesso, la Federazione consentirebbe a chiunque disponga di tale certificato di rappresentare qualsiasi utente nel cloud. **Quando possibile, è consigliabile disabilitare le relazioni di trust federativa per l'autenticazione Microsoft 365.**

* La **sincronizzazione dell'account** può essere utilizzata per modificare gli utenti con privilegi (incluse le relative credenziali) o i gruppi a cui sono stati concessi privilegi amministrativi in Microsoft 365 **Si consiglia di assicurarsi che gli oggetti sincronizzati non contengano privilegi oltre a un utente in Microsoft 365,** direttamente o tramite l'inclusione in ruoli o gruppi attendibili. Assicurarsi che questi oggetti non dispongano di un'assegnazione diretta o annidata in ruoli o gruppi cloud attendibili.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Protezione Microsoft 365 dalla compromissione locale


Per risolvere i vettori di minacce descritti in precedenza, è consigliabile rispettare i principi illustrati di seguito:

![Architettura di riferimento per la protezione di Microsoft 365 ](media/protect-m365/protect-m365-principles.png)

*  **Isolare completamente gli account amministratore Microsoft 365.** Devono essere

    * Mastered in Azure AD.

     * Autenticazione con multi-factor authentication.

     *  Protetto da Azure AD accesso condizionale.

     *  Accessibili solo tramite le workstation gestite di Azure.

Si tratta di account use limitati. **In Microsoft 365 non devono essere presenti account locali con privilegi amministrativi.** Per ulteriori informazioni, vedere questa [Panoramica dei ruoli di amministratore Microsoft 365](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).
Vedere anche [ruoli per Microsoft 365 in Azure Active Directory](../roles/m365-workload-docs.md).

*  **Gestire i dispositivi da Microsoft 365.** Usare Azure AD join e la gestione di dispositivi mobili (MDM) basata sul cloud per eliminare le dipendenze dall'infrastruttura di gestione dei dispositivi locale, che può compromettere i controlli di dispositivo e sicurezza.

* **Nessun account locale dispone di privilegi elevati per Microsoft 365.**
    Gli account che accedono ad applicazioni locali che richiedono l'autenticazione NTLM, LDAP o Kerberos necessitano di un account nell'infrastruttura di identità locale dell'organizzazione. Assicurarsi che questi account, inclusi gli account del servizio, non siano inclusi in gruppi o ruoli cloud con privilegi e che le modifiche apportate a questi account non possano influisca sull'integrità dell'ambiente cloud. Il software locale con privilegi non deve essere in grado di influenzare Microsoft 365 account o ruoli con privilegi.

*  **Usare Azure ad autenticazione cloud** per eliminare le dipendenze dalle credenziali locali. Usare sempre l'autenticazione avanzata, ad esempio Windows Hello, FIDO, il Microsoft Authenticator o l'autenticazione a più fattori di Azure AD.

## <a name="specific-recommendations"></a>Raccomandazioni specifiche


Le sezioni seguenti forniscono indicazioni specifiche su come implementare i principi descritti in precedenza.

### <a name="isolate-privileged-identities"></a>Isolare le identità con privilegi


In Azure AD, gli utenti con ruoli con privilegi, ad esempio gli amministratori, rappresentano la radice di attendibilità per creare e gestire il resto dell'ambiente. Implementare le procedure seguenti per ridurre al minimo l'effetto di un compromesso.

* Usare account solo cloud per Azure AD e Microsoft 365 ruoli con privilegi. d

* Distribuire i [dispositivi con accesso con privilegi](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) per l'accesso con privilegi per gestire Microsoft 365 e Azure ad.

*  Distribuire [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) per l'accesso just-in-time (JIT) a tutti gli account umani con ruoli con privilegi e richiedere l'autenticazione avanzata per attivare i ruoli.

* Fornire ruoli amministrativi il [privilegio minimo possibile per eseguire le attività](../roles/delegate-by-task.md).

* Per abilitare un'esperienza di assegnazione di ruolo più completa che includa contemporaneamente la delega e più ruoli, è consigliabile usare Azure AD gruppi di sicurezza o gruppi di Microsoft 365 (collettivamente "gruppi di cloud") e [abilitare il controllo degli accessi in base al ruolo](../roles/groups-assign-role.md). È anche possibile usare [unità amministrative](../roles/administrative-units.md) per limitare l'ambito dei ruoli a una parte dell'organizzazione.

* Distribuire gli [account di accesso di emergenza](../roles/security-emergency-access.md) e non usare insiemi di credenziali delle password locali per archiviare le credenziali.

Per ulteriori informazioni, vedere la pagina relativa alla [protezione dell'accesso con privilegi](https://aka.ms/SPA), che include istruzioni dettagliate su questo argomento. Vedere anche [procedure di accesso protetto per gli amministratori in Azure ad](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Usa autenticazione cloud 

Le credenziali sono un vettore di attacco primario. Implementare le procedure seguenti per rendere più sicure le credenziali.

* [Distribuire l'autenticazione con password](../authentication/howto-authentication-passwordless-deployment.md): ridurre il più possibile l'uso delle password distribuendo le credenziali password. Queste credenziali vengono gestite e convalidate in modo nativo nel cloud. Scegliere tra:

   * [Windows Hello for business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [App Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [Chiavi di sicurezza FIDO2](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Distribuire multi-factor authentication](https://aka.ms/deploymentplans/mfa): effettuare il provisioning di [più credenziali complesse usando Azure ad](../fundamentals/resilience-in-credentials.md)autenticazione a più fattori In questo modo, l'accesso alle risorse cloud richiede una credenziale gestita in Azure AD oltre a una password locale che può essere modificata.

   * Per altre informazioni, vedere [creare una strategia di gestione del controllo di accesso resiliente con Azure Active Directory](https://aka.ms/resilientaad).

**Limitazioni e compromessi**

* La gestione delle password dell'account ibrido richiede componenti ibridi, ad esempio agenti di protezione delle password e agenti di writeback delle password. Se l'infrastruttura locale è compromessa, gli utenti malintenzionati possono controllare i computer in cui si trovano questi agenti. Sebbene questo non comprometterà l'infrastruttura cloud, gli account cloud non proteggeranno questi componenti da compromessi locali.

*  Gli account locali sincronizzati da Active Directory sono contrassegnati in modo da non scadere mai in Azure AD, in base al presupposto che i criteri per le password di AD locali riducono questo problema. Se l'annuncio locale è compromesso e la sincronizzazione da AD Connect deve essere disabilitata, è necessario impostare l'opzione [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="provision-user-access-from-the-cloud"></a>Eseguire il provisioning dell'accesso utente dal cloud

Il provisioning si riferisce alla creazione di account utente e gruppi in applicazioni o provider di identità.

![Diagramma dell'architettura di provisioning](media/protect-m365/protect-m365-provision.png)

* **Effettuare il provisioning da app HR cloud a Azure ad:** In questo modo è possibile isolare una compromissione locale senza compromettere il ciclo di Joiner-Mover-Leaver dalle app HR cloud a Azure AD.

* **Applicazioni cloud:** Laddove possibile, distribuire [app Azure ad il provisioning](../app-provisioning/user-provisioning.md) in contrapposizione alle soluzioni di provisioning locali. In questo modo si proteggono alcune app SaaS perché non vengono elaborate con profili utente malintenzionati a causa di violazioni locali. 

* **Identità esterne:** Usare [Azure ad collaborazione B2B](../external-identities/what-is-b2b.md).
    Questa operazione ridurrà la dipendenza dagli account locali per la collaborazione esterna con partner, clienti e fornitori. Valutare con attenzione qualsiasi federazione diretta con altri provider di identità. È consigliabile limitare gli account Guest B2B nei modi seguenti.

   *  Limitare l'accesso Guest ai gruppi di esplorazione e ad altre proprietà della directory. Usare le impostazioni di collaborazione esterna per limitare la capacità Guest di leggere i gruppi di cui non sono membri. 

    *   Blocca l'accesso al portale di Azure. È possibile eseguire rare eccezioni necessarie.  Creare un criterio di accesso condizionale che includa tutti i guest e gli utenti esterni, quindi [implementare i criteri per bloccare l'accesso](/azure/role-based-access-control/conditional-access-azure-management). 

* **Foreste disconnesse:** Usare [Azure ad il provisioning cloud](../cloud-provisioning/what-is-cloud-provisioning.md). Ciò consente di connettersi a foreste disconnesse, eliminando la necessità di stabilire la connettività tra foreste o i trust, che possono ampliare l'effetto di una violazione locale. * 
 
**Limitazioni e compromessi:**

* Quando viene usato per eseguire il provisioning di account ibridi, il Azure AD dai sistemi HR cloud si basa sulla sincronizzazione locale per completare il flusso di dati da AD a Azure AD. Se la sincronizzazione viene interrotta, i nuovi record dei dipendenti non saranno disponibili in Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Usare i gruppi di cloud per la collaborazione e l'accesso

I gruppi di cloud consentono di separare la collaborazione e l'accesso dall'infrastruttura locale.

* **Collaborazione:** USA i gruppi di Microsoft 365 e i team Microsoft per la collaborazione moderna. Rimuovere le autorizzazioni dalle liste di distribuzione locali e [aggiornare le liste di distribuzione per Microsoft 365 gruppi in Outlook](https://docs.microsoft.com/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide).

* **Accesso:** Usare Azure AD gruppi di sicurezza o gruppi di Microsoft 365 per autorizzare l'accesso alle applicazioni in Azure AD.
* **Licenze di Office 365:** Usare le licenze basate sui gruppi per eseguire il provisioning in Office 365 usando gruppi solo cloud. Questo separa il controllo dell'appartenenza al gruppo dall'infrastruttura locale.

I proprietari dei gruppi usati per l'accesso devono essere considerati identità con privilegi per evitare l'acquisizione dell'appartenenza da un compromesso locale.
Questa proprietà include la manipolazione diretta dell'appartenenza a un gruppo locale o la manipolazione di attributi locali che possono influenzare l'appartenenza dinamica ai gruppi in Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Gestire dispositivi dal cloud


Usare Azure AD funzionalità per gestire in modo sicuro i dispositivi.

-   **Usare le workstation Windows 10:** [distribuire Azure ad dispositivi aggiunti](../devices/azureadjoin-plan.md) con criteri MDM. Abilitare [Windows Autopilot](https://docs.microsoft.com/mem/autopilot/windows-autopilot) per un'esperienza di provisioning completamente automatizzata.

    -   Deprecare Windows 8.1 e i computer precedenti.

    -   Non distribuire computer del sistema operativo server come workstation.

    -   Usare [Microsoft Intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) come origine dell'autorità di tutti i carichi di lavoro di gestione dei dispositivi.

-   [**Distribuire i dispositivi con accesso con privilegi**](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) per l'accesso con privilegi per gestire Microsoft 365 e Azure ad.

 ## <a name="workloads-applications-and-resources"></a>Carichi di lavoro, applicazioni e risorse 

-   **Sistemi SSO locali:** Deprecare qualsiasi Federazione locale e Accesso Web infrastruttura di gestione e configurare le applicazioni per l'uso Azure AD.  

-   **Applicazioni SaaS e LOB che supportano i protocolli di autenticazione moderni:** [usare Azure ad per Single Sign-on](../manage-apps/what-is-single-sign-on.md). Maggiore è il numero di app configurate per l'uso Azure AD per l'autenticazione, minore è il rischio in caso di compromissione locale.


* **Applicazioni legacy** 

   * L'autenticazione, l'autorizzazione e l'accesso remoto alle applicazioni legacy che non supportano l'autenticazione moderna possono essere abilitati tramite [Azure ad proxy di applicazione](../manage-apps/application-proxy.md). Possono anche essere abilitate tramite una soluzione di rete o controller per la distribuzione di applicazioni con  [integrazioni sicure dei partner di accesso ibrido](../manage-apps/secure-hybrid-access.md).   

   * Scegliere un fornitore VPN che supporti l'autenticazione moderna e integrare l'autenticazione con Azure AD. Nel caso di un compromesso di anon-locali, è possibile usare Azure AD per disabilitare o bloccare l'accesso disabilitando la VPN.

*  **Server applicazioni e carichi di lavoro**

   * Applicazioni o risorse in cui è possibile eseguire la migrazione dei server richiesti ad Azure IaaS e usare [Azure ad Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) (Azure AD DS) per separare il trust e la dipendenza da Active Directory locale. Per ottenere questa separazione, le reti virtuali usate per Azure AD DS non devono avere la connessione alle reti aziendali.

   * Seguire le istruzioni per la suddivisione in [livelli delle credenziali](https://aka.ms/TierModel). I server applicazioni sono in genere considerati asset di livello 1.

 ## <a name="conditional-access-policies"></a>Criteri di accesso condizionale

Usare Azure AD l'accesso condizionale per interpretare i segnali e prendere decisioni di autenticazione basate su di essi. Per altre informazioni, vedere il [piano di distribuzione dell'accesso condizionale.](https://aka.ms/deploymentplans/ca)

* [Protocolli di autenticazione legacy](../fundamentals/auth-sync-overview.md): usare l'accesso condizionale per bloccare i protocolli di [autenticazione legacy](../conditional-access/howto-conditional-access-policy-block-legacy.md) laddove possibile. Inoltre, disabilitare i protocolli di autenticazione legacy a livello di applicazione usando la configurazione specifica dell'applicazione.

   * Vedere dettagli specifici per [Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) e [SharePoint Online](https://docs.microsoft.com/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps).

* Implementare le configurazioni consigliate [per l'identità e l'accesso ai dispositivi.](https://docs.microsoft.com/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)

* Se si usa una versione di Azure AD che non include l'accesso condizionale, assicurarsi di usare le [impostazioni predefinite di sicurezza di Azure ad](../fundamentals/concept-fundamentals-security-defaults.md).

   * Per ulteriori informazioni sulle licenze per le funzionalità Azure AD, vedere la [Guida ai prezzi Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitoring"></a>Monitoraggio 

Dopo aver configurato l'ambiente per proteggere la Microsoft 365 da un compromesso locale, monitorare in modo [proattivo](../reports-monitoring/overview-monitoring.md) l'ambiente.
### <a name="scenarios-to-monitor"></a>Scenari di monitoraggio

Monitorare gli scenari principali seguenti, oltre a eventuali scenari specifici dell'organizzazione. Ad esempio, è necessario monitorare in modo proattivo l'accesso alle applicazioni e alle risorse cruciali per l'azienda.

* **Attività sospetta**: tutti [gli eventi di rischio Azure ad](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#risk-detection-and-remediation) devono essere monitorati per attività sospette. [Azure ad Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) è integrato in modo nativo con il Centro sicurezza di Azure.

   * Definire i percorsi di rete [denominati](../reports-monitoring/quickstart-configure-named-locations.md) per evitare rilevamenti fastidiosi sui segnali basati sulla posizione. 
*  **Avvisi offrono dati (User Entity Behavioral Analytics)** Usare offrono dati per ottenere informazioni dettagliate sul rilevamento delle anomalie.
   * Microsoft Cloud app Discovery (MCAS) fornisce [offrono dati nel cloud](https://docs.microsoft.com/cloud-app-security/tutorial-ueba).

   * È possibile [integrare offrono dati locali da Azure ATP](https://docs.microsoft.com/defender-for-identity/install-step2). MCAS legge i segnali dal Azure AD Identity Protection. 

* **Attività degli account di accesso di emergenza**: tutti gli accessi con [account di accesso di emergenza](../roles/security-emergency-access.md) devono essere monitorati e gli avvisi creati per le indagini. Questo monitoraggio deve includere: 

   * Accessi. 

   * Gestione delle credenziali. 

   * Eventuali aggiornamenti sulle appartenenze ai gruppi. 

   *    Assegnazioni di applicazioni. 
* **Attività del ruolo con privilegi**: configurare ed esaminare gli [avvisi di sicurezza generati da Azure ad PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-configure-security-alerts?tabs=new#security-alerts).
    Consente di monitorare l'assegnazione diretta dei ruoli con privilegi all'esterno di PIM generando avvisi ogni volta che un utente viene assegnato direttamente.
* **Azure ad configurazioni a livello di tenant**: qualsiasi modifica alle configurazioni a livello di tenant deve generare avvisi nel sistema. Sono inclusi, tra l'altro,
  *  Aggiornamento di domini personalizzati  

  * Azure AD le modifiche dell'elenco Consenti/blocca B2B.
  * Azure AD provider di identità consentiti B2B (IDP SAML attraverso la Federazione diretta o gli account di accesso di social networking).  
  * Modifiche ai criteri di accesso condizionale o di rischio 

* **Oggetti applicazione e oggetti entità servizio**:
   * Nuove applicazioni o entità servizio che potrebbero richiedere criteri di accesso condizionale. 

   * Sono state aggiunte altre credenziali alle entità servizio.
   * Attività di consenso dell'applicazione. 

* **Ruoli personalizzati**:
   * Aggiornamenti delle definizioni di ruolo personalizzate. 

   * Nuovi ruoli personalizzati creati. 

### <a name="log-management"></a>Log Management

Definire una strategia di archiviazione e di conservazione dei log, la progettazione e l'implementazione per facilitare un set di strumenti coerenti, ad esempio sistemi SIEM come Azure Sentinel, query comuni e playbook di analisi e forensi.

* **Log di Azure ad** I log e i segnali di inserimento hanno prodotto le seguenti procedure consigliate coerenti, tra cui impostazioni di diagnostica, conservazione dei log e inserimento di SIEM. La strategia di log deve includere i registri di Azure AD seguenti:
   * Attività di accesso 

   * Log di controllo 

   * Eventi di rischio 

Azure AD fornisce l' [integrazione di monitoraggio di Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) per il log attività di accesso e i log di controllo. Gli eventi di rischio possono essere inseriti tramite [Microsoft Graph API](https://aka.ms/AzureADSecuredAzure/32b). È possibile [trasmettere i log Azure ad ai log di monitoraggio di Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Log di sicurezza del sistema operativo dell'infrastruttura ibrida.** Tutti i log del sistema operativo dell'infrastruttura di identità ibrida devono essere archiviati e monitorati attentamente come <br>Sistema di livello 0, date le implicazioni sulla superficie di attacco. ad esempio: 

   *  Azure AD Connect. Per monitorare la sincronizzazione delle identità, è necessario distribuire [Azure ad Connect Health](https://aka.ms/AzureADSecuredAzure/32e) .

   *  Agenti proxy di applicazione 


   * Agenti write-back password 

   * Computer gateway di protezione con password  

   * Server dei criteri di server con estensione RADIUS Azure multi-factor authentication 

## <a name="next-steps"></a>Passaggi successivi
* [Integrare la resilienza nella gestione di identità e accessi con Azure AD](resilience-overview.md)

* [Proteggere l'accesso esterno alle risorse](secure-external-access-resources.md) 
* [Integra tutte le tue app con Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)
