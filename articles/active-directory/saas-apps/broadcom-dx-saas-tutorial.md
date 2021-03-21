---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Broadcom DX SaaS | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e la soluzione SaaS DX di Broadcom.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: jeedes
ms.openlocfilehash: 35ab4d335560689a7e55bf1a8dff9fcb0bcc7d7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601517"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Broadcom DX SaaS

In questa esercitazione si apprenderà come integrare Broadcom DX SaaS con Azure Active Directory (Azure AD). Quando si integra Broadcom DX SaaS con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a Broadcom DX SaaS.
* Abilitare gli utenti per l'accesso automatico a Broadcom DX SaaS con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Broadcom DX SaaS Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Broadcom DX SaaS supporta l'accesso SSO avviato da **IDP** .

* Broadcom DX SaaS supporta il provisioning degli utenti **just-in-Time** .

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>Aggiungere Broadcom DX SaaS dalla raccolta

Per configurare l'integrazione di Broadcom DX SaaS in Azure AD, è necessario aggiungere Broadcom DX SaaS dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Broadcom DX Saas** nella casella di ricerca.
1. Selezionare **Broadcom DX Saas** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Configurare e testare Azure AD SSO per Broadcom DX SaaS

Configurare e testare Azure AD SSO con Broadcom DX SaaS usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Broadcom DX SaaS.

Per configurare e testare Azure AD SSO con Broadcom DX SaaS, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Broadcom DX Saas SSO](#configure-broadcom-dx-saas-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Broadcom DX Saas](#create-broadcom-dx-saas-test-user)** : per avere una controparte di B. Simon in Broadcom DX Saas collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Broadcom DX Saas** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `DXI_<TENANT_NAME>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Broadcom DX Saas](mailto:dxi-na1@saas.broadcom.com) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione SaaS DX Broadcom prevede un formato specifico per le asserzioni SAML. è quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre a quanto sopra, l'applicazione SaaS DX di Broadcom prevede che vengano restituiti altri attributi nella risposta SAML, come illustrato di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome |  Attributo di origine|
    | --------------- | --------- |
    | Gruppo | user.groups |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **set up Broadcom DX Saas** copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Broadcom DX SaaS.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Broadcom DX Saas**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-broadcom-dx-saas-sso"></a>Configurare Broadcom DX SaaS SSO

1. Accedere al sito aziendale Broadcom DX SaaS come amministratore.

2. Passare a **Impostazioni** e fare clic sulla scheda **utenti** .

    ![Utenti](./media/broadcom-dx-saas-tutorial/settings.png "Utenti")

3. Nella sezione **Gestione utenti** fare clic su ellissi e selezionare **SAML**.

    ![Gestione utenti](./media/broadcom-dx-saas-tutorial/local.png "Gestione degli utenti")

4. Nella sezione **identificare l'account SAML** seguire questa procedura.
   
    ![Account](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Account") 

    a. Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Nella casella di testo **URL di accesso provider di identità (IDP)** incollare il valore dell' **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **URL di disconnessione del provider di identità (IDP)** incollare il valore dell' **URL di disconnessione** copiato dal portale di Azure.

    d. Aprire il **certificato (base64)** scaricato dal portale di Azure nel blocco note e incollare il contenuto nella casella di testo **Identity provider Certificate (certificato provider di identità** ).

    e. Fare clic su **Avanti**.

5. Nella sezione **attributi della mappa** inserire gli attributi SAML richiesti nella pagina seguente e fare clic su **Avanti**.

    ![Attributes (Attributi)](./media/broadcom-dx-saas-tutorial/broadcom-2.png "Attributi") 


6. Nella sezione **identificare il gruppo di utenti** immettere l'ID oggetto del gruppo e fare clic su **Avanti**.

    ![Aggiungi gruppo](./media/broadcom-dx-saas-tutorial/broadcom-3.png "Aggiungi gruppo") 

7. Nella sezione **popolare l'account SAML** verificare i valori popolati e fare clic su **Salva**.

    ![Account SAML](./media/broadcom-dx-saas-tutorial/broadcom-4.png "Account SAML") 

### <a name="create-broadcom-dx-saas-test-user"></a>Creare un utente di test di Broadcom DX SaaS

In questa sezione viene creato un utente di nome Britta Simon in Broadcom DX SaaS. Broadcom DX SaaS supporta il provisioning degli utenti just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già in Broadcom DX SaaS, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente all'applicazione SaaS DX di Broadcom per la quale si configura l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Broadcom DX SaaS in My Apps (app personali), si dovrebbe accedere automaticamente a Broadcom DX SaaS per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo la configurazione di Broadcom DX SaaS è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).