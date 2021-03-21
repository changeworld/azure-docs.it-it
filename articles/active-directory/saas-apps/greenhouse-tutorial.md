---
title: 'Esercitazione: Integrazione di Azure Active Directory con Greenhouse | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Greenhouse.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 77f72d6c63231f0854b58470f86c65ffc81c9775
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731921"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Esercitazione: Integrazione di Azure Active Directory con Greenhouse

Questa esercitazione descrive come integrare Greenhouse con Azure Active Directory (Azure AD). Integrando Greenhouse con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Greenhouse.
* Abilitare gli utenti per l'accesso automatico a Greenhouse con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Greenhouse abilitata per l'accesso Single Sign-On (SSO).

> [!NOTE] 
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico. 

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Greenhouse supporta l'accesso SSO avviato da **SP**

## <a name="adding-greenhouse-from-the-gallery"></a>Aggiunta di Greenhouse dalla raccolta

Per configurare l'integrazione di Greenhouse in Azure AD, è necessario aggiungere Greenhouse dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Greenhouse** nella casella di ricerca.
1. Selezionare **Greenhouse** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Configurare e testare l'accesso SSO di Azure AD per Greenhouse

Configurare e testare l'accesso SSO di Azure AD con Greenhouse usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Greenhouse.

Per configurare e testare l'accesso SSO di Azure AD con Greenhouse, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di Greenhouse](#configure-greenhouse-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Greenhouse](#create-greenhouse-test-user)**: per avere una controparte di Britta Simon in Greenhouse collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Greenhouse** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.greenhouse.io`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<companyname>.greenhouse.io`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere tali valori, contattare il [team di supporto clienti di Greenhouse](https://www.greenhouse.io/contact). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Greenhouse** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Greenhouse.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Greenhouse**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-greenhouse-sso"></a>Configurare l'accesso SSO per Greenhouse

1. In un'altra finestra del Web browser accedere al sito Web di Greenhouse come amministratore.

1. Passare a **Configure > Dev Center > Single Sign-On**.

    ![Screenshot della pagina SSO](./media/greenhouse-tutorial/configure.png)

1. Nella pagina Single Sign-On seguire questa procedura.

    ![Screenshot della pagina di configurazione di SSO](./media/greenhouse-tutorial/sso-page.png)

    a. Copiare il valore di **SSO Assertion Consumer URL** (URL consumer di asserzione SSO) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Nella casella di testo **Entity ID/Issuer** (ID entità/Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **Single Sign-On URL** (URL di accesso SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Aprire nel Blocco note il file **XML dei metadati della federazione**, scaricato dal portale di Azure, quindi incollarne il contenuto nella casella di testo **IdP Certificate Fingerprint** (Impronta digitale del certificato IdP).

    e. Selezionare il valore di **Name Identifier Format** (Formato identificatore nome) nell'elenco a discesa.

    f. Fare clic su **Begin Testing** (Inizia test).

    >[!NOTE]
    >In alternativa, è anche possibile caricare il file **XML dei metadati di federazione** facendo clic sull'opzione **Choose File** (Scegli file).

### <a name="create-greenhouse-test-user"></a>Creare l'utente di test di Greenhouse

Per consentire agli utenti di Azure AD di accedere a Greenhouse, è necessario eseguirne il provisioning in Greenhouse. Nel caso di Greenhouse, il provisioning è un'attività manuale.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Greenhouse per effettuare il provisioning degli account utente Azure AD. 

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito della società **Greenhouse** come amministratore.

2. Passare a **Configure > Users > New Users** (Configura > Utenti > Nuovi utenti)
   
    ![Utenti](./media/greenhouse-tutorial/create-user-1.png "Utenti")

4. Nella sezione **Add New Users** eseguire la procedura seguente:
   
    ![Aggiungi nuovo utente](./media/greenhouse-tutorial/create-user-2.png "Aggiungi nuovo utente")

    a. Nella casella di testo **Enter user emails** digitare l'indirizzo di posta elettronica di un account di Azure Active Directory valido di cui si vuole eseguire il provisioning.

    b. Fare clic su **Salva**.    
   
      >[!NOTE]
      >I titolari dell'account Azure Active Directory riceveranno un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Greenhouse, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Greenhouse e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Greenhouse in App personali, verrà eseguito il reindirizzamento all'URL di accesso di Greenhouse. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Greenhouse, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).