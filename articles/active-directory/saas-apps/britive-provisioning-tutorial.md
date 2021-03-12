---
title: 'Esercitazione: configurare la funzione Brity per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Brity.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 8bebcb49bc7bf31614a161c08d33d5910679b614
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225771"
---
# <a name="tutorial-configure-britive-for-automatic-user-provisioning"></a>Esercitazione: configurare la funzione Brity per il provisioning utenti automatico

In questa esercitazione vengono descritti i passaggi da eseguire sia in Brity che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Quando è configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning [di utenti](https://www.britive.com/) e gruppi in modo da usare il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in un Brity
> * Rimuovere gli utenti in modo che non richiedano più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e Brity
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in Brity
> * [Single Sign-on](britive-tutorial.md) a brity (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Tenant [brity](https://www.britive.com/) .
* Un account utente con autorizzazioni di amministratore.


## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
1. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determinare quali dati eseguire il [mapping tra Azure ad e brity](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-britive-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Brity per supportare il provisioning con Azure AD

L'applicazione deve essere configurata manualmente usando i passaggi indicati di seguito:
1. Accesso all'applicazione Brity con privilegi di amministratore
1. Fare clic su **amministratore->amministrazione utente-provider di identità >**
1. Fare clic su **Aggiungi provider di identità**. Immettere il nome e la descrizione. Fare clic sul pulsante Aggiungi provider di identità.

    ![Provider di identità](media/britive-provisioning-tutorial/identity.png)

1. Verrà visualizzata una pagina di configurazione simile alla seguente.

    ![Pagina Configurazione](media/britive-provisioning-tutorial/configuration.png)

1. Fare clic sulla scheda **scim** . Modificare il provider SCIM da generico ad Azure e salvare le modifiche. Copiare l'URL SCIM e annotarlo. Questi valori verranno immessi nelle caselle **URL tenant** della scheda provisioning dell'applicazione brity nell'portale di Azure.

    ![Pagina SCIM](media/britive-provisioning-tutorial/scim.png)

1. Fare clic su **crea token**. Selezionare la validità del token come richiesto e fare clic sul pulsante crea token.

    ![Crea token](media/britive-provisioning-tutorial/create-token.png)

1. Copiare il token generato e annotarlo. Fare clic su OK. Si noti che l'utente non sarà in grado di visualizzare di nuovo il token. Se necessario, fare clic su Re-Create pulsante per generare un nuovo token. Questi valori verranno immessi nelle caselle **token Secret** e URL tenant nella scheda provisioning dell'applicazione getAbstract nell'portale di Azure.

    ![Copia token](media/britive-provisioning-tutorial/copy-token.png) 


## <a name="step-3-add-britive-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere un Brity dalla raccolta di applicazioni Azure AD

Aggiungere Brity dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in Brity. Se in precedenza è stato configurato il programma Brity for SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a un metodo Brity, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-britive"></a>Passaggio 5. Configurare il provisioning utenti automatico in Brity 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in modo Brity in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-britive-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Brity in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **Britive**.

    ![Collegamento di Brity nell'elenco delle applicazioni](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

1. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

1. Nella sezione **credenziali amministratore** immettere l'URL del tenant brity e il token segreto. Fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi a brity. Se la connessione ha esito negativo, verificare che l'account di questo sistema abbia le autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

1. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a brity**.

1. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Brity nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in brity per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API di supporto per il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;
   |active|Boolean|
   |displayName|string|
   |title|string|
   |externalId|string|
   |preferredLanguage|string|
   |name.givenName|string|
   |name.familyName|string|
   |nickName|string|
   |userType|string|
   |locale|string|
   |timezone|string|
   |emails[type eq "home"].value|string|
   |emails[type eq "other"].value|string|
   |emails[type eq "work"].value|string|
   |phoneNumbers[type eq "home"].value|string|
   |phoneNumbers[type eq "other"].value|string|
   |phoneNumbers[type eq "pager"].value|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "fax"].value|string|
   |addresses[type eq "work"].formatted|string|
   |addresses[type eq "work"].streetAddress|string|
   |addresses[type eq "work"].locality|string|
   |addresses[type eq "work"].region|string|
   |addresses[type eq "work"].postalCode|string|
   |addresses[type eq "work"].country|string|
   |addresses[type eq "home"].formatted|string|
   |addresses[type eq "home"].streetAddress|string|
   |addresses[type eq "home"].locality|string|
   |addresses[type eq "home"].region|string|
   |addresses[type eq "home"].postalCode|string|
   |addresses[type eq "home"].country|string|
   |addresses[type eq "other"].formatted|string|
   |addresses[type eq "other"].streetAddress|string|
   |addresses[type eq "other"].locality|string|
   |addresses[type eq "other"].region|string|
   |addresses[type eq "other"].postalCode|string|
   |addresses[type eq "other"].country|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|


1. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to brity**.

1. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Brity nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|Supportato per il filtro|
      |---|---|---|
      |displayName|string|&check;
      |externalId|string|
      |Membri di|Informazioni di riferimento|

1. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il servizio di provisioning di Azure AD per Brity, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

1. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Brity scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

1. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
* Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)