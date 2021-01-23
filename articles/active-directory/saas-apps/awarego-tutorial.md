---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con AwareGo | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e AwareGo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 4682396f68d6ff1af0b2fb6a5b1a8419d6963529
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735339"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Esercitazione: Integrazione dell'accesso Single Sign-On di Azure Active Directory con AwareGo

Questa esercitazione descrive come integrare AwareGo con Azure Active Directory (Azure AD). Integrando AwareGo con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad AwareGo.
* Abilitare gli utenti per l'accesso automatico ad AwareGo con gli account Azure AD personali.
* Gestire gli account in una posizione centrale, il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di AwareGo abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. AwareGo supporta l'accesso SSO avviato dal provider di servizi (SP).


## <a name="adding-awarego-from-the-gallery"></a>Aggiunta di AwareGo dalla raccolta

Per configurare l'integrazione di AwareGo in Azure AD, è necessario aggiungere AwareGo dalla raccolta all'elenco di app SaaS (Software as a service) gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory**.
1. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **AwareGo** nella casella di ricerca.
1. Selezionare **AwareGo** nel pannello dei risultati e quindi aggiungere l'app. Entro pochi secondi l'app viene aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per AwareGo

Configurare e testare l'accesso SSO di Azure AD con AwareGo by usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AwareGo.

Per configurare e testare l'accesso SSO di Azure AD con AwareGo, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.  

    a. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.  
    b. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per consentire all'utente B.Simon di usare l'accesso Single Sign-On di Azure AD.  

1. **[Configurare l'accesso Single Sign-On di AwareGo](#configure-awarego-sso)** per configurare le impostazioni di Single Sign-On sul lato applicazione.

    a. **[Creare un utente di test di AwareGo](#create-an-awarego-test-user)** per avere una controparte di B.Simon in AwareGo collegata alla rappresentazione dell'utente in Azure AD.  
    b. **[Testare l'accesso SSO](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **AwareGo** nel portale di Azure selezionare **Single Sign-On** nella sezione **Gestione**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Per modificare le impostazioni, nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare il pulsante **Modifica**.

   ![Screenshot del pulsante Modifica per Configurazione SAML di base.](common/edit-urls.png)

1. Nel riquadro di modifica seguire questa procedura in **Configurazione SAML di base**:

    a. Nella casella **URL di accesso** immettere uno degli URL seguenti:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. Nella casella di testo **Identificatore (ID entità)** immettere un URL nel formato seguente: `https://<SUBDOMAIN>.awarego.com`

    c. Nella casella **URL di risposta** immettere un URL nel formato seguente: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > I valori precedenti non sono valori reali. Aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere i valori, contattare il [team del supporto clienti di AwareGo](mailto:support@awarego.com). È anche possibile fare riferimento agli esempi nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** accanto a **Certificato (Base64)** selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Screenshot del collegamento "Scarica" del certificato nel riquadro Certificato di firma SAML.](common/certificatebase64.png)

1. Nella sezione **Configura AwareGo** copiare uno o più URL in base alle esigenze.

    ![Screenshot del riquadro "Configura AwareGo" per la copia degli URL di configurazione.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** e quindi **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. Nel riquadro delle proprietà **Utente** seguire questa procedura:

   a. Nella casella **Nome** immettere **B.Simon**.  
   b. Nella casella **Nome utente** immettere il nome utente nel formato seguente: `<username>@<companydomain>.<extension>`, ad esempio B.Simon@contoso.com.  
   c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** per usarlo in un secondo momento.  
   d. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita l'utente B.Simon all'uso dell'accesso SSO di Azure concedendole l'accesso ad AwareGo.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco **Applicazioni** selezionare **AwareGo**.
1. Nella sezione **Gestione** della pagina di panoramica dell'app selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.
1. Nel riquadro **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti** e quindi fare clic su **Seleziona**.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è *Accesso predefinito*.
1. Nel riquadro **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

## <a name="configure-awarego-sso"></a>Configurare l'accesso Single Sign-On di AwareGo

Per configurare l'accesso Single Sign-On sul lato **AwareGo**, inviare il **certificato (Base64)** scaricato in precedenza e gli URL copiati dal portale di Azure al [team di supporto di AwareGo](mailto:support@awarego.com). Il team di supporto crea questa impostazione in modo che la connessione SSO SAML venga stabilita correttamente su entrambi i lati.

### <a name="create-an-awarego-test-user"></a>Creare un utente di test di AwareGo

In questa sezione viene creato un utente di nome Britta Simon in AwareGo. Collaborare con il [team di supporto di AwareGo](mailto:support@awarego.com) per aggiungere gli utenti alla piattaforma AwareGo. È necessario creare e attivare gli utenti prima di poter usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione è possibile testare la configurazione dell'accesso Single Sign-On di Azure AD eseguendo una delle operazioni seguenti: 

* Nel portale di Azure selezionare **Test this application** (Testa questa applicazione). Verrà eseguito il reindirizzamento alla pagina di accesso di AwareGo, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente alla pagina di accesso di AwareGo e avviare il flusso di accesso.

* Passare ad App personali Microsoft. Quando si seleziona il riquadro di **AwareGo** in App personali, verrà eseguito il reindirizzamento alla pagina di accesso di AwareGo. Per altre informazioni, vedere [Accedere e avviare app dal portale App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato AwareGo, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione del Controllo app per l'accesso condizionale. Per altre informazioni, vedere [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).