---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler Private Access (ZPA) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler Private Access (ZPA).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 347fc931dcc7f794fe7376daaac21ce7ed9198df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184744"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Esercitazione: Integrare Zscaler Private Access (ZPA) con Azure Active Directory

Questa esercitazione descrive come integrare Zscaler Private Access (ZPA) con Azure Active Directory (Azure AD). Integrando Zscaler Private Access (ZPA) con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Zscaler Private Access (ZPA).
* Abilitare gli utenti per l'accesso automatico a Zscaler Private Access (ZPA) con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Zscaler Private Access (ZPA) abilitata per l'accesso Single Sign-On.

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

* Zscaler Private Access (ZPA) supporta l'accesso SSO avviato da **SP**.

## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Aggiungere Zscaler Private Access (ZPA) dalla raccolta

Per configurare l'integrazione di Zscaler Private Access (ZPA) in Azure AD, è necessario aggiungere Zscaler Private Access (ZPA) dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Zscaler Private Access (ZPA)** nella casella di ricerca.
1. Selezionare **Zscaler Private Access (ZPA)** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

Configurare e testare Azure AD SSO con zScaler private Access (ZPA) usando un utente di test di nome **B. Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler Private Access (ZPA).

Per configurare e testare Azure AD SSO con zScaler private Access (ZPA), seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare zScaler private Access (ZPA) SSO](#configure-zscaler-private-access-zpa-sso)** per configurare le impostazioni Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di zScaler private Access (ZPA)](#create-zscaler-private-access-zpa-test-user)** : per avere una controparte di B. Simon in zScaler private Access (ZPA) collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **zScaler private Access (ZPA)** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    1. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Nella casella di testo **Identificatore (ID entità)** digitare l'URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > Poiché il valore **URL accesso** non è reale, È necessario aggiornare il valore con l'URL di accesso effettivo. Contattare il [team di supporto clienti di Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) per ottenere il valore. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Zscaler Private Access (ZPA)** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a zScaler private Access (ZPA).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni, selezionare **Zscaler Private Access (ZPA)** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-zscaler-private-access-zpa-sso"></a>Configurare SSO ZPA (zScaler private Access)

1. Per automatizzare la configurazione all'interno di Zscaler Private Access (ZPA), è necessario installare l'**estensione del browser per l'accesso sicuro My Apps** facendo clic su **Install the extension** (Installa l'estensione).

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Zscaler Private Access (ZPA)** per passare direttamente all'applicazione Zscaler Private Access (ZPA). Nell'applicazione fornire le credenziali di amministratore per accedere a Zscaler Private Access (ZPA). L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Zscaler Private Access (ZPA), aprire una nuova finestra del Web browser, accedere al sito aziendale di Zscaler Private Access (ZPA) come amministratore e seguire questa procedura:

4. Nel lato sinistro del menu fare clic su **Administration** (Amministrazione) e passare alla sezione **AUTHENTICATION** (AUTENTICAZIONE), quindi fare clic su **IdP Configuration** (Configurazione IdP).

    ![Amministrazione dell'amministratore di Zscaler Private Access](./media/zscalerprivateaccess-tutorial/administration.png)

5. Nell'angolo superiore destro, fare clic su **Aggiungi configurazione IdP**. 

    ![IdP dell'amministratore di Zscaler Private Access](./media/zscalerprivateaccess-tutorial/metadata.png)

6. Nella pagina **Aggiungi configurazione IdP**, eseguire la procedura seguente:
 
    ![Selezione dell'amministratore di Zscaler Private Access](./media/zscalerprivateaccess-tutorial/select.png)

    a. Fare clic su **Seleziona file** per caricare il file di metadati scaricati da Azure AD nel campo **Caricamento file di metadati IdP**.

    b. Vengono letti i **metadati IdP** da Azure AD e vengono compilate tutte le informazioni dei campi, come mostrato di seguito.

    ![Configurazione dell'amministratore di Zscaler Private Access](./media/zscalerprivateaccess-tutorial/configure.png)

    c. Selezionare il dominio dal campo **Domini**.
    
    d. Fare clic su **Salva**.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Creare l'utente di test di Zscaler Private Access (ZPA)

In questa sezione viene creato un utente di nome Britta Simon in a Zscaler Private Access (ZPA). Consultare il [team di supporto di Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) per aggiungere utenti sulla piattaforma a Zscaler Private Access (ZPA).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di zScaler private Access (ZPA) in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso a zScaler private Access (ZPA) e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro zScaler private Access (ZPA) in app personali, questo verrà reindirizzato all'URL di accesso zScaler private Access (ZPA). Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurato l'accesso privato zScaler (ZPA), è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).