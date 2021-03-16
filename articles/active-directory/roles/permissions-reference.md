---
title: Ruoli predefiniti Azure AD-Azure Active Directory
description: Descrive i ruoli e le autorizzazioni predefiniti Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 03/13/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2c327154e2f4eae735e7078be3b057b1d8cf1c5
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490875"
---
# <a name="azure-ad-built-in-roles"></a>Ruoli predefiniti di Azure AD

In Azure Active Directory (Azure AD), se un altro amministratore o un utente non amministratore deve gestire le risorse Azure AD, si assegna loro un ruolo Azure AD che fornisce le autorizzazioni necessarie. Ad esempio, è possibile assegnare ruoli per consentire l'aggiunta o la modifica di utenti, la reimpostazione delle password utente, la gestione delle licenze utente o la gestione dei nomi di dominio.

Questo articolo elenca i Azure AD ruoli predefiniti che è possibile assegnare per consentire la gestione delle risorse Azure AD. Per informazioni su come assegnare i ruoli, vedere [assegnare Azure ad ruoli agli utenti](manage-roles-portal.md).

## <a name="limit-use-of-global-administrator"></a>Limitare l'utilizzo dell'amministratore globale

Gli utenti assegnati al ruolo di amministratore globale possono leggere e modificare tutte le impostazioni amministrative nell'organizzazione Azure AD. Per impostazione predefinita, quando un utente si iscrive a un servizio cloud Microsoft, viene creato un tenant di Azure AD e l'utente viene reso membro del ruolo di amministratore globale. Quando si aggiunge una sottoscrizione a un tenant esistente, non viene assegnato al ruolo di amministratore globale. Solo gli amministratori globali e gli amministratori dei ruoli con privilegi possono delegare ruoli di amministratore. Per ridurre il rischio per l'azienda, è consigliabile assegnare questo ruolo al numero minore possibile di persone nell'organizzazione.

La procedura consigliata prevede di assegnare questo ruolo a meno di cinque utenti nell'organizzazione. Se nell'organizzazione esistono più di cinque amministratori assegnati al ruolo di amministratore globale, di seguito sono riportati alcuni modi per ridurne l'uso.

## <a name="all-roles"></a>Tutti i ruoli

