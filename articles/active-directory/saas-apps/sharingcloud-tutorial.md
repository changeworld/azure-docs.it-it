---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con SharingCloud | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e Instant suite.
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
ms.openlocfilehash: 7ae447a9577feba8b43b5b03a757ec4095ee2cb4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177947"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharingcloud"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con SharingCloud

Questa esercitazione illustra come integrare SharingCloud con Azure Active Directory (Azure AD). Quando si integra SharingCloud con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SharingCloud.
* Abilitare gli utenti per l'accesso automatico a SharingCloud con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere informazioni sull' [accesso alle applicazioni e Single Sign-on con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione abilitata per SharingCloud Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SharingCloud supporta SSO avviato da **SP e IDP**
* SharingCloud supporta il provisioning degli utenti **just-in-Time**

## <a name="adding-sharingcloud-from-the-gallery"></a>Aggiunta di SharingCloud dalla raccolta

Per configurare l'integrazione di SharingCloud in Azure AD, è necessario aggiungere SharingCloud dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)
    
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)
    
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.

    ![Pulsante Nuova applicazione](common/add-new-app.png)
    
1. Nella sezione **Aggiungi dalla raccolta** digitare **SharingCloud** nella casella di ricerca.

    ![SharingCloud nell'elenco risultati](common/search-new-app.png)
    
1. Selezionare **SharingCloud** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharingcloud"></a>Configurare e testare Azure AD Single Sign-On per SharingCloud

Configurare e testare Azure AD SSO con SharingCloud usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in SharingCloud.

Per configurare e testare Azure AD SSO con SharingCloud, completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare SHARINGCLOUD SSO](#configure-sharingcloud-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di SharingCloud](#create-sharingcloud-test-user)** : per avere una controparte di B. Simon in SharingCloud collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SharingCloud** del [portale di Azure](https://portal.azure.com/)individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
    
    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)
    
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nella pagina **configura Single Sign-on con SAML** fare clic sull'icona di **modifica** per la **configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    Caricare il file di metadati con il file XML fornito da SharingCloud. Per ottenere il file, contattare il [team di supporto clienti di SharingCloud](mailto:support@sharingcloud.com) .

    ![image](common/upload-metadata.png)
    
    Selezionare il file di metadati fornito e fare clic su **carica**.

    ![image](common/browse-upload-metadata.png)

1. L'applicazione SharingCloud prevede un formato specifico per le asserzioni SAML. è quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit_attribute.png)

1. Oltre a quanto sopra, l'applicazione SharingCloud prevede che venga restituito un numero maggiore di attributi nella risposta SAML, indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---------------| --------- |
    | urn: sharingcloud: SSO: FirstName | user.givenname |
    | urn: sharingcloud: SSO: LastName | user.surname |
    | urn: sharingcloud: SSO: posta elettronica | user.mail |

1. Nella sezione **certificato di firma SAML** della pagina **impostare Single Sign-on con SAML** fare clic sull'icona **copia** per copiare l' **URL dei metadati federativi** dalle opzioni specificate in base ai requisiti.

    ![URL dei metadati da copiare](common/copy_metadataurl.png)

## <a name="configure-sharingcloud-sso"></a>Configurare SharingCloud SSO

Per configurare Single Sign-On sul lato **SharingCloud** , è necessario inviare l' **URL dei metadati di federazione** copiato da portale di Azure al team di supporto di [SharingCloud](mailto:support@sharingcloud.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a SharingCloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SharingCloud**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

   ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sharingcloud-test-user"></a>Creare un utente di test di SharingCloud

In questa sezione viene creato un utente di nome Britta Simon in SharingCloud. SharingCloud supporta il provisioning degli utenti just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già in SharingCloud, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

* Passare direttamente all'URL SharingCloud e avviare il flusso di accesso da qui.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato SharingCloud, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

