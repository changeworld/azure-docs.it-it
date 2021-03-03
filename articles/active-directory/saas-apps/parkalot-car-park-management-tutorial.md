---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Parkalot-Car Park Management | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e la gestione di Parkalot-Car Park.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 0098d28d2b211ad9e4bbe60a44c5de4058f2b96b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651421"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-parkalot---car-park-management"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Parkalot-Car Park Management

Questa esercitazione illustra come integrare Parkalot-Car Park Management con Azure Active Directory (Azure AD). Quando si integra la gestione di Parkalot-Car Park con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso alla gestione di Parkalot-Car Park.
* Consentire agli utenti di accedere automaticamente alla gestione di Parkalot-Car Park con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Parkalot-Car Park Management Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Parkalot-gestione del parcheggio supporta SSO avviato da **SP**

* Parkalot-gestione del Car Park supporta il provisioning degli utenti **just-in-Time**

## <a name="adding-parkalot---car-park-management-from-the-gallery"></a>Aggiunta di Parkalot-Car Park Management dalla raccolta

Per configurare l'integrazione di Parkalot-Car Park Management in Azure AD, è necessario aggiungere Parkalot-Car Park Management dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Parkalot-Car Park Management** nella casella di ricerca.
1. Selezionare **Parkalot-Car Park Management** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-parkalot---car-park-management"></a>Configurare e testare Azure AD SSO per Parkalot-gestione parcheggio

Configurare e testare Azure AD SSO con Parkalot-Car Park Management usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Parkalot-Car Park Management.

Per configurare e testare Azure AD SSO con Parkalot-Car Park Management, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare il Parkalot-Car SSO di gestione del parco](#configure-parkalot-car-park-management-sso)** per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Parkalot-Car Park Management](#create-parkalot-car-park-management-test-user)** : per avere una controparte di B. Simon in Parkalot-Car Park Management collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure della pagina di integrazione dell'applicazione **Parkalot-Car Park Management** individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL in uno dei formati seguenti:

    | Identificatore (ID entità) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. Nella casella di testo **URL di risposta** digitare un URL in uno dei formati seguenti:

    | URL di risposta |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. Nella casella di testo **URL di accesso** digitare un URL in uno dei formati seguenti:

    | URL di accesso |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Parkalot-Car Park Management](mailto:contact-us@parkalot.io) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **configurare la gestione di Parkalot-Car Park** copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso alla gestione di Parkalot-Car Park.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Parkalot-Car Park Management**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-parkalot-car-park-management-sso"></a>Configurare l'accesso SSO di gestione Parkalot-Car Park

1. In un'altra finestra del Web browser accedere al sito aziendale di Parkalot-Car Park Management come amministratore.

1. Selezionare **Setup SAML** e fare clic sull'icona **Edit** ( **Aggiungi nuova** scheda).

    ![Aggiungi nuova icona modifica.](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. Eseguire i passaggi indicati di seguito nella seguente pagina.

    ![Configurare Parkalot-Car Park Management SSO.](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. Nella casella di testo **nome visualizzato** assegnare un nome valido.

    b. Nella casella di testo **IDP Entity ID (ID entità IDP** ) incollare il valore dell' **identificatore Azure AD** copiato dall'portale di Azure.

    c. Nella casella di testo **SSO URL (URL SSO** ) incollare il valore dell' **URL di accesso** copiato dal portale di Azure.

    d. Aprire nel Blocco note il file del **certificato (Base64)** scaricato dal portale di Azure e incollarne il contenuto nella casella di testo **Certificate** (Certificato).

    e. Fare clic su **SAVE**.

### <a name="create-parkalot-car-park-management-test-user"></a>Creare un utente di test di Parkalot-Car Park Management

In questa sezione viene creato un utente di nome Britta Simon in Parkalot-Car Park Management. Parkalot-gestione del Car Park supporta il provisioning degli utenti just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già nella gestione Parkalot-Car Park, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Parkalot-Car Park Management in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di gestione di Parkalot-Car Park e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Parkalot-Car Park Management in app personali, questo verrà reindirizzato all'URL di accesso di Parkalot-Car Park Management. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la gestione di Parkalot-Car Park, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).