> [!div class="mx-tableFixed"]
> | Ruolo | Descrizione | ID modello |
> | --- | --- | --- |
> | [Amministratore applicazione](#application-administrator) | Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Sviluppatore di applicazioni](#application-developer) | Può creare registrazioni di applicazioni indipendentemente dall'impostazione 'Gli utenti possono registrare applicazioni'. | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Autore del payload di attacco](#attack-payload-author) | Consente di creare payload di attacco che un amministratore può avviare in un secondo momento. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Amministratore simulazione di attacco](#attack-simulation-administrator) | Consente di creare e gestire tutti gli aspetti delle campagne di simulazione di attacco. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Amministratore dell'autenticazione](#authentication-administrator) | Può accedere a per visualizzare, impostare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente non amministratore. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Amministratore dei criteri di autenticazione](#authentication-policy-administrator) | Consente di creare e gestire tutti gli aspetti dei metodi di autenticazione e i criteri di protezione delle password. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Amministratore locale dispositivo Azure AD aggiunto](#azure-ad-joined-device-local-administrator) | Gli utenti assegnati a questo ruolo vengono aggiunti al gruppo di amministratori locale nei dispositivi aggiunti ad Azure AD. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Amministratore di Azure DevOps](#azure-devops-administrator) | Può gestire i criteri e le impostazioni dell'organizzazione di Azure DevOps. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Amministratore di Azure Information Protection](#azure-information-protection-administrator) | Può gestire tutti gli aspetti del prodotto Azure Information Protection. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [Amministratore dei set di chiavi IEF B2C](#b2c-ief-keyset-administrator) | Consente di gestire i segreti per la Federazione e la crittografia nel Framework dell'esperienza di gestione delle identità (Framework dell'esperienza). | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [Amministratore dei criteri IEF B2C](#b2c-ief-policy-administrator) | Consente di creare e gestire criteri di Framework attendibilità nel Framework dell'esperienza di gestione delle identità (Framework dell'esperienza). | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Amministratore fatturazione](#billing-administrator) | Può eseguire attività comuni relative alla fatturazione, ad esempio aggiornare le informazioni di pagamento. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Amministratore di applicazioni cloud](#cloud-application-administrator) | Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali, ad eccezione del Proxy di applicazione. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Amministratore dispositivo cloud](#cloud-device-administrator) | Accesso limitato per gestire i dispositivi in Azure AD. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Amministratore di conformità](#compliance-administrator) | Consente di leggere e gestire la configurazione e i report di conformità in Azure AD e Microsoft 365. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Amministratore dei dati sulla conformità](#compliance-data-administrator) | Creare e gestire i contenuti relativi alla conformità. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Amministratore accesso condizionale](#conditional-access-administrator) | Può gestire le funzionalità di accesso condizionale. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Responsabile approvazione per l'accesso a Customer Lockbox](#customer-lockbox-access-approver) | Può approvare le richieste di supporto Microsoft per l'accesso ai dati aziendali dei clienti. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Amministratore Desktop Analytics](#desktop-analytics-administrator) | Consente di accedere e gestire i servizi e gli strumenti di gestione desktop. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Ruolo con autorizzazioni di lettura nella directory](#directory-readers) | Può leggere le informazioni base della directory. Utilizzato comunemente per concedere l'accesso in lettura alla directory alle applicazioni e ai guest. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Account di sincronizzazione della directory](#directory-synchronization-accounts) | Usata solo dal servizio di Azure AD Connect. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Ruolo con autorizzazioni di scrittura nella directory](#directory-writers) | Consente di leggere e scrivere informazioni di base sulla directory. Per concedere l'accesso alle applicazioni, non destinato agli utenti. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Amministratore del nome di dominio](#domain-name-administrator) | Può gestire i nomi di dominio nel cloud e in locale. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Amministratore di Dynamics 365](#dynamics-365-administrator) | Può gestire tutti gli aspetti del prodotto Dynamics 365. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Amministratore di Exchange](#exchange-administrator) | Può gestire tutti gli aspetti del prodotto Exchange. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Amministratore flusso utente ID esterno](#external-id-user-flow-administrator) | Consente di creare e gestire tutti gli aspetti dei flussi utente. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Amministratore attributo flusso utente ID esterno](#external-id-user-flow-attribute-administrator) | Consente di creare e gestire lo schema dell'attributo disponibile per tutti i flussi utente. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Amministratore dei provider di identità esterni](#external-identity-provider-administrator) | Consente di configurare i provider di identità per l'utilizzo nella Federazione diretta. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Amministratore globale](#global-administrator) | Può gestire tutti gli aspetti di Azure AD e dei servizi Microsoft che usano identità di Azure AD. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Ruolo con autorizzazioni di lettura globali](#global-reader) | Consente di leggere tutti gli elementi che un amministratore globale può, ma non di aggiornare. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Amministratore di gruppi](#groups-administrator) | I membri di questo ruolo possono creare o gestire gruppi, creare/gestire impostazioni di gruppi come i criteri di denominazione e scadenza, nonché visualizzare i gruppi attività e report di controllo. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Mittente dell'invito guest](#guest-inviter) | Può invitare utenti guest indipendentemente dall'impostazione 'I membri possono invitare utenti guest'. | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Amministratore supporto tecnico](#helpdesk-administrator) | Può reimpostare le password per gli utenti non amministratori e gli amministratori supporto tecnico. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Amministratore delle identità ibride](#hybrid-identity-administrator) | Consente di gestire Active Directory per Azure AD le impostazioni di provisioning e Federazione del cloud. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Amministratore di Insights](#insights-administrator) | Ha accesso amministrativo nell'app Microsoft 365 Insights. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Leader aziendale di Insights](#insights-business-leader) | Può visualizzare e condividere dashboard e informazioni dettagliate tramite l'app M365 Insights. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Amministratore di Intune](#intune-administrator) | Può gestire tutti gli aspetti del prodotto Intune. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Amministratore di Kaizala](#kaizala-administrator) | Può gestire le impostazioni per Microsoft Kaizala. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Amministratore della Knowledge base](#knowledge-administrator) | Può configurare la Knowledge base, l'apprendimento e altre funzionalità intelligenti. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Amministratore licenze](#license-administrator) | Possono gestire licenze dei prodotti per utenti e gruppi. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi](#message-center-privacy-reader) | Può leggere solo gli aggiornamenti e i messaggi di sicurezza nel centro messaggi di Office 365. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Ruolo con autorizzazioni di lettura per il Centro messaggi](#message-center-reader) | Può eseguire la lettura di messaggi e aggiornamenti per la propria organizzazione solo nel Centro messaggi di Office 365. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Utente Commerce moderno](#modern-commerce-user) | Può gestire gli acquisti commerciali per una società, un reparto o un team. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Amministratore di rete](#network-administrator) | Può gestire i percorsi di rete ed esaminare le informazioni dettagliate sulla progettazione della rete aziendale per le applicazioni SaaS (Software as a Service) di Microsoft 365. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Amministratore delle app di Office](#office-apps-administrator) | Può gestire i servizi cloud delle app di Office, inclusa la gestione dei criteri e delle impostazioni, e gestire la possibilità di selezionare, deselezionare e pubblicare il contenuto della funzionalità "Novità" nei dispositivi dell'utente finale. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [Supporto partner - Livello 1](#partner-tier1-support) | Non usare: non destinato all'uso generale. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Supporto partner - Livello 2](#partner-tier2-support) | Non usare: non destinato all'uso generale. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Amministratore password](#password-administrator) | Consente di reimpostare le password per gli amministratori non amministratori e password. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Amministratore di Power BI](#power-bi-administrator) | Può gestire tutti gli aspetti del prodotto Power BI. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Amministratore di Power Platform](#power-platform-administrator) | Può creare e gestire tutti gli aspetti di Microsoft Dynamics 365, PowerApps e Microsoft Flow. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Amministratore stampante](#printer-administrator) | Può gestire tutti gli aspetti delle stampanti e dei connettori delle stampanti. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Tecnico della stampante](#printer-technician) | Può registrare e annullare la registrazione delle stampanti e aggiornare lo stato della stampante. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Amministratore autenticazione con privilegi](#privileged-authentication-administrator) | Può accedere a per visualizzare, impostare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente (amministratore o non amministratore). | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Amministratore dei ruoli con privilegi](#privileged-role-administrator) | Consente di gestire le assegnazioni di ruolo in Azure AD e tutti gli aspetti delle Privileged Identity Management. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Lettore di report](#reports-reader) | Può eseguire la lettura dei report di accesso e di controllo. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Amministratore della ricerca](#search-administrator) | Consente di creare e gestire tutti gli aspetti delle impostazioni di Microsoft Search. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Editor della ricerca](#search-editor) | Può creare e gestire i contenuti editoriali, ad esempio segnalibri, domande e risposte, posizioni e planimetrie. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Amministratore della sicurezza](#security-administrator) | Consente di leggere le informazioni di sicurezza e i report e gestire la configurazione in Azure AD e Office 365. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Operatore per la sicurezza](#security-operator) | Creare e gestire gli eventi di sicurezza. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader) | Può eseguire la lettura dei report e delle informazioni di sicurezza in Azure AD e Office 365. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Amministratore servizio di supporto](#service-support-administrator) | Può eseguire la lettura delle informazioni di integrità dei servizi e gestire i ticket di supporto. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [Amministratore di SharePoint](#sharepoint-administrator) | Può gestire tutti gli aspetti del servizio SharePoint. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Amministratore di Skype for business](#skype-for-business-administrator) | Può gestire tutti gli aspetti del prodotto Skype for Business. | 75941009-915A-4869-abe7-691bff18279e |
> | [Amministratore di Teams](#teams-administrator) | Può gestire il servizio Microsoft Teams. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Amministratore delle comunicazioni con Teams](#teams-communications-administrator) | Può gestire le funzionalità per chiamate e riunioni all'interno del servizio Microsoft Teams. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Tecnico supporto comunicazioni Teams](#teams-communications-support-engineer) | Può risolvere i problemi di comunicazione all'interno di Teams tramite strumenti avanzati. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Specialista supporto comunicazioni Teams](#teams-communications-support-specialist) | Può risolvere i problemi di comunicazione all'interno di Teams tramite strumenti di base. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Amministratore di dispositivi di Teams](#teams-devices-administrator) | Consente di eseguire attività correlate alla gestione su dispositivi certificati da team. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Lettore report di riepilogo utilizzo](#usage-summary-reports-reader) | Consente di visualizzare solo le aggregazioni a livello di tenant in Microsoft 365 analisi di utilizzo e il Punteggio di produttività. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Amministratore utenti](#user-administrator) | Può gestire tutti gli aspetti di utenti e gruppi, inclusa la reimpostazione delle password per gli amministratori con limitazioni. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>Amministratore di applicazioni

gli utenti in questo ruolo possono creare e gestire tutti gli aspetti delle applicazioni aziendali, le registrazioni delle applicazioni e le impostazioni proxy dell'applicazione. Si noti che gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o nelle applicazioni aziendali.

Questo ruolo consente inoltre di concedere il consenso per le autorizzazioni delegate e le autorizzazioni dell'applicazione, ad eccezione delle autorizzazioni dell'applicazione per Microsoft Graph e Azure AD Graph.

> [!IMPORTANT]
> Questa eccezione indica che è ancora possibile fornire il consenso alle autorizzazioni dell'applicazione per _altre_ app, ad esempio app non Microsoft o app registrate. È comunque possibile _richiedere_ queste autorizzazioni come parte della registrazione dell'app, ma _concedendo_ (ovvero il consenso) a queste autorizzazioni è necessario un amministratore con privilegi più elevati, ad esempio amministratore globale.
>
>questo ruolo concede la possibilità di gestire le credenziali delle applicazioni. Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. Se all'identità dell'applicazione è stato concesso l'accesso a una risorsa, ad esempio la possibilità di creare o aggiornare l'utente o altri oggetti, un utente assegnato a questo ruolo potrebbe eseguire tali azioni durante la rappresentazione dell'applicazione. Questa possibilità di rappresentare l'identità dell'applicazione può essere un'elevazione dei privilegi rispetto alle operazioni che l'utente può eseguire tramite le assegnazioni di ruolo. È importante comprendere che assegnare a un utente il ruolo di Amministratore di applicazioni gli concede la possibilità di rappresentare l'identità di un'applicazione.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/applications/create | Creare tutti i tipi di applicazioni |
> | microsoft.directory/applications/delete | Elimina tutti i tipi di applicazioni |
> | microsoft.directory/applications/applicationProxy/read | Leggi tutte le proprietà del proxy di applicazione |
> | microsoft.directory/applications/applicationProxy/update | Aggiornare tutte le proprietà del proxy dell'applicazione |
> | Microsoft. directory/Applications/applicationProxyAuthentication/Update | Aggiornare le proprietà di autenticazione del proxy di applicazione |
> | Microsoft. directory/Applications/applicationProxySslCertificate/Update | Aggiornare i domini personalizzati del proxy applicazione |
> | Microsoft. directory/Applications/applicationProxyUrlSettings/Update | Aggiornare gli URL interni ed esterni del proxy di applicazione |
> | Microsoft. directory/Applications/appRoles/Update | Aggiornare la proprietà appRoles in tutti i tipi di applicazioni |
> | microsoft.directory/applications/audience/update | Aggiornare la proprietà audience per le applicazioni |
> | microsoft.directory/applications/authentication/update | Aggiornare l'autenticazione su tutti i tipi di applicazioni |
> | microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni |
> | microsoft.directory/applications/credentials/update | Aggiornare le credenziali dell'applicazione |
> | microsoft.directory/applications/owners/update | Aggiornare i proprietari delle applicazioni |
> | microsoft.directory/applications/permissions/update | Aggiornare le autorizzazioni esposte e le autorizzazioni necessarie per tutti i tipi di applicazioni |
> | microsoft.directory/applications/policies/update | Aggiornare i criteri delle applicazioni |
> | Microsoft. directory/applicazioni/Verifica/aggiornamento | Aggiornare la proprietà applicationsverification |
> | microsoft.directory/applications/synchronization/standard/read | Leggere le impostazioni di provisioning associate all'oggetto applicazione |
> | microsoft.directory/applicationTemplates/instantiate | Creare un'istanza di applicazioni della raccolta da modelli di applicazione |
> | microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà nei log di controllo, incluse le proprietà con privilegi |
> | microsoft.directory/connectors/create | Creare connettori del proxy di applicazione |
> | Microsoft. directory/connettori/allProperties/lettura | Leggi tutte le proprietà dei connettori del proxy di applicazione |
> | microsoft.directory/connectorGroups/create | Creare gruppi di connettori del proxy di applicazione |
> | microsoft.directory/connectorGroups/delete | Elimina gruppi di connettori del proxy di applicazione |
> | Microsoft. directory/connectorGroups/allProperties/Read | Leggi tutte le proprietà dei gruppi di connettori del proxy di applicazione |
> | Microsoft. directory/connectorGroups/allProperties/Update | Aggiornare tutte le proprietà dei gruppi di connettori del proxy di applicazione |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare le concessioni di autorizzazione OAuth 2,0, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/applicationPolicies/create | Creare criteri di applicazione |
> | microsoft.directory/applicationPolicies/delete | Elimina criteri di applicazione |
> | microsoft.directory/applicationPolicies/standard/read | Leggere le proprietà standard dei criteri dell'applicazione |
> | microsoft.directory/applicationPolicies/owners/read | Leggere i proprietari nei criteri di applicazione |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Legge i criteri dell'applicazione applicati all'elenco oggetti |
> | microsoft.directory/applicationPolicies/basic/update | Aggiornare le proprietà standard dei criteri dell'applicazione |
> | microsoft.directory/applicationPolicies/owners/update | Aggiornare la proprietà Owner dei criteri dell'applicazione |
> | microsoft.directory/provisioningLogs/allProperties/read | Lettura di tutte le proprietà dei log di provisioning. |
> | microsoft.directory/servicePrincipals/create | Creare entità servizio |
> | microsoft.directory/servicePrincipals/delete | Elimina entità servizio |
> | microsoft.directory/servicePrincipals/disable | Disabilitare le entità servizio |
> | microsoft.directory/servicePrincipals/enable | Abilitare le entità servizio |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Gestire le credenziali Single Sign-On password nelle entità servizio |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gestire segreti e credenziali per il provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Avviare, riavviare e sospendere i processi sincronizzazione del provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Creare e gestire lo schema e i processi sincronizzazione del provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Leggere le credenziali Single Sign-On password nelle entità servizio |
> | Microsoft. directory/entità servizio/managePermissionGrantsForAll. Microsoft-Application-admin | Concedere il consenso per le autorizzazioni dell'applicazione e le autorizzazioni delegate per conto di un utente o di tutti gli utenti, ad eccezione delle autorizzazioni dell'applicazione per Microsoft Graph e Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare le assegnazioni di ruolo dell'entità servizio |
> | microsoft.directory/servicePrincipals/audience/update | Aggiornare le proprietà del gruppo di destinatari nelle entità servizio |
> | microsoft.directory/servicePrincipals/authentication/update | Aggiornare le proprietà di autenticazione nelle entità servizio |
> | microsoft.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base per le entità servizio |
> | microsoft.directory/servicePrincipals/credentials/update | Aggiornare le credenziali delle entità servizio |
> | microsoft.directory/servicePrincipals/owners/update | Aggiornare i proprietari delle entità servizio |
> | microsoft.directory/servicePrincipals/permissions/update | Aggiornare le autorizzazioni delle entità servizio |
> | microsoft.directory/servicePrincipals/policies/update | Aggiornare i criteri delle entità servizio |
> | microsoft.directory/servicePrincipals/tag/update | Aggiornare la proprietà Tag per le entità servizio |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Leggere le impostazioni di provisioning associate all'entità servizio |
> | microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà nei report di accesso, incluse le proprietà con privilegi |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="application-developer"></a>Sviluppatore di applicazioni

gli utenti in questo ruolo possono creare registrazioni di applicazioni quando l'opzione "Gli utenti possono registrare applicazioni" è impostata su No. Questo ruolo concede anche l'autorizzazione per fornire il consenso per proprio conto quando l'opzione "Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto" è impostata su No. Gli utenti assegnati a questo ruolo vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o di applicazioni aziendali.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | Creare tutti i tipi di applicazioni e l'autore viene aggiunto come primo proprietario |
> | microsoft.directory/appRoleAssignments/createAsOwner | Creare assegnazioni di ruolo applicazione, con il creatore come primo proprietario |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | Creare concessioni di autorizzazione OAuth 2,0, con Creator come primo proprietario |
> | microsoft.directory/servicePrincipals/createAsOwner | Creare entità servizio con il creatore come primo proprietario |

## <a name="attack-payload-author"></a>Autore del payload di attacco

Gli utenti con questo ruolo possono creare payload di attacco ma non avviarli o pianificarli. I payload di attacco sono quindi disponibili per tutti gli amministratori del tenant che possono usarli per creare una simulazione.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. office365. protectionCenter/attackSimulator/payload/allProperties/allTasks | Creare e gestire i payload di attacco nel simulatore di attacco |
> | Microsoft. office365. protectionCenter/attackSimulator/Reports/allProperties/Read | Leggere i report sulla simulazione degli attacchi, le risposte e il training associato |

## <a name="attack-simulation-administrator"></a>Amministratore simulazione di attacco

Gli utenti con questo ruolo possono creare e gestire tutti gli aspetti della creazione della simulazione di attacco, l'avvio o la pianificazione di una simulazione e la verifica dei risultati della simulazione. I membri di questo ruolo dispongono di questo accesso per tutte le simulazioni nel tenant.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. office365. protectionCenter/attackSimulator/payload/allProperties/allTasks | Creare e gestire i payload di attacco nel simulatore di attacco |
> | Microsoft. office365. protectionCenter/attackSimulator/Reports/allProperties/Read | Leggere i report sulla simulazione degli attacchi, le risposte e il training associato |
> | Microsoft. office365. protectionCenter/attackSimulator/Simulation/allProperties/allTasks | Creare e gestire modelli di simulazione di attacco nel simulatore di attacco |

## <a name="authentication-administrator"></a>Amministratore dell'autenticazione

Gli utenti con questo ruolo possono impostare o reimpostare qualsiasi metodo di autenticazione (incluse le password) per gli utenti non amministratori e alcuni ruoli. Gli amministratori dell'autenticazione possono richiedere agli utenti non amministratori o assegnati ad alcuni ruoli di eseguire di nuovo la registrazione con credenziali esistenti diverse dalle password (ad esempio, MFA o FIDO) e anche revocare l'opzione **Memorizza Multi-Factor Authentication** nel dispositivo, richiedendo l'autenticazione MFA all'accesso successivo. Per un elenco dei ruoli che un amministratore di autenticazione può leggere o aggiornare i metodi di autenticazione, vedere [autorizzazioni di reimpostazione della password](#password-reset-permissions).

Il ruolo [amministratore autenticazione con privilegi](#privileged-authentication-administrator) dispone dell'autorizzazione per forzare la ripetizione della registrazione e l'autenticazione a più fattori per tutti gli utenti.

Il ruolo di [amministratore dei criteri di autenticazione](#authentication-policy-administrator) dispone delle autorizzazioni per impostare i criteri del metodo di autenticazione del tenant che determina quali metodi possono essere registrati e usati da ogni utente.

| Ruolo | Gestire i metodi di autenticazione dell'utente | Gestire l'autenticazione a più fattori per utente | Gestire le impostazioni dell'autenticazione a più fattori | Gestisci criteri metodo di autenticazione | Gestire i criteri di protezione delle password |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Amministratore dell'autenticazione | Sì per alcuni utenti (vedere sopra) | Sì per alcuni utenti (vedere sopra) | No | No | No |
| Amministratore autenticazione con privilegi| Sì per tutti gli utenti | Sì per tutti gli utenti | No | No | No |
| Amministratore dei criteri di autenticazione | No |No | Sì | Sì | Sì |

> [!IMPORTANT]
> gli utenti con questo ruolo possono modificare le credenziali di utenti che possono avere accesso a dati sensibili, informazioni private o configurazioni critiche sia all'interno che all'esterno di Azure Active Directory. La modifica delle credenziali di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
>
>* Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli amministratori dell'autenticazione. Attraverso questo percorso, un amministratore di autenticazione può assumere l'identità del proprietario di un'applicazione e quindi assumere ulteriormente l'identità di un'applicazione con privilegi aggiornando le credenziali per l'applicazione.
>* Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
>* I proprietari del gruppo di sicurezza e del gruppo di Microsoft 365, che possono gestire l'appartenenza ai gruppi. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
>* Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
>* Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

> [!IMPORTANT]
> Questo ruolo non è attualmente in grado di gestire l'autenticazione a più fattori per utente nel portale di gestione dell'autenticazione a più fattori legacy. Le stesse funzioni possono essere eseguite usando il modulo di PowerShell [set-MsolUser](/powershell/module/msonline/set-msoluser) cmdlet Azure ad.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forza la disconnessione invalidando i token di aggiornamento utente |
> | microsoft.directory/users/strongAuthentication/update | Aggiornare la proprietà autenticazione avanzata per gli utenti |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="authentication-policy-administrator"></a>Amministratore dei criteri di autenticazione

Gli utenti con questo ruolo possono configurare i criteri di autenticazione, l'autenticazione a più fattori a livello di tenant e i criteri di protezione delle password. Questo ruolo concede le autorizzazioni per gestire le impostazioni di protezione delle password: configurazioni di blocco intelligente e aggiornamento dell'elenco di password escluse.

I ruoli amministratore [autenticazione](#authentication-administrator) e [amministratore con privilegi di autenticazione](#privileged-authentication-administrator) dispongono delle autorizzazioni per gestire i metodi di autenticazione registrati sugli utenti e possono forzare la ripetizione della registrazione e l'autenticazione a più fattori per tutti gli utenti.

| Ruolo | Gestire i metodi di autenticazione dell'utente | Gestire l'autenticazione a più fattori per utente | Gestire le impostazioni dell'autenticazione a più fattori | Gestisci criteri metodo di autenticazione | Gestire i criteri di protezione delle password |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Amministratore dell'autenticazione | Sì per alcuni utenti (vedere sopra) | Sì per alcuni utenti (vedere sopra) | No | No | No |
| Amministratore autenticazione con privilegi| Sì per tutti gli utenti | Sì per tutti gli utenti | No | No | No |
| Amministratore dei criteri di autenticazione | No | No | Sì | Sì | Sì |

> [!IMPORTANT]
> Questo ruolo non è attualmente in grado di gestire le impostazioni dell'autenticazione a più fattori nel portale di gestione di multi-factor authentication.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/Organization/strongAuthentication/Update | Aggiornare le proprietà di autenticazione avanzata di un'organizzazione |
> | Microsoft. directory/userCredentialPolicies/crea | Creare criteri di credenziali per gli utenti |
> | Microsoft. directory/userCredentialPolicies/Delete | Elimina criteri credenziali per gli utenti |
> | Microsoft. directory/userCredentialPolicies/standard/lettura | Leggere le proprietà standard dei criteri delle credenziali per gli utenti |
> | Microsoft. directory/userCredentialPolicies/owners/Read | Leggere i proprietari dei criteri delle credenziali per gli utenti |
> | Microsoft. directory/userCredentialPolicies/policyAppliedTo/Read | Leggi il collegamento di navigazione Policy. appliesTo |
> | Microsoft. directory/userCredentialPolicies/Basic/Update | Aggiornare i criteri di base per gli utenti |
> | Microsoft. directory/userCredentialPolicies/owners/Update | Aggiornare i proprietari dei criteri delle credenziali per gli utenti |
> | Microsoft. directory/userCredentialPolicies/tenantDefault/Update | Aggiornare la proprietà Policy. isOrganizationDefault |

## <a name="azure-ad-joined-device-local-administrator"></a>Amministratore locale dispositivo Azure AD aggiunto

questo ruolo è disponibile per l'assegnazione solo come amministratore locale aggiuntivo in [Impostazioni dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Gli utenti con questo ruolo diventano amministratori del computer locale in tutti i dispositivi Windows 10 aggiunti ad Azure Active Directory. Non possono gestire gli oggetti di dispositivi in Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/groupSettings/standard/lettura | Leggere le proprietà di base nelle impostazioni del gruppo |
> | Microsoft. directory/groupSettingTemplates/standard/lettura | Leggere le proprietà di base nei modelli di impostazioni di gruppo |

## <a name="azure-devops-administrator"></a>Amministratore di Azure DevOps

Gli utenti con questo ruolo possono gestire i criteri di Azure DevOps per limitare la creazione di nuove organizzazioni Azure DevOps a un set di utenti o gruppi configurabili. Gli utenti con questo ruolo possono gestire questo criterio tramite qualsiasi organizzazione DevOps di Azure supportata dall'organizzazione Azure AD della società. Questo ruolo non concede altre autorizzazioni specifiche di Azure DevOps (ad esempio, Project Collection Administrators) all'interno di una delle organizzazioni DevOps di Azure supportate dall'organizzazione Azure AD della società.

Tutti i criteri aziendali di Azure DevOps possono essere gestiti dagli utenti in questo ruolo.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | Leggere e configurare Azure DevOps |

## <a name="azure-information-protection-administrator"></a>Amministratore di Azure Information Protection

gli utenti con questo ruolo hanno tutte le autorizzazioni nel servizio Azure Information Protection. Questo ruolo consente di configurare etichette per i criteri di Azure Information Protection, gestire i modelli di protezione e attivare la protezione. Questo ruolo non concede alcuna autorizzazione in Identity Protection Center, Privileged Identity Management, monitoraggio Microsoft 365 integrità del servizio o Microsoft Office 365 Security & Compliance Center.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.informationProtection/allEntities/allTasks | Gestire tutti gli aspetti di Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="b2c-ief-keyset-administrator"></a>Amministratore dei set di chiavi IEF B2C

L'utente può creare e gestire le chiavi dei criteri e i segreti per la crittografia dei token, le firme dei token e la crittografia/decrittografia delle attestazioni.  Con l'aggiunta di nuove chiavi a contenitori di chiavi esistenti, questo amministratore limitato può eseguire il rollover dei segreti in base alle esigenze senza effetti sulle applicazioni esistenti.  Questo utente può visualizzare il contenuto completo di questi segreti e le relative date di scadenza anche dopo la loro creazione.

> [!IMPORTANT]
> Si tratta di un ruolo sensibile.  Il ruolo di amministratore dei set di chiavi deve essere controllato attentamente e assegnato con attenzione durante la pre-produzione e la produzione.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Leggere e aggiornare tutte le proprietà dei criteri di autorizzazione |

## <a name="b2c-ief-policy-administrator"></a>Amministratore dei criteri IEF B2C

Gli utenti con questo ruolo hanno la possibilità di creare, leggere, aggiornare ed eliminare tutti i criteri personalizzati in Azure AD B2C e quindi di avere il controllo completo su Identity Experience Framework nell'organizzazione Azure AD B2C pertinente. Modificando i criteri, l'utente può stabilire una federazione diretta con provider di identità esterni, modificare lo schema della directory, modificare tutti i contenuti esposti agli utenti (HTML, CSS, JavaScript), modificare i requisiti per completare un'autenticazione, creare nuovi utenti, inviare dati utente a sistemi esterni, incluse migrazioni complete, e modificare tutte le informazioni utente, inclusi i campi sensibili come password e numeri di telefono. Viceversa, questo ruolo non può modificare le chiavi di crittografia o modificare i segreti usati per la federazione nell'organizzazione.

> [!IMPORTANT]
> L'amministratore dei criteri IEF B2 è un ruolo estremamente sensibile da assegnare su base molto limitata per le organizzazioni in produzione.  Le attività svolte da questi utenti devono essere controllate attentamente, soprattutto per le organizzazioni in produzione.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Leggere e configurare i set di chiavi in Azure Active Directory B2C |

## <a name="billing-administrator"></a>Amministratore fatturazione

può effettuare acquisti, gestire le sottoscrizioni e i ticket di supporto e monitorare l'integrità dei servizi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/organization/basic/update | Aggiornare le proprietà di base nell'organizzazione |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.commerce.billing/allEntities/allTasks | Gestire tutti gli aspetti della fatturazione di Office 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="cloud-application-administrator"></a>Amministratore applicazione cloud

gli utenti in questo ruolo hanno le stesse autorizzazioni del ruolo di amministratore di applicazioni, esclusa la possibilità di gestire il proxy dell'applicazione. Questo ruolo concede la possibilità di creare e gestire tutti gli aspetti delle applicazioni aziendali e delle registrazioni dell'applicazione. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o di applicazioni aziendali.

Questo ruolo consente inoltre di concedere il consenso per le autorizzazioni delegate e le autorizzazioni dell'applicazione, ad eccezione delle autorizzazioni dell'applicazione per Microsoft Graph e Azure AD Graph.

> [!IMPORTANT]
> Questa eccezione indica che è ancora possibile fornire il consenso alle autorizzazioni dell'applicazione per _altre_ app, ad esempio app non Microsoft o app registrate. È comunque possibile _richiedere_ queste autorizzazioni come parte della registrazione dell'app, ma _concedendo_ (ovvero il consenso) a queste autorizzazioni è necessario un amministratore con privilegi più elevati, ad esempio amministratore globale.
>
>questo ruolo concede la possibilità di gestire le credenziali delle applicazioni. Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. Se all'identità dell'applicazione è stato concesso l'accesso a una risorsa, ad esempio la possibilità di creare o aggiornare l'utente o altri oggetti, un utente assegnato a questo ruolo potrebbe eseguire tali azioni durante la rappresentazione dell'applicazione. Questa possibilità di rappresentare l'identità dell'applicazione può essere un'elevazione dei privilegi rispetto alle operazioni che l'utente può eseguire tramite le assegnazioni di ruolo. È importante comprendere che assegnare a un utente il ruolo di Amministratore di applicazioni gli concede la possibilità di rappresentare l'identità di un'applicazione.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/applications/create | Creare tutti i tipi di applicazioni |
> | microsoft.directory/applications/delete | Elimina tutti i tipi di applicazioni |
> | Microsoft. directory/Applications/appRoles/Update | Aggiornare la proprietà appRoles in tutti i tipi di applicazioni |
> | microsoft.directory/applications/audience/update | Aggiornare la proprietà audience per le applicazioni |
> | microsoft.directory/applications/authentication/update | Aggiornare l'autenticazione su tutti i tipi di applicazioni |
> | microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni |
> | microsoft.directory/applications/credentials/update | Aggiornare le credenziali dell'applicazione |
> | microsoft.directory/applications/owners/update | Aggiornare i proprietari delle applicazioni |
> | microsoft.directory/applications/permissions/update | Aggiornare le autorizzazioni esposte e le autorizzazioni necessarie per tutti i tipi di applicazioni |
> | microsoft.directory/applications/policies/update | Aggiornare i criteri delle applicazioni |
> | Microsoft. directory/applicazioni/Verifica/aggiornamento | Aggiornare la proprietà applicationsverification |
> | microsoft.directory/applications/synchronization/standard/read | Leggere le impostazioni di provisioning associate all'oggetto applicazione |
> | microsoft.directory/applicationTemplates/instantiate | Creare un'istanza di applicazioni della raccolta da modelli di applicazione |
> | microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà nei log di controllo, incluse le proprietà con privilegi |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare le concessioni di autorizzazione OAuth 2,0, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/applicationPolicies/create | Creare criteri di applicazione |
> | microsoft.directory/applicationPolicies/delete | Elimina criteri di applicazione |
> | microsoft.directory/applicationPolicies/standard/read | Leggere le proprietà standard dei criteri dell'applicazione |
> | microsoft.directory/applicationPolicies/owners/read | Leggere i proprietari nei criteri di applicazione |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Legge i criteri dell'applicazione applicati all'elenco oggetti |
> | microsoft.directory/applicationPolicies/basic/update | Aggiornare le proprietà standard dei criteri dell'applicazione |
> | microsoft.directory/applicationPolicies/owners/update | Aggiornare la proprietà Owner dei criteri dell'applicazione |
> | microsoft.directory/provisioningLogs/allProperties/read | Lettura di tutte le proprietà dei log di provisioning. |
> | microsoft.directory/servicePrincipals/create | Creare entità servizio |
> | microsoft.directory/servicePrincipals/delete | Elimina entità servizio |
> | microsoft.directory/servicePrincipals/disable | Disabilitare le entità servizio |
> | microsoft.directory/servicePrincipals/enable | Abilitare le entità servizio |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Gestire le credenziali Single Sign-On password nelle entità servizio |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gestire segreti e credenziali per il provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Avviare, riavviare e sospendere i processi sincronizzazione del provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Creare e gestire lo schema e i processi sincronizzazione del provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Leggere le credenziali Single Sign-On password nelle entità servizio |
> | Microsoft. directory/entità servizio/managePermissionGrantsForAll. Microsoft-Application-admin | Concedere il consenso per le autorizzazioni dell'applicazione e le autorizzazioni delegate per conto di un utente o di tutti gli utenti, ad eccezione delle autorizzazioni dell'applicazione per Microsoft Graph e Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare le assegnazioni di ruolo dell'entità servizio |
> | microsoft.directory/servicePrincipals/audience/update | Aggiornare le proprietà del gruppo di destinatari nelle entità servizio |
> | microsoft.directory/servicePrincipals/authentication/update | Aggiornare le proprietà di autenticazione nelle entità servizio |
> | microsoft.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base per le entità servizio |
> | microsoft.directory/servicePrincipals/credentials/update | Aggiornare le credenziali delle entità servizio |
> | microsoft.directory/servicePrincipals/owners/update | Aggiornare i proprietari delle entità servizio |
> | microsoft.directory/servicePrincipals/permissions/update | Aggiornare le autorizzazioni delle entità servizio |
> | microsoft.directory/servicePrincipals/policies/update | Aggiornare i criteri delle entità servizio |
> | microsoft.directory/servicePrincipals/tag/update | Aggiornare la proprietà Tag per le entità servizio |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Leggere le impostazioni di provisioning associate all'entità servizio |
> | microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà nei report di accesso, incluse le proprietà con privilegi |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="cloud-device-administrator"></a>Amministratore dispositivo cloud

gli utenti con questo ruolo possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure. Il ruolo non concede le autorizzazioni per gestire eventuali altre proprietà nel dispositivo.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà nei log di controllo, incluse le proprietà con privilegi |
> | Microsoft. directory/bitlockerKeys/chiave/lettura | Leggere i metadati e la chiave di BitLocker nei dispositivi |
> | microsoft.directory/devices/delete | Elimina i dispositivi da Azure AD |
> | microsoft.directory/devices/disable | Disabilitare i dispositivi in Azure AD |
> | microsoft.directory/devices/enable | Abilitare i dispositivi in Azure AD |
> | Microsoft. directory/devices/extensionAttributes/Update | Aggiornare tutti i valori per la proprietà Devices. extensionAttributes |
> | Microsoft. directory/deviceManagementPolicies/standard/lettura | Leggere le proprietà standard nei criteri delle applicazioni per la gestione dei dispositivi |
> | Microsoft. directory/deviceManagementPolicies/Basic/Update | Aggiornare le proprietà di base sui criteri di applicazione per la gestione dei dispositivi |
> | Microsoft. directory/deviceRegistrationPolicy/standard/lettura | Leggere le proprietà standard nei criteri di registrazione del dispositivo |
> | Microsoft. directory/deviceRegistrationPolicy/Basic/Update | Aggiornare le proprietà di base nei criteri di registrazione del dispositivo |
> | microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà nei report di accesso, incluse le proprietà con privilegi |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="compliance-administrator"></a>Amministratore di conformità

gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla conformità nel Centro conformità Microsoft 365, nell'interfaccia di amministrazione di Microsoft 365, in Azure e nel Centro sicurezza e conformità di Office 365. Gli assegnatari possono anche gestire tutte le funzionalità all'interno dell'interfaccia di amministrazione di Exchange e nelle interfacce di amministrazione di Teams e Skype for Business, nonché creare ticket di supporto per Azure e Microsoft 365. Ulteriori informazioni sono disponibili in [informazioni sui ruoli di amministratore Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

In ingresso | Operazione consentita
----- | ----------
[Centro conformità Microsoft 365](https://protection.office.com) | Proteggere e gestire i dati dell'organizzazione in tutti i servizi di Microsoft 365<br>Gestire gli avvisi di conformità
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Monitorare, assegnare e verificare le attività di conformità alle normative dell'organizzazione
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire la governance dei dati<br>Eseguire analisi sui dati e di carattere legale<br>Gestire le richieste di soggetti interessati<br><br>Questo ruolo ha le stesse autorizzazioni del [gruppo di ruoli Amministratore di conformità](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) nel controllo degli accessi in base al ruolo di Centro sicurezza e conformità di Office 365.
[Intune](/intune/role-based-access-control) | Visualizzare tutti i dati di controllo di Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi<br>Creare e modificare i criteri di file e consentire azioni di governance sui file<br>Visualizzare tutti i report predefiniti in Gestione dati

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | Microsoft. directory/entitlementManagement/allProperties/Read | Leggi tutte le proprietà in Azure AD gestione dei diritti |
> | microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="compliance-data-administrator"></a>Amministratore dati di conformità

Gli utenti con questo ruolo hanno le autorizzazioni per tenere traccia dei dati nel Centro conformità Microsoft 365, nell'interfaccia di amministrazione di Microsoft 365 e in Azure. Gli utenti possono anche tenere traccia dei dati di conformità all'interno dell'interfaccia di amministrazione di Exchange, in Compliance Manager, nell'interfaccia di amministrazione di Teams e Skype for Business, nonché creare ticket di supporto per Azure e Microsoft 365. [Questa documentazione](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) contiene informazioni dettagliate sulle differenze tra l'amministratore della conformità e l'amministratore dei dati di conformità.

In ingresso | Operazione consentita
----- | ----------
[Centro conformità Microsoft 365](https://protection.office.com) | Monitorare i criteri correlati alla conformità in tutti i servizi di Microsoft 365<br>Gestire gli avvisi di conformità
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Monitorare, assegnare e verificare le attività di conformità alle normative dell'organizzazione
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire la governance dei dati<br>Eseguire analisi sui dati e di carattere legale<br>Gestire le richieste di soggetti interessati<br><br>Questo ruolo ha le stesse autorizzazioni del [gruppo di ruoli Amministratore dati di conformità](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) nel controllo degli accessi in base al ruolo di Centro sicurezza e conformità di Office 365.
[Intune](/intune/role-based-access-control) | Visualizzare tutti i dati di controllo di Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi<br>Creare e modificare i criteri di file e consentire azioni di governance sui file<br>Visualizzare tutti i report predefiniti in Gestione dati

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/cloudAppSecurity/allProperties/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in Microsoft Cloud App Security |
> | microsoft.azure.informationProtection/allEntities/allTasks | Gestire tutti gli aspetti di Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="conditional-access-administrator"></a>Amministratore accesso condizionale

Gli utenti con questo ruolo possono gestire le impostazioni di accesso condizionale di Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/conditionalAccessPolicies/crea | Creare criteri di accesso condizionale |
> | Microsoft. directory/conditionalAccessPolicies/Delete | Eliminare i criteri di accesso condizionale |
> | Microsoft. directory/conditionalAccessPolicies/standard/lettura | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/owners/Read | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/policyAppliedTo/Read | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/Basic/Update | Aggiornare le proprietà di base per i criteri di accesso condizionale |
> | Microsoft. directory/conditionalAccessPolicies/owners/Update | Aggiornare la proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/tenantDefault/Update | Aggiornare la proprietà Policies. conditionalAccess |
> | Microsoft. directory/crossTenantAccessPolicies/crea | Creare criteri di accesso tra tenant |
> | Microsoft. directory/crossTenantAccessPolicies/Delete | Eliminare i criteri di accesso tra tenant |
> | Microsoft. directory/crossTenantAccessPolicies/standard/lettura | Leggere le proprietà di base dei criteri di accesso tra tenant |
> | Microsoft. directory/crossTenantAccessPolicies/owners/Read | Leggere i proprietari dei criteri di accesso tra tenant |
> | Microsoft. directory/crossTenantAccessPolicies/policyAppliedTo/Read | Leggere la proprietà policyAppliedTo dei criteri di accesso tra tenant |
> | Microsoft. directory/crossTenantAccessPolicies/Basic/Update | Aggiornare le proprietà di base dei criteri di accesso tra tenant |
> | Microsoft. directory/crossTenantAccessPolicies/owners/Update | Aggiornare i proprietari dei criteri di accesso tra tenant |
> | Microsoft. directory/crossTenantAccessPolicies/tenantDefault/Update | Aggiornare il tenant predefinito per i criteri di accesso tra tenant |

## <a name="customer-lockbox-access-approver"></a>Responsabile approvazione per l'accesso a Customer Lockbox

gestisce le [richieste Customer Lockbox](/office365/admin/manage/customer-lockbox-requests) nell'organizzazione. Gli utenti con questo ruolo ricevono notifiche di posta elettronica per le richieste Customer Lockbox e possono approvare e rifiutare le richieste dall'interfaccia di amministrazione di Microsoft 365. Possono anche attivare o disattivare la funzionalità Customer Lockbox. Solo gli amministratori globali possono reimpostare le password degli utenti assegnati a questo ruolo.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | Gestire tutti gli aspetti di Customer Lockbox |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="desktop-analytics-administrator"></a>Amministratore di Desktop Analytics

Gli utenti in questo ruolo possono gestire Desktop Analytics, il Servizio criteri e lo Strumento di personalizzazione di Office. Per Desktop Analytics, è inclusa la possibilità di visualizzare l'inventario degli asset, creare piani di distribuzione e visualizzare lo stato della distribuzione e dell'integrità. Per il Servizio criteri e lo Strumento di personalizzazione di Office, questo ruolo consente agli utenti di gestire i criteri di Office.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Gestire tutti gli aspetti di analisi desktop |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="directory-readers"></a>Ruoli con autorizzazioni di lettura nella directory

Gli utenti con questo ruolo possono leggere le informazioni di base della directory. Questo ruolo deve essere usato per:

* Concedere l'accesso in lettura a un set specifico di utenti guest anziché concederlo a tutti gli utenti guest.
* Concedere a un set specifico di utenti non amministratori l'accesso al portale di Azure quando l'opzione "Limita l'accesso al portale Azure AD solo agli amministratori" è impostata su "Sì".
* Concedere alle entità servizio l'accesso alla directory in cui Directory.Read.All non è un'opzione.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/administrativeUnits/standard/lettura | Leggere le proprietà di base in unità amministrative |
> | microsoft.directory/administrativeUnits/members/read | Leggere i membri delle unità amministrative |
> | Microsoft. directory/applicazioni/standard/lettura | Leggere le proprietà standard delle applicazioni |
> | microsoft.directory/applications/owners/read | Leggere i proprietari delle applicazioni |
> | microsoft.directory/applications/policies/read | Leggere i criteri delle applicazioni |
> | Microsoft. directory/contatti/standard/lettura | Leggere le proprietà di base nei contatti in Azure AD |
> | microsoft.directory/contacts/memberOf/read | Leggere l'appartenenza al gruppo per tutti i contatti in Azure AD |
> | Microsoft. directory/contratti/standard/lettura | Leggere le proprietà di base nei contratti con i partner |
> | Microsoft. directory/dispositivi/standard/lettura | Leggere le proprietà di base nei dispositivi |
> | microsoft.directory/devices/memberOf/read | Lettura delle appartenenze ai dispositivi |
> | microsoft.directory/devices/registeredOwners/read | Legge i proprietari registrati dei dispositivi |
> | microsoft.directory/devices/registeredUsers/read | Leggere gli utenti registrati dei dispositivi |
> | Microsoft. directory/directoryRoles/standard/lettura | Aggiornare le proprietà di base nei ruoli Azure AD |
> | microsoft.directory/directoryRoles/eligibleMembers/read | Leggere i membri idonei dei ruoli Azure AD |
> | microsoft.directory/directoryRoles/members/read | Leggi tutti i membri dei ruoli Azure AD |
> | Microsoft. Directory/domini/standard/lettura | Leggere le proprietà di base nei domini |
> | Microsoft. Directory/gruppi/standard/lettura | Leggere le proprietà di base nei gruppi |
> | microsoft.directory/groups/appRoleAssignments/read | Legge le assegnazioni di ruolo applicazione dei gruppi |
> | microsoft.directory/groups/memberOf/read | Leggere i gruppi di cui un gruppo è membro in Azure AD |
> | microsoft.directory/groups/members/read | Leggere i membri dei gruppi |
> | microsoft.directory/groups/owners/read | Legge i proprietari dei gruppi |
> | microsoft.directory/groups/settings/read | Leggere le impostazioni dei gruppi |
> | Microsoft. directory/groupSettings/standard/lettura | Leggere le proprietà di base nelle impostazioni del gruppo |
> | Microsoft. directory/groupSettingTemplates/standard/lettura | Leggere le proprietà di base nei modelli di impostazioni di gruppo |
> | Microsoft. directory/oAuth2PermissionGrants/standard/lettura | Leggere le proprietà di base sulle concessioni di autorizzazione OAuth 2,0 |
> | Microsoft. directory/organizzazione/standard/lettura | Leggere le proprietà di base in un'organizzazione |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Leggere le autorità di certificazione attendibili per l'autenticazione con password |
> | microsoft.directory/applicationPolicies/standard/read | Leggere le proprietà standard dei criteri dell'applicazione |
> | Microsoft. directory/roleAssignments/standard/lettura | Leggere le proprietà di base sulle assegnazioni di ruolo |
> | Microsoft. directory/roleDefinitions/standard/lettura | Leggere le proprietà di base nelle definizioni di ruolo |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Leggere le assegnazioni di ruolo dell'entità servizio |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Leggere le assegnazioni di ruolo assegnate a entità servizio |
> | microsoft.directory/servicePrincipals/standard/read | Leggere le proprietà di base delle entità servizio |
> | microsoft.directory/servicePrincipals/memberOf/read | Lettura delle appartenenze a gruppi per le entità servizio |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Lettura delle concessioni di autorizzazioni delegate per le entità servizio |
> | microsoft.directory/servicePrincipals/owners/read | Leggere i proprietari delle entità servizio |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Leggere oggetti di proprietà delle entità servizio |
> | microsoft.directory/servicePrincipals/policies/read | Leggere i criteri delle entità servizio |
> | Microsoft. directory/subscribedSkus/standard/lettura | Leggere le proprietà di base nelle sottoscrizioni |
> | Microsoft. Directory/utenti/standard/lettura | Leggere le proprietà di base per gli utenti |
> | microsoft.directory/users/appRoleAssignments/read | Legge le assegnazioni di ruolo dell'applicazione degli utenti |
> | microsoft.directory/users/directReports/read | Leggi i report diretti per gli utenti |
> | microsoft.directory/users/manager/read | Gestione utenti di lettura |
> | microsoft.directory/users/memberOf/read | Lettura delle appartenenze a gruppi di utenti |
> | Microsoft. Directory/Users/oAuth2PermissionGrants/Read | Lettura delle concessioni di autorizzazioni delegate per gli utenti |
> | microsoft.directory/users/ownedDevices/read | Lettura dei dispositivi di proprietà degli utenti |
> | microsoft.directory/users/ownedObjects/read | Leggere oggetti di proprietà degli utenti |
> | microsoft.directory/users/registeredDevices/read | Lettura dei dispositivi registrati degli utenti |

## <a name="directory-synchronization-accounts"></a>Account di sincronizzazione della directory

Non usare. Questo ruolo viene assegnato automaticamente al servizio Azure AD Connect e non è progettato o supportato per altri usi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/applications/create | Creare tutti i tipi di applicazioni |
> | microsoft.directory/applications/delete | Elimina tutti i tipi di applicazioni |
> | Microsoft. directory/Applications/appRoles/Update | Aggiornare la proprietà appRoles in tutti i tipi di applicazioni |
> | microsoft.directory/applications/audience/update | Aggiornare la proprietà audience per le applicazioni |
> | microsoft.directory/applications/authentication/update | Aggiornare l'autenticazione su tutti i tipi di applicazioni |
> | microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni |
> | microsoft.directory/applications/credentials/update | Aggiornare le credenziali dell'applicazione |
> | microsoft.directory/applications/owners/update | Aggiornare i proprietari delle applicazioni |
> | microsoft.directory/applications/permissions/update | Aggiornare le autorizzazioni esposte e le autorizzazioni necessarie per tutti i tipi di applicazioni |
> | microsoft.directory/applications/policies/update | Aggiornare i criteri delle applicazioni |
> | microsoft.directory/organization/dirSync/update | Aggiornare la proprietà di sincronizzazione della directory dell'organizzazione |
> | microsoft.directory/policies/create | Creare criteri in Azure AD |
> | microsoft.directory/policies/delete | Eliminare i criteri in Azure AD |
> | microsoft.directory/policies/standard/read | Leggere le proprietà di base sui criteri |
> | microsoft.directory/policies/owners/read | Leggere i proprietari dei criteri |
> | Microsoft. directory/Policies/policyAppliedTo/Read | Lettura della proprietà Policies. policyAppliedTo |
> | microsoft.directory/policies/basic/update | Aggiornare le proprietà di base nei criteri |
> | microsoft.directory/policies/owners/update | Aggiornare i proprietari dei criteri |
> | microsoft.directory/policies/tenantDefault/update | Aggiornare i criteri dell'organizzazione predefiniti |
> | microsoft.directory/servicePrincipals/create | Creare entità servizio |
> | microsoft.directory/servicePrincipals/delete | Elimina entità servizio |
> | microsoft.directory/servicePrincipals/enable | Abilitare le entità servizio |
> | microsoft.directory/servicePrincipals/disable | Disabilitare le entità servizio |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Gestire le credenziali Single Sign-On password nelle entità servizio |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Leggere le credenziali Single Sign-On password nelle entità servizio |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Leggere le assegnazioni di ruolo dell'entità servizio |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Leggere le assegnazioni di ruolo assegnate a entità servizio |
> | microsoft.directory/servicePrincipals/standard/read | Leggere le proprietà di base delle entità servizio |
> | microsoft.directory/servicePrincipals/memberOf/read | Lettura delle appartenenze a gruppi per le entità servizio |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Lettura delle concessioni di autorizzazioni delegate per le entità servizio |
> | microsoft.directory/servicePrincipals/owners/read | Leggere i proprietari delle entità servizio |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Leggere oggetti di proprietà delle entità servizio |
> | microsoft.directory/servicePrincipals/policies/read | Leggere i criteri delle entità servizio |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare le assegnazioni di ruolo dell'entità servizio |
> | microsoft.directory/servicePrincipals/audience/update | Aggiornare le proprietà del gruppo di destinatari nelle entità servizio |
> | microsoft.directory/servicePrincipals/authentication/update | Aggiornare le proprietà di autenticazione nelle entità servizio |
> | microsoft.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base per le entità servizio |
> | microsoft.directory/servicePrincipals/credentials/update | Aggiornare le credenziali delle entità servizio |
> | microsoft.directory/servicePrincipals/owners/update | Aggiornare i proprietari delle entità servizio |
> | microsoft.directory/servicePrincipals/permissions/update | Aggiornare le autorizzazioni delle entità servizio |
> | microsoft.directory/servicePrincipals/policies/update | Aggiornare i criteri delle entità servizio |
> | microsoft.directory/servicePrincipals/tag/update | Aggiornare la proprietà Tag per le entità servizio |

## <a name="directory-writers"></a>Writer di directory

Gli utenti con questo ruolo possono leggere e aggiornare le informazioni di base di utenti, gruppi ed entità servizio. Assegnare questo ruolo solo alle applicazioni che non supportano il [Framework di consenso](../develop/quickstart-register-app.md). Non deve essere assegnato agli utenti.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. Directory/gruppi/assignLicense | Assegnare licenze prodotto a gruppi per le licenze basate sui gruppi |
> | microsoft.directory/groups/create | Creazione di gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. Directory/gruppi/reprocessLicenseAssignment | Rielaborare le assegnazioni delle licenze per le licenze basate sui gruppi |
> | microsoft.directory/groups/basic/update | Aggiornare le proprietà di base nei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. Directory/gruppi/Classificazione/aggiornamento | Aggiornare la proprietà di classificazione dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. directory/groups/dynamicMembershipRule/Update | Aggiornamento della regola di appartenenza dinamica dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. directory/groups/groupType/Update | Aggiornare la proprietà groupType per un gruppo |
> | microsoft.directory/groups/members/update | Aggiornare i membri dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. directory/groups/onPremWriteBack/Update | Aggiornare i gruppi di Azure AD da scrivere nuovamente in locale |
> | microsoft.directory/groups/owners/update | Aggiornare i proprietari dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/groups/settings/update | Aggiornare le impostazioni dei gruppi |
> | Microsoft. Directory/gruppi/visibilità/aggiornamento | Aggiornare la proprietà Visibility dei gruppi |
> | microsoft.directory/groupSettings/create | Creare le impostazioni dei gruppi |
> | microsoft.directory/groupSettings/delete | Eliminare le impostazioni dei gruppi |
> | microsoft.directory/groupSettings/basic/update | Aggiornare le proprietà di base nelle impostazioni del gruppo |
> | Microsoft. directory/oAuth2PermissionGrants/crea | Creare concessioni di autorizzazione OAuth 2,0 |
> | Microsoft. directory/oAuth2PermissionGrants/Basic/Update | Aggiornare le concessioni di autorizzazione OAuth 2,0 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gestire segreti e credenziali per il provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Avviare, riavviare e sospendere i processi sincronizzazione del provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Creare e gestire lo schema e i processi sincronizzazione del provisioning delle applicazioni |
> | Microsoft. directory/entità servizio/managePermissionGrantsForGroup. Microsoft-all-Application-Permissions | Concedere a un servizio entità l'accesso diretto ai dati di un gruppo |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare le assegnazioni di ruolo dell'entità servizio |
> | microsoft.directory/users/assignLicense | Gestire le licenze utente |
> | microsoft.directory/users/create | Aggiungere utenti |
> | Microsoft. Directory/utenti/Disabilita | Disabilita utenti |
> | Microsoft. Directory/utenti/Abilita | Abilita utenti |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forza la disconnessione invalidando i token di aggiornamento utente |
> | Microsoft. Directory/Users/reprocessLicenseAssignment | Rielaborare le assegnazioni delle licenze per gli utenti |
> | microsoft.directory/users/basic/update | Aggiornare le proprietà di base per gli utenti |
> | microsoft.directory/users/manager/update | Gestione aggiornamenti per gli utenti |
> | microsoft.directory/users/userPrincipalName/update | Aggiornare il nome dell'entità utente degli utenti |

## <a name="domain-name-administrator"></a>Amministratore del nome di dominio

Gli utenti con questo ruolo possono gestire i nomi di dominio (lettura, aggiunta, verifica, aggiornamento ed eliminazione). Possono inoltre leggere le informazioni relative alla directory su utenti, gruppi e applicazioni, in quanto questi oggetti posseggono dipendenze di dominio. Per gli ambienti locali, gli utenti con questo ruolo possono configurare i nomi di dominio per la Federazione, in modo che gli utenti associati vengano sempre autenticati in locale. Questi utenti possono quindi accedere ai servizi basati su Azure AD con le proprie password locali tramite Single Sign-On. Le impostazioni di Federazione devono essere sincronizzate tramite Azure AD Connect, in modo che gli utenti dispongano anche delle autorizzazioni per gestire Azure AD Connect.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | Creare ed eliminare domini, leggere e aggiornare tutte le proprietà |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |

## <a name="dynamics-365-administrator"></a>Amministratore di Dynamics 365

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Dynamics 365 Online, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Altre informazioni sono disponibili in [Usare il ruolo di amministratore del servizio per gestire l'organizzazione Azure AD](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Dynamics 365". È l'"Amministratore di Dynamics 365" nel [portale di Azure](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.dynamics365/allEntities/allTasks | Gestire tutti gli aspetti di Dynamics 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="exchange-administrator"></a>Amministratore di Exchange

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Exchange Online, quando il servizio è presente. È inoltre in grado di creare e gestire tutti i gruppi di Microsoft 365, gestire i ticket di supporto e monitorare l'integrità dei servizi. Altre informazioni [su Microsoft 365 ruoli di amministratore](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Exchange". È l'"Amministratore di Exchange" nel [portale di Azure](https://portal.azure.com). È l'"Amministratore di Exchange Online" nell'[interfaccia di amministrazione di Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | Leggere i membri nascosti di un gruppo |
> | Microsoft. directory/groups. Unified/create | Creare gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/Delete | Eliminare gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/Restore | Ripristinare i gruppi di Microsoft 365 |
> | Microsoft. directory/groups. Unified/Basic/Update | Aggiornare le proprietà di base nei gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/members/Update | Aggiornare i membri dei gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/owners/Update | Aggiornare i proprietari dei gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | Microsoft. office365. Exchange/allEntities/Basic/allTasks | Gestire tutti gli aspetti di Exchange Online |
> | microsoft.office365.network/performance/allProperties/read | Leggere tutte le proprietà delle prestazioni di rete nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Leggere i report sull'utilizzo di Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="external-id-user-flow-administrator"></a>ID esterno - Amministratore dei flussi utente

Gli utenti con questo ruolo possono creare e gestire i flussi utente (detti anche criteri "predefiniti") nell'portale di Azure. Questi utenti possono personalizzare il contenuto HTML/CSS/JavaScript, modificare i requisiti dell'autenticazione a più fattori, selezionare attestazioni nel token, gestire i connettori API e configurare le impostazioni di sessione per tutti i flussi utente nell'organizzazione Azure AD. D'altra parte, questo ruolo non include la possibilità di esaminare i dati dell'utente o di apportare modifiche agli attributi inclusi nello schema dell'organizzazione. Anche le modifiche ai criteri del Framework dell'esperienza di identità (noti anche come criteri personalizzati) non rientrano nell'ambito di questo ruolo.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/b2cUserFlow/allProperties/allTasks | Leggere e configurare gli attributi utente in Azure Active Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>ID esterno - Amministratore degli attributi dei flussi utente

Gli utenti con questo ruolo aggiungono o eliminano attributi personalizzati disponibili per tutti i flussi utente nell'organizzazione Azure AD.  Gli utenti con questo ruolo possono quindi modificare o aggiungere nuovi elementi allo schema degli utenti finali e influire sul comportamento di tutti i flussi utente, causando indirettamente modifiche ai dati che possono essere richiesti agli utenti finali e inviati infine come attestazioni alle applicazioni.  Questo ruolo non può modificare i flussi utente.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/b2cUserAttribute/allProperties/allTasks | Leggere e configurare criteri personalizzati in Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Amministratore dei provider di identità esterni

Questo amministratore gestisce la federazione tra le organizzazioni Azure AD e i provider di identità esterni.  Con questo ruolo gli utenti possono aggiungere nuovi provider di identità e configurare tutte le impostazioni disponibili, ad esempio percorso di autenticazione, ID del servizio e contenitori di chiavi assegnati.  Questo utente può abilitare l'organizzazione Azure AD in modo che ritenga attendibili le autenticazioni da provider di identità esterni.  L'impatto risultante sulle esperienze degli utenti finali dipende dal tipo di organizzazione:

* Azure AD organizzazioni per dipendenti e partner: l'aggiunta di una federazione, ad esempio con Gmail, avrà immediatamente un effetto su tutti gli inviti Guest non ancora riscattati. Vedere [Aggiungere Google come provider di identità per utenti guest B2B](../external-identities/google-federation.md).
* Organizzazioni Azure Active Directory B2C: l'aggiunta di una federazione, ad esempio con Facebook o con un'altra organizzazione Azure AD, non ha impatto immediato sui flussi degli utenti finali fino all'aggiunta del provider di identità come opzione in un flusso utente, definito anche criterio predefinito. Per un esempio, vedere [Configurazione di un account Microsoft come provider di identità](../../active-directory-b2c/identity-provider-microsoft-account.md).  Per modificare i flussi utente, è necessario il ruolo limitato di "Amministratore dei flussi utente B2C".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/non/allProperties/allTasks | Leggere e configurare i provider di identità in Azure Active Directory B2C |

## <a name="global-administrator"></a>Amministratore globale

gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative in Azure Active Directory, nonché ai servizi che usano identità di Azure Active Directory come Centro sicurezza Microsoft 365, Centro conformità Microsoft 365, Exchange Online, SharePoint Online e Skype for Business Online. Inoltre, gli amministratori globali possono [elevare l'accesso](../../role-based-access-control/elevate-access-global-admin.md) per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure. Ciò consente agli amministratori globali di ottenere l'accesso completo a tutte le risorse di Azure usando il rispettivo tenant Azure AD. La persona che si iscrive al Azure AD organizzazione diventa un amministratore globale. L'azienda può avere più di un amministratore globale. Gli amministratori globali possono reimpostare la password per qualsiasi utente e per tutti gli altri amministratori.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/accessReviews/allProperties/allTasks | Creare ed eliminare le verifiche di accesso e leggere e aggiornare tutte le proprietà delle verifiche di accesso in Azure AD |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Creare e gestire le unità amministrative inclusi i membri |
> | microsoft.directory/applications/allProperties/allTasks | Creare ed eliminare applicazioni, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/applications/synchronization/standard/read | Leggere le impostazioni di provisioning associate all'oggetto applicazione |
> | microsoft.directory/applicationTemplates/instantiate | Creare un'istanza di applicazioni della raccolta da modelli di applicazione |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | Creare ed eliminare appRoleAssignments, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà nei log di controllo, incluse le proprietà con privilegi |
> | Microsoft. directory/authorizationPolicy/allProperties/allTasks | Gestire tutti gli aspetti dei criteri di autorizzazione |
> | Microsoft. directory/bitlockerKeys/chiave/lettura | Leggere i metadati e la chiave di BitLocker nei dispositivi |
> | Microsoft. directory/cloudAppSecurity/allProperties/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in Microsoft Cloud App Security |
> | microsoft.directory/connectors/create | Creare connettori del proxy di applicazione |
> | Microsoft. directory/connettori/allProperties/lettura | Leggi tutte le proprietà dei connettori del proxy di applicazione |
> | microsoft.directory/connectorGroups/create | Creare gruppi di connettori del proxy di applicazione |
> | microsoft.directory/connectorGroups/delete | Elimina gruppi di connettori del proxy di applicazione |
> | Microsoft. directory/connectorGroups/allProperties/Read | Leggi tutte le proprietà dei gruppi di connettori del proxy di applicazione |
> | Microsoft. directory/connectorGroups/allProperties/Update | Aggiornare tutte le proprietà dei gruppi di connettori del proxy di applicazione |
> | microsoft.directory/contacts/allProperties/allTasks | Creare ed eliminare contatti, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/contracts/allProperties/allTasks | Creare ed eliminare contratti con i partner, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/devices/allProperties/allTasks | Creare ed eliminare dispositivi, leggere e aggiornare tutte le proprietà |
> | Microsoft. directory/deviceManagementPolicies/standard/lettura | Leggere le proprietà standard nei criteri delle applicazioni per la gestione dei dispositivi |
> | Microsoft. directory/deviceManagementPolicies/Basic/Update | Aggiornare le proprietà di base sui criteri di applicazione per la gestione dei dispositivi |
> | Microsoft. directory/deviceRegistrationPolicy/standard/lettura | Leggere le proprietà standard nei criteri di registrazione del dispositivo |
> | Microsoft. directory/deviceRegistrationPolicy/Basic/Update | Aggiornare le proprietà di base nei criteri di registrazione del dispositivo |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Creare ed eliminare ruoli della directory, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Creare ed eliminare Azure AD modelli di ruolo e leggere e aggiornare tutte le proprietà |
> | microsoft.directory/domains/allProperties/allTasks | Creare ed eliminare domini, leggere e aggiornare tutte le proprietà |
> | Microsoft. directory/entitlementManagement/allProperties/allTasks | Creare ed eliminare risorse, leggere e aggiornare tutte le proprietà in Azure AD gestione dei diritti |
> | microsoft.directory/groups/allProperties/allTasks | Creare ed eliminare gruppi, leggere e aggiornare tutte le proprietà |
> | Microsoft. directory/groupsAssignableToRoles/crea | Creare gruppi assegnabili a un ruolo |
> | Microsoft. directory/groupsAssignableToRoles/Delete | Elimina gruppi assegnabili al ruolo |
> | Microsoft. directory/groupsAssignableToRoles/Restore | Ripristinare i gruppi assegnabili ai ruoli |
> | Microsoft. directory/groupsAssignableToRoles/allProperties/Update | Aggiornare i gruppi assegnabili al ruolo |
> | microsoft.directory/groupSettings/allProperties/allTasks | Creare ed eliminare impostazioni di gruppo, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | Creare ed eliminare modelli di impostazioni di gruppo, leggere e aggiornare tutte le proprietà |
> | Microsoft. directory/identityProtection/allProperties/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in Azure AD Identity Protection |
> | Microsoft. directory/loginOrganizationBranding/allProperties/allTasks | Creare ed eliminare loginTenantBranding, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare le concessioni di autorizzazione OAuth 2,0, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/organization/allProperties/allTasks | Creare ed eliminare organizzazioni, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/policies/allProperties/allTasks | Creare ed eliminare criteri, leggere e aggiornare tutte le proprietà |
> | Microsoft. directory/conditionalAccessPolicies/allProperties/allTasks | Gestire tutte le proprietà dei criteri di accesso condizionale |
> | Microsoft. directory/privilegedIdentityManagement/allProperties/Read | Leggi tutte le risorse in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lettura di tutte le proprietà dei log di provisioning. |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Creare ed eliminare assegnazioni di ruolo e leggere e aggiornare tutte le proprietà di assegnazione di ruolo |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Creare ed eliminare definizioni di ruolo e leggere e aggiornare tutte le proprietà |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Creare ed eliminare appartenenze, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/serviceAction/activateService | È possibile eseguire l'azione "Attiva servizio" per un servizio |
> | microsoft.directory/serviceAction/disableDirectoryFeature | Consente di eseguire l'azione di servizio "Disabilita funzionalità directory" |
> | microsoft.directory/serviceAction/enableDirectoryFeature | Consente di eseguire l'azione di servizio "Abilita funzionalità directory" |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Consente di eseguire l'azione del servizio Getavailableextentionproperties |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Creare ed eliminare entità servizio, leggere e aggiornare tutte le proprietà |
> | Microsoft. directory/entità servizio/managePermissionGrantsForAll. Microsoft-Company-admin | Concedere il consenso per qualsiasi autorizzazione a qualsiasi applicazione |
> | Microsoft. directory/entità servizio/managePermissionGrantsForGroup. Microsoft-all-Application-Permissions | Concedere a un servizio entità l'accesso diretto ai dati di un gruppo |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Leggere le impostazioni di provisioning associate all'entità servizio |
> | microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà nei report di accesso, incluse le proprietà con privilegi |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | Acquistare e gestire sottoscrizioni ed eliminare sottoscrizioni |
> | microsoft.directory/users/allProperties/allTasks | Creare ed eliminare utenti, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/permissionGrantPolicies/create | Creare criteri di concessione delle autorizzazioni |
> | microsoft.directory/permissionGrantPolicies/delete | Eliminare i criteri di concessione delle autorizzazioni |
> | microsoft.directory/permissionGrantPolicies/standard/read | Leggere le proprietà standard dei criteri di concessione delle autorizzazioni |
> | microsoft.directory/permissionGrantPolicies/basic/update | Aggiornare le proprietà di base dei criteri di concessione delle autorizzazioni |
> | Microsoft. directory/servicePrincipalCreationPolicies/crea | Creare criteri di creazione dell'entità servizio |
> | Microsoft. directory/servicePrincipalCreationPolicies/Delete | Eliminare i criteri di creazione dell'entità servizio |
> | Microsoft. directory/servicePrincipalCreationPolicies/standard/lettura | Leggere le proprietà standard dei criteri di creazione dell'entità servizio |
> | Microsoft. directory/servicePrincipalCreationPolicies/Basic/Update | Aggiornare le proprietà di base dei criteri di creazione dell'entità servizio |
> | Microsoft. Azure. advancedThreatProtection/allEntities/allTasks | Gestire tutti gli aspetti di Azure Advanced Threat Protection |
> | microsoft.azure.informationProtection/allEntities/allTasks | Gestire tutti gli aspetti di Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.commerce.billing/allEntities/allTasks | Gestire tutti gli aspetti della fatturazione di Office 365 |
> | microsoft.dynamics365/allEntities/allTasks | Gestire tutti gli aspetti di Dynamics 365 |
> | microsoft.flow/allEntities/allTasks | Gestisci tutti gli aspetti di Microsoft Power automatici |
> | microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Microsoft Intune |
> | microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Gestire tutti gli aspetti di analisi desktop |
> | Microsoft. office365. Exchange/allEntities/Basic/allTasks | Gestire tutti gli aspetti di Exchange Online |
> | microsoft.office365.lockbox/allEntities/allTasks | Gestire tutti gli aspetti di Customer Lockbox |
> | microsoft.office365.messageCenter/messages/read | Leggere i messaggi nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365, esclusi i messaggi di sicurezza |
> | microsoft.office365.messageCenter/securityMessages/read | Leggere i messaggi di sicurezza nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.network/performance/allProperties/read | Leggere tutte le proprietà delle prestazioni di rete nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. office365. protectionCenter/allEntities/allProperties/allTasks | Gestire tutti gli aspetti dei centri di sicurezza e conformità |
> | Microsoft. office365. search/content/Manage | Creare ed eliminare contenuto, leggere e aggiornare tutte le proprietà in Microsoft Search |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard nel centro sicurezza e conformità di Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. office365. sharePoint/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in SharePoint |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestisci tutti gli aspetti di Skype for business online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Leggere i report sull'utilizzo di Office 365 |
> | microsoft.office365.userCommunication/allEntities/allTasks | Leggi e aggiorna la visibilità dei nuovi messaggi |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.powerApps/allEntities/allTasks | Gestisci tutti gli aspetti di Power Apps |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Gestire tutti gli aspetti di Power BI |
> | Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/allTasks | Gestire tutti gli aspetti di Microsoft Defender per l'endpoint |

## <a name="global-reader"></a>Ruolo con autorizzazioni di lettura globali

Gli utenti con questo ruolo possono leggere le impostazioni e le informazioni amministrative tra servizi Microsoft 365, ma non possono eseguire azioni di gestione. Global Reader è la controparte di sola lettura dell'amministratore globale. Assegnare il Reader globale anziché l'amministratore globale per la pianificazione, i controlli o le indagini. Usare il ruolo con autorizzazioni di lettura globali insieme ad altri ruoli amministrativi limitati, come l'amministratore di Exchange, per semplificare il lavoro senza assegnare il ruolo di amministratore globale. Il ruolo con autorizzazioni di lettura globali funziona con l'interfaccia di amministrazione di Microsoft 365, l'interfaccia di amministrazione di Exchange, l'interfaccia di amministrazione di SharePoint, l'interfaccia di amministrazione di Teams, il Centro sicurezza, il Centro conformità, l'interfaccia di amministrazione di Azure AD e l'interfaccia di amministrazione di Gestione dispositivi.

> [!NOTE]
> Il ruolo con autorizzazioni di lettura globali presenta al momento alcune limitazioni:
>
>- Interfaccia di [amministrazione di OneDrive](https://admin.onedrive.com/) : l'interfaccia di amministrazione di OneDrive non supporta il ruolo di lettore globale
>- [Interfaccia di amministrazione di M365](https://admin.microsoft.com/Adminportal/Home#/homepage) - Il ruolo con autorizzazioni di lettura globali non è in grado di leggere le richieste di Customer Lockbox. Non è possibile trovare la scheda **Richieste di Customer Lockbox** in **Supporto** nel riquadro sinistro dell'interfaccia di amministrazione di M365.
>- [Centro sicurezza e conformità Office](https://sip.protection.office.com/homepage) - Il ruolo con autorizzazioni di lettura globali non può leggere i log di controllo di Centro sicurezza e conformità, eseguire la ricerca di contenuto o visualizzare Secure Store.
>- [Interfaccia di amministrazione di Teams](https://admin.teams.microsoft.com) - Il ruolo con autorizzazioni di lettura globali non può leggere **Teams Lifecycle**, **Teams Reporting & Call Analytics**, **Teams IP Phone Device Management** e **Teams App Catalog**.
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) non supporta il ruolo con autorizzazioni di lettura globali.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) - Il ruolo con autorizzazioni di lettura globali è supportato solo per la [creazione di report centralizzata](/azure/information-protection/reports-aip) e quando l'organizzazione Azure AD non si trova nella [piattaforma di etichettatura unificata](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Queste funzionalità sono attualmente in fase di sviluppo.
>

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/applications/applicationProxy/read | Leggi tutte le proprietà del proxy di applicazione |
> | microsoft.directory/applications/synchronization/standard/read | Leggere le impostazioni di provisioning associate all'oggetto applicazione |
> | microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà nei log di controllo, incluse le proprietà con privilegi |
> | Microsoft. directory/bitlockerKeys/chiave/lettura | Leggere i metadati e la chiave di BitLocker nei dispositivi |
> | Microsoft. directory/connettori/allProperties/lettura | Leggi tutte le proprietà dei connettori del proxy di applicazione |
> | Microsoft. directory/connectorGroups/allProperties/Read | Leggi tutte le proprietà dei gruppi di connettori del proxy di applicazione |
> | Microsoft. directory/entitlementManagement/allProperties/Read | Leggi tutte le proprietà in Azure AD gestione dei diritti |
> | Microsoft. directory/deviceManagementPolicies/standard/lettura | Leggere le proprietà standard nei criteri delle applicazioni per la gestione dei dispositivi |
> | Microsoft. directory/deviceRegistrationPolicy/standard/lettura | Leggere le proprietà standard nei criteri di registrazione del dispositivo |
> | microsoft.directory/groups/hiddenMembers/read | Leggere i membri nascosti di un gruppo |
> | microsoft.directory/policies/standard/read | Leggere le proprietà di base sui criteri |
> | microsoft.directory/policies/owners/read | Leggere i proprietari dei criteri |
> | Microsoft. directory/Policies/policyAppliedTo/Read | Lettura della proprietà Policies. policyAppliedTo |
> | Microsoft. directory/conditionalAccessPolicies/standard/lettura | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/owners/Read | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/policyAppliedTo/Read | Lettura della proprietà Policies. conditionalAccess |
> | microsoft.directory/provisioningLogs/allProperties/read | Lettura di tutte le proprietà dei log di provisioning. |
> | microsoft.directory/servicePrincipals/authentication/read | Leggere le proprietà di autenticazione sulle entità servizio |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Leggere le impostazioni di provisioning associate all'entità servizio |
> | microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà nei report di accesso, incluse le proprietà con privilegi |
> | microsoft.directory/users/strongAuthentication/read | Leggere la proprietà autenticazione avanzata per gli utenti |
> | microsoft.commerce.billing/allEntities/read | Leggi tutte le risorse di Office 365 fatturazione |
> | Microsoft. office365. Exchange/allEntities/standard/Read | Leggi tutte le risorse di Exchange Online |
> | microsoft.office365.messageCenter/messages/read | Leggere i messaggi nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365, esclusi i messaggi di sicurezza |
> | microsoft.office365.messageCenter/securityMessages/read | Leggere i messaggi di sicurezza nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.network/performance/allProperties/read | Leggere tutte le proprietà delle prestazioni di rete nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. office365. protectionCenter/allEntities/allProperties/Read | Leggere tutte le proprietà nei centri sicurezza e conformità |
> | microsoft.office365.securityComplianceCenter/allEntities/read | Leggere le proprietà standard in Microsoft 365 Centro sicurezza e conformità |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Leggere i report sull'utilizzo di Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="groups-administrator"></a>Amministratore di gruppi

Gli utenti con questo ruolo possono creare o gestire gruppi e le relative impostazioni, ad esempio i criteri di denominazione e scadenza. È importante comprendere che l'assegnazione di un utente a questo ruolo offre la possibilità di gestire tutti i gruppi nell'organizzazione per diversi carichi di lavoro, ad esempio Teams, SharePoint, Yammer, oltre a Outlook. Inoltre, l'utente sarà in grado di gestire le diverse impostazioni dei gruppi nei vari portali di amministrazione, come l'interfaccia di amministrazione di Microsoft e il portale di Azure, oltre a quelli specifici del carico di lavoro, come le interfacce di amministrazione di Teams e SharePoint.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. Directory/gruppi/assignLicense | Assegnare licenze prodotto a gruppi per le licenze basate sui gruppi |
> | microsoft.directory/groups/create | Creazione di gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/groups/delete | Elimina gruppi, escluso il gruppo assegnabile al ruolo |
> | microsoft.directory/groups/hiddenMembers/read | Leggere i membri nascosti di un gruppo |
> | Microsoft. Directory/gruppi/reprocessLicenseAssignment | Rielaborare le assegnazioni delle licenze per le licenze basate sui gruppi |
> | microsoft.directory/groups/restore | Ripristinare i gruppi eliminati |
> | microsoft.directory/groups/basic/update | Aggiornare le proprietà di base nei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. Directory/gruppi/Classificazione/aggiornamento | Aggiornare la proprietà di classificazione dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. directory/groups/dynamicMembershipRule/Update | Aggiornamento della regola di appartenenza dinamica dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. directory/groups/groupType/Update | Aggiornare la proprietà groupType per un gruppo |
> | microsoft.directory/groups/members/update | Aggiornare i membri dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. directory/groups/onPremWriteBack/Update | Aggiornare i gruppi di Azure AD da scrivere nuovamente in locale |
> | microsoft.directory/groups/owners/update | Aggiornare i proprietari dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/groups/settings/update | Aggiornare le impostazioni dei gruppi |
> | Microsoft. Directory/gruppi/visibilità/aggiornamento | Aggiornare la proprietà Visibility dei gruppi |
> | Microsoft. directory/entità servizio/managePermissionGrantsForGroup. Microsoft-all-Application-Permissions | Concedere a un servizio entità l'accesso diretto ai dati di un gruppo |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="guest-inviter"></a>Mittente dell'invito guest

gli utenti con questo ruolo possono gestire gli inviti per gli utenti guest di Azure Active Directory B2B quando il valore dell'impostazione utente **I membri possono invitare** è No. Altre informazioni sulla collaborazione B2B in [Informazioni su Collaborazione B2B di Azure Active Directory](../external-identities/what-is-b2b.md). Il ruolo non include altre autorizzazioni.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | Invitare gli utenti guest |
> | Microsoft. Directory/utenti/standard/lettura | Leggere le proprietà di base per gli utenti |
> | microsoft.directory/users/appRoleAssignments/read | Legge le assegnazioni di ruolo dell'applicazione degli utenti |
> | microsoft.directory/users/directReports/read | Leggi i report diretti per gli utenti |
> | microsoft.directory/users/manager/read | Gestione utenti di lettura |
> | microsoft.directory/users/memberOf/read | Lettura delle appartenenze a gruppi di utenti |
> | Microsoft. Directory/Users/oAuth2PermissionGrants/Read | Lettura delle concessioni di autorizzazioni delegate per gli utenti |
> | microsoft.directory/users/ownedDevices/read | Lettura dei dispositivi di proprietà degli utenti |
> | microsoft.directory/users/ownedObjects/read | Leggere oggetti di proprietà degli utenti |
> | microsoft.directory/users/registeredDevices/read | Lettura dei dispositivi registrati degli utenti |

## <a name="helpdesk-administrator"></a>Amministratore del supporto tecnico

gli utenti con questo ruolo possono modificare le password, invalidare i token di aggiornamento, gestire le richieste di servizio e monitorare l'integrità dei servizi. Invalidando un token di aggiornamento si impone all'utente di eseguire di nuovo l'accesso. Il fatto che un amministratore del supporto tecnico possa reimpostare la password di un utente e invalidare i token di aggiornamento dipende dal ruolo assegnato dall'utente. Per un elenco dei ruoli che un amministratore del supporto tecnico può reimpostare le password per e invalidare i token di aggiornamento, vedere [autorizzazioni di reimpostazione della password](#password-reset-permissions).

> [!IMPORTANT]
> gli utenti con questo ruolo possono modificare le password di utenti che possono accedere a dati sensibili, informazioni riservate o configurazioni critiche sia dall'interno che dall'esterno di Azure Active Directory. Modificare la password di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
>
>- Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero disporre di autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli utenti con ruolo di Amministratore supporto tecnico. Ciò significa che un Amministratore supporto tecnico potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
>- Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure.
>- I proprietari del gruppo di sicurezza e del gruppo di Microsoft 365, che possono gestire l'appartenenza ai gruppi. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
>- Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
>- Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

La delega delle autorizzazioni amministrative su subset di utenti e l'applicazione di criteri a un subset di utenti è possibile con le [unità amministrative](administrative-units.md).

Questo ruolo era precedentemente chiamato "Amministratore password" nel [portale di Azure](https://portal.azure.com/). Il nome "Amministratore supporto tecnico" in Azure AD ora corrisponde al nome in Azure AD PowerShell e nell'API Microsoft Graph.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/bitlockerKeys/chiave/lettura | Leggere i metadati e la chiave di BitLocker nei dispositivi |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forza la disconnessione invalidando i token di aggiornamento utente |
> | microsoft.directory/users/password/update | Reimposta le password per tutti gli utenti |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="hybrid-identity-administrator"></a>Amministratore delle identità ibride

Gli utenti con questo ruolo possono creare, gestire e distribuire il programma di configurazione del provisioning da AD a Azure AD usando il provisioning cloud, nonché gestire le impostazioni di Federazione. Gli utenti possono inoltre risolvere i problemi e monitorare i log utilizzando questo ruolo.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/applications/create | Creare tutti i tipi di applicazioni |
> | microsoft.directory/applications/delete | Elimina tutti i tipi di applicazioni |
> | Microsoft. directory/Applications/appRoles/Update | Aggiornare la proprietà appRoles in tutti i tipi di applicazioni |
> | microsoft.directory/applications/audience/update | Aggiornare la proprietà audience per le applicazioni |
> | microsoft.directory/applications/authentication/update | Aggiornare l'autenticazione su tutti i tipi di applicazioni |
> | microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni |
> | microsoft.directory/applications/credentials/update | Aggiornare le credenziali dell'applicazione |
> | microsoft.directory/applications/owners/update | Aggiornare i proprietari delle applicazioni |
> | microsoft.directory/applications/permissions/update | Aggiornare le autorizzazioni esposte e le autorizzazioni necessarie per tutti i tipi di applicazioni |
> | microsoft.directory/applications/policies/update | Aggiornare i criteri delle applicazioni |
> | microsoft.directory/applications/synchronization/standard/read | Leggere le impostazioni di provisioning associate all'oggetto applicazione |
> | microsoft.directory/applicationTemplates/instantiate | Creare un'istanza di applicazioni della raccolta da modelli di applicazione |
> | microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà nei log di controllo, incluse le proprietà con privilegi |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | Leggere e configurare tutte le proprietà del servizio di provisioning cloud di Azure AD. |
> | Microsoft. Directory/domini/allProperties/lettura | Leggi tutte le proprietà dei domini |
> | Microsoft. Directory/domini/Federazione/aggiornamento | Aggiorna la proprietà di Federazione dei domini |
> | microsoft.directory/organization/dirSync/update | Aggiornare la proprietà di sincronizzazione della directory dell'organizzazione |
> | microsoft.directory/provisioningLogs/allProperties/read | Lettura di tutte le proprietà dei log di provisioning. |
> | microsoft.directory/servicePrincipals/create | Creare entità servizio |
> | microsoft.directory/servicePrincipals/delete | Elimina entità servizio |
> | microsoft.directory/servicePrincipals/disable | Disabilitare le entità servizio |
> | microsoft.directory/servicePrincipals/enable | Abilitare le entità servizio |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gestire segreti e credenziali per il provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Avviare, riavviare e sospendere i processi sincronizzazione del provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Creare e gestire lo schema e i processi sincronizzazione del provisioning delle applicazioni |
> | microsoft.directory/servicePrincipals/audience/update | Aggiornare le proprietà del gruppo di destinatari nelle entità servizio |
> | microsoft.directory/servicePrincipals/authentication/update | Aggiornare le proprietà di autenticazione nelle entità servizio |
> | microsoft.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base per le entità servizio |
> | microsoft.directory/servicePrincipals/credentials/update | Aggiornare le credenziali delle entità servizio |
> | microsoft.directory/servicePrincipals/owners/update | Aggiornare i proprietari delle entità servizio |
> | microsoft.directory/servicePrincipals/permissions/update | Aggiornare le autorizzazioni delle entità servizio |
> | microsoft.directory/servicePrincipals/policies/update | Aggiornare i criteri delle entità servizio |
> | microsoft.directory/servicePrincipals/tag/update | Aggiornare la proprietà Tag per le entità servizio |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Leggere le impostazioni di provisioning associate all'entità servizio |
> | microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà nei report di accesso, incluse le proprietà con privilegi |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.messageCenter/messages/read | Leggere i messaggi nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365, esclusi i messaggi di sicurezza |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="insights-administrator"></a>Amministratore di Insights

Gli utenti con questo ruolo possono accedere al set completo di funzionalità amministrative nell' [applicazione M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Questo ruolo è in grado di leggere le informazioni della directory, monitorare l'integrità dei servizi, i ticket di supporto file e accedere agli aspetti delle impostazioni di amministrazione di Insights.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | Microsoft. Insights/allEntities/allTasks | Gestire tutti gli aspetti dell'app Insights |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="insights-business-leader"></a>Leader aziendale di Insights

Gli utenti con questo ruolo possono accedere a un set di dashboard e informazioni dettagliate tramite l' [applicazione M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Questo include l'accesso completo a tutti i dashboard e le funzionalità di esplorazione dei dati e delle informazioni fornite. Gli utenti con questo ruolo non hanno accesso alle impostazioni di configurazione del prodotto, che è responsabilità del ruolo di amministratore di Insights.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. Insights/report/lettura | Visualizzare report e dashboard nell'App Insights |
> | Microsoft. Insights/programmi/aggiornamento | Distribuire e gestire i programmi nell'App Insights |

## <a name="intune-administrator"></a>Amministratore di Intune

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Intune Online, quando il servizio è presente. Inoltre questo ruolo implica la possibilità di gestire utenti e dispositivi per associare i criteri, nonché creare e gestire gruppi. Altre informazioni sono disponibili in [Controllo degli accessi in base al ruolo (RBAC) con Microsoft Intune](/intune/role-based-access-control).

Questo ruolo può creare e gestire tutti i gruppi di sicurezza. Tuttavia, l'amministratore di Intune non ha diritti di amministratore per i gruppi di Office. Questo significa che l'amministratore non può aggiornare i proprietari o le appartenenze di tutti i gruppi di Office nell'organizzazione. Tuttavia, è in grado di gestire il gruppo di Office creato come parte dei propri privilegi di utente finale. Pertanto, qualsiasi gruppo di Office (non gruppo di sicurezza) creato dall'utente deve essere conteggiato ai fini della quota di 250.

> [!NOTE]
> Nell'API Microsoft Graph e Azure AD PowerShell, questo ruolo è identificato come "amministratore del servizio Intune". È l'"Amministratore di Intune" nel [portale di Azure](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/bitlockerKeys/chiave/lettura | Leggere i metadati e la chiave di BitLocker nei dispositivi |
> | microsoft.directory/contacts/create | Crea contatti |
> | microsoft.directory/contacts/delete | Elimina contatti |
> | microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base nei contatti |
> | microsoft.directory/devices/create | Creazione di dispositivi (registrazione in Azure AD) |
> | microsoft.directory/devices/delete | Elimina i dispositivi da Azure AD |
> | microsoft.directory/devices/disable | Disabilitare i dispositivi in Azure AD |
> | microsoft.directory/devices/enable | Abilitare i dispositivi in Azure AD |
> | microsoft.directory/devices/basic/update | Aggiornare le proprietà di base nei dispositivi |
> | Microsoft. directory/devices/extensionAttributes/Update | Aggiornare tutti i valori per la proprietà Devices. extensionAttributes |
> | microsoft.directory/devices/registeredOwners/update | Aggiornare i proprietari registrati dei dispositivi |
> | microsoft.directory/devices/registeredUsers/update | Aggiornare gli utenti registrati dei dispositivi |
> | Microsoft. directory/deviceManagementPolicies/standard/lettura | Leggere le proprietà standard nei criteri delle applicazioni per la gestione dei dispositivi |
> | Microsoft. directory/deviceRegistrationPolicy/standard/lettura | Leggere le proprietà standard nei criteri di registrazione del dispositivo |
> | microsoft.directory/groups/hiddenMembers/read | Leggere i membri nascosti di un gruppo |
> | Microsoft. directory/groups. Security/create | Creare gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/delete | Eliminare i gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/Basic/Update | Aggiornare le proprietà di base nei gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/Classification/Update | Aggiornare la proprietà di classificazione dei gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/dynamicMembershipRule/Update | Aggiornare la proprietà dynamicMembershipRule dei gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/members/Update | Aggiornare i membri dei gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/owners/Update | Aggiornare i proprietari dei gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/Visibility/Update | Aggiornare la proprietà Visibility dei gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | microsoft.directory/users/basic/update | Aggiornare le proprietà di base per gli utenti |
> | microsoft.directory/users/manager/update | Gestione aggiornamenti per gli utenti |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Microsoft Intune |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="kaizala-administrator"></a>Amministratore di Kaizala

Gli utenti con questo ruolo hanno autorizzazioni globali per gestire le impostazioni in Microsoft Kaizala, quando è presente il servizio, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Inoltre, l'utente può accedere ai report relativi all'adozione e all'utilizzo di Kaizala da parte dei membri dell'organizzazione e ai report aziendali generati usando le azioni Kaizala.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="knowledge-administrator"></a>Amministratore della Knowledge base

Gli utenti con questo ruolo hanno accesso completo a tutte le impostazioni di Knowledge base, apprendimento e funzionalità intelligenti nell'interfaccia di amministrazione di Microsoft 365. Hanno una conoscenza generale della suite di prodotti, dei dettagli delle licenze e hanno la responsabilità di controllare l'accesso. L'amministratore delle informazioni può creare e gestire contenuto, ad esempio argomenti, acronimi e risorse di formazione. Questi utenti possono inoltre creare centri di contenuto, monitorare l'integrità dei servizi e creare richieste di servizio.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/groups. Security/create | Creare gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/createAsOwner | Creare gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo e l'autore viene aggiunto come primo proprietario |
> | Microsoft. directory/groups. Security/delete | Eliminare i gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/Basic/Update | Aggiornare le proprietà di base nei gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/members/Update | Aggiornare i membri dei gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Security/owners/Update | Aggiornare i proprietari dei gruppi di sicurezza con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. office365. Knowledge/contentUnderstanding/allProperties/allTasks | Leggere e aggiornare tutte le proprietà di comprensione del contenuto nel Microsoft 365 interfaccia di amministrazione |
> | Microsoft. office365. Knowledge/knowledgeNetwork/allProperties/allTasks | Leggere e aggiornare tutte le proprietà di Knowledge base in Microsoft 365 interfaccia di amministrazione |
> | Microsoft. office365. protectionCenter/sensitivityLabels/allProperties/Read | Leggere le etichette di riservatezza nei centri sicurezza e conformità |
> | Microsoft. office365. sharePoint/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="license-administrator"></a>Amministratore licenze

gli utenti in questo ruolo possono aggiungere, rimuovere e aggiornare le assegnazioni di licenze a utenti, gruppi (usando licenze basate su gruppi) e gestire i percorsi di utilizzo per gli utenti. Il ruolo non garantisce la possibilità di acquistare o gestire sottoscrizioni, creare o gestire gruppi o creare o gestire utenti al di fuori del percorso di utilizzo. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. Directory/gruppi/assignLicense | Assegnare licenze prodotto a gruppi per le licenze basate sui gruppi |
> | Microsoft. Directory/gruppi/reprocessLicenseAssignment | Rielaborare le assegnazioni delle licenze per le licenze basate sui gruppi |
> | microsoft.directory/users/assignLicense | Gestire le licenze utente |
> | Microsoft. Directory/Users/reprocessLicenseAssignment | Rielaborare le assegnazioni delle licenze per gli utenti |
> | microsoft.directory/users/usageLocation/update | Aggiornare la località di utilizzo degli utenti |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="message-center-privacy-reader"></a>Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi

Gli utenti con questo ruolo possono monitorare tutte le notifiche nel Centro messaggi, inclusi i messaggi sulla privacy dei dati. Gli utenti con il ruolo con autorizzazioni di lettura per la privacy del Centro messaggi ricevono notifiche tramite posta elettronica, incluse quelle relative alla privacy dei dati, e possono annullare la sottoscrizione usando le preferenze del Centro messaggi. Solo l'amministratore globale e il ruolo con autorizzazioni di lettura per la privacy del Centro messaggi possono leggere i messaggi sulla privacy dei dati. Questo ruolo include anche la possibilità di visualizzare gruppi, domini e sottoscrizioni. Questo ruolo non ha le autorizzazioni per visualizzare, creare o gestire richieste di servizio.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Leggere i messaggi nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365, esclusi i messaggi di sicurezza |
> | microsoft.office365.messageCenter/securityMessages/read | Leggere i messaggi di sicurezza nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="message-center-reader"></a>Ruolo con autorizzazioni di lettura per il Centro messaggi

Gli utenti con questo ruolo possono monitorare le notifiche e gli aggiornamenti di integrità consultivi in [Message Center](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) per la propria organizzazione in servizi configurati, ad esempio Exchange, Intune e Microsoft teams. I lettori del centro messaggi ricevono i digest settimanali dei post, degli aggiornamenti e possono condividere i post del centro messaggi in Microsoft 365. In Azure AD, gli utenti assegnati a questo ruolo avranno solo l'accesso di sola lettura ai servizi di Azure AD, ad esempio utenti e gruppi. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Leggere i messaggi nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365, esclusi i messaggi di sicurezza |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="modern-commerce-user"></a>Utente Commerce moderno

Non usare. Questo ruolo viene assegnato automaticamente da Commerce e non è progettato o supportato per altri usi. Vedere i dettagli di seguito.

Il ruolo utente commerciale moderno concede a determinati utenti le autorizzazioni per accedere Microsoft 365 interfaccia di amministrazione e visualizzare le voci di spostamento a sinistra per **Home**, **fatturazione** e **supporto**. I contenuti disponibili in queste aree sono controllati da [ruoli specifici per attività commerciali](../../cost-management-billing/manage/understand-mca-roles.md) assegnati agli utenti per gestire i prodotti acquistati per se stessi o per la propria organizzazione. Questo potrebbe includere attività quali il pagamento di fatture o l'accesso agli account di fatturazione e ai profili di fatturazione.

Gli utenti con il ruolo di utente Commerce moderno hanno in genere autorizzazioni amministrative in altri sistemi di acquisto Microsoft, ma non hanno i ruoli di amministratore globale o amministratore fatturazione usati per accedere all'interfaccia di amministrazione.

**Quando viene assegnato il ruolo utente commerciale moderno?**

* **Acquisto self-service nell'interfaccia di amministrazione di Microsoft 365** - L'acquisto self-service offre agli utenti la possibilità di provare nuovi prodotti acquistandoli o effettuando l'iscrizione autonomamente. Questi prodotti vengono gestiti nell'interfaccia di amministrazione. Agli utenti che effettuano un acquisto self-service viene assegnato un ruolo nel sistema commerciale e il ruolo utente commerciale moderno per poter gestire gli acquisti nell'interfaccia di amministrazione. Gli amministratori possono bloccare gli acquisti self-service (per Power BI, Power Apps, Power Automate) tramite [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell). Per altre informazioni, vedere [Domande frequenti sugli acquisti self-service](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).
* **Acquisti da Microsoft Commercial Marketplace** : Analogamente all'acquisto self-service, quando un utente acquista un prodotto o un servizio da Microsoft AppSource o Azure Marketplace, viene assegnato il ruolo utente Commerce moderno se non ha il ruolo amministratore globale o amministratore fatturazione. In alcuni casi, è possibile che gli utenti non possano effettuare questi acquisti. Per altre informazioni, vedere [Marketplace commerciale Microsoft](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase).
* **Proposte di Microsoft** -una proposta è un'offerta formale da Microsoft per la propria organizzazione per l'acquisto di prodotti e servizi Microsoft. Quando la persona che accetta la proposta non ha un ruolo amministratore globale o amministratore fatturazione in Azure AD, viene assegnato un ruolo specifico del commercio per completare la proposta e il ruolo utente commerciale moderno per accedere al centro di amministrazione. Quando accedono all'interfaccia di amministrazione, possono usare solo le funzionalità autorizzate dal proprio ruolo specifico per attività commerciali.
* **Ruoli specifici per attività commerciali** - Ad alcuni utenti vengono assegnati ruoli specifici per attività commerciali. Se un utente non è un amministratore globale o di fatturazione, ottiene il ruolo di utente commerciale moderno per poter accedere al centro di amministrazione.

Se il ruolo utente Commerce moderno non è assegnato a un utente, perderà l'accesso al centro di amministrazione di Microsoft 365. Se era responsabile della gestione di prodotti, per se stesso o per l'organizzazione, non sarà più in grado di occuparsene. I compiti di gestione potrebbero includere l'assegnazione di licenze, la modifica dei metodi di pagamento, il pagamento di fatture o altre attività per la gestione delle sottoscrizioni.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | Legge la proprietà partner di Microsoft 365 fatturazione |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | Gestire tutti gli aspetti di Volume Licensing Service Center |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="network-administrator"></a>Amministratore di rete

Gli utenti con questo ruolo possono esaminare le raccomandazioni relative all'architettura perimetrale di rete di Microsoft basate sui dati di telemetria di rete dalle posizioni degli utenti. Le prestazioni di rete per Microsoft 365 si basano su un'attenta architettura perimetrale della rete del cliente aziendale, che in genere è specifica del percorso utente. Questo ruolo consente di modificare le posizioni utente individuate e la configurazione dei parametri di rete per tali percorsi per semplificare le misurazioni di telemetria migliorate e le indicazioni di progettazione

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | Gestire tutti gli aspetti dei percorsi di rete |
> | microsoft.office365.network/performance/allProperties/read | Leggere tutte le proprietà delle prestazioni di rete nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="office-apps-administrator"></a>Amministratore delle app di Office

Gli utenti con questo ruolo possono gestire le impostazioni cloud di Microsoft 365 app. Sono incluse la gestione dei criteri cloud, la gestione dei download self-service e la possibilità di visualizzare report correlati alle app di Office. Questo ruolo concede anche la possibilità di gestire i ticket di supporto e di monitorare l'integrità dei servizi nell'interfaccia di amministrazione principale. Gli utenti assegnati a questo ruolo possono anche gestire la comunicazione delle nuove funzionalità nelle app di Office.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.messageCenter/messages/read | Leggere i messaggi nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365, esclusi i messaggi di sicurezza |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.userCommunication/allEntities/allTasks | Leggi e aggiorna la visibilità dei nuovi messaggi |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="partner-tier1-support"></a>Supporto partner - Livello 1

Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

> [!IMPORTANT]
> Questo ruolo può reimpostare le password e invalidare i token di aggiornamento solo per gli amministratori non. Questo ruolo non deve essere usato perché è deprecato e non verrà più restituito nell'API.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/Applications/appRoles/Update | Aggiornare la proprietà appRoles in tutti i tipi di applicazioni |
> | microsoft.directory/applications/audience/update | Aggiornare la proprietà audience per le applicazioni |
> | microsoft.directory/applications/authentication/update | Aggiornare l'autenticazione su tutti i tipi di applicazioni |
> | microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni |
> | microsoft.directory/applications/credentials/update | Aggiornare le credenziali dell'applicazione |
> | microsoft.directory/applications/owners/update | Aggiornare i proprietari delle applicazioni |
> | microsoft.directory/applications/permissions/update | Aggiornare le autorizzazioni esposte e le autorizzazioni necessarie per tutti i tipi di applicazioni |
> | microsoft.directory/applications/policies/update | Aggiornare i criteri delle applicazioni |
> | microsoft.directory/contacts/create | Crea contatti |
> | microsoft.directory/contacts/delete | Elimina contatti |
> | microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base nei contatti |
> | microsoft.directory/groups/create | Creazione di gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/groups/delete | Elimina gruppi, escluso il gruppo assegnabile al ruolo |
> | microsoft.directory/groups/restore | Ripristinare i gruppi eliminati |
> | microsoft.directory/groups/members/update | Aggiornare i membri dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/groups/owners/update | Aggiornare i proprietari dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare le concessioni di autorizzazione OAuth 2,0, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare le assegnazioni di ruolo dell'entità servizio |
> | microsoft.directory/users/assignLicense | Gestire le licenze utente |
> | microsoft.directory/users/create | Aggiungere utenti |
> | microsoft.directory/users/delete | Eliminare gli utenti |
> | Microsoft. Directory/utenti/Disabilita | Disabilita utenti |
> | Microsoft. Directory/utenti/Abilita | Abilita utenti |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forza la disconnessione invalidando i token di aggiornamento utente |
> | microsoft.directory/users/restore | Ripristinare gli utenti eliminati |
> | microsoft.directory/users/basic/update | Aggiornare le proprietà di base per gli utenti |
> | microsoft.directory/users/manager/update | Gestione aggiornamenti per gli utenti |
> | microsoft.directory/users/password/update | Reimposta le password per tutti gli utenti |
> | microsoft.directory/users/userPrincipalName/update | Aggiornare il nome dell'entità utente degli utenti |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="partner-tier2-support"></a>Supporto partner - Livello 2

Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

> [!IMPORTANT]
> Questo ruolo può reimpostare le password e invalidare i token di aggiornamento per tutti gli amministratori e non amministratori (inclusi gli amministratori globali). Questo ruolo non deve essere usato perché è deprecato e non verrà più restituito nell'API.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/Applications/appRoles/Update | Aggiornare la proprietà appRoles in tutti i tipi di applicazioni |
> | microsoft.directory/applications/audience/update | Aggiornare la proprietà audience per le applicazioni |
> | microsoft.directory/applications/authentication/update | Aggiornare l'autenticazione su tutti i tipi di applicazioni |
> | microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni |
> | microsoft.directory/applications/credentials/update | Aggiornare le credenziali dell'applicazione |
> | microsoft.directory/applications/owners/update | Aggiornare i proprietari delle applicazioni |
> | microsoft.directory/applications/permissions/update | Aggiornare le autorizzazioni esposte e le autorizzazioni necessarie per tutti i tipi di applicazioni |
> | microsoft.directory/applications/policies/update | Aggiornare i criteri delle applicazioni |
> | microsoft.directory/contacts/create | Crea contatti |
> | microsoft.directory/contacts/delete | Elimina contatti |
> | microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base nei contatti |
> | Microsoft. Directory/Domains/Basic/allTasks | Creare ed eliminare domini, leggere e aggiornare le proprietà standard |
> | microsoft.directory/groups/create | Creazione di gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/groups/delete | Elimina gruppi, escluso il gruppo assegnabile al ruolo |
> | microsoft.directory/groups/restore | Ripristinare i gruppi eliminati |
> | microsoft.directory/groups/members/update | Aggiornare i membri dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/groups/owners/update | Aggiornare i proprietari dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare le concessioni di autorizzazione OAuth 2,0, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/organization/basic/update | Aggiornare le proprietà di base nell'organizzazione |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Creare ed eliminare assegnazioni di ruolo e leggere e aggiornare tutte le proprietà di assegnazione di ruolo |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Creare ed eliminare definizioni di ruolo e leggere e aggiornare tutte le proprietà |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Creare ed eliminare appartenenze, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare le assegnazioni di ruolo dell'entità servizio |
> | Microsoft. directory/subscribedSkus/standard/lettura | Leggere le proprietà di base nelle sottoscrizioni |
> | microsoft.directory/users/assignLicense | Gestire le licenze utente |
> | microsoft.directory/users/create | Aggiungere utenti |
> | microsoft.directory/users/delete | Eliminare gli utenti |
> | Microsoft. Directory/utenti/Disabilita | Disabilita utenti |
> | Microsoft. Directory/utenti/Abilita | Abilita utenti |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forza la disconnessione invalidando i token di aggiornamento utente |
> | microsoft.directory/users/restore | Ripristinare gli utenti eliminati |
> | microsoft.directory/users/basic/update | Aggiornare le proprietà di base per gli utenti |
> | microsoft.directory/users/manager/update | Gestione aggiornamenti per gli utenti |
> | microsoft.directory/users/password/update | Reimposta le password per tutti gli utenti |
> | microsoft.directory/users/userPrincipalName/update | Aggiornare il nome dell'entità utente degli utenti |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="password-administrator"></a>Amministratore password

Gli utenti con questo ruolo hanno la possibilità di gestire le password in modo limitato. Questo ruolo non concede la possibilità di gestire le richieste di servizio o di monitorare l'integrità dei servizi. La possibilità di reimpostare la password di un utente dipende dal ruolo assegnato dall'utente. Per un elenco dei ruoli a cui un amministratore delle password può reimpostare le password, vedere [autorizzazioni di reimpostazione](#password-reset-permissions)della password.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/users/password/update | Reimposta le password per tutti gli utenti |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="power-bi-administrator"></a>Amministratore di Power BI

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Power BI, quando è presente il servizio, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Per altre informazioni, vedere [Informazioni sul ruolo di amministratore di Power BI](/power-bi/service-admin-role).

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Power BI". È l'"Amministratore di Power BI" nel [portale di Azure](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Gestire tutti gli aspetti di Power BI |

## <a name="power-platform-administrator"></a>Amministratore di Power Platform

Gli utenti con questo ruolo possono creare e gestire tutti gli aspetti degli ambienti, PowerApps, Flows e i criteri di prevenzione della perdita dei dati. Possono inoltre gestire i ticket di supporto e monitorare l'integrità del servizio.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.dynamics365/allEntities/allTasks | Gestire tutti gli aspetti di Dynamics 365 |
> | microsoft.flow/allEntities/allTasks | Gestisci tutti gli aspetti di Microsoft Power automatici |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.powerApps/allEntities/allTasks | Gestisci tutti gli aspetti di Power Apps |

## <a name="printer-administrator"></a>Amministratore stampante

Gli utenti con questo ruolo possono registrare le stampanti e gestire tutti gli aspetti di tutte le configurazioni della stampante nella soluzione Stampa universale Microsoft, incluse le impostazioni del connettore Stampa universale. Possono concedere il consenso a tutte le richieste di autorizzazione di stampa delegate. Gli amministratori stampante possono anche accedere ai report di stampa.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Creare ed eliminare stampanti e connettori, leggere e aggiornare tutte le proprietà in Microsoft Print |

## <a name="printer-technician"></a>Tecnico della stampante

Gli utenti con questo ruolo possono registrare le stampanti e gestire lo stato della stampante nella soluzione Stampa universale Microsoft. Possono anche leggere tutte le informazioni sul connettore. Due attività chiave che un tecnico della stampante non può eseguire sono impostare le autorizzazioni utente per le stampanti e condividere stampanti.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | Leggi tutte le proprietà dei connettori in Microsoft Print |
> | microsoft.azure.print/printers/allProperties/read | Leggi tutte le proprietà delle stampanti in Microsoft Print |
> | microsoft.azure.print/printers/register | Registrare le stampanti in Microsoft Print |
> | microsoft.azure.print/printers/unregister | Annulla la registrazione delle stampanti in Microsoft Print |
> | microsoft.azure.print/printers/basic/update | Aggiornare le proprietà di base delle stampanti in Microsoft Print |

## <a name="privileged-authentication-administrator"></a>Amministratore autenticazione con privilegi

Gli utenti con questo ruolo possono impostare o reimpostare qualsiasi metodo di autenticazione (incluse le password) per tutti gli utenti, inclusi gli amministratori globali. Gli amministratori dell'autenticazione con privilegi possono forzare gli utenti a eseguire di nuovo la registrazione per le credenziali esistenti diverse dalle password (ad esempio MFA, FIDO) e revocare la scelta di ricordare l'autenticazione a più fattori sul dispositivo, richiedendo l'autenticazione MFA al successivo accesso per tutti gli utenti.

Il ruolo [amministratore autenticazione](#authentication-administrator) dispone dell'autorizzazione per forzare la ripetizione della registrazione e l'autenticazione a più fattori per gli utenti e gli utenti standard con alcuni ruoli di amministratore.

Il ruolo di [amministratore dei criteri di autenticazione](#authentication-policy-administrator) dispone delle autorizzazioni per impostare i criteri del metodo di autenticazione del tenant che determina quali metodi possono essere registrati e usati da ogni utente.

| Ruolo | Gestire i metodi di autenticazione dell'utente | Gestire l'autenticazione a più fattori per utente | Gestire le impostazioni dell'autenticazione a più fattori | Gestisci criteri metodo di autenticazione | Gestire i criteri di protezione delle password |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Amministratore dell'autenticazione | Sì per alcuni utenti (vedere sopra) | Sì per alcuni utenti (vedere sopra) | No | No | No |
| Amministratore autenticazione con privilegi| Sì per tutti gli utenti | Sì per tutti gli utenti | No | No | No |
| Amministratore dei criteri di autenticazione | No | No | Sì | Sì | Sì |

> [!IMPORTANT]
> gli utenti con questo ruolo possono modificare le credenziali di utenti che possono avere accesso a dati sensibili, informazioni private o configurazioni critiche sia all'interno che all'esterno di Azure Active Directory. La modifica delle credenziali di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
>
>* Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli amministratori dell'autenticazione. Attraverso questo percorso, un amministratore di autenticazione può assumere l'identità del proprietario di un'applicazione e quindi assumere ulteriormente l'identità di un'applicazione con privilegi aggiornando le credenziali per l'applicazione.
>* Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
>* I proprietari del gruppo di sicurezza e del gruppo di Microsoft 365, che possono gestire l'appartenenza ai gruppi. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
>* Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
>* Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.


> [!IMPORTANT]
> Questo ruolo non è attualmente in grado di gestire l'autenticazione a più fattori per utente nel portale di gestione dell'autenticazione a più fattori legacy. Le stesse funzioni possono essere eseguite usando il modulo di PowerShell [set-MsolUser](https://docs.microsoft.com/powershell/module/msonline/set-msoluser) cmdlet Azure ad.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forza la disconnessione invalidando i token di aggiornamento utente |
> | microsoft.directory/users/strongAuthentication/update | Aggiornare la proprietà autenticazione avanzata per gli utenti |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="privileged-role-administrator"></a>Amministratore dei ruoli con privilegi

gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure Active Directory e in Azure AD Privileged Identity Management. Possono creare e gestire gruppi che possono essere assegnati ai ruoli Azure AD. Inoltre, questo ruolo consente la gestione di tutti gli aspetti di Privileged Identity Management e delle unità amministrative.

> [!IMPORTANT]
> Questo ruolo concede la possibilità di gestire le assegnazioni per tutti i ruoli di Azure AD, incluso il ruolo di Amministratore globale. Non prevede altre capacità con privilegi in Azure Active Directory, ad esempio la creazione o l'aggiornamento degli utenti. Tuttavia, gli utenti assegnati a questo ruolo possono concedere a se stessi o ad altri utenti privilegi aggiuntivi, perché possono assegnare ruoli aggiuntivi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Creare e gestire le unità amministrative inclusi i membri |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | Creare ed eliminare appRoleAssignments, leggere e aggiornare tutte le proprietà |
> | Microsoft. directory/authorizationPolicy/allProperties/allTasks | Gestire tutti gli aspetti dei criteri di autorizzazione |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Creare ed eliminare ruoli della directory, leggere e aggiornare tutte le proprietà |
> | Microsoft. directory/groupsAssignableToRoles/crea | Creare gruppi assegnabili a un ruolo |
> | Microsoft. directory/groupsAssignableToRoles/Delete | Elimina gruppi assegnabili al ruolo |
> | Microsoft. directory/groupsAssignableToRoles/Restore | Ripristinare i gruppi assegnabili ai ruoli |
> | Microsoft. directory/groupsAssignableToRoles/allProperties/Update | Aggiornare i gruppi assegnabili al ruolo |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare le concessioni di autorizzazione OAuth 2,0, leggere e aggiornare tutte le proprietà |
> | Microsoft. directory/privilegedIdentityManagement/allProperties/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in Privileged Identity Management |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Creare ed eliminare assegnazioni di ruolo e leggere e aggiornare tutte le proprietà di assegnazione di ruolo |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Creare ed eliminare definizioni di ruolo e leggere e aggiornare tutte le proprietà |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Creare ed eliminare appartenenze, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare le assegnazioni di ruolo dell'entità servizio |
> | microsoft.directory/servicePrincipals/permissions/update | Aggiornare le autorizzazioni delle entità servizio |
> | Microsoft. directory/entità servizio/managePermissionGrantsForAll. Microsoft-Company-admin | Concedere il consenso per qualsiasi autorizzazione a qualsiasi applicazione |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="reports-reader"></a>Lettore di report

Gli utenti con questo ruolo possono visualizzare i dati dei report sull'utilizzo e il dashboard dei report nell'interfaccia di amministrazione di Microsoft 365 e in Adoption Content Pack in Power BI. Il ruolo consente anche l'accesso ai report e alle attività di accesso in Azure Active Directory e ai dati restituiti dall'API di creazione report di Microsoft Graph. Un utente assegnato al ruolo di lettore di report può accedere solo alle metriche rilevanti per utilizzo e adozione. Questi utenti non hanno le autorizzazioni di amministratore per configurare le impostazioni o accedere alle interfacce di amministrazione specifiche di prodotti come Exchange. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà nei log di controllo, incluse le proprietà con privilegi |
> | microsoft.directory/provisioningLogs/allProperties/read | Lettura di tutte le proprietà dei log di provisioning. |
> | microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà nei report di accesso, incluse le proprietà con privilegi |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.office365.network/performance/allProperties/read | Leggere tutte le proprietà delle prestazioni di rete nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Leggere i report sull'utilizzo di Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="search-administrator"></a>Amministratore della ricerca

Gli utenti con questo ruolo hanno accesso completo a tutte le funzionalità di gestione di Microsoft Search nell'interfaccia di amministrazione di Microsoft 365. Questi utenti possono anche visualizzare il centro messaggi, monitorare l'integrità del servizio e creare richieste di servizio.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Leggere i messaggi nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365, esclusi i messaggi di sicurezza |
> | Microsoft. office365. search/content/Manage | Creare ed eliminare contenuto, leggere e aggiornare tutte le proprietà in Microsoft Search |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="search-editor"></a>Editor della ricerca

Gli utenti con questo ruolo possono creare, gestire ed eliminare contenuti per Microsoft Search nell'interfaccia di amministrazione di Microsoft 365, inclusi segnalibri, domande e risposte e posizioni.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Leggere i messaggi nel centro messaggi nell'interfaccia di amministrazione di Microsoft 365, esclusi i messaggi di sicurezza |
> | Microsoft. office365. search/content/Manage | Creare ed eliminare contenuto, leggere e aggiornare tutte le proprietà in Microsoft Search |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="security-administrator"></a>Amministratore della protezione

Gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla sicurezza nel Centro sicurezza Microsoft 365, in Azure Active Directory Identity Protection, per l'autenticazione di Azure Active Directory, in Azure Information Protection e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [autorizzazioni nel centro sicurezza & conformità](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In ingresso | Operazione consentita
--- | ---
[Centro sicurezza Microsoft 365](https://protection.office.com) | Monitorare i criteri correlati alla sicurezza in tutti i servizi di Microsoft 365<br>Gestire gli avvisi e le minacce alla sicurezza<br>Visualizzazione di report
Centro di Identity Protection | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Inoltre, eseguire tutte le operazioni di Identity Protection Center, tranne la reimpostazione delle password
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>**Non è consentito** gestire le assegnazioni di ruolo o le impostazioni di Azure AD
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire i criteri di sicurezza<br>Visualizzare, analizzare e rispondere alle minacce alla sicurezza<br>Visualizzazione di report
Azure Advanced Threat Protection | Monitorare e rispondere alle attività sospette dal punto di vista della sicurezza
Windows Defender ATP ed EDR | Assegnare ruoli<br>Gestire i gruppi di computer<br>Configurare il rilevamento delle minacce agli endpoint e le funzionalità automatizzate di correzione<br>Visualizzare, analizzare e rispondere agli avvisi
[Intune](/intune/role-based-access-control) | Visualizzare le informazioni relative a utenti, dispositivi e applicazioni e i dati di registrazione e configurazione<br>Non è consentito apportare modifiche a Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Aggiungere amministratori, criteri e impostazioni, caricare i log ed eseguire azioni di governance
[Centro sicurezza di Azure](../../key-vault/managed-hsm/built-in-roles.md) | è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni
[Integrità del servizio Microsoft 365](/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi Microsoft 365
[Blocco intelligente](../authentication/howto-password-smart-lockout.md) | Definire la soglia e la durata dei blocchi quando si verificano eventi di accesso non riuscito.
[Password di protezione](../authentication/concept-password-ban-bad.md) | Configurare l'elenco delle password personalizzate escluse o la password di protezione locale.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/applications/policies/update | Aggiornare i criteri delle applicazioni |
> | microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà nei log di controllo, incluse le proprietà con privilegi |
> | Microsoft. directory/bitlockerKeys/chiave/lettura | Leggere i metadati e la chiave di BitLocker nei dispositivi |
> | Microsoft. directory/entitlementManagement/allProperties/Read | Leggi tutte le proprietà in Azure AD gestione dei diritti |
> | Microsoft. directory/identityProtection/allProperties/Read | Leggi tutte le risorse in Azure AD Identity Protection |
> | Microsoft. directory/identityProtection/allProperties/Update | Aggiornare tutte le risorse in Azure AD Identity Protection |
> | microsoft.directory/policies/create | Creare criteri in Azure AD |
> | microsoft.directory/policies/delete | Eliminare i criteri in Azure AD |
> | microsoft.directory/policies/basic/update | Aggiornare le proprietà di base nei criteri |
> | microsoft.directory/policies/owners/update | Aggiornare i proprietari dei criteri |
> | microsoft.directory/policies/tenantDefault/update | Aggiornare i criteri dell'organizzazione predefiniti |
> | Microsoft. directory/conditionalAccessPolicies/crea | Creare criteri di accesso condizionale |
> | Microsoft. directory/conditionalAccessPolicies/Delete | Eliminare i criteri di accesso condizionale |
> | Microsoft. directory/conditionalAccessPolicies/standard/lettura | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/owners/Read | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/policyAppliedTo/Read | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/Basic/Update | Aggiornare le proprietà di base per i criteri di accesso condizionale |
> | Microsoft. directory/conditionalAccessPolicies/owners/Update | Aggiornare la proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/tenantDefault/Update | Aggiornare la proprietà Policies. conditionalAccess |
> | Microsoft. directory/privilegedIdentityManagement/allProperties/Read | Leggi tutte le risorse in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lettura di tutte le proprietà dei log di provisioning. |
> | microsoft.directory/servicePrincipals/policies/update | Aggiornare i criteri delle entità servizio |
> | microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà nei report di accesso, incluse le proprietà con privilegi |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | Microsoft. office365. protectionCenter/allEntities/standard/Read | Leggere le proprietà standard di tutte le risorse nei centri sicurezza e conformità |
> | Microsoft. office365. protectionCenter/allEntities/Basic/Update | Aggiornare le proprietà di base di tutte le risorse nei centri sicurezza e conformità |
> | Microsoft. office365. protectionCenter/attackSimulator/payload/allProperties/allTasks | Creare e gestire i payload di attacco nel simulatore di attacco |
> | Microsoft. office365. protectionCenter/attackSimulator/Reports/allProperties/Read | Leggere i report sulla simulazione degli attacchi, le risposte e il training associato |
> | Microsoft. office365. protectionCenter/attackSimulator/Simulation/allProperties/allTasks | Creare e gestire modelli di simulazione di attacco nel simulatore di attacco |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="security-operator"></a>Operatore di sicurezza

Gli utenti con questo ruolo possono gestire gli avvisi e hanno accesso globale di sola lettura alle funzionalità correlate alla sicurezza, incluse tutte le informazioni disponibili nel Centro sicurezza Microsoft 365 e in Azure Active Directory, Identity Protection, Privileged Identity Management e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [autorizzazioni nel centro sicurezza & conformità](/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

In ingresso | Operazione consentita
--- | ---
[Centro sicurezza Microsoft 365](https://protection.office.com) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi per minacce alla sicurezza
Azure AD Identity Protection | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Inoltre, la possibilità di eseguire tutte le operazioni di Identity Protection Center ad eccezione della reimpostazione delle password e della configurazione dei messaggi di posta elettronica di avviso.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi per la sicurezza
Windows Defender ATP ed EDR | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi per la sicurezza
[Intune](/intune/role-based-access-control) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
[Cloud App Security](/cloud-app-security/manage-admins) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
[Integrità del servizio Microsoft 365](/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi Microsoft 365

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà nei log di controllo, incluse le proprietà con privilegi |
> | Microsoft. directory/cloudAppSecurity/allProperties/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in Microsoft Cloud App Security |
> | Microsoft. directory/identityProtection/allProperties/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in Azure AD Identity Protection |
> | Microsoft. directory/privilegedIdentityManagement/allProperties/Read | Leggi tutte le risorse in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lettura di tutte le proprietà dei log di provisioning. |
> | microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà nei report di accesso, incluse le proprietà con privilegi |
> | Microsoft. Azure. advancedThreatProtection/allEntities/allTasks | Gestire tutti gli aspetti di Azure Advanced Threat Protection |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | Microsoft. Intune/allEntities/Read | Leggi tutte le risorse in Microsoft Intune |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard nel centro sicurezza e conformità di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/allTasks | Gestire tutti gli aspetti di Microsoft Defender per l'endpoint |

## <a name="security-reader"></a>Ruolo con autorizzazioni di lettura per la sicurezza

gli utenti con questo ruolo hanno accesso globale di sola lettura alle funzionalità relative alla sicurezza, incluse tutte le informazioni nel Centro sicurezza Microsoft 365, in Azure Active Directory, Identity Protection e Privileged Identity Management, oltre alla possibilità di accedere in lettura ai log di controllo e ai report di accesso di Azure Active Directory, e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [autorizzazioni nel centro sicurezza & conformità](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In ingresso | Operazione consentita
--- | ---
[Centro sicurezza Microsoft 365](https://protection.office.com) | Visualizzare i criteri correlati alla sicurezza in tutti i servizi di Microsoft 365<br>Visualizzare gli avvisi e le minacce alla sicurezza<br>Visualizzazione di report
Centro di Identity Protection | Leggere tutte le informazioni sulle impostazioni e sui report di sicurezza per le funzionalità di sicurezza<br><ul><li>Filtro posta indesiderata<li>Crittografia<li>Prevenzione della perdita dei dati<li>Antimalware<li>Protezione avanzata dalle minacce<li>Anti-phishing<li>Regole del flusso di posta
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Ha accesso in sola lettura a tutte le informazioni presentate in Azure AD Privileged Identity Management: Criteri e report per le assegnazioni di ruolo e le verifiche della sicurezza di Azure AD.<br>**Non è possibile** iscriversi per Azure AD Privileged Identity Management o apportare modifiche. Nel portale di Privileged Identity Management o tramite PowerShell, un utente con questo ruolo può attivare ruoli aggiuntivi, ad esempio amministratore globale o amministratore del ruolo con privilegi, se l'utente è idoneo.
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visualizzare i criteri di sicurezza<br>Visualizzare e analizzare le minacce alla sicurezza<br>Visualizzazione di report
Windows Defender ATP ed EDR | Visualizzare e analizzare gli avvisi. Quando si attiva il controllo degli accessi in base al ruolo in Windows Defender ATP, gli utenti con autorizzazioni di sola lettura, ad esempio il ruolo con autorizzazioni di lettura per la sicurezza di Azure AD, perdono l'accesso fino a quando non vengono assegnati a un ruolo di Windows Defender ATP.
[Intune](/intune/role-based-access-control) | Visualizzare le informazioni relative a utenti, dispositivi e applicazioni e i dati di registrazione e configurazione. Non è consentito apportare modifiche a Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi
[Centro sicurezza di Azure](../../key-vault/managed-hsm/built-in-roles.md) | è possibile visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non è possibile apportare modifiche
[Integrità del servizio Microsoft 365](/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi Microsoft 365

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà nei log di controllo, incluse le proprietà con privilegi |
> | Microsoft. directory/bitlockerKeys/chiave/lettura | Leggere i metadati e la chiave di BitLocker nei dispositivi |
> | Microsoft. directory/entitlementManagement/allProperties/Read | Leggi tutte le proprietà in Azure AD gestione dei diritti |
> | Microsoft. directory/identityProtection/allProperties/Read | Leggi tutte le risorse in Azure AD Identity Protection |
> | microsoft.directory/policies/standard/read | Leggere le proprietà di base sui criteri |
> | microsoft.directory/policies/owners/read | Leggere i proprietari dei criteri |
> | Microsoft. directory/Policies/policyAppliedTo/Read | Lettura della proprietà Policies. policyAppliedTo |
> | Microsoft. directory/conditionalAccessPolicies/standard/lettura | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/owners/Read | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/conditionalAccessPolicies/policyAppliedTo/Read | Lettura della proprietà Policies. conditionalAccess |
> | Microsoft. directory/privilegedIdentityManagement/allProperties/Read | Leggi tutte le risorse in Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lettura di tutte le proprietà dei log di provisioning. |
> | microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà nei report di accesso, incluse le proprietà con privilegi |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | Microsoft. office365. protectionCenter/allEntities/standard/Read | Leggere le proprietà standard di tutte le risorse nei centri sicurezza e conformità |
> | Microsoft. office365. protectionCenter/attackSimulator/payload/allProperties/Read | Leggi tutte le proprietà dei payload di attacco nel simulatore di attacco |
> | Microsoft. office365. protectionCenter/attackSimulator/Reports/allProperties/Read | Leggere i report sulla simulazione degli attacchi, le risposte e il training associato |
> | Microsoft. office365. protectionCenter/attackSimulator/Simulation/allProperties/Read | Leggi tutte le proprietà dei modelli di simulazione di attacco nel simulatore di attacco |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="service-support-administrator"></a>Amministratore del supporto servizio

Gli utenti con questo ruolo possono aprire richieste di supporto con Microsoft per Azure e Microsoft 365 Services e visualizzare il dashboard del servizio e il centro messaggi nell'interfaccia di amministrazione di [portale di Azure](https://portal.azure.com) e [Microsoft 365](https://admin.microsoft.com). Per altre informazioni vedere [Informazioni sui ruoli di amministratore](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> In precedenza, questo ruolo era denominato "Amministratore del servizio" nel [portale di Azure](https://portal.azure.com) e nell'[interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com). È stato rinominato in "Amministratore servizio di supporto" per allinearlo al nome esistente nell'API Microsoft Graph, nell'API Azure AD Graph e in Azure AD PowerShell.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.network/performance/allProperties/read | Leggere tutte le proprietà delle prestazioni di rete nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="sharepoint-administrator"></a>Amministratore di SharePoint

Gli utenti con questo ruolo dispongono delle autorizzazioni globali all'interno di Microsoft SharePoint Online, quando il servizio è presente, nonché la possibilità di creare e gestire tutti i gruppi di Microsoft 365, gestire i ticket di supporto e monitorare l'integrità dei servizi. Per altre informazioni vedere [Informazioni sui ruoli di amministratore](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio SharePoint". È l'"Amministratore di SharePoint" nel [portale di Azure](https://portal.azure.com).

> [!NOTE]
> Questo ruolo concede anche le autorizzazioni con ambito per l'API Microsoft Graph per Microsoft Intune, consentendo la gestione e la configurazione dei criteri correlati alle risorse SharePoint e OneDrive.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | Microsoft. directory/groups. Unified/create | Creare gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/Delete | Eliminare gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/Restore | Ripristinare i gruppi di Microsoft 365 |
> | Microsoft. directory/groups. Unified/Basic/Update | Aggiornare le proprietà di base nei gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/members/Update | Aggiornare i membri dei gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/owners/Update | Aggiornare i proprietari dei gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.network/performance/allProperties/read | Leggere tutte le proprietà delle prestazioni di rete nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. office365. sharePoint/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Leggere i report sull'utilizzo di Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="skype-for-business-administrator"></a>Amministratore di Skype for Business

gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Skype for Business, quando il servizio è presente, nonché la possibilità di gestire gli attributi specifici per Skype in Azure Active Directory. Inoltre, questo ruolo garantisce la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio, oltre ad accedere a Teams e all'interfaccia di amministrazione di Skype for Business. L'account deve anche avere una licenza per Teams. In caso contrario, non può eseguire i cmdlet di PowerShell per Teams. Per altre informazioni, vedere [Amministratore Skype for Business Online](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5). Per informazioni sulle licenze per Teams, vedere [Licenze per i componenti aggiuntivi Skype for Business e Microsoft Teams](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del sevizio Lync". È l'"Amministratore di Skype for Business" nel [portale di Azure](https://portal.azure.com/).

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestisci tutti gli aspetti di Skype for business online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Leggere i report sull'utilizzo di Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="teams-administrator"></a>Amministratore di Teams

gli utenti in questo ruolo possono gestire tutti gli aspetti del carico di lavoro Microsoft Teams tramite l'interfaccia di amministrazione di Microsoft Teams e Skype for Business e i rispettivi moduli di PowerShell. Sono inclusi, tra le altre aree, tutti gli strumenti di gestione correlati a telefonia, messaggistica, riunioni e i team stessi. Questo ruolo concede inoltre la possibilità di creare e gestire tutti i gruppi di Microsoft 365, gestire i ticket di supporto e monitorare l'integrità dei servizi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | Leggere i membri nascosti di un gruppo |
> | Microsoft. directory/groups. Unified/create | Creare gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/Delete | Eliminare gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/Restore | Ripristinare i gruppi di Microsoft 365 |
> | Microsoft. directory/groups. Unified/Basic/Update | Aggiornare le proprietà di base nei gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/members/Update | Aggiornare i membri dei gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/groups. Unified/owners/Update | Aggiornare i proprietari dei gruppi di Microsoft 365 con l'esclusione dei gruppi assegnabili al ruolo |
> | Microsoft. directory/entità servizio/managePermissionGrantsForGroup. Microsoft-all-Application-Permissions | Concedere a un servizio entità l'accesso diretto ai dati di un gruppo |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.network/performance/allProperties/read | Leggere tutte le proprietà delle prestazioni di rete nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestisci tutti gli aspetti di Skype for business online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Leggere i report sull'utilizzo di Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. teams/allEntities/allProperties/allTasks | Gestire tutte le risorse nei team |

## <a name="teams-communications-administrator"></a>Amministratore comunicazioni Teams

gli utenti con questo ruolo possono gestire gli aspetti del carico di lavoro Microsoft Teams correlati a voce e telefonia. Sono inclusi gli strumenti di gestione per l'assegnazione di numeri di telefono, i criteri per chiamate vocali e riunioni, nonché l'accesso completo al set di strumenti di analisi delle chiamate.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestisci tutti gli aspetti di Skype for business online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Leggere i report sull'utilizzo di Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. teams/callQuality/allProperties/Read | Leggere tutti i dati nel dashboard della qualità della chiamata (CQD) |
> | Microsoft. teams/Meetings/allProperties/allTasks | Gestire riunioni, tra cui criteri per la riunione, configurazioni e Bridge di conferenze |
> | Microsoft. teams/Voice/allProperties/allTasks | Gestire la voce, inclusi i criteri di chiamata e l'inventario e l'assegnazione di numeri di |

## <a name="teams-communications-support-engineer"></a>Tecnico di supporto comunicazioni Teams

gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti in questo ruolo possono visualizzare informazioni complete sui record delle chiamate per tutti i partecipanti coinvolti. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestisci tutti gli aspetti di Skype for business online |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. teams/callQuality/allProperties/Read | Leggere tutti i dati nel dashboard della qualità della chiamata (CQD) |

## <a name="teams-communications-support-specialist"></a>Specialista di supporto comunicazioni Teams

gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti con questo ruolo possono visualizzare i dettagli dell'utente nella chiamata per l'utente specifico cercato. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestisci tutti gli aspetti di Skype for business online |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. teams/callQuality/standard/Read | Leggere i dati di base nel dashboard della qualità della chiamata (CQD) |

## <a name="teams-devices-administrator"></a>Amministratore di dispositivi di Teams

Gli utenti con questo ruolo possono gestire i [dispositivi certificati](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) dai team dal centro di amministrazione dei team. Questo ruolo consente di visualizzare tutti i dispositivi a colpo d'occhio, con la possibilità di cercare e filtrare i dispositivi. L'utente può controllare i dettagli di ogni dispositivo, tra cui account di accesso, marca e modello del dispositivo. L'utente può modificare le impostazioni del dispositivo e aggiornare le versioni del software. Questo ruolo non concede le autorizzazioni per controllare l'attività dei team e la qualità della chiamata del dispositivo.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. teams/Devices/standard/Read | Gestire tutti gli aspetti dei dispositivi certificati da team, inclusi i criteri di configurazione |

## <a name="usage-summary-reports-reader"></a>Lettore report di riepilogo utilizzo

Gli utenti con questo ruolo possono accedere ai dati aggregati a livello di tenant e alle informazioni dettagliate associate nel Microsoft 365 interfaccia di amministrazione per il Punteggio di utilizzo e produttività, ma non possono accedere a dettagli o informazioni dettagliate a livello di utente. Nel centro di amministrazione Microsoft 365 per i due report, si distingue tra i dati aggregati a livello di tenant e i dettagli a livello di utente. Questo ruolo offre un livello di protezione aggiuntivo per i singoli dati identificabili dall'utente, richiesti sia dai clienti che dai team legali.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.office365.network/performance/allProperties/read | Leggere tutte le proprietà delle prestazioni di rete nell'interfaccia di amministrazione di Microsoft 365 |
> | Microsoft. office365. usageReports/allEntities/standard/Read | Leggere report sull'utilizzo di Office 365 aggregati a livello di tenant |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="user-administrator"></a>Amministratore utenti

Gli utenti con questo ruolo possono creare utenti e gestire tutti gli aspetti degli utenti con alcune restrizioni (vedere la tabella) e possono aggiornare i criteri di scadenza delle password. Possono anche creare e gestire tutti i gruppi. Il ruolo consente anche di creare e gestire visualizzazioni utente, gestire i ticket di supporto e monitorare l'integrità del servizio. Gli amministratori degli utenti non sono autorizzati a gestire alcune proprietà utente per gli utenti con la maggior parte dei ruoli di amministratore. L'utente con questo ruolo non ha le autorizzazioni per gestire l'autenticazione MFA. I ruoli che rappresentano eccezioni a questa restrizione sono elencati nella tabella seguente.

| Autorizzazione utente amministratore | Note |
| --- | --- |
| Creare utenti e gruppi<br/>Creare e gestire visualizzazioni utente<br/>Gestire ticket di supporto di Office<br/>Aggiornare i criteri di scadenza delle password |  |
| Gestire licenze<br/>Gestire tutte le proprietà utente, ad eccezione del Nome dell'entità utente | Si applica a tutti gli utenti, inclusi tutti gli amministratori |
| Eliminare e ripristinare<br/>Disattivare e attivare<br/>Gestire tutte le proprietà utente, incluso il Nome dell'entità utente<br/>Aggiornare le chiavi dispositivo (FIDO) | Si applica agli utenti che non sono amministratori o in uno dei seguenti ruoli:<ul><li>Amministratore del supporto tecnico</li><li>Utente senza ruolo</li><li>Amministratore utenti</li></ul> |
| Invalidare i token di aggiornamento<br/>Reimposta password | Per un elenco dei ruoli che un amministratore utente può reimpostare le password per e invalidare i token di aggiornamento, vedere [autorizzazioni di reimpostazione della password](#password-reset-permissions). |

> [!IMPORTANT]
> gli utenti con questo ruolo possono modificare le password di utenti che possono accedere a dati sensibili, informazioni riservate o configurazioni critiche sia dall'interno che dall'esterno di Azure Active Directory. Modificare la password di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
>
>- Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli utenti con ruolo di amministratore degli utenti. Ciò significa che un amministratore degli utenti potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
>- Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
>- I proprietari del gruppo di sicurezza e del gruppo di Microsoft 365, che possono gestire l'appartenenza ai gruppi. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
>- Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
>- Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | microsoft.directory/appRoleAssignments/create | Creare assegnazioni di ruolo applicazione |
> | microsoft.directory/appRoleAssignments/delete | Eliminare le assegnazioni di ruolo applicazione |
> | Microsoft. directory/appRoleAssignments/Basic/Update | Aggiornare le proprietà di base delle assegnazioni di ruolo applicazione |
> | microsoft.directory/contacts/create | Crea contatti |
> | microsoft.directory/contacts/delete | Elimina contatti |
> | microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base nei contatti |
> | Microsoft. directory/entitlementManagement/allProperties/allTasks | Creare ed eliminare risorse, leggere e aggiornare tutte le proprietà in Azure AD gestione dei diritti |
> | Microsoft. Directory/gruppi/assignLicense | Assegnare licenze prodotto a gruppi per le licenze basate sui gruppi |
> | microsoft.directory/groups/create | Creazione di gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/groups/delete | Elimina gruppi, escluso il gruppo assegnabile al ruolo |
> | microsoft.directory/groups/hiddenMembers/read | Leggere i membri nascosti di un gruppo |
> | Microsoft. Directory/gruppi/reprocessLicenseAssignment | Rielaborare le assegnazioni delle licenze per le licenze basate sui gruppi |
> | microsoft.directory/groups/restore | Ripristinare i gruppi eliminati |
> | microsoft.directory/groups/basic/update | Aggiornare le proprietà di base nei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. Directory/gruppi/Classificazione/aggiornamento | Aggiornare la proprietà di classificazione dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. directory/groups/dynamicMembershipRule/Update | Aggiornamento della regola di appartenenza dinamica dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. directory/groups/groupType/Update | Aggiornare la proprietà groupType per un gruppo |
> | microsoft.directory/groups/members/update | Aggiornare i membri dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | Microsoft. directory/groups/onPremWriteBack/Update | Aggiornare i gruppi di Azure AD da scrivere nuovamente in locale |
> | microsoft.directory/groups/owners/update | Aggiornare i proprietari dei gruppi, esclusi i gruppi assegnabili al ruolo |
> | microsoft.directory/groups/settings/update | Aggiornare le impostazioni dei gruppi |
> | Microsoft. Directory/gruppi/visibilità/aggiornamento | Aggiornare la proprietà Visibility dei gruppi |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare le concessioni di autorizzazione OAuth 2,0, leggere e aggiornare tutte le proprietà |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare le assegnazioni di ruolo dell'entità servizio |
> | microsoft.directory/users/assignLicense | Gestire le licenze utente |
> | microsoft.directory/users/create | Aggiungere utenti |
> | microsoft.directory/users/delete | Eliminare gli utenti |
> | Microsoft. Directory/utenti/Disabilita | Disabilita utenti |
> | Microsoft. Directory/utenti/Abilita | Abilita utenti |
> | microsoft.directory/users/inviteGuest | Invitare gli utenti guest |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forza la disconnessione invalidando i token di aggiornamento utente |
> | Microsoft. Directory/Users/reprocessLicenseAssignment | Rielaborare le assegnazioni delle licenze per gli utenti |
> | microsoft.directory/users/restore | Ripristinare gli utenti eliminati |
> | microsoft.directory/users/basic/update | Aggiornare le proprietà di base per gli utenti |
> | microsoft.directory/users/manager/update | Gestione aggiornamenti per gli utenti |
> | microsoft.directory/users/password/update | Reimposta le password per tutti gli utenti |
> | microsoft.directory/users/userPrincipalName/update | Aggiornare il nome dell'entità utente degli utenti |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità del servizio di Azure |
> | microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto di Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Leggere e configurare l'integrità dei servizi nell'interfaccia di amministrazione di Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire richieste di servizio Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Leggere le proprietà di base per tutte le risorse nell'interfaccia di amministrazione di Microsoft 365 |

## <a name="deprecated-roles"></a>Ruoli deprecati

I ruoli seguenti non devono essere usati. Questi ruoli sono stati deprecati e verranno rimossi da Azure Active Directory in futuro.

* Amministratore di licenze ad hoc
* Aggiunta di dispositivi
* Gestione dispositivi
* Utenti di dispositivi
* Autore di utenti verificati tramite posta elettronica
* Amministratore della cassetta postale
* Aggiunta di dispositivi all'area di lavoro

## <a name="roles-not-shown-in-the-portal"></a>Ruoli non visualizzati nel portale

Non tutti i ruoli restituiti da PowerShell o dall'API Microsoft Graph sono visibili nel portale di Azure. Nella tabella seguente sono riportate queste differenze in modo organizzato.

Nome API | Nome portale di Azure | Note
-------- | ------------------- | -------------
Aggiunta di dispositivi | Deprecato | [Documentazione dei ruoli deprecati](#deprecated-roles)
Gestione dispositivi | Deprecato | [Documentazione dei ruoli deprecati](#deprecated-roles)
Utenti di dispositivi | Deprecato | [Documentazione dei ruoli deprecati](#deprecated-roles)
Account di sincronizzazione della directory | Non viene visualizzato perché non deve essere usato | [Documentazione per gli account di sincronizzazione della directory](#directory-synchronization-accounts)
Utente guest | Non viene visualizzato perché non può essere usato | ND
Supporto di livello 1 partner | Non viene visualizzato perché non deve essere usato | [Documentazione per Supporto partner - Livello 1](#partner-tier1-support)
Supporto di livello 2 partner | Non viene visualizzato perché non deve essere usato | [Documentazione per Supporto partner - Livello 2](#partner-tier2-support)
Utente guest con restrizioni | Non viene visualizzato perché non può essere usato | ND
Utente | Non viene visualizzato perché non può essere usato | ND
Aggiunta di dispositivi all'area di lavoro | Deprecato | [Documentazione dei ruoli deprecati](#deprecated-roles)

## <a name="password-reset-permissions"></a>Autorizzazioni di reimpostazione password

Le intestazioni di colonna rappresentano i ruoli che possono reimpostare le password. Le righe della tabella contengono i ruoli per i quali è possibile reimpostare la password.

È possibile reimpostare la password | Amministratore password | Amministratore helpdesk | Amministratore autenticazione | User Admin | Amministratore dell'autenticazione con privilegi | Amministratore globale
------ | ------ | ------ | ------ | ------ | ------ | ------
Amministratore autenticazione | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Ruoli con autorizzazioni di lettura nella directory | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Amministratore globale | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Amministrazione gruppi | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Mittente dell'invito guest | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Amministratore helpdesk | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Ruolo con autorizzazioni di lettura per il Centro messaggi | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Amministratore password | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Amministratore dell'autenticazione con privilegi | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Amministratore del ruolo con privilegi | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Lettore di report | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Utente (nessun ruolo amministratore) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
User Admin | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Lettore report di riepilogo utilizzo | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* Un amministratore globale non può rimuovere la propria assegnazione di amministratore globale. In questo modo si evita una situazione in cui un'organizzazione ha 0 amministratori globali.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD ai gruppi](groups-assign-role.md)
- [Informazioni sui diversi ruoli](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Assegnare un utente come amministratore di una sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
