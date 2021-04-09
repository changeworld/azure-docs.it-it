---
title: 'Esercitazione: Integrazione di Azure Active Directory con OpsGenie | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OpsGenie.
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
ms.openlocfilehash: 2ab1f9ee6095dfc0f708ec33622aad6f70fcae65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728526"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con OpsGenie

Questa esercitazione descrive come integrare OpsGenie con Azure Active Directory (Azure AD). Integrando OpsGenie con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a OpsGenie.
* Abilitare gli utenti per l'accesso automatico a OpsGenie con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di OpsGenie abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* OpsGenie supporta l'accesso SSO avviato da **IDP**

## <a name="adding-opsgenie-from-the-gallery"></a>Aggiunta di OpsGenie dalla raccolta

Per configurare l'integrazione di OpsGenie in Azure AD, è necessario aggiungere OpsGenie dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **OpsGenie** nella casella di ricerca.
1. Selezionare **OpsGenie** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-opsgenie"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per OpsGenie

Configurare e testare l'accesso SSO di Azure AD con OpsGenie usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OpsGenie.

Per configurare e testare l'accesso SSO di Azure AD con OpsGenie, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di OpsGenie](#configure-opsgenie-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di OpsGenie](#create-opsgenie-test-user)** : per avere una controparte di B.Simon in OpsGenie collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **OpsGenie** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di risposta effettivi, come illustrato più avanti in questa esercitazione.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

1. Nella sezione **Configura OpsGenie** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OpsGenie.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **OpsGenie**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-opsgenie-sso"></a>Configurare l'accesso Single Sign-On di OpsGenie

1. Per automatizzare la configurazione all'interno di OpsGenie, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura OpsGenie** per passare direttamente all'applicazione OpsGenie. Specificare quindi le credenziali di amministratore per accedere a OpsGenie. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare OpsGenie manualmente, in un'altra finestra del Web browser accedere al sito aziendale di OpsGenie come amministratore.

2. Fare clic su **Settings** e quindi selezionare la scheda **Single Sign On**.
   
    ![Accesso Single Sign-On di OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Per abilitare l'accesso Single Sign-On, selezionare **Enabled**.
   
    ![Screenshot che mostra la casella di controllo "Enabled" selezionata.](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. Nella sezione **Provider** fare clic sulla scheda **Azure Active Directory**.
   
    ![Screenshot che mostra la sezione "Provider" con la scheda "Azure Active Directory" selezionata.](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Nella pagina Azure Active Directory, seguire questa procedura:
   
    ![Screenshot che mostra la sezione "Single sign-on" con l'interruttore "Enable single sign-on" e le opzioni "SAML 2.0 Endpoint" e "Metadata URL".](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Copiare il valore di **App ID URI** (URI ID app) e incollarlo nella casella di testo **Identificatore (ID entità)** nella sezione **Configurazione SAML di base** del portale di Azure.

    a. Copiare il valore di **Reply URL** (URL di risposta) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    a. Nella casella di testo **SAML 2.0 Endpoint** (Endpoint SAML 2.0) incollare il valore di **URL di accesso** copiato dal portale di Azure.
    
    b. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.
    
    c. Per abilitare l'accesso Single Sign-On, attivare l'interruttore **Enable single sign-on** (Abilita Single Sign-On).

    d. Fare clic su **Apply SSO settings** (Applica impostazioni SSO).

### <a name="create-opsgenie-test-user"></a>Creare un utente di test di OpsGenie

In questa sezione viene creato un utente di nome B.Simon in OpsGenie. 

1. In una finestra del Web browser accedere al tenant di OpsGenie come amministratore.

2. Passare all'elenco di utenti facendo clic su **Users** (Utenti) nel pannello a sinistra.
   
    ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Fare clic su **Add User**.

4. Nella finestra di dialogo **Aggiungi utente** seguire questa procedura:
   
    ![Screenshot che mostra la finestra di dialogo "Add User" con le caselle di testo "Email" e "Full name" evidenziate e il pulsante "Save" selezionato.](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica di B.Simon in Azure Active Directory.
   
    b. Nella casella di testo **Full Name** (Nome completo) digitare **B.Simon**.
   
    c. Fare clic su **Salva**. 

> [!NOTE]
> B.Simon riceve un messaggio di posta elettronica contenente le istruzioni per la configurazione del profilo.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Dopo aver fatto clic su Test this application (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di OpsGenie per cui si è configurato l'accesso SSO

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di OpsGenie in App personali, si dovrebbe accedere automaticamente all'istanza di OpsGenie per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver configurato OpsGenie, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).