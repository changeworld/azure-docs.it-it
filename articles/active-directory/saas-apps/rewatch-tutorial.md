---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con rewatch | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e il controllo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: jeedes
ms.openlocfilehash: 032957b49c7ef9170a7e1aaa63eaa66a3c3e8401
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648528"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rewatch"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con rewatch

In questa esercitazione si apprenderà come integrare rewatch con Azure Active. Directory (Azure AD). Quando si integra rewatch con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a rewatch.
* Consentire agli utenti di eseguire l'accesso automaticamente per eseguire il monitoraggio con gli account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Rewatch Single Sign-On sottoscrizione abilitata per l'accesso SSO.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Rewatch supporta SSO avviato da **SP e IDP**
* Il monitoraggio supporta il provisioning degli utenti **just-in-Time**

## <a name="adding-rewatch-from-the-gallery"></a>Aggiunta di rewatch dalla raccolta

Per configurare l'integrazione di rewatch in Azure AD, è necessario aggiungere rewatch dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** Digitare **rewatch** nella casella di ricerca.
1. Selezionare **rewatch** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-rewatch"></a>Configurare e testare Azure AD SSO per il rewatch

Configurare e testare Azure AD SSO con rewatch usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in rewatch.

Per configurare e testare Azure AD SSO con rewatch, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare il rewatch SSO](#configure-rewatch-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di rewatch](#create-rewatch-test-user)** : per avere una controparte di B. Simon in rewatch collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure nella pagina di integrazione dell'applicazione **rewatch** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://rewatch.tv/login`

1. L'applicazione rewatch prevede che le asserzioni SAML abbiano un formato specifico. è quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre a quanto sopra, l'applicazione rewatch prevede che venga restituito un numero maggiore di attributi nella risposta SAML, indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome |  Attributo di origine|
    | --------------- | --------- |
    | Gruppo | user.groups |


1. Fare clic su **Salva**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura rewatch** copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso per il rewatch.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **rewatch**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-rewatch-sso"></a>Configurare il rewatch SSO

1. In un'altra finestra del Web browser accedere al sito aziendale di rewatch come amministratore.

1. Fare clic su **console di amministrazione** nel menu a sinistra.

    ![Controllare la console di amministrazione nel home page.](./media/rewatch-tutorial/admin-console.png)

1. Passare alla pagina **sicurezza** ed eseguire i passaggi seguenti nella sezione **SAML Single Sign-on** .

    ![SAML Single Sign-On sezione.](./media/rewatch-tutorial/security.png)

    a. Nella casella di testo **IDP SSO target URL (URL di destinazione SSO** ) incollare il valore dell' **URL di accesso** copiato dal portale di Azure.

    b. Aprire il **certificato (base64)** scaricato dal portale di Azure nel blocco note e incollare il contenuto nella casella di testo **IDP certificate (certificato IDP** ).

    c. Selezionare **Abilita accesso SAML per questo canale** e fare clic su **Salva**.

### <a name="create-rewatch-test-user"></a>Creare un utente di test di rewatch

In questa sezione viene creato un utente di nome Britta Simon in rewatch. Rewatch supporta il provisioning utente JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già in rewatch, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. In questo modo verrà reindirizzato a rewatch URL di accesso in cui è possibile avviare il flusso di accesso.  

* Passare a rewatch URL di accesso diretto e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al rewatch per il quale si configura l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro rewatch in app personali, se configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente al rewatch per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il monitoraggio, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).