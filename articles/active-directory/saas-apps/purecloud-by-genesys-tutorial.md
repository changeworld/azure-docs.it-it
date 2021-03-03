---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con PureCloud by Genesys | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PureCloud by Genesys.
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
ms.openlocfilehash: 7c6f84ee3bb4920dbe57221b8b0bbf9f5880742b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653009"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con PureCloud by Genesys

Questa esercitazione descrive come integrare PureCloud by Genesys con Azure Active Directory (Azure AD). Integrando PureCloud by Genesys con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a PureCloud by Genesys.
* Abilitare gli utenti per l'accesso automatico a PureCloud by Genesys con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di PureCloud by Genesys abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* PureCloud by Genesys supporta l'accesso SSO avviato da **provider di servizi e provider di identità**.

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-purecloud-by-genesys-from-the-gallery"></a>Aggiungere PureCloud by Genesys dalla raccolta

Per configurare l'integrazione di PureCloud by Genesys in Azure AD, è necessario aggiungere PureCloud by Genesys dalla raccolta all'elenco di app SaaS gestite. A questo scopo, seguire questa procedura:

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **PureCloud by Genesys** nella casella di ricerca.
1. Selezionare **PureCloud by Genesys** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-purecloud-by-genesys"></a>Configurare e testare Azure AD SSO per PureCloud by Genesys

Configurare e testare l'accesso SSO di Azure AD con PureCloud by Genesys usando un utente di test di nome **B.Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PureCloud by Genesys.

Per configurare e testare Azure AD SSO con PureCloud by Genesys, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso SSO di PureCloud by Genesys](#configure-purecloud-by-genesys-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** per avere una controparte di B. Simon in PureCloud by Genesys collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **PureCloud by Genesys** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **provider di identità**:

    a. Nella casella **identificatore** immettere gli URL che corrispondono alla propria area geografica:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. Nella casella **URL di risposta** immettere gli URL che corrispondono alla propria area geografica:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Selezionare **Impostare URL aggiuntivi** e quindi seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **provider di servizi**:

    Nella casella **URL di accesso** immettere gli URL che corrispondono alla propria area geografica:
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. L'applicazione PureCloud by Genesys prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti:

    ![image](common/default-attributes.png)

1. L'applicazione PureCloud by Genesys prevede inoltre il passaggio di altri attributi nella risposta SAML, come illustrato nella tabella seguente. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base alle esigenze.

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | Email | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura PureCloud by Genesys** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure:

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere il nome utente nel formato seguente: username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a PureCloud by Genesys.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **PureCloud by Genesys**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-purecloud-by-genesys-sso"></a>Configurare l'accesso Single Sign-On di PureCloud by Genesys

1. In un'altra finestra del Web browser accedere a PureCloud by Genesys come amministratore.

1. Selezionare **Amministratore** nella parte superiore e passare a **Single Sign-On** in **Integrazioni**.

    ![Screenshot che mostra la finestra PureCloud Admin in cui è possibile selezionare Single Sign-on.](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. Passare alla scheda **ADFS/Azure AD (Premium)** e quindi seguire questa procedura:

    ![Screenshot che mostra la pagina Integrations in cui è possibile immettere i valori descritti.](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. Scegliere **Sfoglia** per caricare il certificato con codifica Base64 scaricato dal portale di Azure in **ADFS Certificate** (Certificato ADFS).

    b. Nella casella di testo **ADFS Issuer URI** (URI autorità di certificazione ADFS) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella **URI di destinazione** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Per il valore di **Relying Party Identifier** (Identificatore relying party) passare al portale di Azure e quindi nella pagina di integrazione dell'applicazione **PureCloud by Genesys** selezionare la scheda **Proprietà** e copiare il valore di **ID applicazione**. Incollare questo valore nella casella **Relying Party Identifier** (Identificatore relying party).

    ![Screenshot che mostra il riquadro delle proprietà in cui è possibile trovare il valore dell'ID applicazione.](./media/purecloud-by-genesys-tutorial/configure-6.png)

    e. Selezionare **Salva**.

### <a name="create-purecloud-by-genesys-test-user"></a>Creare l'utente di test di PureCloud by Genesys

Per consentire agli utenti di Azure AD di accedere a PureCloud by Genesys, è necessario effettuarne il provisioning in PureCloud by Genesys. Nel caso di PureCloud by Genesys il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere a PureCloud by Genesys come amministratore.

1. Selezionare **Amministratore** nella parte superiore e passare a **Persone** in **People & Permissions** (Persone e autorizzazioni).

    ![Screenshot che mostra la finestra PureCloud Admin in cui è possibile selezionare People.](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. Nella pagina **Persone** selezionare **Aggiungi persona**.

    ![Screenshot che mostra la pagina People in cui è possibile aggiungere una persona.](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. Nella finestra di dialogo **Add People to the Organization** (Aggiungi persone all'organizzazione) seguire questa procedura:

    ![Screenshot che mostra la pagina in cui è possibile immettere i valori descritti.](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. Nella casella **Nome completo** immettere il nome di un utente. Ad esempio: **B.simon**.

    b. Nella casella **Posta elettronica** immettere l'indirizzo di posta elettronica dell'utente, ad esempio **b.simon\@contoso.com**.

    c. Selezionare **Create** (Crea).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà reindirizzato all'URL di accesso di PureCloud by Genesys, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di PureCloud by Genesys e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente a PureCloud by Genesys per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro PureCloud by Genesys in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente al PureCloud di Genesys per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato PureCloud by Genesys è possibile imporre il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).