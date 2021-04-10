---
title: 'Esercitazione: Integrazione di Azure Active Directory con Splunk Enterprise and Splunk Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Splunk Enterprise e Splunk Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 81107b3655ae86d51e36445ce46661d553ab3b5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649853"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Splunk Enterprise and Splunk Cloud

Questa esercitazione illustra come integrare Splunk Enterprise e Splunk cloud con Azure Active Directory (Azure AD). Quando si integra Splunk Enterprise e Splunk cloud con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a Splunk Enterprise e Splunk cloud.
* Abilitare gli utenti per l'accesso automatico a Splunk Enterprise e Splunk cloud con i relativi account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Splunk Enterprise e Splunk cloud Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Splunk Enterprise and Splunk Cloud supporta l'accesso SSO avviato da **SP**

## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Aggiungere Splunk Enterprise e Splunk Cloud dalla raccolta

Per configurare l'integrazione di Splunk Enterprise e Splunk Cloud in Azure AD, è necessario aggiungere Splunk Enterprise e Splunk Cloud dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Splunk Enterprise e Splunk cloud** nella casella di ricerca.
1. Selezionare **Splunk Enterprise e Splunk cloud** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Configurare e testare Azure AD SSO per Splunk Enterprise e Splunk cloud

Configurare e testare Azure AD SSO con Splunk Enterprise e Splunk cloud usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Splunk Enterprise e Splunk cloud.

Per configurare e testare Azure AD SSO con Splunk Enterprise e Splunk cloud, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Splunk Enterprise e Splunk cloud SSO](#configure-splunk-enterprise-and-splunk-cloud-sso)** per configurare le impostazioni Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Splunk Enterprise e Splunk cloud](#create-splunk-enterprise-and-splunk-cloud-test-user)** : per avere una controparte di B. Simon in Splunk Enterprise e Splunk cloud collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Splunk Enterprise e Splunk cloud** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)
4. Nella sezione **configurazione SAML di base** eseguire il modello seguente:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<splunkserverUrl>/app/launcher/home`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `<splunkserverUrl>`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Splunk Enterprise e Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Splunk Enterprise e Splunk cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Splunk Enterprise e Splunk Cloud**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-splunk-enterprise-and-splunk-cloud-sso"></a>Configurare Splunk Enterprise e Splunk cloud SSO

  Per configurare l'accesso Single Sign-On sul lato **Splunk Enterprise and Splunk Cloud**, è necessario inviare il file **XML metadati federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Splunk Enterprise and Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Creare un utente di test di Splunk Enterprise and Splunk Cloud

In questa sezione viene creato un utente chiamato Britta Simon in Splunk Enterprise e Splunk Cloud. Collaborare con il [team di supporto di Splunk Enterprise e Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html) per aggiungere gli utenti alla piattaforma Splunk Enterprise e Splunk Cloud. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso Splunk Enterprise e Splunk cloud in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Splunk Enterprise e Splunk cloud e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Splunk Enterprise e Splunk cloud in app personali, questo verrà reindirizzato all'URL di accesso a Splunk Enterprise e Splunk cloud. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Splunk Enterprise e Splunk cloud, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)