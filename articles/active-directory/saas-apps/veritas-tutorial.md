---
title: 'Esercitazione: Integrazione di Azure Active Directory con Veritas Enterprise Vault.cloud SSO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Veritas Enterprise Vault.cloud SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 2796928316197bd48723253dbc97dfcd34ac95e8
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222283"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Esercitazione: Integrazione di Azure Active Directory con Veritas Enterprise Vault.cloud SSO

In questa esercitazione si apprenderà come integrare VERITAS Enterprise Vault.cloud SSO con Azure Active Directory (Azure AD). Quando si integra VERITAS Enterprise Vault.cloud SSO con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a VERITAS Enterprise Vault.cloud SSO.
* Consentire agli utenti di accedere automaticamente a VERITAS Enterprise Vault.cloud SSO con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Veritas Enterprise Vault.cloud SSO, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di VERITAS Enterprise Vault.cloud SSO abilitata per la Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* VERITAS Enterprise Vault.cloud SSO supporta l'accesso SSO avviato da **SP** .

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Aggiungere VERITAS Enterprise Vault.cloud SSO dalla raccolta

Per configurare l'integrazione di Veritas Enterprise Vault.cloud SSO in Azure AD, è necessario aggiungere Veritas Enterprise Vault.cloud SSO dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **VERITAS Enterprise Vault.Cloud SSO** nella casella di ricerca.
1. Selezionare **VERITAS Enterprise Vault.Cloud SSO** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-veritas-enterprise-vaultcloud-sso"></a>Configurare e testare Azure AD SSO for VERITAS Enterprise Vault.cloud SSO

Configurare e testare Azure AD SSO con VERITAS Enterprise Vault.cloud SSO usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in VERITAS Enterprise Vault.cloud SSO.

Per configurare e testare Azure AD SSO con VERITAS Enterprise Vault.cloud SSO, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare VERITAS Enterprise Vault.Cloud SSO](#configure-veritas-enterprise-vaultcloud-sso-sso)** SSO: per configurare le impostazioni Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Veritas enterprise Vault.Cloud SSO](#create-veritas-enterprise-vaultcloud-sso-test-user)** : per avere una controparte di B. Simon in Veritas Enterprise Vault.Cloud SSO collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **VERITAS Enterprise Vault.Cloud SSO** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. Nella casella **identificatore** Digitare uno degli URL in base al Data Center:

    | Data center| URL |
    |----------|----|
    | America del Nord| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Asia Pacifico| `https://auth.syd.archivecloud.net`|

    c. Nella casella di testo **URL di risposta** Digitare uno degli URL in base al Data Center:

    | Data center| URL |
    |----------|----|
    | America del Nord| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Asia Pacifico| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Veritas Enterprise Vault.cloud SSO** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a VERITAS Enterprise Vault.cloud SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Veritas Enterprise Vault.cloud SSO**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-veritas-enterprise-vaultcloud-sso-sso"></a>Configurare VERITAS Enterprise Vault.cloud SSO SSO

Per configurare l'accesso Single Sign-On sul lato **Veritas Enterprise Vault.cloud SSO**, è necessario inviare il file **Certificato (Base64)** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Creare l'utente di test di Veritas Enterprise Vault.cloud SSO

In questa sezione viene creato un utente chiamato Britta Simon in Veritas Enterprise Vault.cloud SSO. Collaborare con il [team di supporto di Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) per aggiungere gli utenti alla piattaforma Veritas Enterprise Vault.cloud SSO. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso SSO di VERITAS Enterprise Vault.cloud, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso Single Sign-on di VERITAS Enterprise Vault.cloud e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro VERITAS Enterprise Vault.cloud SSO in app personali, viene reindirizzato all'URL di accesso SSO di VERITAS Enterprise Vault.cloud. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato VERITAS Enterprise Vault.cloud SSO è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
