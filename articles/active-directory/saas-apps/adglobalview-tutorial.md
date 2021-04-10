---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con ADP GlobalView (deprecata) | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e ADP GlobalView (deprecato).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 08b1294436ead372234104008a48ca23e56de389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589311"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp-globalview-deprecated"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con ADP GlobalView (deprecata)

In questa esercitazione si apprenderà come integrare ADP GlobalView (deprecato) con Azure Active Directory (Azure AD). Quando si integra ADP GlobalView (deprecato) con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad ADP GlobalView (deprecato).
* Abilitare gli utenti per l'accesso automatico ad ADP GlobalView (deprecato) con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ADP GlobalView (deprecata) abilitata per la Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ADP GlobalView (deprecato) supporta l'accesso SSO avviato da **IDP** .

## <a name="adding-adp-globalview-deprecated-from-the-gallery"></a>Aggiunta di ADP GlobalView (deprecato) dalla raccolta

Per configurare l'integrazione di ADP GlobalView (deprecato) in Azure AD, è necessario aggiungere ADP GlobalView (deprecato) dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ADP GlobalView (deprecato)** nella casella di ricerca.
1. Selezionare **ADP GlobalView (deprecato)** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-adp-globalview-deprecated"></a>Configurare e testare Azure AD SSO per ADP GlobalView (deprecato)

Configurare e testare Azure AD SSO con ADP GlobalView (deprecato) usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in ADP GlobalView (deprecato).

Per configurare e testare Azure AD SSO con ADP GlobalView (deprecato), seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare ADP GlobalView (deprecato) SSO](#configure-adp-globalview-deprecated-sso)** per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di ADP GlobalView (deprecated)](#create-adp-globalview-deprecated-test-user)** : per avere una controparte di B. Simon in ADP GlobalView (deprecata) collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **ADP GlobalView (deprecated)** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    
    Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | Identificatore |
    | ----------- |
    | `https://<subdomain>.globalview.adp.com/federate` |
    | `https://<subdomain>.globalview.adp.com/federate2` |
    |

    > [!NOTE]
    > Poiché non è reale, È necessario aggiornare questo valore con l'ID effettivo. Per ottenere il valore, contattare il [team di supporto clienti di ADP GlobalView (deprecato)](https://www.adp.com/contact-us/overview.aspx) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **configurare ADP GlobalView (deprecato)** copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso ad ADP GlobalView (deprecato).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **ADP GlobalView (deprecato)**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-adp-globalview-deprecated-sso"></a>Configurare ADP GlobalView (deprecato) SSO

Per configurare Single Sign-On sul lato **ADP GlobalView (deprecato)** , è necessario inviare il **certificato (base64)** scaricato e gli URL copiati appropriati da portale di Azure al [team di supporto di ADP GlobalView (deprecato)](https://www.adp.com/contact-us/overview.aspx). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-adp-globalview-deprecated-test-user"></a>Creare un utente di test di ADP GlobalView (deprecato)

In questa sezione viene creato un utente denominato B. Simon in ADP GlobalView (deprecato). Collaborare con il [team di supporto di ADP GlobalView (deprecato)](https://www.adp.com/contact-us/overview.aspx) per aggiungere gli utenti nella piattaforma ADP GlobalView (deprecated). Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente ad ADP GlobalView (deprecato) per il quale si configura l'accesso SSO

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro ADP GlobalView (deprecated) in app personali, si dovrebbe accedere automaticamente al GlobalView ADP (deprecato) per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato ADP GlobalView (deprecato) è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).