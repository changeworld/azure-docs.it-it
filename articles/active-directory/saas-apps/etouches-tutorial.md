---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Aventri | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e Aventri.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/10/2020
ms.author: jeedes
ms.openlocfilehash: 28dff02bea6f27b0da96c9a852ec5a62fe643151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453910"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aventri"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Aventri

Questa esercitazione illustra come integrare Aventri con Azure Active Directory (Azure AD). Quando si integra Aventri con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Aventri.
* Abilitare gli utenti per l'accesso automatico a Aventri con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per Aventri Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Aventri supporta SSO avviato da **SP**
* Dopo aver configurato Aventri, è possibile applicare il controllo della sessione, che proteggono exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


## <a name="adding-aventri-from-the-gallery"></a>Aggiunta di Aventri dalla raccolta

Per configurare l'integrazione di Aventri in Azure AD, è necessario aggiungere Aventri dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Aventri** nella casella di ricerca.
1. Selezionare **Aventri** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-aventri"></a>Configurare e testare Azure AD Single Sign-On per Aventri

Configurare e testare Azure AD SSO con Aventri usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Aventri.

Per configurare e testare Azure AD SSO con Aventri, completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare AVENTRI SSO](#configure-aventri-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Aventri](#create-aventri-test-user)** : per avere una controparte di B. Simon in Aventri collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Aventri** del [portale di Azure](https://portal.azure.com/)individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://na-admin.eventscloud.com/saml/accounts/acs/<ACCOUNTID>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://na-admin.eventscloud.com/saml/accounts/sso/<ACCOUNTID>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare i valori con URL di accesso e identificatore effettivi, come illustrato più avanti nell'esercitazione.

1. L'applicazione Aventri prevede un formato specifico per le asserzioni SAML. è quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

1. Oltre a quanto sopra, l'applicazione Aventri prevede che venga restituito un numero maggiore di attributi nella risposta SAML, indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ------------------- | -------------------- |
    | Email | user.mail | 

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **set up Aventri** copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Aventri.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Aventri**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-aventri-sso"></a>Configurare Aventri SSO

1. Per ottenere l'accesso SSO configurato per l'applicazione, seguire questa procedura nell'applicazione Aventri: 

    ![Configurazione di Aventri](./media/etouches-tutorial/aventri-tutorial-06.png) 

    a. Accedere all'applicazione **Aventri** usando i diritti di amministratore.
   
    b. Passare alla configurazione **SAML** .

    c. Nella sezione **General Settings** (Impostazioni generali) aprire il certificato scaricato dal portale di Azure nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo dei metadati IDP. 

    d. Fare clic sul pulsante **Save & Stay** .
  
    e. Fare clic sul pulsante **Update Metadata** (Aggiorna metadati) nella sezione relativa ai metadati SAML. 

    f. La pagina viene aperta e viene eseguito l'accesso SSO. Quando l'accesso SSO funziona, è possibile impostare il nome utente.

    g. Nel campo username (nome utente) selezionare **EmailAddress** come illustrato nell'immagine seguente. 

    h. Copiare il valore di **SP entity ID** (ID entità SP) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    i. Copiare il valore di **SSO URL / ACS** (URL/ACS SSO) e incollarlo nella casella di testo **URL accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

### <a name="create-aventri-test-user"></a>Creare un utente di test di Aventri

In questa sezione viene creato un utente denominato B. Simon in Aventri. Collaborare con il [team di supporto clienti di Aventri](mailto:support@aventri.com) per aggiungere gli utenti nella piattaforma Aventri.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Aventri nel pannello di accesso, si dovrebbe accedere automaticamente al Aventri per il quale si configura SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Prova Aventri con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)