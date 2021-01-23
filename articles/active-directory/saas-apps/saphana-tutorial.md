---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP HANA | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP HANA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1a1e155974b66dce9a036a20cdebe19ded81fed5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98727082"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Esercitazione: Integrazione di Azure Active Directory con SAP HANA

Questa esercitazione descrive come integrare SAP HANA con Azure Active Directory (Azure AD). Integrando SAP HANA con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SAP HANA.
* Abilitare gli utenti per l'accesso automatico a SAP HANA con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP HANA, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di SAP HANA abilitata per l'accesso Single Sign-On (SSO)
- Istanza di HANA in esecuzione in qualsiasi VM IaaS pubblica, locale o di Azure oppure in istanze di grandi dimensioni di SAP in Azure
- Interfaccia Web di amministrazione di XSA e HANA Studio installati nell'istanza di HANA

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione SAP HANA per testare i passaggi di questa esercitazione. Testare prima l'integrazione nell'ambiente di sviluppo o di gestione temporanea dell'applicazione e successivamente usare l'ambiente di produzione.

A questo scopo, seguire queste indicazioni:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SAP HANA abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP HANA supporta l'accesso SSO avviato da **IdP**
* SAP HANA supporta il provisioning **Just-In-Time** (JIT) degli utenti

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.


## <a name="adding-sap-hana-from-the-gallery"></a>Aggiunta di SAP HANA dalla raccolta

Per configurare l'integrazione di SAP HANA in Azure AD, è necessario aggiungere SAP HANA dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAP HANA** nella casella di ricerca.
1. Selezionare **SAP HANA** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Configurare e testare l'accesso SSO di Azure AD per SAP HANA

Configurare e testare l'accesso SSO di Azure AD con SAP HANA usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP HANA.

