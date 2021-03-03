---
title: 'Esercitazione: Integrazione di Azure Active Directory con UNIFI | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e UNIFI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 1fa1c40c96694b71bc762050998257102b687c18
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650627"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Esercitazione: Integrazione di Azure Active Directory con UNIFI

In questa esercitazione si apprenderà come integrare UNIFI con Azure Active Directory (Azure AD). Quando si integra UNIFI con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a UNIFI.
* Consentire agli utenti di eseguire l'accesso automatico a UNIFI con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per Single Sign-On UNIFI (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* UNIFI supporta il protocollo SSO avviato da **SP e IDP** .
* UNIFI supporta il provisioning **automatizzato** degli utenti.

## <a name="add-unifi-from-the-gallery"></a>Aggiungere UNIFI dalla raccolta

Per configurare l'integrazione di UNIFI in Azure AD, è necessario aggiungere UNIFI dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **UniFi** nella casella di ricerca.
1. Selezionare **UniFi** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-unifi"></a>Configurare e testare Azure AD SSO per UNIFI

Configurare e testare Azure AD SSO con UNIFI usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in UNIFI.

Per configurare e testare Azure AD SSO con UNIFI, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso SSO UniFi](#configure-unifi-sso)** per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di unifi](#create-unifi-test-user)** : per avere una controparte di B. Simon in UniFi collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure nella pagina di integrazione dell'applicazione **UniFi** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    Nella casella di testo **Identificatore** digitare l'URL: `INVIEWlabs`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://app.discoverunifi.com/login`

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura UNIFI** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a UNIFI.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **UNIFI**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-unifi-sso"></a>Configurare SSO UNIFI

1. In un'altra finestra del browser Web accedere al sito aziendale di **UNIFI** come amministratore.

2. Fare clic su **Users** (Utenti).

    ![Screenshot che mostra l'opzione Users selezionata nel sito UNIFI.](./media/unifi-tutorial/app-1.png)

3. Fare clic su **Add New Identity Provider** (Aggiungi nuovo provider di identità).

    ![Screenshot che mostra l'opzione Add New Identity Provider selezionata.](./media/unifi-tutorial/app-2.png)

4. Nella sezione **Add New Identity Provider** (Aggiungi nuovo provider di identità) seguire questa procedura:

    ![Screenshot che mostra la pagina Add Identity Provider in cui è possibile immettere i valori indicati.](./media/unifi-tutorial/app-3.png) 

    a. Nella casella di testo **Provider Name** (Nome provider) digitare il nome del provider di identità.

    b. Nella casella di testo **Provider URL** (URL del provider) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Aprire il certificato scaricato dal portale di Azure nel blocco note, rimuovere i tag **---BEGIN CERTIFICATE---** e **---END CERTIFICATE---** e quindi incollare il contenuto rimanente nella casella di testo **Certificate** (Certificato).

    d. Selezionare la casella di controllo **is Default Provider** (Provider predefinito).

### <a name="create-unifi-test-user"></a>Creare l'utente di test di UNIFI

In questa sezione viene creato un utente di nome Britta Simon. **UNIFI** supporta il provisioning automatico dell'utente, pertanto non è necessaria la procedura manuale. Gli utenti vengono creati automaticamente al termine dell'autenticazione da Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di UNIFI in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di UNIFI e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente a UniFi per cui è stato configurato l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro UNIFI in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente a UNIFI per cui si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato UNIFI è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).