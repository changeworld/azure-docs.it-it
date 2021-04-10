---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con FortiWeb Web Application Firewall | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e FortiWeb Web Application Firewall.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731937"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con FortiWeb Web Application Firewall

Questa esercitazione descrive come integrare FortiWeb Web Application Firewall con Azure Active Directory (Azure AD). Integrando FortiWeb Web Application Firewall con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a FortiWeb Web Application Firewall.
* Abilitare gli utenti per l'accesso automatico a FortiWeb Web Application Firewall con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di FortiWeb Web Application Firewall abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* FortiWeb Web Application Firewall supporta l'accesso SSO avviato da **SP**.

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>Aggiunta di FortiWeb Web Application Firewall dalla raccolta

Per configurare l'integrazione di FortiWeb Web Application Firewall in Azure AD, è necessario aggiungere FortiWeb Web Application Firewall dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **FortiWeb Web Application Firewall** nella casella di ricerca.
1. Selezionare **FortiWeb Web Application Firewall** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per FortiWeb Web Application Firewall

Configurare e testare l'accesso SSO di Azure AD con FortiWeb Web Application Firewall usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in FortiWeb Web Application Firewall.

Per configurare e testare l'accesso SSO di Azure AD con FortiWeb Web Application Firewall, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di FortiWeb Web Application Firewall](#configure-fortiweb-web-application-firewall-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di FortiWeb Web Application Firewall](#create-fortiweb-web-application-firewall-test-user)** : per avere una controparte di B.Simon in FortiWeb Web Application Firewall collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **FortiWeb Web Application Firewall** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

   a. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://www.<CUSTOMER_DOMAIN>.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://www.<CUSTOMER_DOMAIN>.com`

    d. Nella casella di testo **URL di disconnessione** digitare un URL nel formato seguente: `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` è un identificatore di nome che verrà usato in seguito quando si fornisce la configurazione a FortiWeb.
    > Per ottenere i valori degli URL effettivi, contattare il [team di supporto di FortiWeb Web Application Firewall](mailto:support@fortinet.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a FortiWeb Web Application Firewall.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **FortiWeb Web Application Firewall**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-fortiweb-web-application-firewall-sso"></a>Configurare l'accesso Single Sign-On di FortiWeb Web Application Firewall

1.  Passare a `https://<address>:8443` dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiWeb.

2.  Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiWeb.

1. Nella pagina successiva seguire questa procedura.

    ![Pagina del server SAML](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  Scegliere **User** (Utente) dal menu a sinistra.

    b.  In User (Utente) fare clic su **Remote Server** (Server remoto).

    c.  Fare clic su **SAML Server** (Server SAML).

    d.  Fare clic su **Create New**.

    e.  Nel campo **Name** (Nome) specificare il valore per `<fwName>` usato nella sezione Configurare Azure AD.

    f.  Nella casella di testo **ID entità** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    g. Accanto a **Metadata** (Metadati) fare clic su **Choose File** (Scegli file) e selezionare il file **XML dei metadati della federazione** scaricato dal portale di Azure.

    h.  Fare clic su **OK**.

### <a name="create-a-site-publishing-rule"></a>Creare una regola di pubblicazione siti

1.  Passare a `https://<address>:8443` dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiWeb.

1.  Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiWeb.
1. Nella pagina successiva seguire questa procedura.

    ![Regola di pubblicazione siti](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  Scegliere **Application Delivery** (Recapito applicazioni) dal menu a sinistra.
    
    b.  In **Application Delivery** (Recapito applicazioni) fare clic su **Site Publish** (Pubblicazione siti).
    
    c.  In **Site Publish** (Pubblicazione siti) fare clic su **Site Publish** (Pubblicazione siti).
    
    d.  Fare clic su **Site Publish Rule** (Regola di pubblicazione siti).
    
    e.  Fare clic su **Create New**.
    
    f.  Specificare un nome per la regola di pubblicazione siti.
    
    g.  Accanto a **Published Site Type** (Tipo di sito pubblicato) fare clic su **Regular Expression** (Espressione regolare).
    
    i.  Accanto a **Published Site** (Sito pubblicato) specificare una stringa che corrisponderà all'intestazione host del sito Web da pubblicare.
    
    j.  Accanto a **Path** (Percorso) immettere un carattere di barra rovesciata (/).
    
    k.  Accanto a **Client Authentication Method** (Metodo di autenticazione client) selezionare **SAML Authentication** (Autenticazione SAML).
    
    l.  Nell'elenco a discesa **SAML Server** (Server SAML) selezionare il server SAML creato in precedenza.
    
    m.  Fare clic su **OK**.


### <a name="create-a-site-publishing-policy"></a>Creare un criterio di pubblicazione siti

1.  Passare a `https://<address>:8443` dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiWeb.

2.  Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiWeb.

1. Nella pagina successiva seguire questa procedura.

    ![Criterio di pubblicazione siti](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  Scegliere **Application Delivery** (Recapito applicazioni) dal menu a sinistra.

    b.  In **Application Delivery** (Recapito applicazioni) fare clic su **Site Publish** (Pubblicazione siti).

    c.  In **Site Publish** (Pubblicazione siti) fare clic su **Site Publish** (Pubblicazione siti).

    d.  Fare clic su **Site Publish Policy** (Criterio di pubblicazione siti).

    e.  Fare clic su **Create New**.

    f.  Specificare un nome per il criterio di pubblicazione siti.

    g.  Fare clic su **OK**.

    h.  Fare clic su **Create New**.

    i.  Nell'elenco a discesa **Rule** (Regola) selezionare la regola di pubblicazione siti creata in precedenza.

    j.  Fare clic su **OK**.

### <a name="create-and-assign-a-web-protection-profile"></a>Creare e assegnare un profilo di protezione Web

1.  Passare a `https://<address>:8443` dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiWeb.

2.  Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiWeb.
3.  Scegliere **Policy** (Criterio) dal menu a sinistra.
4.  In **Policy** (Criterio) fare clic su **Web Protection Profile** (Profilo di protezione Web).
5.  Fare clic su **Inline Standard Protection** (Protezione standard inline) e fare clic su **Clone** (Clona).
6.  Specificare un nome per il profilo di protezione Web e fare clic su **OK**.
7.  Selezionare il nuovo profilo di protezione Web e fare clic su **Edit** (Modifica).
8.  Accanto a **Site Publish** (Pubblicazione siti) selezionare il criterio di pubblicazione siti creato in precedenza.
9.  Fare clic su **OK**.
 
    ![Opzione Site Publish](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. Scegliere **Policy** (Criterio) dal menu a sinistra.
11. In **Policy** (Criterio) fare clic su **Server Policy** (Criterio server).
12. Selezionare il criterio server usato per pubblicare il sito Web per cui si vuole usare Azure Active Directory per l'autenticazione.
13. Fare clic su **Modifica**.
14. Nell'elenco a discesa **Web Protection Profile** (Profilo di protezione Web) selezionare il profilo di protezione Web appena creato.
15. Fare clic su **OK**.
16. Provare ad accedere all'URL esterno usato da FortiWeb per la pubblicazione del sito Web. Si dovrebbe essere reindirizzati ad Azure Active Directory per l'autenticazione.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>Creare l'utente di test di FortiWeb Web Application Firewall

In questa sezione viene creato un utente di nome Britta Simon in FortiWeb Web Application Firewall. Collaborare con il [team di supporto di FortiWeb Web Application Firewall](mailto:support@fortinet.com) per aggiungere gli utenti alla piattaforma FortiWeb Web Application Firewall. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di FortiWeb Web Application, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di FortiWeb Web Application e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di FortiWeb Web Application in App personali, verrà eseguito il reindirizzamento all'URL di accesso di FortiWeb Web Application. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato FortiWeb Web Application Firewall, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).