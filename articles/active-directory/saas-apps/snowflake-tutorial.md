---
title: 'Esercitazione: Integrazione di Azure Active Directory con Snowflake | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Snowflake.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1af0209265ec4945950120e80a83e19c8ab2eb4b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726276"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Esercitazione: Integrazione di Azure Active Directory con Snowflake

Questa esercitazione descrive come integrare Snowflake con Azure Active Directory (Azure AD). Integrando Snowflake con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Snowflake.
* Abilitare gli utenti per l'accesso automatico a Snowflake con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Snowflake, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Snowflake abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

- Snowflake supporta l'accesso SSO avviato da **SP e IDP**
- Snowflake supporta [provisioning e deprovisioning utenti automatici](snowflake-provisioning-tutorial.md) (scelta consigliata)

## <a name="adding-snowflake-from-the-gallery"></a>Aggiunta di Snowflake dalla raccolta

Per configurare l'integrazione di Snowflake in Azure AD, è necessario aggiungere Snowflake dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Snowflake** nella casella di ricerca.
1. Selezionare **Snowflake** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>Configurare e testare l'accesso SSO di Azure AD per Snowflake

Configurare e testare l'accesso SSO di Azure AD con Snowflake usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Snowflake.

Per configurare e testare l'accesso SSO di Azure AD con Snowflake, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Snowflake](#configure-snowflake-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Snowflake](#create-snowflake-test-user)** : per avere una controparte di B.Simon in Snowflake collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Snowflake** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione nella modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da SP:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
    b. Nella casella di testo **URL di disconnessione** digitare un URL nel formato seguente: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Snowflake](https://support.snowflake.net/s/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Snowflake** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Snowflake.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco applicazioni selezionare **Snowflake**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-snowflake-sso"></a>Configurare l'accesso Single Sign-On di Snowflake

1. In un'altra finestra del Web browser accedere a Snowflake come amministratore della sicurezza.

1. Impostare **Switch Role** (Cambia ruolo) su **ACCOUNTADMIN** facendo clic su **profile** (profilo) nella parte superiore destra della pagina.

    > [!NOTE]
    > È separato dal contesto selezionato nell'angolo in alto a destra sotto il nome utente.
    
    ![Amministratore di Snowflake](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. Aprire il **certificato Base64 scaricato** nel Blocco note. Copiare il valore tra ---BEGIN CERTIFICATE--- e ---END CERTIFICATE--- e incollarlo tra le virgolette accanto a **certificate**. Nella casella di testo **ssoUrl** incollare il valore dell'**URL di accesso** copiato dal portale di Azure. Selezionare **Tutte le query** e fare clic su **Esegui**.

   ![SQL Snowflake](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```


### <a name="create-snowflake-test-user"></a>Creare un utente di test di Snowflake

Per consentire agli utenti di Azure AD di accedere a Snowflake, è necessario effettuarne il provisioning in Snowflake. In Snowflake il provisioning viene effettuato manualmente.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Snowflake come amministratore della sicurezza.

2. Impostare **Switch Role** (Cambia ruolo) su **ACCOUNTADMIN** facendo clic su **profile** (profilo) nella parte superiore destra della pagina.  

    ![Amministratore di Snowflake](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Creare l'utente eseguendo la query SQL seguente e verificando che il parametro "Login name" sia impostato sul nome utente di Azure AD nel foglio di lavoro come illustrato di seguito.

    ![SQL amministratore di Snowflake](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Snowflake, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Snowflake e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Snowflake per cui si è configurato l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Snowflake in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Snowflake per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Snowflake, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)