---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAML 1.1 Token enabled LOB App | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAML 1.1 Token enabled LOB App.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 477180a6576d52e3386e18b6e2ba12dd33e4d794
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654409"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Esercitazione: Integrazione di Azure Active Directory con SAML 1.1 Token enabled LOB App

Questa esercitazione illustra come integrare l'app LOB abilitata per il token SAML 1,1 con Azure Active Directory (Azure AD). Quando si integra un'app LOB abilitata per il token SAML 1,1 con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso all'app LOB abilitata per il token SAML 1,1.
* Consentire agli utenti di accedere automaticamente all'app LOB abilitata per il token SAML 1,1 con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per la Single Sign-On app LOB abilitata per il token SAML 1,1.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAML 1.1 Token enabled LOB App supporta l'accesso SSO avviato da **SP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-saml-11-token-enabled-lob-app-from-the-gallery"></a>Aggiunta dell'app LOB abilitata per il token SAML 1,1 dalla raccolta

Per configurare l'integrazione di SAML 1.1 Token enabled LOB App in Azure AD, è necessario aggiungere SAML 1.1 Token enabled LOB App dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAML 1,1 token app LOB Enabled** nella casella di ricerca.
1. Selezionare **SAML 1,1 token Enabled app LOB** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-saml-11-token-enabled-lob-app"></a>Configurare e testare Azure AD SSO per il token SAML 1,1 abilitata app LOB

Configurare e testare Azure AD SSO con SAML 1,1 token abilitata app LOB usando un utente test di nome **B. Simon**. Per il funzionamento dell'accesso Single Sign-on, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato nell'app LOB abilitata per il token SAML 1,1.

Per configurare e testare Azure AD SSO con l'app LOB abilitata per il token SAML 1,1, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare SAML 1,1 token Enabled app LOB SSO](#configure-saml-11-token-enabled-lob-app-sso)** : per configurare le impostazioni Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di saml 1,1 enabled token abilitato](#create-saml-11-token-enabled-lob-app-test-user)** : per avere una controparte di B. Simon in SAML 1,1 token Enabled app LOB collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nel portale di Azure, nella pagina di integrazione dell'applicazione LOB per il **token SAML 1,1 abilitata** , trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://your-app-url`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://your-app-url`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori contattare il team di supporto clienti di SAML 1.1 Token enabled LOB App. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura SAML 1.1 Token enabled LOB App** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso all'app LOB abilitata per il token SAML 1,1.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAML 1.1 Token enabled LOB App**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-saml-11-token-enabled-lob-app-sso"></a>Configurare l'accesso SSO per l'app LOB abilitato per il token SAML 1,1

Per configurare l'accesso Single Sign-On sul lato **SAML 1.1 Token enabled LOB App**, è necessario inviare il file **Certificato (Base64)** scaricato e gli URL appropriati copiati dal portale di Azure al team di supporto di SAML 1.1 Token enabled LOB App. La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Creare un utente di test di SAML 1.1 Token enabled LOB App

In questa sezione viene creato un utente di nome Britta Simon in SAML 1.1 Token enabled LOB App. Collaborare con il team di supporto di SAML 1.1 Token enabled LOB App per aggiungere gli utenti alla piattaforma SAML 1.1 Token enabled LOB App. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso dell'app LOB abilitato per il token SAML 1,1, in cui è possibile avviare il flusso di accesso. 

* Passare a SAML 1,1 token Enabled LOB URL accesso diretto e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro app LOB abilitato per il token SAML 1,1 in app personali, questo verrà reindirizzato all'URL di accesso dell'app LOB abilitato per il token SAML 1,1. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato l'app LOB abilitata per il token SAML 1,1, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).