Per configurare e testare l'accesso SSO di Azure AD con SAP HANA, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di SAP HANA](#configure-sap-hana-sso)** : per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di SAP HANA](#create-sap-hana-test-user)** : per avere una controparte di Britta Simon in SAP HANA collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SAP HANA** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. è necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere questi valori, contattare il [team di supporto clienti di SAP HANA](https://cloudplatform.sap.com/contact.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione SAP HANA prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Screenshot che mostra la sezione "Attributi utente" con l'icona "Modifica" selezionata.](common/edit-attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Attributi utente e attestazioni** eseguire questa procedura:
 
    a. Fare clic sull'**icona Modifica** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![Screenshot che mostra la finestra di dialogo "Attributi utente e attestazioni" con l'icona "Modifica" selezionata.](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Nell'elenco **Trasformazione** selezionare **ExtractMailPrefix()** .

    c. Nell'elenco **Parametro 1** selezionare **user.mail**.

    d. Fare clic su **Salva**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP HANA.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAP HANA**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-sap-hana-sso"></a>Configurare l'accesso SSO per SAP HANA

1. Per configurare l'accesso Single Sign-On sul lato SAP HANA, accedere alla **console Web HANA XSA** passando al relativo endpoint HTTPS.

    > [!NOTE]
    > Nella configurazione predefinita l'URL reindirizza la richiesta a una schermata di accesso, che richiede le credenziali di un utente del database SAP HANA autenticato. L'utente che effettua l'accesso deve disporre delle autorizzazioni per eseguire attività di amministrazione di SAML.

2. Nell'interfaccia Web XSA passare a **SAML Identity Provider** (Provider di identità SAML). A questo punto selezionare il pulsante **+** nella parte inferiore della schermata per visualizzare il riquadro **Add Identity Provider Info** (Aggiungi informazioni provider di identità). Seguire quindi questa procedura:

    ![Aggiungi provider di identità](./media/saphana-tutorial/sap1.png)

    a. Nel riquadro **Add Identity Provider Info** (Aggiungi informazioni provider di identità) incollare i contenuti del file XML metadati scaricato dal portale di Azure nella casella **Metadata** (Metadati).

    ![Screenshot che mostra il riquadro "Add Identity Provider Info" con le caselle "Metadata" e "Name" evidenziate.](./media/saphana-tutorial/sap2.png)

    b. Se il contenuto del documento XML è valido, il processo di analisi estrae le informazioni necessarie per i campi **Subject, Entity ID, and Issuer** (Oggetto, ID entità e Autorità di certificazione) nell'area dello schermo **General data** (Dati generali). Vengono estratte anche le informazioni necessarie per i campi relativi agli URL nell'area dello schermo **Destination** (Destinazione), ad esempio i campi **Base URL e SingleSignOn URL (*)** (URL di base e URL Single Sign-On).

    ![Impostazioni di aggiunta del provider di identità](./media/saphana-tutorial/sap3.png)

    c. Nella casella **Name** (Nome) dell'area dello schermo **General Data** (Dati generali) immettere un nome per il nuovo provider di identità SSO SAML.

    > [!NOTE]
    > Il nome del provider di identità SAML è obbligatorio e deve essere univoco. Viene visualizzato nell'elenco di provider di identità SAML disponibili che viene visualizzato quando si seleziona SAML come metodo di autenticazione per le applicazioni SAP HANA XS da usare. Ad esempio, è possibile eseguire questa procedura nell'area dello schermo **Authentication** (Autenticazione) dello strumento di amministrazione di elementi XS.

3. Selezionare **Save** (Salva) per salvare i dettagli del provider di identità SAML e aggiungere il nuovo provider di identità SAML all'elenco di provider noti.

    ![Pulsante per il salvataggio](./media/saphana-tutorial/sap4.png)

4. In Studio HANA, nella scheda **Configuration** (Configurazione) filtrare le impostazioni all'interno delle proprietà del sistema in base a **saml**. Modificare quindi il valore del parametro **assertion_timeout** da **10 sec** a **120 sec**.

    ![Impostazione assertion_timeout](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>Creare un utente di test di SAP HANA

Per consentire agli utenti di Azure AD di accedere a SAP HANA, è necessario effettuare il provisioning di tali utenti in SAP HANA.
SAP HANA supporta il **provisioning JIT**, che è abilitato per impostazione predefinita.

Per creare un utente manualmente, seguire questa procedura:

>[!NOTE]
>È possibile modificare l'autenticazione esterna usata dagli utenti. Questi possono autenticarsi con un sistema esterno, ad esempio Kerberos. Per informazioni dettagliate sulle identità esterne, contattare l'[amministratore di dominio](https://cloudplatform.sap.com/contact.html).

1. Aprire [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) come amministratore e quindi abilitare DB-User per l'accesso SSO SAML.

    ![Create user](./media/saphana-tutorial/sap5.png)

2. Selezionare la casella di controllo invisibile a sinistra di **SAML** e quindi selezionare il collegamento **Configura**.

3. Selezionare **Aggiungi** per aggiungere il provider di identità SAML.  Selezionare il provider di identità SAML appropriato e quindi **OK**.

4. Aggiungere un valore nel campo **External Identity** (Identità esterna), in questo caso BrittaSimon, oppure scegliere **Any** (Qualsiasi). Selezionare **OK**.

   > [!Note]
   > Se la casella di controllo **Any** (Qualsiasi) non è selezionata, il nome utente in HANA deve corrispondere esattamente al nome dell'utente nell'UPN prima del suffisso di dominio (ad esempio BrittaSimon@contoso.com diventerebbe BrittaSimon in HANA).

5. A scopo di test, assegnare tutti i ruoli **XS** all'utente.

    ![Assegnazione di ruoli](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > È consigliabile assegnare solo le autorizzazioni appropriate per i casi d'uso.

6. Salvare l'utente.

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Dopo aver fatto clic su Testa l'applicazione nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di SAP HANA per cui si è configurato l'accesso SSO

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di SAP HANA in App personali, si dovrebbe accedere automaticamente all'istanza di SAP HANA per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato SAP HANA, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).