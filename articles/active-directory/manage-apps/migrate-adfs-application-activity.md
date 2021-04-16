---
title: Usare il report attività per spostare AD FS app in Azure Active Directory | Microsoft Docs'
description: Il report Active Directory Federation Services (AD FS) dell'applicazione consente di eseguire rapidamente la migrazione delle applicazioni da AD FS a Azure Active Directory (Azure AD). Questo strumento di migrazione per AD FS la compatibilità con Azure AD e fornisce indicazioni sulla migrazione.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 01/14/2019
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b9265a61b0879078332b8ccc2d10e711b4ac8f1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377421"
---
# <a name="use-the-ad-fs-application-activity-report-to-migrate-applications-to-azure-ad"></a>Usare il report attività dell'applicazione Active Directory Federation Services per eseguire la migrazione delle applicazioni ad Azure AD

Molte organizzazioni usano Active Directory Federation Services (AD FS) per fornire l'accesso Single Sign-On alle applicazioni cloud. Lo spostamento delle applicazioni AD FS in Azure AD per l'autenticazione, in particolare in termini di gestione dei costi, gestione dei rischi, produttività, conformità e governance. Tuttavia, la comprensione delle applicazioni compatibili con Azure AD e l'identificazione di passaggi di migrazione specifici possono richiedere molto tempo.

Il AD FS di attività dell'applicazione nel portale di Azure consente di identificare rapidamente le applicazioni di cui è possibile eseguire la migrazione a Azure AD. Valuta tutte le applicazioni AD FS compatibilità con Azure AD, verifica la presenza di eventuali problemi e fornisce indicazioni sulla preparazione delle singole applicazioni per la migrazione. Con il AD FS di attività dell'applicazione, è possibile:

* **Individuare AD FS applicazioni e l'ambito della migrazione.** Il AD FS di attività dell'applicazione elenca tutte AD FS applicazioni dell'organizzazione che hanno avuto un account di accesso utente attivo negli ultimi 30 giorni. Il report indica l'idoneità delle app per la migrazione Azure AD. Il report non visualizza le relying party correlate a Microsoft AD FS, ad esempio Office 365. Ad esempio, le relying party con nome 'urn:federation:MicrosoftOnline'.

* **Assegnare priorità alle applicazioni per la migrazione.** Ottenere il numero di utenti univoci che hanno eseguito l'accesso all'applicazione negli ultimi 1, 7 o 30 giorni per determinare la criticità o il rischio di migrazione dell'applicazione.
* **Eseguire test di migrazione e risolvere i problemi.** Il servizio di report esegue automaticamente i test per determinare se un'applicazione è pronta per la migrazione. I risultati vengono visualizzati nel report delle AD FS dell'applicazione come stato di migrazione. Se la AD FS non è compatibile con una configurazione Azure AD, si ottengono indicazioni specifiche su come risolvere la configurazione in Azure AD.

I AD FS dell'attività dell'applicazione sono disponibili per gli utenti a cui è assegnato uno di questi ruoli di amministratore: amministratore globale, lettore di report, ruolo con autorizzazioni di lettura per la sicurezza, amministratore dell'applicazione o amministratore dell'applicazione cloud.

## <a name="prerequisites"></a>Prerequisiti

