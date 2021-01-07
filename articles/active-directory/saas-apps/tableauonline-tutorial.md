---
title: 'Esercitazione: Esercitazione: Integrazione di Azure Active Directory con Tableau Online | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Tableau Online.
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
ms.openlocfilehash: d6b1fad722fa0a2f44c7e415f208f7e29a272a70
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813956"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Tableau Online

Questa esercitazione descrive come integrare Tableau Online con Azure Active Directory (Azure AD). Integrando Tableau Online con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Tableau Online.
* Abilitare gli utenti per l'accesso automatico a Tableau Online con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Tableau Online abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Tableau Online supporta l'accesso SSO avviato da **SP**

## <a name="adding-tableau-online-from-the-gallery"></a>Aggiunta di Tableau Online dalla raccolta

Per configurare l'integrazione di Tableau Online in Azure AD, è necessario aggiungere Tableau Online dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Tableau Online** nella casella di ricerca.
1. Selezionare **Tableau Online** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Tableau Online

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Tableau Online usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Tableau Online.

Per configurare e testare l'accesso SSO di Azure AD con Tableau Online, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Tableau Online](#configure-tableau-online-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Tableau Online](#create-tableau-online-test-user)** : per avere una controparte di B.Simon in Tableau Online collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Tableau Online** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare l'URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Il valore `<entityid>` verrà ottenuto dalla sezione **Configura Tableau Online** di questa esercitazione. Il valore dell'ID entità corrisponderà al valore di **Identificatore Azure AD** nella sezione **Configura Tableau Online**.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Tableau Online** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Tableau Online.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Tableau Online**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-tableau-online-sso"></a>Configurare l'accesso Single Sign-On di Tableau Online

1. Per automatizzare la configurazione all'interno di Tableau Online, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Tableau Online** per passare direttamente all'applicazione Tableau Online. Specificare quindi le credenziali di amministratore per accedere a Tableau Online. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare Tableau Online manualmente, in un'altra finestra del Web browser accedere al sito aziendale di Tableau Online come amministratore.

1. Fare clic su **Settings** (Impostazioni) e quindi su **Authentication** (Autenticazione).

    ![Screenshot che mostra l'opzione Authentication selezionata nel menu Settings.](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Per abilitare SAML, nella sezione **Authentication types** (Tipi di autenticazione): Selezionare **Enable an additional authentication method** (Abilita un metodo di autenticazione aggiuntivo) e quindi selezionare la casella di controllo **SAML**.

    ![Screenshot che mostra la sezione Authentication types in cui è possibile selezionare i valori.](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Scorrere fino alla sezione **Import metadata file into Tableau Online** (Importa file di metadati in Tableau Online).  Fare clic su Browse (Sfoglia) e importare il file di metadati scaricato da Azure AD. Fare quindi clic su **Apply** (Applica).

   ![Screenshot che mostra la sezione in cui è possibile importare il file di metadati.](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Nella sezione **Match assertions** (Corrispondenza asserzioni) inserire il nome di asserzione del provider di identità corrispondente per l'**indirizzo e-mail**, il **nome** e il **cognome**. Per ottenere queste informazioni da Azure AD: 
  
    a. Nel portale di Azure, aprire la pagina di integrazione dell'applicazione **Tableau Online**.

    b. Nella sezione **User Attributes & Claims** (Attributi e attestazioni utente) fare clic sull'icona di modifica.

   ![Screenshot che mostra la sezione Attributi utente e intestazioni in cui è possibile selezionare l'icona Modifica.](./media/tableauonline-tutorial/attributesection.png)

    c. Copiare il valore dello spazio dei nomi per i seguenti attributi: givenname, e-mail e cognome utilizzando la procedura seguente:

   ![Screenshot che mostra gli attributi Givenname, Surname ed Emailaddress.](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Fare clic sul valore **user.givenname**

    e. Copiare il valore dalla casella di testo **Spazio dei nomi**.

    ![Screenshot che mostra la sezione Gestisci attestazioni utente in cui è possibile immettere lo spazio dei nomi.](./media/tableauonline-tutorial/attributesection2.png)

    f. Per copiare i valori dello spazio dei nomi per l'indirizzo di posta elettronica e il cognome, ripetere i passaggi precedenti.

    g. Passare all'applicazione Tableau Online e quindi impostare la sezione **User Attributes & Claims** (Attributi e attestazioni utente) nel modo seguente:

    * Indirizzo di posta elettronica: **mail** o **userprincipalname**

    * Nome: **givenname**

    * Cognome: **surname**

    ![Screenshot che mostra la sezione Match attributes in cui è possibile immettere i valori.](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Creare l'utente di test di Tableau Online

In questa sezione viene creato un utente chiamato Britta Simon in Tableau Online.

1. In **Tableau Online** fare clic su **Settings** (Impostazioni) e quindi sulla sezione **Authentication** (Autenticazione). Scorrere fino alla sezione **Manage Users** (Gestisci utenti). Fare clic su **Add Users** (Aggiungi utenti) e quindi su **Enter Email Addresses** (Immetti indirizzi di posta elettronica).
  
    ![Screenshot che mostra la sezione Manage Users in cui è possibile selezionare Add users.](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selezionare **Add users for (SAML) authentication** (Aggiungi utenti per autenticazione - SAML). Nella casella di testo **Enter email addresses** (Immetti indirizzi di posta elettronica) aggiungere britta.simon\@contoso.com
  
    ![Screenshot che mostra la pagina Add Users in cui è possibile immettere un indirizzo di posta elettronica.](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Fare clic su **Aggiungi utenti**.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Tableau Online, in cui è possibile avviare il flusso di accesso.

* Passare direttamente all'URL di accesso di Tableau Online e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Tableau Online in App personali, verrà eseguito il reindirizzamento all'URL di accesso di Tableau Online. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Tableau Online, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)