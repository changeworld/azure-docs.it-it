---
title: 'Esercitazione: Integrazione di Azure Active Directory con ArcGIS Online | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ArcGIS Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 7a445eefa31e741562105e89fa105d404ccc0c7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646351"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Esercitazione: Integrazione di Azure Active Directory con ArcGIS Online

In questa esercitazione si apprenderà come integrare ArcGIS Online con Azure Active Directory (Azure AD). Quando si integra ArcGIS Online con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a ArcGIS Online.
* Abilitare gli utenti per l'accesso automatico a ArcGIS Online con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ArcGIS Online Single Sign-On (SSO) abilitata.

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ArcGIS Online supporta SSO avviato da **SP** .

## <a name="add-arcgis-online-from-the-gallery"></a>Aggiungere ArcGIS Online dalla raccolta

Per configurare l'integrazione di ArcGIS Online in Azure AD, è necessario aggiungere ArcGIS Online dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ArcGIS Online** nella casella di ricerca.
1. Selezionare **ArcGIS Online** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-online"></a>Configurare e testare Azure AD SSO for ArcGIS Online

Configurare e testare Azure AD SSO con ArcGIS Online usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in ArcGIS Online.

Per configurare e testare Azure AD SSO con ArcGIS Online, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso SSO di ArcGIS Online](#configure-arcgis-online-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di ArcGIS Online](#create-arcgis-online-test-user)** : per avere una controparte di B. Simon in ArcGIS Online collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **ArcGIS Online** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.maps.arcgis.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ArcGIS Online](https://support.esri.com/en/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Per automatizzare la configurazione all'interno di **ArcGIS Online**, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Install the extension** (Installa estensione).

    ![image](./media/arcgis-tutorial/install-extension.png)

7. Dopo aver aggiunto l'estensione al browser, fare clic su **setup ArcGIS Online** (Configura ArcGIS Online) per passare direttamente all'applicazione ArcGIS Online. Da qui, fornire le credenziali di amministratore per accedere ad ArcGIS Online. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi nella sezione **Configurare il Single Sign-On di ArcGIS Online**.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a ArcGIS Online.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **ArcGIS Online**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-arcgis-online-sso"></a>Configurare l'accesso SSO di ArcGIS Online

1. Se si vuole configurare manualmente ArcGIS Online, aprire una nuova finestra del Web browser, accedere al sito aziendale di ArcGIS come amministratore e seguire questa procedura:

2. Fare clic su **EDIT SETTINGS** (Modifica impostazioni).

    ![Edit Settings](./media/arcgis-tutorial/settings.png "Edit Settings")

3. Fare clic su **Security**.

    ![Sicurezza](./media/arcgis-tutorial/secure.png "Security")

4. In **Enterprise Logins** (Accessi aziendali) fare clic su **SET IDENTITY PROVIDER** (Imposta provider di identità).

    ![Enterprise Logins](./media/arcgis-tutorial/enterprise.png "Enterprise Logins")

5. Nella pagina di configurazione **Set Identity Provider** seguire questa procedura:

    ![Set Identity Provider](./media/arcgis-tutorial/identity-provider.png "Set Identity Provider")

    a. Nella casella di testo **Name** (Nome) digitare il nome della propria organizzazione.

    b. In **I metadati per il provider di identità dell'organizzazione verranno forniti mediante**, selezionare **Un File**.

    c. Per caricare il file di metadati scaricato, fare clic su **Seleziona file**.

    d. Fare clic su **SET IDENTITY PROVIDER** (Imposta provider di identità).

### <a name="create-arcgis-online-test-user"></a>Creare un utente di test di ArcGIS Online

Per consentire agli utenti di Azure AD di accedere ad ArcGIS Online, è necessario effettuarne il provisioning in ArcGIS Online.  
Nel caso di ArcGIS Online, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **ArcGIS** .

2. Fare clic su **INVITE MEMBERS** (Invita membri).

    ![Invite Members](./media/arcgis-tutorial/invite.png "Invita Membri")

3. Selezionare **Add members automatically without sending an email** (Aggiungi membri automaticamente senza inviare un'e-mail) e quindi fare clic su **NEXT** (Avanti).

    ![Add Members Automatically](./media/arcgis-tutorial/members.png "Add Members Automatically")

4. Nella finestra di dialogo **Membri** seguire questa procedura:

    ![Add and review](./media/arcgis-tutorial/review.png "Aggiungi e verifica")

     a. Nelle caselle di testo **Email** (Indirizzo di posta elettronica), **First name** (Nome) e **Last name** (Cognome) digitare rispettivamente l'indirizzo di posta elettronica, il nome e il cognome relativi a un account Azure AD valido di cui si vuole effettuare il provisioning.

     b. Fare clic su **ADD AND REVIEW** (Aggiungi e verifica).
5. Verificare i dati immessi e quindi fare clic su **ADD MEMBERS** (Aggiungi membri).

    ![Add member](./media/arcgis-tutorial/add.png "Add member")

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso a ArcGIS Online in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso a ArcGIS Online e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro ArcGIS Online in app personali, questo verrà reindirizzato all'URL di accesso a ArcGIS Online. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato ArcGIS Online, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).