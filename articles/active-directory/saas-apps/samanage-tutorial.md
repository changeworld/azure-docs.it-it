---
title: 'Esercitazione: Integrazione di Azure Active Directory con SolarWinds (in precedenza Samanage) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SolarWinds Service Desk (in precedenza Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 6dcd5612bd2c5957ae0a397c3463dbb42445a754
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956044"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Esercitazione: Integrazione di Azure Active Directory con SolarWinds (in precedenza Samanage)

In questa esercitazione si apprenderà come integrare SolarWinds con Azure Active Directory (Azure AD). Quando si integra SolarWinds con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a SolarWinds.
* Consentire agli utenti di accedere automaticamente a SolarWinds con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per SolarWinds Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SolarWinds supporta SSO avviato da **SP** .

## <a name="add-solarwinds-from-the-gallery"></a>Aggiungere SolarWinds dalla raccolta

Per configurare l'integrazione di SolarWinds in Azure AD, è necessario aggiungere SolarWinds dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SolarWinds** nella casella di ricerca.
1. Selezionare **SolarWinds** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>Configurare e testare Azure AD SSO per SolarWinds

Configurare e testare Azure AD SSO con SolarWinds usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in SolarWinds.

Per configurare e testare Azure AD SSO con SolarWinds, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare](#configure-solarwinds-sso)** l'accesso SSO di SolarWinds per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di SolarWinds](#create-solarwinds-test-user)** : per avere una controparte di B. Simon in SolarWinds collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SolarWinds** del portale di Azure trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. La procedura è descritta più avanti nell'esercitazione. Per altri dettagli, contattare il [team di supporto clienti di Samanage](https://www.samanage.com/support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura SolarWinds** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a SolarWinds.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SolarWinds**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

<a name="configure-solarwinds-single-sign-on"></a>

## <a name="configure-solarwinds-sso"></a>Configurare l'accesso SSO per SolarWinds

1. In un'altra finestra del Web browser accedere al sito aziendale di SolarWinds come amministratore.

2. Fare clic su **Dashboard** e selezionare **Setup** (Configurazione) nel riquadro di spostamento a sinistra.
   
    ![Dashboard](./media/samanage-tutorial/tutorial-samanage-1.png "Dashboard")

3. Fare clic su **Single Sign-On**.
   
    ![Single Sign-On](./media/samanage-tutorial/tutorial-samanage-2.png "Single Sign-On")

4. Nella sezione **Login using SAML** (Accesso tramite SAML) seguire questa procedura:
   
    ![Accesso tramite SAML](./media/samanage-tutorial/tutorial-samanage-3.png "Login using SAML")
 
    a. Fare clic su **Enable Single Sign-On with SAML**(Abilita Single Sign-On con SAML).  
 
    b. Nella casella di testo **Identity Provider URL** (URL del provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.    
 
    c. Verificare che il valore di **URL di accesso** corrisponda a quello di **URL di accesso** della sezione **Configurazione SAML di base** nel portale di Azure.
 
    d. Nella casella di testo **URL disconnessione** immettere il valore di **URL disconnessione** copiato dal portale di Azure.
 
    e. Nella casella di testo **SAML Issuer** (Autorità di certificazione SAML) digitare l'URI ID app impostato nel provider di identità.
 
    f. Aprire il certificato con codifica Base 64 scaricato dal portale di Azure nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Paste your Identity Provider x.509 Certificate below** (Incollare il certificato X.509 del provider di identità di seguito).
 
    g. Fare clic su **Create users if they do not exist in SolarWinds**(Crea utenti se non presenti in SolarWinds).
 
    h. Fare clic su **Update**.

### <a name="create-solarwinds-test-user"></a>Creare l'utente di test di SolarWinds

Per consentire agli utenti di Azure AD di accedere a SolarWinds, è necessario effettuarne il provisioning in SolarWinds.  
Nel caso di SolarWinds il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di SolarWinds come amministratore.

2. Fare clic su **Dashboard** e selezionare **Setup** (Configurazione) nel riquadro di spostamento a sinistra.
   
    ![Configurazione](./media/samanage-tutorial/tutorial-samanage-1.png "Configurazione")

3. Fare clic sulla scheda **Users**.
   
    ![Utenti](./media/samanage-tutorial/tutorial-samanage-6.png "Utenti")

4. Fare clic su **New User**.
   
    ![New User](./media/samanage-tutorial/tutorial-samanage-7.png "Nuovo utente")

5. In **Name** (Nome) e **Email Address** (Indirizzo di posta elettronica) digitare nome e indirizzo di posta elettronica di un account Azure Active Directory di cui si vuole effettuare il provisioning e fare clic su **Create user** (Crea utente).
   
    ![Creare un utente](./media/samanage-tutorial/tutorial-samanage-8.png "Crea utente")
   
   >[!NOTE]
   >Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da SolarWinds per effettuare il provisioning degli account utente di Azure Active Directory.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso a SolarWinds, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso a SolarWinds e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro SolarWinds in app personali, questo verrà reindirizzato all'URL di accesso a SolarWinds. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato SolarWinds è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).