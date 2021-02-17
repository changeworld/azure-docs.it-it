---
title: 'Esercitazione: Integrazione di Azure Active Directory con Costpoint | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Costpoint.
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
ms.openlocfilehash: ed3a808085e09ec7b5989fc623cc9129244ca2a6
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559088"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Esercitazione: Integrare Costpoint con Azure Active Directory

Questa esercitazione descrive come integrare Costpoint con Azure Active Directory (Azure AD). Integrando Costpoint con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Costpoint.
* Abilitare gli utenti per l'accesso automatico a Costpoint con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Costpoint abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

* Costpoint supporta l'accesso SSO avviato da **SP e IDP**.

## <a name="generate-costpoint-metadata"></a>Generare i metadati di Costpoint

La configurazione SSO SAML di Costpoint è illustrata nella guida **DeltekCostpoint711Security.pdf**. Scaricare questa guida dal sito di supporto Deltek Costpoint e fare riferimento alla sezione **SAML Single Sign-on Setup** (Configurazione dell'accesso Single Sign-On di SAML) >  **Configure SAML Single Sign-on between Costpoint and Azure AD** (Configura l'accesso Single Sign-On di SAML tra Costpoint e Microsoft Azure). Seguire le istruzioni e generare un file **XML dei metadati di federazione SP di Costpoint**. 

![Screenshot che mostra la schermata "Product Configuration Utility" con la scheda "Weblogic - Security" selezionata.](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Aggiunta di Costpoint dalla raccolta

Per configurare l'integrazione di Costpoint in Azure AD, è necessario aggiungere Costpoint dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Costpoint** nella casella di ricerca.
1. Selezionare **Costpoint** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Configurare e testare Azure AD SSO per Costpoint

Configurare e testare l'accesso SSO di Azure AD con Costpoint usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Costpoint.

Per configurare e testare Azure AD SSO con Costpoint, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare COSTPOINT SSO](#configure-costpoint-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Costpoint](#create-costpoint-test-user)** : per avere una controparte di B. Simon in Costpoint collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Costpoint** selezionare **Single Sign-On**.

1. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

   > [!NOTE]
   > Per ottenere il file di metadati del provider di servizi, vedere [Generare i metadati di Costpoint](#generate-costpoint-metadata). La modalità d'uso del file verrà illustrata più avanti nell'esercitazione.
 
   1. Selezionare il pulsante **Carica file di metadati** e quindi selezionare il file **XML dei metadati di federazione SP di Costpoint**, generato in precedenza da Costpoint e quindi selezionare il pulsante **Aggiungi** per caricare il file.

      ![Caricare il file di metadati](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione Costpoint.

      > [!NOTE]
      > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, immetterli manualmente in base alle esigenze. Verificare che i valori di **Identificatore (ID entità)** e **URL di risposta (URL del servizio consumer di asserzione)** siano impostati correttamente e che il valore di **URL del servizio consumer di asserzione** corrisponda a un URL di Costpoint valido che termina con **/LoginServlet.cps**.

   1. Selezionare **Imposta URL aggiuntivi**. Per **Stato dell'inoltro** immettere un valore con il formato seguente: `system=[your system]` (ad esempio **system=DELTEKCP**).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Copia** per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel Blocco note.

   ![Certificato di firma SAML](common/copy-metadataurl.png)

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

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Costpoint.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Costpoint**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-costpoint-sso"></a>Configurare Costpoint SSO

1. Tornare all'utilità di configurazione di Costpoint. Nella casella di testo **IdP Federation Metadata XML** incollare i contenuti del file dell'*URL dei metadati di federazione dell'app*. 

   ![Utilità di configurazione di Costpoint](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. Continuare a seguire le istruzioni dalla guida **DeltekCostpoint711Security.pdf** per completare la configurazione SAML di Costpoint.

### <a name="create-costpoint-test-user"></a>Creare l'utente di test di Costpoint

In questa sezione viene creato un utente in Costpoint. Presupporre che l'ID utente sia **B.SIMON** e che il nome utente sia **B.Simon**. Collaborare con il [team di supporto clienti di Costpoint](https://www.deltek.com/about/contact-us) per aggiungere gli utenti alla piattaforma Costpoint. L'utente deve essere creato e attivato prima di usare l'accesso Single Sign-On.

Dopo aver creato l'utente, l'opzione da selezionare come **Metodo di autenticazione** dell'utente deve essere **Active Directory**, la casella di controllo **Single Sign-on SAML** deve essere selezionata e il nome utente di Azure Active Directory deve essere **Active Directory o ID certificato** (come illustrato nello screenshot seguente).

![Utente di Costpoint](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Costpoint, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Costpoint e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al Costpoint per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Costpoint in My Apps (app personali), se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al Costpoint per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Costpoint, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
