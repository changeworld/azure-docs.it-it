---
title: 'Esercitazione: Integrazione di Azure Active Directory con Ivanti Service Manager (ISM) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Ivanti Service Manager (ISM).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 16aceeddcba574953ac03ccf04d099762b0000c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648884"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Esercitazione: Integrazione di Azure Active Directory con Ivanti Service Manager

In questa esercitazione si apprenderà come integrare ivanti Service Manager (ISM) con Azure Active Directory (Azure AD). Quando si integra ivanti Service Manager (ISM) con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a ivanti Service Manager (ISM).
* Abilitare gli utenti per l'accesso automatico a ivanti Service Manager (ISM) con i rispettivi account di Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ivanti Service Manager (ISM) Single Sign-On abilitata per l'accesso SSO.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Ivanti Service Manager (ISM) supporta l'accesso SSO avviato da **SP e IDP**
* Ivanti Service Manager (ISM) supporta il provisioning utenti **Just In Time** (JIT)

## <a name="add-ivanti-service-manager-ism-from-the-gallery"></a>Aggiungere ivanti Service Manager (ISM) dalla raccolta

Per configurare l'integrazione di Ivanti Service Manager (ISM) in Azure AD, è necessario aggiungere Ivanti Service Manager (ISM) dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **IVANTI Service Manager (ISM)** nella casella di ricerca.
1. Selezionare **Ivanti Service Manager (ISM)** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-ivanti-service-manager-ism"></a>Configurare e testare Azure AD SSO per ivanti Service Manager (ISM)

Configurare e testare Azure AD SSO con ivanti Service Manager (ISM) usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in ivanti Service Manager (ISM).

Per configurare e testare Azure AD SSO con ivanti Service Manager (ISM), seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Ivanti Service Manager (ISM) SSO](#configure-ivanti-service-manager-ism-sso)** per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Ivanti Service Manager (ISM)](#create-ivanti-service-manager-ism-test-user)** : per avere una controparte di B. Simon in ivanti Service Manager (ISM) collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Ivanti Service Manager (ISM)** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    
    ```http
    https://<customer>.saasit.com/
    https://<customer>.saasiteu.com/
    https://<customer>.saasitau.com/
    ```

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<customer>.saasit.com/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **Certificato (base)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificateraw.png)

7. Nella sezione **Configura Ivanti Service Manager (ISM)** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a ivanti Service Manager (ISM).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Ivanti Service Manager (ISM)** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-ivanti-service-manager-ism-sso"></a>Configurare ivanti Service Manager (ISM) SSO

Per configurare l'accesso Single Sign-On sul lato **Ivanti Service Manager (ISM)** , è necessario inviare il file **Certificato (base)** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-ivanti-service-manager-ism-test-user"></a>Creare un utente di test di Ivanti Service Manager (ISM)

In questa sezione viene creato un utente di nome Britta Simon in Ivanti Service Manager (ISM). Ivanti Service Manager (ISM) supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Ivanti Service Manager (ISM), ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso ivanti Service Manager (ISM) in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso a ivanti Service Manager (ISM) e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al Service Manager IVANTI (ISM) per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro ivanti Service Manager (ISM) in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente al Service Manager ivanti (ISM) per il quale si configura SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato ivanti Service Manager (ISM) è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).