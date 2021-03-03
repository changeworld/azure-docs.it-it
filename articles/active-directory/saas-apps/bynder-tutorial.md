---
title: 'Esercitazione: Integrazione di Azure Active Directory con Bynder | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Bynder.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: jeedes
ms.openlocfilehash: 9e233e30521e5c1fa05509fa6dc42a567f6ac4e2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653100"
---
# <a name="tutorial-integrate-bynder-with-azure-active-directory"></a>Esercitazione: Integrare Bynder con Azure Active Directory

Questa esercitazione descrive come integrare Bynder con Azure Active Directory (Azure AD). Integrando Bynder con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Bynder.
* Abilitare gli utenti per l'accesso automatico a Bynder con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Bynder abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Bynder supporta l'accesso SSO avviato da **SP e IDP**
* Bynder supporta il provisioning utenti **JIT**

## <a name="add-bynder-from-the-gallery"></a>Aggiungere Bynder dalla raccolta

Per configurare l'integrazione di Bynder in Azure AD, è necessario aggiungere Bynder dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Bynder** nella casella di ricerca.
1. Selezionare **Bynder** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Bynder usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Bynder.

Per configurare e testare Azure AD SSO con Bynder, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
   1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
   1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di Bynder](#configure-bynder-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
   1. **[Creare l'utente di test di Bynder](#create-bynder-test-user)** : per avere una controparte di Britta Simon in Bynder collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Bynder** del portale di Azure individuare la sezione **Gestione** e selezionare **Single sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    
    Per un dominio predefinito: `https://<company name>.getbynder.com`
    
    Per un dominio personalizzato: `https://<subdomain>.<domain>.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:
    
     Per un dominio predefinito: `https://<company name>.getbynder.com/sso/SAML/authenticate/`
    
    Per un dominio personalizzato: `https://<subdomain>.<domain>.com/sso/SAML/authenticate/`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente:
    
     Per un dominio predefinito: `https://<company name>.getbynder.com/login/`
    
     Per un dominio personalizzato: ` https://<subdomain>.<domain>.com/login/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Bynder](https://www.bynder.com/en/support/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML dei metadati** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Bynder** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Bynder.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Bynder**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-bynder-sso"></a>Configurare l'accesso Single Sign-On di Bynder

Per configurare l'accesso Single Sign-On sul lato **Bynder**, è necessario inviare il file **XML dei metadati**  scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di Bynder](https://www.bynder.com/en/support/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.


### <a name="create-bynder-test-user"></a>Creare l'utente di test di Bynder

In questa sezione viene creato un utente di nome Britta Simon in Bynder. Bynder supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Bynder, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il [team di supporto di Bynder](https://www.bynder.com/en/support/).

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Bynder, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Bynder e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Bynder per cui si è configurato l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Bynder in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Bynder per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Bynder, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).