---
title: 'Esercitazione: integrazione di Azure Active Directory con SAP Analytics cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Analytics Cloud.
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
ms.openlocfilehash: 19bdb6d2a586dcb279687673c7fa4e302dc4928b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652641"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Esercitazione: integrare SAP Analytics cloud con Azure Active Directory

Questa esercitazione descrive come integrare SAP Analytics Cloud con Azure Active Directory (Azure AD). Integrando SAP Analytics Cloud con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SAP Analytics Cloud.
* Abilitare gli utenti per l'accesso automatico a SAP Analytics Cloud con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SAP Analytics Cloud abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Analytics cloud supporta l'accesso SSO avviato da **SP** .

## <a name="add-sap-analytics-cloud-from-the-gallery"></a>Aggiungere SAP Analytics cloud dalla raccolta

Per configurare l'integrazione di SAP Analytics Cloud in Azure AD, è necessario aggiungere SAP Analytics Cloud dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAP Analytics Cloud** nella casella di ricerca.
1. Selezionare **SAP Analytics Cloud** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-sap-analytics-cloud"></a>Configurare e testare Azure AD SSO per SAP Analytics cloud

Configurare e testare l'accesso Single Sign-On di Azure AD con SAP Analytics Cloud usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Analytics Cloud.

Per configurare e testare Azure AD SSO con SAP Analytics cloud, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare SAP Analytics cloud SSO](#configure-sap-analytics-cloud-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare l'utente di test di SAP Analytics Cloud](#create-sap-analytics-cloud-test-user)**: per avere una controparte di B.Simon in SAP Analytics Cloud collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SAP Analytics cloud** della portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** Digitare un URL usando uno dei modelli seguenti:

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL in uno dei formati seguenti:

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > I valori in questi URL sono forniti solo a scopo dimostrativo. Aggiornare i valori con l'URL di accesso e l'URL dell'identificatore effettivi. Per ottenere l'URL di accesso, contattare il [team di supporto clienti di SAP Analytics Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Per ottenere l'URL dell'identificatore, scaricare i metadati di SAP Analytics Cloud dalla console di amministrazione. Questo concetto verrà spiegato più avanti nell'esercitazione.

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura SAP Analytics Cloud** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP Analytics Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAP Analytics Cloud**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sap-analytics-cloud-sso"></a>Configurare l'accesso SSO di SAP Analytics Cloud

1. In un'altra finestra del Web browser accedere al sito aziendale di SAP Analytics Cloud come amministratore.

2. Selezionare **menu**  >    >  **Amministrazione** sistema.
    
    ![Selezionare Menu, quindi Sistema e infine Amministrazione](./media/sapboc-tutorial/configure-1.png)

3. Selezionare l'icona **Modifica** (penna) nella scheda **Sicurezza**.
    
    ![Selezionare l'icona Modifica nella scheda Sicurezza](./media/sapboc-tutorial/configure-2.png)  

4. Per **Metodo di autenticazione** selezionare **SAML Single Sign-On (SSO)**.

    ![Selezionare SAML Single Sign-On per il metodo di autenticazione](./media/sapboc-tutorial/configure-3.png)  

5. Per scaricare i metadati del provider di servizi (passaggio 1), selezionare **Download**. Nel file di metadati trovare e copiare il valore **entityID**. Nella finestra di dialogo **Configurazione SAML di base** del portale di Azure incollare il valore nella casella **Identificatore**.

    ![Copiare e incollare il valore di entityID](./media/sapboc-tutorial/configure-4.png)  

6. Per caricare i metadati del provider di servizi (passaggio 2) nel file scaricato nel portale di Azure, in **Upload Identity Provider metadata** (Caricare i metadati del provider di identità) selezionare **Carica**.  

    ![In Upload Identity Provider metadata (Caricare i metadati del provider di identità) selezionare Carica](./media/sapboc-tutorial/configure-5.png)

7. Nell'elenco **Attributo utente** selezionare l'attributo utente (passaggio 3) da usare per l'implementazione. Questo attributo utente esegue il mapping al provider di identità. Per immettere un attributo personalizzato nella pagina dell'utente, usare l'opzione **Custom SAML Mapping** (Mapping SAML personalizzato). oppure selezionare **Posta elettronica** o **ID UTENTE** come attributo utente. Nell'esempio è stato selezionato **Posta elettronica** perché è stato eseguito il mapping dell'attestazione dell'ID utente con l'attributo **userprincipalname** nella sezione **Attributi utente e attestazioni** del portale di Azure. Si ottiene così un indirizzo di posta elettronica dell'utente univoco, che viene inviato all'applicazione SAP Analytics Cloud in ogni risposta SAML con esito positivo.

    ![Selezionare Attributo utente](./media/sapboc-tutorial/configure-6.png)

8. Per verificare l'account con il provider di identità (passaggio 4), nella casella **Login Credential (Email)** (Credenziale di accesso - Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente. Selezionare quindi **Verifica account**. Il sistema aggiunge le credenziali di accesso all'account utente.

    ![Immettere l'indirizzo di posta elettronica e selezionare Verifica account](./media/sapboc-tutorial/configure-7.png)

9. Selezionare l'icona **Salva**.

    ![Icona Salva](./media/sapboc-tutorial/save.png)

### <a name="create-sap-analytics-cloud-test-user"></a>Creare l'utente di test di SAP Analytics Cloud

È necessario effettuare il provisioning degli utenti di Azure AD in SAP Analytics Cloud perché possano accedere a SAP Business Object Cloud. In SAP Analytics Cloud il provisioning è un'attività manuale.

Per effettuare il provisioning di un account utente:

1. Accedere al sito aziendale di SAP Analytics Cloud come amministratore.

2. Selezionare **menu**  >  **sicurezza**  >  **utenti**.

    ![Aggiungere un dipendente](./media/sapboc-tutorial/user-1.png)

3. Nella pagina **Users** (Utenti) selezionare **+** per aggiungere i dettagli di un nuovo utente. 

    ![Pagina Aggiungi utenti (Aggiungi utenti)](./media/sapboc-tutorial/user-4.png)

    Completare quindi i passaggi seguenti:

    1. Nella casella **USER ID** (ID UTENTE) immettere l'ID dell'utente, ad esempio **B**.

    1. Nella casella **NAME** (NOME) immettere il nome dell'utente, ad esempio **B**.

    1. Nella casella **LAST NAME** (COGNOME) immettere il cognome dell'utente, ad esempio **Simon**.

    1. Nella casella **DISPLAY NAME** (NOME VISUALIZZATO) immettere nome e cognome dell'utente, ad esempio **B.Simon**.

    1. Nella casella **E-MAIL** (POSTA ELETTRONICA) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `b.simon@contoso.com`.

    1. Nella pagina **Select Roles** (Selezione ruoli) selezionare il ruolo appropriato per l'utente e quindi fare clic su **OK**.

        ![Seleziona il ruolo](./media/sapboc-tutorial/user-3.png)

    1. Selezionare l'icona **Salva**.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso cloud di SAP Analytics in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL accesso cloud di SAP Analytics e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro SAP Analytics cloud in app personali, questo verrà reindirizzato all'URL di SAP Analytics cloud Sign-on. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato SAP Analytics cloud, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).