---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler Beta | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 4e8dce970c9e7cfb2aa7887cf1fb3ed6ef9030a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735587"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler Beta

Questa esercitazione spiega come integrare Zscaler Beta con Azure Active Directory (Azure AD).
Quando si integra Zscaler Beta con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Zscaler Beta.
* Consentire agli utenti di eseguire l'accesso automatico (Single Sign-On) a Zscaler Beta con gli account Azure AD personali. Questo controllo di accesso è denominato accesso Single Sign-On (SSO).
* È possibile gestire gli account in un'unica posizione centrale usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zscaler Beta, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Zscaler Beta che usa l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ZScaler Beta supporta SSO avviato da **SP** .
* ZScaler Beta supporta il provisioning degli utenti **just-in-Time** .

## <a name="adding-zscaler-beta-from-the-gallery"></a>Aggiunta di Zscaler Beta dalla raccolta

Per configurare l'integrazione di Zscaler Beta in Azure AD, è necessario aggiungere Zscaler Beta dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **zScaler beta** nella casella di ricerca.
1. Selezionare **zScaler beta** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Configurare e testare Azure AD SSO per zScaler beta

Configurare e testare Azure AD SSO con zScaler beta usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in zScaler beta.

Per configurare e testare Azure AD SSO con zScaler beta, seguire questa procedura:


1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare zScaler beta SSO](#configure-zscaler-beta-sso)** : per configurare le singole impostazioni di Sign-On sul lato applicazione.
    1. **[Creare un utente di test di zScaler beta](#create-zscaler-beta-test-user)** : per avere una controparte di B. Simon in zScaler beta collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **zScaler beta** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella **URL di accesso** immettere l'URL usato dagli utenti per accedere all'applicazione zScaler beta beta.

    > [!NOTE]
    > Poiché il valore non è reale, è necessario aggiornarlo con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Zscaler Beta](https://www.zscaler.com/company/contact).

5. L'applicazione Zscaler Beta prevede un formato specifico per le asserzioni SAML. È necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Selezionare **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Finestra di dialogo Attributi utente](common/edit-attribute.png)

6. L'applicazione Zscaler Beta prevede inoltre il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** seguire questa procedura per aggiungere l'attributo del token SAML come illustrato nella tabella seguente.
    
    | Nome | Attributo di origine | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Selezionare **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    b. Nella casella **Nome** immettere il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per **Origine**, selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** immettere il valore dell'attributo indicato per la riga.

    f. Selezionare **OK**.

    g. Selezionare **Salva**.

    > [!NOTE]
    > Fare clic [qui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) per informazioni su come configurare un ruolo in Azure AD.

7. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** per scaricare il file **Certificato (Base64)**. Salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Configura Zscaler Beta** copiare gli URL necessari in base alle esigenze:

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a zScaler beta.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Zscaler Beta**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se i ruoli sono stati configurati come illustrato sopra, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-zscaler-beta-sso"></a>Configurare zScaler beta SSO

1. Per automatizzare la configurazione all'interno di Zscaler Beta, installare l'estensione del browser **My Apps Secure Sign-in Extension** selezionando **Installa l'estensione**.

    ![Estensione My Apps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, selezionare **Configura Zscaler Beta** per aprire direttamente l'applicazione Zscaler Beta. Nell'applicazione fornire le credenziali di amministratore per accedere a Zscaler Beta. L'estensione del browser configura automaticamente l'applicazione e automatizza i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Per configurare manualmente Zscaler Beta, aprire una nuova finestra del Web browser. Accedere al sito aziendale di Zscaler Beta come amministratore e seguire questa procedura.

4. Passare ad **Amministrazione**  >  **autenticazione** autenticazione  >  **Impostazioni** e seguire questa procedura.
   
    ![Amministrazione](./media/zscaler-beta-tutorial/ic800206.png "Amministrazione")

    a. In **Authentication Type** (Tipo di autenticazione) selezionare **SAML**.

    b. Selezionare **Configure SAML** (Configura SAML).

5. Nella finestra **Edit SAML** (Modifica SAML) seguire questa procedura: 
            
    ![Gestire utenti e autenticazione](./media/zscaler-beta-tutorial/ic800208.png "Manage Users & Authentication")
    
    a. Nella casella **SAML Portal URL** (URL del portale SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella **Login Name Attribute** (Attributo del nome di accesso) immettere **NameID**.

    c. Nella casella **Public SSL Certificate** (Certificato SSL pubblico) selezionare **Upload** (Carica) per caricare il certificato di firma SAML di Azure scaricato dal portale di Azure.

    d. Attivare/disattivare **Enable SAML Auto-Provisioning** (Abilita provisioning automatico SAML).

    e. Nella casella **User Display Name Attribute** (Attributo del nome visualizzato utente) immettere **displayName** se si vuole abilitare il provisioning automatico SAML per gli attributi displayName.

    f. Nella casella **Group Name Attribute** (Attributo del nome gruppo) immettere **memberOf** se si vuole abilitare il provisioning automatico SAML per gli attributi memberOf.

    g. Nella casella di testo **Department Name Attribute** (Attributo nome reparto) immettere **department** se si vuole abilitare il provisioning automatico SAML per gli attributi department.

    h. Selezionare **Salva**.

6. Nella pagina della finestra di dialogo **Configure User Authentication** (Configura autenticazione utente) seguire questa procedura:

    ![Menu Activation e pulsante Activate](./media/zscaler-beta-tutorial/ic800207.png)

    a. Passare il puntatore sul menu **Activation** (Attivazione) in basso a sinistra.

    b. Selezionare **Attiva**.

## <a name="configure-proxy-settings&quot;></a>Configurare le impostazioni proxy
Per configurare le impostazioni proxy in Internet Explorer, seguire questa procedura.

1. Avviare **Internet Explorer**.

2. Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**. 
    
     ![Finestra di dialogo Opzioni Internet](./media/zscaler-beta-tutorial/ic769492.png &quot;Opzioni Internet")

3. Selezionare la scheda **Connessioni**. 
  
     ![Scheda Connessioni](./media/zscaler-beta-tutorial/ic769493.png "Connessioni")

4. Selezionare **Impostazioni LAN** per aprire la finestra di dialogo **Impostazione rete locale (LAN)**.

5. Nella sezione **Server proxy** seguire questa procedura: 
   
    ![Sezione Server proxy](./media/zscaler-beta-tutorial/ic769494.png "Server proxy")

    a. Selezionare la casella di controllo **Usa un server proxy per le connessioni LAN**.

    b. Nella casella **Indirizzo** immettere **gateway.Zscaler Beta.net**.

    c. Nella casella **Porta** immettere **80**.

    d. Selezionare la casella **di controllo Ignora server proxy per indirizzi locali** .

    e. Selezionare **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)**.

6. Selezionare **OK** per chiudere la finestra di dialogo **Opzioni Internet**.

### <a name="create-zscaler-beta-test-user"></a>Creare l'utente di test di Zscaler Beta

In questa sezione viene creato l'utente Britta Simon in Zscaler Beta. Zscaler Beta supporta il **provisioning utenti JIT**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Zscaler Beta, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Per creare un utente manualmente, contattare il [team di supporto di Zscaler Beta](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso a zScaler beta, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di zScaler beta e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro zScaler beta in app personali, questo verrà reindirizzato all'URL di accesso di zScaler beta. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la versione beta di zScaler, è possibile applicare il controllo della sessione, che protegge i exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
