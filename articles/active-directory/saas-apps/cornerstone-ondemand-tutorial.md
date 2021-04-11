---
title: "Esercitazione: Azure Active Directory l'integrazione dell'accesso Single Sign-on (SSO) con l'elemento di base Sign-On singolo | Microsoft Docs"
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e Single Sign-on di base.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/09/2021
ms.author: jeedes
ms.openlocfilehash: f7167df523ca6f84eacd92fc7af1011e8b3b00b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950336"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-single-sign-on"></a>Esercitazione: Azure Active Directory l'integrazione dell'accesso Single Sign-on (SSO) con l'unica pietra miliare Sign-On

In questa esercitazione si apprenderà come integrare il singolo elemento fondamentale Sign-On con Azure Active Directory (Azure AD). Quando si integra la Sign-On di base singola con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso al Single Sign-on di base.
* Consentire agli utenti di accedere automaticamente a un'unica Sign-On cardine con gli account di Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di base Single Sign-On Single Sign-On (SSO) abilitata.

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Il singolo elemento cardine Sign-On supporta SSO avviato da **SP** .
* Il singolo elemento cardine Sign-On supporta il [provisioning automatico degli utenti](cornerstone-ondemand-provisioning-tutorial.md).


## <a name="adding-cornerstone-single-sign-on-from-the-gallery"></a>Aggiunta di una singola Sign-On cardine dalla raccolta

Per configurare l'integrazione di una singola Sign-On cardine in Azure AD, è necessario aggiungere l'elemento di base Single Sign-On dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** Digitare **Unsign-on per l'accesso Single Sign-on** nella casella di ricerca.
1. Selezionare **elemento di base Single Sign-on nel** pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-cornerstone-single-sign-on"></a>Configurare e testare Azure AD SSO per single Sign-On di base

Configurare e testare Azure AD SSO con una singola Sign-On cardine usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato nel Single Sign-on di base.

Per configurare e testare Azure AD SSO con l'accesso Single Sign-on di base, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. Configurare l'accesso Single-Sign-on **[Sign-On SSO](#configure-cornerstone-single-sign-on-sso)** per configurare le singole impostazioni Sign-On sul lato applicazione.
    1. **[Creare un utente di test di base single Sign-On](#create-cornerstone-single-sign-on-test-user)** : per avere una controparte di B. Simon in un singolo Sign-On di base collegato alla rappresentazione Azure ad dell'utente.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure della pagina di integrazione dell'applicazione **Single Sign-on** di base individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<PORTAL_NAME>.csod.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    c. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'URL di risposta, l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti Single Sign-On](mailto:moreinfo@csod.com) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura accesso Single Sign-on** di base, copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso al Single Sign-on di base.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare elemento di base **Single Sign-on**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-cornerstone-single-sign-on-sso"></a>Configurare la chiave singola Sign-On SSO

1. Accedere al Sign-On di base singolo come amministratore.

1. Accedere agli **strumenti di amministrazione->**.

    ![LinkedDataFormUpdated per la pagina di amministrazione.](./media/cornerstone-ondemand-tutorial/admin.png)

1. Selezionare Pannello **perimetrale** in **strumenti di configurazione**.

    ![LinkedDataFormUpdated per il pannello EDGE.](./media/cornerstone-ondemand-tutorial/edge-panel.png)

1. Selezionare Single Sign-On nella sezione **integrazione** .

    ![LinkedDataFormUpdated per l'opzione Single Sign-On.](./media/cornerstone-ondemand-tutorial/single-sign-on.png)

1. Fare clic sul pulsante **Aggiungi SSO** . Selezionare **SAML in ingresso** nella finestra popup riportata di seguito, quindi fare clic su **Aggiungi**.

    ![LinkedDataFormUpdated per SAML in ingresso.](./media/cornerstone-ondemand-tutorial/inbound.png)

1. Eseguire i passaggi seguenti nella pagina seguente:

    ![LinkedDataFormUpdated per la sezione di configurazione per l'elemento di base.](./media/cornerstone-ondemand-tutorial/configuration.png)

    a. In **Proprietà generali** fare clic su **Carica file** per caricare il file del **certificato (base64)** scaricato dal portale di Azure.

    b. Selezionare la casella di controllo **Abilita** e nella casella di testo **IDP URL (URL IDP** ) incollare il valore dell' **url di accesso** copiato dal portale di Azure.

    c. Fare clic su **Salva**.

### <a name="create-cornerstone-single-sign-on-test-user"></a>Creazione di un utente di test di base singolo Sign-On

Questa sezione descrive come creare un utente denominato B. Simon in elemento di base Single Sign-on. Il singolo elemento cardine Sign-On supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. Altri dettagli su come configurare il provisioning utenti automatico sono disponibili [qui](./cornerstone-ondemand-provisioning-tutorial.md).

**Per creare un utente manualmente, seguire questa procedura:**

1. Accedere al Sign-On di base singolo come amministratore.

1. Passare a **amministratore-> utenti** e fare clic su **Aggiungi utente** nella parte inferiore della pagina.

    ![LinkedDataFormUpdated per la creazione di un utente di test di base.](./media/cornerstone-ondemand-tutorial/user-1.png)

1. Compilare i campi obbligatori nella pagina **Aggiungi nuovo utente** e fare clic su **Salva**.

    ![LinkedDataFormUpdated per la creazione di utenti di test con i campi obbligatori.](./media/cornerstone-ondemand-tutorial/user-2.png)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. In questo modo verrà reindirizzato a un URL Single Sign-On URL di accesso in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso Single Sign-On Access e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di una singola Sign-On di base in app personali, viene reindirizzato a un URL di accesso Single Sign-On Sign-on. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la singola Sign-On cardine, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)