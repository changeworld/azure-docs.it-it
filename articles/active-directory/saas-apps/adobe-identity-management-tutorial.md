---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Adobe Identity Management | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Identity Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fdca04c645e1bb956c8e9f294c702b639c8e2f74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98726402"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Adobe Identity Management

Questa esercitazione descrive come integrare Adobe Identity Management con Azure Active Directory (Azure AD). Integrando Adobe Identity Management con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Adobe Identity Management.
* Abilitare gli utenti per l'accesso automatico ad Adobe Identity Management con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Adobe Identity Management abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Adobe Identity Management supporta l'accesso SSO avviato da **SP**

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Aggiunta di Adobe Identity Management dalla raccolta

Per configurare l'integrazione di Adobe Identity Management in Azure AD, è necessario aggiungere Adobe Identity Management dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Adobe Identity Management** nella casella di ricerca.
1. Selezionare **Adobe Identity Management** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Configurare e testare Azure AD SSO per Adobe Identity Management

Configurare e testare l'accesso SSO di Azure AD con Adobe Identity Management usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Identity Management.

Per configurare e testare Azure AD SSO con Adobe Identity Management, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Adobe Identity Management](#configure-adobe-identity-management-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Adobe Identity Management](#create-adobe-identity-management-test-user)** : per avere una controparte di B.Simon in Adobe Identity Management collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Adobe Identity Management** della portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare l'URL: `https://adobe.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > Il valore dell'identificatore non è reale. È necessario aggiornare questo valore con l'ID effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Adobe Identity Management](mailto:identity@adobe.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Adobe Identity Management** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Adobe Identity Management.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Adobe Identity Management**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-adobe-identity-management-sso"></a>Configurare l'accesso Single Sign-On di Adobe Identity Management

1. Per automatizzare la configurazione in Adobe Identity Management, è necessario installare l' **estensione del browser per l'accesso sicuro alle app** facendo clic su **installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo l'aggiunta dell'estensione al browser, fare clic su **Configura gestione identità Adobe** per indirizzare l'utente all'applicazione Adobe Identity Management. Da qui, fornire le credenziali di amministratore per accedere ad Adobe Identity Management. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 8.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare Adobe Identity Management manualmente, in un'altra finestra del Web browser accedere al sito aziendale di Adobe Identity Management come amministratore.

4. Passare alla scheda **Settings (impostazioni** ) e fare clic su **create directory (Crea directory**).

    ![Impostazioni di Adobe Identity Management](./media/adobe-identity-management-tutorial/settings.png)

5. Assegnare il nome della directory nella casella di testo e selezionare **FEDERATO ID** **, quindi fare** clic su Avanti.

    ![Creazione directory di Adobe Identity Management](./media/adobe-identity-management-tutorial/create-directory.png)

6. Selezionare gli **altri provider SAML** e fare clic su **Avanti.**
 
    ![Provider SAML di Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Fare clic su **Seleziona** per caricare il file **XML dei metadati** scaricato dal portale di Azure.

    ![Configurazione SAML di Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Fare clic su **Done** (Fine).

### <a name="create-adobe-identity-management-test-user"></a>Creare l'utente di test di Adobe Identity Management

1. Passare alla scheda **utenti** e fare clic su **Aggiungi utente**.

    ![Aggiunta dell'utente in Adobe Identity Management](./media/adobe-identity-management-tutorial/add-user.png)

2. Nella casella di testo **immettere l'indirizzo di posta elettronica dell'utente** assegnare l' **indirizzo di posta elettronica**.

    ![Gestione identità Adobe Salva utente](./media/adobe-identity-management-tutorial/save-user.png)

3. Fare clic su **Salva**.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Adobe Identity Management in cui è possibile avviare il flusso di accesso.

* Passare direttamente all'URL di accesso di Adobe Identity Management e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Adobe Identity Management in app personali, questo verrà reindirizzato all'URL di accesso di Adobe Identity Management. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Adobe Identity Management, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).