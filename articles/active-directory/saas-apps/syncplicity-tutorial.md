---
title: 'Esercitazione: Integrazione di Azure Active Directory con Syncplicity | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Syncplicity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 3c665795325ed3863583eb0f21f3e0d3f534154a
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201403"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Esercitazione: integrare Syncplicity con Azure Active Directory

Questa esercitazione descrive come integrare Syncplicity con Azure Active Directory (Azure AD). Integrando Syncplicity con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Syncplicity.
* Abilitare gli utenti per l'accesso automatico a Syncplicity con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita di 12 mesi [qui](https://azure.microsoft.com/free/).
* Sottoscrizione di Syncplicity abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Syncplicity supporta l'accesso SSO avviato da **SP**.

## <a name="adding-syncplicity-from-the-gallery"></a>Aggiunta di Syncplicity dalla raccolta

Per configurare l'integrazione di Syncplicity in Azure AD, è necessario aggiungere Syncplicity dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. In **Crea** fare clic su **applicazione aziendale**.
1. Nella sezione **sfoglia Azure ad Gallery** digitare **Syncplicity** nella casella di ricerca.
1. Selezionare **Syncplicity** nel pannello dei risultati e quindi fare clic su **Crea** per aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Syncplicity usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Syncplicity.

Per configurare e testare l'accesso SSO di Azure AD con Syncplicity, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Syncplicity](#configure-syncplicity-sso)**: per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Syncplicity](#create-syncplicity-test-user)**: per avere una controparte di B. Simon in Syncplicity collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.
7. **[Aggiorna SSO](#update-sso)** -per apportare le modifiche necessarie in Syncplicity se le impostazioni SSO sono state modificate in Azure AD.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Syncplicity** del [portale di Azure](https://portal.azure.com/)individuare la sezione **Introduzione** e selezionare **configura Single Sign-on**.
2. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<companyname>.syncplicity.com/sp`

    b. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.syncplicity.com`
    
    c. Nella casella di testo **Reply URL (Assertion Consumer Service URL)** (URL di risposta - URL del servizio consumer di asserzione) digitare un URL nel formato seguente: `https://<companyname>.syncplicity.com/Auth/AssertionConsumerService.aspx`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Syncplicity](https://www.syncplicity.com/contact-us). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **certificato di firma SAML** della pagina **Configura singolo Sign-On con SAML** fare clic su **modifica**. Nella finestra di dialogo fare clic sul pulsante con i puntini di sospensione accanto al certificato attivo e selezionare il **download del certificato PEM**.

   ![Collegamento di download del certificato](common/certificatebase64.png)

    > [!NOTE]
    > È necessario il certificato PEM, perché Syncplicity non accetta certificati in formato CER.

6. Nella sezione **Configura Syncplicity** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Configurare l'accesso Single Sign-On per Syncplicity

1. Accedere al tenant di **Syncplicity**.

1. Nel menu in alto fare clic su **amministratore**, selezionare **Impostazioni** e quindi fare clic su **dominio personalizzato e Single Sign-on**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Nella finestra di dialogo **Single Sign-On (SSO)** eseguire la procedura seguente:

    ![Single Sign-On \( SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Nella casella di testo **Dominio personalizzato** digitare il nome del dominio.
  
    b. Selezionare **Abilitato** per l'opzione **Stato di Single Sign-On**.

    c. Nella casella di testo **Entity Id** (ID entità) incollare il valore di **Identificatore (ID entità)** usato nella sezione **Configurazione SAML di base** del portale di Azure.

    d. Nella casella di testo **Sign-in page URL (URL pagina di accesso** ) incollare l' **URL di accesso** copiato dal portale di Azure.

    e. Nella casella di testo **Logout Page URL (URL pagina di disconnessione** ) incollare l' **URL di disconnessione** copiato dal portale di Azure.

    f. In **Identity Provider Certificate** (Certificato provider di identità) fare clic su **Choose file** (Scegli file) e quindi caricare il certificato scaricato dal portale di Azure.

    g. Fare clic su **SAVE CHANGES** (Salva modifiche).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
2. Selezionare **Nuovo utente** in alto nella schermata.
3. In **Proprietà utente** seguire questa procedura:

   a. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.

   b. Nel campo **Nome** immettere `B.Simon`.  
   
   c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   
   d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Syncplicity.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Syncplicity**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente/gruppo**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)
1. Nella pagina **Aggiungi assegnazione** selezionare **utenti**. 
1. Nella finestra di dialogo **utenti** selezionare **B. Simon** dall'elenco utenti, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella pagina **Aggiungi assegnazione** fare clic sul pulsante **assegna** .

### <a name="create-syncplicity-test-user"></a>Creare l'utente di test di Syncplicity

Per consentire agli utenti di Azure AD di accedere, è necessario effettuarne il provisioning nell'applicazione Syncplicity. Questa sezione descrive come creare account utente Azure AD in Syncplicity.

**Per eseguire il provisioning di un account utente, eseguire la procedura seguente:**

1. Accedere al tenant **Syncplicity** (ad esempio: `https://company.Syncplicity.com`).

1. Fare clic su **amministratore** e selezionare **account utente**, quindi fare clic su **Aggiungi un utente**.

    ![Gestire gli utenti](./media/syncplicity-tutorial/ic769764.png "Gestire gli utenti")

1. Digitare gli **indirizzi di posta elettronica** di un account Azure ad di cui si vuole eseguire il provisioning, selezionare **utente** come **ruolo**, quindi fare clic su **Avanti**.

    ![Informazioni sull'account](./media/syncplicity-tutorial/ic769765.png "Informazioni account")

    > [!NOTE]
    > Il titolare dell'account Azure AD riceve un messaggio di posta elettronica contenente un collegamento per confermare e attivare l'account.

1. Selezionare un gruppo nella società di cui il nuovo utente diventerà membro, quindi fare clic su **Avanti**.

    ![Appartenenza a gruppi](./media/syncplicity-tutorial/ic769772.png "Appartenenza al gruppo")

    > [!NOTE]
    > Se non sono elencati gruppi, fare clic su **Avanti**.

1. Selezionare le cartelle che si desidera inserire nel controllo di Syncplicity nel computer dell'utente, quindi fare clic su **Avanti**.

    ![Cartelle Syncplicity](./media/syncplicity-tutorial/ic769773.png "Cartella Syncplicity")

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Syncplicity per effettuare il provisioning degli account utente Azure AD.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Syncplicity nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Syncplicity per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

### <a name="update-sso"></a>Aggiorna SSO

Quando è necessario apportare modifiche a SSO, è necessario controllare il **certificato di firma SAML** usato. Se il certificato è stato modificato, assicurarsi di caricarne uno nuovo in Syncplicity, come descritto in **[configurare SYNCPLICITY SSO](#configure-syncplicity-sso)**.

Se si usa l'app per dispositivi mobili Syncplicity, rivolgersi al supporto tecnico di Syncplicity ( support@syncplicity.com ) per assistenza.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)
