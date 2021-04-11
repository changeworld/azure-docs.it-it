---
title: 'Esercitazione: Integrazione di Azure Active Directory con MOVEit Transfer - Azure AD integration | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e MOVEit Transfer - Azure AD integration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 73f378bb0f71df4df3731a3ef2a1fdb7f8abb4aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653054"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Esercitazione: Integrazione di Azure Active Directory con MOVEit Transfer - Azure AD integration

Questa esercitazione illustra come integrare MOVEit Transfer-Azure AD Integration con Azure Active Directory (Azure AD). Quando si integra MOVEit Transfer-Azure AD Integration con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a MOVEit Transfer-Azure AD Integration.
* Consentire agli utenti di accedere automaticamente all'integrazione di MOVEit Transfer-Azure AD con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di MOVEit Transfer-Azure AD Integration Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* MOVEit Transfer - Azure AD integration supporta l'accesso SSO avviato da **SP**

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Aggiungere MOVEit Transfer-Azure AD Integration dalla raccolta

Per configurare l'integrazione di MOVEit Transfer - Azure AD integration in Azure AD, è necessario aggiungere MOVEit Transfer - Azure AD integration dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **MOVEit Transfer-Azure ad Integration** nella casella di ricerca.
1. Selezionare **MoveIt Transfer-Azure ad Integration** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Configurare e testare Azure AD SSO per l'integrazione Azure AD Transfer-MOVEit

Configurare e testare Azure AD SSO con MOVEit Transfer-Azure AD Integration usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in MOVEit Transfer-Azure AD Integration.

Per configurare e testare Azure AD SSO con MOVEit Transfer-Azure AD Integration, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare MoveIt Transfer-Azure ad Integration SSO](#configure-moveit-transfer---azure-ad-integration-sso)** -per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di MoveIt Transfer-Azure ad Integration](#create-moveit-transfer---azure-ad-integration-test-user)** : per avere una controparte di B. Simon in MoveIt Transfer-Azure ad Integration collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **MoveIt Transfer-Azure ad Integration** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione **Configurazione SAML di base**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di MOVEit Transfer - Azure AD integration](common/sp-identifier-reply.png)

    Nella casella di testo **URL di accesso** digitare l'URL: `https://contoso.com`

    > [!NOTE]
    > Il valore dell' **URL di accesso** non è reale. è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di clienti di MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support). È possibile scaricare il **file di metadati del provider di servizi** dall'**URL dei metadati del provider di servizi**, come descritto più avanti nella sezione **Configurare l'accesso Single Sign-On per MOVEit Transfer - Azure AD integration** dell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura MOVEit Transfer - Azure AD integration** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a MOVEit Transfer-Azure AD Integration.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **MOVEit Transfer - Azure AD integration**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>Configurare MOVEit Transfer-Azure AD Integration SSO

1. Accedere al tenant di MOVEit Transfer come amministratore.

2. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni).

    ![Sezione delle impostazioni sul lato dell'app](./media/moveittransfer-tutorial/settings.png)

3. Fare clic su **singolo collegamento Sign-on** , che si trova in **criteri di sicurezza-> autenticazione utente**.

    ![Criteri di sicurezza sul lato dell'app](./media/moveittransfer-tutorial/sso.png)

4. Fare clic sul collegamento con l'URL dei metadati per scaricare il documento di metadati.

    ![URL dei metadati del provider di servizi](./media/moveittransfer-tutorial/metadata.png)
    
   * Verificare che **entityID** corrisponda al valore di **Identificatore** nella sezione **Configurazione SAML di base**.
   * Verificare che l'URL del percorso di **AssertionConsumerService** corrisponda all'**URL DI RISPOSTA** nella sezione **Configurazione SAML di base**.
    
     ![Configurazione accesso Single Sign-On sul lato app](./media/moveittransfer-tutorial/xml.png)

5. Fare clic sul pulsante **Add Identity Provider** (Aggiungi provider di identità) per aggiungere un nuovo provider di identità federato.

    ![Aggiungi provider di identità](./media/moveittransfer-tutorial/idp.png)

6. Fare clic su **Browse** (Sfoglia) per selezionare il file di metadati scaricato dal portale di Azure e quindi fare clic su **Add Identity Provider** (Aggiungi provider di identità) per caricare il file scaricato.

    ![Provider di identità SAML](./media/moveittransfer-tutorial/saml.png)

7. Selezionare "**Yes**" (Sì) per **Enabled** (Abilitato) nella pagina **Edit Federated Identity Provider Settings** (Modifica impostazioni provider di identità federato) e fare clic su **Save** (Salva).

    ![Impostazioni provider di identità federato](./media/moveittransfer-tutorial/save.png)

8. Nella pagina **Edit Federated Identity Provider User Settings** (Modifica impostazioni utente del provider di identità federato) eseguire queste operazioni:
    
    ![Modifica delle impostazioni del provider di identità federato](./media/moveittransfer-tutorial/attributes.png)
    
    a. Selezionare **SAML NameID** (ID nome SAML) per **Login name** (Nome di accesso).
    
    b. Selezionare **Other** (Altro) per **Full name** (Nome completo) e nella casella di testo **Attribute name** (Nome attributo) immettere il valore: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selezionare **Other** (Altro) per **Email** (Posta elettronica) e nella casella di testo **Attribute name** (Nome attributo) immettere il valore: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Selezionare **Yes** (Sì) per **Auto-create account on signon** (Crea automaticamente account all'accesso).
    
    e. Fare clic sul pulsante **Salva** .

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Creare un utente di test di MOVEit Transfer - Azure AD integration

Questa sezione descrive come creare un utente di test di nome Britta Simon in MOVEit Transfer - Azure AD integration. MOVEit Transfer - Azure AD integration supporta il provisioning JIT, che è stato abilitato. Non è necessario alcun intervento dell'utente in questa sezione. Durante il tentativo di accesso a MOVEit Transfer - Azure AD integration viene creato un nuovo utente, se questo non esiste già.

>[!NOTE]
>Per creare un utente manualmente, contattare il [team di supporto clienti di MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso MOVEit Transfer-Azure AD Integration, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di MOVEit Transfer-Azure AD Integration e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro MOVEit Transfer-Azure AD Integration in My Apps (app personali), si dovrebbe accedere automaticamente all'integrazione MOVEit Transfer-Azure AD per la quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurata l'integrazione di MOVEit Transfer-Azure AD, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).