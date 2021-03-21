---
title: 'Esercitazione: Integrazione di Azure Active Directory con LaunchDarkly | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LaunchDarkly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ddfffb77ca889aea9ff32c7be1ce2e4cb7fc04a7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037607"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Esercitazione: Integrazione di Azure Active Directory con LaunchDarkly

Questa esercitazione illustra come integrare LaunchDarkly con Azure Active Directory (Azure AD). Quando si integra LaunchDarkly con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a LaunchDarkly.
* Abilitare gli utenti per l'accesso automatico a LaunchDarkly con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

    > [!NOTE]
    > L'integrazione Azure Active Directory LaunchDarkly è unidirezionale. Dopo aver configurato l'integrazione, è possibile usare Azure AD per gestire utenti, SSO e account in LaunchDarkly, ma non è **possibile** usare LaunchDarkly per gestire utenti, SSO e account in Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di LaunchDarkly Single Sign-On abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* LaunchDarkly supporta SSO avviato da **IDP** .
* LaunchDarkly supporta il provisioning degli utenti **just-in-Time** .

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-launchdarkly-from-the-gallery"></a>Aggiungere LaunchDarkly dalla raccolta

Per configurare l'integrazione di LaunchDarkly in Azure AD, è necessario aggiungere LaunchDarkly dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **LaunchDarkly** nella casella di ricerca.
1. Selezionare **LaunchDarkly** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Configurare e testare Azure AD SSO per LaunchDarkly

Configurare e testare Azure AD SSO con LaunchDarkly usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in LaunchDarkly.

Per configurare e testare Azure AD SSO con LaunchDarkly, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare LAUNCHDARKLY SSO](#configure-launchdarkly-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di LaunchDarkly](#create-launchdarkly-test-user)** : per avere una controparte di B. Simon in LaunchDarkly collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **LaunchDarkly** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **Identificatore** digitare l'URL: `app.launchdarkly.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. È necessario aggiornarlo con l'URL di risposta reale, descritto più avanti nell'esercitazione. Se si intende usare l'applicazione in modalità **IdP** occorre lasciare vuoto il campo **URL di accesso**, altrimenti non sarà possibile avviare l'accesso dall'**IdP**. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura LaunchDarkly** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a LaunchDarkly.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **LaunchDarkly**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-launchdarkly-sso"></a>Configurare LaunchDarkly SSO

1. In un'altra finestra del Web browser accedere al sito aziendale di LaunchDarkly come amministratore.

2. Selezionare **Account Settings** (Impostazioni account) nel pannello di spostamento a sinistra.

    ![Screenshot che mostra la voce Account Settings selezionata in Production.](./media/launchdarkly-tutorial/configure-1.png)

3. Fare clic sulla scheda **Security** (Sicurezza).

    ![Screenshot che mostra la scheda Security in Account settings.](./media/launchdarkly-tutorial/configure-2.png)

4. Fare clic su **ENABLE SSO** (ABILITA SSO) e quindi su **EDIT SAML CONFIGURATION** (MODIFICA CONFIGURAZIONE SAML).

    ![Screenshot che mostra la pagina Single Sign-On in cui è possibile scegliere ENABLE S S O e EDIT SAML CONFIGURATION.](./media/launchdarkly-tutorial/configure-3.png)

5. Nella sezione **Edit your SAML configuration** (Modifica la configurazione SAML) seguire questa procedura:

    ![Screenshot che mostra la sezione Edit your SAML configuration in cui è possibile apportare le modifiche descritte qui.](./media/launchdarkly-tutorial/configure-4.png)

    a. Copiare l'**URL del servizio consumer di SAML** dell'istanza in uso e incollarlo nella casella di testo URL di risposta della sezione **URL e dominio LaunchDarkly** del portale di Azure.

    b. Nella casella di testo **URL di accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Aprire il certificato scaricato dal portale di Azure nel Blocco note, copiare il contenuto e quindi incollarlo nella casella **X.509 certificate** (Certificato X.509) oppure caricare direttamente il certificato facendo clic sul collegamento **upload one** (caricarne uno).

    d. Fare clic su **Salva**.

### <a name="create-launchdarkly-test-user"></a>Creare un utente di test di LaunchDarkly

In questa sezione viene creato un utente denominato B. Simon in LaunchDarkly. LaunchDarkly supporta il provisioning utente just-in-time, abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già in LaunchDarkly, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente al LaunchDarkly per il quale si configura l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro LaunchDarkly in My Apps (app personali), si dovrebbe accedere automaticamente al LaunchDarkly per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato LaunchDarkly, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
