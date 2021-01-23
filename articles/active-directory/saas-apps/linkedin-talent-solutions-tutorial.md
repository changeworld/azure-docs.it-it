---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con LinkedIn Talent Solutions | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LinkedIn Talent Solutions.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: 160c7514b095a330a52dd1607dca6f2eb87215d8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98727340"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-talent-solutions"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con LinkedIn Talent Solutions

Questa esercitazione descrive come integrare LinkedIn Talent Solutions con Azure Active Directory (Azure AD). Integrando LinkedIn Talent Solutions con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a LinkedIn Talent Solutions.
* Abilitare gli utenti per l'accesso automatico a LinkedIn Talent Solutions con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Accedere ad Account Center nel dashboard di LinkedIn Talent Solutions

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* LinkedIn Talent Solutions supporta l'accesso SSO avviato da **SP e IDP**
* LinkedIn Talent Solutions supporta il provisioning utenti **JIT**


## <a name="adding-linkedin-talent-solutions-from-the-gallery"></a>Aggiunta di LinkedIn Talent Solutions dalla raccolta

Per configurare l'integrazione di LinkedIn Talent Solutions in Azure AD, è necessario aggiungere LinkedIn Talent Solutions dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **LinkedIn Talent Solutions** nella casella di ricerca.
1. Selezionare **LinkedIn Talent Solutions** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-talent-solutions"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per LinkedIn Talent Solutions

Configurare e testare l'accesso SSO di Azure AD con LinkedIn Talent Solutions usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LinkedIn Talent Solutions.

Per configurare e testare l'accesso SSO di Azure AD con LinkedIn Talent Solutions, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di LinkedIn Talent Solutions](#configure-linkedin-talent-solutions-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di LinkedIn Talent Solutions](#create-linkedin-talent-solutions-test-user)** : per avere una controparte di B.Simon in LinkedIn Talent Solutions collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **LinkedIn Talent Solutions** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    a. Fare clic su **Carica il file di metadati**.

    ![immagine1](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Immagine2](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione Configurazione SAML di base:

    ![immagine3](common/idp-intiated.png)

    > [!Note]
    > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, inserirli manualmente in base alle proprie esigenze.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://www.linkedin.com/talent/`

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura LinkedIn Talent Solutions** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LinkedIn Talent Solutions.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **LinkedIn Talent Solutions**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-linkedin-talent-solutions-sso"></a>Configurare l'accesso Single Sign-On di LinkedIn Talent Solutions

1. Accedere al sito Web di LinkedIn Talent Solutions come amministratore.
1. Passare ad **Account Center** (Centro account). 
1. Selezionare la scheda **Settings** (Impostazioni) sulla barra di spostamento.

    ![pagina delle impostazioni](./media/linkedin-talent-solutions-tutorial/settings.png)

1. Espandere la sezione **Single Sign-On (SSO)** . 

1. Fare clic sul pulsante **Download** (Scarica) per scaricare il **file di metadati** oppure fare clic sul collegamento **or click here to load and copy individual fields from the form** (oppure fare clic qui per caricare e copiare i singoli campi del modulo) per visualizzare i dati di configurazione.

    ![ Dati di configurazione](./media/linkedin-talent-solutions-tutorial/sso-settings.png)

1. Seguire questa procedura per copiare singoli campi del modulo.

    ![ Configurazione con dati di input](./media/linkedin-talent-solutions-tutorial/configuration.png)

    a. Copiare il valore di **Entity ID** (ID entità) e incollarlo nella casella di testo **Identificatore Azure AD** nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Copiare il valore di **ACS URL** (URL ACS) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    c. Copiare il contenuto della casella di testo **SP X.509 Certificate(signing)** (Certificato X.509 SP - firma) e negli Appunti e salvarlo nel computer.

1. Fare clic su **Upload XML file** (Carica file XML) per caricare il file **XML dei metadati della federazione** copiato dal portale di Azure.

    ![ Opzione Upload XML file](./media/linkedin-talent-solutions-tutorial/xml-file.png)

### <a name="create-linkedin-talent-solutions-test-user"></a>Creare l'utente di test di LinkedIn Talent Solutions

In questa sezione viene creato un utente di nome Britta Simon in LinkedIn Talent Solutions. LinkedIn Talent Solutions supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in LinkedIn Talent Solutions, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di LinkedIn Talent Solutions, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di LinkedIn Talent Solutions e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di LinkedIn Talent Solutions per cui si è configurato l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di LinkedIn Talent Solutions in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di LinkedIn Talent Solutions per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato LinkedIn Talent Solutions, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).