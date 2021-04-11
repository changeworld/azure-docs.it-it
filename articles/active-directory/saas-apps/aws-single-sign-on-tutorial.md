---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con AWS Single Sign-on | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e Single Sign-on di AWS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 2d0b9e45dc5de0cd4550cf4b9f944fd33ebd7e7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720691"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con AWS Single Sign-on

In questa esercitazione si apprenderà come integrare AWS Single Sign-on con Azure Active Directory (Azure AD). Quando si integra AWS Single Sign-on con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a AWS Single Sign-on.
* Consentire agli utenti di accedere automaticamente a AWS Single Sign-on con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di AWS abilitata per l'accesso Single Sign-on Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* AWS Single Sign-on supporta la funzionalità SSO avviata da **SP e IDP** .

* AWS Single Sign-on supporta il [**provisioning automatizzato degli utenti**](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Aggiunta di AWS Single Sign-on dalla raccolta

Per configurare l'integrazione di AWS Single Sign-on in Azure AD, è necessario aggiungere AWS Single Sign-on dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **AWS Single Sign-on** nella casella di ricerca.
1. Selezionare **AWS Single Sign-on nel** pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Configurare e testare l'accesso Single Sign-on di Azure AD SSO for AWS

Configurare e testare Azure AD SSO con l'accesso Single Sign-on di AWS usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in AWS Single Sign-on.

Per configurare e testare Azure AD SSO con l'accesso Single Sign-on di AWS, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare AWS SSO Single Sign-on](#configure-aws-single-sign-on-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di AWS Single Sign-on](#create-aws-single-sign-on-test-user)** : per avere una controparte di B. Simon in AWS Single Sign-on collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Single Sign-on di AWS** della portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Se si dispone di un **file di metadati del provider di servizi**, nella sezione **configurazione SAML di base** seguire questa procedura:

    a. Fare clic su **Carica il file di metadati**.

    b. Fare clic sul **logo della cartella** per selezionare il file di metadati scaricato dalla sezione **Configure AWS Single Sign-on SSO** (punto 8) e fare clic su **Aggiungi**.

    ![Immagine2](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione Configurazione SAML di base:

    ![immagine3](common/idp-intiated.png)

    > [!Note]
    > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, inserirli manualmente in base alle proprie esigenze.

1. Se non si dispone di un **file di metadati del provider di servizi**, seguire questa procedura nella sezione **configurazione SAML di base** , se si vuole configurare l'applicazione in modalità avviata da **IDP** , immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di AWS Single Sign-on](mailto:aws-sso-partners@amazon.com) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Single Sign-on di AWS prevede un formato specifico per le asserzioni SAML. è quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > Se ABAC è abilitato in AWS SSO, gli attributi aggiuntivi possono essere passati come tag di sessione direttamente negli account AWS.

1. Nella sezione **certificato di firma SAML** della pagina **impostare Single Sign-on con SAML** individuare **certificato (base64)** e selezionare **download** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **set up AWS Single Sign-on** copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a AWS Single Sign-on.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **AWS Single Sign-on**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-aws-single-sign-on-sso"></a>Configurare SSO Single Sign-on di AWS

1. Per automatizzare la configurazione all'interno di AWS Single Sign-on, è necessario installare l'estensione del browser per l' **accesso sicuro di app personali** facendo clic su **installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo l'aggiunta dell'estensione al browser, fare clic su **Configura AWS Single Sign-on** per indirizzare l'utente all'applicazione Single Sign-on di AWS. Da qui, fornire le credenziali di amministratore per l'accesso a AWS Single Sign-on. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 10.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare l'accesso Single Sign-on di AWS manualmente, in un'altra finestra del Web browser accedere al sito della società Single Sign-on di AWS come amministratore.

1. Passare a **Servizi-> sicurezza, identità, & conformità-> AWS Single Sign-on**.
2. Nel riquadro di spostamento a sinistra scegliere **Impostazioni**.
3. Nella pagina **Impostazioni** individuare **origine identità** e fare clic su **modifica**.

    ![Schermata per il servizio di modifica dell'origine identità](./media/aws-single-sign-on-tutorial/settings.png)

4. In modifica origine identità scegliere provider di **identità esterno**.

    
    ![Schermata per la selezione della sezione del provider di identità esterno](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. Eseguire i passaggi seguenti nella sezione **configurare il provider di identità esterno** :

    ![Screenshot per scaricare e caricare la sezione dei metadati](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. Nella sezione **metadati del provider di servizi** individuare i **metadati SAML di AWS SSO** e selezionare **Scarica file di metadati** per scaricare il file di metadati e salvarlo nel computer e usare questo file di metadati per caricare il portale di Azure.

    b. Copiare il valore dell' **URL di accesso SSO di AWS** , incollare questo valore nella casella di testo **URL di accesso** nella **sezione configurazione SAML di base** nel portale di Azure.

    c. Nella sezione **metadati del provider di identità** scegliere **Sfoglia** per caricare il file di metadati scaricato dal portale di Azure.

    d. Scegliere **Avanti: Revisione**.

8. Nella casella di testo digitare **Accept** per modificare l'origine dell'identità.

    ![Screenshot per confermare la configurazione](./media/aws-single-sign-on-tutorial/accept.png)

9. Fare clic su **Cambia origine identità**.

### <a name="create-aws-single-sign-on-test-user"></a>Creare un utente di test di AWS Single Sign-on

1. Aprire la **console SSO di AWS**.

2. Nel riquadro di spostamento a sinistra scegliere **utenti**.

3. Nella pagina utenti scegliere **Aggiungi utente**.

4. Nella pagina Aggiungi utente, attenersi alla seguente procedura:

    a. Nel campo **username (nome utente** ) immettere B. Simon.

    b. Nel campo **indirizzo di posta elettronica** immettere il `username@companydomain.extension` . Ad esempio: `B.Simon@contoso.com`.

    c. Nel campo **Conferma indirizzo di posta elettronica** immettere nuovamente l'indirizzo di posta elettronica del passaggio precedente.

    d. Nel campo First Name (nome) immettere `Jane` .

    e. Nel campo Last Name (cognome) immettere `Doe` .

    f. Nel campo nome visualizzato immettere `Jane Doe` .

    g. Scegliere **Avanti: gruppi**.

    > [!NOTE]
    > Verificare che il nome utente immesso in AWS SSO corrisponda al nome di accesso Azure AD dell'utente. Questo consente di evitare eventuali problemi di autenticazione.

5. Scegliere **Aggiungi utente**.
6. Successivamente, l'utente verrà assegnato all'account AWS. A tale scopo, nel riquadro di spostamento sinistro della console SSO di AWS scegliere **account AWS**.
7. Nella pagina account AWS selezionare la scheda organizzazione AWS, quindi selezionare la casella accanto all'account AWS da assegnare all'utente. Scegliere quindi **Assegna utenti**.
8. Nella pagina Assegna utenti individuare e selezionare la casella accanto all'utente B. Simon. Quindi scegliere **Avanti: set di autorizzazioni**.
9. Nella sezione selezionare i set di autorizzazioni selezionare la casella accanto al set di autorizzazioni che si vuole assegnare all'utente B. Simon. Se non si dispone di un set di autorizzazioni esistente, scegliere **Crea nuovo set di autorizzazioni**.

    > [!NOTE]
    > I set di autorizzazioni definiscono il livello di accesso di utenti e gruppi a un account AWS. Per altre informazioni sui set di autorizzazioni, vedere la pagina **set di autorizzazioni** SSO di AWS.
10. Scegliere **Fine**.

> [!NOTE]
> AWS Single Sign-on supporta anche il provisioning utenti automatico [. per ulteriori](./aws-single-sign-on-provisioning-tutorial.md) informazioni, vedere la pagina relativa alla configurazione del provisioning utenti automatico.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso Single Sign-on di AWS in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso Single Sign-on di AWS e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al Single Sign-on di AWS per il quale si configura SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro AWS Single Sign-on in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente al Single Sign-on di AWS per il quale si configura SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato l'accesso Single Sign-on di AWS, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).