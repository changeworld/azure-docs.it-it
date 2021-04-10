---
title: 'Esercitazione: Integrazione di Azure Active Directory con HubSpot | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e HubSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 91dfdcef01a121c8282b8fad2e75f67989b75dc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733251"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Esercitazione: Integrazione di Azure Active Directory con HubSpot

Questa esercitazione descrive come integrare HubSpot con Azure Active Directory (Azure AD). Integrando HubSpot con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a HubSpot.
* Abilitare gli utenti per l'accesso automatico a HubSpot con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con HubSpot, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di una sottoscrizione di Azure AD, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Una sottoscrizione a HubSpot con accesso Single Sign-On abilitato.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test e viene integrato HubSpot con Azure AD.

HubSpot supporta le funzionalità seguenti:

* **Accesso Single Sign-On avviato da provider di servizi**
* **Accesso Single Sign-On avviato da IDP**

## <a name="adding-hubspot-from-the-gallery"></a>Aggiunta di HubSpot dalla raccolta

Per configurare l'integrazione di HubSpot in Azure AD, è necessario aggiungere HubSpot dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **HubSpot** nella casella di ricerca.
1. Selezionare **HubSpot** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>Configurare e testare l'accesso SSO di Azure AD per HubSpot

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con HubSpot usando un utente di test di nome **Britta Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in HubSpot.

Per configurare e testare l'accesso Single Sign-On di Azure AD con HubSpot, è necessario completare le procedure di base seguenti:

| Attività | Descrizione |
| --- | --- |
| **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** | Consente agli utenti di usare questa funzionalità. |
| **[Configurare l'accesso Single Sign-On per HubSpot](#configure-hubspot-single-sign-on)** | Consente di configurare le impostazioni di accesso Single Sign-On nell'applicazione. |
| **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** | Consente di verificare l'accesso Single Sign-On di Azure AD per un utente chiamato Britta Simon. |
| **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** | Consente a Britta Simon di usare l'accesso Single Sign-On di Azure AD. |
| **[Creare un utente di test di HubSpot](#create-a-hubspot-test-user)** | Crea una controparte di Britta Simon in HubSpot collegata alla rappresentazione dell'utente in Azure AD. |
| **[Testare l'accesso Single Sign-On](#test-single-sign-on)** | Verifica se la configurazione funziona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

1. Nella pagina di integrazione dell'applicazione **HubSpot** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nel riquadro **Configurazione SAML di base** seguire questa procedura per configurare la *modalità avviata da IDP*:

    1. Nella casella **Identificatore** immettere un URL nel formato seguente: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>.

    1. Nella casella **URL di risposta** immettere un URL nel formato seguente: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di HubSpot](common/idp-intiated.png)

    > [!NOTE]
    > Per formattare gli URL, è anche possibile fare riferimento ai modelli mostrati nel riquadro **Configurazione SAML di base** del portale di Azure.

1. Per configurare l'applicazione in *modalità avviata da SP*:

    1. Selezionare **Imposta URL aggiuntivi**.

    1. Nella casella **URL di accesso** immettere **https:\//app.hubspot.com/login**.

    ![Opzione Impostare URL aggiuntivi](common/metadata-upload-additional-signon.png)

1. Nella sezione **Certificato di firma SAML** nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** accanto a **Certificato (Base64)** . Selezionare un'opzione di download in base alle esigenze. Salvare il certificato nel computer.

    ![Opzione di download Certificato (Base64)](common/certificatebase64.png)

1. Nella sezione **Configura HubSpot** copiare gli URL seguenti in base ai requisiti:

    * URL di accesso
    * Identificatore di Azure AD
    * URL di chiusura sessione

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Configurare l'accesso Single Sign-On per HubSpot

1. Aprire una nuova scheda del browser e accedere all'account di amministratore di HubSpot.

1. Selezionare l'icona **Impostazioni** nell'angolo superiore destro della pagina.

    ![L'icona Impostazioni in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selezionare **Account Defaults** (Impostazioni predefinite account).

    ![Opzione Account Defaults (Impostazioni predefinite account) in HubSpot](./media/hubspot-tutorial/config2.png)

1. Scorrere verso il basso fino alla sezione **Security** (Sicurezza) e quindi selezionare **Set up** (Configura).

    ![Opzione Set up (Configura) in HubSpot](./media/hubspot-tutorial/config3.png)

1. Nella sezione **Set Up Single Sign-On** (Configura Single Sign-On) seguire questa procedura:

    1. Nella casella **Audience URl (Service Provider Entity ID)** (URI destinatari - ID entità provider di servizio) selezionare **Copia** per copiare il valore. Nel riquadro **Configurazione SAML di base** del portale di Azure incollare il valore nella casella **Identificatore**.

    1. Nella casella **Sign on URl, ACS, Recipient, or Redirect** (URI di accesso, ACS, destinatario o reindirizzamento) selezionare **Copia** per copiare il valore. Nel riquadro **Configurazione SAML di base** del portale di Azure incollare il valore nella casella **URL di risposta**.

    1. In HubSpot, nella casella **Identity Provider Identifier or Issuer URL** (Identificatore provider identità o URL autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. In HubSpot, nella casella **Identity Provider Single Sign-On URL** (URL Single Sign-On provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Nel Blocco note di Windows aprire il file del certificato (Base64) scaricato. Selezionare e copiare il contenuto del file. Incollarlo quindi nella casella **X.509 Certificate** (Certificato X.509) in HubSpot.

    1. Selezionare **Verifica**.

        ![Sezione Set Up Single Sign-On (Configura Single Sign-On) in HubSpot](./media/hubspot-tutorial/config4.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a HubSpot.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **HubSpot**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-a-hubspot-test-user"></a>Creare un utente di test di HubSpot

Per consentire agli utenti di Azure AD di accedere a HubSpot, è necessario effettuarne il provisioning in HubSpot. Nel caso di HubSpot il provisioning è un'attività manuale.

Per effettuare il provisioning di un account utente in HubSpot:

1. Accedere al sito aziendale di HubSpot come amministratore.

1. Selezionare l'icona **Impostazioni** nell'angolo superiore destro della pagina.

    ![L'icona Impostazioni in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selezionare **Users & Teams** (Utenti e team).

    ![Opzione Users & Teams (Utenti e team) in HubSpot](./media/hubspot-tutorial/user1.png)

1. Selezionare **Create user** (Crea utente).

    ![Opzione Create user (Crea utente) in HubSpot](./media/hubspot-tutorial/user2.png)

1. Nella casella **Add email addess(es)** (Aggiungi indirizzi di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente nel formato brittasimon\@contoso.com e quindi selezionare **Avanti**.

    ![Casella Add email addess(es) (Aggiungi indirizzi di posta elettronica) nella sezione Create users (Crea utenti) in HubSpot](./media/hubspot-tutorial/user3.png)

1. Nella sezione **Create users (Crea utenti)** selezionare ogni scheda. In ogni scheda impostare le opzioni e le autorizzazioni appropriate per l'utente. Quindi selezionare **Avanti**.

    ![Schede nella sezione Create users (Crea utenti) in HubSpot](./media/hubspot-tutorial/user4.png)

1. Fare clic su **Send** (Invia) per inviare l'invito all'utente.

    ![L'opzione Send (Invia) in HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > L'utente viene attivato dopo aver accettato l'invito.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di HubSpot, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di HubSpot e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di HubSpot per cui si è configurato l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di HubSpot in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di HubSpot per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato HubSpot, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).