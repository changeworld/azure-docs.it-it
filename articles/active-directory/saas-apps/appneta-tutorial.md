---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con AppNeta Performance Monitor | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e AppNeta Performance Monitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: b2558b4b3bcd60acba3bf47d4a973a2b6de7424f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736007"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con AppNeta Performance Monitor

Questa esercitazione descrive come integrare AppNeta Performance Monitor con Azure Active Directory (Azure AD). Integrando AppNeta Performance Monitor con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad AppNeta Performance Monitor.
* Abilitare gli utenti per l'accesso automatico ad AppNeta Performance Monitor con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di AppNeta Performance Monitor abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* AppNeta Performance Monitor supporta l'accesso SSO avviato da **SP**

* AppNeta Performance Monitor supporta il provisioning utenti **JIT**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Aggiunta di AppNeta Performance Monitor dalla raccolta

Per configurare l'integrazione di AppNeta Performance Monitor in Azure AD, è necessario aggiungere AppNeta Performance Monitor dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **AppNeta Performance Monitor** nella casella di ricerca.
1. Selezionare **AppNeta Performance Monitor** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-appneta-performance-monitor"></a>Configurare e testare l'accesso SSO di Azure AD per AppNeta Performance Monitor

Configurare e testare l'accesso SSO di Azure AD con AppNeta Performance Monitor usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AppNeta Performance Monitor.

Per configurare e testare l'accesso SSO di Azure AD con AppNeta Performance Monitor, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di AppNeta Performance Monitor](#configure-appneta-performance-monitor-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di AppNeta Performance Monitor](#create-appneta-performance-monitor-test-user)**: per avere una controparte di B.Simon in AppNeta Performance Monitor collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **AppNeta Performance Monitor** nel portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<subdomain>.pm.appneta.com`

    > [!NOTE]
    > Poiché il valore di URL accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Contattare il [team di supporto del client AppNeta Performance Monitor](mailto:support@appneta.com) per ottenere questo valore. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione AppNeta Performance Monitor prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione AppNeta Performance Monitor prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | --------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | email| user.userprincipalname|
    | name| user.userprincipalname|
    | groups  | user.assignedroles |
    | phone| user.telephonenumber |
    | title| user. jobtitle|
    | | |

    > [!NOTE]
    > **groups** fa riferimento al gruppo di sicurezza in AppNeta di cui viene eseguito il mapping a un **ruolo** in Azure AD. Vedere [questo](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) documento che illustra come creare ruoli personalizzati in Azure AD.

    1. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    1. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    1. Lasciare vuota la casella **Spazio dei nomi**.

    1. Per Origine selezionare **Attributo**.

    1. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    1. Fare clic su **OK**.

    1. Fare clic su **Salva**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura AppNeta Performance Monitor** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad AppNeta Performance Monitor.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **AppNeta Performance Monitor**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se i ruoli sono stati configurati come illustrato sopra, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.
## <a name="configure-appneta-performance-monitor-sso"></a>Configurare l'accesso Single Sign-On di AppNeta Performance Monitor

Per configurare l'accesso Single Sign-On sul lato **AppNeta Performance Monitor**, è necessario inviare il file di **XML metadati federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di AppNeta Performance Monitor](mailto:support@appneta.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-appneta-performance-monitor-test-user"></a>Creare l'utente di test di AppNeta Performance Monitor

In questa sezione viene creato un utente di nome Britta Simon in AppNeta Performance Monitor. AppNeta Performance Monitor supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in AppNeta Performance Monitor, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di AppNeta Performance Monitor](mailto:support@appneta.com).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di AppNeta Performance Monitor, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di AppNeta Performance Monitor e avviare il flusso di accesso.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di AppNeta Performance Monitor in App personali, verrà eseguito il reindirizzamento all'URL di accesso di AppNeta Performance Monitor. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato AppNeta Performance Monitor, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
