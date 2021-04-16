---
title: "Esercitazione: Azure Active Directory'integrazione dell'accesso Single Sign-On (SSO) con Check Point Identity Awareness | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Check Point Identity Awareness.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 68705d4d1f870820e30563d2a197ac56349d2445
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520920"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>Esercitazione: Azure Active Directory'integrazione dell'accesso Single Sign-On (SSO) con Check Point Identity Awareness

Questa esercitazione illustra come integrare Check Point Identity Awareness con Azure Active Directory (Azure AD). Integrando Check Point Identity Awareness con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Check Point Identity Awareness.
* Abilitare gli utenti per l'accesso automatico a Check Point riconoscimento delle identità con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Check Point sottoscrizione di Identity Awareness abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Check Point Identity Awareness supporta l'accesso SSO avviato da **SP.**

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>Aggiunta Check Point riconoscimento delle identità dalla raccolta

Per configurare l'integrazione di Check Point Identity Awareness in Azure AD, è necessario aggiungere Check Point Identity Awareness dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta digitare** Check Point Identity **Awareness** nella casella di ricerca.
1. Selezionare **Check Point Riconoscimento identità nel** pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>Configurare e testare l'Azure AD SSO per Check Point identity awareness

Configurare e testare Azure AD SSO con Check Point Identity Awareness usando un utente di test di **nome B.Simon**. Per il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Check Point Identity Awareness.

Per configurare e testare Azure AD SSO con Check Point Identity Awareness, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Check Point'accesso Single](#configure-check-point-identity-awareness-sso)** Sign-On di Identity Awareness: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. Creare Check Point utente di test di **[Identity Awareness:](#create-check-point-identity-awareness-test-user)** per avere una controparte di B.Simon in Check Point Identity Awareness collegata alla Azure AD dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina portale di Azure'applicazione **Check Point Identity Awareness** individuare la sezione Gestione  e selezionare **Single Sign-On.**
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<GATEWAY_IP>/connect`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per [ottenere Check Point, contattare il team di supporto clienti](mailto:support@checkpoint.com) di Identity Awareness. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella **sezione Configura Check Point riconoscimento** dell'identità copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso Check Point Identity Awareness.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare Check Point **Identity Awareness**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-check-point-identity-awareness-sso"></a>Configurare l Check Point'accesso Single Sign-On di Check Point Identity Awareness

1. Accedere al sito aziendale Check Point Identity Awareness come amministratore.

1. Nella visualizzazione SmartConsole > **Gateway & Server** fare clic su Nuovo > altre > **utente/identità**> provider di identità .

    ![screenshot per il nuovo provider di identità.](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. Nella finestra **New Identity Provider (Nuovo provider di identità) seguire questa** procedura.

    ![screenshot per la sezione Provider di identità.](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. Nel campo **Gateway** selezionare il gateway di sicurezza, che deve eseguire l'autenticazione SAML.

    b. Nel campo **Service (Servizio)** selezionare **Identity Awareness (Consapevolezza dell'identità)** nell'elenco a discesa.

    c. Copiare **il valore di Identificatore (ID entità)** e incollarlo nella casella di testo Identificatore nella sezione Configurazione  **SAML** di base del portale di Azure.

    d. Copiare **il valore di URL** di risposta e incollarlo nella casella di testo **URL** di risposta nella sezione **Configurazione SAML** di base del portale di Azure.

    e. Selezionare **Importa file di metadati** per caricare il certificato **(Base64)** scaricato dal portale di Azure.

    > [!NOTE]
    > In alternativa, è  anche possibile selezionare Inserisci manualmente per incollare manualmente i valori di **ID** entità e **URL** di accesso nei campi corrispondenti e caricare il **file** di certificato dal portale di Azure.

    f. Fare clic su **OK**.

### <a name="create-check-point-identity-awareness-test-user"></a>Creare un Check Point di test di Identity Awareness

In questa sezione viene creato un utente di nome Britta Simon in Check Point Identity Awareness. Collaborare con [Check Point team di](mailto:support@checkpoint.com) supporto di Identity Awareness per aggiungere gli utenti alla piattaforma Check Point Identity Awareness. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà reindirizzato all'URL Check Point'URL di accesso di Identity Awareness da cui è possibile avviare il flusso di accesso. 

* Passare direttamente Check Point'URL di accesso di Identity Awareness e avviare il flusso di accesso da questa pagina.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Check Point Identity Awareness nel App personali, si verrà reindirizzati all'URL Check Point di accesso di Identity Awareness. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Check Point Identity Awareness, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


