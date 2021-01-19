---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e piattaforma SAP Cloud.
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
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964049"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform

Questa esercitazione descrive come integrare SAP Cloud Platform con Azure Active Directory (Azure AD). Integrando SAP Cloud Platform con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SAP Cloud Platform.
* Abilitare gli utenti per l'accesso automatico a SAP Cloud Platform con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Cloud Platform, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SAP Cloud Platform abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SAP Cloud Platform saranno in grado di eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

>[!IMPORTANT]
>È necessario distribuire l'applicazione o la sottoscrizione a un'applicazione per l'account di SAP Cloud Platform per testare l'accesso Single Sign-On. In questa esercitazione, viene distribuita un'applicazione nell'account.
> 

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Cloud Platform supporta l'accesso SSO avviato da **provider di servizi**

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Aggiunta di SAP Cloud Platform dalla raccolta

Per configurare l'integrazione di SAP Cloud Platform in Azure AD, è necessario aggiungere SAP Cloud Platform dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAP Cloud Platform** nella casella di ricerca.
1. Selezionare **SAP Cloud Platform** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Configurare e testare l'accesso SSO di Azure AD per SAP Cloud Platform

Configurare e testare l'accesso SSO di Azure AD con SAP Cloud Platform usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Cloud Platform.

Per configurare e testare l'accesso SSO di Azure AD con SAP Cloud Platform, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di SAP Cloud Platform](#configure-sap-cloud-platform-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creazione di un utente di test di SAP Cloud Platform](#create-sap-cloud-platform-test-user)** : per avere una controparte di Britta Simon in SAP Cloud Platform collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SAP Cloud Platform** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud Platform](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL utilizzato dagli utenti per accedere all'applicazione **SAP Cloud Platform**. Questo è l'URL specifico dell'account di una risorsa protetta dell'applicazione SAP Cloud Platform. L'URL è basato sul formato seguente: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Questo è l'URL dell'applicazione SAP Cloud Platform che richiede l'autenticazione dell'utente.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. Nella casella di testo **Identifier** si fornirà il tipo di SAP Cloud Platform con un URL nel seguente formato: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Contattare il [team di supporto dei client di SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) per ottenere l'URL di accesso e l'identificatore. URL di risposta che è possibile ottenere dalla sezione di gestione trust, illustrata più avanti nell'esercitazione.
    > 
4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP Cloud Platform.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni, selezionare **SAP Cloud Platform**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sap-cloud-platform-sso"></a>Configurare l'accesso Single Sign-On di SAP Cloud Platform

