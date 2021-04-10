---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Exium | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e Exium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 01b3bbc0a4f0c7d53ed0a9ba2449cf5985b2ab86
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610233"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-exium"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Exium

Questa esercitazione illustra come integrare Exium con Azure Active Directory (Azure AD). Quando si integra Exium con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Exium.
* Abilitare gli utenti per l'accesso automatico a Exium con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per Exium Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Exium supporta SSO avviato da **SP** .

## <a name="adding-exium-from-the-gallery"></a>Aggiunta di Exium dalla raccolta

Per configurare l'integrazione di Exium in Azure AD, è necessario aggiungere Exium dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Exium** nella casella di ricerca.
1. Selezionare **Exium** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-exium"></a>Configurare e testare Azure AD SSO per Exium

Configurare e testare Azure AD SSO con Exium usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Exium.

Per configurare e testare Azure AD SSO con Exium, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare EXIUM SSO](#configure-exium-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Exium](#create-exium-test-user)** : per avere una controparte di B. Simon in Exium collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Exium** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://subapi.exium.net/saml/<WORKSPACE_ID>/metadata`
    
    b.  Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://subapi.exium.net/saml/<WORKSPACE_ID>/acs`

    c. Nella casella di testo **URL di accesso** digitare l'URL: `https://service.exium.net/sign-in`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Exium](mailto:support@exium.net) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Exium.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Exium**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-exium-sso"></a>Configurare Exium SSO

1. Accedere al sito aziendale di Exium come amministratore.

1. Nella **console di amministrazione** selezionare Pannello **Profilo azienda** .

    ![screenshot della console di amministrazione](./media/exium-tutorial/company-profile.png)

1. Nel **profilo** fare clic su **SSO Settings (impostazioni SSO** ) e **modificarlo** .

1. Eseguire i passaggi seguenti nella sezione **impostazioni SSO** .

    ![screenshot per le impostazioni SSO](./media/exium-tutorial/update.png)

    a. Selezionare **SSO Type** As **AzureAD** nell'elenco a discesa.

    b. Incollare il valore dell' **URL dei metadati di Federazione dell'app** nel campo **URL metadati IDP SAML 2,0** .

    c. Copiare il valore di **URL SSO SAML 2,0** , incollare questo valore nella casella di testo **URL di risposta** nella sezione di **configurazione SAML di base** nel portale di Azure.

    d. Copiare il valore dell' **ID di entità saml 2,0 SP** , incollare questo valore nella casella di testo **identificatore** nella sezione di **configurazione SAML di base** nel portale di Azure.  

    e. Fare clic su **Update** (Aggiorna).

### <a name="create-exium-test-user"></a>Creare un utente di test di Exium

1. Accedere al sito aziendale di Exium come amministratore.

1. Passare a **Gestione utenti-> utenti** e fare clic su **Aggiungi utente**.

    ![screenshot per Crea utente test](./media/exium-tutorial/add-user.png)

1. Immettere i campi obbligatori nella pagina seguente e fare clic su **Salva**.

    ![screenshot per creare campi utente test con il pulsante Salva](./media/exium-tutorial/add-user-2.png)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Exium, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Exium e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Exium in app personali, questo verrà reindirizzato all'URL di accesso di Exium. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Exium, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


