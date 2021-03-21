---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ADP | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ADP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: cff4a75468181354a2ff61c0f9ac36bf6c78b9dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736124"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con ADP

Questa esercitazione descrive come integrare ADP con Azure Active Directory (Azure AD). Integrando ADP con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad ADP.
* Abilitare gli utenti per l'accesso automatico ad ADP con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ADP abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ADP supporta l'accesso SSO avviato da **IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-adp-from-the-gallery"></a>Aggiunta di ADP dalla raccolta

Per configurare l'integrazione di ADP in Azure AD, è necessario aggiungere ADP dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ADP** nella casella di ricerca.
1. Selezionare **ADP** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-adp"></a>Configurare e testare l'accesso SSO di Azure AD per ADP

Configurare e testare l'accesso SSO di Azure AD con ADP usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ADP.

Per configurare e testare l'accesso SSO di Azure AD con ADP, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di ADP](#configure-adp-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di ADP](#create-adp-test-user)**: per avere una controparte di B.Simon in ADP collegata alla relativa rappresentazione in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nel portale di Azure, nella pagina di integrazione dell'applicazione **ADP** fare clic sulla scheda **Proprietà** e seguire questa procedura: 

    ![Proprietà del servizio Single Sign-On](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Impostare il valore del campo **Abilitata per l'accesso degli utenti** su **Sì**.

    b. Copiare l'**URL accesso utente** che dovrà essere incollato nella sezione **Configura URL di accesso**, descritta più avanti nell'esercitazione.

    c. Impostare il valore del campo **Assegnazione di utenti obbligatoria** su **Sì**.

    d. Impostare il valore del campo **Visibile agli utenti** su **No**.

1. Nella pagina di integrazione dell'applicazione **ADP** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    Nella casella di testo **Identificatore (ID entità)** digitare un URL: `https://fed.adp.com`

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura ADP** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad ADP.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **ADP**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-adp-sso"></a>Configurare l'accesso Single Sign-On di ADP

Per configurare l'accesso Single Sign-On sul lato **ADP**, è necessario caricare il file **XML dei metadati** scaricato nel [sito Web di ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Questo processo potrebbe richiedere qualche giorno.

### <a name="configure-your-adp-services-for-federated-access"></a>Configurare i servizi ADP per l'accesso federato

>[!Important]
> I dipendenti che richiedono l'accesso federato ai servizi ADP devono essere assegnati all'app di servizio ADP e, successivamente, gli utenti devono essere riassegnati al servizio ADP specifico.
Al momento della ricezione della conferma dal rappresentante ADP, configurare i servizi ADP e assegnare/gestire gli utenti per controllare l'accesso al servizio ADP specifico.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ADP** nella casella di ricerca.
1. Selezionare **ADP** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.
1. Nel portale di Azure, nella pagina di integrazione dell'applicazione **ADP** fare clic sulla scheda **Proprietà** e seguire questa procedura:  

    ![Proprietà collegate per l'accesso Single Sign-On](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    1. Impostare il valore del campo **Abilitata per l'accesso degli utenti** su **Sì**.

    1. Impostare il valore del campo **Assegnazione di utenti obbligatoria** su **Sì**.

    1. Impostare il valore del campo **Visibile agli utenti** su **Sì**.

1. Nella pagina di integrazione dell'applicazione **ADP** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.

1. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare **Collegato** come **Modalità**. Per collegare l'applicazione ad **ADP**.

    ![Single Sign-On collegato](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Nella sezione **Configura URL di accesso** seguire questa procedura:

    ![Proprietà Single Sign-On](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    1. Incollare l'**URL di accesso utente** copiato in precedenza dalla scheda **Proprietà** (dall'app ADP principale).

    1. Le 5 app seguenti supportano **URL di stato dell'inoltro** diversi. È necessario aggiungere manualmente il valore appropriato dell'**URL di stato dell'inoltro** per una particolare applicazione all'**URL di accesso utente**.

        * **ADP Workforce Now**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

        * **ADP Workforce Now Enhanced Time**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`

        * **ADP Vantage HCM**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

        * **ADP Enterprise HR**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

        * **MyADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

1. **Salvare** le modifiche.

1. Dopo aver ricevuto conferma dal rappresentante ADP, iniziare i test con uno o due utenti.

    1. Assegnare alcuni utenti all'app del servizio ADP per testare l'accesso federato.

    1. Il test ha esito positivo quando gli utenti accedono all'app del servizio ADP nella raccolta e possono accedere al rispettivo servizio ADP.

1. Alla conferma dell'esito positivo del test, assegnare il servizio federato ADP a singoli utenti o gruppi di utenti, come illustrato più avanti nell'esercitazione, e distribuirlo ai dipendenti.

### <a name="create-adp-test-user"></a>Creare l'utente di test di ADP

Questa sezione descrive come creare un utente di nome B.Simon in ADP. Collaborare con il [team di supporto di ADP](https://www.adp.com/contact-us/overview.aspx) per aggiungere gli utenti nell'account ADP. 

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Dopo aver fatto clic su Test this application (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di ADP per cui si è configurato l'accesso SSO

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di ADP in App personali, si dovrebbe accedere automaticamente all'istanza di ADP per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato ADP, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).