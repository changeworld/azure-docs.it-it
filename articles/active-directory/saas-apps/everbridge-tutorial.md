---
title: 'Esercitazione: Integrazione di Azure Active Directory con Everbridge | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Everbridge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 6e281931eb4646e09bb9aa3226ed7d0735c84e3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643780"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Esercitazione: Integrazione di Azure Active Directory con Everbridge

Questa esercitazione descrive come integrare Everbridge con Azure Active Directory (Azure AD).
Quando si integra Everbridge con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Everbridge.
* Consentire agli utenti di eseguire l'accesso automatico (Single Sign-On) a Everbridge con gli account Azure AD personali. Questo controllo di accesso è denominato accesso Single Sign-On (SSO).
* È possibile gestire gli account in un'unica posizione centrale usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Everbridge, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Everbridge che usa l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Everbridge supporta l'accesso SSO avviato da IDP.

## <a name="add-everbridge-from-the-gallery"></a>Aggiungere Everbridge dalla raccolta

Per configurare l'integrazione di Everbridge in Azure AD, è necessario aggiungere Everbridge dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Everbridge** nella casella di ricerca.
1. Selezionare **Everbridge** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>Configurare e testare Azure AD SSO per Everbridge

Configurare e testare Azure AD SSO con Everbridge usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Everbridge.

Per configurare e testare Azure AD SSO con Everbridge, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare EVERBRIDGE SSO](#configure-everbridge-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Everbridge](#create-everbridge-test-user)** : per avere una controparte di B. Simon in Everbridge collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Everbridge** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

    >[!NOTE]
    >Configurare l'applicazione come portale di gestione *o* come portale dei membri sia nel portale di Azure che nel portale Everbridge.

4. Per configurare l'applicazione **Everbridge** come **portale di gestione di Everbridge** nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Everbridge](common/idp-intiated.png)

    a. Nella casella **identificatore** immettere un URL che segue il modello.
    `https://sso.everbridge.net/<API_Name>`

    b. Nella casella **URL di risposta** immettere un URL che segue il modello.
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di Everbridge](mailto:support@everbridge.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Per configurare l'applicazione **Everbridge** come **portale dei membri di Everbridge** nella sezione **Configurazione SAML di base** seguire questa procedura:

  * Se si vuole configurare l'applicazione in modalità avviata da IDP, seguire questa procedura:

     ![Informazioni su URL e dominio per l'accesso Single Sign-On di Everbridge per la modalità avviata da IDP](common/idp-intiated.png)

    a. Nella casella **Identificatore** immettere un URL nel formato `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Nella casella **URL di risposta** immettere un URL nel formato `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Se si vuole configurare l'applicazione in modalità avviata da SP, selezionare **Impostare URL aggiuntivi** ed eseguire il passaggio seguente:

     ![Informazioni su URL e dominio per l'accesso Single Sign-On di Everbridge per la modalità avviata da SP](common/both-signonurl.png)

     a. Nella casella **URL di accesso** immettere un URL nel formato `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di Everbridge](mailto:support@everbridge.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** per scaricare il file **XML dei metadati della federazione**. Salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura Everbridge** copiare gli URL necessari in base alle esigenze:

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Everbridge.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Everbridge**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-everbridge-sso"></a>Configurare Everbridge SSO

Per configurare l'accesso SSO in **Everbridge** come applicazione del **portale di gestione di Everbridge**, seguire questa procedura.
 
1. In un'altra finestra del Web browser accedere a Everbridge come amministratore.

1. Nel menu in alto selezionare la scheda **Impostazioni** . In **sicurezza** selezionare **Single Sign-on**.
   
     ![Configura accesso Single Sign-On](./media/everbridge-tutorial/sso.png)
   
     a. Nella casella **Name** (Nome) immettere il nome del provider dell'identificatore, ad esempio il nome della società.
   
     b. Nella casella **API Name** (Nome API) immettere il nome dell'API.
   
     c. Selezionare **Choose File** (Scegli file) per caricare il file di metadati scaricato dal portale di Azure.
   
     d. Per **SAML Identity location** selezionare **Identity is in the NameIdentifier element of the subject Statement**.
   
     e. Nella casella **Identity Provider Login URL** (URL di accesso del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.
   
     f. Per **Service Provider Initiated Request Binding** (Binding delle richieste avviate dal provider di servizi) selezionare **HTTP Redirect** (Reindirizzamento HTTP).

     g. Selezionare **Salva**.

### <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>Configurare Everbridge come portale membro Everbridge SSO

Per configurare l'accesso Single Sign-On in **Everbridge** come **portale dei membri di Everbridge**, inviare il file **XML dei metadati della federazione** scaricato al [team di supporto di Everbridge](mailto:support@everbridge.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-everbridge-test-user"></a>Creare un utente di test di Everbridge

In questa sezione viene creato l'utente di test Britta Simon in Everbridge. Per aggiungere gli utenti alla piattaforma Everbridge, collaborare con il [team di supporto di Everbridge](mailto:support@everbridge.com). Gli utenti devono essere creati e attivati in Everbridge prima di usare l'accesso Single Sign-On. 

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente al Everbridge per il quale si configura l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Everbridge in My Apps (app personali), si dovrebbe accedere automaticamente al Everbridge per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Everbridge, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).