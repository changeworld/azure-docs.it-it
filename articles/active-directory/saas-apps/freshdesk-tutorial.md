---
title: 'Esercitazione: Integrazione di Azure Active Directory con FreshDesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e FreshDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: e1394eafdfd733b5d69a4d4abbb6b218b4c8c10d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651905"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Esercitazione: Integrazione di Azure Active Directory con FreshDesk

Questa esercitazione illustra come integrare FreshDesk con Azure Active Directory (Azure AD). Quando si integra FreshDesk con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a FreshDesk.
* Abilitare gli utenti per l'accesso automatico a FreshDesk con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:
 
* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di FreshDesk Single Sign-On abilitata per l'accesso SSO.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* FreshDesk supporta l'accesso SSO avviato da **SP**

## <a name="add-freshdesk-from-the-gallery"></a>Aggiungere FreshDesk dalla raccolta

Per configurare l'integrazione di FreshDesk in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **FreshDesk** nella casella di ricerca.
1. Selezionare **FreshDesk** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per FreshDesk

Configurare e testare l'accesso SSO di Azure AD con FreshDesk usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in FreshDesk.

Per configurare e testare Azure AD SSO con FreshDesk, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare FRESHDESK SSO](#configure-freshdesk-sso)** : per configurare le impostazioni del singolo Sign-On sul lato applicazione.
    1. **[Creare un utente di test di FreshDesk](#create-freshdesk-test-user)** : per avere una controparte di Britta Simon in FreshDesk collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

1. Nella pagina di integrazione dell'applicazione **FreshDesk** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Single Sign-on di configurazione con SAML** fare clic sull'icona a matita per la **configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello `https://<tenant-name>.freshdesk.com` o qualsiasi altro valore suggerito da FreshDesk.

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL usando il modello `https://<tenant-name>.freshdesk.com` o qualsiasi altro valore suggerito da FreshDesk.
     
    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione FreshDesk prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. **Identificatore univoco dell'utente** è mappato con **user.userprincipalname** ma FreshDesk si aspetta che questa attestazione sia mappata con **user.mail**, quindi è necessario modificare il mapping degli attributi facendo clic sull'icona Modifica e cambiando il mapping degli attributi.

    ![image](common/edit-attribute.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura FreshDesk** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a FreshDesk.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni, selezionare **FreshDesk**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-freshdesk-sso"></a>Configurare FreshDesk SSO

1. In un'altra finestra del Web browser accedere al sito aziendale di Freshdesk come amministratore.

2. Selezionare l'**icona Security** (Sicurezza) e nella sezione **Security** (Sicurezza) seguire questa procedura:

    ![Single Sign-On](./media/freshdesk-tutorial/configure-1.png "Single Sign On")
  
    a. Per **Single Sign On** selezionare **On**.

    b. In **Login Method** (Metodo di accesso) selezionare **SAML SSO** (SSO SAML).

    c. Nella casella di testo **Entity ID provided by the IdP** (ID entità fornito dall'IdP) incollare il valore di **ID entità**, copiato dal portale di Azure.

    d. Nella casella di testo **SAML SSO URL** (URL SSO SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. In **Signing Options** (Opzioni di firma) selezionare **Only Signed Assertions** (Solo asserzioni firmate) dall'elenco a discesa.

    f. Nella casella di testo **Logout URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    g. Nella casella di testo **Security Certificate** (Certificato di sicurezza) incollare il valore di **Certificato (Base64)** ottenuto in precedenza.
  
    h. Fare clic su **Salva**.

## <a name="create-freshdesk-test-user"></a>Creare l'utente di test di FreshDesk

Per consentire agli utenti di Azure AD di accedere a FreshDesk, è necessario eseguirne il provisioning in FreshDesk.  
Nel caso di FreshDesk, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Freshdesk** .

1. Nel menu a sinistra fare clic su **Admin** (Amministratore) e nella scheda **General Settings** (Impostazioni generali) fare clic su **Agents** (Agenti).
  
    ![Agenti](./media/freshdesk-tutorial/create-user-1.png "Agenti")

1. Fare clic su **New Agent**.

    ![Nuovo agente](./media/freshdesk-tutorial/create-user-2.png "New Agent")

1. Nella finestra di dialogo delle informazioni sull'agente immettere i campi necessari e fare clic su **Create agent** (Crea agente).

    ![Informazioni dell'agente](./media/freshdesk-tutorial/create-user-3.png "Agent Information")

    >[!NOTE]
    >Il titolare dell'account AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account.
    >
    >[!NOTE]
    >È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Freshdesk per eseguire il provisioning degli account utente Azure AD in FreshDesk.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di FreshDesk, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di FreshDesk e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro FreshDesk in My Apps (app personali), si dovrebbe accedere automaticamente al FreshDesk per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato FreshDesk, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).