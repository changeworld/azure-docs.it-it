---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con NS1 SSO for Azure | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e NS1 SSO for Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: a7fad0af26d15bc77a52d55309c594937ba3be13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518620"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con NS1 SSO for Azure

Questa esercitazione descrive come integrare NS1 SSO for Azure con Azure Active Directory (Azure AD). Integrando NS1 SSO for Azure con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a NS1 SSO for Azure.
* Consentire agli utenti di eseguire l'accesso automatico a NS1 SSO for Azure con i relativi account Azure AD.
* Gestire gli account in una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di NS1 SSO for Azure abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* NS1 SSO for Azure supporta la funzionalità SSO avviata da SP e IDP.
* Dopo aver configurato NS1 SSO for Azure, è possibile applicare il controllo della sessione. che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>Aggiungere NS1 SSO for Azure dalla raccolta

Per configurare l'integrazione di NS1 SSO for Azure in Azure AD, è necessario aggiungere NS1 SSO for Azure dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **NS1 SSO for Azure** nella casella di ricerca.
1. Selezionare **NS1 SSO for Azure** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per NS1 SSO for Azure

Configurare e testare Azure AD SSO con NS1 SSO for Azure usando un utente test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, stabilire una relazione collegata tra un utente Azure AD e l'utente correlato in NS1 SSO for Azure.

Ecco i passaggi generali per configurare e testare Azure AD SSO con NS1 SSO for Azure:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.

    a. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.

    b. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare NS1 SSO for Azure SSO](#configure-ns1-sso-for-azure-sso)** per configurare le impostazioni di Single Sign-on sul lato applicazione.

    a. **[Creare un utente di test di NS1 SSO for Azure](#create-an-ns1-sso-for-azure-test-user)** per avere una controparte di B. Simon in NS1 SSO for Azure. Tale controparte è collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **NS1 SSO for Azure** della [portale di Azure](https://portal.azure.com/)trovare la sezione **Gestisci** . e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML, in cui è evidenziata l'icona della matita](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **identificatore** Digitare l'URL seguente: `https://api.nsone.net/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://api.nsone.net/saml/sso/<ssoid>`

1. Selezionare **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** Digitare l'URL seguente:  `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. Aggiornare questo valore con l'URL di risposta effettivo. Per ottenere il valore, contattare il [team di supporto clienti di NS1 SSO for Azure](mailto:techops@nsone.net) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione NS1 SSO for Azure prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **attributi utente & attestazioni** nella pagina di integrazione dell'applicazione. Nella pagina **Configura singolo Sign-On con SAML** selezionare l'icona della matita per aprire la finestra di dialogo **attributi utente** .

    ![Screenshot degli attributi utente & sezione delle attestazioni con icona a matita evidenziata](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Selezionare il nome dell'attributo per modificare l'attestazione.

    ![Screenshot degli attributi utente & sezione Claims, con il nome dell'attributo evidenziato](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Selezionare **trasformazione**.

    ![Screenshot della sezione Gestisci attestazioni con trasformazione evidenziata](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Nella sezione **Manage Transformation** eseguire la procedura seguente:

    ![Screenshot della sezione Gestisci trasformazione con diversi campi evidenziati](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Selezionare **ExactMailPrefix()** come valore di **Trasformazione**.

    1. Selezionare **user.userprincipalname** come valore di **Parametro 1**.

    1. Selezionare **Aggiungi**.

    1. Selezionare **Salva**.

1. Nella sezione **certificato di firma SAML** della pagina **impostare Single Sign-on con SAML** selezionare il pulsante copia. per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Screenshot della sezione Certificato di firma SAML con il pulsante di copia evidenziato](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:

   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nel campo **Password**.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione viene abilitato B. Simon per l'uso di Azure Single Sign-On concedendo l'accesso a NS1 SSO for Azure.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **NS1 SSO for Azure**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Screenshot della sezione Gestione con Utenti e gruppi evidenziato](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Screenshot della pagina Utenti e gruppi, in cui è evidenziato il pulsante Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare quindi clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Configurare l'accesso Single Sign-On di NS1 SSO for Azure

Per configurare Single Sign-On sul lato NS1 SSO for Azure, è necessario inviare l'URL dei metadati di Federazione dell'app al [team di supporto di NS1 SSO for Azure](mailto:techops@nsone.net). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Creare un utente di test di NS1 SSO for Azure

In questa sezione viene creato un utente di nome B.Simon in NS1 SSO for Azure. Collaborare con il team di supporto di NS1 SSO for Azure per aggiungere gli utenti nella piattaforma NS1 SSO for Azure. Prima di poter usare l'accesso Single Sign-On, è necessario creare e attivare gli utenti.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro NS1 SSO for Azure nel pannello di accesso, si dovrebbe accedere automaticamente al NS1 SSO for Azure per il quale si configura SSO. Per altre informazioni, vedere l'[introduzione al pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare NS1 SSO for Azure con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)