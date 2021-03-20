---
title: 'Esercitazione: Configurare ServiceNow per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539044"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Esercitazione: Configurare ServiceNow per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia in ServiceNow che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Quando viene configurato Azure AD, effettua automaticamente il provisioning e il deprovisioning di utenti e gruppi in [ServiceNow](https://www.servicenow.com/) usando il servizio di provisioning Azure ad. 

Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in ServiceNow
> * Rimuovere gli utenti in ServiceNow quando non è più necessario accedere
> * Gestione della sincronizzazione degli attributi utente tra Azure AD e ServiceNow
> * Provisioning di gruppi e appartenenze a gruppi in ServiceNow
> * Consenti [Single Sign-on](servicenow-tutorial.md) a ServiceNow (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l' [autorizzazione](../roles/permissions-reference.md) per configurare il provisioning (amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale)
* Un'[istanza di ServiceNow](https://www.servicenow.com/) Calgary o versione successiva
* Un'[istanza di ServiceNow Express](https://www.servicenow.com/) Helsinki o versione successiva
* Un account utente in ServiceNow con il ruolo amministratore

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1: pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare i dati da [mappare tra Azure AD e ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Passaggio 2: configurare ServiceNow per supportare il provisioning con Azure AD

1. Identificare il nome dell'istanza di ServiceNow. Il nome dell'istanza è disponibile nell'URL usato per accedere a ServiceNow. Nell'esempio seguente il nome dell'istanza è **dev35214**.

   ![Screenshot che mostra un'istanza di ServiceNow.](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Ottenere le credenziali per un amministratore in ServiceNow. Passare al profilo utente in ServiceNow e verificare che l'utente disponga del ruolo di amministratore. 

   ![Screenshot che mostra un ruolo di amministratore di ServiceNow.](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Passaggio 3: aggiungere ServiceNow dalla raccolta di applicazioni Azure AD

Aggiungere ServiceNow dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in ServiceNow. Se in precedenza è stato impostato ServiceNow per Single Sign-On (SSO), è possibile usare la stessa applicazione. Tuttavia, è consigliabile creare un'applicazione separata quando si sta testando l'integrazione. [Altre informazioni sull'aggiunta di un'applicazione dalla raccolta](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4: definire chi sarà nell'ambito del provisioning 

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile usare i [passaggi per assegnare utenti e gruppi all'applicazione](../manage-apps/assign-user-or-group-access-portal.md). Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, è possibile [utilizzare un filtro di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Tenere presente i suggerimenti seguenti:

* Quando si assegnano utenti e gruppi a ServiceNow, è necessario selezionare un ruolo diverso dall'accesso predefinito. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Passaggio 5: configurare il provisioning utenti automatico in ServiceNow 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e gruppi in TestApp. È possibile basare la configurazione sulle assegnazioni di utenti e gruppi in Azure AD.

Per configurare il provisioning utenti automatico per ServiceNow in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Screenshot che mostra il riquadro Applicazioni aziendali.](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **ServiceNow**.

    ![Screenshot che mostra un elenco di applicazioni.](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa modalità di provisioning con l'opzione automatica chiamata.](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere il nome utente e le credenziali di amministratore di ServiceNow. Selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a ServiceNow. Se la connessione non riesce, verificare che l'account ServiceNow disponga delle autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra la pagina di provisioning del servizio, in cui è possibile immettere le credenziali di amministratore.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. Nel campo **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche di errore relative al provisioning. Selezionare quindi la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Screenshot che mostra le caselle per il messaggio di posta elettronica di notifica.](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a ServiceNow**.

9. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD in ServiceNow. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in ServiceNow per le operazioni di aggiornamento. 

   Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), è necessario assicurarsi che l'API ServiceNow supporti il filtraggio degli utenti in base a tale attributo. 
   
   Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to ServiceNow**.

11. Nella sezione **Mapping degli attributi** esaminare gli attributi del gruppo che vengono sincronizzati da Azure AD in ServiceNow. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in ServiceNow per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

12. Per configurare i filtri di definizione dell'ambito, vedere le istruzioni riportate in questa [esercitazione](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per ServiceNow, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Screenshot che mostra lo stato del provisioning attivato.](common/provisioning-toggle-on.png)

14. Definire gli utenti e i gruppi di cui si vuole eseguire il provisioning in ServiceNow selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Screenshot che mostra le opzioni per l'ambito del provisioning.](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Screenshot del pulsante per il salvataggio di una configurazione di provisioning.](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo iniziale richiede più tempo rispetto ai cicli successivi. I cicli successivi vengono eseguiti ogni 40 minuti, a condizione che il servizio di provisioning Azure AD sia in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6: monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

- Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
- Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
- Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. [Altre informazioni sugli stati di quarantena](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
* Quando si esegue il provisioning di determinati attributi, ad esempio **Department** e **location**, in ServiceNow, è necessario che i valori esistano già in una tabella di riferimento in ServiceNow. In caso contrario, si otterrà un errore **InvalidLookupReference** . 

   Si potrebbero ad esempio avere due posizioni (Seattle, Los Angeles) e tre reparti (vendite, finanza, marketing) in una determinata tabella in ServiceNow. Se si tenta di eseguire il provisioning di un utente il cui reparto è "Sales" e il cui percorso è "Seattle", verrà eseguito il provisioning dell'utente. Se si tenta di effettuare il provisioning di un utente il cui reparto è "Sales" e la cui posizione è "LA," non verrà effettuato il provisioning dell'utente. Il percorso "LA" deve essere aggiunto alla tabella di riferimento in ServiceNow o l'attributo utente in Azure AD deve essere aggiornato in modo che corrisponda al formato in ServiceNow. 
* Se viene ricevuto un errore **EntryJoiningPropertyValueIsMissing** , rivedere i [mapping degli attributi](../app-provisioning/customize-application-attributes.md) per identificare l'attributo corrispondente. Questo valore deve essere presente nell'utente o gruppo di cui si sta provando a eseguire il provisioning. 
* Per comprendere eventuali requisiti o limitazioni (ad esempio, il formato per specificare un codice paese per un utente), esaminare l' [API SOAP ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html).
* Per impostazione predefinita, le richieste di provisioning vengono inviate a https://{nome-istanza}.service-now.com/{nome-tabella}. Se è necessario un URL tenant personalizzato, è possibile specificare l'intero URL come nome dell'istanza.
* L'errore **ServiceNowInstanceInvalid** indica un problema di comunicazione con l'istanza di ServiceNow. Ecco il testo dell'errore:
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  Se si verificano problemi di connessione, provare a selezionare **No** per le impostazioni seguenti in ServiceNow:
   
  - **Sicurezza**  >  del sistema Impostazioni di sicurezza **elevate**  >  **Richiedi autenticazione di base per le richieste di schema in ingresso**
  - **Proprietà**  >  di sistema **Servizi Web**  >  **Richiedi autorizzazione di base per le richieste SOAP in ingresso**

     ![Screenshot che mostra l'opzione per l'autorizzazione delle richieste SOAP.](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  Se non si riesce ancora a risolvere il problema, contattare il supporto ServiceNow e chiedere di attivare il debug SOAP per facilitare la risoluzione dei problemi. 

* Il servizio di provisioning Azure AD attualmente funziona in [intervalli IP](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)specifici. Se necessario, è possibile limitare altri intervalli IP e aggiungere questi intervalli IP specifici all'elenco Consenti dell'applicazione. Questa tecnica consentirà il flusso del traffico dal servizio Azure AD provisioning all'applicazione.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
