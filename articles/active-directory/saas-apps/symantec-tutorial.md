---
title: 'Esercitazione: Integrazione di Azure Active Directory con Symantec Web Security Service (WSS) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Symantec Web Security Service (WSS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: af7d126bfdc9ff8edf6b498747fab9c7f497a0f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484847"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Esercitazione: Integrazione di Azure Active Directory con Symantec Web Security Service (WSS)

Questa esercitazione descrive come integrare l'account Symantec Web Security Service con l'account Azure Active Directory (Azure AD) in modo che WSS possa autenticare un utente finale di cui è stato effettuato il provisioning in Azure AD usando l'autenticazione SAML e applicare regole dei criteri a livello di utente o gruppo.

L'integrazione di Symantec Web Security Service (WSS) con Azure AD offre i vantaggi seguenti:

- Gestire tutti gli utenti finali e i gruppi usati dall'account WSS dal portale di Azure AD.

- Consentire agli utenti finali di autenticarsi in WSS usando le credenziali di Azure AD.

- Abilitare l'applicazione delle regole dei criteri a livello di utente e gruppo definite nell'account WSS.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Symantec Web Security Service (WSS) abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Symantec Web Security Service (WSS) supporta l'accesso SSO avviato da **IDP.**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Aggiungere Symantec Web Security Service (WSS) dalla raccolta

Per configurare l'integrazione di Symantec Web Security Service (WSS) in Azure AD, è necessario aggiungere Symantec Web Security Service (WSS) dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Symantec Web Security Service (WSS)** nella casella di ricerca.
1. Selezionare **Symantec Web Security Service (WSS)** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>Configurare e testare Azure AD SSO per Symantec Web Security Service (WSS)

Configurare e testare Azure AD SSO con Symantec Web Security Service (WSS) usando un utente di test di **nome B.Simon**. Per il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Symantec Web Security Service (WSS).

Per configurare e testare Azure AD SSO con Symantec Web Security Service (WSS), seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Symantec Web Security Service (WSS):](#configure-symantec-web-security-service-wss-sso)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. Creare un utente di test di **[Symantec Web Security Service (WSS):](#create-symantec-web-security-service-wss-test-user)** per avere una controparte di B.Simon in Symantec Web Security Service (WSS) collegata alla Azure AD dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina portale di Azure'applicazione **Symantec Web Security Service (WSS)** individuare  la sezione Gestione e selezionare **Single Sign-On.**
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Nella casella di testo **URL di risposta** digitare il valore `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Contattare il [team di supporto clienti di Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) se per qualche motivo i valori **Identificatore** e **URL di risposta** non funzionano. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Symantec Web Security Service (WSS).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Symantec Web Security Service (WSS)**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-symantec-web-security-service-wss-sso"></a>Configurare l'accesso SSO di Symantec Web Security Service (WSS)

Per configurare l'accesso Single Sign-On sul lato Symantec Web Security Service (WSS), fare riferimento alla documentazione online di WSS. Il file **XML dei metadati della federazione** scaricato dovrà essere importato nel portale di WSS. Per assistenza per la configurazione nel portale di WSS, contattare il [team di supporto di Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us).

### <a name="create-symantec-web-security-service-wss-test-user"></a>Creare l'utente di test di Symantec Web Security Service (WSS)

In questa sezione viene creato un utente di nome Britta Simon in Symantec Web Security Service (WSS). Il nome utente corrispondente può essere creato manualmente nel portale di WSS oppure è possibile attendere alcuni minuti (circa 15) affinché venga eseguita la sincronizzazione con il portale di WSS degli utenti/gruppi di cui è stato effettuato il provisioning in Azure AD. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. È anche necessario effettuare il provisioning nel portale di Symantec Web Security Service (WSS) dell'indirizzo IP pubblico del computer dell'utente finale che verrà usato per esplorare i siti Web.

> [!NOTE]
> Fare clic [qui](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) per ottenere l'indirizzo IP pubblico del computer.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su Test this application in portale di Azure per accedere automaticamente all'applicazione Symantec Web Security Service (WSS) per cui si è configurato l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Symantec Web Security Service (WSS) nel App personali, si dovrebbe accedere automaticamente all'applicazione Symantec Web Security Service (WSS) per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Symantec Web Security Service (WSS), è possibile applicare il controllo sessione, che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
