---
title: Spostamento dell'autenticazione dell'applicazione AD FS a Azure Active Directory
description: Informazioni su come usare Azure Active Directory sostituire Active Directory Federation Services (AD FS), offrendo agli utenti l'accesso Single Sign-On a tutte le applicazioni.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: b0fd3dae2ff9c6de39462d19dc41a32ba51171e0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534861"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Trasferimento dell'autenticazione dell'applicazione da Azure Active Directory Federation Services ad Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) offre a persone, partner e clienti una singola identità per accedere alle applicazioni e collaborare da qualsiasi piattaforma e dispositivo. Azure AD include una [suite completa di funzionalità di gestione delle identità.](../fundamentals/active-directory-whatis.md) La standardizzazione dell'autenticazione e dell'autorizzazione dell'Azure AD offre questi vantaggi.

> [!TIP]
> Questo articolo è stato scritto per un pubblico di sviluppatori. I project manager e gli amministratori che pianificano di spostare un'applicazione in Azure AD consigliabile leggere [Migrazione dell'autenticazione dell'applicazione Azure AD](migrate-application-authentication-to-azure-active-directory.md).

## <a name="azure-ad-benefits"></a>Vantaggi di Azure AD

Se si dispone di una directory locale che contiene account utente, è probabile che siano installate molte applicazioni per cui gli utenti eseguono l'autenticazione. Ognuna di queste app è configurata per consentire agli utenti di accedere usando le proprie identità.

Gli utenti possono anche eseguire l'autenticazione direttamente con il Active Directory locale. Active Directory Federation Services (AD FS) è un servizio di gestione delle identità locale basato su standard. Estende la possibilità di usare la funzionalità Single Sign-On (SSO) tra partner commerciali attendibili in modo che gli utenti non siano tenuti ad accedere separatamente a ogni applicazione. Questa operazione è nota come identità federata.

