---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cisco Webex | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cisco Webex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ad6d5308638b112afe2b51c4e149f876651e429d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592524"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cisco Webex

Questa esercitazione descrive come integrare Cisco Webex con Azure Active Directory (Azure AD). Integrando Cisco Webex con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Cisco Webex.
* Abilitare gli utenti per l'accesso automatico a Cisco Webex con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Cisco Webex abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Cisco Webex supporta l'accesso SSO avviato da **SP**.
* Cisco WebEx supporta il [**provisioning automatizzato degli utenti**](./cisco-webex-provisioning-tutorial.md).

## <a name="adding-cisco-webex-from-the-gallery"></a>Aggiunta di Cisco Webex dalla raccolta

Per configurare l'integrazione di Cisco Webex in Azure AD, è necessario aggiungere Cisco Webex dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Cisco Webex** nella casella di ricerca.
1. Selezionare **Cisco Webex** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Configurare e testare Azure AD SSO per Cisco WebEx

Configurare e testare l'accesso SSO di Azure AD con Cisco Webex usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cisco Webex.

Per configurare e testare Azure AD SSO con Cisco WebEx, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
2. **[Configurare Cisco Webex](#configure-cisco-webex)** per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di Cisco Webex](#create-cisco-webex-test-user)** per avere una controparte di B. Simon in Cisco Webex collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Cisco WebEx** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** caricare il **file di metadati del provider di servizi** e configurare l'applicazione seguendo questa procedura:

    >[!Note]
    >Si otterrà il file di metadati del provider di servizi in base alla procedura descritta più avanti nella sezione **Configurare Cisco Webex** dell'esercitazione. 

    a. Fare clic su **Carica il file di metadati**.

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    c. Al termine del caricamento del file di metadati del provider di servizi, i valori dell'**Identificatore** e dell'**URL di risposta** vengono popolati automaticamente nella sezione **Configurazione SAML di base**:

    Nella casella di testo **URL di accesso** incollare il valore **URL di risposta**, che viene compilato automaticamente dal caricamento del file di metadati del provider di servizi.

1. L'applicazione Cisco Webex prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre a quelli elencati in precedenza, l'applicazione Cisco Webex prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
  
    | Nome |  Attributo di origine|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    > [!NOTE]
    >  Per impostazione predefinita, il valore dell'attributo di origine è mappato a userpricipalname. Questo può essere modificato in User. mail o User. onpremiseuserprincipalname o qualsiasi altro valore in base all'impostazione in WebEx.


1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/metadataxml.png)

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

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cisco Webex.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Cisco Webex**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-cisco-webex"></a>Configurare Cisco Webex

1. Accedere a Cisco WebEx con le credenziali di amministratore.

1. Selezionare **Impostazioni organizzazione** e, nella sezione **autenticazione** , fare clic su **modifica**.

    ![Screenshot che mostra la schermata delle impostazioni di autenticazione in cui selezionare Modify.](./media/cisco-spark-tutorial/organization-settings.png)
  
1. Selezionare **integra un provider di identità di terze parti. (Avanzate)** e fare clic su **Avanti.**

    ![Screenshot mostra come integrare un provider di identità di terze parti.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Fare clic su **Scarica file di metadati** per scaricare il **file di metadati del provider di servizi** e salvarlo nel computer, quindi fare clic su **Avanti.**

    ![Screenshot che mostra il file di metadati del provider di servizi.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Fare clic sull'opzione **file browser** per individuare e caricare il file di metadati Azure ad. Selezionare quindi **Require certificate signed by a certificate authority in Metadata (more secure)** (Richiedi certificato firmato da un'autorità di certificazione in metadati - più sicura) e fare clic su **Next** (Avanti).

    ![Screenshot che mostra la pagina Import IdP Metadata.](./media/cisco-spark-tutorial/idp-metadata.png)

1. Selezionare **Test SSO Connection** (Test connessione SSO) e, quando viene aperta una nuova scheda del browser, eseguire l'autenticazione con Azure AD effettuando l'accesso.

1. Tornare alla scheda **Cisco Cloud Collaboration Management** del browser. Se il test ha avuto esito positivo, selezionare l'opzione **This test was successful. Enable Single Sign-On**  (Test riuscito. Abilita Single Sign-On), quindi fare clic su **Next** (Avanti).

1. Fare clic su **Salva**.

> [!NOTE]
> Per ulteriori informazioni su come configurare Cisco WebEx, fare riferimento a [questa](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) pagina.

### <a name="create-cisco-webex-test-user"></a>Creare un utente di test per Cisco Webex

In questa sezione, un utente denominato B. Simon viene creato in Cisco WebEx. questa applicazione supporta il provisioning utenti automatico, che consente il provisioning e il deprovisioning automatici in base alle regole di business.  Quando possibile, Microsoft consiglia di usare il provisioning automatico. Vedere come abilitare il provisioning automatico per [Cisco WebEx](./cisco-webex-provisioning-tutorial.md).

Per creare un utente manualmente, seguire questa procedura:

1. Accedere a Cisco WebEx con le credenziali di amministratore.

2. Fare clic su **Users** (Utenti) e quindi su **Manage Users** (Gestisci utenti).
   
    ![Screenshot che mostra la pagina Users in cui selezionare Manage Users.](./media/cisco-spark-tutorial/user-1.png) 

3. Nella finestra **Gestisci utenti** selezionare **Aggiungi o modifica utenti manualmente**.

    ![Screenshot mostra la pagina utenti in cui è possibile gestire gli utenti e selezionare Aggiungi o modifica manualmente gli utenti.](./media/cisco-spark-tutorial/user-2.png)

4. Selezionare **Names and Email address** (Nomi e indirizzi di posta elettronica). Compilare quindi la casella di testo come indicato di seguito:

    ![Screenshot che mostra la finestra di dialogo Manage Users in cui è possibile aggiungere manualmente o modificare gli utenti.](./media/cisco-spark-tutorial/user-3.png) 

    a. Nella casella di testo **First Name** (Nome) digitare il nome dell'utente, ad esempio **B**.

    b. Nella casella di testo **Last name** (Cognome) digitare il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio b.simon@contoso.com.

5. Fare clic sul segno più per aggiungere B.Simon. Quindi fare clic su **Next**.

6. Nella finestra **Aggiungi servizi per utenti** fare clic su **Aggiungi utenti** e quindi su **fine**.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso a Cisco WebEx in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso a Cisco WebEx e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Cisco WebEx in app personali, questo verrà reindirizzato all'URL di accesso a Cisco WebEx. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Cisco WebEx è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)