---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Workplace by Facebook | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workplace by Facebook.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 3f66da38d3303b47c2a9b6cefeee19af6bf64ec1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725508"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Workplace by Facebook

Questa esercitazione descrive come integrare Workplace by Facebook con Azure Active Directory (Azure AD). Integrando Workplace by Facebook con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a Workplace by Facebook.
* Abilitare gli utenti per l'accesso automatico a Workplace by Facebook con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Workplace by Facebook abilitata per l'accesso Single Sign-On (SSO).

> [!NOTE]
> Facebook offre due prodotti, Workplace Standard (gratuito) e Workplace Premium (a pagamento). Qualsiasi tenant di Workplace Premium può configurare l'integrazione SCIM e SSO senza alcun costo né alcuna necessità di una licenza. SSO e SCIM non sono disponibili nelle istanze di Workplace Standard.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Workplace by Facebook supporta l'accesso SSO avviato da **provider di servizi**
* Workplace by Facebook supporta il provisioning **JIT**
* Workplace by Facebook supporta il **[provisioning utenti automatico](workplacebyfacebook-provisioning-tutorial.md)**
* È ora possibile configurare l'applicazione per dispositivi mobili Workplace by Facebook con Azure AD per abilitare l'accesso SSO. In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.


## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Aggiunta di Workplace by Facebook dalla raccolta

Per configurare l'integrazione di Workplace by Facebook in Azure AD, è necessario aggiungere Workplace by Facebook dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Workplace by Facebook** nella casella di ricerca.
1. Selezionare **Workplace by Facebook** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Workplace by Facebook

Configurare e testare l'accesso SSO di Azure AD con Workplace by Facebook usando un utente di test di nome **B.Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Workplace by Facebook.

Per configurare e testare l'accesso SSO di Azure AD con Workplace by Facebook, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso SSO di Workplace by Facebook](#configure-workplace-by-facebook-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di Workplace by Facebook](#create-workplace-by-facebook-test-user)**: per avere una controparte di B.Simon in Workplace by Facebook collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Workplace by Facebook** nel portale di Azure trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** (disponibile in WorkPlace come Recipient URL) digitare un URL nel formato seguente: `https://.workplace.com/work/saml.php`

    b. Nella casella di testo **Identificatore (ID entità)** (disponibile in WorkPlace come Audience URL) digitare un URL nel formato seguente: `https://www.workplace.com/company/`

    c. Nella casella di testo **URL di risposta** (disponibile in WorkPlace come Assertion Consumer Service) digitare un URL nel formato seguente: `https://.workplace.com/work/saml.php`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per i valori corretti per la community Workplace, vedere la pagina di autenticazione del dashboard aziendale di Workplace, descritta più avanti nell'esercitazione.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Workplace by Facebook** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Workplace by Facebook.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Workplace by Facebook**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-workplace-by-facebook-sso"></a>Configurare l'accesso SSO di Workplace by Facebook

1. Per automatizzare la configurazione all'interno di Workplace by Facebook, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Workplace by Facebook** per passare direttamente all'applicazione Workplace by Facebook. Nell'applicazione specificare le credenziali di amministratore per accedere a Workplace by Facebook. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 5.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente Workplace by Facebook, aprire una nuova finestra del Web browser, accedere al sito aziendale di Workplace by Facebook come amministratore e seguire questa procedura:

    > [!NOTE]
    > Come parte del processo di autenticazione SAML, Workplace può usare stringhe di query di dimensioni massime di 2,5 kilobyte per passare i parametri ad Azure AD.

