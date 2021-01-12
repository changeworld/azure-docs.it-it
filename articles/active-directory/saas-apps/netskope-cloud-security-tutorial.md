---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Netskope Administrator Console | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Netskope Administrator Console.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2020
ms.author: jeedes
ms.openlocfilehash: c29a1b409a2deb3695470f5bbec4865afdbae8e1
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913992"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Netskope Administrator Console

Questa esercitazione descrive come integrare Netskope Administrator Console con Azure Active Directory (Azure AD). Integrando Netskope Administrator Console con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Netskope Administrator Console.
* Abilitare gli utenti per l'accesso automatico a Netskope Administrator Console con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Netskope Administrator Console abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Netskope Administrator Console supporta l'accesso SSO avviato da **SP e IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.


## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Aggiunta di Netskope Administrator Console dalla raccolta

Per configurare l'integrazione di Netskope Administrator Console in Azure AD è necessario aggiungere Netskope Administrator Console dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Netskope Administrator Console** nella casella di ricerca.
1. Selezionare **Netskope Administrator Console** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-netskope-administrator-console"></a>Configurare e testare l'accesso SSO di Azure AD per Netskope Administrator Console

Configurare e testare l'accesso SSO di Azure AD con Netskope Administrator Console usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Netskope Administrator Console.

Per configurare e testare l'accesso SSO di Azure AD con Netskope Administrator Console, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Netskope Administrator Console](#configure-netskope-administrator-console-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Netskope Administrator Console](#create-netskope-administrator-console-test-user)** : per avere una controparte di B.Simon in Netskope Administrator Console collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Netskope Administrator Console** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di risposta effettivo. Il valore verrà spiegato più avanti nell'esercitazione.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Poiché il valore di URL accesso non è reale, aggiornarlo con l'URL di accesso effettivo. Per ottenere il valore dell'URL di accesso, contattare il [team di supporto clienti di Netskope Administrator Console](mailto:support@netskope.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Netskope Administrator Console prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Netskope Administrator Console prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome |  Attributo di origine|
    | ---------| --------- |
    | admin-role | user.assignedroles |

    > [!NOTE]
    > Fare clic [qui](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) per informazioni su come configurare i ruoli in Azure AD.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Netskope Administrator Console** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Netskope Administrator Console.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Netskope Administrator Console**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se i ruoli sono stati configurati come illustrato sopra, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-netskope-administrator-console-sso"></a>Configurare l'accesso Single Sign-On di Netskope Administrator Console

1. Aprire una nuova scheda nel browser e accedere al sito aziendale di Netskope Administrator Console come amministratore.

1. Fare clic sulla scheda **Settings** (Impostazioni) dal riquadro di spostamento sinistro.

    ![Screenshot che mostra la scheda Settings selezionata nel riquadro di spostamento.](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Fare clic sulla scheda **Administration** (Amministrazione).

    ![Screenshot che mostra la scheda Administration selezionata nelle impostazioni.](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Fare clic sulla scheda **SSO**.

    ![Screenshot che mostra la voce S S O nel menu Administration.](./media/netskope-cloud-security-tutorial/config-sso.png)

1. Nella sezione **Network Settings** (Impostazioni di rete) seguire questa procedura:
    
    ![Screenshot che mostra la sezione Network Settings in cui è possibile immettere i valori descritti.](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Copiare il valore di **Assertion Consumer Service URL** (URL del servizio consumer di asserzione) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Copiare il valore di **Service Provider Entity ID** (ID entità del provider di servizi) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

1. Fare clic su **EDIT SETTINGS** (MODIFICA IMPOSTAZIONI) nella sezione **SSO/SLO Settings** (Impostazioni SSO/SLO).

    ![Screenshot che mostra S S O / S L O Settings in cui è possibile selezionare EDIT SETTINGS.](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. Nella finestra popup **Settings** (Impostazioni) seguire questa procedura:

    ![Screenshot che mostra la finestra di dialogo Settings in cui è possibile immettere i valori descritti.](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Selezionare **Enable SSO** (Abilita SSO).

    b. Nella casella di testo **IDP URL** (URL dell'IDP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **IDP ENTITY ID** (ID ENTITÀ IDP) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Aprire il certificato con codifica Base64 scaricato nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **IDP CERTIFICATE** (CERTIFICATO IDP).

    e. Selezionare **Enable SSO** (Abilita SSO).

    f. Nella casella di testo **IDP SLO URL** (URL SLO DELL'IDP) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    g. Fare clic su **SUBMIT** (INVIA).

### <a name="create-netskope-administrator-console-test-user"></a>Creare l'utente di test di Netskope Administrator Console

1. Aprire una nuova scheda nel browser e accedere al sito aziendale di Netskope Administrator Console come amministratore.

1. Fare clic sulla scheda **Settings** (Impostazioni) dal riquadro di spostamento sinistro.

    ![Screenshot che mostra la scheda Settings selezionata.](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Fare clic sulla scheda **Active Platform** (Piattaforma attiva).

    ![Screenshot che mostra la scheda Active Platform selezionata nelle impostazioni.](./media/netskope-cloud-security-tutorial/user1.png)

1. Fare clic sulla scheda **Utenti** scheda.

    ![Screenshot che mostra la scheda Users selezionata in Active Platform.](./media/netskope-cloud-security-tutorial/add-user.png)

1. Fare clic su **ADD USERS** (AGGIUNGI UTENTI).

    ![Screenshot che mostra la finestra di dialogo Users in cui è possibile selezionare ADD USERS.](./media/netskope-cloud-security-tutorial/user-add.png)

1. Immettere l'indirizzo di posta elettronica dell'utente che si vuole aggiungere e fare clic su **ADD** (AGGIUNGI).

    ![Screenshot che mostra la schermata Add Users in cui è possibile immettere un elenco di utenti.](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Netskope Administrator Console, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Netskope Administrator Console e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Testa l'applicazione** nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Netskope Administrator Console per cui si è configurato l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Netskope Administrator Console in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Netskope Administrator Console per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Netskope Administrator Console, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).