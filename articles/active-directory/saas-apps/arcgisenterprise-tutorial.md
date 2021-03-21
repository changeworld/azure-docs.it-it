---
title: 'Esercitazione: Integrazione di Azure Active Directory con ArcGIS Enterprise | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9cde75440292fffb830656c32181d7be64a55f3d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645524"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con ArcGIS Enterprise

In questa esercitazione si apprenderà come integrare ArcGIS Enterprise con Azure Active Directory (Azure AD). Quando si integra ArcGIS Enterprise con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a ArcGIS Enterprise.
* Consentire agli utenti di accedere automaticamente ad ArcGIS Enterprise con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ArcGIS Enterprise Single Sign-On (SSO) abilitata.

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ArcGIS Enterprise supporta SSO avviato da **SP e IDP** .
* ArcGIS Enterprise supporta il provisioning degli utenti **just-in-Time** .

## <a name="add-arcgis-enterprise-from-the-gallery"></a>Aggiungere ArcGIS Enterprise dalla raccolta

Per configurare l'integrazione di ArcGIS Enterprise in Azure AD, è necessario aggiungere ArcGIS Enterprise dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ArcGIS Enterprise** nella casella di ricerca.
1. Selezionare **ArcGIS Enterprise** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Configurare e testare Azure AD SSO for ArcGIS Enterprise

Configurare e testare Azure AD SSO con ArcGIS Enterprise usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in ArcGIS Enterprise.

Per configurare e testare Azure AD SSO con ArcGIS Enterprise, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare ArcGIS Enterprise SSO](#configure-arcgis-enterprise-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di ArcGIS Enterprise](#create-arcgis-enterprise-test-user)** : per avere una controparte di B. Simon in ArcGIS Enterprise collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure nella pagina di integrazione dell'applicazione **ArcGIS Enterprise** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione nella modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `<EXTERNAL_DNS_NAME>.portal`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ArcGIS Enterprise](mailto:support@esri.com). Si otterrà il valore dell'identificatore dalla sezione **Set Identity Provider** (Imposta provider di identità), la cui procedura è descritta più avanti in questa esercitazione.

5. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a ArcGIS Enterprise.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **ArcGIS Enterprise**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-arcgis-enterprise-sso"></a>Configurare Enterprise SSO ArcGIS

1. Per automatizzare la configurazione all'interno di ArcGIS Enterprise, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura ArcGIS Enterprise** per passare direttamente all'applicazione ArcGIS Enterprise. Nell'applicazione specificare le credenziali di amministratore per accedere ad ArcGIS Enterprise. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare ArcGIS Enterprise manualmente, accedere al sito aziendale di ArcGIS Enterprise come amministratore.


1. Selezionare **Organization >EDIT SETTINGS** (Organizzazione > MODIFICA IMPOSTAZIONI).

    ![Screenshot che mostra la scheda ArcGIS Enterprise Organization con l'opzione Edit settings evidenziata.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Selezionare la scheda **Security** (Sicurezza).

    ![Screenshot che mostra la scheda Security selezionata.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Scorrere verso il basso alla sezione **Enterprise Logins via SAML** e selezionare **SET ENTERPRISE LOGIN** (IMPOSTA ACCESSO ENTERPRISE).

    ![Screenshot che mostra la sezione Enterprise Logins via SAML in cui è possibile selezionare Set Enterprise Login.](./media/arcgisenterprise-tutorial/configure-3.png)

1. Nella sezione **Set Identity Provider** (Imposta provider di identità) eseguire questa procedura:

    ![Screenshot che mostra la sezione Set Identity Provider in cui eseguire i passaggi descritti qui.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Specificare un nome simile a **Test di Azure Active Directory** nella casella di testo **Nome**.

    b. Nella casella di testo **URL** incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    c. Fare clic su **Mostra impostazioni avanzate**, copiare il valore **ID entità** e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio ArcGIS Enterprise** nel portale di Azure.

    ![Screenshot che mostra dove ottenere l'ID entità e pulsante Aggiorna provider di identità.](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Fare clic su **AGGIORNA PROVIDER DI IDENTITÀ**.

### <a name="create-arcgis-enterprise-test-user"></a>Creare l'utente di test di ArcGIS Enterprise

In questa sezione viene creato un utente di nome Britta Simon in ArcGIS Enterprise. ArcGIS Enterprise supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in ArcGIS Enterprise, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Per creare un utente manualmente contattare il [team di supporto di ArcGIS Enterprise](mailto:support@esri.com).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso ArcGIS Enterprise, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente a ArcGIS Enterprise Sign-on URL e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente a ArcGIS Enterprise per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro ArcGIS Enterprise in app personali, se è stato configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente a ArcGIS Enterprise per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato ArcGIS Enterprise è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).