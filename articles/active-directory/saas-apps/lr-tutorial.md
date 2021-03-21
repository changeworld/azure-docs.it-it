---
title: 'Esercitazione: Integrazione di Azure Active Directory con LoginRadius | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LoginRadius.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606420"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Esercitazione: Integrazione di Azure Active Directory con LoginRadius

Questa esercitazione descrive come integrare LoginRadius con Azure Active Directory (Azure AD).

L'integrazione di LoginRadius con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a LoginRadius.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a LoginRadius con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con LoginRadius, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di LoginRadius abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* LoginRadius supporta l'accesso SSO avviato da **SP**

## <a name="adding-loginradius-from-the-gallery"></a>Aggiunta di LoginRadius dalla raccolta

Per configurare l'integrazione di LoginRadius in Azure AD, è necessario aggiungere LoginRadius dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LoginRadius dalla raccolta:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione**:

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **LoginRadius**, selezionare **LoginRadius** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![LoginRadius nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LoginRadius con un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LoginRadius.

Per configurare e testare l'accesso Single Sign-On di Azure AD con LoginRadius, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di LoginRadius](#configure-loginradius-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test per LoginRadius](#create-loginradius-test-user)** : per avere una controparte di Britta Simon in LoginRadius collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con LoginRadius, eseguire queste operazioni:

1. Nella pagina di integrazione dell'applicazione [LoginRadius](https://portal.azure.com/) del **portale di Azure** fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nel riquadro **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire il riquadro **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base**:

   ![Informazioni su URL e dominio di LoginRadius per l'accesso Single Sign-On](common/sp-identifier.png)

   1. Nella casella di testo **URL di accesso** immettere l'URL `https://secure.loginradius.com/login`

   1. Nella casella di testo **Identificatore (ID entità)** immettere l'URL `https://lr.hub.loginradius.com/`

   1. Nella casella di testo **URL di risposta (URL del servizio consumer di asserzione)** immettere l'URL del servizio consumer di asserzione di LoginRadius `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` 

5. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** per scaricare il file **XML dei metadati della federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura LoginRadius** copiare gli URL appropriati in base alle proprie esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

   - URL di accesso

   - Identificatore di Azure AD

   - URL di chiusura sessione

## <a name="configure-loginradius-single-sign-on"></a>Configurare il Single Sign-On su LoginRadius

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nella console di amministrazione di LoginRadius.

1. Accedere all'account della [console di amministrazione](https://adminconsole.loginradius.com/login) di LoginRadius.

2. Passare alla sezione **Team Management** (Gestione team) della [console di amministrazione di LoginRadius](https://secure.loginradius.com/account/team).

3. Selezionare la scheda **Single Sign-On** e quindi **Azure AD**:

   ![Screenshot che mostra il menu Single Sign-On nella console di Team Management di LoginRadius](./media/loginradius-tutorial/azure-ad.png)
4. Nella pagina di configurazione di Azure AD seguire questa procedura:

   ![Screenshot che mostra la configurazione di Azure Active Directory nella console di Team Management di LoginRadius](./media/loginradius-tutorial/single-sign-on.png)

    1. In **ID Provider Location** (Percorso del provider di ID) immettere il valore di SIGN-ON ENDPOINT (ENDPOINT DI ACCESSO), ottenuto dall'account Azure AD.

    1. In **ID Provider Logout URL** (URL di disconnessione del provider di ID) immettere il valore di SIGN-OUT ENDPOINT (ENDPOINT DI DISCONNESSIONE), ottenuto dall'account Azure AD.
 
    1. In **ID Provider Certificate** (Certificato del provider di ID) immettere il valore del certificato di Azure AD, ottenuto dall'account Azure AD. Immettere il valore del certificato con intestazione e piè di pagina. Esempio: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. In **Service Provider Certificate** (Certificato del provider di servizi) e **Server Provider Certificate Key** (Chiave del certificato del provider server) immettere il certificato e la chiave. 

       Per creare un certificato autofirmato, eseguire i comandi seguenti sulla riga di comando (Linux/Mac):

       - Comando per ottenere la chiave del certificato per il provider di servizi: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Comando per ottenere il certificato per il provider di servizi: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > Assicurarsi di immettere i valori del certificato e della chiave del certificato con intestazione e piè di pagina:
       > - Formato del valore del certificato di esempio: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - Formato del valore della chiave del certificato di esempio: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. Nella sezione **Data Mapping** (Mapping dei dati) selezionare i campi (campi SP) e immettere i campi corrispondenti di Azure AD (campi IdP).

    Ecco alcuni nomi di campo per Azure AD.

    | Campi    | Chiave del profilo                                                          |
    | --------- | -------------------------------------------------------------------- |
    | Email     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > Il mapping del campo **Email** è obbligatorio. I mapping dei campi **FirstName** e **LastName** sono facoltativi.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

L'obiettivo di questa sezione consiste nel creare l'utente di test "Britta Simon" nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. Nelle proprietà di **Utente** seguire questa procedura.

   ![Finestra di dialogo Utente](common/user-properties.png)

   1. Nel campo **Nome** immettere **BrittaSimon**.
  
   1. Nel campo **Nome utente** immettere `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com.

   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

   1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LoginRadius.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **LoginRadius**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **LoginRadius**.

    ![Collegamento LoginRadius nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Selezionare il pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nel riquadro **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti** e quindi scegliere il pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un qualsiasi valore di ruolo nell'asserzione SAML, nel riquadro **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nel riquadro **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="create-loginradius-test-user"></a>Creare un utente di test per LoginRadius

1. Accedere all'account della [console di amministrazione](https://adminconsole.loginradius.com/login) di LoginRadius.

2. Passare alla sezione Team Management (Gestione team) della console di amministrazione di LoginRadius.

   ![Screenshot che mostra la console di amministrazione di LoginRadius](./media/loginradius-tutorial/team-management.png)
3. Selezionare **Add Team Member** (Aggiungi membro del team) nel menu laterale per aprire il modulo. 

4. Nel modulo **Add Team Member** (Aggiungi membro del team) creare un utente di nome Britta Simon nel sito di LoginRadius specificando i dettagli dell'utente e assegnando le autorizzazioni che l'utente deve avere. Per altre informazioni sulle autorizzazioni in base ai ruoli, vedere la sezione relativa alle [autorizzazioni di accesso al ruolo](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) del documento di LoginRadius sulla [gestione dei membri del team](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0). Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

1. In un browser passare a https://accounts.loginradius.com/auth.aspx e selezionare **Fed SSO log in** (Accesso SSO federato).
2. Immettere il nome dell'app LoginRadius e quindi selezionare **Login** (Accedi).
3. Dovrebbe essere visualizzato un popup in cui viene chiesto di accedere all'account Azure AD.
4. Dopo l'autenticazione, il popup si chiuderà e si accederà alla console di amministrazione di LoginRadius.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)
