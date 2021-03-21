---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con la piattaforma di gestione delle autorizzazioni CloudKnox | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e la piattaforma di gestione delle autorizzazioni CloudKnox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 8c1b45d78d4fb61d239fef178cbcb9fa6efd9539
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645687"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudknox-permissions-management-platform"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con la piattaforma di gestione delle autorizzazioni di CloudKnox

In questa esercitazione si apprenderà come integrare la piattaforma di gestione delle autorizzazioni di CloudKnox con Azure Active Directory (Azure AD). Quando si integra la piattaforma di gestione delle autorizzazioni di CloudKnox con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere alla piattaforma di gestione delle autorizzazioni CloudKnox.
* Consentire agli utenti di accedere automaticamente alla piattaforma di gestione delle autorizzazioni di CloudKnox con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per CloudKnox Management Platform Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* La piattaforma di gestione delle autorizzazioni CloudKnox supporta SSO avviato da **IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-cloudknox-permissions-management-platform-from-the-gallery"></a>Aggiunta della piattaforma di gestione delle autorizzazioni di CloudKnox dalla raccolta

Per configurare l'integrazione della piattaforma di gestione delle autorizzazioni CloudKnox in Azure AD, è necessario aggiungere CloudKnox Permissions Management Platform dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **CloudKnox Permissions Management Platform** nella casella di ricerca.
1. Selezionare **CloudKnox Permissions Management Platform** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-cloudknox-permissions-management-platform"></a>Configurare e testare Azure AD SSO per la piattaforma di gestione delle autorizzazioni CloudKnox

Configurare e testare Azure AD SSO con la piattaforma di gestione delle autorizzazioni CloudKnox usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato nella piattaforma di gestione delle autorizzazioni CloudKnox.

Per configurare e testare Azure AD SSO con la piattaforma di gestione delle autorizzazioni CloudKnox, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare CloudKnox Permissions Management Platform SSO](#configure-cloudknox-permissions-management-platform-sso)** : per configurare le impostazioni Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di CloudKnox Permissions Management Platform](#create-cloudknox-permissions-management-platform-test-user)** : per avere una controparte di B. Simon in CloudKnox Permissions Management Platform collegata al Azure ad rappresentazione dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure della pagina di integrazione dell'applicazione **CloudKnox Permissions Management Platform** , trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** immettere i valori per i campi seguenti:

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://app.cloudknox.io/saml/<ID>`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. è necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere il valore, contattare il [team di supporto clienti di CloudKnox Permissions Management Platform](mailto:support@cloudknox.io) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione della piattaforma di gestione delle autorizzazioni CloudKnox prevede un formato specifico per le asserzioni SAML. è quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre a quanto sopra, l'applicazione della piattaforma di gestione delle autorizzazioni di CloudKnox prevede che vengano passati altri attributi nella risposta SAML, come illustrato di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome |  Attributo di origine|
    | --------------- | --------- |
    | First_Name | user.givenname |
    | Gruppi | user.groups |
    | Last_Name | user.surname |
    | Email_Address | user.mail |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **impostazione della piattaforma di gestione delle autorizzazioni di CloudKnox** copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso alla piattaforma di gestione delle autorizzazioni CloudKnox.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **CloudKnox Permissions Management Platform**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-cloudknox-permissions-management-platform-sso"></a>Configurare l'accesso SSO della piattaforma di gestione delle autorizzazioni CloudKnox

Per configurare Single Sign-On sul lato **della piattaforma di gestione delle autorizzazioni CloudKnox** , è necessario inviare il **file XML di metadati della Federazione** scaricato e gli URL copiati appropriati da portale di Azure al team di supporto di [CloudKnox Permissions Management Platform](mailto:support@cloudknox.io). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-cloudknox-permissions-management-platform-test-user"></a>Creare un utente di test di CloudKnox Permissions Management Platform

In questa sezione viene creato un utente chiamato Britta Simon in CloudKnox Permissions Management Platform. Collaborare con il [team di supporto di CloudKnox Permissions Management Platform](mailto:support@cloudknox.io) per aggiungere gli utenti nella piattaforma CloudKnox Permissions Management Platform. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente alla piattaforma di gestione delle autorizzazioni CloudKnox per la quale si configura l'accesso SSO

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro CloudKnox Permissions Management Platform (app personali), si dovrebbe accedere automaticamente alla piattaforma di gestione delle autorizzazioni di CloudKnox per la quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la piattaforma di gestione delle autorizzazioni di CloudKnox, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).