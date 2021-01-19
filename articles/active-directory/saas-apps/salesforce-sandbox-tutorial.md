---
title: 'Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Salesforce Sandbox.
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
ms.openlocfilehash: 23f1fad851e2c2aeff29e596dd7550b4c7d7bd6a
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967736"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Salesforce Sandbox

Questa esercitazione descrive come integrare Salesforce Sandbox con Azure Active Directory (Azure AD). Integrando Salesforce Sandbox con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Salesforce Sandbox.
* Abilitare gli utenti per l'accesso automatico a Salesforce Sandbox con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Salesforce Sandbox abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Salesforce Sandbox supporta l'accesso Single Sign-On avviato da **SP e IDP**
* Salesforce Sandbox supporta il provisioning utenti **Just-In-Time** (JIT)
* Salesforce Sandbox supporta il [**provisioning utenti automatico**](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Aggiunta di Salesforce Sandbox dalla raccolta

Per configurare l'integrazione di Salesforce Sandbox in Azure AD, è necessario aggiungere Salesforce Sandbox dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Salesforce Sandbox** nella casella di ricerca.
1. Selezionare **Salesforce Sandbox** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-salesforce-sandbox"></a>Configurare e testare l'accesso SSO di Azure AD per Salesforce Sandbox

Configurare e testare l'accesso SSO di Azure AD con Salesforce Sandbox usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Salesforce Sandbox.

Per configurare e testare l'accesso SSO di Azure AD con Salesforce Sandbox, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Salesforce Sandbox](#configure-salesforce-sandbox-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Salesforce Sandbox](#create-salesforce-sandbox-test-user)** : per avere una controparte di B.Simon in Salesforce Sandbox collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Salesforce Sandbox** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si dispone del **file di metadati del provider di servizi** e si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    > [!NOTE]
    > Il file di metadati del provider di servizi sarà disponibile nel portale di amministrazione Salesforce Sandbox, come spiegato più avanti nell'esercitazione.

    c. Dopo che è stato caricato il file di metadati, il valore **URL di risposta** viene popolato automaticamente nella casella di testo della sezione **URL di risposta**.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Se il valore di **URL di risposta** non viene popolato automaticamente, inserirlo manualmente in base alle esigenze.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **file XML dei metadati** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Salesforce Sandbox** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Salesforce Sandbox.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Salesforce Sandbox**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-salesforce-sandbox-sso"></a>Configurare l'accesso Single Sign-On di Salesforce Sandbox

1. Aprire una nuova scheda del browser e accedere all'account amministratore di Salesforce Sandbox.

2. Fare clic su **Setup** (Configura) sotto l'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Screenshot che mostra l'icona "Settings" in alto a destra selezionata e l'opzione "Setup" selezionata nell'elenco a discesa.](./media/salesforce-sandbox-tutorial/configure1.png)

