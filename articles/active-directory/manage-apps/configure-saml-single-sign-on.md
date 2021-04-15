---
title: Informazioni sull'accesso Single Sign-On (SSO) basato su SAML per le app Azure Active Directory
description: Informazioni sull'accesso Single Sign-On (SSO) basato su SAML per le app in Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b7468f33c75dd58e70c344f3ef19c51e220a7abb
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374489"
---
# <a name="understand-saml-based-single-sign-on"></a>Informazioni sull'accesso Single Sign-On basato su SAML

Nella serie [di avvio rapido sulla](view-applications-portal.md) gestione delle applicazioni si è appreso come usare Azure AD come provider di identità (IdP) per un'applicazione. Questo articolo illustra in dettaglio l'opzione basata su SAML per l'accesso Single Sign-On. 


## <a name="before-you-begin"></a>Prima di iniziare

L Azure AD come provider di identità (IdP) e la configurazione dell'accesso Single Sign-On (SSO) possono essere semplici o complessi a seconda dell'applicazione in uso. Alcune applicazioni possono essere configurate con poche azioni. Altri richiedono una configurazione approfondita. Per aumentare rapidamente le conoscenze, vedere la [serie di avvio rapido](view-applications-portal.md) sulla gestione delle applicazioni. Se l'applicazione che si sta aggiungendo è semplice, probabilmente non è necessario leggere questo articolo. Se l'applicazione che si sta aggiungendo richiede una configurazione personalizzata per l'accesso SSO basato su SAML, questo articolo fa per te.

Nella [serie di avvio rapido](add-application-portal-setup-sso.md)è presente un articolo sulla configurazione dell'accesso Single Sign-On. In questo articolo si apprenderà come accedere alla pagina di configurazione SAML per un'app. La pagina di configurazione SAML include cinque sezioni. Queste sezioni sono descritte in dettaglio in questo articolo.

