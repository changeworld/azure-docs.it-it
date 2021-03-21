---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 6db863f43deb6eb2787cda60650a267a62076aad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654341"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign

In questa esercitazione si apprenderà come integrare SAP Business ByDesign con Azure Active Directory (Azure AD). Quando si integra SAP Business ByDesign con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SAP Business ByDesign.
* Abilitare gli utenti per l'accesso automatico a SAP Business ByDesign con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SAP Business ByDesign Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Business ByDesign supporta l'accesso SSO avviato da **SP**

## <a name="add-sap-business-bydesign-from-the-gallery"></a>Aggiungere SAP Business ByDesign dalla raccolta

Per configurare l'integrazione di SAP Business ByDesign in Azure AD, è necessario aggiungere SAP Business ByDesign dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAP Business ByDesign** nella casella di ricerca.
1. Selezionare **SAP Business ByDesign** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

Configurare e testare Azure AD SSO con SAP Business ByDesign usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in SAP Business ByDesign.

Per configurare e testare Azure AD SSO con SAP Business ByDesign, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare SAP Business BYDESIGN SSO](#configure-sap-business-bydesign-sso)** : per configurare le impostazioni del singolo Sign-On sul lato applicazione.
    1. **[Creare un utente di test di SAP Business ByDesign](#create-sap-business-bydesign-test-user)** : per avere una controparte di Britta Simon in SAP Business ByDesign collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SAP Business ByDesign** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<servername>.sapbydesign.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione SAP Business ByDesign prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![immagine1](common/edit-attribute.png)

6. Fare clic sull'icona **Modifica** per modificare il **valore dell'identificatore Nome**.

    ![Immagine2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Nella sezione **Gestisci attestazioni utente** seguire questa procedura:

    ![immagine3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selezionare **Trasformazione** come **Origine**.

    b. Nell'elenco a discesa **Trasformazione**, selezionare **ExtractMailPrefix()** .

    > [!NOTE]
    > Per impostazione predefinita, ByD usa il formato NameID non **specificato** per il mapping utente. ByD esegue il mapping dell'NameID di asserzioni SAML nell'alias utente ByD. Inoltre, ByD supporta il formato ID nome **EmailAddress**. In questo caso ByD esegue il mapping di NameID dell'asserzione SAM nell'indirizzo di posta elettronica dell'utente ByD dei dati di contatto del dipendente ByD. Per informazioni dettagliate, è possibile fare riferimento a [questo Blog SAP](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/).

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

9. Nella sezione **Configura SAP Business ByDesign** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a SAP Business ByDesign.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni, selezionare **SAP Business ByDesign**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".

1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sap-business-bydesign-sso"></a>Configurare SAP Business ByDesign SSO

1. Accedere al portale di SAP Business ByDesign con diritti di amministratore.

2. Passare a **Application and User Management Common Task** (Attività comuni di gestione utenti e applicazioni) e fare clic sulla scheda **Identity Provider** (Provider di identità).

3. Fare clic su **New Identity Provider** (Nuovo provider di identità) e selezionare il file XML metadati scaricato dal portale di Azure. Quando si importano i metadati, il sistema carica automaticamente il certificato di firma e il certificato di crittografia necessari.

    ![Configurare l'accesso Single Sign-On - 1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Per includere l'**URL del servizio consumer di asserzione** nella richiesta SAML, selezionare **Include Assertion Consumer Service URL** (Includi URL del servizio Consumer di asserzione).

5. Fare clic su **Activate Single Sign-On**(Attiva Single Sign-On).

6. Salvare le modifiche.

7. Fare clic sulla scheda **My System** (Sistema locale).

    ![Configurare l'accesso Single Sign-On - 2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Nella casella di testo **Azure AD Sign-On URL** (URL di accesso SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    ![Configurare l'accesso Single Sign-On - 3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Specificare se il dipendente può scegliere manualmente tra l'accesso con ID utente e password o SSO selezionando **Manual Identity Provider Selection**(Selezione manuale provider di identità).

10. Nella sezione **SSO URL** (URL SSO), specificare l'URL che deve essere usato dai dipendenti per l'accesso al sistema.
    Nell'elenco a discesa URL Sent to Employee (URL inviato al dipendente) è possibile scegliere tra le opzioni seguenti:

    **Non-SSO URL**

    Il sistema invia al dipendente solo il normale URL di sistema. Il dipendente non può accedere con SSO e deve usare invece la password o il certificato.

    **SSO URL**

    Il sistema invia al dipendente solo l'URL SSO. Il dipendente può accedere con SSO. La richiesta di autenticazione viene reindirizzata tramite IdP.

    **Automatic Selection**

    Se SSO non è attivo, il sistema invia al dipendente il normale URL di sistema. Se SSO è attivo, il sistema verifica se il dipendente ha una password. Se è disponibile una password, vengono inviati al dipendente sia l'URL SSO che l'URL non SSO. Tuttavia, se il dipendente non ha una password, riceverà solo l'URL SSO.

11. Salvare le modifiche.

### <a name="create-sap-business-bydesign-test-user"></a>Creare un utente di test di SAP Business ByDesign

In questa sezione viene creato un utente di nome Britta Simon in SAP Business ByDesign. Collaborare con il [team di supporto clienti di SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) per aggiungere gli utenti nella piattaforma SAP Business ByDesign. 

> [!NOTE]
> Assicurarsi che il valore di NameID corrisponda al campo username nella piattaforma SAP Business ByDesign.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

1. Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di SAP Business ByDesign, in cui è possibile avviare il flusso di accesso. 

2. Passare direttamente all'URL di accesso di SAP Business ByDesign e avviare il flusso di accesso da qui.

3. È possibile usare App personali Microsoft. Quando si fa clic sul riquadro SAP Business ByDesign in app personali, questo verrà reindirizzato all'URL di accesso a SAP Business ByDesign. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

* Dopo la configurazione di SAP Business ByDesign è possibile applicare i controlli della sessione, che proteggono exfiltration e infiltrano i dati sensibili dell'organizzazione in tempo reale. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).