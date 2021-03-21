---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Catchpoint"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Catchpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: a8515920985c569df74b1e328d6bfe1c4ec97195
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735315"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Esercitazione: Integrazione dell'accesso Single Sign-On di Azure Active Directory con Catchpoint

Questa esercitazione descrive come integrare Catchpoint con Azure Active Directory (Azure AD). Integrando Catchpoint con Azure AD, è possibile:

* Controllare l'accesso utente a Catchpoint da Azure AD.
* Abilitare l'accesso automatico a Catchpoint per gli utenti con account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Catchpoint abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Catchpoint supporta l'accesso SSO avviato da SP e IDP.
* Catchpoint supporta il provisioning utenti JIT.

## <a name="add-catchpoint-from-the-gallery"></a>Aggiungere Catchpoint dalla raccolta

Per configurare l'integrazione di Catchpoint in Azure AD, aggiungere Catchpoint all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Catchpoint** nella casella di ricerca.
1. Selezionare **Catchpoint** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-catchpoint"></a>Configurare e testare l'accesso SSO di Azure AD per Catchpoint

Per consentire il funzionamento dell'accesso Single Sign-On, è necessario collegare un utente Azure AD a un utente in Catchpoint. Per questa esercitazione verrà configurato un utente di test di nome **B.Simon**. 

Completare le sezioni seguenti:

1. [Configurare l'accesso SSO di Azure AD](#configure-azure-ad-sso), per abilitare questa funzionalità per gli utenti.
    * [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user), per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user), per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On di Catchpoint](#configure-catchpoint-sso), per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * [Creare l'utente di test di Catchpoint](#create-a-catchpoint-test-user), per consentire il collegamento dell'account di test di Azure AD di B.Simon a un account utente simile in Catchpoint.
1. [Testare l'accesso SSO](#test-sso), per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD, seguire questa procedura nel portale di Azure:

1. Accedere al portale di Azure.
1. Nella pagina di integrazione dell'applicazione **Catchpoint** individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Configurare la modalità avviata per Catchpoint:
   - Per la modalità avviata da **IDP** immettere i valori per i campi seguenti:
     - In **Identificatore**: `https://portal.catchpoint.com/SAML2`
     - In **URL di risposta**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - Per la modalità avviata da **SP** selezionare **Imposta URL aggiuntivi** e immettere il valore seguente:
     - In **URL di accesso dell'app**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. L'applicazione RolePoint prevede un formato specifico per le asserzioni SAML. Aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Nella tabella seguente sono elencati gli attributi predefiniti:

    | Nome | Attributo di origine|
    | ------------ | --------- |
    | Givenname | user.givenname |
    | Surname | user.surname |
    | Emailaddress | user.mail |
    | Nome | user.userprincipalname |
    | Identificatore univoco dell'utente | user.userprincipalname |

    ![Screenshot dell'elenco Attributi utente e attestazioni](common/default-attributes.png)

1. Inoltre, l'applicazione Catchpoint prevede il passaggio di un altro attributo in una risposta SAML. Vedere la tabella seguente. Anche questo attributo viene prepopolato, ma è possibile esaminarlo e aggiornarlo in base alle esigenze.

    | Nome | Attributo di origine|
    | ------------ | --------- |
    | namespace | user.assignedrole |

    > [!NOTE]
    > È necessario eseguire il mapping dell'attestazione di `namespace` con il nome dell'account. Questo nome dell'account deve essere configurato con un ruolo in Azure AD, che verrà restituito nella risposta SAML. Per altre informazioni sui ruoli in Azure AD, vedere [Configurare l'attestazione basata su ruolo rilasciata nel token SAML per applicazioni aziendali](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Passare alla pagina **Configura l'accesso Single Sign-On con SAML**. Nella sezione **Certificato di firma SAML** individuare il **certificato (Base64)** . Selezionare **Scarica** per salvare il certificato nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Catchpoint** copiare gli URL necessari in un passaggio successivo.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione si usa il portale di Azure per creare un utente di test di Azure AD di nome B.Simon.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Immettere ad esempio `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password**. Prendere nota del valore visualizzato della password.
   1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Catchpoint.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Catchpoint**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Fare clic su **Seleziona** nella parte inferiore della schermata.
1. Se i ruoli sono stati configurati come illustrato sopra, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-catchpoint-sso"></a>Configurare l'accesso Single Sign-On di Catchpoint

1. In un'altra finestra del Web browser accedere all'applicazione Catchpoint come amministratore.

1. Fare clic sull'icona **Settings** (Impostazioni) e selezionare **SSO Identity Provider** (Provider di identità SSO).

    ![Screenshot delle impostazioni di Catchpoint con l'opzione SSO Identity Provider selezionata](./media/catchpoint-tutorial/configuration1.png)

1. Nella pagina **Single Sign-On** immettere un valore nei campi seguenti:

   ![Screenshot della pagina Single Sign-On di Catchpoint](./media/catchpoint-tutorial/configuration2.png)

   Campo | Valore
   ----- | ----- 
   **Namespace** | Valore di uno spazio dei nomi valido.
   **Identity Provider Issuer** | Valore di `Azure AD Identifier` nel portale di Azure.
   **Single Sign On Url** | Valore di `Login URL` nel portale di Azure.
   **Certificate** | Contenuto del file `Certificate (Base64)` scaricato dal portale di Azure. Usare il Blocco note per la visualizzazione e la copia.

   È anche possibile caricare il file **XML dei metadati di federazione** selezionando l'opzione **Upload Metadata** (Carica metadati).

1. Selezionare **Save** (Salva).

### <a name="create-a-catchpoint-test-user"></a>Creare l'utente di test di Catchpoint

Catchpoint supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. In questa sezione non è previsto alcun intervento da parte dell'utente. Se l'utente B.Simon non esiste già in Catchpoint, viene creato dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Catchpoint, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Catchpoint e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Testa l'applicazione** nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Catchpoint per cui si è configurato l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Catchpoint in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Catchpoint per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


> [!NOTE]
> Quando si accede all'applicazione Catchpoint tramite la pagina di accesso, dopo aver specificato le **credenziali di Catchpoint**, immettere il valore valido di **Namespace** (Spazio dei nomi) nel campo **Company Credentials(SSO)** (Credenziali aziendali - SSO) e selezionare **Login** (Accedi).
> 
> ![Configurazione di Catchpoint](./media/catchpoint-tutorial/loginimage.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Catchpoint, è possibile applicare il controllo sessione. Questa precauzione consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