> [!IMPORTANT] 
> Esistono alcuni scenari in cui **l'opzione Single Sign-On** non sarà presente nella navigazione per un'applicazione nelle **applicazioni aziendali**. 
>
> Se l'applicazione è stata registrata **Registrazioni app,** la funzionalità Single Sign-On è configurata per l'uso di OAuth OIDC per impostazione predefinita. In questo caso, **l'opzione Single Sign-On** non verrà mostrata nel riquadro di spostamento in **Applicazioni aziendali**. Quando si usa **Registrazioni app** per aggiungere l'app personalizzata, si configurano le opzioni nel file manifesto. Per altre informazioni sul file manifesto, vedere Azure Active Directory [manifesto dell'app.](../develop/reference-app-manifest.md) Per altre informazioni sugli standard SSO, vedere [Autenticazione e autorizzazione con Microsoft Identity Platform.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Altri scenari in cui **l'accesso Single Sign-On** non sarà presente nella navigazione includono quando un'applicazione è ospitata in un altro tenant o se l'account non dispone delle autorizzazioni necessarie (amministratore globale, amministratore di applicazioni cloud, amministratore dell'applicazione o proprietario dell'entità servizio). Le autorizzazioni possono anche causare uno scenario in cui è possibile aprire **l'accesso Single Sign-On,** ma non sarà possibile salvarlo. Per altre informazioni sui Azure AD amministrativi, vedere ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Configurazione SAML di base

È necessario ottenere i valori necessari dal fornitore dell'applicazione. È possibile immettere manualmente i valori o caricare un file di metadati per estrarre il valore dei campi.

> [!TIP]
> Molte app sono già state preconfigurato per l'utilizzo con Azure AD. Queste app sono elencate nella raccolta di app che è possibile esplorare quando si aggiunge un'app al tenant Azure AD. La [serie di guide introduttive](add-application-portal-setup-sso.md) illustra il processo. Per le app nella raccolta sono disponibili istruzioni dettagliate. Per accedere ai passaggi, è possibile fare clic sul collegamento nella pagina di configurazione SAML per l'app come descritto nella serie di guide introduttive oppure è possibile esplorare un elenco di tutte le esercitazioni sulla configurazione delle app in Esercitazioni sulla configurazione delle [app SaaS.](../saas-apps/tutorial-list.md)

| Impostazione della configurazione SAML di base | SSO avviato da provider di servizi | SSO avviato da IdP | Descrizione |
|:--|:--|:--|:--|
| **Identificatore (ID entità)** | Obbligatoria per alcune app | Obbligatoria per alcune app | Identifica l'applicazione in modo univoco. Azure AD restituisce l'identificatore all'applicazione come parametro Audience del token SAML. L'applicazione dovrebbe convalidarlo. Questo valore viene inoltre visualizzato come ID entità in tutti i metadati SAML forniti dall'applicazione. Immettere un URL con il modello seguente: "https://<subdomain>.contoso.com" *È possibile trovare questo valore come elemento **Autorità di certificazione** nella richiesta **AuthRequest** (SAML) inviata dall'applicazione.* |
| **URL di risposta** | Obbligatoria | Obbligatoria | Specifica dove l'applicazione prevede di ricevere il token SAML. L'URL di risposta è denominato anche URL del servizio consumer di asserzione. È possibile usare i campi URL di risposta aggiuntivi per specificare più URL di risposta. Ad esempio, potrebbero essere necessari URL di risposta aggiuntivi per più sottodomini. In alternativa, a scopo di test è possibile specificare contemporaneamente più URL di risposta (URL pubblici e host locale). |
| **URL di accesso** | Obbligatoria | Non specificare | Quando un utente apre questo URL, il provider di servizi esegue il reindirizzamento ad Azure AD per l'autenticazione e l'accesso dell'utente. Azure AD usa l'URL per avviare l'applicazione da Microsoft 365 o Azure AD App personali. Se vuoto, Azure AD esegue un accesso avviato dal provider di identità quando un utente avvia l'applicazione da Microsoft 365, Azure AD App personali o dall'URL SSO di Azure AD.|
| **Stato dell'inoltro** | Facoltativo | Facoltativo | Comunica all'applicazione dove reindirizzare l'utente al termine dell'autenticazione. In genere il valore è un URL valido per l'applicazione. Tuttavia, alcune applicazioni usano questo campo in modo diverso. Per altre informazioni, rivolgersi al fornitore dell'applicazione.
| **URL di disconnessione** | Facoltativo | Facoltativo | Usato per restituire una risposta di disconnessione SAML all'applicazione.

## <a name="user-attributes-and-claims"></a>Attributi utente e attestazioni 

Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia all'applicazione un token SAML contenente informazioni (o attestazioni) sull'utente, che lo identificano in modo univoco. Per impostazione predefinita sono inclusi il nome utente, l'indirizzo di posta elettronica, il nome e il cognome dell'utente. Potrebbe essere necessario personalizzare queste attestazioni se, ad esempio, l'applicazione richiede valori di attestazione specifici o un formato del **Nome** diverso dal nome utente. 

> [!IMPORTANT]
> Molte app sono già preconfigurato e nella raccolta di app e non è necessario preoccuparsi di impostare attestazioni di utenti e gruppi. La [serie di guide introduttive](add-application-portal.md) illustra come aggiungere e configurare le app.


Il **valore identificatore identificatore univoco dell'utente (ID nome)** è un'attestazione obbligatoria ed è importante. Il valore predefinito è *user.userprincipalname*. L'ID utente identifica in modo univoco ogni utente all'interno dell'applicazione. Se l'indirizzo di posta elettronica è sia il nome utente che l'identificatore univoco, ad esempio, impostare il valore su *user.mail*.

Per altre informazioni sulla personalizzazione delle attestazioni SAML, vedere Procedura: Personalizzare le attestazioni [rilasciate nel token SAML per le applicazioni aziendali.](../develop/active-directory-saml-claims-customization.md)

È possibile aggiungere nuove attestazioni. Per informazioni dettagliate, vedere [Aggiunta di](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) attestazioni specifiche dell'applicazione o per aggiungere attestazioni di gruppo, vedere Configurare attestazioni [di gruppo.](../hybrid/how-to-connect-fed-group-claims.md)


> [!NOTE]
> Per informazioni sugli altri modi disponibili per personalizzare il token SAML di Azure AD nell'applicazione, vedere le risorse seguenti.
>- Per creare ruoli personalizzati tramite il portale di Azure, vedere [Configurare le attestazioni dei ruoli](../develop/active-directory-enterprise-app-role-management.md).
>- Per personalizzare le attestazioni tramite PowerShell, vedere [Personalizzare le attestazioni - PowerShell](../develop/active-directory-claims-mapping.md).
>- Per modificare il manifesto dell'applicazione e configurare attestazioni facoltative per l'applicazione, vedere [Configurare le attestazioni facoltative](../develop/active-directory-optional-claims.md).
>- Per impostare i criteri di durata per i token di aggiornamento, i token di accesso, i token di sessione e i token ID, vedere [Configurare la durata dei token](../develop/active-directory-configurable-token-lifetimes.md). In alternativa, per limitare le sessioni di autenticazione tramite Accesso condizionale di Azure AD, vedere [Funzionalità di gestione delle sessioni di autenticazione](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="saml-signing-certificate"></a>Certificato di firma SAML

Azure AD usa un certificato per firmare i token SAML che invia all'applicazione. Questo certificato è necessario per configurare il trust tra Azure AD e l'applicazione. Per informazioni dettagliate sul formato del certificato, vedere la documentazione sul protocollo SAML dell'applicazione. Per altre informazioni, vedere [Gestione di certificati per l'accesso Single Sign-On federato](manage-certificates-for-federated-single-sign-on.md) e [Opzioni avanzate di firma del certificato nel token SAML](certificate-signing-options.md).

> [!IMPORTANT]
> Molte app sono già preconfigurati e nella raccolta di app e non è necessario approfondire i certificati. La [serie di avvio rapido](add-application-portal.md) illustra come aggiungere e configurare le app.

Da Azure AD è possibile scaricare il certificato attivo in formato Base64 o RAW direttamente dalla pagina principale **Configurare l'accesso Single Sign-On con SAML**. È anche possibile ottenere il certificato attivo scaricando il file XML dei metadati dell'applicazione o usando l'URL dei metadati di federazione dell'app. Per visualizzare, creare o scaricare i certificati (attivi o inattivi), seguire questa procedura.

Di seguito sono riportati alcuni aspetti comuni da verificare per verificare un certificato: 
   - *Data di scadenza corretta.* È possibile configurare la data di scadenza fino a un massimo di tre anni nel futuro.
   - *Stato attivo per il certificato giusto.* Se lo stato è **Inattivo**, modificarlo in **Attivo**. Per modificare lo stato, fare clic con il pulsante destro del mouse sulla riga del certificato e **scegliere Rendi attivo il certificato**.
   - *Opzione e algoritmo di firma corretti.*
   - *Indirizzi del messaggio di notifica corretti.* Quando la data del certificato attivo si avvicina alla scadenza, Azure AD invia una notifica all'indirizzo di posta elettronica configurato in questo campo.

A volte potrebbe essere necessario scaricare il certificato. Prestare attenzione alla posizione in cui viene salvato. Per scaricare il certificato, selezionare una delle opzioni per formato Base64, RAW o XML metadati federazione. Azure AD fornisce anche l'**URL dei metadati della federazione dell'app** tramite cui è possibile accedere ai metadati specifici dell'applicazione nel formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

> [!NOTE]
> L'applicazione deve essere in grado di gestire il marcatore dell'ordine dei byte presente nel codice XML sottoposto a rendering quando si usa https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . Il byte order mark è rappresentato come un carattere ASCII non stampabile »» e in Hex viene rappresentato come EF BB BF durante la revisione dei dati XML.

Per apportare modifiche al certificato, selezionare il pulsante Modifica. Nella pagina Certificato di firma SAML è possibile eseguire **diverse** operazioni:
   - Creare un nuovo certificato: selezionare **Nuovo certificato,** selezionare **La data di scadenza** e quindi selezionare **Salva**. Per attivare il certificato, selezionare il menu di scelta rapida **(...)** e scegliere **Rendi attivo il certificato**.
   - Caricare un certificato con la chiave privata e le credenziali pfx: selezionare **Importa certificato** e passare al certificato. Immettere la **Password PFX** e quindi selezionare **Aggiungi**.  
   - Configurare la firma avanzata del certificato. Per altre informazioni su queste opzioni, vedere [Opzioni avanzate per la firma dei certificati.](certificate-signing-options.md)
   - Inviare una notifica ad altri utenti quando il certificato attivo è prossimo alla data di scadenza: immettere gli indirizzi di posta elettronica nei **campi Indirizzi di posta elettronica di** notifica.

## <a name="set-up-the-application-to-use-azure-ad"></a>Configurare l'applicazione per l'uso di Azure AD

La **sezione \<applicationName> Configura** elenca i valori che devono essere configurati nell'applicazione in modo che usi Azure AD come provider di identità SAML. I valori vengono impostati nella pagina di configurazione del sito Web delle applicazioni. Ad esempio, se si configura GitHub, si passa al sito github.com e si impostano i valori. Se l'applicazione è già preconfigurato e nella raccolta Azure AD, è disponibile un collegamento a Visualizzare le **istruzioni dettagliate**. In caso contrario, sarà necessario trovare la documentazione per l'applicazione che si sta configurando. 

I **valori di URL** di accesso e **URL** di disconnessione vengono entrambi risolti nello stesso endpoint, ovvero l'endpoint di gestione delle richieste SAML per Azure AD tenant. 

**Identificatore Azure AD** è il valore di **Issuer** nel token SAML inviato all'applicazione.

## <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Dopo aver configurato l'applicazione per l'uso di Azure AD come provider di identità basato su SAML, è possibile testare le impostazioni per verificare che l'accesso Single Sign-On funzioni per l'account. 

Selezionare **Test** e quindi scegliere di eseguire il test con l'utente attualmente connesso o con un altro utente. 

Se l'accesso riesce, è possibile assegnare utenti e gruppi all'applicazione SAML. Congratulazioni!

Se viene visualizzato un messaggio di errore, completare la procedura seguente:

1. Copiare e incollare le specifiche nella casella **Qual è l'errore riscontrato?** .

    ![Ottieni procedure per la risoluzione](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selezionare **Ottieni procedure per la risoluzione**. Verranno visualizzate indicazioni relative alla causa radice e alla risoluzione.  In questo esempio, l'utente non è stato assegnato all'applicazione.

3. Leggere le linee guida per la risoluzione e quindi provare a risolvere il problema.

4. Eseguire di nuovo il test finché non viene completato correttamente.

Per altre informazioni, vedere [Eseguire il debug di Single Sign-On basato su SAML per applicazioni in Azure Active Directory](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Passaggi successivi

- [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
- [Assegnare utenti o gruppi all'applicazione](./assign-user-or-group-access-portal.md)
- [Configurare il provisioning automatico degli account utente](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Protocollo SAML per Single Sign-On](../develop/single-sign-on-saml-protocol.md)
