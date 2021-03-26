---
title: 'Esercitazione: configurare SAAS Olfeo per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Olfeo SAAS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: b74175c7847bb19aa9410edd613afbfe1d762d05
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105549293"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>Esercitazione: configurare SAAS Olfeo per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Olfeo SAAS e Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [OLFEO Saas](https://www.olfeo.com) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Olfeo SAAS
> * Rimuovere gli utenti in Olfeo SAAS quando non sono più necessari per l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e SAAS Olfeo
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in Olfeo SAAS
> * [Single Sign-on](olfeo-saas-tutorial.md) per Olfeo SaaS (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* [Tenant SaaS Olfeo](https://www.olfeo.com/).
* Un account utente in Olfeo SAAS con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning

1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
1. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determinare quali dati eseguire il [mapping tra Azure ad e SaaS Olfeo](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare SAAS Olfeo per supportare il provisioning con Azure AD

1. Accedere alla console di amministrazione SAAS di Olfeo. 
1. Passare a **Configuration > annuaires**.
1. Creare una nuova directory e denominarla.
1. Selezionare provider di **Azure** e quindi fare clic su **CR er** per salvare la nuova directory. 
1. Passare alla scheda **sincronizzazione** per visualizzare l'URL del **tenant** e il **segreto jeton**. Questi valori verranno copiati e incollati nei campi **URL tenant** e **token Secret** nella scheda provisioning dell'applicazione SaaS Olfeo nel portale di Azure.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Olfeo SAAS dalla raccolta di applicazioni Azure AD

Aggiungere Olfeo SAAS dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in Olfeo SAAS. Se in precedenza è stato configurato Olfeo SAAS per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'applicazione separata quando si testa inizialmente l'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a SAAS Olfeo, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>Passaggio 5. Configurare il provisioning utenti automatico in Olfeo SAAS 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e gruppi nell'app SAAS Olfeo in base alle assegnazioni di utenti e gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Olfeo SAAS in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **OLFEO Saas**.

    ![Collegamento SAAS Olfeo nell'elenco delle applicazioni](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

1.  Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

1. Nella sezione **credenziali amministratore** immettere l' **URL del tenant** Saas Olfeo e le informazioni sul **token segreto** . Selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a Olfeo Saas. Se la connessione non riesce, verificare che l'account SAAS Olfeo disponga delle autorizzazioni di amministratore e riprovare.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to Olfeo Saas**.

1. Esaminare gli attributi utente sincronizzati da Azure AD a SAAS Olfeo nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Olfeo SaaS per le operazioni di aggiornamento. Se si modifica l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), è necessario assicurarsi che l'API Saas Olfeo supporti il filtraggio degli utenti in base a tale attributo. Selezionare **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |displayName|string|
   |active|Boolean|
   |emails[type eq "work"].value|string|
   |preferredLanguage|string|
   |name.givenName|string|
   |name.familyName|string|
   |name.formatted|string|
   |externalId|string|  

1. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Olfeo Saas**.

1. Esaminare gli attributi di gruppo sincronizzati da Azure AD a SAAS Olfeo nella sezione **mapping** degli attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Olfeo SaaS per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|Supportato per il filtro|
      |---|---|---|
      |displayName|string|&check;|
      |externalId|string|
      |Membri di|Informazioni di riferimento|

1. Per configurare i filtri di ambito, vedere le istruzioni fornite nell'esercitazione relativa al [filtro di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il servizio di provisioning Azure AD per Olfeo SAAS, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

1. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in Olfeo SAAS selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

1. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano ogni 40 minuti a condizione che sia in esecuzione il servizio di provisioning Azure AD.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione

Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato effettuato il provisioning con esito positivo o negativo.
* Controllare l' [indicatore di stato per visualizzare](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) lo stato del ciclo di provisioning e il modo in cui terminarlo.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, vedere [stato di provisioning dell'applicazione per la quarantena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Altre risorse

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)