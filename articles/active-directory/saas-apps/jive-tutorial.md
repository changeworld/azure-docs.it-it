---
title: 'Esercitazione: Integrazione di Azure Active Directory con Jive | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: bcfc2996daeb34f357625572d39661638982ae42
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489124"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Jive

Questa esercitazione descrive come integrare Jive con Azure Active Directory (Azure AD). Integrando Jive con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Jive.
* Abilitare gli utenti per l'accesso automatico a Jive con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Jive abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Jive supporta SSO avviato da **SP** .
* Jive supporta il [provisioning **automatizzato** degli utenti](jive-provisioning-tutorial.md).

## <a name="add-jive-from-the-gallery"></a>Aggiungere Jive dalla raccolta

Per configurare l'integrazione di Jive in Azure AD, è necessario aggiungere Jive dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Jive** nella casella di ricerca.
1. Selezionare **Jive** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-jive"></a>Configurare e testare Azure AD SSO per Jive

Configurare e testare l'accesso SSO di Azure AD con Jive usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Jive.

Per configurare e testare Azure AD SSO con Jive, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Jive](#configure-jive-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Jive](#create-jive-test-user)** : per avere una controparte di B.Simon in Jive collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Jive** del portale di Azure trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

   a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<instance name>.jivecustom.com`

   b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente:
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto client di Jive](https://www.jivesoftware.com/services-support/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Jive** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Jive.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Jive**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-jive-sso"></a>Configurare l'accesso Single Sign-On di Jive

1. Per configurare l'accesso single sign-on sul lato **Jive**, accedere al tenant Jive come amministratore.

1. Nel menu in alto fare clic su **SAML**.

    ![Screenshot che mostra la scheda SAML con l'opzione Enabled selezionata.](./media/jive-tutorial/jive-2.png)

    a. Selezionare **Abilitato** nella scheda **Generale**.

    b. Fare clic sul pulsante **SAVE ALL SAML SETTINGS** (SALVA TUTTE LE IMPOSTAZIONI SAML).

1. Passare alla scheda **IDP METADATA** (METADATI IDP).

    ![Screenshot che mostra la scheda SAML con l'opzione I D P METADATA selezionata.](./media/jive-tutorial/jive-3.png)

    a. Copiare il contenuto del file XML di metadati scaricato e incollarlo nella casella di testo **Identity Provider (IdP) Metadata** (Metadati del provider di identità - IdP).

    b. Fare clic sul pulsante **SAVE ALL SAML SETTINGS** (SALVA TUTTE LE IMPOSTAZIONI SAML).

1. Selezionare la scheda **USER ATTRIBUTE MAPPING** (MAPPING ATTRIBUTI UTENTE).

    ![Screenshot che mostra la scheda SAML con l'opzione USER ATTRIBUTE MAPPING selezionata.](./media/jive-tutorial/jive-4.png)

    a. Nella casella di testo **Email** copiare e incollare il nome dell'attributo del valore **mail**.

    b. Nella casella di testo **Nome** copiare e incollare il nome dell'attributo del valore **givenname**.

    c. Nella casella di testo **Cognome** copiare e incollare il nome dell'attributo del valore **surname**.

### <a name="create-jive-test-user"></a>Creare l'utente di test di Jive

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Jive. Jive supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. Altri dettagli su come configurare il provisioning utenti automatico sono disponibili [qui](jive-provisioning-tutorial.md).

Per creare manualmente un utente, collaborare con il [Team di supporto clienti di Jive](https://www.jivesoftware.com/services-support/) per aggiungere gli utenti alla piattaforma Jive.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso a Jive in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso a Jive e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Jive in app personali, viene reindirizzato all'URL di accesso a Jive. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Jive è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).