---
title: 'Esercitazione: integrazione di Azure Active Directory con il controllo di accesso di genea | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e il controllo di accesso a genea.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 82c05f77781abdaea3b2c84aa1071656c206439a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669864"
---
# <a name="tutorial-azure-active-directory-integration-with-genea-access-control"></a>Esercitazione: integrazione di Azure Active Directory con il controllo di accesso di genea

In questa esercitazione si apprenderà come integrare il controllo di accesso di genea con Azure Active Directory (Azure AD). Quando si integra il controllo di accesso di genea con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a genea Access Control.
* Consentire agli utenti di accedere automaticamente al controllo di accesso a genea con i loro account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare Azure AD l'integrazione con il controllo di accesso di Genea, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Single Sign-On abilitata per il controllo di accesso genea

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Il controllo di accesso di genea supporta la funzionalità SSO avviata da **SP e IDP** .
> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.


## <a name="adding-genea-access-control-from-the-gallery"></a>Aggiunta del controllo di accesso a genea dalla raccolta

Per configurare l'integrazione del controllo di accesso di Genea in Azure AD, è necessario aggiungere il controllo di accesso di genea dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **genea Access Control** nella casella di ricerca.
1. Selezionare il **controllo di accesso di genea** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-genea-access-control"></a>Configurare e testare Azure AD SSO per il controllo di accesso a genea

Configurare e testare Azure AD SSO con il controllo di accesso di genea usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in controllo di accesso di genea.

Per configurare e testare Azure AD SSO con il controllo di accesso di Genea, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare il controllo di accesso SSO di genea](#configure-genea-access-control-sso)** per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di genea Access Control](#create-genea-access-control-test-user)** : per avere una controparte di B. Simon in genea Access Control collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione del **controllo di accesso di genea** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    Nella casella di testo **Identificatore** digitare l'URL: `https://login.sequr.io`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    a. Nella casella di testo **URL di accesso** digitare l'URL: `https://login.sequr.io`

    b. Nella casella di testo **Stato dell'inoltro** si otterrà questo valore, che verrà spiegato più avanti nell'esercitazione.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **configurare il controllo di accesso di genea** , copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso al controllo di accesso di genea.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare il **controllo di accesso a genea**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.
## <a name="configure-genea-access-control-sso"></a>Configurare l'accesso SSO per il controllo di accesso a genea

1. In un'altra finestra del Web browser accedere al sito aziendale di controllo di accesso di genea come amministratore.

1. Fare clic su **Integrazioni** nel pannello di spostamento a sinistra.

    ![Screenshot che mostra l'opzione Integrations selezionata nel riquadro di spostamento.](./media/sequr-tutorial/configure-1.png)

1. Scorrere verso il basso fino alla sezione **Single Sign-On**, quindi fare clic su **Gestisci**.

    ![Screenshot che mostra la sezione Single Sign-On con il pulsante Manage selezionato.](./media/sequr-tutorial/configure-2.png)

1. Nella sezione **Manage Single Sign-On** (Gestione Single Sign-On) seguire questa procedura:

    ![Screenshot che mostra la sezione Manage Single Sign-On in cui è possibile immettere i valori descritti.](./media/sequr-tutorial/configure-3.png)

    a. Nella casella di testo **URL di accesso Single Sign-On del provider di identità** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Trascinare il file del **certificato** scaricato dal portale di Azure oppure immettere manualmente il contenuto del certificato.

    c. Dopo aver salvato la configurazione, verrà generato il valore dello stato dell'inoltro. Copiare il valore dello **stato dell'inoltro** e incollarlo nella casella di testo **Stato dell'inoltro** della sezione **Configurazione SAML di base** del portale di Azure.

    d. Fare clic su **Salva**.

### <a name="create-genea-access-control-test-user"></a>Creare un utente di test di genea Access Control

In questa sezione viene creato un utente di nome Britta Simon in genea Access Control. Collaborare con il [team di supporto clienti di controllo di accesso di genea](mailto:support@sequr.io) per aggiungere gli utenti nella piattaforma di controllo di accesso di genea. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di controllo di accesso a Genea, in cui è possibile avviare il flusso di accesso.  

* Passare all'URL di accesso di controllo di accesso a genea direttamente e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al controllo di accesso di genea per il quale si configura SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di controllo di accesso Genea in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente al controllo di accesso di genea per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Quando si configura il controllo di accesso di Genea, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).