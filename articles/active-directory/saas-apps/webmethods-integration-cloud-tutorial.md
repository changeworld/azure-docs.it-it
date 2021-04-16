---
title: "Esercitazione: Azure Active Directory'integrazione con webMethods Integration Suite | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e webMethods Integration Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: c3eaac19ffbf3fd93311073ff69e28532b1c15e6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520055"
---
# <a name="tutorial-azure-active-directory-integration-with-webmethods-integration-suite"></a>Esercitazione: Azure Active Directory'integrazione con webMethods Integration Suite

Questa esercitazione illustra come integrare webMethods Integration Suite con Azure Active Directory (Azure AD). Integrando webMethods Integration Suite con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a webMethods Integration Suite.
* Abilitare gli utenti per l'accesso automatico a webMethods Integration Suite con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* webMethods Integration Suite sottoscrizione abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* webMethods Integration Suite supporta **l'accesso** SSO avviato da SP e **IDP.**

* webMethods Integration Suite supporta il provisioning **utenti JUST-In-Time.**

## <a name="add-webmethods-integration-suite-from-the-gallery"></a>Aggiungere webMethods Integration Suite dalla raccolta

Per configurare l'integrazione di webMethods Integration Suite in Azure AD, è necessario aggiungere webMethods Integration Suite dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta digitare** **webMethods Integration Suite** nella casella di ricerca.
1. Selezionare **webMethods Integration Suite** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-webmethods-integration-suite"></a>Configurare e testare Azure AD SSO per webMethods Integration Suite

Configurare e testare Azure AD SSO con webMethods Integration Suite usando un utente di test di **nome B.Simon**. Per il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in webMethods Integration Suite.

Per configurare e testare Azure AD SSO con webMethods Integration Suite, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare webMethods Integration Suite SSO:](#configure-webmethods-integration-suite-sso)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare webMethods Integration Suite utente di test:](#create-webmethods-integration-suite-test-user)** per avere una controparte di B.Simon in webMethods Integration Suite collegata alla Azure AD dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina portale di Azure di integrazione dell'applicazione **webMethods Integration Suite** trovare  la sezione Gestione e selezionare **Single Sign-On.**
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Per configurare **webMethods Integration Cloud**, nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | URL identificatore |
    |----------------------------------------------|
    | `<SUBDOMAIN>.webmethodscloud.com`|
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |
    |

    b. Nella casella di testo **URL di risposta** digitare un URL in uno dei formati seguenti:

    | URL di risposta |
    |----------------------------------------------|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoResponse`|
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoResponse`|
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoResponse`|
    |

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    d. Nella casella di testo **URL di accesso** digitare un URL in uno dei formati seguenti:

    | URL di accesso |
    |--------------------------------|
    |`https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoRequest`|
    |`https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoRequest`|
    |`https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoRequest`|
    |

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di webMethods Integration Suite](https://empower.softwareag.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Per configurare **webMethods API Cloud**, nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | URL identificatore |
    |----------------------------------------------|
    | `<SUBDOMAIN>.webmethodscloud.com`|
    |`<SUBDOMAIN>.webmethodscloud.eu`|
    | `<SUBDOMAIN>.webmethodscloud.de`|
    |

    b. Nella casella di testo **URL di risposta** digitare un URL in uno dei formati seguenti:

    | URL di risposta |
    |----------------------------------------------|
    | `https://<SUBDOMAIN>.webmethodscloud.com/umc/rest/saml/initsso`|
    | `https://<SUBDOMAIN>.webmethodscloud.eu/umc/rest/saml/initsso`|
    | `https://<SUBDOMAIN>.webmethodscloud.de/umc/rest/saml/initsso`|
    |

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    d. Nella casella di testo **URL di accesso** digitare un URL in uno dei formati seguenti:
    
    | URL di accesso |
    |--------------------------------|
    | `https://api.webmethodscloud.com/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    | `https://api.webmethodscloud.eu/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    | `https://api.webmethodscloud.de/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    |

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di webMethods Integration Suite](https://empower.softwareag.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura webMethods Integration Suite** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a webMethods Integration Suite.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **webMethods Integration Suite**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-webmethods-integration-suite-sso"></a>Configurare l'accesso SSO Integration Suite webMethods

Per configurare l'accesso Single Sign-On sul lato **webMethods Integration Suite**, è necessario inviare il file **XML dei metadati della federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di webMethods Integration Suite](https://empower.softwareag.com/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-webmethods-integration-suite-test-user"></a>Creare l'utente di test di webMethods Integration Suite

In questa sezione viene creato un utente di nome Britta Simon in webMethods Integration Suite. webMethods Integration Suite supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in webMethods Integration Suite, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà reindirizzato a webMethods Integration Suite URL di accesso da cui è possibile avviare il flusso di accesso.  

* Passare a webMethods Integration Suite'URL di accesso e avviare il flusso di accesso da questa pagina.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic **su Test this application** (Testa questa applicazione) in portale di Azure per accedere automaticamente all'applicazione webMethods Integration Suite per cui si è configurato l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Integration Suite webMethods nel App personali, se configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di webMethods Integration Suite per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato webMethods Integration Suite è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
