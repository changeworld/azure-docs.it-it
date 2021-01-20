---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler Internet Access Administrator | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler Internet Access Administrator.
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
ms.openlocfilehash: c3a2f2b04827fac06a0729e45b46765928aedd34
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539787"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler Internet Access Administrator

Questa esercitazione illustra come integrare zScaler Internet Access Administrator con Azure Active Directory (Azure AD). Quando si integra zScaler Internet Access Administrator con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a zScaler Internet Access Administrator.
* Consentire agli utenti di accedere automaticamente a zScaler Internet Access Administrator con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zscaler Internet Access Administrator sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Zscaler Internet Access Administrator

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zscaler Internet Access Administrator supporta SSO avviato da **IDP**

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Aggiunta di Zscaler Internet Access Administrator dalla raccolta

Per configurare l'integrazione di Zscaler Internet Access Administrator in Azure AD è necessario aggiungere Zscaler Internet Access Administrator dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **zScaler Internet Access Administrator** nella casella di ricerca.
1. Selezionare **zScaler Internet Access Administrator** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Configurare e testare Azure AD SSO per zScaler Internet Access Administrator

Configurare e testare Azure AD SSO con zScaler Internet Access Administrator usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in zScaler Internet Access Administrator.

Per configurare e testare Azure AD SSO con zScaler Internet Access Administrator, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. Configurare l'accesso Single Sign-on **[amministratore di zScaler Internet](#configure-zscaler-internet-access-administrator-sso)** per configurare le impostazioni di Sign-On singola sul lato applicazione.
    1. **[Creare un utente test di Zscaler Internet Access Administrator](#create-zscaler-internet-access-administrator-test-user)** : per avere una controparte di Britta Simon in Zscaler Internet Access Administrator collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **zScaler Internet Access Administrator** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore** digitare un URL in base alle esigenze:

    | Identificatore |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Nella casella di testo **URL di risposta** digitare un URL in base alle esigenze:

    | URL di risposta |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. L'applicazione Zscaler Internet Access Administrator prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente e attestazioni** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente e attestazioni**.

    ![Collegamento Attributi](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome  | Attributo di origine  |
    | ---------| ------------ |
    | Ruolo | user.assignedroles |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    b. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo.

    c. Fare clic su **OK**.

    d. Fare clic su **Salva**.

    > [!NOTE]
    > Fare clic [qui](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) per informazioni su come configurare un ruolo in Azure AD.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Configura Zscaler Internet Access Administrator** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a zScaler Internet Access Administrator.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **zScaler Internet Access Administrator**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se i ruoli sono stati configurati come illustrato sopra, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.


## <a name="configure-zscaler-internet-access-administrator-sso"></a>Configurare SSO amministratore accesso Internet zScaler

1. In un'altra finestra del Web browser accedere all'interfaccia utente di amministrazione di Zscaler Internet Access Administrator.

2. Passare a **Amministrazione > Gestione amministratori**, eseguire i passaggi seguenti e fare clic su Salva:

    ![Screenshot che mostra la sezione Administrator Management con le opzioni per abilitare l'autenticazione SAML, caricare il certificato SSL e specificare un'autorità di certificazione.](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Amministrazione")

    a. Selezionare **Abilita autenticazione SAML**.

    b. Fare clic su **Carica** per caricare il certificato di firma di Azure SAML scaricato dal portale di Azure nel **certificato pubblico SSL**.

    c. Facoltativamente, per maggiore sicurezza, aggiungere i dettagli dell'**autorità emittente** per verificare l'autorità emittente della risposta SAML.

3. Nell'interfaccia utente di amministrazione seguire questa procedura:

    ![Screenshot che mostra l'interfaccia utente di amministrazione in cui è possibile eseguire i passaggi.](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Passare il mouse sul menu **Attivazione** nella parte inferiore sinistra.

    b. Fare clic su **Attiva**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Creare un utente di test di Zscaler Internet Access Administrator

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Zscaler Internet Access Administrator. Zscaler Internet Access Administrator non supporta il provisioning Just-In-Time (JIT) per l'accesso Single Sign-On. È necessario creare manualmente un account amministratore.
Per i passaggi su come creare un account amministratore, fare riferimento alla documentazione di Zscaler:

https://help.zscaler.com/zia/adding-admins

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente all'amministratore di accesso zScaler Internet per il quale si configura SSO

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro zScaler Internet Access Administrator in app personali, si dovrebbe accedere automaticamente all'amministratore di accesso a Internet zScaler per il quale si configura SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato zScaler Internet Access Administrator è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
