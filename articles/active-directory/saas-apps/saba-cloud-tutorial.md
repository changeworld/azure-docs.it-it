---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Saba cloud | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e Saba cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 5ce9eb41755d7faa2ce00b38dfd971313443bfb7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572694"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Saba cloud

In questa esercitazione si apprenderà come integrare Saba cloud con Azure Active Directory (Azure AD). Quando si integra Saba cloud con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a Saba cloud.
* Abilitare gli utenti per l'accesso automatico a Saba cloud con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Saba cloud Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Il cloud Saba supporta la funzionalità SSO avviata da **SP e IDP** .
* Il cloud Saba supporta il provisioning degli utenti **just-in-Time** .
* È ora possibile configurare l'applicazione cloud per dispositivi mobili Saba con Azure AD per l'abilitazione di SSO. In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

## <a name="adding-saba-cloud-from-the-gallery"></a>Aggiunta di Saba cloud dalla raccolta

Per configurare l'integrazione di Saba cloud in Azure AD, è necessario aggiungere Saba cloud dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Saba cloud** nella casella di ricerca.
1. Selezionare **Saba cloud** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Configurare e testare Azure AD SSO per il cloud Saba

Configurare e testare Azure AD SSO con Saba cloud usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Saba cloud.

Per configurare e testare Azure AD SSO con Saba cloud, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Saba cloud SSO](#configure-saba-cloud-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Saba cloud](#create-saba-cloud-test-user)** : per avere una controparte di B. Simon in Saba cloud collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.
1. **[Test SSO for Saba cloud (mobile)](#test-sso-for-saba-cloud-mobile)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Saba cloud** del portale di Azure trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `<CUSTOMER_NAME>_SPLN_PRINCIPLE`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<SIGN-ON URL>/Saba/saml/SSO/alias/<ENTITY_ID>`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<CUSTOMER_NAME>.sabacloud.com`

    b. Nella casella di testo **stato dell'inoltro** Digitare un URL usando il modello seguente: `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` o, nel caso in cui sia configurato SAML per un microsito, digitare un URL usando il modello seguente: `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > Per ulteriori informazioni sulla configurazione del RelayState, fare riferimento a [questo](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html) collegamento.

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornare i valori con l'identificatore, l'URL di risposta, l'URL di accesso e lo stato dell'inoltro effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Saba cloud](mailto:support@saba.com) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **configurare il cloud di Saba** copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Saba cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Saba cloud**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-saba-cloud-sso"></a>Configurare Saba cloud SSO

1. Accedere al sito aziendale di Saba cloud come amministratore.
1. Fare clic sull'icona del **menu** , fare clic su **amministratore** e quindi selezionare scheda **amministratore di sistema** .

    ![screenshot per l'amministratore di sistema](./media/saba-cloud-tutorial/system.png)

1. In **Configura sistema** selezionare **SAML SSO Setup** e fare clic su **Setup SAML SSO** Button. 

    ![screenshot per la configurazione](./media/saba-cloud-tutorial/configure.png)

1. Nella finestra popup selezionare **microsito** nell'elenco a discesa e fare clic su **Aggiungi e configura**.

    ![screenshot per Aggiungi sito/microsito](./media/saba-cloud-tutorial/microsite.png)

1. Nella sezione **Configura IDP** fare clic su **Sfoglia** per caricare il file **XML dei metadati di Federazione** scaricato dal portale di Azure. Abilitare la casella di controllo **IDP specifico del sito** e fare clic su **Importa**.

    ![screenshot per l'importazione di certificati](./media/saba-cloud-tutorial/certificate.png) 

1. Nella sezione **Configura SP** copiare il valore dell' **alias dell'entità** e incollare questo valore nella casella di testo **identificatore (ID entità)** nella sezione **configurazione SAML di base** nel portale di Azure. Fare clic su **genera**.

    ![screenshot per Configure SP](./media/saba-cloud-tutorial/generate-metadata.png) 

1. Nella sezione **Configura proprietà** verificare i campi popolati e fare clic su **Salva**. 

    ![screenshot per la configurazione delle proprietà](./media/saba-cloud-tutorial/configure-properties.png) 

### <a name="create-saba-cloud-test-user"></a>Creare un utente di test di Saba cloud

In questa sezione viene creato un utente di nome Britta Simon in Saba cloud. Il cloud Saba supporta il provisioning degli utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già in Saba cloud, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Per abilitare il provisioning degli utenti just-in-Time SAML con Saba cloud, fare riferimento a [questa](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html) documentazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso a Saba cloud in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso a Saba cloud e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al cloud Saba per il quale si configura l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Saba cloud in app personali, se configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al cloud Saba per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

> [!NOTE]
> Se l'URL di accesso non viene popolato in Azure AD, l'applicazione viene considerata come modalità avviata da IDP e se l'URL di accesso viene popolato, Azure AD reindirizza sempre l'utente all'applicazione cloud Saba per il flusso avviato dal provider di servizi.

## <a name="test-sso-for-saba-cloud-mobile"></a>Test SSO for Saba cloud (mobile)

1. Aprire l'applicazione cloud per dispositivi mobili di Saba, assegnare il **nome del sito** nella casella di testo e fare clic su **invio**.

    ![Screenshot per il nome del sito.](./media/saba-cloud-tutorial/site-name.png)

1. Immettere l' **indirizzo di posta elettronica** e fare clic su **Avanti**.

    ![Screenshot per l'indirizzo di posta elettronica.](./media/saba-cloud-tutorial/email-address.png)

1. Infine, dopo aver eseguito l'accesso, verrà visualizzata la pagina dell'applicazione.

    ![Screenshot per l'accesso riuscito.](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo la configurazione di Saba cloud è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


