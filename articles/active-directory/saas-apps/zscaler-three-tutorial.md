---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Zscaler Three | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler Three.
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
ms.openlocfilehash: 75d95ff77b48e7b1102900bc103e6930282e21e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726250"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Zscaler Three

Questa esercitazione descrive come integrare Zscaler Three con Azure Active Directory (Azure AD). Integrando Zscaler Three con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Zscaler Three.
* Abilitare gli utenti per l'accesso automatico a Zscaler Three con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Zscaler Three abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zscaler Three supporta l'accesso SSO avviato da **SP**

* Zscaler Three supporta il provisioning utenti **JIT**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-zscaler-three-from-the-gallery"></a>Aggiunta di Zscaler Three dalla raccolta

Per configurare l'integrazione di Zscaler Three in Azure AD, è necessario aggiungere Zscaler Three dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Zscaler Three** nella casella di ricerca.
1. Selezionare **Zscaler Three** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-three"></a>Configurare e testare l'accesso SSO di Azure AD per Zscaler Three

Configurare e testare l'accesso SSO di Azure AD con Zscaler Three usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler Three.

Per configurare e testare l'accesso SSO di Azure AD con Zscaler Three, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Zscaler Three](#configure-zscaler-three-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Zscaler Three](#create-zscaler-three-test-user)** : per avere una controparte di B.Simon in Zscaler Three collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Zscaler Two** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL di accesso** digitare un URL: `https://login.zscalerthree.net/sfc_sso`

1. L'applicazione Zscaler Three prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Screenshot che mostra il riquadro Attributi utente con l'icona di modifica selezionata.](common/edit-attribute.png)

6. Oltre quelli elencati in precedenza, l'applicazione Zscaler Three prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    > [!NOTE]
    > Fare clic [qui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) per informazioni su come configurare un ruolo in Azure AD.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Zscaler Three** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zscaler Three.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Zscaler Three**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare l'utente **Britta Simon** nell'elenco e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

    ![Screenshot che mostra la finestra di dialogo Utenti e gruppi in cui è possibile selezionare un utente.](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. Nella finestra di dialogo **Seleziona ruolo** scegliere il ruolo utente appropriato dall'elenco e fare clic sul pulsante **Seleziona** in basso nella schermata.

    ![Screenshot che mostra la finestra di dialogo Seleziona ruolo in cui è possibile selezionare un ruolo utente.](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

    ![Screenshot che mostra la finestra di dialogo Aggiungi assegnazione in cui è possibile selezionare Assegna.](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Configurare l'accesso Single Sign-On di Zscaler Three

1. Per automatizzare la configurazione all'interno di Zscaler Three, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installare l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Zscaler Three** per passare direttamente all'applicazione Zscaler Three. Nell'applicazione fornire le credenziali di amministratore per accedere a Zscaler Three. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Zscaler Three, aprire una nuova finestra del Web browser, accedere al sito aziendale di Zscaler Three come amministratore e completare i passaggi seguenti:

4. Passare a **Amministrazione > Autenticazione > Impostazioni di autenticazione** ed eseguire i passaggi seguenti:

    ![Screenshot che mostra il sito Zscaler One con i passaggi descritti.](./media/zscaler-three-tutorial/ic800206.png "Amministrazione")

    a. In Tipo di autenticazione scegliere **SAML**.

    b. Fare clic su **Configure SAML**.

5. Nella finestra **Modifica SAML** eseguire i passaggi seguenti: e fare clic su Salva.  

    ![Gestire utenti e autenticazione](./media/zscaler-three-tutorial/ic800208.png "Manage Users & Authentication")

    a. Nella casella di testo **URL portale SAML** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Attributo del nome di accesso** immettere **NameID**.

    c. Fare clic su **Carica** per caricare il certificato di firma di Azure SAML scaricato dal portale di Azure nel **certificato pubblico SSL**.

    d. Selezionare **Abilita provisioning automatico SAML**.

    e. Nella casella di testo **Attributo nome visualizzato dell'utente** immettere **displayName** se si desidera abilitare il provisioning automatico di SAML per gli attributi displayName.

    f. Nella casella di testo **Attributo nome gruppo** immettere **memberOf** se si desidera abilitare il provisioning automatico di SAML per gli attributi memberOf.

    g. Nella casella di testo **Attributo nome reparto** immettere **department** se si desidera abilitare il provisioning automatico di SAML per gli attributi department.

    h. Fare clic su **Salva**.

6. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:

    ![Screenshot che mostra la finestra di dialogo Configure User Authentication con l'opzione Attiva selezionata.](./media/zscaler-three-tutorial/ic800207.png)

    a. Passare il mouse sul menu **Attivazione** nella parte inferiore sinistra.

    b. Fare clic su **Attiva**.

## <a name="configuring-proxy-settings"></a>Configurazione delle impostazioni proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Per configurare le impostazioni proxy in Internet Explorer

1. Avviare **Internet Explorer**.

2. Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**.   

     ![Opzioni Internet](./media/zscaler-three-tutorial/ic769492.png "Opzioni Internet")

3. Fare clic sulla scheda **Connessioni** .   

     ![Connessioni](./media/zscaler-three-tutorial/ic769493.png "Connessioni")

4. Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN**.

5. Nella sezione del server proxy seguire questa procedura:   

    ![Server proxy](./media/zscaler-three-tutorial/ic769494.png "Server proxy")

    a. Selezionare **Usa un server proxy per la LAN**.

    b. Nella casella di testo Indirizzo digitare **gateway.Zscaler Three.net**.

    c. Nella casella di testo Porta digitare **80**.

    d. Selezionare **Ignora server proxy per indirizzi locali**.

    e. Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)** .

6. Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet**.

### <a name="create-zscaler-three-test-user"></a>Creare un utente di test di Zscaler Three

In questa sezione viene creato un utente di nome B.Simon in Zscaler Three. Zscaler Three supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Zscaler Three, ne viene creato uno nuovo quando si prova ad accedere a Zscaler Three.

>[!Note]
>Per creare un utente manualmente, è necessario contattare il [team di supporto di Zscaler Three](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Zscaler Three, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Zscaler Three e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Zscaler Three in App personali, verrà eseguito il reindirizzamento all'URL di accesso di Zscaler Three. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Zscaler Three, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
