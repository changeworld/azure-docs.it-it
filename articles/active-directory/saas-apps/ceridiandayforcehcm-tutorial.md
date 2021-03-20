---
title: 'Esercitazione: Integrazione di Azure Active Directory con Ceridian Dayforce HCM | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 1f2e01a79f980e63102bb6538859f0da30c555c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651848"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Esercitazione: Integrazione di Azure Active Directory con Ceridian Dayforce HCM

Questa esercitazione illustra come integrare Ceridian Dayforce HCM con Azure Active Directory (Azure AD). Quando si integra Ceridian Dayforce HCM con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a Ceridian Dayforce HCM.
* Abilitare gli utenti per l'accesso automatico a Ceridian Dayforce HCM con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Ceridian Dayforce HCM Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Ceridian Dayforce HCM supporta l'accesso SSO avviato da **SP**

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Aggiungere Ceridian Dayforce HCM dalla raccolta

Per configurare l'integrazione di Ceridian Dayforce HCM in Azure AD, è necessario aggiungere Ceridian Dayforce HCM dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Ceridian Dayforce HCM** nella casella di ricerca.
1. Selezionare **Ceridian DAYFORCE HCM** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Configurare e testare Azure AD SSO per Ceridian Dayforce HCM

Configurare e testare Azure AD SSO con Ceridian Dayforce HCM usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Ceridian Dayforce HCM.

Per configurare e testare Azure AD SSO con Ceridian Dayforce HCM, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Ceridian DAYFORCE HCM SSO](#configure-ceridian-dayforce-hcm-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Ceridian DAYFORCE HCM](#create-ceridian-dayforce-hcm-test-user)** : per avere una controparte di B. Simon in CERIDIAN Dayforce HCM collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD 

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Ceridian DAYFORCE HCM** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Ceridian Dayforce HCM](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Ceridian Dayforce HCM.

    | Environment | URL |
    | :-- | :-- |
    | Per la produzione | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Per il test | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Nella casella di testo **Identificatore** digitare l'URL usando il modello seguente: 

    | Environment | URL |
    | :-- | :-- |
    | Per la produzione | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Per il test | `https://fs-test.dayforcehcm.com/sp` |

    c. Nella casella di testo **URL di risposta** digitare l'URL utilizzato da Azure AD per inserire la risposta.

    | Environment | URL |
    | :-- | :-- |
    | Per la produzione | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Per il test | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Ceridian Dayforce HCM](https://www.ceridian.com/support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione Ceridian Dayforce HCM prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Screenshot che mostra il riquadro Attributi utente con l'icona di modifica selezionata.](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome | Attributo di origine|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![Screenshot che mostra il riquadro Attestazioni utente con l'opzione per aggiungere una nuova attestazione.](common/new-save-attribute.png)

    ![Screenshot che mostra la finestra di dialogo Gestisci attestazioni utente in cui è possibile immettere i valori descritti.](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare l'attributo utente da usare per l'implementazione. Ad esempio, se si vuole usare il valore EmployeeID come identificatore utente univoco e il valore dell'attributo è stato archiviato in ExtensionAttribute2, selezionare user.extensionattribute2.

    f. Fare clic su **OK**.

    g. Fare clic su **Salva**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **file XML dei metadati** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

8. Nella sezione **Configura Ceridian Dayforce HCM** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Ceridian Dayforce HCM.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni, selezionare **Ceridian Dayforce HCM**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Configurare Ceridian Dayforce HCM SSO

Per configurare l'accesso Single Sign-On sul lato **Ceridian Dayforce HCM**, è necessario inviare il file **XML dei metadati** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di Ceridian Dayforce HCM](https://www.ceridian.com/support). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Creare l'utente di test di Ceridian Dayforce HCM

In questa sezione viene creato un utente di nome Britta Simon in Ceridian Dayforce HCM. Collaborare con il [team di supporto di Ceridian Dayforce HCM](https://www.ceridian.com/support) per aggiungere gli utenti alla piattaforma Ceridian Dayforce HCM. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Ceridian Dayforce HCM in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Ceridian Dayforce HCM e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Ceridian Dayforce HCM in app personali, questo verrà reindirizzato all'URL di accesso di Ceridian Dayforce HCM. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Ceridian Dayforce HCM è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).