1. In un'altra finestra del Web browser accedere al pannello di controllo di SAP Cloud Platform all'indirizzo `https://account.<landscape host>.ondemand.com/cockpit` (ad esempio https://account.hanatrial.ondemand.com/cockpit).

2. Scegliere la scheda **Trust** .
   
    ![Trust](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Trust")

3. Nella sezione Trust Management, in **Local Service Provider**, eseguire la procedura seguente:

    ![Screenshot che mostra la sezione "Trust Management" con la scheda "Local Service Provider" selezionata e tutte le caselle di testo evidenziate.](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Gestione relazione di trust")
   
    a. Fare clic su **Modifica**.

    b. In **Configuration Type** selezionare **Custom**.

    c. In **Local Provider Name** lasciare il valore predefinito. Copiare questo valore e incollarlo nel campo **Identifier** campo nella configurazione di Azure Active Directory per SAP Cloud Platform.

    d. Per generare una **chiave per la firma** e una coppia di chiavi del **certificato di firma**, fare clic su **Generate Key Pair**.

    e. In **Propagazione entità** selezionare **Disabilitato**.

    f. In **Force Authentication** selezionare **Disabled**.

    g. Fare clic su **Salva**.

4. Dopo il salvataggio delle impostazioni **Local Service Provider**, eseguire le operazioni seguenti per ottenere l'URL di risposta:
   
    ![Ottenere metadati](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Get Metadata")

    a. Scaricare il file di metadati di SAP Cloud Platform facendo clic su **Get Metadata**.

    b. Aprire il file di metadati XML SAP Cloud piattaforma scaricato e quindi individuare il tag **ns3:AssertionConsumerService** .
 
    c. Copiare il valore dell'attributo **Percorso** e incollarlo nel campo **URL di risposta** nella configurazione di Azure Active Directory per SAP Cloud Platform.

5. Fare clic sulla scheda **Trusted Identity Provider**, quindi fare clic su **Add Trusted Identity Provider**.
   
    ![Screenshot che mostra la pagina "Trust Management" con la scheda "Trusted Identity Provider" selezionata.](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Gestione relazione di trust")
   
    >[!NOTE]
    >Per gestire l'elenco dei provider di identità attendibili, è necessario aver scelto il tipo di configurazione Personalizza nella sezione Provider di servizi locale. Per il tipo di configurazione predefinito è presente una relazione di trust implicita e non modificabile per il servizio SAP ID. Per Nessuno, non sono disponibili impostazioni di trust.
    > 
    > 

6. Fare clic sulla scheda **General**, quindi fare clic su **Browse** per caricare il file di metadati scaricati.
    
    ![Gestione della relazione di trust](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Gestione relazione di trust")
    
    >[!NOTE]
    >Dopo aver caricato il file di metadati, i valori per **URL Single Sign-On**, **URL disconnessione singola** e **Certificato di firma** vengono popolati automaticamente.
    > 
     
7. Fare clic sulla scheda **Attributes** .

8. Nella scheda **Attributes** eseguire la procedura seguente:
    
    ![Attributes (Attributi)](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Attributes") 

    a. Fare clic su **Add Assertion-Based Attribute** e quindi aggiungere gli attributi basati su asserzione seguenti:
       
    | Attributo di asserzione | Attributo di entità |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >La configurazione degli attributi dipende da come le applicazioni per SCP vengono sviluppate, vale a dire quali attributi sono previsti nella risposta SAML e con quale nome (attributo di entità) accedono a questo attributo nel codice.
     > 
    
    b. L'**attributo predefinito** presente nella schermata è solo a scopo illustrativo. Non è necessario per il funzionamento dello scenario.  
 
    c. I nomi e i valori per l'**attributo di entità** illustrati nella schermata dipendono dal modo in cui viene sviluppata l'applicazione. È possibile che l'applicazione richieda mapping diversi.

### <a name="assertion-based-groups"></a>Gruppi basati su asserzione

Come passaggio facoltativo, è possibile configurare gruppi basati su asserzione per il provider di identità Azure Active Directory.

L'uso dei gruppi in SAP Cloud Platform consente di assegnare dinamicamente uno o più utenti a uno o più ruoli nelle applicazioni SAP Cloud Platform, determinate dai valori degli attributi nell'asserzione SAML 2.0. 

Ad esempio, se l'asserzione contiene l'attributo "*contract=temporary*", si potrebbe volere che tutti gli utenti interessati siano aggiunti al gruppo"*TEMPORARY*". Il gruppo "*TEMPORARY*" può contenere uno o più ruoli da uno o più applicazioni distribuite nell'account di SAP Cloud Platform.
 
Usare i gruppi basati su asserzione quando si vogliono assegnare simultaneamente diversi utenti a uno o più ruoli delle applicazioni nell'account di SAP Cloud Platform. Per assegnare un singolo utente o un numero ridotto di utenti a ruoli specifici, è consigliabile di eseguirne l'assegnazione diretta nella scheda **Authorizations** (Autorizzazioni) del pannello di controllo di SAP Cloud Platform.

### <a name="create-sap-cloud-platform-test-user"></a>Creare un utente di test di SAP Cloud Platform

Per consentire agli utenti di Azure AD di accedere a SAP Cloud Platform è necessario assegnare loro i ruoli in SAP Cloud Platform.

**Per assegnare un ruolo a un utente, seguire questa procedura:**

1. Accedere al pannello di controllo di **SAP Cloud Platform** .

2. Eseguire questi passaggi:
   
    ![Autorizzazioni](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Authorizations")
   
    a. Fare clic su **Authorization**.

    b. Fare clic sulla scheda **Utenti** .

    c. Nella casella di testo **User** digitare l'indirizzo di posta elettronica dell'utente.

    d. Fare clic su **Assign** per assegnare l'utente a un ruolo.

    e. Fare clic su **Salva**.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di SAP Cloud Platform, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di SAP Cloud Platform e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di SAP Cloud Platform in App personali, si dovrebbe accedere automaticamente all'istanza di SAP Cloud Platform per cui si è configurato l'accesso Single Sign-On. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato SAP Cloud Platform, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).