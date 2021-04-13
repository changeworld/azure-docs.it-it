---
title: 'Esercitazione: Configurare Jostle per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il de-provisioning degli account utente da Azure AD a Jostle.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2021
ms.author: Zhchia
ms.openlocfilehash: d2ab0009f036afa38dc9e401223854a034d45e42
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368673"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>Esercitazione: Configurare Jostle per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Jostle e Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurata, Azure AD esegue automaticamente il provisioning di utenti e gruppi in [Jostle](https://www.jostle.me/) usando il Azure AD Provisioning. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Jostle
> * Rimuovere gli utenti in Jostle quando non richiedono più l'accesso
> * Mantenere sincronizzati gli attributi utente tra Azure AD e Jostle
> * [Single Sign-On](jostle-tutorial.md) a Jostle (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Tenant [di Jostle.](https://www.jostle.me/)
* Un account utente in Jostle con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning

1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
1. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determinare quali dati [eseguire il mapping tra Azure AD e Jostle](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Jostle per supportare il provisioning con Azure AD

### <a name="automation-account"></a>Account di Automazione

Prima di iniziare, è necessario creare un utente di **Automazione** nella intranet di Jostle. Questo sarà l'account che si userà per configurare con Azure. Gli utenti di Automazione possono essere creati in Impostazioni amministratore > account utente e **dati > utenti di Automazione**.

Per altre informazioni sugli utenti di Automazione e su come crearne uno, vedere [questo articolo.](https://forum.jostle.us/hc/en-us/articles/360057364073)

Dopo la creazione, **l'account** utente di Automazione deve essere attivato (ad esempio, connesso alla intranet almeno una volta) prima di poterlo usare per configurare Azure.

### <a name="manage-user-provisioning"></a>Gestire il provisioning utenti

Prima di iniziare, assicurarsi che la sottoscrizione dell'account **includa le funzionalità di provisioning SSO/utente.** In caso contrario, è possibile contattare customer success manager per assistenza nell'aggiunta <success@jostle.me> al proprio account.

Il passaggio successivo consiste nell'ottenere l'URL e la **chiave API dell'API** da Jostle: 

1. Passare al riquadro di spostamento principale e fare clic su **Admin Settings (Impostazioni di amministrazione).**
1. In **User data to/from other systems (Dati utente da/verso altri sistemi)** fare **clic su Manage user provisioning (Gestisci provisioning utenti).** Se non viene visualizzato "Manage user provisioning" (Gestisci provisioning utenti) e si è verificato che l'account includa il provisioning SSO/utente, contattare il supporto tecnico per fare in modo che questa pagina sia abilitata <support@jostle.me> nelle impostazioni di amministrazione.
1. Nella sezione **Dettagli DELL'API** Provisioning utenti passare al campo URL di **base,** fare clic sul pulsante Copia e salvare l'URL in un punto in cui sia possibile accedervi facilmente in un secondo momento.                                                           

   ![Provisioning](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. Fare quindi clic su **Aggiungi una nuova chiave...** Pulsante
1. Nella schermata seguente passare al campo Utente di **Automazione** e usare il menu a discesa per selezionare l'account utente di Automazione. 

   ![Account di integrazione](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. Nel campo **Provisioning API key description (Descrizione** chiave API di provisioning) assegnare alla chiave un nome (ad esempio, "Azure") e quindi fare clic sul pulsante **Aggiungi.**

1. Dopo aver generato la **chiave,** assicurarsi di copiarla immediatamente e salvarla nel percorso in cui è stato salvato l'URL,poiché questa sarà l'unica volta che verrà visualizzata la chiave.                                                               
1. Si useranno quindi **l'URL e** la chiave API **dell'API** per configurare l'integrazione in Azure.
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Jostle dalla raccolta Azure AD applicazioni

Aggiungere Jostle dalla raccolta Azure AD di applicazioni per iniziare a gestire il provisioning in Jostle. Se Jostle è stato precedentemente creato per l'accesso SSO, è possibile usare la stessa applicazione. Tuttavia, è consigliabile creare un'app separata quando si testa inizialmente l'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a Jostle, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>Passaggio 5. Configurare il provisioning utenti automatico in Jostle 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e gruppi nell'app Jostle in base alle assegnazioni di utenti e gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Jostle in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni, selezionare **Jostle**.

    ![Collegamento a Jostle nell'elenco delle applicazioni](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

1.  Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

1. Nella sezione **Credenziali amministratore** immettere l'URL del **tenant** Jostle e le informazioni **sul token** segreto. Selezionare **Test connessione** per assicurarsi che Azure AD possibile connettersi a Jostle. Se la connessione non riesce, verificare che l'account Jostle abbia le autorizzazioni di amministratore e riprovare.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Nel campo **Messaggio di posta elettronica** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. Nella sezione **Mapping selezionare** Synchronize Azure Active Directory Users **to Jostle**.

1. Esaminare gli attributi utente sincronizzati da Azure AD a Jostle nella **sezione Mapping** attributi. Gli attributi selezionati come **proprietà corrispondenti** vengono usati per trovare corrispondenze con gli account utente in Jostle per le operazioni di aggiornamento. Se si modifica [l'attributo di destinazione](../app-provisioning/customize-application-attributes.md)corrispondente, è necessario assicurarsi che l'API Jostle supporti il filtro degli utenti in base a tale attributo. Selezionare **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |active|Boolean|
   |name.givenName|string|
   |name.familyName|string|
   |emails[type eq "work"].value|string|
   |emails[type eq "personal"].value|string|
   |emails[type eq "alternate1"].value|string|
   |emails[type eq "alternate2"].value|string|  
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail1Label|string|
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail2Label |string|  

1. Per configurare i filtri di ambito, vedere le istruzioni fornite [nell'esercitazione filtro ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il Azure AD provisioning per Jostle, impostare **Stato provisioning** su **Attivato** nella **sezione** Impostazioni.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

1. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in Jostle selezionando i valori desiderati in **Ambito** nella **sezione** Impostazioni.

    ![Ambito di provisioning](common/provisioning-scope.png)

1. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano circa ogni 40 minuti, purché il Azure AD di provisioning sia in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione

Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning per](../reports-monitoring/concept-provisioning-logs.md) determinare quali utenti sono stati provisioning riusciti o non riusciti.
* Controllare [l'indicatore di](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) stato per visualizzare lo stato del ciclo di provisioning e la sua chiusura al completamento.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, vedere [Stato di provisioning dell'applicazione di quarantena.](../app-provisioning/application-provisioning-quarantine-status.md)

## <a name="more-resources"></a>Altre risorse

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)