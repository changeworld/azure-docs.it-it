---
title: 'Esercitazione: configurare atea per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a atea.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b788328b-10fd-4eaa-a4bc-909d738d8b8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 67b114ac355b293db61d00b89816ab73aaa2e5b2
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550269"
---
# <a name="tutorial-configure-atea-for-automatic-user-provisioning"></a>Esercitazione: configurare atea per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia in atea che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [atea](https://www.atea.com/) usando il servizio di provisioning Azure ad. Per informazioni importanti sul funzionamento di questo servizio, sul suo funzionamento e sulle domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in atea
> * Rimuovere gli utenti in atea quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e atea

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un account utente in atea con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e atea](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-atea-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare atea per supportare il provisioning con Azure AD

Per configurare atea per il supporto del provisioning con Azure AD, scrivere un messaggio di posta elettronica al team di supporto di atea <SSO.Support@atea.com>

## <a name="step-3-add-atea-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere atea dalla raccolta di applicazioni Azure AD

Aggiungere atea dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in atea. Se in precedenza è stato configurato atea per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'applicazione separata quando si testa inizialmente l'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione e o in base agli attributi dell'utente e del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a atea, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-atea"></a>Passaggio 5. Configurare il provisioning utenti automatico in atea 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e gruppi in atea, in base alle assegnazioni di utenti e gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atea-in-azure-ad"></a>Per configurare il provisioning utenti automatico per atea in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **atea**.

    ![Collegamento di atea nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** selezionare **autorizza**. Viene aperta una finestra di dialogo di accesso atea in una nuova finestra del browser.

     ![Autorizzazione atea](media/atea-provisioning-tutorial/provisioning-authorize.png)

6. Nella finestra di dialogo di accesso di atea accedere al tenant di atea e verificare la propria identità.
       
      ![Finestra di dialogo di accesso atea](media/atea-provisioning-tutorial/atea-login.png)

7. Dopo aver completato i passaggi 5 e 6, fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a atea. Se la connessione non riesce, assicurarsi che atea disponga delle autorizzazioni di amministratore e riprovare.
        
      ![Connessione test atea](media/atea-provisioning-tutorial/test-connection.png)

8. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche degli errori di provisioning. Quindi selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Selezionare **Salva**.

10. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a atea**.

11. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a atea nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in atea per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), è necessario assicurarsi che l'API atea supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|Supportato per il filtro|
      |---|---|---|
      |userName|string|&check;|
      |active|Boolean|
      |emails[type eq "work"].value|string|
      |name.givenName|string|
      |name.familyName|string|
      |name.formatted|string|
      |phoneNumbers[type eq "mobile"].value|string|
      |locale|string|
      |nickName|string|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per atea, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e i gruppi di cui si vuole eseguire il provisioning in atea scegliendo il valore pertinente nell' **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti se il servizio Azure AD provisioning è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
* Controllare l' [indicatore di stato per visualizzare](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) lo stato del ciclo di provisioning e il modo in cui terminarlo.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
