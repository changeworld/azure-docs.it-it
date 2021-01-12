---
title: 'Esercitazione: Integrazione di Azure Active Directory con Andromeda | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Andromeda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: f3fcad14ae0c448ee2a41cddf56f5ea64c8e08d2
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916134"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Esercitazione: Integrazione di Azure Active Directory con Andromeda

Questa esercitazione descrive come integrare Andromeda con Azure Active Directory (Azure AD).
L'integrazione di Andromeda con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad Andromeda.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad Andromeda con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Andromeda, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Andromeda abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Andromeda supporta l'accesso SSO avviato da **SP e IDP**
* Andromeda supporta il provisioning utenti **JIT**

## <a name="adding-andromeda-from-the-gallery"></a>Aggiunta di Andromeda dalla raccolta

Per configurare l'integrazione di Andromeda in Azure AD, è necessario aggiungere Andromeda dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Andromeda** nella casella di ricerca.
1. Selezionare **Andromeda** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Configurare e testare l'accesso SSO di Azure AD per Andromeda

Configurare e testare l'accesso SSO di Azure AD con Andromeda usando un utente di test di nome **B. Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Andromeda.

Per configurare e testare l'accesso SSO di Azure AD con Andromeda, seguire questa procedura:


1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di Andromeda](#configure-andromeda-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di Andromeda](#create-andromeda-test-user)** : per definire una controparte di Britta Simon in Andromeda collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Andromeda** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<tenantURL>.ngcxpress.com/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Screenshot che mostra l'opzione Impostare URL aggiuntivi che consente di immettere un URL di accesso.](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Poiché questi non sono i valori reali, È necessario aggiornare tale valore con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura è descritta più avanti nell'esercitazione.

6. L'applicazione Andromeda si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Screenshot che mostra gli attributi utente, tra cui givenname user.givenname e emailaddress user.mail.](common/edit-attribute.png)

    > [!Important]
    > Cancellare le definizioni dello spazio dei nomi durante la configurazione.

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura: 

    | Nome | Attributo di origine|
    | ------ | -----------|
    | ruolo        | Ruolo specifico dell'app |
    | type        | Tipo di app |
    | company       | CompanyName |

    > [!NOTE]
    > Andromeda prevede ruoli per gli utenti assegnati all'applicazione. Configurare questi ruoli in Azure AD in modo che agli utenti possano essere assegnati i ruoli appropriati. Per informazioni su come configurare i ruoli in Azure AD, vedere [qui](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![Screenshot che mostra la finestra Attestazioni utente con le opzioni Aggiungi nuova attestazione e Salva.](common/new-save-attribute.png)

    ![Screenshot che mostra la finestra di dialogo Gestisci attestazioni utente in cui è possibile immettere i valori descritti in questo passaggio.](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **OK**.

    g. Fare clic su **Salva**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

9. Nella sezione **Configura Andromeda** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Andromeda.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Andromeda**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se i ruoli sono stati configurati come illustrato sopra, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-andromeda-sso"></a>Configurare l'accesso SSO per Andromeda

1. Accedere al sito aziendale di Andromeda come amministratore.

2. Nella parte superiore della barra dei menu fare clic su **Admin** (Amministratore) e passare ad **Administration** (Amministrazione).

    ![Amministratore di Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Sul lato sinistro della barra degli strumenti in **Interfaces** (Interfacce) fare clic su **SAML Configuration** (Configurazione SAML).

    ![SAML per Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Nella sezione **SAML Configuration** (Configurazione SAML) seguire la procedura seguente:

    ![Configurazione di Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Selezionare **Enable SSO with SAML** (Abilita SSO con SAML).

    b. Nella sezione **Andromeda Information** (Informazioni su Andromeda) copiare il valore del campo **SP Identity** (Identità SP) e incollarlo nella casella di testo **Identificatore** della sezione **Configurazione SAML di base**.

    c. Copiare il valore del campo **Consumer URL** (URL consumer) nella casella di testo **URL di risposta** della sezione **Configurazione SAML di base**.

    d. Copiare il valore del campo **URL di accesso** nella casella di testo **URL di accesso** della sezione **Configurazione SAML di base**.

    e. Nella sezione **Provider di identità SAML** digitare il nome del provider di identità.

    f. Nella casella di testo **Single Sign On End Point** (Endpoint Single Sign-On) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    g. Aprire il **certificato con codifica Base64** scaricato dal portale di Azure nel Blocco note e incollarlo nella casella di testo **Certificato X.509**.
    
    h. Eseguire il mapping degli attributi seguenti con il rispettivo valore per implementare l'accesso SSO da Azure AD. L'attributo **ID utente** è richiesto per l'accesso. Ai fini del provisioning, gli attributi relativi a **posta elettronica**, **società**, **tipo di utente** e **ruolo** sono obbligatori. In questa sezione viene definito il mapping degli attributi (nome e valori) associati agli attributi definiti nel portale di Azure

    ![Mapping degli attributi di Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Fare clic su **Salva**.


### <a name="create-andromeda-test-user"></a>Creare un utente di test di Andromeda

In questa sezione si crea un utente di nome Britta Simon in Andromeda. Andromeda supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Andromeda, ne viene creato uno nuovo dopo l'autenticazione. Per creare un utente manualmente, contattare il [team del supporto clienti di Andromeda](https://www.ngcsoftware.com/support/).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Andromeda, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Andromeda e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Testa l'applicazione** nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Andromeda per cui si è configurato l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Andromeda in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Andromeda per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Andromeda, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).