1. Nel pannello di spostamento sinistro passare alla scheda **Security** (Sicurezza)  > **Authentication** (Autenticazione).

    ![Admin Panel](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Selezionare l'opzione **Single-sign on (SSO)** (Accesso SSO).
    
    b. Fare clic su **+Add new SSO Provider** (+ Aggiungi nuovo provider SSO).
    > [!NOTE]
    > Assicurarsi di selezionare anche la casella di controllo Password login (Accesso tramite password). Gli amministratori potrebbero scegliere di usare questa opzione per l'accesso durante la distribuzione dei certificati, in modo da impedirne il blocco.

1. Nella scheda **Authentication** (Autenticazione) selezionare **Single-Sign On (SSO)** e seguire questa procedura:

    ![Scheda Authentication](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. In **Name of the SSO Provider** (Nome del provider SSO) immettere il nome dell'istanza di SSO, ad esempio Azureadsso.

    b. Nella casella di testo **SAML URL** (URL SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **SAML Issuer URL** (URL autorità di certificazione SAML) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Aprire nel Blocco note il **certificato con codifica Base 64** scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **SAML Certificate** (Certificato SAML).

    e. Copiare il valore **Audience URL** (URL destinatario) per l'istanza e incollarlo nella casella di testo **Identificatore (ID entità)** nella sezione **Configurazione SAML di base** del portale di Azure.

    f. Copiare il valore di **Recipient URL** (URL destinatario) per l'istanza e incollarlo nella casella di testo **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    g. Copiare il valore di **ACS (Assertion Consumer Service) URL** (URL del servizio consumer di asserzione) dell'istanza corrente e incollarlo nella casella di testo **URL di risposta** della sezione **Configurazione SAML di base** del portale di Azure.

    h. Scorrere fino alla fine della sezione e fare clic sul pulsante **Test SSO** (Testa SSO). Si ottiene una finestra popup visualizzata con la pagina di accesso di Azure AD. Immettere le credenziali come di consueto per l'autenticazione.

    **Risoluzione dei problemi:** assicurarsi che l'indirizzo e-mail restituito da Azure AD sia lo stesso dell'account aziendale con cui si è connessi.

    i. Dopo il completamento del test, scorrere fino alla fine della pagina e fare clic sul pulsante **Save** (Salva).

    j. Tutti gli utenti che usano Workplace visualizzeranno ora una pagina di accesso di Azure AD per l'autenticazione.

1. **Reindirizzamento disconnessione SAML (facoltativo)**  -

    È possibile scegliere di configurare facoltativamente un URL di disconnessione SAML, che può essere usato per puntare alla pagina di disconnessione di Azure AD. Quando questa impostazione è abilitata e configurata, l'utente non sarà più indirizzato alla pagina di disconnessione di Workplace. Al contrario, l'utente verrà reindirizzato all'URL aggiunto nelle impostazioni di reindirizzamento di disconnessione SAML.

### <a name="configuring-reauthentication-frequency"></a>Configurazione della frequenza di riautenticazione

È possibile configurare Workplace per la richiesta di una verifica SAML ogni giorno, ogni 3 giorni, ogni settimana, ogni 2 settimane, ogni mese o mai.

> [!NOTE]
> Il valore minimo per la verifica SAML nelle applicazioni per dispositivi mobili è impostato su una settimana.

È anche possibile forzare una reimpostazione SAML per tutti gli utenti che usano il pulsante: Require SAML authentication for all users now (Richiedi ora l'autenticazione SAML a tutti gli utenti).

### <a name="create-workplace-by-facebook-test-user"></a>Creare un utente di test di Workplace by Facebook

In questa sezione si crea un utente di nome B.Simon in Workplace by Facebook. Workplace by Facebook supporta il provisioning JIT, abilitato per impostazione predefinita.

Non è necessaria alcuna azione dell'utente in questa sezione. Se un utente non esiste in Workplace by Facebook, si crea una nuova istanza quando si tenta di accedere a Workplace by Facebook.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto clienti di Workplace by Facebook](https://www.workplace.com/help/work/).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Workplace by Facebook, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Workplace by Facebook e avviare il flusso di accesso.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Workplace by Facebook in App personali, verrà eseguito il reindirizzamento all'URL di accesso di Workplace by Facebook. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Testare l'accesso SSO per Workplace by Facebook (per dispositivi mobili)

1. Aprire l'applicazione per dispositivi mobili Workplace by Facebook. Nella pagina di accesso fare clic su **LOG IN** (ACCEDI).

    ![Accesso](./media/workplacebyfacebook-tutorial/test05.png)

2. Immettere l'indirizzo di posta elettronica aziendale e fare clic su **CONTINUE** (CONTINUA).

    ![Indirizzo di posta elettronica](./media/workplacebyfacebook-tutorial/test02.png)

3. Fare clic su **JUST ONCE** (UNA SOLA VOLTA).

    ![Una sola volta](./media/workplacebyfacebook-tutorial/test04.png)

4. Fare clic su **Consenti**.

    ![Consentire](./media/workplacebyfacebook-tutorial/test03.png)

5. Al termine dell'accesso verrà infine visualizzata la home page dell'applicazione.    

    ![Home page](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Workplace by Facebook, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)