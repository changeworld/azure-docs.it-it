---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Timeclock 365 SAML | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Timeclock 365 SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: d0c8364cc85cfce900021272d17456527919122b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99050805"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeclock-365-saml"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Timeclock 365 SAML

Questa esercitazione descrive come integrare Timeclock 365 SAML con Azure Active Directory (Azure AD). Integrando Timeclock 365 SAML con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Timeclock 365 SAML.
* Abilitare gli utenti per l'accesso automatico a Timeclock 365 SAML con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Timeclock 365 SAML abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Timeclock 365 SAML supporta l'accesso SSO avviato da **SP**

## <a name="adding-timeclock-365-saml-from-the-gallery"></a>Aggiunta di Timeclock 365 SAML dalla raccolta

Per configurare l'integrazione di Timeclock 365 SAML in Azure AD, è necessario aggiungere Timeclock 365 SAML dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Timeclock 365 SAML** nella casella di ricerca.
1. Selezionare **Timeclock 365 SAML** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-timeclock-365-saml"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Timeclock 365 SAML

Configurare e testare l'accesso SSO di Azure AD con Timeclock 365 SAML usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Timeclock 365 SAML.

Per configurare e testare l'accesso SSO di Azure AD con Timeclock 365 SAML, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Timeclock 365 SAML](#configure-timeclock-365-saml-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Timeclock 365 SAML](#create-timeclock-365-saml-test-user)** : per avere una controparte di B.Simon in Timeclock 365 SAML collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Timeclock 365 SAML** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://live.timeclock365.com/login`


1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Timeclock 365 SAML.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Timeclock 365 SAML**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-timeclock-365-saml-sso"></a>Configurare l'accesso Single Sign-On di Timeclock 365 SAML

1. Per automatizzare la configurazione all'interno di Timeclock 365 SAML, è necessario installare l'estensione del browser per l' **accesso sicuro alle app** facendo clic su **installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo l'aggiunta dell'estensione al browser, fare clic su **Configura Timeclock 365 SAML** per indirizzare l'applicazione saml Timeclock 365. Da qui, fornire le credenziali di amministratore per accedere a Timeclock 365 SAML. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 4.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare Timeclock 365 SAML manualmente, in un'altra finestra del Web browser accedere al sito aziendale di Timeclock 365 SAML come amministratore.

1. Eseguire i passaggi indicati di seguito.

    ![Configurazione di Timeclock](./media/timeclock-365-saml-tutorial/saml-configuration.png)

    a. Passare alla scheda **Settings > Company profile > Settings** (Impostazioni > Profilo aziendale > Impostazioni).

    b. In **IDP metadata path** (Percorso metadati IDP) incollare il valore di **URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    c. Fare quindi clic su **Update** (Aggiorna).

### <a name="create-timeclock-365-saml-test-user"></a>Creare l'utente di test di Timeclock 365 SAML

1. Aprire una nuova scheda nel browser e accedere al sito aziendale di Timeclock 365 SAML come amministratore.

1. Passare a **Users > Add new user** (Utenti > Aggiungi nuovo utente).

    ![Creare l'utente di test 1 ](./media/timeclock-365-saml-tutorial/add-user-1.png)

1. Specificare tutte le informazioni necessarie nella pagina **User information** (Informazioni utente) e fare clic su **Save** (Salva).

    ![Creare l'utente di test 2 ](./media/timeclock-365-saml-tutorial/add-user-2.png)

1. Fare clic sul pulsante **Create** (Crea) per creare l'utente di test.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Timeclock 365 SAML, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Timeclock 365 SAML e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Timeclock 365 SAML in App personali, verrà eseguito il reindirizzamento all'URL di accesso di Timeclock 365 SAML. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Timeclock 365 SAML, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
