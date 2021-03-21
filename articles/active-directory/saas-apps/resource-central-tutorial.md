---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Resource Central | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Resource Central.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: dbc148fcbcd9c3be86a29df1e08755611a347b07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586629"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Resource Central

Questa esercitazione descrive come integrare Resource Central con Azure Active Directory (Azure AD). Integrando Resource Central con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a Resource Central.
* Abilitare gli utenti per l'accesso automatico a Resource Central con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Resource Central abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Resource Central supporta l'accesso SSO avviato da **SP**

* Resource Central supporta il provisioning utenti **JIT**

## <a name="add-resource-central-from-the-gallery"></a>Aggiungere Resource Central dalla raccolta

Per configurare l'integrazione di Resource Central in Azure AD, è necessario aggiungere Resource Central dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** , nella casella di ricerca, immettere **Resource Central**.
1. Selezionare **Resource Central** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-resource-central"></a>Configurare e testare l'accesso SSO di Azure AD per Resource Central

Configurare e testare l'accesso SSO di Azure AD con Resource Central usando un utente di test di nome **B.Simon**. Per consentire il funzionamento del Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Resource Central.

Per configurare e testare l'accesso SSO di Azure AD con Resource Central, completare la procedura seguente:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
    1. **[Creare l'utente di test di Resource Central](#create-resource-central-test-user)** : per avere una controparte di B.Simon in Resource Central collegata alla rappresentazione dell'utente in Azure AD.
1. **[Configurare l'accesso Single Sign-On di Resource Central](#configure-resource-central-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Resource Central** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. In **configurazione SAML di base** immettere i valori per i seguenti campi:

   1. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<DOMAIN_NAME>/ResourceCentral`

   1. Nella casella di testo **identificatore (ID entità)** Digitare un URL usando il modello seguente:  `https://<DOMAIN_NAME>/ResourceCentral`

   1. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Questi valori non sono valori letterali. Aggiornare questi valori con l'URL di accesso, l'identificatore e i valori di URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Resource Central](mailto:st@aod.vn).  È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella pagina **configura Single Sign-on con SAML** , in certificato di **firma SAML**, trova **certificato (base64)** e seleziona **download** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. In **configurare Resource Central**, copiare gli URL appropriati in base ai requisiti.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere `username@companydomain.extension`. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Resource Central.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Resource Central**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente**, quindi selezionare **utenti e gruppi** nel riquadro **Aggiungi assegnazione** .
1. Nel riquadro **utenti e gruppi** selezionare **B. Simon** dall'elenco **utenti** , quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che un ruolo venga assegnato agli utenti, è possibile selezionarlo in **selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, viene visualizzato il ruolo **accesso predefinito** selezionato.
1. Nel riquadro **Aggiungi assegnazione** fare clic sul pulsante **assegna** .

### <a name="create-resource-central-test-user"></a>Creare l'utente di test di Resource Central

In questa sezione viene creato un utente denominato **B. Simon** in **Resource Central**.

1. In Resource Central selezionare **Security**  >  **Persons**  >  **New**.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Screenshot che mostra il riquadro persone in Resource Central, con il pulsante nuovo evidenziato.":::

1. In **Dettagli persona** Digitare l'utente **B. Simon** per **nome visualizzato**. Per **indirizzo SMTP** immettere il nome utente Azure ad utente. Ad esempio, `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Screenshot che mostra il riquadro dei dettagli della persona in Resource Central.":::

## <a name="configure-resource-central-sso"></a>Configurare l'accesso Single Sign-On di Resource Central

In questa sezione si configurerà Single Sign-On nell' **amministratore di sistema Resource Central**.

1. In Resource Central System Administrator selezionare **External Authentication**.
1.  Per **Abilita configurazione** selezionare **Sì**.

    ![Screenshot che mostra l'opzione Abilita configurazione selezionata nel riquadro autenticazione esterna in Resource Central.](./media/resource-central/enable.png)

1. In **protocollo di autenticazione** selezionare **Saml2**. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Screenshot che mostra il SAML2 selezionato per il protocollo di autenticazione in Resource Central.":::

1. In **configurazione di Saml2** immettere i valori per i seguenti campi:

    1. Per **identificatore (ID entità)**, **URL di accesso**, **URL di disconnessione** e **identificatore Azure ad**, immettere gli URL pertinenti:

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Screenshot del riquadro di configurazione di SAML2 in Resource Central.":::

        Copiare gli URL dal riquadro **imposta risorsa centrale** :

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Screenshot del riquadro centrale per la configurazione delle risorse in Resource Central.":::

   1. Per **Return URL (URL restituito**) immettere `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler` .
  
1. Per **certificato**, caricare il certificato e immettere la password.

   ![Screenshot della sezione certificate in Resource Central.](./media/resource-central/cert.png)
   
1. Selezionare **Salva**.

1. Tornare al **portale di Azure**. In **certificato di firma SAML** caricare il certificato e immettere la password.

   ![Screenshot del riquadro Importa certificato nel portale di Azure.](./media/resource-central/cert2.png).

1. Selezionare **Aggiungi**.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD. Per testare Single Sign-On sono disponibili tre opzioni:

* Nel portale di Azure selezionare **Test this application** (Testa questa applicazione). Il collegamento reindirizza all'URL di accesso centrale delle risorse, in cui è possibile avviare l'accesso.

* Passare direttamente all'URL di accesso di Resource Central e avviare l'accesso.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Screenshot della pagina Web di test della Single Sign-On centrale delle risorse.":::

* Usare il portale app personali di Microsoft. Nel portale app personali selezionare il riquadro **Resource Central** per eseguire il reindirizzamento all'URL di accesso di Resource Central. Per altre informazioni, vedere [Accedere e avviare app dal portale App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Resource Central per Single Sign-On con Azure AD, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
