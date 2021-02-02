---
title: 'Esercitazione: Integrazione di Azure Active Directory con Dropbox Business | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Dropbox Business.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: cc7e75094f23c5382797541911d0aeb8be50f432
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430916"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Esercitazione: Integrare Dropbox Business con Azure Active Directory

Questa esercitazione descrive come integrare Dropbox Business con Azure Active Directory (Azure AD). Integrando Dropbox Business con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Dropbox Business.
* Abilitare gli utenti per l'accesso automatico a Dropbox Business con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Dropbox Business abilitata per l'accesso Single Sign-On (SSO).

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

* In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Dropbox Business supporta l'accesso SSO avviato da **SP**

* Dropbox Business supporta [provisioning e deprovisioning utenti automatici](dropboxforbusiness-tutorial.md)

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-dropbox-business-from-the-gallery"></a>Aggiungere Dropbox business dalla raccolta

Per configurare l'integrazione di Dropbox Business in Azure AD, è necessario aggiungere Dropbox Business dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Dropbox Business** nella casella di ricerca.
1. Selezionare **Dropbox Business** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-dropbox-business"></a>Configurare e testare Azure AD SSO for Dropbox business

Configurare e testare l'accesso SSO di Azure AD con Dropbox Business usando un utente di test di nome **Britta Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Dropbox Business.

Per configurare e testare Azure AD SSO con Dropbox business, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.    
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On per Dropbox Business](#configure-dropbox-business-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Dropbox Business](#create-dropbox-business-test-user)** : per avere una controparte di Britta Simon in Dropbox Business collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure nella pagina di integrazione dell'applicazione **Dropbox business** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://www.dropbox.com/sso/<id>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare il valore `Dropbox`

    > [!NOTE]
    > Il valore precedente non è un valore reale. È necessario aggiornarlo con l'URL di accesso effettivo, descritto più avanti nell'esercitazione.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Dropbox Business** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `Britta Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Dropbox business.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Dropbox Business**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-dropbox-business-sso"></a>Configurare l'accesso Single Sign-On di Dropbox Business

1. Per automatizzare la configurazione all'interno di Dropbox Business, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Setup Dropbox Business** (Configura Dropbox Business) per passare direttamente all'applicazione Dropbox Business. Da tale posizione, specificare le credenziali di amministratore per accedere a Dropbox Business. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 8.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Per configurare manualmente Dropbox Business, aprire una nuova finestra del Web browser, passare al tenant di Dropbox Business e accedere al proprio tenant di Dropbox Business. Seguire quindi questa procedura:

    ![Screenshot che mostra la pagina "Dropbox Business Sign in".](./media/dropboxforbusiness-tutorial/account.png "Configura accesso Single Sign-On")

4. Fare clic su **Icona utente** e selezionare la scheda **Impostazioni**.

    ![Screenshot che mostra l'azione "USER ICON" e l'opzione "Settings" selezionate.](./media/dropboxforbusiness-tutorial/configure1.png "Configura accesso Single Sign-On")

5. Nel riquadro di spostamento a sinistra fare clic su **Console di amministrazione**.

    ![Screenshot che mostra l'opzione "Admin console" selezionata.](./media/dropboxforbusiness-tutorial/configure2.png "Configura accesso Single Sign-On")

6. In **Console di amministrazione** fare clic su **Impostazioni** nel riquadro di spostamento a sinistra.

    ![Screenshot che mostra l'opzione "Settings" selezionata.](./media/dropboxforbusiness-tutorial/configure3.png "Configura accesso Single Sign-On")

7. Selezionare l'opzione **Single sign-on** sotto la sezione **Autenticazione**.

    ![Screenshot che mostra la sezione "Authentication" con l'opzione "Single sign-on" selezionata.](./media/dropboxforbusiness-tutorial/configure4.png "Configura accesso Single Sign-On")

8. Nella sezione **Single sign-on** seguire questa procedura:  

    ![Screenshot che mostra le impostazioni di configurazione di "Single sign-on".](./media/dropboxforbusiness-tutorial/configure5.png "Configura accesso Single Sign-On")

    a. Selezionare **Required** (Obbligatorio) come opzione nell'elenco a discesa **Single sign-on**.

    b. Fare clic su **Aggiungi URL di accesso** e quindi nella casella di testo **URL di accesso del provider di identità** incollare il valore di **URL di accesso** copiato dal portale di Azure e selezionare **Fine**.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/sso.png "Configura accesso Single Sign-On")

    c. Fare clic su **Carica certificato** e quindi andare al **file di certificato oBase64** scaricato dal portale di Azure.

    d. Fare clic su **Copy link** (Copia collegamento) e incollare il valore copiato nella casella di testo **Sign-on URL** (URL di accesso) della sezione **URL e dominio di Dropbox Business** nel portale di Azure.

    e. Fare clic su **Salva**.

### <a name="create-dropbox-business-test-user"></a>Creare l'utente di test di Dropbox Business

In questa sezione viene creato un utente di nome B.Simon in Dropbox Business. Dropbox Business supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Dropbox Business, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto clienti di Dropbox Business](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Dropbox business in cui è possibile avviare il flusso di accesso. 

* Passare direttamente a Dropbox business sign-on URL e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro business Dropbox in app personali, questo verrà reindirizzato all'URL di accesso aziendale a Dropbox. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Dropbox business è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).