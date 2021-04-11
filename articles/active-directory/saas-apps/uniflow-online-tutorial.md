---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con uniFLOW Online | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e uniFLOW Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9fdcd8a82b901e00e28f0ddd89ba53d9a2e3fbae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952682"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con uniFLOW Online

Questa esercitazione descrive come integrare uniFLOW Online con Azure Active Directory (Azure AD). Integrando uniFLOW Online con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a uniFLOW Online.
* Abilitare gli utenti per l'accesso a uniFLOW Online con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Tenant di uniFLOW Online.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* uniFLOW Online supporta l'accesso SSO avviato da **SP**

## <a name="add-uniflow-online-from-the-gallery"></a>Aggiungere uniFLOW online dalla raccolta

Per configurare l'integrazione di uniFLOW Online in Azure AD, è necessario aggiungere uniFLOW Online dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **uniFLOW Online** nella casella di ricerca.
1. Selezionare **uniFLOW Online** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>Configurare e testare Azure AD SSO per uniFLOW online

Configurare e testare l'accesso SSO di Azure AD con uniFLOW Online usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in uniFLOW Online.

Per configurare e testare Azure AD SSO con uniFLOW online, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
   1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
   1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On per uniFLOW Online](#configure-uniflow-online-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Accedere a uniFLOW Online con l'utente di test creato](#sign-in-to-uniflow-online-using-the-created-test-user)** : per testare l'accesso utente sul lato applicazione.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **uniFLOW online** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** Digitare un URL usando uno dei modelli seguenti:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL in uno dei formati seguenti:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di uniFLOW Online](mailto:support@nt-ware.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure o all'URL di risposta visualizzato nel tenant di uniFLOW Online.

1. L'applicazione uniFLOW Online prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione uniFLOW Online prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome |  Attributo di origine|
    | -----------| --------------- |
    | displayname | user.displayname |
    | nickname | user.onpremisessamaccountname |

   > [!NOTE]
   > L'attributo `user.onpremisessamaccountname` conterrà un valore solo se gli utenti Azure AD vengono sincronizzati da un'istanza locale di Windows Active Directory.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a uniFLOW Online.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **uniFLOW Online**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

> [!NOTE]
> Per consentire a tutti gli utenti di accedere all'applicazione senza assegnazione manuale, passare alla sezione **Gestione** e selezionare **Proprietà**. Modificare quindi l'impostazione del parametro **Assegnazione di utenti obbligatoria** in **NO**.

## <a name="configure-uniflow-online-sso"></a>Configurare l'accesso Single Sign-On per uniFLOW Online

1. In un'altra finestra del Web browser accedere al sito Web di uniFLOW Online come amministratore.

1. Nel pannello di spostamento a sinistra selezionare la scheda **User** (Utente).

    ![Screenshot che mostra l'opzione User selezionata nel sito uniflow Online.](./media/uniflow-online-tutorial/configure-1.png)

1. Fare clic su **Provider di identità**.

    ![Screenshot che mostra l'opzione Identity Providers selezionata.](./media/uniflow-online-tutorial/configure-2.png)

1. Fare clic su **Add identity provider** (Aggiungi provider di identità).

    ![Screenshot che mostra l'opzione Add Identity Providers selezionata.](./media/uniflow-online-tutorial/configure-3.png)

1. Nella sezione **ADD IDENTITY PROVIDER** (AGGIUNGI PROVIDER DI IDENTITÀ) seguire questa procedura:

    ![Screenshot che mostra la sezione ADD IDENTITY PROVIDER in cui è possibile immettere i valori indicati.](./media/uniflow-online-tutorial/configure-4.png)

    a. Immettere il nome visualizzato, ad esempio *AzureAD SSO*.

    b. Per **Provider type** (Tipo di provider) selezionare l'opzione **WS-Fed** nell'elenco a discesa.

    c. Per **WS-Fed type** (Tipo WS-Fed) selezionare l'opzione **Azure Active Directory** nell'elenco a discesa.

    d. Fare clic su **Salva**.

1. Nella scheda **General** (Generale) seguire questa procedura:

    ![Screenshot che mostra la scheda General in cui è possibile immettere i valori indicati.](./media/uniflow-online-tutorial/configure-5.png)

    a. Immettere il nome visualizzato, ad esempio *AzureAD SSO*.

    b. Selezionare l'opzione **From URL** (Da URL) per **ADFS Federation Metadata** (Metadati della federazione ADFS).

    c. Nella casella di testo **Federation Metadata URL** (URL dei metadati della federazione) incollare il valore di **URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    d. Impostare **Identity provider** (Provider di identità) su **Enabled** (Abilitato).

    e. Impostare **Automatic user registration** (Registrazione utenti automatica) su **Activated** (Attivata).

    f. Fare clic su **Salva**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Accedere a uniFLOW Online con l'utente di test creato

1. In un'altra finestra del Web browser passare all'URL di uniFLOW Online per il tenant.

1. Selezionare il provider di identità creato in precedenza per eseguire l'accesso tramite l'istanza di Azure AD.

1. Accedere con l'utente di test.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso a uniFLOW online in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso a uniFLOW online e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro uniFLOW online in app personali, questo verrà reindirizzato all'URL di accesso a uniFLOW online. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato uniFLOW online, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).