---
title: 'Esercitazione: Integrazione di Azure Active Directory con Pegasystems | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare l'accesso Single Sign-On tra Azure Active Directory e Pegasystems.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 4cd767736d6349199f4c82b00cb0b35db36cdb44
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430139"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Esercitazione: Integrazione di Azure Active Directory con Pegasystems

Questa esercitazione descrive come integrare Pegasystems con Azure Active Directory (Azure AD). Quando si integrano i sistemi Pegasystems con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso ai sistemi Pegasystems.
* Consentire agli utenti di accedere automaticamente ai sistemi Pegasystems con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per Pegasystems Systems Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Pegasystems supporta l'accesso SSO avviato da SP e IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Aggiungere Pegasystems dalla raccolta

Per configurare l'integrazione di Pegasystems in Azure AD, è necessario aggiungere Pegasystems dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Pegasystems Systems** nella casella di ricerca.
1. Selezionare **sistemi Pegasystems** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Configurare e testare Azure AD SSO per i sistemi Pegasystems

Configurare e testare Azure AD SSO con i sistemi Pegasystems usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Pegasystems Systems.

Per configurare e testare Azure AD SSO con i sistemi Pegasystems, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Pegasystems Systems SSO](#configure-pega-systems-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Pegasystems Systems](#create-pega-systems-test-user)** : per avere una controparte di B. Simon in Pegasystems Systems collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Pegasystems Systems** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** completare la seguente procedura se si vuole configurare l'applicazione nella modalità avviata da IdP.

    ![Finestra di dialogo Configurazione SAML di base](common/idp-intiated.png)

    1. Nella casella **Identificatore** immettere un URL nel formato seguente:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Nella casella **URL di risposta** immettere un URL nel formato seguente:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Se si desidera configurare l'applicazione in modalità avviata da SP, selezionare **Imposta URL aggiuntivi** e completare la seguente procedura.

    ![Informazioni sull'accesso Single Sign-On di URL e dominio di Pegasystems](common/both-advanced-urls.png)

    1. Nella casella **URL di accesso** immettere il valore dell'URL di accesso.

    1. Nella casella **Stato dell'inoltro** immettere un URL nel formato seguente: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > I valori qui forniti sono segnaposto. È necessario usare l'identificatore, l'URL di risposta, l'URL di accesso e l'URL dello stato dell'inoltro effettivi. È possibile ottenere i valori dell'identificatore e dell'URL di risposta in un'applicazione Pega illustrata più avanti in questa esercitazione. Per ottenere il valore dello stato dell'inoltro, contattare il [team di supporto di Pegasystems](https://www.pega.com/contact-us). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. L'applicazione Pegasystems ha bisogno di un formato specifico per le asserzioni SAML. Per visualizzarle nel formato corretto, è necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi dei token SAML. Lo screenshot seguente mostra gli attributi predefiniti. Selezionare l'icona **Modifica** per aprire la finestra di dialogo **Attributi utente**:

    ![Attributi utente](common/edit-attribute.png)

7. Oltre agli attributi illustrati nello screenshot precedente, l'applicazione Pegasystems richiede il passaggio di alcuni altri attributi nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** completare questa procedura per aggiungere gli attributi del token SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Questi valori sono specifici dell'organizzazione. Fornire i valori appropriati.

    1. Selezionare **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**:

    ![Selezionare Aggiungi nuova attestazione](common/new-save-attribute.png)

    ![Finestra di dialogo Gestisci attestazioni utente](common/new-attribute-details.png)

    1. Nella casella **Nome** immettere il nome dell'attributo indicato per la riga.

    1. Lasciare vuota la casella **Spazio dei nomi**.

    1. Per **Origine**, selezionare **Attributo**.

    1. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    1. Selezionare **OK**.

    1. Selezionare **Salva**.

8. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** accanto a **XML metadati federazione**, in base ai propri requisiti, quindi salvare il certificato nel computer:

    ![Collegamento di download del certificato](common/metadataxml.png)

9. Nella sezione **Configura Pegasystems** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso ai sistemi Pegasystems.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Pegasystems**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-pega-systems-sso"></a>Configurare SSO per Pegasystems Systems

1. Per configurare l'accesso Single Sign-On sul lato **Pegasystems**, accedere al portale Pega usando un account amministratore in un'altra finestra del browser.

2. Selezionare **Create** > **SysAdmin** > **Authentication Service** (Crea > Amministrazione del sistema > Servizio di autenticazione):

    ![Selezionare Authentication Service (Servizio di autenticazione)](./media/pegasystems-tutorial/admin.png)
    
3. Completare i passaggi seguenti nella schermata **Create Authentication Service** (Crea servizio di autenticazione).

    ![Schermata di creazione del servizio di autenticazione](./media/pegasystems-tutorial/admin1.png)

    1. Nell'elenco **Type** (Tipo) selezionare **SAML 2.0**.

    1. Nella casella **Name** (Nome) digitare un nome qualsiasi, ad esempio **Azure AD SSO**.

    1. Nella casella **Short description** (Descrizione breve) immettere una descrizione.  

    1. Selezionare **Create and open** (Crea e apri).
    
4. Nella sezione **Identity Provider (IdP) information** (Informazioni sul provider di identità) selezionare **Import IdP metadata** (Importa dati IdP) e cercare il file di metadati scaricato dal portale di Azure. Fare clic su **Submit** (Invia) per caricare i metadati:

    ![Sezione delle informazioni sul provider di identità (IdP)](./media/pegasystems-tutorial/admin2.png)
    
    L'importazione inserirà i dati del provider di identità come illustrato nella seguente immagine:

    ![Dati IdP importati](./media/pegasystems-tutorial/idp.png)
    
6. Completare la seguente procedura nella sezione **Service provider settings** (Impostazioni provider di servizi).

    ![Impostazioni provider di servizi](./media/pegasystems-tutorial/sp.png)

    1. Copiare il valore di **Entity Identification** (Identificazione entità) e incollarlo nella casella **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    1. Copiare il valore di **Assertion Consumer Service (ACS) location** (Posizione servizio consumer di asserzione) e incollarlo nella casella **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    1. Selezionare **Disable request signing** (Disabilita firma richiesta).

7. Selezionare **Salva**.

### <a name="create-pega-systems-test-user"></a>Creare l'utente di test di Pega Systems

A questo punto occorre creare un utente di nome Britta Simon in Pegasystems. Collaborare con il [team di supporto di Pegasystems](https://www.pega.com/contact-us) per creare gli utenti.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso ai sistemi Pegasystems in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Pegasystems Systems e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente ai sistemi Pegasystems per i quali si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Pegasystems Systems in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente ai sistemi Pegasystems per i quali si configura SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurati i sistemi Pegasystems, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).