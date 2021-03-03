---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con lavagna Learn | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Blackboard Learn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 9e2898fdeda6e72f09d4127561249906569affb0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649006"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blackboard-learn"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con lavagna Learn

Questa esercitazione descrive come integrare Blackboard Learn con Azure Active Directory (Azure AD). Integrando Blackboard Learn con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a Blackboard Learn.
* Abilitare gli utenti per l'accesso automatico a Blackboard Learn con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Blackboard Learn abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Blackboard Learn supporta l'accesso SSO avviato da **SP**
* Blackboard Learn supporta il provisioning utenti **JIT**


## <a name="add-blackboard-learn-from-the-gallery"></a>Aggiungere Blackboard Learn dalla raccolta

Per configurare l'integrazione di Blackboard Learn in Azure AD, è necessario aggiungere Blackboard Learn dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Blackboard Learn** nella casella di ricerca.
1. Selezionare **Blackboard Learn** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-blackboard-learn"></a>Configurare e testare Azure AD SSO for lavagna Learn

Configurare e testare l'accesso SSO di Azure AD con Blackboard Learn usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Blackboard Learn.

Per configurare e testare Azure AD SSO con lavagna Learn, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Blackboard Learn](#configure-blackboard-learn-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Blackboard Learn](#create-blackboard-learn-test-user)**: per avere una controparte di B.Simon in Blackboard Learn collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure nella pagina di integrazione dell'applicazione **lavagna informazioni** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<subdomain>.blackboard.com/`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<subdomain>.blackboard.com/auth-saml/saml/SSO/entity-id/SAML_AD`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Blackboard Learn](https://www.blackboard.com/support/index.aspx). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Blackboard Learn** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abilita B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Blackboard Learn.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Blackboard Learn**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-blackboard-learn-sso"></a>Configurare l'accesso Single Sign-On di Blackboard Learn

Per configurare l'accesso Single Sign-On sul lato **Blackboard Learn** selezionare [questo collegamento](https://help.blackboard.com/Learn/Administrator/SaaS/Authentication/Implement_Authentication/SAML_Authentication_Provider_Type). Se si riscontrano problemi durante la configurazione, contattare il [team di supporto di Blackboard Learn](https://www.blackboard.com/support/index.aspx).


### <a name="create-blackboard-learn-test-user"></a>Creare l'utente di test di Blackboard Learn

In questa sezione, un utente denominato B. Simon viene creato in lavagna Learn. Lavagna Learn supporta il provisioning degli utenti just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già in lavagna, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà reindirizzato all'URL di accesso a Lavagna, in cui è possibile avviare il flusso di accesso. 

* Passare a lavagna informazioni sull'URL di accesso direttamente e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro lavagna Learn nell'app My, viene reindirizzato all'URL di accesso a Lavagna Learn. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurate le informazioni sulla lavagna, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).