---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Shopify Plus | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Shopify Plus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 65f4963f23d97ca2e3af34febb0d5dbea652fc12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646980"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Shopify Plus

Questa esercitazione descrive come integrare Shopify Plus con Azure Active Directory (Azure AD). Integrando Shopify Plus con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Shopify Plus.
* Abilitare gli utenti per l'accesso automatico a Shopify Plus con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Shopify Plus abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Shopify Plus supporta SSO avviato da **SP e IDP** .

## <a name="add-shopify-plus-from-the-gallery"></a>Aggiungere Shopify Plus dalla raccolta

Per configurare l'integrazione di Shopify Plus in Azure AD, è necessario aggiungere Shopify Plus dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Shopify Plus** nella casella di ricerca.
1. Selezionare **Shopify Plus** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-shopify-plus"></a>Configurare e testare Azure AD SSO per Shopify Plus

Configurare e testare l'accesso SSO di Azure AD con Shopify Plus usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Shopify Plus.

Per configurare e testare Azure AD SSO con Shopify Plus, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Shopify Plus](#configure-shopify-plus-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Shopify Plus](#create-shopify-plus-test-user)** : per avere una controparte di B.Simon in Shopify Plus collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Shopify Plus** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://shopify.plus/login`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. è necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Shopify Plus](mailto:plus-user-management@shopify.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Shopify Plus prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Shopify Plus prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---- | --------------- |
    | email | user.mail |

1. Modificare il formato di **Name ID** (ID nome) in **Persistente**. Selezionare l'opzione **Identificatore utente univoco (ID nome)** e quindi il formato **Identificatore nome**. Selezionare **Persistente** per questa opzione. Salvare le modifiche.
1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il pulsante di copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Shopify Plus.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Shopify Plus**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-shopify-plus-sso"></a>Configurare l'accesso Single Sign-On di Shopify Plus

Per la procedura completa, vedere la [documentazione di Shopify sulla configurazione delle integrazioni SAML](https://help.shopify.com/en/manual/shopify-plus/saml).

Per configurare Single Sign-On sul lato **Shopify Plus**, copiare l'**URL dei metadati di federazione dell'app** da Azure Active Directory. Accedere quindi alla schermata di [amministrazione dell'organizzazione](https://shopify.plus) e passare a **Users** > **Security** (Utenti > Sicurezza). Selezionare **Set up configuration** (Imposta configurazione) e incollare l'URl dei metadati di federazione dell'app nella sezione **Identity provider metadata URL** (URL dei metadati del provider di identità). Per completare questo passaggio, selezionare **Add** (Aggiungi).

### <a name="create-shopify-plus-test-user"></a>Creare l'utente di test di Shopify Plus

In questa sezione viene creato un utente di nome B.Simon in Shopify Plus. Tornare alla sezione **Users** (Utenti) e aggiungere un utente immettendo l'indirizzo di posta elettronica e le autorizzazioni. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="enforce-saml-authentication"></a>Applicare l'autenticazione SAML

> [!NOTE]
> È consigliabile testare l'integrazione usando singoli utenti prima di applicarla a livello generale.

Singoli utenti:
1. Passare alla pagina di un singolo utente in Shopify Plus con un dominio di posta elettronica gestito da Azure AD e verificato in Shopify Plus.
1. Nella sezione di autenticazione SAML selezionare **Edit** (Modifica), **Required** (Obbligatoria) e quindi **Save** (Salva).
1. Verificare che l'utente possa accedere correttamente tramite i flussi avviati da IdP e SP.

Per tutti gli utenti di un dominio di posta elettronica:
1. Tornare alla pagina **Security** (Sicurezza).
1. Selezionare **Required** (Obbligatoria) per l'impostazione di autenticazione SAML. In questo modo l'autenticazione SAML verrà applicata a tutti gli utenti con tale dominio di posta elettronica in Shopify Plus.
1. Selezionare **Save** (Salva).

> [!IMPORTANT]
> L'abilitazione di SAML per tutti gli utenti in un dominio di posta elettronica ha effetto su tutti gli utenti che usano l'applicazione. Gli utenti non potranno accedere usando la normale pagina di accesso, ma potranno accedere all'app solo tramite Azure Active Directory. Shopify non fornisce un URL di accesso per il backup che gli utenti possono usare per accedere usando il nome utente e la password normali. Se necessario, è possibile contattare il supporto di Shopify per disattivare l'autenticazione SAML.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Shopify Plus, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Shopify Plus e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente a Shopify Plus per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Shopify Plus in app personali, se è configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al Shopify Plus per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Shopify Plus, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).