* L'organizzazione deve attualmente usare AD FS per accedere alle applicazioni.
* Azure AD Connect Health deve essere abilitato nel tenant Azure AD.
* È Azure AD Connect Health installare AD FS'agente.
   * [Altre informazioni sulle Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
   * [Introduzione alla configurazione Azure AD Connect Health installare l'agente AD FS](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT] 
>Esistono due motivi per cui non verranno visualizzati tutti i motivi per cui si prevede di installare le Azure AD Connect Health. Il report AD FS'attività dell'applicazione mostra AD FS relying party con account di accesso utente negli ultimi 30 giorni. Inoltre, il report non visualizza le relying party correlate a Microsoft, ad esempio Office 365.

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Individuare AD FS applicazioni di cui è possibile eseguire la migrazione 

Il report AD FS'attività dell'applicazione è disponibile nel portale di Azure in Azure AD **utilizzo & informazioni dettagliate.** Il report AD FS'attività dell'applicazione analizza ogni applicazione AD FS per determinare se è possibile eseguirne la migrazione senza modifiche o se è necessaria una revisione aggiuntiva. 

1. Accedere al [](https://portal.azure.com) portale di Azure con un ruolo di amministratore che abbia accesso ai dati delle attività dell'applicazione AD FS (amministratore globale, lettore di report, ruolo con autorizzazioni di lettura per la sicurezza, amministratore dell'applicazione o amministratore dell'applicazione cloud).

2. Selezionare **Azure Active Directory** e quindi applicazioni **aziendali.**

3. In **Attività** selezionare **Utilizzo & Insights** e  quindi selezionare AD FS'attività dell'applicazione per aprire un elenco di tutte le applicazioni AD FS nell'organizzazione.

   ![AD FS'attività dell'applicazione](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Per ogni applicazione nell'elenco AD FS attività dell'applicazione, visualizzare lo **stato della migrazione**:

   * **Pronto per la migrazione** significa che AD FS configurazione dell'applicazione è completamente supportata in Azure AD e può essere migrata così come è.

   * **La revisione** delle esigenze indica che è possibile eseguire la migrazione di alcune impostazioni dell'applicazione a Azure AD, ma è necessario esaminare le impostazioni di cui non è possibile eseguire la migrazione così come sono.

   * **I passaggi aggiuntivi** necessari Azure AD non supportano alcune impostazioni dell'applicazione, quindi non è possibile eseguire la migrazione dell'applicazione nello stato corrente.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Valutare l'idoneità di un'applicazione per la migrazione 

1. Nell'AD FS di attività dell'applicazione fare clic sullo stato nella colonna **Stato migrazione** per aprire i dettagli della migrazione. Verrà visualizzato un riepilogo dei test di configurazione superati, insieme a eventuali problemi di migrazione.

   ![Dettagli sulla migrazione](media/migrate-adfs-application-activity/migration-details.png)

2. Fare clic su un messaggio per aprire dettagli aggiuntivi della regola di migrazione. Per un elenco completo delle proprietà testate, vedere la tabella AD FS [test](#ad-fs-application-configuration-tests) di configurazione dell'applicazione, riportata di seguito.

   ![Dettagli della regola di migrazione](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS test di configurazione dell'applicazione

Nella tabella seguente sono elencati tutti i test di configurazione eseguiti AD FS applicazioni.

|Risultato  |Pass/Warning/Fail  |Descrizione  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> È stata rilevata almeno una regola non migrabile per AdditionalAuthentication.       | Pass/Warning          | Il relying party include regole per richiedere l'autenticazione a più fattori (MFA). Per passare alla Azure AD, convertire tali regole in criteri di accesso condizionale. Se si usa un'autenticazione a più fattori locale, è consigliabile passare a Azure AD MFA. [Altre informazioni sull'accesso condizionale.](../authentication/concept-mfa-howitworks.md)        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> La relying party ha AdditionalWSFedEndpoint impostato su true.       | Esito positivo o negativo          | Il relying party in AD FS più endpoint WS-Fed di asserzione.Attualmente, Azure AD ne supporta solo uno.In uno scenario in cui questo risultato blocca la migrazione, [determinare .](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> La relying party ha impostato AllowedAuthenticationClassReferences.       | Esito positivo o negativo          | Questa impostazione in AD FS consente di specificare se l'applicazione è configurata per consentire solo determinati tipi di autenticazione. Per ottenere questa funzionalità, è consigliabile usare l'accesso condizionale. In uno scenario in cui questo risultato blocca la migrazione, [determinare .](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)  [Altre informazioni sull'accesso condizionale.](../authentication/concept-mfa-howitworks.md)          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Esito positivo o negativo          | Questa impostazione in AD FS consente di specificare se l'applicazione è configurata per ignorare i cookie SSO e **Always Prompt for Authentication.** In Azure AD è possibile gestire la sessione di autenticazione usando i criteri di accesso condizionale per ottenere un comportamento simile. [Altre informazioni sulla configurazione della gestione delle sessioni di autenticazione con l'accesso condizionale.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|Test-ADFSRPAutoUpdateEnabled <br> La relying party ha AutoUpdateEnabled impostato su true       | Pass/Warning          | Questa impostazione in AD FS consente di specificare se AD FS è configurato per aggiornare automaticamente l'applicazione in base alle modifiche all'interno dei metadati della federazione. Azure AD attualmente non supporta questa operazione, ma non deve bloccare la migrazione dell'applicazione a Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> La relying party ha più ClaimsProvider abilitati       | Esito positivo o negativo          | Questa impostazione in AD FS chiama i provider di identità da cui il relying party accetta attestazioni. In Azure AD è possibile abilitare la collaborazione esterna usando Azure AD B2B. [Altre informazioni su Azure AD B2B.](../external-identities/what-is-b2b.md)          |
|Test-ADFSRPDelegationAuthorizationRules      | Esito positivo o negativo          | Per l'applicazione sono definite regole di autorizzazione di delega personalizzate. Questo è un WS-Trust che Azure AD protocolli di autenticazione moderni, ad esempio OpenID Connect e OAuth 2.0. [Altre informazioni su Microsoft Identity Platform.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPImpersonationAuthorizationRules       | Pass/Warning          | Nell'applicazione sono definite regole di autorizzazione di rappresentazione personalizzate.Si tratta di un WS-Trust che Azure AD protocolli di autenticazione moderni, ad esempio OpenID Connect e OAuth 2.0. [Altre informazioni su Microsoft Identity Platform.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPIssuanceAuthorizationRules <br> È stata rilevata almeno una regola non migrabile per IssuanceAuthorization.       | Pass/Warning          | L'applicazione dispone di regole di autorizzazione per il rilascio personalizzate definite in AD FS.Azure AD supporta questa funzionalità con l'Azure AD condizionale. [Altre informazioni sull'accesso condizionale.](../conditional-access/overview.md) <br> È anche possibile limitare l'accesso a un'applicazione in base a utenti o gruppi assegnati all'applicazione. [Altre informazioni sull'assegnazione di utenti e gruppi per l'accesso alle applicazioni](./assign-user-or-group-access-portal.md).            |
|Test-ADFSRPIssuanceTransformRules <br> È stata rilevata almeno una regola non migrabile per IssuanceTransform.       | Pass/Warning          | L'applicazione dispone di regole di trasformazione rilascio personalizzate definite in AD FS. Azure AD supporta la personalizzazione delle attestazioni rilasciate nel token. Per altre informazioni, vedere [Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali.](../develop/active-directory-saml-claims-customization.md)           |
|Test-ADFSRPMonitoringEnabled <br> La relying party ha MonitoringEnabled impostato su true.       | Pass/Warning          | Questa impostazione in AD FS consente di specificare se AD FS è configurato per aggiornare automaticamente l'applicazione in base alle modifiche all'interno dei metadati della federazione. Azure AD attualmente non supporta questa funzionalità, ma non deve bloccare la migrazione dell'applicazione a Azure AD.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Pass/Warning          | AD FS consente un'affollata temporale in base alle ore NotBefore e NotOnOrAfter nel token SAML. Azure AD gestisce automaticamente questa operazione per impostazione predefinita.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> La relying party ha RequestMFAFromClaimsProviders impostato su true.       | Pass/Warning          | Questa impostazione in AD FS determina il comportamento dell'autenticazione a più fattori quando l'utente proviene da un provider di attestazioni diverso. In Azure AD è possibile abilitare la collaborazione esterna usando Azure AD B2B. È quindi possibile applicare criteri di accesso condizionale per proteggere l'accesso guest. Altre informazioni su [Azure AD B2B](../external-identities/what-is-b2b.md) e [l'accesso condizionale.](../conditional-access/overview.md)          |
|Test-ADFSRPSignedSamlRequestsRequired <br> La relying party ha SignedSamlRequestsRequired impostato su true       | Esito positivo o negativo          | L'applicazione viene configurata AD FS per verificare la firma nella richiesta SAML. Azure AD accetta una richiesta SAML firmata. tuttavia, non verificherà la firma. Azure AD diversi metodi per la protezione da chiamate dannose. Ad esempio, Azure AD gli URL di risposta configurati nell'applicazione per convalidare la richiesta SAML. Azure AD invierà un token solo agli URL di risposta configurati per l'applicazione. Se si ha uno scenario in cui questo risultato blocca la migrazione, [determinare .](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Pass/Warning         | L'applicazione è configurata per una durata del token personalizzata. Il AD FS predefinito è un'ora.Azure AD questa funzionalità tramite l'accesso condizionale. Per altre informazioni, vedere Configurare [la gestione delle sessioni di autenticazione con l'accesso condizionale.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|La relying party è impostata per crittografare le attestazioni. Questa funzionalità è supportata da Azure AD       | Pass          | Con Azure AD, è possibile crittografare il token inviato all'applicazione. Per altre informazioni, vedere [Configurare la Azure AD di token SAML.](./howto-saml-token-encryption.md)          |
|EncryptedNameIdRequiredCheckResult      | Esito positivo o negativo          | L'applicazione è configurata per crittografare l'attestazione nameID nel token SAML.Con Azure AD, è possibile crittografare l'intero token inviato all'applicazione.La crittografia di attestazioni specifiche non è ancora supportata. Per altre informazioni, vedere [Configurare la Azure AD di token SAML.](./howto-saml-token-encryption.md)         |

## <a name="check-the-results-of-claim-rule-tests"></a>Controllare i risultati dei test delle regole attestazioni

Se è stata configurata una regola attestazione per l'applicazione in AD FS, l'esperienza fornirà un'analisi granulare per tutte le regole attestazione. Verranno esaminate le regole attestazione che possono essere spostate in Azure AD e quelle che devono essere esaminate ulteriormente.

1. Nell'AD FS attività dell'applicazione fare clic sullo stato nella **colonna Stato migrazione** per aprire i dettagli della migrazione. Verrà visualizzato un riepilogo dei test di configurazione superati, insieme a eventuali potenziali problemi di migrazione.

2. Nella pagina **Dettagli regola di migrazione** espandere i risultati per visualizzare i dettagli sui potenziali problemi di migrazione e per ottenere indicazioni aggiuntive. Per un elenco dettagliato di tutte le regole attestazione testate, vedere la tabella Controllare i risultati dei test delle [regole](#check-the-results-of-claim-rule-tests) attestazioni riportata di seguito.

   L'esempio seguente mostra i dettagli della regola di migrazione per la regola IssuanceTransform. Elenca le parti specifiche dell'attestazione che devono essere esaminate e trattate prima di poter eseguire la migrazione dell'applicazione Azure AD.

   ![Informazioni aggiuntive sulle regole di migrazione](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Test delle regole attestazione

Nella tabella seguente sono elencati tutti i test delle regole attestazione eseguiti AD FS applicazioni.

|Proprietà  |Descrizione  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | L'istruzione condition usa espressioni regolari per valutare se l'attestazione corrisponde a un determinato modello.Per ottenere una funzionalità simile in Azure AD, è possibile usare una trasformazione predefinita, ad esempio IfEmpty(), StartWith(), Contains(), tra gli altri. Per altre informazioni, vedere [Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_CONDITION_CLASS      | L'istruzione condition ha più condizioni che devono essere valutate prima di eseguire l'istruzione di rilascio.Azure AD può supportare questa funzionalità con le funzioni di trasformazione dell'attestazione in cui è possibile valutare più valori di attestazione.Per altre informazioni, vedere [Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_RULE_TYPE      | Impossibile riconoscere la regola attestazione. Per altre informazioni su come configurare le attestazioni in Azure AD, vedere Personalizzare le attestazioni [rilasciate nel token SAML per le applicazioni aziendali.](../develop/active-directory-saml-claims-customization.md)          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | L'istruzione condition usa un'autorità di emittente non supportata in Azure AD.Attualmente, Azure AD non viene origine di attestazioni da archivi diversi da Active Directory o Azure AD. Se questa operazione non consente di eseguire la migrazione di applicazioni Azure AD, [determinare](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | L'istruzione condition usa una funzione di aggregazione per rilasciare o aggiungere una singola attestazione indipendentemente dal numero di corrispondenze.In Azure AD, è possibile valutare l'attributo di un utente per decidere quale valore usare per l'attestazione con funzioni come IfEmpty(), StartWith(), Contains(), tra le altre.Per altre informazioni, vedere [Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali.](../develop/active-directory-saml-claims-customization.md)          |
|RESTRICTED_CLAIM_ISSUED      | L'istruzione condition usa un'attestazione limitata in Azure AD. Potrebbe essere possibile rilasciare un'attestazione con restrizioni, ma non è possibile modificarne l'origine o applicare alcuna trasformazione. Per altre informazioni, vedere [Personalizzare le attestazioni generate nei token per un'app specifica in Azure AD](../develop/active-directory-claims-mapping.md).          |
|EXTERNAL_ATTRIBUTE_STORE      | L'istruzione di rilascio usa un archivio attributi diverso da Active Directory. Attualmente, Azure AD non origine attestazioni da archivi diversi da Active Directory o Azure AD. Se questo risultato blocca la migrazione delle applicazioni Azure AD, [determinare .](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)          |
|UNSUPPORTED_ISSUANCE_CLASS      | L'istruzione di rilascio usa ADD per aggiungere attestazioni al set di attestazioni in ingresso. In Azure AD, questo può essere configurato come più trasformazioni di attestazione.Per altre informazioni, vedere [Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali.](../develop/active-directory-claims-mapping.md)         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | L'istruzione di rilascio usa espressioni regolari per trasformare il valore dell'attestazione da emettere.Per ottenere funzionalità simili in Azure AD, è possibile usare una trasformazione predefinita, ad esempio Extract(), Trim(), ToLower, tra le altre. Per altre informazioni, vedere [Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali.](../develop/active-directory-saml-claims-customization.md)          |

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>Non è possibile visualizzare tutte le AD FS nel report

 Se è stato installato Azure AD Connect integrità, ma viene comunque visualizzato il prompt per installarlo o non vengono visualizzate tutte le applicazioni AD FS nel report, è possibile che non siano presenti applicazioni AD FS attive o che le applicazioni AD FS siano applicazioni Microsoft.
 
 Il report AD FS'attività dell'applicazione elenca tutte le applicazioni AD FS dell'organizzazione con accesso degli utenti attivi negli ultimi 30 giorni. Inoltre, il report non visualizza le relying party correlate a Microsoft AD FS, ad esempio Office 365. Ad esempio, le relying party con nome 'urn:federation:MicrosoftOnline', 'microsoftonline', 'microsoft:winhello:cert:prov:server' non vengono mostrate nell'elenco.





## <a name="next-steps"></a>Passaggi successivi

- [Video: Come usare il report attività AD FS per eseguire la migrazione di un'applicazione](https://www.youtube.com/watch?v=OThlTA239lU)
- [Gestione delle applicazioni con Azure Active Directory](what-is-application-management.md)
- [Gestire l'accesso alle app](what-is-access-management.md)
- [Azure AD Connect e federazione](../hybrid/how-to-connect-fed-whatis.md)
