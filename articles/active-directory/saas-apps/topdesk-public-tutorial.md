---
title: 'Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Public |Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TOPdesk - Public.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 5d16fd87b01db69d3f55e22aad573b7847b9048c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518082"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Public

Questa esercitazione illustra come integrare TOPdesk - Public con Azure Active Directory (Azure AD). Integrando TOPdesk - Public con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a TOPdesk - Public.
* Abilitare gli utenti per l'accesso automatico a TOPdesk - Public con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* TOPdesk : sottoscrizione pubblica abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TOPdesk: Public supporta l'accesso SSO avviato da **SP.**

## <a name="add-topdesk---public-from-the-gallery"></a>Aggiungere TOPdesk - Public dalla raccolta

Per configurare l'integrazione di TOPdesk - Public in Azure AD, è necessario aggiungere TOPdesk - Public dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta digitare** **TOPdesk - Public** nella casella di ricerca.
1. Selezionare **TOPdesk - Public nel** pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>Configurare e testare Azure AD SSO for TOPdesk - Public

Configurare e testare Azure AD SSO con TOPdesk - Public usando un utente di test di **nome B.Simon**. Per il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in TOPdesk - Public.

Per configurare e testare Azure AD SSO con TOPdesk - Public, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare TOPdesk - SSO pubblico:](#configure-topdesk---public-sso)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. Creare l'utente di test di **[TOPdesk -](#create-topdesk---public-test-user)** Public: per avere una controparte di B.Simon in TOPdesk - Public collegata alla Azure AD dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina portale di Azure di integrazione dell'applicazione **TOPdesk -**  Public individuare la sezione Gestione e selezionare **Single Sign-On.**
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4.  Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    >[!NOTE]
    >Si otterrà il **file di metadati del provider di servizi** in base alla procedura descritta più avanti nella sezione **Configurare l'accesso Single Sign-On di TOPdesk - Public** dell'esercitazione.

    a. Fare clic su **Carica il file di metadati**.
    
    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione Configurazione SAML di base.

    d. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<companyname>.topdesk.net`

    e. Nella casella di testo **Identifier URL** (URL dell'identificatore) inserire l'URL dei metadati di TOPdesk ottenuto dalla configurazione TOPdesk. Il formato da usare è: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Se i valori di **Identificatore** e **URL di risposta** non vengono immessi automaticamente, è necessario specificarli manualmente. Per Identificatore, seguire il modello descritto sopra e ottenere il valore di URL di risposta come indicato più avanti nella sezione **Configurare l'accesso Single Sign-On di TOPdesk - Public** dell'esercitazione. Poiché il valore di **URL accesso** non è reale, è necessario aggiornarlo con l'effettivo URL di accesso. Per ottenere questo valore, contattare il [team di supporto clienti di TOPdesk - Public](https://help.topdesk.com/saas/enterprise/user/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura TOPdesk - Public** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Creare un utente di test di Azure AD 

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a TOPdesk - Public.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **TOPdesk - Public**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è &quot;Accesso predefinito&quot;.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name=&quot;configure-topdesk---public-sso&quot;></a>Configurare TOPdesk - SSO pubblico

1. Accedere al sito della società **TOPdesk - Public** come amministratore.

2. Scegliere **Settings** dal menu **TOPdesk**.
   
    ![Impostazioni](./media/topdesk-public-tutorial/menu.png &quot;Impostazioni")

3. Fare clic su **Login Settings**.
   
    ![Impostazioni di accesso](./media/topdesk-public-tutorial/login.png "Login Settings")

4. Espandere il menu **Login Settings** e quindi fare clic su **General**.
   
    ![Impostazioni generali](./media/topdesk-public-tutorial/general.png "Impostazioni generali")

5. Nell'area **Public** della sezione di configurazione **SAML login** seguire la procedura seguente:
   
    ![Impostazioni tecniche](./media/topdesk-public-tutorial/public.png "Technical Settings")
   
    a. Fare clic su **Download** per scaricare il file di metadati pubblici e salvarlo in locale nel computer.
   
    b. Aprire il file dei metadati scaricato e quindi individuare il nodo **AssertionConsumerService** .

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. Copiare il valore **AssertionConsumerService** e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base**.      
   
6. Per creare un file del certificato, seguire questa procedura:
    
    ![Certificate](./media/topdesk-public-tutorial/certificate-file.png "Certificato")
    
    a. Aprire il file di metadati scaricato dal portale di Azure.
    
    b. Espandere il nodo **RoleDescriptor** con **xsi:type** uguale a **fed:ApplicationServiceType**.
    
    c. Copiare il valore del nodo **X509Certificate**.
    
    d. Salvare il valore copiato di **X509Certificate** in un file locale nel computer.

7. Nella sezione **Public** fare clic su **Add**.
    
    ![Accesso SAML](./media/topdesk-public-tutorial/add.png "SAML login")

8. Nella pagina **SAML configuration assistant** eseguire la procedura seguente:
    
    ![Assistente alla configurazione SAML](./media/topdesk-public-tutorial/configuration.png "SAML configuration assistant")
    
    a. Per caricare il file di metadati scaricato dal portale di Azure, in **Metadati della federazione** fare clic su **Sfoglia**.

    b. Per caricare il file del certificato, in **Certificate (RSA)** fare clic su **Browse**.

    c. Per caricare il file del logo ottenuto dal team di supporto di TOPdesk, in **Logo icon** fare clic su **Browse**.

    d. Nella casella di testo **User name attribute** (Attributo nome utente) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Nella casella di testo **Display name** digitare un nome per la configurazione.

    f. Fare clic su **Salva**.

### <a name="create-topdesk---public-test-user"></a>Creare un utente di test di TOPdesk - Public

Per consentire agli utenti di Azure AD di accedere a TOPdesk - Public, è necessario effettuarne il provisioning in TOPdesk - Public. Nel caso di TOPdesk - Public, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:

1. Accedere al sito aziendale di **TOPdesk - Public** come amministratore.

2. Nel menu presente sulla parte superiore fare clic su **TOPdesk \> New (Nuovo) \> Support Files (File di supporto) \> Person (Persona)**.
   
    ![Person](./media/topdesk-public-tutorial/files.png "Persona")

3. Nella finestra di dialogo New Person seguire questa procedura:
   
    ![New Person](./media/topdesk-public-tutorial/new.png "New Person")
   
    a. Fare clic sulla scheda General.

    b. Digitare il cognome dell'utente, ad esempio Simon, nella casella di testo **Surname** (Cognome)
 
    c. Selezionare un **sito** per l'account.
 
    d. Fare clic su **Salva**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione account utente fornita da TOPdesk - Public per eseguire il provisioning degli account utente Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà reindirizzato a TOPdesk - URL di accesso pubblico in cui è possibile avviare il flusso di accesso. 

* Passare direttamente a TOPdesk - URL di accesso pubblico e avviare il flusso di accesso da questa pagina.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro TOPdesk - Public nel App personali, viene reindirizzato a TOPdesk - URL di accesso pubblico. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato TOPdesk - Public, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
