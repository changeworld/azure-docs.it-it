---
title: 'Esercitazione: integrare Azure Active Directory Single Sign-On (SSO) con il visitatore di sPlan | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Splan Visitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: ed136d06f18190124abe4598d580f40e41bf8592
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704221"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Esercitazione: integrare Azure Active Directory Single Sign-On (SSO) con splan Visitor

Questa esercitazione descrive come integrare Splan Visitor con Azure Active Directory (Azure AD). Integrando Splan Visitor con Azure AD, è possibile:

* Usare Azure AD per controllare chi ha accesso a splan Visitor.
* Consentire agli utenti di accedere automaticamente al visitatore di splan con i relativi account Azure AD.
* Gestire gli account in una posizione centrale, il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di splan Visitor Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Il visitatore di splan supporta SSO avviato da IdP.

## <a name="add-splan-visitor-from-the-gallery"></a>Aggiungere splan Visitor dalla raccolta

Per configurare l'integrazione di splan Visitor in Azure AD, aggiungere splan Visitor dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure usando un account aziendale o dell'Istituto di istruzione o un account Microsoft personale.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** immettere **splan Visitor** nella casella di ricerca.
1. Selezionare **Visitor splan** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Splan Visitor

Configurare e testare Azure AD SSO con splan Visitor usando un utente di test denominato **B. Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Splan Visitor.

Per configurare e testare l'accesso SSO di Azure AD con Splan Visitor, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure ad](#create-an-azure-ad-test-user)** per testare Azure ad Single Sign-on con l'utente test B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare splan Visitor SSO](#configure-splan-visitor-sso)** per configurare le impostazioni di Single Sign-on con il visitatore di splan.
    1. **[Creare un utente di test di splan Visitor](#create-a-splan-visitor-test-user)** per avere una controparte di B. Simon in splan Visitor collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Splan Visitor** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura singolo Sign-On con SAML** selezionare l'icona di **Modifica/penna** per la **configurazione SAML di base** per modificare le impostazioni.

   ![Screenshot che evidenzia l'icona di modifica/penna per la configurazione SAML di base.](common/edit-urls.png)

1. Nella sezione di **configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono prepopolati con Azure. Selezionare il pulsante **Salva** per salvare la configurazione.

1. Nella sezione **certificato di firma SAML** della pagina **configurare l'accesso Single Sign-on con SAML** , trovare il **file XML dei metadati federativi**. Selezionare **download** per scaricare il certificato e salvarlo nel computer.

    ![Screenshot che evidenzia il collegamento per il download XML dei metadati federativi.](common/metadataxml.png)

1. Nella sezione **set up splan Visitor** copiare l'URL o gli URL appropriati in base ai requisiti.

    ![Screenshot che evidenzia la sezione URL di configurazione.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, selezionare **utenti** e quindi selezionare **tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere **B. Simon**.  
   1. Nel campo **nome utente** immettere il nome utente nel _username@companydomain.extension_ formato. Ad esempio, immettere **B.Simon@contoso.com** .
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Splan Visitor.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **splan Visitor** per aprire la panoramica dell'app.
1. Individuare la sezione **Gestisci** e selezionare **utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **utenti e gruppi** selezionare **B. Simon** dall'elenco **utenti** , quindi fare clic su **Seleziona** nella parte inferiore della schermata.
1. Se all'utente viene assegnato un ruolo, selezionarlo nel menu **a discesa selezionare un ruolo** . Se per questa app non è stato configurato alcun ruolo, lasciare selezionato il ruolo **accesso predefinito** .
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-splan-visitor-sso"></a>Configurare l'accesso Single Sign-On di Splan Visitor

Per configurare Single Sign-On con splan Visitor, inviare il **file XML dei metadati di Federazione** scaricato e gli URL copiati appropriati dal portale di Azure al [team di supporto di splan Visitor](mailto:support@splan.com). Ciò garantisce che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-a-splan-visitor-test-user"></a>Creare un utente di test di splan Visitor

Creare un utente di test di nome **Britta Simon** in splan Visitor. Collaborare con il [team di supporto di splan Visitor](mailto:support@splan.com) per aggiungere l'utente a splan Visitor. È necessario creare e attivare l'utente prima di usare Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

Testare la configurazione di Azure AD Single Sign-On con una delle opzioni seguenti:

* **Portale di Azure**: selezionare **test questa applicazione** per accedere automaticamente al visitatore di splan per il quale si configura SSO.
* **Portale di Microsoft My Apps**: selezionare il riquadro **Visitor splan** per accedere automaticamente al visitatore di splan per il quale si configura SSO. Per altre informazioni sul portale App personali, vedere [Accedere e avviare app dal portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il visitatore di splan, è possibile [apprendere come applicare i controlli della sessione in Microsoft cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app). I controlli della sessione consentono di proteggere exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. I controlli sessione costituiscono un'estensione dell'accesso condizionale.
