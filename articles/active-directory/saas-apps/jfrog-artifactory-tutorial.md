---
title: 'Esercitazione: Integrazione di Azure Active Directory con JFrog Artifactory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e JFrog Artifactory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: b943be684d84e1e193d9318e9f1c6423dcd38795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648923"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Esercitazione: Integrare JFrog Artifactory con Azure Active Directory

Questa esercitazione descrive come integrare JFrog Artifactory con Azure Active Directory (Azure AD). Integrando JFrog Artifactory con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a JFrog Artifactory.
* Abilitare gli utenti per l'accesso automatico a JFrog Artifactory con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di JFrog Artifactory abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* JFrog Artifactory supporta il protocollo SSO avviato da **SP e IDP** .
* JFrog Artifactory supporta il provisioning degli utenti **just-in-Time** .

## <a name="add-jfrog-artifactory-from-the-gallery"></a>Aggiungere JFrog Artifactory dalla raccolta

Per configurare l'integrazione di JFrog Artifactory in Azure AD, è necessario aggiungere JFrog Artifactory dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **JFrog Artifactory** nella casella di ricerca.
1. Selezionare **JFrog Artifactory** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>Configurare e testare Azure AD SSO per JFrog Artifactory

Configurare e testare l'accesso Single Sign-On di Azure AD con JFrog Artifactory usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in JFrog Artifactory.

Per configurare e testare Azure AD SSO con JFrog Artifactory, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare JFrog ARTIFACTORY SSO](#configure-jfrog-artifactory-sso)** per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare l'utente di test di JFrog Artifactory](#create-jfrog-artifactory-test-user)** : per avere una controparte di B.Simon in JFrog Artifactory collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **JFrog Artifactory** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `<servername>.jfrog.io`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:
    
    - Per Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Per Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente:
    - Per Artifactory 6. x: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Per Artifactory 7. x: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di JFrog Artifactory](https://support.jfrog.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione JFrog Artifactory prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic sull'icona **modifica** per aprire la finestra di dialogo attributi utente.

    ![Screenshot che mostra la finestra Attributi utente con il controllo di modifica evidenziato.](common/edit-attribute.png)

1. Oltre alla precedente, JFrog Artifactory prevede che venga restituito un certo numero di attributi aggiuntivi nella risposta SAML. Nella sezione **Attributi e attestazioni utente** della finestra di dialogo **Attestazioni gruppo (Anteprima)** seguire questa procedura:

    a. Fare clic sulla **penna** accanto a **Gruppi restituiti nell'attestazione**.

    ![Screenshot che mostra il riquadro Attributi utente e attestazioni con l'icona di modifica selezionata.](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![Screenshot che mostra la sezione Attestazioni di gruppo con l'opzione Tutti i gruppi selezionata.](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. Selezionare **Tutti i gruppi** dall'elenco dei pulsanti di opzione.

    c. Fare clic su **Salva**.

4. Nella sezione **certificato di firma SAML** della pagina **Configura singolo Sign-On con SAML** individuare il **certificato (base64)** e selezionare **download** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Configurare Artifactory (nome del provider di servizi SAML) con il campo "Identifier" (vedere il passaggio 4). Nella sezione **set up JFrog Artifactory** copiare gli URL appropriati in base ai requisiti.

   - Per Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Per Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a JFrog Artifactory.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **JFrog Artifactory**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-jfrog-artifactory-sso"></a>Configurare l'accesso SSO di JFrog Artifactory

Per configurare l'accesso Single Sign-On sul lato **JFrog Artifactory**, è necessario inviare il **Certificato (base)** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di JFrog Artifactory](https://support.jfrog.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-jfrog-artifactory-test-user"></a>Creare l'utente di test di JFrog Artifactory

In questa sezione viene creato un utente di nome B.Simon in JFrog Artifactory. JFrog Artifactory supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in JFrog Artifactory, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di JFrog Artifactory in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di JFrog Artifactory e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al Artifactory JFrog per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro JFrog Artifactory in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente al Artifactory JFrog per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato JFrog Artifactory, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).