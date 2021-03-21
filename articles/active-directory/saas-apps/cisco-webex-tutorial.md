---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cisco Webex Meetings | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cisco Webex Meetings.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: bb8ea637d0353e4efa0cb946f486d68639fc699d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592490"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cisco Webex Meetings

Questa esercitazione descrive come integrare Cisco Webex Meetings con Azure Active Directory (Azure AD). Integrando Cisco Webex Meetings con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Cisco Webex Meetings.
* Abilitare gli utenti per l'accesso automatico a Cisco Webex Meetings con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Cisco Webex Meetings abilitata per l'accesso Single Sign-On (SSO).

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Le riunioni Cisco WebEx supportano la funzionalità SSO avviata da **SP e IDP** .

* Le riunioni Cisco WebEx supportano il provisioning degli utenti **just-in-Time** .

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Aggiunta di Cisco Webex Meetings dalla raccolta

Per configurare l'integrazione di Cisco Webex Meetings in Azure AD, è necessario aggiungere Cisco Webex Meetings dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Cisco Webex Meetings** nella casella di ricerca.
1. Selezionare **Cisco Webex Meetings** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>Configurare e testare Azure AD SSO per le riunioni di Cisco WebEx

Configurare e testare l'accesso SSO di Azure AD con Cisco Webex Meetings usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cisco Webex Meetings.

Per configurare e testare Azure AD SSO con riunioni Cisco WebEx, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
   1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
   1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
   
1. **[Configurare l'accesso Single Sign-On di Cisco Webex Meetings](#configure-cisco-webex-meetings-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
   * **[Creare l'utente di test di Cisco Webex Meetings](#create-cisco-webex-meetings-test-user)**: per avere una controparte di B.Simon in Cisco Webex Meetings collegata alla rappresentazione dell'utente in Azure AD.
    
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Cisco WebEx Meetings** della portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** è possibile configurare l'applicazione in modalità avviata da **IDP** caricando il file di **metadati del provider di servizi** come descritto di seguito:
   1. Fare clic su **Carica il file di metadati**.
   1. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.
   1. Al termine del caricamento del file di metadati del provider di servizi, i valori di **Identificatore** e **URL di risposta** vengono popolati automaticamente nella sezione **Configurazione SAML di base**.
   
      > [!Note]
      > Si otterrà il file di metadati del provider di servizi in base alla procedura descritta più avanti nella sezione **Configurare l'accesso Single Sign-On di Cisco Webex Meetings** dell'esercitazione. 

1. Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:  
   1. Nella sezione **Configurazione SAML di base** fare clic sull'icona Modifica (la penna).

      ![Modificare la configurazione SAML di base](common/edit-urls.png)

   1. Nella casella di testo **URL di accesso** digitare l'URL nel formato seguente: `https://<customername>.my.webex.com`

1. L'applicazione Cisco Webex Meetings prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo Attributi utente.

   ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione Cisco Webex Meetings prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione Attestazioni utente della finestra di dialogo Attributi utente eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente: 

   | Nome | Attributo di origine|
   | ---------------|  --------- |
   |   firstname    | user.givenname |
   |   lastname    | user.surname |
   |   email       | user.mail |
   |   uid    | user.mail |

   1. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.
   1. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
   1. Lasciare vuota la casella **Spazio dei nomi**.
   1. Per Origine selezionare **Attributo**.
   1. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga dall'elenco a discesa.
   1. Fare clic su **Salva**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

   ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Cisco Webex Meetings** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cisco Webex Meetings.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Cisco Webex Meetings**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-cisco-webex-meetings-sso"></a>Configurare l'accesso Single Sign-On di Cisco Webex Meetings

1. Accedere alle riunioni di Cisco WebEx con le credenziali di amministratore.
1. Passare a **Common Site Settings** (Impostazioni sito comune) e quindi a **SSO Configuration** (Configurazione dell'accesso Single Sign-On).

   ![Screenshot che mostra la schermata Cisco Webex Administration con le opzioni Common Site Settings e SSO Configuration selezionate.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. Nella pagina **Webex Administration** (Amministrazione di Webex) seguire questa procedura:

   ![Screenshot che mostra la pagina Webex Administration con le informazioni descritte in questo passaggio.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. Selezionare **SAML 2.0** come valore di **Federation Protocol** (Protocollo di federazione).
   1. Fare clic sul link **Import SAML Metadata** (Importa metadati SAML) per caricare il file di metadati scaricato dal portale di Azure.
   1. Selezionare **profilo SSO** come provider di identità **avviato**  e fare clic sul pulsante **Esporta** per scaricare il file di metadati del provider di servizi e CARICArlo nella sezione di **configurazione SAML di base** in portale di Azure.
   1. Nella casella di testo **AuthContextClassRef** Digitare uno dei valori seguenti:
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      Per abilitare l'autenticazione a più fattori usando Azure AD, immettere i due valori come segue: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

   1. Selezionare **Auto Account Creation** (Creazione automatica dell'account).
   
      > [!NOTE]
      > Per l'abilitazione del provisioning utenti **just-in-time** è necessario controllare la **Auto Account Creation** (Creazione automatica dell'account). Inoltre, gli attributi token SAML devono essere passati nella risposta SAML.

   1. Fare clic su **Salva**.

      > [!NOTE]
      > Questa configurazione è valida solo per i clienti che usano Webex UserID nel formato dell'indirizzo di posta elettronica.
      > 
      > Per ulteriori informazioni su come configurare le riunioni di Cisco WebEx, vedere la pagina della [documentazione di WebEx](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) .

### <a name="create-cisco-webex-meetings-test-user"></a>Creare l'utente di test di Cisco Webex Meetings

Questa sezione descrive come creare un utente di nome B.Simon in Cisco Webex Meetings. Cisco Webex Meetings supporta il provisioning **just-in-time**, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in Cisco Webex Meetings, ne viene creato uno nuovo quando si tenta di accedere a Cisco Webex Meetings.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso a Cisco WebEx Meetings, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso alle riunioni di Cisco WebEx e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente alle riunioni di Cisco WebEx per le quali si configura l'accesso SSO.

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro riunioni Cisco WebEx in app personali, se è stato configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente alle riunioni di Cisco WebEx per le quali si configura SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Una volta configurate le riunioni Cisco WebEx è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)