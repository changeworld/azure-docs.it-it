---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Count Me In - Operations Dashboard | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Count Me In - Operations Dashboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/30/2020
ms.author: jeedes
ms.openlocfilehash: 3339516193af6e1ff832ac586f4a81f8799c5b83
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98727678"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-count-me-in---operations-dashboard"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Count Me In - Operations Dashboard

Questa esercitazione descrive come integrare Count Me In - Operations Dashboard con Azure Active Directory (Azure AD). Integrando Count Me In - Operations Dashboard con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Count Me In - Operations Dashboard.
* Abilitare gli utenti per l'accesso automatico a Count Me In - Operations Dashboard con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Count Me In - Operations Dashboard abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Count Me In - Operations Dashboard supporta l'accesso SSO avviato da **SP**

## <a name="adding-count-me-in---operations-dashboard-from-the-gallery"></a>Aggiunta di Count Me In - Operations Dashboard dalla raccolta

Per configurare l'integrazione di Count Me In - Operations Dashboard in Azure AD, è necessario aggiungere Count Me In - Operations Dashboard dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Count Me In - Operations Dashboard** nella casella di ricerca.
1. Selezionare **Count Me In - Operations Dashboard** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-count-me-in---operations-dashboard"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Count Me In - Operations Dashboard

Configurare e testare l'accesso SSO di Azure AD con Count Me In - Operations Dashboard usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Count Me In - Operations Dashboard.

Per configurare e testare l'accesso SSO di Azure AD con Count Me In - Operations Dashboard, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Count Me In-Operations Dashboard](#configure-count-me-in-operations-dashboard-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Count Me In-Operations Dashboard](#create-count-me-in-operations-dashboard-test-user)** : per avere una controparte di B.Simon in Count Me In - Operations Dashboard collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Count Me In - Operations Dashboard** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `api-us.localz.io/<PROJECT_ID>`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Count Me In - Operations Dashboard](mailto:support@localz.co). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Count Me In - Operations Dashboard prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Count Me In - Operations Dashboard prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome |  Attributo di origine|
    | ----------- | --------- |
    | Ruoli assegnati | user.assignedroles |

    > [!NOTE]
    > Count Me In - Operations Dashboard prevede ruoli per gli utenti assegnati all'applicazione. Configurare questi ruoli in Azure AD in modo che agli utenti possano essere assegnati i ruoli appropriati. Per informazioni su come configurare i ruoli in Azure AD, vedere [qui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Count Me In - Operations Dashboard** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Count Me In - Operations Dashboard.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Count Me In - Operations Dashboard**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-count-me-in-operations-dashboard-sso"></a>Configurare l'accesso Single Sign-On di Count Me In-Operations Dashboard

Per configurare l'accesso Single Sign-On sul lato **Count Me In - Operations Dashboard** è necessario inviare il file del **certificato (Base64)** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di Count Me In - Operations Dashboard](mailto:support@localz.co). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-count-me-in-operations-dashboard-test-user"></a>Creare l'utente di test di Count Me In - Operations Dashboard

In questa sezione viene creato un utente di nome Britta Simon in Count Me In - Operations Dashboard. Collaborare con il [team di supporto di Count Me In - Operations Dashboard](mailto:support@localz.co) per aggiungere gli utenti alla piattaforma Count Me In - Operations Dashboard. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Count Me In - Operations Dashboard, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Count Me In - Operations Dashboard e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Count Me In - Operations Dashboard in App personali, verrà eseguito il reindirizzamento all'URL di accesso di Count Me In - Operations Dashboard. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Count Me In - Operations Dashboard, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
