---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Perimeter 81 | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Perimeter 81.
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
ms.openlocfilehash: cd6ba1da92a19a1f73fc67c0165bfb19b3bb77aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363850"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeter-81"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Perimeter 81

Questa esercitazione descrive come integrare Perimeter 81 con Azure Active Directory (Azure AD). Integrando Perimeter 81 con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Perimeter 81.
* Abilitare gli utenti per l'accesso automatico a Perimeter 81 con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Perimeter 81 abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Perimeter 81 supporta l'accesso SSO avviato da **SP e IDP**
* Perimeter 81 supporta il provisioning utenti **JIT**

## <a name="adding-perimeter-81-from-the-gallery"></a>Aggiunta di Perimeter 81 dalla raccolta

Per configurare l'integrazione di Perimeter 81 in Azure AD, è necessario aggiungere Perimeter 81 dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Perimeter 81** nella casella di ricerca.
1. Selezionare **Perimeter 81** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-perimeter-81"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Perimeter 81

Configurare e testare l'accesso SSO di Azure AD con Perimeter 81 usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Perimeter 81.

Per configurare e testare l'accesso SSO di Azure AD con Perimeter 81, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Perimeter 81](#configure-perimeter-81-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Perimeter 81](#create-perimeter-81-test-user)** : per avere una controparte di B.Simon in Perimeter 81 collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Perimeter 81** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **identificatore** Digitare un valore usando il modello seguente: `urn:auth0:perimeter81:<SUBDOMAIN>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://auth.perimeter81.com/login/callback?connection=<SUBDOMAIN>`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.perimeter81.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere tali valori, contattare il [team di supporto clienti di Perimeter 81](mailto:support@perimeter81.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **set up perimetrale 81** copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Perimeter 81.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Perimeter 81**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-perimeter-81-sso"></a>Configurare l'accesso Single Sign-On di Perimeter 81

1. Per automatizzare la configurazione nel perimetro 81, è necessario installare l'estensione del browser per l' **accesso sicuro alle app** facendo clic su **installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo l'aggiunta dell'estensione al browser, fare clic su **Configura perimetrale 81** per indirizzare l'utente all'applicazione perimetrale 81. Da qui, fornire le credenziali di amministratore per accedere al perimetro 81. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare il perimetro 81 manualmente, in un'altra finestra del Web browser accedere al sito della società perimetrale 81 come amministratore.

4. Passare a **Impostazioni** e fare clic su **provider di identità**.

    ![Impostazioni perimetrali 81](./media/perimeter-81-tutorial/settings.png)

5. Fare clic sul pulsante **Aggiungi provider** .

    ![Perimetro 81 Aggiungi provider](./media/perimeter-81-tutorial/add-provider.png)

6. Selezionare **SAML 2,0 Identity Providers** e fare clic sul pulsante **continue (continua** ).

    ![Perimetrale 81 Aggiungi provider di identità](./media/perimeter-81-tutorial/add-identity-provider.png)

7. Nella sezione **SAML 2,0 Identity Providers** seguire questa procedura:

    ![Perimetrale 81 configurazione di SAML](./media/perimeter-81-tutorial/setting-up-saml.png)

    a. Nella casella di testo **URL** di accesso incollare il valore dell' **URL di accesso** copiato da portale di Azure.

    b. Nella casella di testo **Domain ALIASS** immettere il valore dell'alias di dominio.

    c. Aprire il **certificato (base64)** scaricato dal portale di Azure nel blocco note e incollare il contenuto nella casella di testo **certificato di firma X509** .

    > [!NOTE]
    > In alternativa, è possibile fare clic su **Carica file PEM/CERT** per caricare il **certificato (base64)** scaricato da portale di Azure.
    
    d. Fare clic su **Done**.

### <a name="create-perimeter-81-test-user"></a>Creare l'utente di test di Perimeter 81

In questa sezione viene creato un utente di nome Britta Simon in Perimeter 81. Perimeter 81 supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Perimeter 81, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Perimeter 81, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Perimeter 81 e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al perimetro 81 per il quale si configura l'accesso SSO.

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Perimeter 81 in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Perimeter 81 per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Perimeter 81, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
