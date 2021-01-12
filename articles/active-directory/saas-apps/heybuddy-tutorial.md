---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con HeyBuddy | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e HeyBuddy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: c1f102182a9f91d2f8038947b6009fff1af1a508
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914264"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-heybuddy"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con HeyBuddy

Questa esercitazione descrive come integrare HeyBuddy con Azure Active Directory (Azure AD). Integrando HeyBuddy con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a HeyBuddy.
* Abilitare gli utenti per l'accesso automatico a HeyBuddy con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di HeyBuddy abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* HeyBuddy supporta l'accesso SSO avviato da **SP**
* HeyBuddy supporta il provisioning utenti **JIT**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.


## <a name="adding-heybuddy-from-the-gallery"></a>Aggiunta di HeyBuddy dalla raccolta

Per configurare l'integrazione di HeyBuddy in Azure AD, è necessario aggiungere HeyBuddy dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **HeyBuddy** nella casella di ricerca.
1. Selezionare **HeyBuddy** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-heybuddy"></a>Configurare e testare l'accesso SSO di Azure AD per HeyBuddy

Configurare e testare l'accesso SSO di Azure AD con HeyBuddy usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in HeyBuddy.

Per configurare e testare l'accesso SSO di Azure AD con HeyBuddy, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di HeyBuddy](#configure-heybuddy-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di HeyBuddy](#create-heybuddy-test-user)** : per avere una controparte di B.Simon in HeyBuddy collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **HeyBuddy** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://api.heybuddy.com/auth/<ENTITY ID>`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Il `Entity ID` l'URL di accesso viene generato automaticamente per ogni organizzazione. Per ottenere questi valori, contattare il [team di supporto di HeyBuddy](mailto:support@heybuddy.com).

1. L'applicazione HeyBuddy prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione EZOfficeInventory prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome |  Attributo di origine|
    | -------- | --------- |
    | Ruoli  | user.assignedroles |
    | | |
    
    > [!NOTE]
    > Consultare questo [collegamento](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) sulle modalità di configurazione e impostazione dei ruoli per l'applicazione.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a HeyBuddy.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **HeyBuddy**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se i ruoli sono stati configurati come illustrato sopra, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-heybuddy-sso"></a>Configurare l'accesso Single Sign-On di HeyBuddy

Per configurare l'accesso Single Sign-On sul lato **HeyBuddy**, è necessario inviare l'**URL dei metadati di federazione dell'app** al [team di supporto di HeyBuddy](mailto:support@heybuddy.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-heybuddy-test-user"></a>Creare un utente di test di HeyBuddy

In questa sezione viene creato un utente di nome Britta Simon in HeyBuddy. HeyBuddy supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in HeyBuddy, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Per creare un utente manualmente, contattare il [team di supporto di HeyBuddy](mailto:support@heybuddy.com).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di HeyBuddy, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di HeyBuddy e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di HeyBuddy in App personali, verrà eseguito il reindirizzamento all'URL di accesso di HeyBuddy. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato HeyBuddy, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