Molte organizzazioni dispongono di app line-of-business personalizzate o SaaS (Software as a Service) federate direttamente in AD FS, insieme alle app basate su Microsoft 365 e Azure AD.

  ![Applicazioni connesse direttamente in locale](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> Per aumentare la sicurezza delle applicazioni, l'obiettivo è avere un unico set di criteri e controlli di accesso negli ambienti locali e cloud.

  ![Applicazioni connesse tramite Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Tipi di app di cui eseguire la migrazione

La migrazione di tutta l'autenticazione dell'applicazione Azure AD è ottimale, perché offre un singolo piano di controllo per la gestione delle identità e degli accessi.

Le applicazioni possono usare protocolli moderni o legacy per l'autenticazione. Quando si pianifica la migrazione a Azure AD, è consigliabile eseguire prima la migrazione delle app che usano protocolli di autenticazione moderni, ad esempio SAML e Open ID Connect. Queste app possono essere riconfigurate per l'autenticazione con Azure AD tramite un connettore incorporato dalla raccolta app Azure o registrando l'applicazione in Azure AD. Le app che usano protocolli meno recenti possono essere integrate usando Application Proxy.

Per altre informazioni, vedere:

* [Uso Azure AD Application Proxy per pubblicare app locali per utenti remoti.](what-is-application-proxy.md)
* [Cos'è la gestione delle applicazioni?](what-is-application-management.md)
* [AD FS report attività dell'applicazione per eseguire la migrazione delle applicazioni Azure AD](migrate-adfs-application-activity.md).
* [Monitorare AD FS usando Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Processo di migrazione

Durante il processo di spostamento dell'autenticazione dell'app Azure AD, testare le app e la configurazione. È consigliabile continuare a usare gli ambienti di test esistenti per i test di migrazione quando si passa all'ambiente di produzione. Se un ambiente di test non è attualmente [](https://azure.microsoft.com/services/app-service/) disponibile, è possibile configurarne uno usando Servizio app di Azure o Macchine virtuali di [Azure,](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)a seconda dell'architettura dell'applicazione.

È possibile scegliere di configurare un tenant di test separato Azure AD in cui sviluppare le configurazioni dell'app.

Il processo di migrazione potrebbe essere simile al seguente:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>Fase 1 : stato corrente: l'app di produzione esegue l'autenticazione con AD FS

  ![Fase 1 della migrazione ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>Fase 2: (facoltativo) Puntare un'istanza di test dell'app al tenant Azure AD test

Aggiornare la configurazione in modo che l'istanza di test dell'app punti a un tenant Azure AD test e apportare le modifiche necessarie. L'app può essere testata con gli utenti nel tenant Azure AD test. Durante il processo di sviluppo, è possibile usare strumenti come [Fiddler](https://www.telerik.com/fiddler) per confrontare e verificare richieste e risposte.

Se non è possibile configurare un tenant di test separato, ignorare questa fase e puntare un'istanza di test dell'app al tenant del Azure AD di produzione, come descritto nella fase 3 seguente.

  ![Fase di migrazione 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>Fase 3: puntare un'istanza di test dell'app al tenant Azure AD produzione

Aggiornare la configurazione in modo che punti l'istanza di test dell'app al tenant Azure AD produzione. È ora possibile eseguire test con gli utenti nel tenant di produzione. Se necessario, vedere la sezione di questo articolo sulla transizione degli utenti.

  ![Fase di migrazione 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>Fase 4: puntare l'app di produzione al tenant Azure AD produzione

Aggiornare la configurazione dell'app di produzione in modo che punti al tenant Azure AD produzione.

  ![Fase di migrazione 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Le app che eseguono l'AD FS possono usare i gruppi di Active Directory per le autorizzazioni. Usare [Azure AD Connect per](../hybrid/how-to-connect-sync-whatis.md) sincronizzare i dati di identità tra l'ambiente locale e Azure AD prima di iniziare la migrazione. Verificare i gruppi e l'appartenenza prima della migrazione in modo che sia possibile concedere l'accesso agli stessi utenti quando viene eseguita la migrazione dell'applicazione.

### <a name="line-of-business-apps"></a>App line-of-business

Le app line-of-business sono quelle sviluppate dall'organizzazione o quelle che sono un prodotto in pacchetto standard. Tra gli esempi sono incluse le app compilate in Windows Identity Foundation e nelle app di SharePoint (non SharePoint Online).

Le app line-of-business che usano OAuth 2.0, OpenID Connect o WS-Federation possono essere integrate con Azure AD come [registrazioni di app.](../develop/quickstart-register-app.md) Integrare app personalizzate che usano SAML 2.0 o WS-Federation come applicazioni non della [raccolta](add-application-portal.md) nella pagina delle applicazioni aziendali nel [portale di Azure](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Accesso Single Sign-On basato su SAML

Le app che usano SAML 2.0 per l'autenticazione possono essere configurate per l'accesso [Single Sign-On](what-is-single-sign-on.md) (SSO) basato su SAML. Con l'accesso SSO basato su SAML è possibile eseguire il mapping degli utenti a ruoli applicazione specifici in base alle regole definite nelle attestazioni SAML.

Per configurare un'applicazione SaaS per l'accesso SSO basato su SAML, vedere [Avvio rapido: Configurare l'accesso Single Sign-On basato su SAML.](add-application-portal-setup-sso.md)

  ![Screenshot dell'utente SAML SSO ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Molte applicazioni SaaS hanno [un'esercitazione specifica](../saas-apps/tutorial-list.md) dell'applicazione che illustra la configurazione per l'accesso SSO basato su SAML.

  ![Esercitazione sull'app](media/migrate-adfs-apps-to-azure/app-tutorial.png)

La migrazione di alcune app può essere eseguita facilmente. Le app con requisiti più complessi, ad esempio attestazioni personalizzate, possono richiedere una configurazione aggiuntiva Azure AD e/o Azure AD Connect. Per informazioni sui mapping delle attestazioni supportati, vedere Procedura: Personalizzare le attestazioni generate nei token per [un'app specifica in un tenant (anteprima).](../develop/active-directory-claims-mapping.md)

Tenere presenti le limitazioni seguenti quando si esegue il mapping degli attributi:

* Non tutti gli attributi che possono essere emessi in AD FS vengono visualizzati in Azure AD come attributi da generare nei token SAML, anche se tali attributi vengono sincronizzati. Quando si modifica l'attributo, **l'elenco** a discesa Valore mostra i diversi attributi disponibili in Azure AD. Controllare [Azure AD Connect configurazione degli argomenti](../hybrid/how-to-connect-sync-whatis.md) di sincronizzazione per assicurarsi che un attributo obbligatorio, ad esempio **samAccountName,** sia sincronizzato con Azure AD. È possibile usare gli attributi di estensione per generare qualsiasi attestazione che non fa parte dello schema utente standard in Azure AD.
* Negli scenari più comuni, per un'app sono necessarie solo l'attestazione **NameID** e altre attestazioni dell'ID utente comuni. Per determinare se sono necessarie attestazioni aggiuntive, esaminare le attestazioni che si sta emettendo da AD FS.
* Non tutte le attestazioni possono essere rilasciate, poiché alcune attestazioni sono protette in Azure AD.
* La possibilità di usare token SAML crittografati è ora disponibile in anteprima. Vedere [Procedura: personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali.](../develop/active-directory-saml-claims-customization.md)

### <a name="software-as-a-service-saas-apps"></a>App SaaS (Software as a Service)

Se gli utenti azionano app SaaS come Salesforce, ServiceNow o Workday e sono integrati con AD FS, si usa l'accesso federato per le app SaaS.

La maggior parte delle applicazioni SaaS può essere configurata in Azure AD. Microsoft ha molte connessioni preconfigurate alle app SaaS nella  [raccolta di app](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)Azure AD , semplificando la transizione. Le applicazioni SAML 2.0 possono essere integrate con Azure AD tramite la raccolta di app Azure AD o [come applicazioni non della raccolta.](add-application-portal.md)

Le app che usano OAuth 2.0 o OpenID Connect possono essere integrate in modo analogo con Azure AD registrazioni [delle app.](../develop/quickstart-register-app.md) Le app che usano protocolli legacy possono [usare](application-proxy.md) Azure AD Application Proxy per l'autenticazione con Azure AD.

Per eventuali problemi relativi all'onboarding delle app SaaS, è possibile contattare l'alias di supporto per l'integrazione di applicazioni [SaaS](mailto:SaaSApplicationIntegrations@service.microsoft.com).

### <a name="saml-signing-certificates-for-sso"></a>Certificati di firma SAML per SSO

I certificati di firma sono una parte importante di qualsiasi distribuzione SSO. Azure AD crea i certificati di firma per stabilire l'accesso SSO federato basato su SAML alle applicazioni SaaS. Dopo aver aggiunto le applicazioni della raccolta o non della raccolta, si configurerà l'applicazione aggiunta usando l'opzione SSO federato. Vedere [Gestire i certificati per l'accesso Single Sign-On federato in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="saml-token-encryption"></a>Crittografia del token SAML

Sia AD FS che Azure AD la crittografia dei token, ovvero la possibilità di crittografare le asserzioni di sicurezza SAML che passano alle applicazioni. Le asserzioni vengono crittografate con una chiave pubblica e decrittografate dall'applicazione ricevente con la chiave privata corrispondente. Quando si configura la crittografia dei token, si caricano i file di certificato X.509 per fornire le chiavi pubbliche.

Per informazioni sulla Azure AD di token SAML e su come [configurarla,](howto-saml-token-encryption.md)vedere Procedura: Configurare la Azure AD di token SAML.  

> [!NOTE]
> La crittografia dei token è una funzionalità Premium di Azure Active Directory (Azure AD). Per altre informazioni sulle edizioni, le funzionalità e i prezzi di Azure AD, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>App e configurazioni che possono essere spostate oggi

Attualmente è possibile spostare facilmente le app SAML 2.0 che usano il set standard di elementi di configurazione e attestazioni. Questi elementi standard sono:

* Nome entità utente
* Indirizzo di posta elettronica
* Nome specificato
* Surname
* Un attributo alternativo come **NameID** SAML, ad esempio l'attributo Mail, il prefisso di posta elettronica, l'ID dipendente o gli attributi dell'estensione 1-15 di Azure AD oppure l'attributo **SamAccountName** locale. Per altre informazioni, vedere [Modifica dell'attestazione NameIdentifier](../develop/active-directory-saml-claims-customization.md).
* Attestazioni personalizzate.

Di seguito sono necessari passaggi di configurazione aggiuntivi per eseguire la migrazione Azure AD:

* Regole di autorizzazione personalizzate o di autenticazione a più fattori (MFA) in AD FS. È possibile configurarli usando la [Azure AD di accesso](../conditional-access/overview.md) condizionale.
* App con più endpoint url di risposta. È possibile configurarli in Azure AD tramite PowerShell o l portale di Azure intervaglia.
* App WS-Federation come le app SharePoint che richiedono token SAML versione 1.1, È possibile configurarli manualmente usando PowerShell. È anche possibile aggiungere un modello generico pre-integrato per le applicazioni SharePoint e SAML 1.1 dalla raccolta. È supportato il protocollo SAML 2.0.
* Il rilascio di attestazioni complesse trasforma le regole. Per informazioni sui mapping di attestazioni supportati, vedere:
   *  [Mapping delle attestazioni in Azure Active Directory](../develop/active-directory-claims-mapping.md).
   * [Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>App e configurazioni attualmente non supportate in Azure AD

Attualmente non è possibile eseguire la migrazione delle app che richiedono determinate funzionalità.

#### <a name="protocol-capabilities"></a>Funzionalità del protocollo

Attualmente non è possibile eseguire la migrazione delle app che richiedono le funzionalità di protocollo seguenti:

* Supporto per il modello WS-Trust ActAs
* Risoluzione artefatto SAML
* Verifica della firma delle richieste SAML firmate
  > [!Note]
  > Le richieste firmate vengono accettate, ma la firma non viene verificata.

  Dato che Azure AD il token viene restituito solo agli endpoint preconfigurati nell'applicazione, la verifica della firma probabilmente non è necessaria nella maggior parte dei casi.

#### <a name="claims-in-token-capabilities"></a>Attestazioni nelle funzionalità del token

Attualmente non è possibile eseguire la migrazione delle app che richiedono le attestazioni seguenti nelle funzionalità del token.

* Le attestazioni provenienti da archivi di attributi diversi dalla directory Azure AD, a meno che i dati non vengano sincronizzati con Azure AD. Per altre informazioni, vedere la panoramica [dell'API Azure AD di sincronizzazione.](/graph/api/resources/synchronization-overview)
* Rilascio di attributi a più valori di directory. Ad esempio, non è possibile rilasciare un'attestazione multivalore per gli indirizzi proxy in questo momento.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Eseguire il mapping delle impostazioni dell'app AD FS a Azure AD

La migrazione richiede la valutazione del modo in cui l'applicazione è configurata in locale e quindi il mapping di tale configurazione Azure AD. AD FS e Azure AD presentano un funzionamento simile, di conseguenza i concetti relativi alla configurazione degli identificatori e degli URL di accesso, disconnessione e trust si applicano in entrambi i casi. Documentare AD FS di configurazione delle applicazioni in modo che sia possibile configurarle facilmente in Azure AD.

### <a name="map-app-configuration-settings"></a>Eseguire il mapping delle impostazioni di configurazione dell'app

La tabella seguente descrive alcuni dei mapping più comuni delle impostazioni tra un'AD FS attendibilità della relying party Azure AD'applicazione aziendale:

* AD FS: trovare l'impostazione nel AD FS attendibilità della relying party per l'app. Fare clic con il pulsante destro del relying party e scegliere Proprietà.
* Azure AD: l'impostazione viene configurata [all'interno portale di Azure](https://portal.azure.com/) nelle proprietà SSO di ogni applicazione.

| Impostazione di configurazione| AD FS| Come configurare in Azure AD| Token SAML |
| - | - | - | - |
| **URL di accesso dell'app** <p>URL per l'accesso dell'utente all'app in un flusso SAML avviato da un provider di servizi.| N/D| Aprire Configurazione SAML di base dall'accesso basato su SAML| N/D |
| **URL di risposta dell'app** <p>URL dell'app dal punto di vista del provider di identità (IdP). Il provider di identità invia qui l'utente e il token dopo che l'utente ha eseguito l'accesso al provider di identità.  Questo è noto anche come **endpoint consumer di asserzione SAML.**| Selezionare la **scheda Endpoint**| Aprire Configurazione SAML di base dall'accesso basato su SAML| Elemento destination nel token SAML. Valore di esempio: `https://contoso.my.salesforce.com` |
| **URL di disconnessione dell'app** <p>Si tratta dell'URL a cui vengono inviate le richieste di pulizia di disconnessione quando un utente si disconnette da un'app. Il provider di identità invia la richiesta di disconnessione dell'utente anche da tutte le altre app.| Selezionare la **scheda Endpoint**| Aprire Configurazione SAML di base dall'accesso basato su SAML| N/D |
| **Identificatore dell'app** <p>Si tratta dell'identificatore dell'app dal punto di vista del provider di identità. Come identificatore viene usato spesso il valore dell'URL di accesso, ma non sempre.  A volte l'app lo chiama "ID entità".| Selezionare la **scheda** Identificatori|Aprire Configurazione SAML di base dall'accesso basato su SAML| Esegue il mapping **all'elemento** Audience nel token SAML. |
| **Metadati di federazione dell'app** <p>Questo è il percorso dei metadati di federazione dell'app. Viene usata dal provider di identità per aggiornare automaticamente specifiche impostazioni di configurazione come gli endpoint o i certificati di crittografia.| Selezionare la **scheda** Monitoraggio| N/D. Azure AD non supporta direttamente l'utilizzo dei metadati di federazione dell'applicazione. È possibile importare manualmente i metadati della federazione.| N/D |
| **Identificatore utente/ID nome** <p>Attributo usato per indicare in modo univoco l'identità utente da Azure AD o AD FS all'app.  Questo attributo è in genere l'UPN o l'indirizzo di posta elettronica dell'utente.| Regole attestazione. Nella maggior parte dei casi, la regola attestazione esega un'attestazione con un tipo che termina con **NameIdentifier**.| È possibile trovare l'identificatore sotto l'intestazione **Attributi utente e attestazioni**. Per impostazione predefinita, viene usato l'UPN| Esegue il mapping **all'elemento NameID** nel token SAML. |
| **Altre attestazioni** <p>Esempi di altre informazioni sull'attestazione comunemente inviate dal provider di identità all'app includono il nome, il cognome, l'indirizzo di posta elettronica e l'appartenenza ai gruppi.| In AD FS sono riportate come altre regole attestazioni per la relying party.| È possibile trovare l'identificatore sotto l'intestazione **Attributi utente & attestazioni**. Selezionare **Visualizza e modifica tutti gli altri attributi utente**.| N/D |

### <a name="map-identity-provider-idp-settings"></a>Eseguire il mapping delle impostazioni del provider di identità (IdP)

Configurare le applicazioni in modo che puntino Azure AD o AD FS per l'accesso SSO. In questo caso, ci si concentra sulle app SaaS che usano il protocollo SAML. Tuttavia, questo concetto si estende anche alle app line-of-business personalizzate.

> [!NOTE]
> I valori di configurazione per Azure AD seguono il modello in cui l'ID tenant di Azure sostituisce {tenant-id} e l'ID applicazione sostituisce {application-id}. Queste informazioni sono disponibili nel [portale di Azure](https://portal.azure.com/) in **Azure Active Directory > proprietà**:

* Selezionare ID directory per visualizzare l'ID tenant.
* Selezionare ID applicazione per visualizzare l'ID applicazione.

 A livello di alto livello, eseguire il mapping dei seguenti elementi di configurazione delle app SaaS chiave Azure AD.

| Elemento| Valore di configurazione |
| - | - |
| Autorità emittente del provider di identità| https: \/ /sts.windows.net/{id-tenant}/ |
| URL di accesso del provider di identità| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| URL di disconnessione del provider di identità| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Percorso dei metadati della federazione| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>Eseguire il mapping delle impostazioni SSO per le app SaaS

Le app SaaS devono sapere dove inviare le richieste di autenticazione e come convalidare i token ricevuti. La tabella seguente descrive gli elementi per configurare le impostazioni SSO nell'app e i relativi valori o posizioni all'interno AD FS e Azure AD

| Impostazione di configurazione| AD FS| Come configurare in Azure AD |
| - | - | - |
| **URL di accesso IdP** <p>URL di accesso del provider di identità dal punto di vista dell'app (dove l'utente viene reindirizzato per l'accesso).| L AD FS'URL di accesso è AD FS nome del servizio federativo seguito da "/adfs/ls/". <p>ad esempio `https://fs.contoso.com/adfs/ls/`| Sostituire {tenant-id} con l'ID tenant. <p> Per le app che usano il protocollo SAML-P: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Per le app che usano il WS-Federation seguente: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **URL di disconnessione IdP**<p>URL di disconnessione del provider di identità dal punto di vista dell'app (in cui l'utente viene reindirizzato quando sceglie di disconnettersi dall'app).| L'URL di disconnessione è lo stesso dell'URL di accesso o lo stesso URL con "wa=wsignout1.0" aggiunto. ad esempio `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Sostituire {tenant-id} con l'ID tenant.<p>Per le app che usano il protocollo SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Per le app che usano il WS-Federation seguente: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificato di firma del token**<p>Il provider di identità usa la chiave privata del certificato per firmare i token emessi. Verifica che il token provenga dallo stesso provider di identità per cui è stato configurato il trust nell'app.| Il certificato per la firma di token di AD FS si trova in **Certificati** in Gestione AD FS.| Trovarlo nell'portale di Azure nelle proprietà di **Single Sign-On** dell'applicazione sotto l'intestazione **SAML Signing Certificate**. da dove può essere scaricato per il caricamento nell'app.  <p>Se l'applicazione ha più di un certificato, è possibile trovare tutti i certificati nel file XML dei metadati della federazione. |
| **Identificatore/"autorità di emittente"**<p>Identificatore del provider di identità dal punto di vista dell'app (talvolta denominato "ID autorità di emittente").<p>Nel token SAML il valore viene visualizzato come elemento Issuer.| L'identificatore per AD FS è in genere l'identificatore del servizio federativo in Gestione AD FS in **> Modifica Servizio federativo proprietà**. ad esempio `http://fs.contoso.com/adfs/services/trust`| Sostituire {tenant-id} con l'ID tenant.<p>https: \/ /sts.windows.net/{tenant-id}/ |
| **Metadati della federazione IdP**<p>Percorso dei metadati di federazione disponibili pubblicamente del provider di identità. Alcune app usano i metadati di federazione come alternativa alla configurazione di URL, identificatore e certificato per la firma di token eseguita singolarmente dall'amministratore.| Trovare l'URL AD FS metadati della federazione in AD FS Management in Endpoint > servizio > **metadati > tipo:** Metadati federazione . ad esempio `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Il valore corrispondente per Azure AD segue il modello [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Sostituire {TenantDomainName} con il nome del tenant nel formato "contoso.onmicrosoft.com".   <p>Per altre informazioni, vedere [Metadati della federazione](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Rappresentare AD FS criteri di sicurezza in Azure AD

Quando si sposta l'autenticazione dell'app Azure AD, creare mapping dai criteri di sicurezza esistenti alle varianti equivalenti o alternative disponibili in Azure AD. Assicurandosi che questi mapping possano essere esequiti nel soddisfare gli standard di sicurezza richiesti dai proprietari delle app, il resto della migrazione delle app risulta notevolmente più semplice.

Per ogni esempio di regola, viene illustrato l'aspetto della regola in AD FS, il codice equivalente del linguaggio delle regole AD FS e come viene eseguito il mapping a Azure AD.

### <a name="map-authorization-rules"></a>Eseguire il mapping delle regole di autorizzazione

Di seguito sono riportati esempi di vari tipi di regole di autorizzazione in AD FS e come è possibile eseguire il mapping a Azure AD.

#### <a name="example-1-permit-access-to-all-users"></a>Esempio 1: Consentire l'accesso a tutti gli utenti

Consentire l'accesso a tutti gli utenti in AD FS:

  ![Screenshot che mostra la finestra di dialogo Set up Single Sign-On with SAML (Configura single Sign-On con SAML).](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Viene eseguito il mapping Azure AD in uno dei modi seguenti:

1. Impostare **Assegnazione utente obbligatoria** su **No.**

    ![modificare i criteri di controllo di accesso per le app SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > **L'impostazione assegnazione utente obbligatoria** su **Sì** richiede che gli utenti siano assegnati all'applicazione per ottenere l'accesso. Se impostata su **No,** tutti gli utenti hanno accesso. Questa opzione non controlla ciò che gli utenti visualizzano **nell'App personali** esperienza.

1. Nella scheda **Utenti e gruppi assegnare** l'applicazione al **gruppo automatico Tutti gli** utenti. È necessario [abilitare Gruppi](../enterprise-users/groups-create-rule.md) dinamici nel tenant Azure AD per la disponibilità del gruppo **predefinito Tutti** gli utenti.

    ![App SaaS personali in Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>Esempio 2: Consentire un gruppo in modo esplicito

Autorizzazione esplicita del gruppo in AD FS:

  ![Screenshot che mostra la finestra di dialogo Modifica regola per la regola di attestazione Consenti amministratori di dominio.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

Per eseguire il mapping di questa regola Azure AD:

1. Nel [portale di Azure](https://portal.azure.com/)creare [un gruppo di](../fundamentals/active-directory-groups-create-azure-portal.md) utenti che corrisponde al gruppo di utenti da AD FS.
1. Assegnare le autorizzazioni dell'app al gruppo:

    ![Aggiungere un'assegnazione ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>Esempio 3: Autorizzare un utente specifico

Autorizzazione utente esplicita in AD FS:

  ![Screenshot che mostra la finestra di dialogo Modifica regola per consenti una regola attestazione utente specifica con un tipo di attestazione in ingresso S I D primario.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Per eseguire il mapping di questa regola Azure AD:

* Nella finestra [portale di Azure](https://portal.azure.com/)aggiungere un utente all'app tramite la scheda Aggiungi assegnazione dell'app, come illustrato di seguito:

    ![App SaaS personali in Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Eseguire il mapping delle regole di autenticazione a più fattori

Una distribuzione locale di [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) e AD FS funziona ancora dopo la migrazione perché si è federati con AD FS. È tuttavia consigliabile eseguire la migrazione alle funzionalità MFA incorporate di Azure collegate ai flussi di lavoro di Azure AD di accesso condizionale di Azure.

Di seguito sono riportati esempi di tipi di regole di autenticazione a più fattori in AD FS e come è possibile eseguire il mapping di Azure AD in base a condizioni diverse.

Impostazioni delle regole dell'autenticazione a più fattori AD FS:

  ![Screenshot che mostra le condizioni per Azure D nella portale di Azure.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Esempio 1: Applicare l'autenticazione a più fattori in base a utenti/gruppi

Il selettore utenti/gruppi è una regola che consente di applicare l'autenticazione a più fattori per singolo gruppo (SID gruppo) o per utente (SID primario). Oltre alle assegnazioni di utenti/gruppi, tutte le caselle di controllo aggiuntive nell'interfaccia utente di configurazione di AD FS MFA funzionano come regole aggiuntive che vengono valutate dopo l'applicazione della regola utenti/gruppi.

Specificare le regole di autenticazione a più fattori per un utente o un gruppo in Azure AD:

1. Creare un [nuovo criterio di accesso condizionale.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Selezionare **Assegnazioni**. Aggiungere gli utenti o i gruppi per i quali si vuole applicare l'autenticazione a più fattori.
1. Configurare le **opzioni di Controlli** di accesso come illustrato di seguito:

    ‎![Screenshot che mostra il riquadro Concedi in cui è possibile concedere l'accesso.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Esempio 2: Applicare l'autenticazione a più fattori per i dispositivi non registrati

Specificare le regole di autenticazione a più fattori per i dispositivi non registrati in Azure AD:

1. Creare un [nuovo criterio di accesso condizionale.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Impostare **Assegnazioni su** Tutti **gli utenti.**
1. Configurare le **opzioni di Controlli** di accesso come illustrato di seguito:

    ![Screenshot che mostra il riquadro Concedi in cui è possibile concedere l'accesso e specificare altre restrizioni.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

Quando si  imposta l'opzione Per più controlli su Richiedi uno dei controlli **selezionati,** significa che se una delle condizioni specificate dalla casella di controllo viene soddisfatta dall'utente, all'utente viene concesso l'accesso all'app.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Esempio 3: Applicare l'autenticazione a più fattori in base alla posizione

Specificare le regole di autenticazione a più fattori in base alla posizione dell'utente in Azure AD:

1. Creare un [nuovo criterio di accesso condizionale.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Impostare **Assegnazioni su** Tutti **gli utenti.**
1. [Configurare percorsi denominati in Azure AD](../reports-monitoring/quickstart-configure-named-locations.md). In caso contrario, la federazione dall'interno della rete aziendale è attendibile.
1. Configurare le **regole condizioni per** specificare i percorsi per cui si vuole applicare l'autenticazione a più fattori.

    ![Screenshot che mostra il riquadro Località per le regole delle condizioni.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Configurare le **opzioni di Controllo di** accesso come illustrato di seguito:

    ![Eseguire il mapping dei criteri di controllo di accesso](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Eseguire il mapping degli attributi emit come regola attestazioni

Creare attributi come regola attestazioni in AD FS:

  ![Screenshot che mostra la finestra di dialogo Modifica regola per Generare attributi come attestazioni.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

Per eseguire il mapping della regola Azure AD:

1. Nel [portale di Azure](https://portal.azure.com/)selezionare Applicazioni **aziendali e** quindi Single **Sign-On** per visualizzare la configurazione dell'accesso basato su SAML:

    ![Screenshot che mostra la pagina Single Sign-On per l'applicazione enterprise.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Selezionare **Modifica** (evidenziato) per modificare gli attributi:

    ![Questa è la pagina per modificare gli attributi utente e le attestazioni](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Eseguire il mapping dei criteri di controllo di accesso predefiniti

Criteri di controllo di accesso predefiniti in AD FS 2016:

  ![Azure AD controllo di accesso integrato](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Per implementare criteri predefiniti in Azure AD, [](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) usare un nuovo criterio di accesso condizionale e configurare i controlli di accesso oppure usare progettazione criteri personalizzati in AD FS 2016 per configurare i criteri di controllo di accesso. L'Editor regole include un elenco completo delle opzioni Consenti e Tranne che consentono di eseguire tutti i tipi di permutazioni.

  ![Azure AD di controllo di accesso](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

In questa tabella sono elencate alcune opzioni utili per consenti e tranne e il modo in cui ne viene Azure AD.

| Opzione | Come configurare l'opzione Consenti in Azure AD?| Come configurare l'opzione Except in Azure AD? |
| - | - | - |
| Da specifico rete| Esegue il mapping [alla posizione denominata](../reports-monitoring/quickstart-configure-named-locations.md) in Azure AD| Usare **l'opzione** Escludi per [i percorsi attendibili](../conditional-access/location-condition.md) |
| Da specifico gruppi| [Impostare un'assegnazione di utenti/gruppi](assign-user-or-group-access-portal.md)| Usare **l'opzione** Escludi in Utenti e gruppi |
| Da dispositivi con livello di attendibilità specifico| Impostare questa opzione dal **controllo Stato** del dispositivo in Assegnazioni -> condizioni| Usare **l'opzione** Escludi in Condizione di stato del dispositivo e Includi **tutti i dispositivi** |
| Con attestazioni specifiche nella richiesta| Non è possibile eseguire la migrazione di questa impostazione| Non è possibile eseguire la migrazione di questa impostazione |

Di seguito è riportato un esempio di come configurare l'opzione Exclude per i percorsi attendibili nel portale di Azure:

  ![Screenshot del mapping dei criteri di controllo di accesso](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Eseguire la transizione degli utenti AD FS a Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Sincronizzare AD FS gruppi in Azure AD

Quando si esegue il mapping delle regole di autorizzazione, le app che eseguono l'AD FS possono usare i gruppi di Active Directory per le autorizzazioni. In tal caso, usare Azure AD Connect per [sincronizzare](https://go.microsoft.com/fwlink/?LinkId=615771) questi gruppi con Azure AD prima di eseguire la migrazione delle applicazioni. Assicurarsi di verificare i gruppi e l'appartenenza prima della migrazione in modo da poter concedere l'accesso agli stessi utenti quando viene eseguita la migrazione dell'applicazione.

Per altre informazioni, vedere [Prerequisiti per l'uso degli attributi del gruppo sincronizzati da Active Directory.](../hybrid/how-to-connect-fed-group-claims.md)

### <a name="set-up-user-self-provisioning"></a>Configurare il provisioning automatico degli utenti

Alcune applicazioni SaaS supportano la possibilità di effettuare il provisioning degli utenti al primo accesso all'applicazione. In Azure AD, il provisioning delle app si riferisce alla creazione automatica di identità utente e ruoli nel cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) a cui gli utenti devono accedere. Gli utenti di cui viene eseguita la migrazione hanno già un account nell'applicazione SaaS. È necessario effettuare il provisioning di tutti i nuovi utenti aggiunti dopo la migrazione. Testare [il provisioning di app SaaS](../app-provisioning/user-provisioning.md) dopo la migrazione dell'applicazione.

### <a name="sync-external-users-in-azure-ad"></a>Sincronizzare gli utenti esterni in Azure AD

Gli utenti esterni esistenti possono essere impostati in due modi AD FS:

* **Utenti esterni con un account locale all'interno** dell'organizzazione: questi account continuano a essere utilizzati nello stesso modo in cui funzionano gli account utente interni. Questi account utente esterni hanno un nome di principio all'interno dell'organizzazione, anche se il messaggio di posta elettronica dell'account può puntare esternamente. Quando si procede con la migrazione, è possibile sfruttare i vantaggi offerti da [Azure AD B2B](../external-identities/what-is-b2b.md) eseguendo la migrazione di questi utenti per usare la propria identità aziendale quando tale identità è disponibile. In questo modo si semplifica il processo di accesso per tali utenti, poiché spesso viene eseguito l'accesso con il proprio account di accesso aziendale. Anche l'amministrazione dell'organizzazione è più semplice, in quanto non è necessario gestire gli account per gli utenti esterni.
* **Identità esterne federate:** se attualmente si sta federatendo con un'organizzazione esterna, è necessario adottare alcuni approcci:
  * [Aggiungere Azure Active Directory utenti di collaborazione B2B nel portale di Azure](../external-identities/add-users-administrator.md). È possibile inviare in modo proattivo gli inviti di collaborazione B2B dal portale amministrativo di Azure AD all'organizzazione partner per i singoli membri per continuare a usare le app e gli asset a cui sono stati usati.
  * Creare un flusso di lavoro di iscrizione [B2B self-service](../external-identities/self-service-portal.md) che genera una richiesta per singoli utenti nell'organizzazione partner usando l'API di invito B2B.

Indipendentemente da come sono configurati gli utenti esterni esistenti, probabilmente hanno autorizzazioni associate al proprio account, nell'appartenenza a gruppi o in autorizzazioni specifiche. Valutare se è necessario eseguire la migrazione o la pulizia di queste autorizzazioni. Gli account all'interno dell'organizzazione che rappresentano un utente esterno devono essere disabilitati dopo la migrazione dell'utente a un'identità esterna. Il processo di migrazione deve essere illustrato con i partner commerciali, in quanto potrebbe verificarsi un'interruzione nella capacità di connettersi alle risorse.

## <a name="migrate-and-test-your-apps"></a>Eseguire la migrazione e testare le app

Seguire il processo di migrazione descritto in questo articolo. Passare quindi al [portale di Azure](https://aad.portal.azure.com/) per verificare se la migrazione è stata completata.

Seguire queste istruzioni:

1. Selezionare **Applicazioni aziendali** Tutte le  >  **applicazioni** e trovare l'app nell'elenco.
1. Selezionare **Gestisci**  >  **utenti e gruppi** per assegnare almeno un utente o un gruppo all'app.
1. Selezionare **Gestisci accesso**  >  **condizionale.** Esaminare l'elenco dei criteri e assicurarsi di non bloccare l'accesso all'applicazione con criteri [di accesso condizionale.](../conditional-access/overview.md)

A seconda di come si configura l'app, verificare che l'accesso Single Sign-On funzioni correttamente.

| Tipo di autenticazione| Test |
| :- | :- |
| OAuth/OpenID Connect| Selezionare **Applicazioni aziendali > autorizzazioni** e assicurarsi di avere dato il consenso all'applicazione nelle impostazioni utente per l'app.|
| SSO basato su SAML | Usare il [pulsante Test SAML Settings (Testa](debug-saml-sso-issues.md) impostazioni SAML) disponibile in Single **Sign-On.** |
| Password-Based SSO |  Scaricare e installare [l'estensione per l'accesso sicuro a MyApps](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [](../user-help/my-apps-portal-end-user-access.md). Questa estensione consente di avviare qualsiasi app cloud dell'organizzazione che richiede l'uso di un processo SSO. |
| Proxy dell'applicazione | Verificare che il connettore sia in esecuzione e assegnato all'applicazione. Per ulteriore [assistenza, Application Proxy guida alla risoluzione dei](application-proxy-troubleshoot.md) problemi. |

> [!NOTE]
> I cookie dell'ambiente AD FS precedente vengono mantenuti nei computer utente. Questi cookie potrebbero causare problemi con la migrazione, in quanto gli utenti potrebbero essere indirizzati all'ambiente di accesso AD FS precedente rispetto al nuovo Azure AD login. Potrebbe essere necessario cancellare i cookie del browser utente manualmente o usando uno script. È anche possibile usare il System Center Gestione configurazione o una piattaforma simile.

### <a name="troubleshoot"></a>Risolvere problemi

Se si verificano errori durante il test delle applicazioni di cui è stata eseguita la migrazione, la risoluzione dei problemi può essere il primo passaggio prima di eseguire il rollback alle relying party AD FS esistenti. Vedere [Come eseguire il debug dell'accesso Single Sign-On](debug-saml-sso-issues.md)basato su SAML alle applicazioni in Azure Active Directory .

### <a name="rollback-migration"></a>Eseguire il rollback della migrazione

Se la migrazione non riesce, è consigliabile lasciare le relying party esistenti nei server AD FS e rimuovere l'accesso alle relying party. Ciò consente un fallback rapido, se necessario durante la distribuzione.

### <a name="employee-communication"></a>Comunicazione dei dipendenti

Anche se la finestra di interruzione pianificata può essere minima, è comunque consigliabile pianificare la comunicazione proattiva di questi tempi ai dipendenti passando dal AD FS al Azure AD. Assicurarsi che l'esperienza dell'app abbia un pulsante di feedback o puntatori al supporto per i problemi.

Al termine della distribuzione, è possibile informare gli utenti della corretta distribuzione e ricordare loro i passaggi da eseguire.

* Indicare agli utenti di [usare](https://myapps.microsoft.com) App personali accedere a tutte le applicazioni di cui è stata eseguita la migrazione.
* Ricordare agli utenti che potrebbe essere necessario aggiornare le impostazioni dell'autenticazione a più fattori.
* Se Self-Service la reimpostazione della password, gli utenti potrebbero dover aggiornare o verificare i metodi di autenticazione. Vedere [Modelli di comunicazione](https://aka.ms/mfatemplates) [dell'utente finale MFA e SSPR.](https://aka.ms/ssprtemplates)

### <a name="external-user-communication"></a>Comunicazione con utenti esterni

Questo gruppo di utenti è in genere il più critico in caso di problemi. Ciò vale soprattutto se il proprio stato di sicurezza determina un set diverso di regole di accesso condizionale o profili di rischio per i partner esterni. Assicurarsi che i partner esterni siano a conoscenza della pianificazione della migrazione al cloud e di avere un intervallo di tempo durante il quale sono invitati a partecipare a una distribuzione pilota che testa tutti i flussi univoci per la collaborazione esterna. Infine, assicurarsi che sia possibile accedere al supporto in caso di problemi.

## <a name="next-steps"></a>Passaggi successivi

* Leggere  [Migrazione dell'autenticazione dell'applicazione Azure AD](https://aka.ms/migrateapps/whitepaper).
* Configurare [l'accesso condizionale e](../conditional-access/overview.md) [MFA.](../authentication/concept-mfa-howitworks.md)
* Provare un esempio di codice per passaggio:[AD FS per Azure AD playbook](https://aka.ms/adfsplaybook)di migrazione delle applicazioni per gli sviluppatori .
