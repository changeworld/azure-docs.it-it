---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con New Relic | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e New Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 8c0ffe4affb6b30f2e2a1aa97a0f4795c130f59b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517607"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con New Relic

Questa esercitazione descrive come integrare New Relic con Azure Active Directory (Azure AD). Integrando New Relic con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a New Relic.
* Abilitare gli utenti per l'accesso automatico a New Relic con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di New Relic abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* New Relic supporta l'accesso SSO avviato dal provider di servizi o dal provider di identità.

## <a name="add-new-relic-from-the-gallery"></a>Aggiungere New Relic dalla raccolta

Per configurare l'integrazione di New Relic in Azure AD, è necessario aggiungere **New Relic (By Organization)** dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Seleziona il servizio **Azure Active Directory**.
1. Selezionare **Applicazioni aziendali** > **Nuova applicazione**.
1. Nella pagina **Esplora la raccolta di Azure AD** digitare **New Relic (By Organization)** nella casella di ricerca.
1. Selezionare **New Relic (By Organization)** nei risultati e quindi selezionare **Crea**. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per New Relic

Configurare e testare l'accesso SSO di Azure AD con New Relic usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione collegata tra un utente di Azure AD e l'utente correlato in New Relic.

Per configurare e testare l'accesso SSO di Azure AD con New Relic:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.
   1. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
   1. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On di New Relic](#configure-new-relic-sso): per configurare le impostazioni di Single Sign-On sul lato dell'applicazione New Relic.
   1. [Creare un utente di test di New Relic](#create-a-new-relic-test-user): per avere una controparte di B.Simon in New Relic collegata all'utente in Azure AD.
1. [Testare l'accesso Single Sign-On](#test-sso): per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina portale di Azure di integrazione **dell'applicazione New Relic per** organizzazione individuare la **sezione** Gestione. Selezionare quindi **Single Sign-On**.

1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot della pagina Configura l'accesso Single Sign-On con SAML, in cui è evidenziata l'icona della matita.](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi **Identificatore** e **URL di risposta**.

   * Per recuperare questi valori, usare l'applicazione **My Organization** (La mia organizzazione) di New Relic. Per usare questa applicazione:
      1. [Eseguire l'accesso](https://login.newrelic.com/) a New Relic.
      1. Nel menu in alto selezionare **Apps** (App).
      1. Nella sezione **Your apps** (App) selezionare **My Organization** > **Authentication domains** (La mia organizzazione > Domini di autenticazione).
      1. Se sono disponibili più domini di autenticazione, scegliere quello a cui si vuole connettere l'accesso SSO di Azure AD. La maggior parte delle società ha un unico dominio di autenticazione denominato **Default** (Predefinito). Se è presente un solo dominio di autenticazione, non è necessario effettuare alcuna selezione.
      1. Nella sezione **Authentication** (Autenticazione) il campo **Assertion consumer URL** (URL del consumer di asserzione) contiene il valore da usare per **URL di risposta**.
      1. Nella sezione **Authentication** (Autenticazione) il campo **Our entity ID** (ID entità) contiene il valore da usare per **Identificatore**.

1. Nella sezione **Attributi utente e attestazioni** assicurarsi che **Identificatore univoco dell'utente** sia associato a un campo che contiene l'indirizzo di posta elettronica da usare con New Relic.

   * Il campo predefinito **user.userprincipalname** è quello corretto se i relativi valori sono uguali agli indirizzi di posta elettronica di New Relic.
   * Il campo **user.mail** è da preferirsi se **user.userprincipalname** non corrisponde all'indirizzo di posta elettronica di New Relic.

1. Nella sezione **Certificato di firma SAML** copiare il valore di **URL dei metadati di federazione dell'app** e salvarlo per usarlo in seguito.

1. Nella sezione **Configura New Relic by Organization** copiare il valore di **URL di accesso** e salvarlo per usarlo in seguito.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso New Relic.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **New Relic**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-new-relic-sso"></a>Configurare l'accesso Single Sign-On di New Relic

Seguire questa procedura per configurare l'accesso SSO per New Relic.

1. [Eseguire l'accesso](https://login.newrelic.com/) a New Relic.

1. Nel menu in alto selezionare **Apps** (App).

1. Nella sezione **Your apps** (App) selezionare **My Organization** > **Authentication domains** (La mia organizzazione > Domini di autenticazione).

1. Se sono disponibili più domini di autenticazione, scegliere quello a cui si vuole connettere l'accesso SSO di Azure AD. La maggior parte delle società ha un unico dominio di autenticazione denominato **Default** (Predefinito). Se è presente un solo dominio di autenticazione, non è necessario effettuare alcuna selezione.

1. Nella sezione **Authentication** (Autenticazione) selezionare **Configure** (Configura).

   1. In **Source of SAML metadata** (Origine dei metadati SAML) immettere il valore salvato in precedenza dal campo **URL dei metadati di federazione dell'app** di Azure AD.

   1. In **SSO target URL** (URL di destinazione SSO) immettere il valore salvato in precedenza dal campo **URL di accesso** di Azure AD.

   1. Dopo aver verificato che le impostazioni siano corrette sia in Azure AD che in New Relic, selezionare **Save** (Salva). Se non si configurano correttamente entrambe le applicazioni, gli utenti non riusciranno ad accedere a New Relic.

### <a name="create-a-new-relic-test-user"></a>Creare un utente di test di New Relic

In questa sezione viene creato un utente di nome B.Simon in New Relic.

1. [Eseguire l'accesso](https://login.newrelic.com/) a New Relic.

1. Nel menu in alto selezionare **Apps** (App).

1. Nella sezione **Your apps** (App) selezionare **User Management** (Gestione utenti).

1. Selezionare **Aggiungi utente**.

   1. Per **Name** (Nome) immettere **B.Simon**.
   
   1. Per **Email** (Indirizzo di posta elettronica) immettere il valore che verrà inviato da SSO di Azure AD.
   
   1. Scegliere un **tipo** e un **gruppo** per l'utente. Per un utente di test i valori corretti sono **Basic User** (Utente Basic) per il tipo e **User** (Utente) per il gruppo.
   
   1. Per salvare l'utente, selezionare **Add User** (Aggiungi utente).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà reindirizzato all'URL New Relic di accesso in cui è possibile avviare il flusso di accesso.  

* Passare direttamente New Relic'URL di accesso e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic **su Test this application** in portale di Azure per accedere automaticamente all'New Relic per cui si è configurato l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro New Relic nel App personali, se configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al New Relic per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato New Relic è possibile applicare il controllo sessione, che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
