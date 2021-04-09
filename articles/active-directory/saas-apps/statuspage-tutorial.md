---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con StatusPage | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: cc3ce56ecd17d627001f4925355c055afdc09d22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con StatusPage

Questa esercitazione descrive come integrare StatusPage con Azure Active Directory (Azure AD).
L'integrazione di StatusPage con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a StatusPage.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a StatusPage con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con StatusPage, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di StatusPage abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* StatusPage supporta l'accesso SSO avviato da **IDP**

## <a name="adding-statuspage-from-the-gallery"></a>Aggiunta di StatusPage dalla raccolta

Per configurare l'integrazione di StatusPage in Azure AD, è necessario aggiungere StatusPage dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **StatusPage** nella casella di ricerca.
1. Selezionare **StatusPage** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per StatusPage

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con StatusPage usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in StatusPage.

Per configurare e testare l'accesso SSO di Azure AD con StatusPage, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di StatusPage](#configure-statuspage-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di StatusPage](#create-statuspage-test-user)** : per avere una controparte di Britta Simon in StatusPage collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **AskYourTeam** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | Identificatore |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:

     | URL di risposta |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > Per richiedere i metadati necessari per configurare l'accesso Single Sign-On, contattare il team di supporto di StatusPage all'indirizzo [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io). 
    >
    > a. Dal file di metadati, copiare il valore relativo all'autorità di certificazione e quindi incollarlo nella casella di testo **Identifier** (Identificatore).
    >
    > b. Dal file di metadati copiare il valore relativo all'URL di risposta e quindi incollarlo nella casella di testo **Reply URL** (URL di risposta).

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura StatusPage** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a StatusPage.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **StatusPage**.

2. Nell'elenco delle applicazioni selezionare **StatusPage**.

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-statuspage-sso"></a>Configurare l'accesso Single Sign-On di StatusPage

1. Per automatizzare la configurazione all'interno di StatusPage, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura StatusPage** per passare direttamente all'applicazione StatusPage. Specificare quindi le credenziali di amministratore per accedere a StatusPage. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare StatusPage manualmente, in un'altra finestra del Web browser accedere al sito aziendale di StatusPage come amministratore.

1. Nella barra degli strumenti principale scegliere **Manage Account**.

    ![Screenshot che mostra la voce Manage Account selezionata nel sito aziendale di StatusPage.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Fare clic sulla scheda **Single Sign-on**.

    ![Screenshot che mostra la scheda Single Sign-On.](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Nella pagina di configurazione dell’accesso Single Sign-On, seguire questa procedura:

    ![Screenshot che mostra la pagina di configurazione dell'accesso Single Sign-On in cui è possibile immettere i valori descritti.](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Screenshot che mostra il pulsante Save Configuration.](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Nella casella di testo **SSO Target URL** (URL di destinazione SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificate** (Certificato).

    c. Fare clic su **SALVA CONFIGURAZIONE**.

### <a name="create-statuspage-test-user"></a>Creare l'utente di test di StatusPage

Questa sezione descrive come creare un utente chiamato Britta Simon in StatusPage.

StatusPage supporta il provisioning JIT (just-in-time), Tale funzionalità è già stata abilitata in [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso).

**Per creare un utente denominato Britta Simon in StatusPage, seguire questa procedura:**

1. Accedere al sito aziendale di StatusPage come amministratore.

1. Nel menu in alto fare clic su **Gestisci Account**.

    ![Screenshot che mostra la voce Manage Account selezionata nel sito aziendale di StatusPage.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Fare clic sulla scheda **Membri del team**.
  
    ![Screenshot che mostra la scheda Team Members.](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Fare clic su **AGGIUNGI MEMBRO DEL TEAM**.
  
    ![Screenshot che mostra il pulsante Add Team Member.](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Digitare i valori **Indirizzo e-mail**, **Nome** e **Cognome** di un utente valido di cui si vuole effettuare il provisioning nelle caselle di testo corrispondenti. 

    ![Screenshot che mostra la finestra di dialogo Add a User in cui è possibile immettere i valori descritti.](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Per **Ruolo** scegliere **Client Administrator** (Amministratore client).

1. Fare clic su **CREA ACCOUNT**.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Dopo aver fatto clic su Test this application (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di StatusPage per cui si è configurato l'accesso SSO

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di StatusPage in App personali, si dovrebbe accedere automaticamente all'istanza di StatusPage per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato StatusPage, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).