3. Scorrere verso il basso fino alla voce **IMPOSTAZIONI** nel riquadro di spostamento a sinistra e fare clic su **Identità** per espandere la sezione corrispondente. Fare quindi clic su **Single Sign-On Settings**.

    ![Screenshot che mostra il menu "Settings" nel riquadro di sinistra e l'opzione "Single Sign-On Settings" selezionata nel menu "Identity".](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Nella pagina **Single Sign-On Settings** fare clic su **Edit**.

    ![Screenshot che mostra la pagina "Single Sign-On Settings" con il pulsante "Edit" selezionato.](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selezionare **SAML Enabled**, quindi fare clic su **Save**.

    ![Screenshot che mostra la pagina "Single Sign-On Settings" con la casella di controllo "SAML Enabled" e il pulsante "Save" selezionati.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Per configurare le impostazioni dell'accesso Single Sign-On SAML, fare clic su **New from Metadata File** (Nuovo da file di metadati).

    ![Screenshot che mostra la pagina "Single Sign-On Settings" con il pulsante "New from Metadata File" selezionato.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Fare clic su **Scegli file** per caricare il file XML di metadati scaricato dal portale di Azure e fare clic su **Crea**.

    ![Screenshot che mostra la pagina "Single Sign-On Settings" con i pulsanti "Choose File" e "Create" selezionati.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Nella pagina **SAML Single Sign-On Settings** (Impostazioni SAML Single Sign-On) i campi vengono popolati automaticamente. Quindi fare clic su Salva.

    ![Screenshot che mostra la pagina "Single Sign-On Settings" con i campi precompilati e il pulsante "Save" selezionato.](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Nella pagina **Impostazioni Single Sign-on** fare clic su **Scarica metadati** per scaricare il file di metadati del provider di servizi. Usare il file nella sezione **Configurazione SAML di base** del portale di Azure per configurare gli URL necessari come spiegato in precedenza.

    ![Screenshot che mostra la pagina "Single Sign-On Settings" con il pulsante "Download Metadata" selezionato.](./media/salesforce-sandbox-tutorial/configure4.png)

10. Per configurare l'applicazione in modalità avviata da **SP**, è necessario soddisfare i prerequisiti seguenti:

    a. È necessario un dominio verificato.

    b. È necessario configurare e abilitare il dominio in Salesforce Sandbox. La relativa procedura è illustrata più avanti in questa esercitazione.

    c. Nella sezione **Configurazione SAML di base** del portale di Azure, fare clic su **Impostare URL aggiuntivi** e seguire questa procedura:
  
    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Salesforce Sandbox](common/both-signonurl.png)

    Nella casella di testo **URL di accesso** digitare il valore usando il modello seguente: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Questo valore deve essere copiato dal portale di Sandbox Salesforce dopo avere abilitato il dominio.

11. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati federazione** e quindi salvare il file XML nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

12. Aprire una nuova scheda del browser e accedere all'account amministratore di Salesforce Sandbox.

13. Fare clic su **Setup** (Configura) sotto l'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Screenshot che mostra l'icona "Settings" in alto a destra selezionata e l'opzione "Setup" selezionata nel menu a discesa.](./media/salesforce-sandbox-tutorial/configure1.png)

14. Scorrere verso il basso fino alla voce **IMPOSTAZIONI** nel riquadro di spostamento a sinistra e fare clic su **Identità** per espandere la sezione corrispondente. Fare quindi clic su **Single Sign-On Settings**.

    ![Screenshot che mostra il menu "Settings" nel riquadro di spostamento sinistro e l'opzione "Single Sign-On Settings" selezionata nel menu "Identity".](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Nella pagina **Single Sign-On Settings** fare clic su **Edit**.

    ![Screenshot che mostra la pagina "Single Sign-On Settings" con il pulsante "Edit" selezionato.](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selezionare **SAML Enabled**, quindi fare clic su **Save**.

    ![Screenshot che mostra la pagina "Single Sign-On Settings" con la casella di controllo "SAML Enabled" e il pulsante "Save" selezionati.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Per configurare le impostazioni dell'accesso Single Sign-On SAML, fare clic su **New from Metadata File** (Nuovo da file di metadati).

    ![Screenshot che mostra la pagina "Single Sign-On Settings" e il pulsante "New from Metadata File" selezionato.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Per caricare il file di metadati, fare clic su **Scegli file** e quindi scegliere **Crea**.

    ![Screenshot che mostra la pagina "Single Sign-On Settings" con il pulsante "Choose File" e l'opzione "Create" selezionati.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Nella pagina **Impostazioni SAML Single Sign-On** i campi vengono popolati automaticamente. Digitare il nome della configurazione (ad esempio, *SPSSOWAAD_Test*) nella casella di testo **Nome** e quindi fare clic su Salva.

    ![Screenshot che mostra la pagina "Single Sign-On Settings" con i campi precompilati (nome di esempio nella casella di testo "Name") e il pulsante "Save" selezionato.](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Per abilitare il dominio su Salesforce Sandbox, eseguire la procedura seguente:

    > [!NOTE]
    > Prima di abilitare il dominio è necessario creare lo stesso in Salesforce Sandbox. Per altre informazioni, vedere [Definizione del nome di dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Dopo aver creato il dominio, assicurarsi che sia configurato correttamente.

21. Nel pannello di navigazione sinistro in Salesforce Sandbox fare clic su **Company Settings** (Impostazioni aziendali) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale).

    ![Screenshot che mostra le opzioni "Company Settings" e "My Domain" selezionate nel riquadro di spostamento sinistro.](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Nella sezione **Configurazione autenticazione** fare clic su **Criterio di autenticazione**.

    ![Screenshot che mostra la sezione "Authentication Configuration" con il pulsante "Edit" selezionato.](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Nella sezione **Configurazione autenticazione**, per **Servizio di autenticazione** selezionare il nome dell'impostazione Single Sign-On SAML definita durante la configurazione SSO in Salesforce Sandbox e quindi fare clic su **Salva**.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Creare l'utente di test di Salesforce Sandbox

In questa sezione si crea un utente di nome Britta Simon in Salesforce Sandbox. Salesforce Sandbox supporta il provisioning JIT (Just-In-Time) che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in Salesforce Sandbox, ne viene creato uno nuovo quando si tenta di accedere a Salesforce Sandbox. Salesforce Sandbox supporta anche il provisioning utenti automatico; [qui](salesforce-sandbox-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Salesforce Sandbox, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Salesforce Sandbox e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Salesforce Sandbox per cui si è configurato l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Salesforce Sandbox in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Salesforce Sandbox per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Salesforce Sandbox, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
