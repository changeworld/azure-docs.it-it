---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform Identity Authentication | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Cloud Platform Identity Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: d2a7b27c7678f604c7f09febac67bf0879e34c3a
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724188"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SAP Cloud Platform Identity Authentication

Questa esercitazione descrive come integrare SAP Cloud Platform Identity Authentication con Azure Active Directory (Azure AD). Integrando SAP Cloud Platform Identity Authentication con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SAP Cloud Platform Identity Authentication.
* Abilitare gli utenti per l'accesso automatico a SAP Cloud Platform Identity Authentication con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SAP Cloud Platform Identity Authentication abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Cloud Platform Identity Authentication supporta l'accesso SSO avviato da **SP** e **IDP**

Prima di approfondire i dettagli tecnici, è fondamentale comprendere i concetti che si desidera esaminare. SAP Cloud Platform Identity Authentication e Active Directory Federation Services consentono di implementare l'accesso SSO per applicazioni o servizi protetti da Azure AD (come IdP) con applicazioni e servizi SAP protetti da SAP Cloud Platform Identity Authentication.

Attualmente SAP Cloud Platform Identity Authentication funge da provider di identità proxy per applicazioni SAP. Azure Active Directory a sua volta agisce come provider di identità iniziale in questa configurazione. 

Il diagramma seguente illustra questa relazione:

![Creazione di un utente di test di Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Con questa configurazione, il tenant di SAP Cloud Platform Identity Authentication viene configurato come applicazione attendibile in Azure Active Directory.

Tutte le applicazioni e i servizi SAP che si desidera proteggere in questo modo vengono configurati successivamente nella console di gestione di SAP Cloud Platform Identity Authentication.

L'autorizzazione per la concessione dell'accesso a servizi e applicazioni SAP deve pertanto avvenire in SAP Cloud Platform Identity Authentication (diversamente dalla configurazione dell'autorizzazione in Azure Active Directory).

Tramite la configurazione di SAP Cloud Platform Identity Authentication come un'applicazione tramite Marketplace di Azure Active Directory, non è necessario configurare singole attestazioni o asserzioni SAML.

> [!NOTE]
> Attualmente solo Web SSO è stato testato da entrambe le parti. I flussi necessari per la comunicazione da app ad API o da API ad API dovrebbero funzionare ma non sono ancora stati testati. I test verranno eseguiti durante le attività successive.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Aggiunta di SAP Cloud Platform Identity Authentication dalla raccolta

Per configurare l'integrazione di SAP Cloud Platform Identity Authentication in Azure AD, è necessario aggiungere SAP Cloud Platform Identity Authentication dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAP Cloud Platform Identity Authentication** nella casella di ricerca.
1. Selezionare **SAP Cloud Platform Identity Authentication** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform-identity-authentication"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per SAP Cloud Platform Identity Authentication

Configurare e testare l'accesso SSO di Azure AD con SAP Cloud Platform Identity Authentication usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Cloud Platform Identity Authentication.

Per configurare e testare l'accesso SSO di Azure AD con SAP Cloud Platform Identity Authentication, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di SAP Cloud Platform Identity Authentication](#create-sap-cloud-platform-identity-authentication-test-user)**: per avere una controparte di B.Simon in SAP Cloud Platform Identity Authentication collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SAP Cloud Platform Identity Authentication** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Contattare il [team di supporto di SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) per ottenere questi valori. Se non si conosce il valore dell'identificatore, seguire la documentazione di SAP Cloud Platform Identity Authentication in [Tenant SAML 2.0 Configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) (Configurazione del tenant SAML 2.0).

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud Platform Identity Authentication](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Usare l'URL del Sign-on dell'applicazione aziendale specifica. Contattare il [team di supporto di SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) in caso di dubbi.

1. L'applicazione SAP Cloud Platform Identity Authentication prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre a quelli elencati in precedenza, l'applicazione SAP Cloud Platform Identity Authentication prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **file XML dei metadati** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

9. Nella sezione **Set up SAP Cloud Platform Identity Authentication** (Configura SAP Cloud Platform Identity Authentication) copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP Cloud Platform Identity Authentication.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAP Cloud Platform Identity Authentication**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".

1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Configurare l'accesso Single Sign-On di SAP Cloud Platform Identity Authentication

1. Per automatizzare la configurazione all'interno di SAP Cloud Platform Identity Authentication, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura SAP Cloud Platform Identity Authentication** per passare direttamente all'applicazione SAP Cloud Platform Identity Authentication. Specificare quindi le credenziali di amministratore per accedere a SAP Cloud Platform Identity Authentication. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare SAP Cloud Platform Identity Authentication manualmente, in un'altra finestra del Web browser passare alla console di amministrazione di SAP Cloud Platform Identity Authentication. L'URL ha il formato seguente: `https://<tenant-id>.accounts.ondemand.com/admin`. Leggere quindi la documentazione relativa a SAP Cloud Platform Identity Authentication disponibile nella pagina [Integration with Microsoft Azure AD](https://developers.sap.com/tutorials/cp-ias-azure-ad.html) (Integrazione con Microsoft Azure AD).

2. Nel portale di Azure selezionare il pulsante **Salva**.

3. Continuare la procedura seguente solo se si vuole aggiungere e abilitare l'accesso SSO per un'altra applicazione SAP. Ripetere la procedura descritta nella sezione **Aggiunta di SAP Cloud Platform Identity Authentication dalla raccolta**.

4. Nel portale di Azure, nella pagina di integrazione di **SAP Cloud Platform Identity Authentication** selezionare **Accesso collegato**.

    ![Configurare l'accesso collegato](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Salvare la configurazione.

> [!NOTE]
> La nuova applicazione sfrutterà la configurazione del processo SSO dell'applicazione SAP precedente. Assicurarsi di usare gli stessi provider di identità aziendale nella console di amministrazione di SAP Cloud Platform Identity Authentication.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Creare l'utente di test di SAP Cloud Platform Identity Authentication

Non è necessario creare un utente in SAP Cloud Platform Identity Authentication. Gli utenti presenti nell'archivio utenti di Azure AD possono usare la funzionalità di accesso SSO.

SAP Cloud Platform Identity Authentication supporta l'opzione di federazione delle identità. Questa opzione consente all'applicazione di controllare se gli utenti autenticati dal provider di identità aziendale sono presenti nell'archivio utenti di SAP Cloud Platform Identity Authentication.

Per impostazione predefinita l'opzione di federazione delle identità è disabilitata. Se la federazione delle identità è abilitata, solo gli utenti che vengono importati in SAP Cloud Platform Identity Authentication sono in grado di accedere all'applicazione.

Per altre informazioni su come abilitare o disabilitare la federazione delle identità con SAP Cloud Platform Identity Authentication, vedere Enable Identity Federation with SAP Cloud Platform Identity Authentication (Abilitare la federazione delle identità con SAP Cloud Platform Identity Authentication) in [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html) (Configurare la federazione delle identità con l'archivio utenti di SAP Cloud Platform Identity Authentication).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di SAP Cloud Platform Identity Authentication, in cui è possibile avviare il flusso di accesso.

* Passare direttamente all'URL di accesso di SAP Cloud Platform Identity Authentication e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di SAP Cloud Platform Identity Authentication per cui si è configurato l'accesso SSO

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di SAP Cloud Platform Identity Authentication in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di SAP Cloud Platform Identity Authentication per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato SAP Cloud Platform Identity Authentication, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)