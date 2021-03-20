---
title: 'Esercitazione: Integrazione di Azure Active Directory con Secret Server (On-Premises)| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Secret Server (On-Premises).
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
ms.openlocfilehash: d723bf1ce82e6d443dfa55dda7d33a3a9bfc16b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647017"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Esercitazione: Integrare Secret Server (On-Premises) con Azure Active Directory

Questa esercitazione descrive come integrare Secret Server (On-Premises) con Azure Active Directory (Azure AD). Quando si integra Secret Server (On-Premises) con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Secret Server (On-Premises).
* Abilitare gli utenti per l'accesso automatico a Secret Server (On-Premises) con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Secret Server (On-Premises) abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Secret Server (On-Premises) supporta l'accesso SSO avviato da **provider di servizi e IDP**

## <a name="add-secret-server-on-premises-from-the-gallery"></a>Aggiungere Secret Server (On-Premises) dalla raccolta

Per configurare l'integrazione di Secret Server (On-Premises) in Azure AD, è necessario aggiungere Secret Server (On-Premises) dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Secret Server (On-Premises)** nella casella di ricerca.
1. Selezionare **Secret Server (On-Premises)** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-secret-server-on-premises"></a>Configurare e testare Azure AD SSO per Secret Server (On-Premises)

Configurare e testare l'accesso SSO di Azure AD con Secret Server (On-Premises) usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Secret Server (On-Premises).

Per configurare e testare Azure AD SSO con Secret Server (On-Premises), seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Secret Server (on-premises) SSO](#configure-secret-server-on-premises-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Secret Server (On-Premises)](#create-secret-server-on-premises-test-user)** : per avere una controparte di B.Simon in Secret Server (On-Premises) collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Secret Server (on-premises)** della portale di Azure trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://secretserveronpremises.azure`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > L'ID entità illustrato in precedenza è solo un esempio ed è possibile scegliere qualsiasi valore univoco che identifica l'istanza di Secret Server in Azure AD. È necessario inviare tale ID entità al [team di supporto clienti di Secret Server (On-Premises)](https://thycotic.force.com/support/s/) affinché il team possa eseguire la configurazione. Per altri dettagli, leggere [questo articolo](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di Secret Server (On-Premises)](https://thycotic.force.com/support/s/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML**.

    ![Screenshot che mostra la sezione "Certificato di firma SAML"con l'azione "Download" selezionata per "Certificato (Base64)".](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Selezionare **Opzione di firma** come **Firma asserzione e risposta SAML**.

    ![Opzioni di firma](./media/secretserver-on-premises-tutorial/signing-option.png)

1. Nella sezione **Configura Secret Server (On-Premises)** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Secret Server (On-Premises).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Secret Server (On-Premises)** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-secret-server-on-premises-sso"></a>Configurare l'accesso SSO di Secret Server (On-Premises)

Per configurare l'accesso Single Sign-On sul lato **Secret Server (On-Premises)** , è necessario inviare il file **Certificato (Base64)** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Secret Server (On-Premises)](https://thycotic.force.com/support/s/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-secret-server-on-premises-test-user"></a>Creare un utente di test di Secret Server (On-Premises)

In questa sezione viene creato un utente di nome Britta Simon in Secret Server (On-Premises). Collaborare con il [team di supporto di Secret Server (On-Premises)](https://thycotic.force.com/support/s/) per aggiungere gli utenti nella piattaforma di Secret Server (On-Premises). Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento a Secret Server (On-Premises) URL di accesso in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso Secret Server (On-Premises) e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al Secret Server (on-premises) per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Secret Server (On-Premises) in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente al Secret Server (On-Premises) per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurato Secret Server (On-Premises) è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).