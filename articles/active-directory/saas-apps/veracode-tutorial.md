---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con VeraCode | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Veracode.
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
ms.openlocfilehash: f56f2dc974df58575c72c93a0609026cd7bbf88d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652624"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con VeraCode

Questa esercitazione descrive come integrare Veracode con Azure Active Directory (Azure AD). Integrando Veracode con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Veracode.
* Abilitare gli utenti per l'accesso automatico a Veracode con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Veracode abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. VeraCode supporta l'accesso SSO avviato da provider di identità e il provisioning utenti Just-In-Time (JIT).

## <a name="add-veracode-from-the-gallery"></a>Aggiungere Veracode dalla raccolta

Per configurare l'integrazione di Veracode in Azure AD, aggiungere Veracode dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** Digitare "VeraCode" nella casella di ricerca.
1. Selezionare **Veracode** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Configurare e testare Azure AD SSO per VeraCode

Configurare e testare l'accesso SSO di Azure AD con Veracode usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire un collegamento tra un utente di Azure AD e l'utente correlato in Veracode.

Per configurare e testare Azure AD SSO con VeraCode, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On per Veracode](#configure-veracode-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare un utente di test di Veracode](#create-veracode-test-user)**: per avere una controparte di B.Simon in Veracode collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure nella pagina di integrazione dell'applicazione **VeraCode** trovare la sezione **Gestisci** . e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. Selezionare **Salva**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** . Selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Screenshot della sezione Certificato di firma SAML con il collegamento Scarica evidenziato](common/certificatebase64.png)

1. Veracode prevede un formato specifico per le asserzioni SAML, in base al quale è necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Screenshot della sezione Attributi utente e attestazioni](common/default-attributes.png)

1. Veracode prevede inoltre il passaggio di qualche altro attributo nella risposta SAML. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base alle esigenze.

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |

1. Nella sezione **Configura Veracode** copiare gli URL appropriati in base alle esigenze.

    ![Screenshot della sezione Configura Veracode con gli URL di configurazione evidenziati](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Veracode.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Veracode**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è &quot;Accesso predefinito&quot;.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name=&quot;configure-veracode-sso&quot;></a>Configurare l'accesso Single Sign-On di Veracode

1. In un'altra finestra del Web browser accedere al sito aziendale di Veracode come amministratore.

1. Dal menu in alto, selezionare **Impostazioni**  >  **amministratore**.
   
    ![Screenshot della schermata di amministrazione di Veracode, con l'icona delle impostazioni e l'opzione per l'accesso come amministratore evidenziate](./media/veracode-tutorial/admin.png &quot;Amministrazione")

1. Selezionare la scheda **SAML**.

1. Nella sezione **Impostazioni SAML dell’organizzazione** seguire questa procedura:

    ![Screenshot della sezione relativa alle impostazioni SAML dell'organizzazione](./media/veracode-tutorial/saml.png "Amministrazione")

    a.  Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Per **Assertion Signing Certificate** (Certificato di firma dell'asserzione) selezionare **Choose File** (Scegli file) per caricare il certificato scaricato dal portale di Azure.

    c. Per **Self Registration** (Registrazione self-service) selezionare **Enable Self Registration** (Abilita registrazione self-service).

1. Nella sezione **Self Registration Settings** (Impostazioni di registrazione self-service) seguire questa procedura e quindi fare clic su **Save** (Salva):

    ![Screenshot della sezione delle impostazioni di registrazione self-service con diverse opzioni evidenziate](./media/veracode-tutorial/save.png "Amministrazione")

    a. Per **New User Activation** (Attivazione nuovo utente) selezionare **No Activation Required** (Nessuna attivazione richiesta).

    b. Per **User Data Updates** (Aggiornamenti dati utenti) selezionare **Preference Veracode User Data** (Dati utenti Veracode di preferenza).

    c. In **Dettagli sull’attributo SAML** selezionare le opzioni seguenti:
      * **Ruoli utente**
      * **Policy Administrator**
      * **Reviewer**
      * **Security Lead**
      * **Executive**
      * **Submitter**
      * **Autore**
      * **All Scan Types**
      * **Team Memberships**
      * **Default Team**

### <a name="create-veracode-test-user"></a>Creare l'utente di test di Veracode

In questa sezione viene creato un utente denominato B. Simon in VeraCode. VeraCode supporta il provisioning utente just-in-time, abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già in VeraCode, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente di Veracode per effettuare il provisioning degli account utente di Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente al VeraCode per il quale si configura l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro VeraCode in My Apps (app personali), si dovrebbe accedere automaticamente al VeraCode per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato VeraCode, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).