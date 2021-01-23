---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler ZSCloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler ZSCloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 605f033ed48dd79fd164aabd95e326a6467d0ecd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726207"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler ZSCloud

Questa esercitazione illustra come integrare Zscaler ZSCloud con Azure Active Directory (Azure AD). Integrando Zscaler ZSCloud con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Zscaler ZSCloud.
* Abilitare gli utenti per l'accesso automatico a Zscaler ZSCloud con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zscaler ZSCloud, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Zscaler ZSCloud abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zscaler ZSCloud supporta l'accesso SSO avviato da **SP**

* Zscaler ZSCloud supporta il provisioning utenti **JIT**

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Aggiunta di Zscaler ZSCloud dalla raccolta

Per configurare l'integrazione di Zscaler ZSCloud in Azure AD, è necessario aggiungere Zscaler ZSCloud dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Zscaler ZSCloud** nella casella di ricerca.
1. Selezionare **Zscaler ZSCloud** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-zscloud"></a>Configurare e testare l'accesso SSO di Azure AD per Zscaler ZSCloud

Configurare e testare l'accesso SSO di Azure AD con Zscaler ZSCloud usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler ZSCloud.

Per configurare e testare l'accesso SSO di Azure AD con Zscaler ZSCloud, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Zscaler ZSCloud](#configure-zscaler-zscloud-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Zscaler ZSCloud](#create-zscaler-zscloud-test-user)** : per avere una controparte di B. Simon in Zscaler ZSCloud collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Zscaler ZSCloud** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona a forma di matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Zscaler ZSCloud.

    > [!NOTE]
    > È necessario aggiornare il valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Zscaler ZSCloud](https://help.zscaler.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione Zscaler ZSCloud prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Screenshot che mostra il riquadro Attributi utente con l'icona di modifica selezionata.](common/edit-attribute.png)

6. Oltre quelli elencati in precedenza, l'applicazione Zscaler ZSCloud prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:
    
    | Nome | Attributo di origine |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![Screenshot che mostra il riquadro Attestazioni utente con l'opzione per aggiungere una nuova attestazione.](common/new-save-attribute.png)

    ![Screenshot che mostra la finestra di dialogo Gestisci attestazioni utente in cui è possibile immettere i valori descritti.](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.
    
    f. Fare clic su **Salva**.

    > [!NOTE]
    > Fare clic [qui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) per informazioni su come configurare un ruolo in Azure AD.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Configura Zscaler ZSCloud** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zscaler ZSCloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Zscaler ZSCloud**.
2. Nell'elenco delle applicazioni selezionare **Zscaler ZSCloud**.
3. Scegliere **Utenti e gruppi** dal menu a sinistra.
4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
5. Nella finestra di dialogo **Utenti e gruppi** selezionare l'utente **Britta Simon** nell'elenco e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

    ![Screenshot che mostra la finestra di dialogo Utenti e gruppi in cui è possibile selezionare un utente.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. Nella finestra di dialogo **Seleziona ruolo** scegliere il ruolo utente appropriato dall'elenco e fare clic sul pulsante **Seleziona** in basso nella schermata.

    ![Screenshot che mostra la finestra di dialogo Seleziona ruolo in cui è possibile selezionare un ruolo utente.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

    ![Screenshot che mostra la finestra di dialogo Aggiungi assegnazione in cui è possibile selezionare Assegna.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >Il ruolo Accesso predefinito non è supportato in quanto interrompe il provisioning, quindi non è possibile selezionare il ruolo predefinito durante l'assegnazione dell'utente.

## <a name="configure-zscaler-zscloud-sso"></a>Configurare l'accesso SSO di Zscaler ZSCloud

1. Per automatizzare la configurazione all'interno di Zscaler ZSCloud, è necessario installare l'**estensione del browser per l'accesso sicuro MyApps** facendo clic su **Install the extension** (Installa l'estensione).

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Zscaler ZSCloud** per passare direttamente all'applicazione Zscaler ZSCloud. Nell'applicazione fornire le credenziali di amministratore per accedere Zscaler ZSCloud. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Configurare l'accesso SSO](common/setup-sso.png)

3. Se si vuole configurare manualmente Zscaler ZSCloud, aprire una nuova finestra del Web browser, accedere al sito aziendale di Zscaler ZSCloud come amministratore e seguire questa procedura:

4. Passare a **Amministrazione > Autenticazione > Impostazioni di autenticazione** ed eseguire i passaggi seguenti:
   
    ![Screenshot che mostra il sito Zscaler con i passaggi descritti.](./media/zscaler-zscloud-tutorial/ic800206.png "Amministrazione")

    a. In Tipo di autenticazione scegliere **SAML**.

    b. Fare clic su **Configure SAML**.

5. Nella finestra **Modifica SAML** eseguire i passaggi seguenti: e fare clic su Salva.  
            
    ![Gestire utenti e autenticazione](./media/zscaler-zscloud-tutorial/ic800208.png "Manage Users & Authentication")
    
    a. Nella casella di testo **URL portale SAML** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Attributo del nome di accesso** immettere **NameID**.

    c. Fare clic su **Carica** per caricare il certificato di firma di Azure SAML scaricato dal portale di Azure nel **certificato pubblico SSL**.

    d. Selezionare **Abilita provisioning automatico SAML**.

    e. Nella casella di testo **Attributo nome visualizzato dell'utente** immettere **displayName** se si desidera abilitare il provisioning automatico di SAML per gli attributi displayName.

    f. Nella casella di testo **Attributo nome gruppo** immettere **memberOf** se si desidera abilitare il provisioning automatico di SAML per gli attributi memberOf.

    g. Nella casella di testo **Attributo nome reparto** immettere **department** se si desidera abilitare il provisioning automatico di SAML per gli attributi department.

    h. Fare clic su **Salva**.

6. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:

    ![Screenshot che mostra la finestra di dialogo Configure User Authentication con l'opzione Attiva selezionata.](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Passare il mouse sul menu **Attivazione** nella parte inferiore sinistra.

    b. Fare clic su **Attiva**.

## <a name="configuring-proxy-settings"></a>Configurazione delle impostazioni proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Per configurare le impostazioni proxy in Internet Explorer

1. Avviare **Internet Explorer**.

2. Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**.   
    
     ![Opzioni Internet](./media/zscaler-zscloud-tutorial/ic769492.png "Opzioni Internet")

3. Fare clic sulla scheda **Connessioni** .   
  
     ![Connessioni](./media/zscaler-zscloud-tutorial/ic769493.png "Connessioni")

4. Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN**.

5. Nella sezione del server proxy seguire questa procedura:   
   
    ![Server proxy](./media/zscaler-zscloud-tutorial/ic769494.png "Server proxy")

    a. Selezionare **Usa un server proxy per la LAN**.

    b. Nella casella di testo Indirizzo digitare **gateway.Zscaler ZSCloud.net**.

    c. Nella casella di testo Porta digitare **80**.

    d. Selezionare **Ignora server proxy per indirizzi locali**.

    e. Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)** .

6. Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet**.


### <a name="create-zscaler-zscloud-test-user"></a>Creare l'utente di test di Zscaler ZSCloud

In questa sezione viene creato un utente di nome Britta Simon in Zscaler ZSCloud. Zscaler ZSCloud supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Zscaler ZSCloud, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di Zscaler ZSCloud](https://help.zscaler.com/).

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Zscaler ZSCloud, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Zscaler ZSCloud e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Zscaler ZSCloud in App personali, verrà eseguito il reindirizzamento all'URL di accesso di Zscaler ZSCloud. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Zscaler ZSCloud, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
